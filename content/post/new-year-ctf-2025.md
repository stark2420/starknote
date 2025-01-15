+++
author = "Hugo Authors"
title = "New Year CTF 2025 Writeup"
date = "2025-01-15"
description = ""
tags = [
    "CTF",
    "writeup",
    "grodno",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/cover.png"
+++

2025/1/13 02:00(JST) - 2025/1/15 02:00(JST)で開催された New Year CTF 2025 に参加した．

<!--more-->
個人で参加し，結果は，1587 点で 658 チーム中 159 位だった．

http://ctf-spcs.mf.grsu.by/

## Crypto
### The hacker sent (100)
> The hacker sent a mysterious message. I don't understand anything...  
`¥☐ü_ç☐_☐∏_∲Λ┏_Λ_₪Λ¥_Λ⊥_↻┏¥þ⊥☐`  
Answer in the form of grodno{plaintext}

暗号文を検索すると，1件だけヒットした．
https://znanija.com/task/49775286  
このウェブサイトでは，全く同じ文章を，解読してほしいという質問に対して以下の回答がついている．
> you go on for a way at orybto или rypto otpyl otlab

初めはよくわからなかったが，よくよく暗号文を眺めると，英語の代わりに記号で書かれていることに気づいた．
```
you_go_on_far_a_way_at_crypto
```

これがflagとなる．上記ウェブサイトの回答は若干間違っていたようだ．

`grodno{you_go_on_far_a_way_at_crypto}`

## Beginner
### Crashme (100)
> Can you break the program?  
nc ~  
crashme.c

添付のコードは以下である．

{{< code lang="c" title="crashme.c" hl_lines="">}}
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char *argv[]){
    char buffer[32];
    printf("Give me some data: \n");
    fflush(stdout);
    fgets(buffer, 64, stdin);
    printf("You entered %s\n", buffer);
    fflush(stdout);
    return 0;
}
{{< /code >}}

ncでサーバにアクセスし，適当に文字を入力すると以下のようになった．
```
$ nc ~
Give me some data: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Wrong answer ...
```

問題文の通り，bufferの大きさを超える長い文字列を与えるとflagが得られた．
```
$ nc ~
Give me some data: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
You entered: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Your flag: grodno{0baf70S3gfaults_4re_a_gr3at_fr1end_0f_h4ck3r5dfecf0}
```

### Broken QR (100)
> I took a picture of this QR code on a cybersecurity forum. I just didn't notice two problems: vandals scratched it and someone "imprinted" a palm print on it. Whatever it is, the flag needs to be read...  
Flag in grodno{text-string} format  
QR-with-fingers.png

添付の画像ファイルは以下である．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/QR-with-fingers.png) 

QRコードの問題は以下のサイトが便利である．  
・QRazyBox: https://merri.cx/qrazybox/

New Project > New blank QR code で新しいQRコードのプロジェクトを作成．
今回のQRコードのサイズは，画像より，QR-Code version: 37x37 (ver. 5) を指定．
さらに，三つの隅のいずれかの青い部分をクリックし，Format Info Patternを設定．
今回の場合，正しそうなのは，左下と右上なので，それと一致するものを選択する．
よって，Error Correction Level: H，Mask Pattern: 4 とする．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im01.png) 

以上を設定すると，ファインダパターンとマスク部分は以下であることがわかる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im02.png)

よって，添付の画像ファイルの不要な部分を消しつつ，ファインダパターンとマスク部分を適当に修正．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/QR-with-fingers_.png) 

適当な読み取りサイトに投げるとflagが得られる．  
・QRコード読み取り: https://qrcode.onl.jp/

`grodno{It's_h4rd_t0_l1ve_without_R33d-S0l0m0n_c0d3s!}`

### RSA for 9+ (687)
> All you need to do is decrypt the secret encrypted using the RSA (Rivest-Shamir-Adleman) algorithm.  
And so 50 times... And the time to answer is no more than 5 seconds...  
As is known, the strength of the RSA algorithm is determined by the complexity of factoring large numbers into prime factors.  
But we will consider the “very childish option” - you will be given the module n and both exponents - e and d.  
nc ~

