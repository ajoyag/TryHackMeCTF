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
### Intial Scan
command: nmap 10.10.197.100
Findings

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
