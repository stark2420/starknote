+++
author = "Hugo Authors"
title = "IrisCTF 2025 Writeup"
date = "2025-01-08"
description = ""
tags = [
    "CTF",
    "writeup",
    "iris",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/cover.png"
+++

2025/1/4 09:00(JST) - 2025/1/6 09:00(JST)で開催された IrisCTF 2025 に参加した．

<!--more-->
個人で参加し，結果は，450 点で 1064 チーム中 241 位だった．以下大会期間中に自分で解けた問題には✔をつけている．

https://2025.irisc.tf

## Binary Exploitation
### sqlate (50 *easy*)
> World‘s most secure paste app.  
nc ~  
sqlate.tar.gz

ソースコードより，flagはログイン認証後，7番を実行すれば得られることがわかる．
```c
int main(void) {
    (省略)
            case '6':
                return 0;
            case '7': {
                if (!check_permissions(permission_root)) continue;

                action_sys();
                continue;
            }
}
void action_sys() {
    system("/usr/bin/cat flag");
}
```

ソースコードより，ncでアクセスすると，init_admin()でadminのパスワードがランダムに作成される．
"/dev/urandom"は，Unix系OSにおける擬似デバイスであり，可変長の擬似乱数文字列を生成するものである．
```c
void init_admin() {
    FILE* rng = fopen("/dev/urandom", "r");
    if (rng == NULL)
        errx(EXIT_FAILURE, "Failed to open /dev/urandom");
    char* result = fgets(line_buffer, 100 * sizeof(char), rng);
    if (result == NULL)
        errx(EXIT_FAILURE, "Failed to read from /dev/urandom");
    char* pass = base64_encode(line_buffer);
    strcpy(admin_password, pass);
    free(pass);
    if (DEBUG) {
        printf("Generated random admin password: %s\n", admin_password);
    }
}
```

ログイン認証の際には，action_login()で入力文字列とパスワードが比較される．
このとき，strlen(line_buffer)で**入力文字列の文字数分**しか比較されないようになっている．
fgets(・)では，最後にEnterを押したとき，改行文字も入力されるので，
何も入力せずEnterを押すと，文字数 1 で改行文字のみとなる．  
よって，改行文字から始まるadminパスワードがランダムに生成されるまでブルートフォース攻撃を行うことで，
ログイン認証を突破できる．

```c
void action_login() {
    // Currently only admin login
    read_to_buffer("Password?");
    unsigned long length = strlen(line_buffer);
    for (unsigned long i = 0; i < length && i < 512; i++) {
        if (line_buffer[i] != admin_password[i]) {
            printf("Wrong password!\n");
            return;
        }
    }

    strcpy(current_user.username, "admin");
    current_user.userId = 0;
    current_user.flags = 0xFFFFFFFF;
}

void read_to_buffer(const char* description) {
    printf("Enter %s: ", description);
    fgets(line_buffer, 256, stdin);
}
```

つまり，adminパスワードが改行文字から始まらない場合は，Wrong password!となるが，
adminパスワードが改行文字から始まる場合は，Enterのみでログイン認証が成功する．

```
$ nc ~
== proof-of-work: disabled ==

===== Service =====
1) Create new Paste
2) Update a Paste
3) Show a Paste
4) List all Pastes
5) Login / Register
6) Exit

> 5
Registration is currently closed.

Enter Password?:
Wrong password!

===== Service =====
1) Create new Paste
2) Update a Paste
3) Show a Paste
4) List all Pastes
5) Login / Register
6) Exit

> 6

$ nc ~
== proof-of-work: disabled ==

===== Service =====
1) Create new Paste
2) Update a Paste
3) Show a Paste
4) List all Pastes
5) Login / Register
6) Exit

> 5
Registration is currently closed.

Enter Password?:

===== Service =====
1) Create new Paste
2) Update a Paste
3) Show a Paste
4) List all Pastes
5) Login / Register
6) Exit

> 7
irisctf{classic_buffer_issues}
```

そして，7番を実行すればflagが得られる．

`irisctf{classic_buffer_issues}`


## Cryptography
### ✔ KittyCrypt (50 *easy*)
> You managed to get access to the KittyCorp encryption system code! But it seems like it requires some sort of keycode in order to decode the catnip storage password.  
Thankfully, you managed to find a test file with both an input and an output! How handy! Maybe you can use this to find the key?  
kittycrypt.tar.gz  
Hint! example_output and flag_output use the same keys!

添付のファイルを見ると，平文が猫の絵文字で暗号化されていることがわかる．
ある平文と暗号文のペアが与えられているので，それらから秘密鍵を復元することになる．
{{< code lang="" title="example_input.txt" hl_lines="">}}
You fools! You will never get my catnip!!!!!!!
{{< /code >}}

{{< code lang="" title="example_output.txt" hl_lines="">}}
🐱‍👓🐱‍🏍🐱‍👤😸😾😹🐱‍👓🐱‍🏍🙀🐱‍👤😾😺🐱‍👓🐈😿🐱‍👤🐱‍👤🐱🐱‍👓🐈🐱‍👤🐱‍🏍🙀😾🐱‍👓🐱‍🏍🙀🐱‍🚀😿😽🐱‍👓🐱‍👓😾😹😾🐱🐱‍👓😽😿🐱‍🏍🙀🐱‍👓🐱‍👓🐱‍👓😾😺😾🐱‍💻🐱‍👓😸😾😸😾🐱‍🏍🐱‍👓😼🙀🙀🐱‍👤😼🐱‍👓🐈🐱‍👤😿🙀😾🐱‍👓😼🐱‍👤😺😾🐱‍💻🐱‍👓🙀😿🐱‍🚀😾😿🐱‍👓😽🙀🐱‍🏍🙀😼🐱‍👓😸🙀🐱‍👓🐱‍👤😿🐱‍💻🐱‍🚀😾😸🐱‍👓🙀😿🐱😿😸🐱‍👓😼🙀🐱‍🏍🐱‍👤🐱‍👤🐱‍👓😼🐱‍👤😸😾😹🐱‍👓😸🐱‍👤😸🐱‍👤🐱‍👤🐱‍👓😽🐱‍👤😾🙀😻🐱‍👓😽😾😻🐱‍👤🐱‍💻🐱‍👓😼🐱‍👤😿😿🐱🐱‍👓🐈😿😽😿😽🐱‍👓😽🙀😻😾😿🐱‍👓😺😾🐱🐱‍👤🙀🐱‍👓🐱‍👤😾🐈😿🐱‍👤🐱‍👓🐱🙀😺😿🙀🐱‍👓😼🙀🙀🐱‍👤🙀🐱‍👓🙀😾😺🙀😾🐱‍👓😹🐱‍👤😻😾😸🐱‍👓😻😿😺🙀🐈🐱‍👓😼😿😸🐱‍👤😸🐱‍👓🐈😾🐱‍💻😿😸🐱‍👓🐈😾🐱‍💻😿😹🐱‍👓🐱‍👤😾🐈🙀😸🐱‍👓🐱‍🏍😾🐈🙀😸🐱‍👓🐱‍🚀🐱‍👤🐱‍🚀🐱‍👤🐱‍💻🐱‍👓😸😾😸😾😿🐱‍👓🐱‍👓🙀😺🐱‍👤🐱🐱‍👓😾😾😽🐱‍👤😿🐱‍👓😸😿🐱‍👤😿🐱‍🚀🐱‍👓🐱‍🏍🙀🙀🙀🙀🐱‍👓🐱‍👤😿🐱‍👤🙀😻🐱‍👓😾🙀😻🙀😾🐱‍👓😾😾😿🐱‍👤😿
{{< /code >}}

{{< code lang="" title="flag_output.txt" hl_lines="">}}
🐱‍👓🐱‍🏍🐱‍👤😸😿😹🐱‍👓🐱‍🏍🙀🐱‍👤😾😽🐱‍👓🐈😿🐱‍👤🙀😺🐱‍👓🐈🐱‍👤🐱‍💻🐱‍👤🐱‍👤🐱‍👓🐱‍🏍🙀🐱‍🚀😿😺🐱‍👓🐱‍👓😾😹😾😻🐱‍👓😽😿🐱‍🏍🙀😻🐱‍👓🐱‍👓😾😺😿🐱‍🏍🐱‍👓😸😾😸😾🐱‍🏍🐱‍👓😼🙀🐱‍👤😾😻🐱‍👓🐈🐱‍👤🙀🐱‍👤😻🐱‍👓😼🐱‍👤😹🙀😽🐱‍👓🙀😿🐱‍👓😿🐈🐱‍👓😽🙀🐱‍👤🙀😸🐱‍👓😸🐱‍👤🐱😾😼🐱‍💻🐱‍💻🐱‍👤🐱‍👓🐱‍👓🙀😿🐱😿🐱‍🏍🐱‍👓😼🙀🐱‍🏍🙀🐱‍👓🐱‍👓😼🐱‍👤🐈🐱‍👤😿🐱‍👓😸🐱‍👤😹🐱‍👤😺🐱‍👓😽🐱‍👤😽🙀🐱‍👓🐱‍👓😽😾😻😾🐱‍👤🐱‍👓😼🐱‍👤😾😾🐱‍🚀🐱‍👓🐈😿😽😿🐈🐱‍👓😽🙀😺😾🐱‍👤🐱‍👓😺😾😸😾🐱‍🏍🐱‍👓🐱‍👤😾🐱🙀😽🐱‍👓🐱🙀😺🙀😹🐱‍👓😼🙀😿🐱‍👤🐱‍💻🐱‍👓🙀😾😻🙀😽🐱‍👓😹🐱‍👤😻😾🐱‍🏍🐱‍👓😻😿😺😿🐱🐱‍👓😼😿😹😾😼🐱‍👓🐈😾🐱‍🏍🙀😼🐱‍👓🐈😾🐱‍💻😿🐈🐱‍👓🐱‍👤😾🐈🙀😽🐱‍👓🐱‍🏍😾🐱🙀😺🐱‍👓🐱‍🚀🐱‍👤🐱‍🚀🐱‍👤🐱‍💻🐱‍👓😸😾🐈🐱‍👤😾🐱‍👓🐱‍👓🙀😻😾😸🐱‍👓😾😾😿😿🐱🐱‍👓😸😿🐱‍🏍🙀🐱‍👓🐱‍👓🐱‍🏍🙀🙀🐱‍👤🐱‍🏍🐱‍👓🐱‍👤😿🐱‍🏍🙀😽🐱‍👓😾🙀😻🐱‍👤🐱‍👓🐱‍👓😾😾🐱‍👤😿😻
{{< /code >}}

暗号化の流れは，ソースコードより，文字列に鍵を加算したものを16進文字列にして，対応する猫文字に変換している．


{{< code lang="go" title="main.go" hl_lines="">}}
var CharSet = map[rune]string{
	'0': "🐱", '1': "🐈", '2': "😸", '3': "😹",
	'4': "😺", '5': "😻", '6': "😼", '7': "😽",
	'8': "😾", '9': "😿", 'A': "🙀", 'B': "🐱‍👤",
	'C': "🐱‍🏍", 'D': "🐱‍💻", 'E': "🐱‍👓", 'F': "🐱‍🚀",
}

func catify(input string, keys []int) string {
	var keyedText string
	var result string

	for i, char := range input {
		keyedText += string(rune(int(char) + keys[i]))
	}
	fmt.Printf("I2Keyed: %s\n", keyedText)

	hexEncoded := strings.ToUpper(hex.EncodeToString([]byte(keyedText)))
	fmt.Printf("K2Hex: %s\n", hexEncoded)

	for _, rune := range hexEncoded {
		result += CharSet[rune]
	}

	return result
}
{{< /code >}}

ちなみに，猫文字のB~FはNinja Cat(猫忍者)と呼ばれるものであり，絵文字と絵文字とをゼロ幅接合子(zero width joiner,ZWJ)で繋いだものである．
すなわち，"🐱‍🏍"はUnicode では，"U+1F431 U+200D U+1F3CD"であり，"U+200D"は，ゼロ幅接合子である．  
参考：[ゼロ幅接合子(zero width joiner,ZWJ)による絵文字の組み合わせ・合体](https://0g0.org/topic/zero-width-joiner/)

以上より，復号を以下のようにPythonで作成．実行すると，flagが得られる．
{{< code lang="python" title="" hl_lines="">}}
import binascii

CharSet = {
    "🐱": "0", "🐈": "1", "😸": "2", "😹": "3",
    "😺": "4", "😻": "5", "😼": "6", "😽": "7",
    "😾": "8", "😿": "9", "🙀": "A", "🐱‍👤": "B",
    "🐱‍🏍": "C", "🐱‍💻": "D", "🐱‍👓": "E", "🐱‍🚀": "F",
}

def split_cats(cat_string):
    cat_list = []
    i = 0
    while i < len(cat_string):
        # len("🐱‍👤") = 3, len("🐱") = 1 である（ゼロ幅接合子）．
        for length in [3, 1]:
            char = "".join(cat_string[i:i+length])
            if char in CharSet:
                cat_list.append(char)
                i += length
                break
    return cat_list

def decryption(plaintext, cat_string):
    # 1. 猫文字を16進数の文字列に変換
    cat_list = split_cats(cat_string)
    hex_str = "".join(CharSet[t] for t in cat_list)

    # 2. 16進数文字列をバイト列に変換
    decoded_bytes = binascii.unhexlify(hex_str).decode()

    # 3. 元の文字列に復号
    key_or_dt = [] # key_or_decoded_text
    for i, db in enumerate(decoded_bytes):
        key_or_dt.append(ord(db) - (ord(plaintext[i]) if type(plaintext[i]) == str else plaintext[i]))

    return key_or_dt

if __name__ == "__main__":
    example_input = "You fools! You will never get my catnip!!!!!!!"
    example_output = "🐱‍👓🐱‍🏍🐱‍👤😸😾😹🐱‍👓🐱‍🏍🙀🐱‍👤😾😺🐱‍👓🐈😿🐱‍👤🐱‍👤🐱🐱‍👓🐈🐱‍👤🐱‍🏍🙀😾🐱‍👓🐱‍🏍🙀🐱‍🚀😿😽🐱‍👓🐱‍👓😾😹😾🐱🐱‍👓😽😿🐱‍🏍🙀🐱‍👓🐱‍👓🐱‍👓😾😺😾🐱‍💻🐱‍👓😸😾😸😾🐱‍🏍🐱‍👓😼🙀🙀🐱‍👤😼🐱‍👓🐈🐱‍👤😿🙀😾🐱‍👓😼🐱‍👤😺😾🐱‍💻🐱‍👓🙀😿🐱‍🚀😾😿🐱‍👓😽🙀🐱‍🏍🙀😼🐱‍👓😸🙀🐱‍👓🐱‍👤😿🐱‍💻🐱‍🚀😾😸🐱‍👓🙀😿🐱😿😸🐱‍👓😼🙀🐱‍🏍🐱‍👤🐱‍👤🐱‍👓😼🐱‍👤😸😾😹🐱‍👓😸🐱‍👤😸🐱‍👤🐱‍👤🐱‍👓😽🐱‍👤😾🙀😻🐱‍👓😽😾😻🐱‍👤🐱‍💻🐱‍👓😼🐱‍👤😿😿🐱🐱‍👓🐈😿😽😿😽🐱‍👓😽🙀😻😾😿🐱‍👓😺😾🐱🐱‍👤🙀🐱‍👓🐱‍👤😾🐈😿🐱‍👤🐱‍👓🐱🙀😺😿🙀🐱‍👓😼🙀🙀🐱‍👤🙀🐱‍👓🙀😾😺🙀😾🐱‍👓😹🐱‍👤😻😾😸🐱‍👓😻😿😺🙀🐈🐱‍👓😼😿😸🐱‍👤😸🐱‍👓🐈😾🐱‍💻😿😸🐱‍👓🐈😾🐱‍💻😿😹🐱‍👓🐱‍👤😾🐈🙀😸🐱‍👓🐱‍🏍😾🐈🙀😸🐱‍👓🐱‍🚀🐱‍👤🐱‍🚀🐱‍👤🐱‍💻🐱‍👓😸😾😸😾😿🐱‍👓🐱‍👓🙀😺🐱‍👤🐱🐱‍👓😾😾😽🐱‍👤😿🐱‍👓😸😿🐱‍👤😿🐱‍🚀🐱‍👓🐱‍🏍🙀🙀🙀🙀🐱‍👓🐱‍👤😿🐱‍👤🙀😻🐱‍👓😾🙀😻🙀😾🐱‍👓😾😾😿🐱‍👤😿"
    keys = decryption(example_input, example_output)

    flag_output = "🐱‍👓🐱‍🏍🐱‍👤😸😿😹🐱‍👓🐱‍🏍🙀🐱‍👤😾😽🐱‍👓🐈😿🐱‍👤🙀😺🐱‍👓🐈🐱‍👤🐱‍💻🐱‍👤🐱‍👤🐱‍👓🐱‍🏍🙀🐱‍🚀😿😺🐱‍👓🐱‍👓😾😹😾😻🐱‍👓😽😿🐱‍🏍🙀😻🐱‍👓🐱‍👓😾😺😿🐱‍🏍🐱‍👓😸😾😸😾🐱‍🏍🐱‍👓😼🙀🐱‍👤😾😻🐱‍👓🐈🐱‍👤🙀🐱‍👤😻🐱‍👓😼🐱‍👤😹🙀😽🐱‍👓🙀😿🐱‍👓😿🐈🐱‍👓😽🙀🐱‍👤🙀😸🐱‍👓😸🐱‍👤🐱😾😼🐱‍💻🐱‍💻🐱‍👤🐱‍👓🐱‍👓🙀😿🐱😿🐱‍🏍🐱‍👓😼🙀🐱‍🏍🙀🐱‍👓🐱‍👓😼🐱‍👤🐈🐱‍👤😿🐱‍👓😸🐱‍👤😹🐱‍👤😺🐱‍👓😽🐱‍👤😽🙀🐱‍👓🐱‍👓😽😾😻😾🐱‍👤🐱‍👓😼🐱‍👤😾😾🐱‍🚀🐱‍👓🐈😿😽😿🐈🐱‍👓😽🙀😺😾🐱‍👤🐱‍👓😺😾😸😾🐱‍🏍🐱‍👓🐱‍👤😾🐱🙀😽🐱‍👓🐱🙀😺🙀😹🐱‍👓😼🙀😿🐱‍👤🐱‍💻🐱‍👓🙀😾😻🙀😽🐱‍👓😹🐱‍👤😻😾🐱‍🏍🐱‍👓😻😿😺😿🐱🐱‍👓😼😿😹😾😼🐱‍👓🐈😾🐱‍🏍🙀😼🐱‍👓🐈😾🐱‍💻😿🐈🐱‍👓🐱‍👤😾🐈🙀😽🐱‍👓🐱‍🏍😾🐱🙀😺🐱‍👓🐱‍🚀🐱‍👤🐱‍🚀🐱‍👤🐱‍💻🐱‍👓😸😾🐈🐱‍👤😾🐱‍👓🐱‍👓🙀😻😾😸🐱‍👓😾😾😿😿🐱🐱‍👓😸😿🐱‍🏍🙀🐱‍👓🐱‍👓🐱‍🏍🙀🙀🐱‍👤🐱‍🏍🐱‍👓🐱‍👤😿🐱‍🏍🙀😽🐱‍👓😾🙀😻🐱‍👤🐱‍👓🐱‍👓😾😾🐱‍👤😿😻"
    flag = decryption(keys, flag_output)
    print(''.join( chr(c) for c in flag))
{{< /code >}}

`irisctf{s0m371m3s_bY735_4r3n7_wh47_y0i_3xp3c7}`

## Forensics
### deldeldel (50 *easy*)
I managed to log more than just keys... perhaps it was too much data to capture?  
deldeldel.tar.gz

添付ファイルの中身は，`klogger.pcapng`というpcapngファイル．
wiresharkで開くとUSB通信であることがわかる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im16.png)

USBデバイスからhostへの通信を見たいので，"URB_INTERRUPT in"のものをフィルタするために，`usb.transfer_type == 0x01`を指定．
さらに，Sourceが1.3.2の通信データが8bytes固定だったので，`usb.data_len == 8`を指定．
よって，以下のフィルタを指定．
```
usb.capdata && usb.transfer_type == 0x01 && usb.data_len == 8
```
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im17.png)

