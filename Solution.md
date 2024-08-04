## Challenge Infomation
Challenge Name: Mr Robot CTF
Difficulty Level: Easy
Author: Ben & TryHackMe

## Introduction
Based on the Mr. Robot show, can you root this box?

## Prerequisites
* Nmap
* GoBuster
* Hydra

## Steps To Solve The Challenge
### 1. Reconnaissance
### 1.1: Intial Scan
command: `nmap 10.10.197.100`

**Findings:**
```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-05 00:44 IST
Nmap scan report for 10.10.197.100
Host is up (0.16s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE
22/tcp  closed ssh
80/tcp  open   http
443/tcp open   https

Nmap done: 1 IP address (1 host up) scanned in 10.23 seconds
```
### Step 1.2: Detailed Service Enumeration
**HTTP Service:** Used **gobuster** to find directories.
Command: ```gobuster dir -u 10.10.197.100 -w /usr/share/seclists/Discovery/Web-Content/common.txt -q -o gobuster-common.txt```
**Findings:** Discovered `/admin` directory.

```
/.htaccess            (Status: 403) [Size: 218]
/.htpasswd            (Status: 403) [Size: 218]
/.hta                 (Status: 403) [Size: 213]
/0                    (Status: 301) [Size: 0] [--> http://10.10.197.100/0/]
/Image                (Status: 301) [Size: 0] [--> http://10.10.197.100/Image/]
/admin                (Status: 301) [Size: 235] [--> http://10.10.197.100/admin/]
/atom                 (Status: 301) [Size: 0] [--> http://10.10.197.100/feed/atom/]
/audio                (Status: 301) [Size: 235] [--> http://10.10.197.100/audio/]
/blog                 (Status: 301) [Size: 234] [--> http://10.10.197.100/blog/]
/css                  (Status: 301) [Size: 233] [--> http://10.10.197.100/css/]
/dashboard            (Status: 302) [Size: 0] [--> http://10.10.197.100/wp-admin/]
/favicon.ico          (Status: 200) [Size: 0]
/feed                 (Status: 301) [Size: 0] [--> http://10.10.197.100/feed/]
/image                (Status: 301) [Size: 0] [--> http://10.10.197.100/image/]
/images               (Status: 301) [Size: 236] [--> http://10.10.197.100/images/]
/index.html           (Status: 200) [Size: 1188]
/index.php            (Status: 301) [Size: 0] [--> http://10.10.197.100/]
/js                   (Status: 301) [Size: 232] [--> http://10.10.197.100/js/]
/intro                (Status: 200) [Size: 516314]
/license              (Status: 200) [Size: 309]
/login                (Status: 302) [Size: 0] [--> http://10.10.197.100/wp-login.php]
/page1                (Status: 301) [Size: 0] [--> http://10.10.197.100/]
/phpmyadmin           (Status: 403) [Size: 94]
/rdf                  (Status: 301) [Size: 0] [--> http://10.10.197.100/feed/rdf/]
/readme               (Status: 200) [Size: 64]
/render/https://www.google.com (Status: 301) [Size: 0] [--> http://10.10.197.100/render/https:/www.google.com]
/robots               (Status: 200) [Size: 41]
/robots.txt           (Status: 200) [Size: 41]
/rss2                 (Status: 301) [Size: 0] [--> http://10.10.197.100/feed/]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.197.100/feed/]
/sitemap              (Status: 200) [Size: 0]
/sitemap.xml          (Status: 200) [Size: 0]
/video                (Status: 301) [Size: 235] [--> http://10.10.197.100/video/]
/wp-admin             (Status: 301) [Size: 238] [--> http://10.10.197.100/wp-admin/]
/wp-content           (Status: 301) [Size: 240] [--> http://10.10.197.100/wp-content/]
/wp-cron              (Status: 200) [Size: 0]
/wp-config            (Status: 200) [Size: 0]
/wp-includes          (Status: 301) [Size: 241] [--> http://10.10.197.100/wp-includes/]
/wp-links-opml        (Status: 200) [Size: 227]
/wp-load              (Status: 200) [Size: 0]
/wp-login             (Status: 200) [Size: 2613]
/wp-settings          (Status: 500) [Size: 0]
/wp-mail              (Status: 500) [Size: 3064]
/wp-signup            (Status: 302) [Size: 0] [--> http://10.10.197.100/wp-login.php?action=register]
/xmlrpc               (Status: 405) [Size: 42]
/xmlrpc.php           (Status: 405) [Size: 42]
```