ncでサーバにアクセスすると，以下が表示され，5秒後にタイムアウトする．値はランダムに変化する．

```
$ nc ~
Все что вам надо - расшифровать секрет, зашифрованный с помощью алгоритам RSA (Ривест-Шамир-Адлеман). И так раз 50 ... И время на ответ - не больше 5 секунд ...

Как известно, стойкость алгоритма RSA определяется сложностью разложения больших чисел на простые множители.
Но мы рассмотрим "совсем детский вариант" - вам даны модуль и обе экспоненты.

Раунд 1/50

e: 0x10001
d: 0x35db5582f9231875e230ba7b8ef6eda7d9e28f34d5f709d06bd61744ccd86c5bae13dad820193ec1cbe28b3a55e023740c573401a018213c2fab42052f90d89bcbf0e640578f79ca518e0547721f5c3ef385354b8a96444d799a729d73ce05e0b778f8f6135fb946d212fb06096cbcd43c8f4023b95e27ab667d1cb9cbf30d8aa174c0b823109422263bca5c02ff26064cbba5fd6bf4066fc0b92694a585a8c335a25792f768b8494fdc1bc99bca72694b8b812269b32330043c3ad854a19b6474062525ab1d2b94759784443ce420da08c6be54b6e0477fe339a6855a920b3a0298d0ad2a16c4baad9a85b70eb85e3a67032a9fab89b87ddcb78151de246479
n: 0x856781316e0869fa5619b4883f7f2a09f1333bd91d6eab2d6f0b1e501db26995166c7e35b596723dd581efad0821d6d26ce70f32ea2169d60422c581437599ad06c2a86879fd57b2dbdfc9b6ce9f7a8946d8dc2628ed290ab843760fe43eb7037a1abf742b7b0fdf997472b99f8a67627e2a947447276dcbb1d3407cdca735f450552c447f24419498c173777177ad63cb28b9a5bffd79fb6e6081968a02eeaa30ac2237547c4f2e87511233a25159b6e945795ae28c75ce4f6dfdbddb030dac6fcbe96bb43665d7defcbedd35b85287a8f34b2c5a50c5faf0d2a5bb15fde99a2e2c9a6f5511b54bf7ed5ef8f6a4995fe97d38ab2d25b5549a4a18cc2cf829d5
secret ciphertext (b64): /X/KHo0WeqVpxJM6jDhAWbOaUWbC4XzHt8tkJ1HfedDZO2W0H+HgyCGS6HrVU/m0nh5/WFryNm1zLllgntOOyJdznZyYN8bjA0PlKi0igMaixWgukQHbrqSKnHbCuguSOIUfaOi4aCV27Dq70fBdAkkoG4ik3cdOIFuOlWfpeAf28KgSJ2doMN/n3+DtO7lePcL7SaL+S1yOo0gKmG4pU773zKRA2siQC2N2C3BMj/xXqOB+DxiNQ0L5F80x2jMPvI6urYVqGatydmSrgh5JZ7W6bdDnf2zCfsGlLGS4if+mt0uqWWCAqdZX8cC0Oh3bAoNihEtaZekTg6PFo7AsUQ==

Plaintext is (b64):
TimeOut...
```

まず，RSAの復号をPythonで作成．
{{< code lang="python" title="test.py" hl_lines="">}}
import base64