このデータを USB keyboard parser を用いてキーボード入力を取得する．いくつかあるが，  
・[ctf-usb-keyboard-parser](https://github.com/TeamRocketIst/ctf-usb-keyboard-parser?tab=readme-ov-file)  
・[USB Keyboard Parser](https://github.com/5h4rrk/CTF-Usb_Keyboard_Parser/tree/main)　　
参考：[Decoding Mixed Case USB Keystrokes from PCAP](https://blog.stayontarget.org/2019/03/decoding-mixed-case-usb-keystrokes-from.html)

今回は１つ目のものを使用．READMEの通りに実行する．
```
$ tshark -r klogger.pcapng -Y 'usb.capdata && usb.data_len == 8' -T fields -e usb.capdata | sed 's/../:&/g2' > usbPcapData
$ python3 usbkeyboard.py usbPcapData
Traceback (most recent call last):
  File "usbkeyboard.py", line 128, in <module>
    sys.stdout.write(read_use(sys.argv[1]))
  File "usbkeyboard.py", line 91, in read_use
    if KEY_CODES[key][shift] == u'↑':
KeyError: 3 
```
エラーが出た．KEY_CODESに0x03がないということで，適当に0x01-0x03を以下のように追加した．
{{< code lang="python" title="usbkeyboard.py" hl_lines="3-5">}}
import sys
KEY_CODES = {
    0x01:[' ', ' '],
    0x02:[' ', ' '],
    0x03:[' ', ' '],
    0x04:['a', 'A'],
    0x05:['b', 'B'],
    0x06:['c', 'C'],
{{< /code >}}

再度実行する．
```
$ tshark -r klogger.pcapng -Y 'usb.capdata && usb.data_len == 8' -T fields -e usb.capdata | sed 's/../:&/g2' > usbPcapData
$ python3 usbkeyboard.py usbPcapData
Hey  Alice! I think  I'm supposed to give you is flag:

irisctfF{this_keylogger_isS_too_hard_to_use}
GABABCABBABBBCCCCCCCBCAAA A AABAAACCCA       CA  BCBC      
```
なぜか余分な文字が入っているが，flagが得られる．

`irisctf{this_keylogger_is_too_hard_to_use}`

## Web Exploitation
### Political (50 *easy*)
> My new enterprise policy ensures you will remain flag-free.  
https://political-web.chal.irisc.tf  
nc ~    
political.tar.gz  
Hint! The admin bot replying with "timeout" does not mean the bot is malfunctioning; just that the page render timed out after the defined time limit.

URLにアクセスすると，以下の画面が表示される．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im15.png)

また，ncで記載のサーバにアクセスすると，URLの入力を受け付けている．
このbotは自分のTokenを有効にしてくれるみたいである．
```
$ nc ~
== proof-of-work: disabled ==
Please send me a URL to open.

```

添付ファイルの`chal.py`を見ると，`request.args["token"]`があるので，
botに送るURLは[クエリパラメータ](https://apidog.com/jp/blog/url-parameter-introduction/)を用いればよい．
```
https://political-web.chal.irisc.tf/giveflag?token=e6c3985772f86cf7ab9a01fe2e944ec2
```
```python
# chal.py
@app.route("/giveflag")
def hello_world():
    if "token" not in request.args or "admin" not in request.cookies:
        return "Who are you?"

    token = request.args["token"]
    admin = request.cookies["admin"]
    if token not in valid_tokens or admin != ADMIN:
        return "Why are you?"

    valid_tokens[token] = True
    return "GG"

@app.route("/redeem", methods=["POST"])
def redeem():
    if "token" not in request.form:
        return "Give me token"

    token = request.form["token"]
    if token not in valid_tokens or valid_tokens[token] != True:
        return "Nice try."

    return FLAG
```

しかしながら，botの`policy.json`で以下の単語はブロックされているため，そのままでは上手くいかない．
```json
{
	"URLBlocklist": ["*/giveflag", "*?token=*"]
}
```

そこで，[URLエンコード(16進数)](https://tool.hiofd.com/ja/url-hex-online/)を行い，botへ送信する．
（参考：[HTML URL-encoding Reference](https://www.eso.org/~ndelmott/url_encode.html)）
```
https://political-web.chal.irisc.tf/giveflag?token=e6c3985772f86cf7ab9a01fe2e944ec2
=>
https://political-web.chal.irisc.tf/%67%69%76%65%66%6c%61%67?%74%6f%6b%65%6e=e6c3985772f86cf7ab9a01fe2e944ec2
```

webサイトへ戻り，Redeemボタンを押すとflagが得られる．

`irisctf{flag_blocked_by_admin}`



## Open-Source Intelligence
### ✔ OSINT DISCLAIMER (50 *freebie*)
> The challenges this year does not require you to contact anyone. There will be no social media accounts needed to solve these challenges. Please refrain from reaching out to individuals on social media or contacting restaurants to inquire about further details. Such actions are unnecessary and will result in consequences leading to disqualification. Solving this challenge will unlock the rest of the Open-Source Intelligence category  
flag: irisctf{sp01l_f00d_1s_b4d_mk4y}

### ✔ Checking Out of Winter (50 *easy*)
> CHALLENGE'S DISCLAIMER: Please do not include any accent markings or special characters into the flag. Apply proper capitalization to the flag. Here's an example of the flag irisctf{Iris_Hotel} if there is a special symbol like '&' please type out the word 'and'.  
We took our annual road trip to Baja California Sur to visit the beach and play some golf. I like how this location is farther from the city compared to other resorts. I really enjoyed the sweet and savory sauce on the pizza with shredded chicken. After eating, I fell asleep, and half of my legs ended up getting tanned.  
Question: Which hotel did Adam stay at?  
https://osint-food-blog-web.chal.irisc.tf/

該当の記事へアクセスすると次の画像が表示された．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/Pizza_by_Pool.png) 

この画像の上1/3程度をGoogleレンズで検索を行うと，類似の画像が見つかり，以下のウェブサイトを発見．  
[ロスカボスのタイムシェア　ラ・パシフィカ　プール＆ビーチ編](https://blog.goo.ne.jp/oceanpaddler/e/9cd1c2555440ddc86a23fce3d12921ae)

Googleマップで「ロスカボスのタイムシェア　ラ・パシフィカ　プール＆ビーチ」と検索すると，以下の場所がヒット．
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im01.png)

このホテル名がflagである．

`irisctf{Hilton_Los_Cabos_Beach_and_Golf_Resort}`

### ✔ Sleuths and Sweets (50 *easy*)
> CHALLENGE'S DISCLAIMER: Here’s an example of the address format irisctf{3_Chome_12_8_Azabu_Minato}. Please do not include any accent markings or special characters into the flag. Apply proper capitalization to the flag.  
I visited my friend in Japan, and we had some alright crepes! The area had a lot of foot traffic, so we expected a long wait, but it was worth it. I’m usually not a fan of yogurt in my crepe, but it still tasted pretty alright.  
Question: What is the address of this location?  
https://osint-food-blog-web.chal.irisc.tf/

該当の記事へアクセスすると次の画像が表示された．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/Crepe_Diorama.png)
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/Crepe_Station.png) 

１枚目の画像をGoogleレンズで検索を行うと，類似の画像が見つかり，お店は「マリオンクレープ」であるとわかった．
Googleマップで「マリオンクレープ」と検索すると，10件ほどヒット．
２枚目の画像で背景に「日本銀行」が写り込んでいることから，Googleマップで「日本銀行」と検索．
マリオンクレープと日本銀行が隣接するような場所は，左下の方の「マリオンクレープ 渋谷モディ店」のみである．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im03.png)
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im04.png)
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im05.png)

