+++
author = "Hugo Authors"
title = "UoftCTF 2025 Writeup"
date = "2025-01-14"
description = ""
tags = [
    "CTF",
    "writeup",
    "uoft",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/cover.png"
+++

2025/1/11 09:00(JST) - 2025/1/13 09:00(JST)で開催された UoftCTF 2025 に参加した．

<!--more-->
チームで参加し，結果は，700 点で 897 チーム中 236 位だった．

https://ctf.uoftctf.org/

公式Writeup：
https://github.com/UofTCTF/uoftctf-2025-chals-public

## Crypto
### funny-cipher (100)
> A wheelbarrow ran over the flag. Can you fix it?  
cipher.txt

{{< code lang="" title="cipher.txt" hl_lines="">}}
60_ZMZ_GBWKNREM_KRA__LQM}WEPRGQL__Q_{RWW_M_KIAGPMNMRXDLM_FMWLDQ0BOIAMNPG
{{< /code >}}

問題文を検索すると，以下のサイトがヒット．昨年も同じような問題が出題されたようだ．  
https://ctftime.org/writeup/38474

[Burrow's Wheeler Transform](https://www.dcode.fr/burrows-wheeler-transform) というサイトで復号できるらしい．

復号結果は以下のようになった．
```
LWPMGMP{NRN_XWL_BDWO_MZA_PRIQM_QLKQMRMLMRWD_GRFZAI_NEMAQ_KEGB_MW_600_KG}
```
まだflagにはなっていないが，LWPMGMP=>uotfctfに置換できそう．  
しかしながら，規則性は見つからない．とりあえず，確定している置換を行うと以下になる．
```
uoftctf{???_?ou_??o?_t??_f???t_?u??t?tut?o?_c?????_??t??_??c?_to_600_?c}
```
真ん中の長い単語の部分を考えてみる．同じ文字は同じ文字に置換されることを踏まえると，  
QLKQMRMLMRWD=>substitutionになりそう．
```
uoftctf{?i?_?ou_?no?_t??_fi?st_substitution_ci????_??t?s_b?c?_to_600_bc}
```
だんだん見えてきた．
```
uoftctf{did_you_know_the_first_substitution_ci?her_d?tes_b?ck_to_600_bc}
```
以下がflagである．
```
uoftctf{did_you_know_the_first_substitution_cipher_dates_back_to_600_bc}
```

`uoftctf{did_you_know_the_first_substitution_cipher_dates_back_to_600_bc}`

## Misc
### Math Test (100)
> Complete this simple math test to get the flag.  
nc ~

ncコマンドでサーバにアクセスすると，以下が表示される．計算式はランダムに変化する．
```
$ nc ~
Welcome to a simple math test.
If you solve these basic math questions, I will give you the flag."
Good Luck
Question: 1 + 4 // 5

Answer: 
```

そこで，LinuxのCLIコマンドを自動化するExpectを用いて，自動化する．
`-re {([^\n]+)}`は改行以外の任意の文字列にマッチする．
send コマンドには，`--`オプションがあり，これ以降の引数をすべて文字列として解釈する．
これがないと，負の数送るときに，`-`がフラグとして間違えられてエラーになる．

参考：
https://qiita.com/Mskmemory/items/013dff1a76e58fcdc364#expect%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6
https://manpages.ubuntu.com/manpages/focal/ja/man1/expect.1.html


{{< code lang="sh" title="main.sh" hl_lines="">}}
#!/usr/bin/expect

spawn nc 34.66.235.106 5000

for {set i 0} {$i < 1000} {incr i} {
    expect "Question:"
    expect -re {([^\n]+)}
    set expression $expect_out(1,string)

    expect "Answer:"
    set result [exec python3 -c "print($expression)"]
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
spawn nc ~

（省略）

Correct 1000/1000

Congratz! Here is the flag uoftctf{7h15_15_b451c_10_7357_d16u153d_45_4_m47h_7357}
```

`uoftctf{7h15_15_b451c_10_7357_d16u153d_45_4_m47h_7357}`


## web
### Scavenger Hunt (100)
> You know what to do.  
Visit the website here

webサイトのどこかに隠された7つのflagの断片を探す問題．以下では，開催後にローカルで実行した画像を使用する．

1. ソースコードに，コメントとして記載．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im01.png) 

2. レスポンスヘッダーに含まれている．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im02.png) 

3. `localhost:3000/robots.txt`にアクセス．
```
User-agent: *
Disallow: /hidden_admin_panel
# part4=411_7h3_
```

4. robots.txtに書かれていた`localhost:3000/hidden_admin_panel`にアクセス．Cookieにフラグが追加される．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im03.png) 

5. styles.cssに，コメントとして記載．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im04.png) 

6. Cookieの`user:guest`を`user:admin`に書き換えて，`localhost:3000/hidden_admin_panel`にアクセス．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im07.png) 
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im06.png) 

7. app.min.jsの最下部にあるように，`localhost:3000/app.min.js.map`へアクセス．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/uoftctf-2025/im05.png) 
```
{
    "version": 3,
    "file": "app.js.map",
    "sources": [
        "app.js"
    ],
    "names": [
        "document",
        "addEventListener",
        "startButton",
        "getElementById",
        "scrollIntoView",
        "behavior",
        "style",
        "display"
    ],
    "mappings": "AAAAA,SAASC,iBAAiB,mBAAmB,KAAK,MAAMC,YAAYF,SAASG,eAAe,aAAa,EAAE,GAAGD,YAAY,CACtHA,YAAYD,iBAAiB,QAAQ,KACjCD,SAASG,eAAe,MAAM,EAAEC,eAAe,CAACC,SAAS,QAAQ,CAAC,EAClEL,SAASG,eAAe,eAAe,EAAEG,MAAMC,QAAU,OAC7D,CAAC,CACL,CAAC,CAAC",
    "part7": "c0d3!!}"
}
```

`uoftctf{ju57_k33p_c4lm_4nd_1n5p3c7_411_7h3_4pp5_50urc3_c0d3!!}`

補足：開催後にローカルで実行する手順は，[公式Writeup](https://github.com/UofTCTF/uoftctf-2025-chals-public)からソースコードをダウンロードし，
問題のディレクトリ（Dockerfileのある場所）で以下のコマンドを実行する．
```
$ docker build -t scavenger-hunt .
$ docker run -p 3000:3000 scavenger-hunt
```
`http://localhost:3000/`にアクセスできる．