e= 0x10001
d= 0x1700797cff97f49928e34045f03873bf6a9878c1daae1e9454297dfc5dd4f67a1632c23bfdbabc73ce682bf19cd79cb0767097e0c7f242c32588eb38cff5632d49cc4193fc94fc33f65668ed46ff2415e1f682711e4699d6cb89e67ee5bf0581f80c361815945d434446ade7ac087ad1f6ff46ca8af2246bbe25194cc402f618aed97c7e38864b8f8dad1b927928621f03b4068a162dfe5a59ab060d5df3be7a6993be9205309de95b07e8a38a8e6611119241be49258eed93c441981187c4e1fcb330a19ec99a5ecd57b86f09d18fadb367af1a1464b9815b01a55aebf374d6815fcbdedec1febcb1092958d37f21882482569acf39f980adedfdacaa28b21
n= 0xd4adf581c5d0300110a0fb2188e62388e5b3d757f62cd4bf606d4be9e3bb635c572549ec59d067633598ef045ccb2df1aa831e196b985e63b1d8275bb563821e84adc63890cb270a7e5a1f56adcfdbc66e3e91e6996a9f667a8907d485fe184e80cd14618151016e6e27f3ce1563108ecd310a2db711503fa8c55348233d9ec42cf254c9d0d1fee0d6934dfe35e06a1071df2a42efe1af18d579058c2c67c8eee2b8a9115e4344aa8ccd440b6202bb601ecfa7b1f1506f5ebc0827eff5a16891a76cfc72c1d8e74104b145d97aa10d5c6318fd99aa91d48a8ffe1ec058fc7882f0372aaeb9dbae30c44d7b8b1b966340c8574658d598ed9dca1299d1c16a401f
c_base64_str = "NHr+wcFCnZSHYNGVr4mQY3DktS2r+OSe7fEEQEyFokstQe4cDvGOlaeQilebJVnqErNK+8fIxf4VjqedOyunMfVKVeTGYaQea6MfshFSTUbRN318akEK/C+lrEAGBvx9YfWndX0MZpvpUZAQuV0cYQkshfxijOfBYRpElQS3i9WNWEhPyf+A7UrmZckLiR/HLtVIYUL/Zl95R3JNJHYFz3XVuN/IcCEqjIXVf5SeKFG8XVPj80rK7RXDvv/i9wE2nNqKjbNbGPq5KXm2ofOxEYfLrToO3mJu7+YB+BW5mHAiOdQOswsrEhivF8PO0MRCslnvM3Z2eBEnS3IRDmh4gQ=="