よって，渋谷区神南１丁目２１−３がflagである．

`irisctf{1_Chome_21_3_Jinnan_Shibuya}`

### ✔ Not Eelaborate (50 *easy*)
> CHALLENGE'S DISCLAIMER: Please do not include any accent markings or special characters into the flag. Apply proper capitalization to the flag. Here's an example of the flag irisctf{Restaurant_Name}  
After my long train ride, I visited a deer park and got to feed the wildlife. There were so many restaurants to choose from but I was craving eel. I really like the soup mixed in with the rice and fish. The wasabi threw me off since I don’t normally have it served this way.  
I would recommend this place if you want to find a quiet restaurant to eat at, and wouldn’t mind finding a few small fish bones. Eels are known to carry lots of tiny bones it’s inevitable that you’ll find it in a lot of places.  
Question: What is the full name of the restaurant?  
https://osint-food-blog-web.chal.irisc.tf/

該当の記事へアクセスすると次の画像が表示された．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/Unagi_Bowl.png)

問題文に「deer park」とあるので，奈良公園だろう．
Googleマップで「奈良公園　うなぎ」と検索すると，７件ヒット．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im06.png)

この中から，画像と同じお盆を利用している店を目視で探すと，「うなぎのねどこ えどがわ近鉄奈良駅前店」であると特定．
しかし問題は，この店の英語での**full nameがわからない点**．調べても出てこない．
そこで，Googleマップの左上の３本線 > 言語 > English に変更すると，「Edogawa Kintetsu Nara」と出てきた．

