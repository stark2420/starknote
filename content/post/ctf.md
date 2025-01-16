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
- default：name.txtが作成される
- `-`：標準出力にそのまま出力

問題例：
https://stark2420.github.io/starknote/post/new-year-ctf-2025/#bel_money-part_1-100

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