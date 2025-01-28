+++
author = "Hugo Authors"
title = "CTF ツール まとめ"
date = "2025-01-15"
description = ""
tags = [
    "CTF",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/cover.png"
+++

CTFで利用できるツールをまとめる．
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

問題例：
https://stark2420.github.io/starknote/post/new-year-ctf-2025/#broken-qr-100  
https://stark2420.github.io/starknote/post/new-year-ctf-2025/#my-best-regards-100

## Web
### FFUF
WEBファジングツール．
```
ffuf -w wordlists.txt -u http://example.com/FUZZ
```
https://jpn.nec.com/cybersecurity/blog/210604/index.html  
https://qiita.com/kk0128/items/5186cbe6062e0f887e3b

### 探索
- ソースコードのコメント
- リスポンスヘッダー
- robots.txt
- robots.txtのDisallowに指定されたディレクトリ
- ソースマップ（.mapの拡張子で終わるJSONのファイル）`app.js.map`
- Cookie
- Cookieの`user:guest`に対して`user:admin`で制限された特定のディレクトリへアクセス


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

### 脆弱なRSA暗号
- (RSA暗号でやってはいけない $n$ のこと) https://www.slideshare.net/slideshow/rsa-n-ssmjp/72368516#1
- $N$ が既知で，暗号化オラクルあり，平文が既知で暗号文を求める場合
- $N$ が未知で，暗号化オラクルあり，平文が既知で暗号文を求める場合

参考：
https://zenn.dev/asusn/articles/6bb19c694e94ce#%E6%B1%8E%E7%94%A8%E3%83%84%E3%83%BC%E3%83%AB


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