`irisctf{Edogawa_Kintetsu_Nara}`

### ✔ fuel deal (50 *medium*)
> My friend spotted this really good fuel deal for his car, but he won't tell me where he got it. He sent this picture to make me mad and he even blurred out the road signs! Can you find this deal for me?  
The flag is the address of the gas station, omitting the post code and any symbols including commas, replacing spaces with underscores (_), and with all abbreviations expanded.  
For example, if the building is located at 1888 Big Eddy Rd (address), Revelstoke (city/suburb), BC (state/territory) V0E 3K0 (post code), Canada (country), the flag would be:  
irisctf{1888_Big_Eddy_Road_Revelstoke_British_Columbia_Canada}  
fuel-deal.tar.gz

添付のファイルの中身は以下の画像であった．
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/fuel-deal.png)

画像の右上の方の看板に754という数字がある．おそらくこれはバスの番号である．
よって，「754 bus route」と検索．

その結果，以下のサイトを発見．  
754 Rowville - Glen Waverley via Caulfield Grammar & Wheelers Hill  
https://www.ptv.vic.gov.au/route/960/754-rowville-glen-waverley-via-caulfield-grammar-and-wheelers-hill/

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im12.png)

問題の画像にガソリンスタンドの「Shell」のマークがあるので，このバスのルートにおけるShellをGoogleマップで検索．
すると，該当のガソリンスタンドは左上の１件のみであることがわかる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im13.png)