c_base64_byte = c_base64_str.encode()
c_byte = base64.b64decode(c_base64_byte)
c = int.from_bytes(c_byte, byteorder='little')
m = pow(c, d, n)
m_byte = m.to_bytes((m.bit_length() + 7) // 8, byteorder='little')
m_base64_byte = base64.b64encode(m_byte)
m_base64_str = m_base64_byte.decode()

print("c_base64_str:\t", c_base64_str)
print("c_base64_byte:\t", c_base64_byte)
print("c_byte:\t\t", c_byte)
print("c:\t\t", c)
print("m:\t\t", m)
print("m_byte:\t\t", m_byte)
print("m_base64_byte:\t", m_base64_byte)
print("m_base64_str:\t", m_base64_str)

# c_base64_str:	 NHr+wcFCnZSHYNGVr4mQY3DktS2r+OSe7fEEQEyFokstQe4cDvGOlaeQilebJVnqErNK+8fIxf4VjqedOyunMfVKVeTGYaQea6MfshFSTUbRN318akEK/C+lrEAGBvx9YfWndX0MZpvpUZAQuV0cYQkshfxijOfBYRpElQS3i9WNWEhPyf+A7UrmZckLiR/HLtVIYUL/Zl95R3JNJHYFz3XVuN/IcCEqjIXVf5SeKFG8XVPj80rK7RXDvv/i9wE2nNqKjbNbGPq5KXm2ofOxEYfLrToO3mJu7+YB+BW5mHAiOdQOswsrEhivF8PO0MRCslnvM3Z2eBEnS3IRDmh4gQ==
# c_base64_byte: b'NHr+wcFCnZSHYNGVr4mQY3DktS2r+OSe7fEEQEyFokstQe4cDvGOlaeQilebJVnqErNK+8fIxf4VjqedOyunMfVKVeTGYaQea6MfshFSTUbRN318akEK/C+lrEAGBvx9YfWndX0MZpvpUZAQuV0cYQkshfxijOfBYRpElQS3i9WNWEhPyf+A7UrmZckLiR/HLtVIYUL/Zl95R3JNJHYFz3XVuN/IcCEqjIXVf5SeKFG8XVPj80rK7RXDvv/i9wE2nNqKjbNbGPq5KXm2ofOxEYfLrToO3mJu7+YB+BW5mHAiOdQOswsrEhivF8PO0MRCslnvM3Z2eBEnS3IRDmh4gQ=='
# c_byte:	     b'4z\xfe\xc1\xc1B\x9d\x94\x87`\xd1\x95\xaf\x89\x90cp\xe4\xb5-\xab\xf8\xe4\x9e\xed\xf1\x04@L\x85\xa2K-A\xee\x1c\x0e\xf1\x8e\x95\xa7\x90\x8aW\x9b%Y\xea\x12\xb3J\xfb\xc7\xc8\xc5\xfe\x15\x8e\xa7\x9d;+\xa71\xf5JU\xe4\xc6a\xa4\x1ek\xa3\x1f\xb2\x11RMF\xd17}|jA\n\xfc/\xa5\xac@\x06\x06\xfc}a\xf5\xa7u}\x0cf\x9b\xe9Q\x90\x10\xb9]\x1ca\t,\x85\xfcb\x8c\xe7\xc1a\x1aD\x95\x04\xb7\x8b\xd5\x8dXHO\xc9\xff\x80\xedJ\xe6e\xc9\x0b\x89\x1f\xc7.\xd5HaB\xfff_yGrM$v\x05\xcfu\xd5\xb8\xdf\xc8p!*\x8c\x85\xd5\x7f\x94\x9e(Q\xbc]S\xe3\xf3J\xca\xed\x15\xc3\xbe\xff\xe2\xf7\x016\x9c\xda\x8a\x8d\xb3[\x18\xfa\xb9)y\xb6\xa1\xf3\xb1\x11\x87\xcb\xad:\x0e\xdebn\xef\xe6\x01\xf8\x15\xb9\x98p"9\xd4\x0e\xb3\x0b+\x12\x18\xaf\x17\xc3\xce\xd0\xc4B\xb2Y\xef3vvx\x11\'Kr\x11\x0ehx\x81'
# c:	         16344115981270018471112946989312422042857570270685786586240532225117263862877939100205817656761887466187423882587462771389560854095077382455856513812623806819867052761499110883152550514103115350523202943915653737415313457683166712148107687011457824595591821556140101775179618839508502513093045781034562440588786507633242716273029885984234631504849614102443092520432905419927350660899701316137620460972985886127104051702143334933006668697681852871792086682488701062731697719032043721813108446394531753010274019409101492488102292279280368988041812229656932662199735359913667124181858859530755916236180012532766246599220
# m:	         43562097050677961452588407792014750473885100382974576
# m_byte:	     b'pretend_pretty_prevent'
# m_base64_byte: b'cHJldGVuZF9wcmV0dHlfcHJldmVudA=='
# m_base64_str:	 cHJldGVuZF9wcmV0dHlfcHJldmVudA==
{{< /code >}}

次に，LinuxのCLIコマンドを自動化するExpectを用いて，自動化する．
`-re {([^\n]+)}`は改行以外の任意の文字列にマッチする．
`set e_val [string map {\r ""} $e_val]`でキャリッジリターンを削除する．
Pythonコード中では「"」は使うことができないので「'」を必ず使うこと．

参考：
https://qiita.com/Mskmemory/items/013dff1a76e58fcdc364#expect%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6

{{< code lang="sh" title="main.sh" hl_lines="">}}
#!/usr/bin/expect

spawn nc ctf.mf.grsu.by 9019

for {set i 0} {$i < 50} {incr i} {
    expect "e: "
    expect -re {([^\n]+)}
    set e_val $expect_out(0,string)
    set e_val [string map {\r ""} $e_val]

    expect "d: "
    expect -re {([^\n]+)}
    set d_val $expect_out(0,string)
    set d_val [string map {\r ""} $d_val]

    expect "n: "
    expect -re {([^\n]+)}
    set n_val $expect_out(0,string)
    set n_val [string map {\r ""} $n_val]

    expect "secret ciphertext (b64): "
    expect -re {([^\n]+)}
    set c_val $expect_out(0,string)
    set c_val [string map {\r ""} $c_val]
    
    expect "Plaintext is (b64):"

    set result [exec python3 -c "
import base64
e = $e_val
d = $d_val
n = $n_val
c_base64_str = '$c_val'
c_base64_byte = c_base64_str.encode()
c_byte = base64.b64decode(c_base64_byte)
c = int.from_bytes(c_byte, byteorder='little')
m = pow(c, d, n)
m_byte = m.to_bytes((m.bit_length() + 7) // 8, byteorder='little')
m_base64_byte = base64.b64encode(m_byte)
m_base64_str = m_base64_byte.decode()
print(m_base64_str)
    "]

    send -- "$result\r"
}

send "exit\r"
expect eof
{{< /code >}}

実行する前に，実行権限を付与しておく．
このスクリプトを実行すると，flagが得られる．
```
$ chmod +x main.sh
$ ./main.sh
（省略）
Раунд 50/50

e: 0x10001
d: 0x10c208bb88060c175bcb9758215059273d10132709d185e46deb26cbf96bf1b2e84b6f72beb3d9316a794681ba5fe9daae74b80ba39b48f18c7cb0a2e126e494158f61e224a73f35f08190d66662e61ea2c0968f10528710c5e0e19229f337fe44377f0eee15136afcdeb970d9c04b78df8ba84d66936456408add7e39c370a7f52207bc038c86076751b8dc9e05596f6c9b61ba11b96332da32149782bd63f8eb8e35bed4ce0b8902702b2061d86c04646757474e018817bbfa6688da322fb981d538e3638a561a9f37d581bc425508764e872d5c451094bcda3eb856446496bafb48511b6db583d58216be06a36075faa7e1ae059523fa4236277c8bf1e1
n: 0xd44b2885d7f6704ced24d70354df873cbea57ca83abfda8de98bd880465479df813cd1fd0161c6b9fab90e01196ad4fa5753b83b6e21dfe8c7b2cb31f809c8db4a1185e1b5831824248731380b47e067e50ac0e80f62ce8bfd2dc32f8c24844bd2197f86b869a428ebe520f7f9fc9049ea0e1caffa3ba8542acf578aea70f1caaf6176c02b0cf9c40d047f3168de1ac5a484e1296572591780f2cf4e6b33799c92649547ee9b583b81ab0fc135e9cdc24684af02cb0bcd257afc835a00856e9c15b957c19a7047c880025646afbb6b8dd303bcb5c8c86ffb0244c4aadcb91cd4d258c3ea093cae419e730fb218c5d70d25d4cee9d3000ea1cca520af28adc1a9
secret ciphertext (b64): xyunslieQbIRsSlCf6DUzK1rpD+XxcoBDAqhsI/bsPEZaqqbtKh1nVN/0HfCWXSGQAwMRWmEdFz2vbuhzj/RfFsQO4oa2N5vSaTqE0j+f/WNMszXOE7qjwAvjjxPig+RIE9PgISol98AFgg4tV0w8jmki8P/kym547TK91lEBPtLTvh27WI7Lw+nxJ8JBA5A1dgPJg+H5eTmtEE/hXBo7Vd4A3ozra+X72ylj2qKf8Emc70smb98hiahC54HDR/6mCtPw4LgCzvJ5bqmQmZgpFBNOtyABefSYKgQcPF2K7bmnuxoKCWIrg7d1cx71qpKaLYxF6047TPxOVhqJjz0zw==

Plaintext is (b64):
Y29va2llX2Nvb2tpbmdfY29vcGVyYXRpb24=
Правильно: cookie_cooking_cooperation
grodno{2c64b0Take_y0urself_the_b1ggest_candydfac0d}
```


## PPC
### My best regards (100)
> （問題文を保存し忘れたため省略．）

ncでサーバにアクセスすると，以下が表示され，5秒後にタイムアウトする．値はランダムに変化する．
```
$ nc ~

██████╗ ██████╗  ██████╗ ██╗  ██╗███████╗███╗   ██╗     ██████╗ ██████╗
██╔══██╗██╔══██╗██╔═══██╗██║ ██╔╝██╔════╝████╗  ██║    ██╔═══██╗██╔══██╗
██████╔╝██████╔╝██║   ██║█████╔╝ █████╗  ██╔██╗ ██║    ██║   ██║██████╔╝
██╔══██╗██╔══██╗██║   ██║██╔═██╗ ██╔══╝  ██║╚██╗██║    ██║▄▄ ██║██╔══██╗
██████╔╝██║  ██║╚██████╔╝██║  ██╗███████╗██║ ╚████║    ╚██████╔╝██║  ██║
╚═════╝ ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝╚══════╝╚═╝  ╚═══╝     ╚══▀▀═╝ ╚═╝  ╚═╝


Вам будет направлено 50 QR-кодов, содержащих критически важную информацию. Правда, среди них встречаются поврежденные QR-коды.
    Ваша задача - создать алгоритм восстановления данных из поврежденных QR-кодов, чтобы избежать потерь критически важной информации.

    Время на ответ - не больше 5 секунд ...

===============
You will be sent 50 QR codes containing critical information. However, among them there are damaged QR codes.
    Your task is to create an algorithm for recovering data from damaged QR codes in order to avoid losing critical information.

    Response time is no more than 5 seconds ...

Round 1/50
QR-code (base64): iVBORw0KGgoAAAANSUhEUgAAAeoAAAHqAQAAAADjFjCXAAADEklEQVR4nO3aTW70IAyAYUtzgBwpV58j5QBIfF/8hzNpqyZk0cXrRaspPHRjATYjfSbeAofD4XA4HA7/S1w8XvtfXjYk6ybS30sTWXsTH9hi6gqHT3L90LfXjmKN97Kvqz/kZVNsdAg4fIbvybga96zUfLUsLem7T8nV4PBnuMWyj+3zJdD/qSKW13D4w3w/jC3KanY2+w84/CGuv3TMUrX7QuVG2H9zvsPhv+aZm3FKf/sjpsLhk7xG3AO9+uhejPjAcS4cPsPj4ufJaHWIbotNnMeABhw+zVNqvvpqy7gvioz7og/A4ZN8D8mICS3P4RjQeWuHw+e51xyRjOKPHCIlh/tHHxAOn+MxddS30VBec3Eb2ODwh7htfB46lv3lbLz404b46AqHz3HdJT22V90qs/q1dTNf4fBZXpN2qwnq7ebcQ/2lLebC4RNcRtLax5btPt0qJXbOr3daOPw6P22VmpuidYh4ZAsGDn+G16htZNsl88sr0WT++PIAHH6Diz9etFIHO4+OjK+71I0UDp/gUYLo3ph8ex2Oauv5xUba4fA5Xr4ZNZ5sc6FSluh/OLf74PA7PKJeEO3216Q2XiKvP3daOPw6t4ZKlLtr93N43Aj9gjjazXD4HNdUjRe0OtUTuWyfUp574fAJLlrVSla6vSRoVB+Sq2kzBg6f5ou/1p5L4Bj9+YCGw6/yOId7pOp4xi2dljiqz40XOPw6j65Kzs+vDFiW9iiQjykNh09wP5GXHi2YsRnmR69IvBg+5jwcfp2Py6CMdp/na8vqo0VtfH5Qg8Mv8zFrxLgMin9vILfP5aPJDIff4HEsS905x7pZfYjF6VoIh1/nI0b14b3kyFLP3H48vuHw29yTMXt+dW/U9G0+wSQc/gTXD34t9IW6df9MKpK4Fp6eNuDwOzyzVO+GefErJ/dbMr5o98HhU9y7elEWi9T0PRTDcPgj3O6B3m7OB92leap6M1Dg8Ae4/op7oKZl7/G3KEGkrGEBh09wj6g+xt7oCRqdZo01/yEcfp/fDTgcDofD4XD4X+H/AOf9At+yfkSaAAAAAElFTkSuQmCC

Your answer:

Time Out...
```

まず，QRコードの復号をPythonで作成．

{{< code lang="python" title="test.py" hl_lines="">}}
import base64
import cv2
from pyzbar.pyzbar import decode

base64_data = "iVBORw0KGgoAAAANSUhEUgAAAeoAAAHqAQAAAADjFjCXAAAC/klEQVR4nO3aSW7rMAyAYQE+QI7kq/tIPkAAvoSjPHSwlQd08WvRurE+dUNQIpUmI2NpcDgcDofD4fC/xJuPyZ5enzz9k/n1JLK+Plv0xdryBRw+xvUPWV98tqmT+NTH01aLt5N0Ag4f4e+p9m6NyF3e8v1DX2zeTnD4B7nmxk2CfKHloVN9DTj843z1+RGlsV9rDv05aOHwX3P9tcbHOd/Dt06JP+7vcPhvuY9Iht/8iKlw+CDvRh0Gm+3N76f4M0I6J8PhI9yLXLG0GOfAOBbqkpogY7UZDh/lWnhkzWHtljgR1inRD43HVAmHX+de33rN29UmLaUuucRGHgwOv8ut5tDYrK6yZknvvrS2f4LDB3m/GWdY6tR84Zt2LH5MlXD4Vf4QiYUqIlurc2Bt3x7NcPggtzokz4GyRVmgaKieFiNw+FVu7zQZSpwNrecy+/Ldd1mkBhw+wP22rPUZUdZKixXDVRvD4YM8wtAvL7K1PMcaddMWOzccPsg3bbxovERurBjO/wWHf4BHqGr1YbwuOSx9xpJ95QKH3+U+IlRbdf/0KTt9rbW8+IDDB3nFYY5ul7bqVy/UJM+GcPgY9+p3y+veVtYK1Wfu3HD4ED8L2i5V2pMl0v74CIcP8LOgFcm0uOR5sbVqRsPhY3w+01LbsvShWgMO/y/cipFsvFgJvL9Qg8Nv8Nai0tiNJXKjVyTbKXD4fS52eRFt5H5CBGhwHYcKGg6/zuuktx+RGwM9++QKh9/nfaVxMqxAztuPdrK/w+FXuTX05jMZO7eN95SuXobD7/PNtwV245n3trnGsfECh1/m3w8L2mzBdMdHOPw+92iMDbrbqq0Y0QuNR61xuMaFw6/zWX/ZbdnUbcG+Nz9E4n4t8yocPsrrjjY7LZ4Rg+s826DzH8Lhn+DNvoBn3b8WnZY6L9qUL06VcPgtvkmVLj1VKlq9Gf1lzMPhv+YRkTYru8r7YYF8rKDh8Ovcx9SvodyqX536kEykcPg4vzvgcDgcDofD4X+F/wOUtm/WGicMDwAAAABJRU5ErkJggg=="
image_data = base64.b64decode(base64_data)
with open("decoded_qr_code.png", "wb") as file:
    file.write(image_data)

image = cv2.imread('decoded_qr_code.png')
qr_codes = decode(image)
for qr in qr_codes:
    qr_data = qr.data.decode('utf-8')
    print(qr_data)

# Happy_New_Year_-_may_it_be_your_best_year_yet!
{{< /code >}}

次に，LinuxのCLIコマンドを自動化するExpectを用いて，自動化する．
`-re {([^\n]+)}`は改行以外の任意の文字列にマッチする．
`set e_val [string map {\r ""} $e_val]`でキャリッジリターンを削除する．
Pythonコード中では「"」は使うことができないので「'」を必ず使うこと．

参考：
https://qiita.com/Mskmemory/items/013dff1a76e58fcdc364#expect%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6

{{< code lang="sh" title="main.sh" hl_lines="">}}
#!/usr/bin/expect

spawn nc ctf.mf.grsu.by 9031

for {set i 0} {$i < 50} {incr i} {
    expect "QR-code (base64): "
    expect -re {([^\n]+)}
    set data $expect_out(0,string)
    set data [string map {\r ""} $data]
    
    expect "Your answer:"

    set result [exec python3 -c "
import base64
import cv2
from pyzbar.pyzbar import decode

base64_data = '$data'
image_data = base64.b64decode(base64_data)
with open('decoded_qr_code.png', 'wb') as file:
    file.write(image_data)

image = cv2.imread('decoded_qr_code.png')
qr_codes = decode(image)
for qr in qr_codes:
    qr_data = qr.data.decode('utf-8')
    print(qr_data)
    "]

    send -- "$result\r"
}

send "exit\r"
expect eof
{{< /code >}}

実行する前に，実行権限を付与しておく．
このスクリプトを実行すると，flagが得られる．

```
$ chmod +x main.sh
$ ./main.sh
（省略）
Round 50/50
QR-code (base64): iVBORw0KGgoAAAANSUhEUgAAAeoAAAHqAQAAAADjFjCXAAADBElEQVR4nO3aQY6zMAyGYUscoEfi6hypB0DK38af7bSwgfSXZvFmwTBDnszGcmKDtZmxGRwOh8PhcDj8L3HTWF53jxaX91ifi9+9Zu2vyzOmrnD4JO+/tPfU9TVLz2KhGn21QcDhM/ypAO1h+ZJ9jd1qflPQ9hhe4PAfck+LWw/Vp31u33D4f+JrzDqkSg9pOPxnPCaMR8DvMWZOOHyaayxNkftx0fBfYyocPsmHsdk7S74jdzOlxeLHyXD4BK9tebdxDCVwFCj22A9BC4df51H46jBoOgLWiBbg2QYNh9/gPQ6VIB/57KvTF0fFuoPD53hTllyfy2nQVgmsAyIcPsf9MKhnZudHQN+5+4OvoIXD73D9ZY9zYCykHPrY804LweE/4R6RWYyMKEI6du6zWgYOv8gzGGOrjtPfZmryaY2mQyMcPsvjhVrUwRnDH4WHDakSDp/lvkZvrShAtVoVw4rmzKFw+Czf4jBoqn6VG32rVvc5FhrWhcMneKXFZ9xV4eEx3LRpK7jh8FnuQassGcfCnN/DN1NltWXg8PvcfGS5q0v8LatfU5Xc4PBf8CUutVX7Zlx7c1/IvFSBw2d5bdB6oRYN5SpQvj7Ug8NneeRBszoRmsqSaPJZHBCPJTAcfoNHChzvam9WqvxMpHD4JO9T62uVyo3xxfsSPI6KcPg011sN5Uvt1xmbrdaNjww8wOHwCW65I2/Ox0ucEvfcm+HweR5Fbsso7ai1oclsQ0fwtFsIh1/i6q98h2p/pijVjuwHRDts0HD4Vd5nKQ969aE6uOVWXanyrASGwy/zsfHiXwbsfhdP4+VtDv+fcPh9XsPrWz8gfjRetHiLM+QKh8/xCEX1VwJlV7kq4mjG+D+Dwyd4D8GO2rHTN8rWxo4MHD7D80WtojQq3S2bMer5vcfjrFsIh9/lsQXvMWtYNwPZa2M4/Ef8NGhbLqnFv4+FcPgd3n/0Dbqq3zgbWn6PHJMPqRIOv841xIdOX77fGE6Exw0aDr/O7w44HA6Hw+Fw+F/h/wBy9nPVFCU1wwAAAABJRU5ErkJggg==

Your answer:

Wishing_you_a_year_of_health,_happiness,_and_success!
Правильно / Good answer: Wishing_you_a_year_of_health,_happiness,_and_success!

grodno{0b83b0345f4a71809cbee57f2ce9}
```

## OSINT
### Beer & Books (100)
> （問題文を保存し忘れたため省略．添付画像の緯度経度をgrodno{52.221,21.016}のフォーマットで求める問題．）  
chall_photo.jpg

添付の画像は以下．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/chall_photo.jpg) 

Googleレンズで画像検索すると，似た写真が出てきた．[三つ目のサイト](https://www.otodom.pl/pl/oferta/kawalerka-inwestycja-niski-czynsz-ID4umPV)はまさに同じ場所．
このサイトに地図も載っていた．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im03.png)

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im04.png)

また，添付画像には看板が写っており「Plac Konstytucji」と書かれている．
Googleマップで検索すると，先ほどのサイトの地図と同じ場所が出てきた．この周辺で会っていそう．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im05.png)

すぐ下の交差点だとわかった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im06.png)

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/im07.png)

`grodno{52.221, 21.016}`
