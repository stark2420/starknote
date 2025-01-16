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
$ sudo apt-get install exiftool
```
```
$ exiftool image.png
```

### pdftotext
PDFをテキストに変換．
```
$ apt install poppler-utils
$ pdftotext -v
```
```
$ pdftotext file.pdf
$ pdftotext file.pdf -
```
- defalt：name.txtが作成される
- `-`：標準出力にそのまま出力

問題例：
https://stark2420.github.io/starknote/post/new-year-ctf-2025/#rsa-for-9-687