ストリートビューで確認すると，ここが画像の場所であることが分かった．住所は「203 Gallaghers Rd, Glen Waverley VIC 3150 オーストラリア」である．VICのVは「Victoria」である．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im14.png)

`irisctf{203_Gallaghers_Road_Glen_Waverley_Victoria_Australia}`


### ✔ where's bobby 2 (50 *medium*)
> I took this picture while I was on a trip, somewhere, and I saw this beautiful wall art. However, in a shocking turn of events, I don't know where it was! Can you find it for me?  
The flag is the coordinates of the building which contains this wall art, in decimal format and correct to 3 decimal places, and seperated by an underscore (_).  
For example, if the building was located at 50°59'52.934"N, 118°13'28.792"W the flag would be irisctf{50.998_-118.225}.  
files

添付のファイルの中身は以下の画像であった．
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/wheres-bobby-2.jpg)

画像をGoogleレンズで検索を行うと，類似の画像が見つかり，以下のwebサイトから，「地铁7号线 百子湾＜百子嬉戏＞吉祥画」だとわかった．  
https://ameblo.jp/yorinoto/entry-12733913890.html

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im07.png)

Googleマップで「百子湾駅」と検索し，緯度と経度を調べた．しかし，`irisctf{39.893_116.500}`または四捨五入して`irisctf{39.893_116.501}`とするも正解できず．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im08.png)

Wikipediaで百子湾駅をみても同じ座標である．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im09.png)

試しに英語版を見てみると，座標が異なることに気づく．しかしこちらでもダメ．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im10.png)

中文版を見てみると，またしても違う座標になっている．そして，これが正解だった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/irisctf-2025/im11.png)

`irisctf{39.891_116.492}`

