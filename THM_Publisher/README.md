<br>

<p align="center">
    <img src="./assets/Publisher_logo.png" width="15%">
</p>

<br>

# TryHackMe - Publisher (Easy)

💡 TryHackMe room : https://tryhackme.com/room/publisher

💻 OS : Kali Linux

## Table of contents 

- **[Setup & Tools](#setup-and-tools)**
- **[Write-up](#write-up)**
    - [1. Recon](#recon)
    - [2. Exploit](#exploit)
    - [3. PrivEsc](#privesc)

<br>

## Setup and Tools 

- nmap (port enumeration)
- ffuf (fuzzing directories)
- Linux knowledges (permissions, filesystem, shell...)
- AppArmor knowledges ([check the documentation](#privesc))

<br>

## Write-Up

**Target IP : 10.10.185.128**

**website discovered : http://10.10.185.128:80**
<br>

### 1. Recon

Firstly, we'll try to enumerate the open ports of the machine using nmap.

nmap command : `nmap -sCV -T4 -p 22,80 --min-rate 150 10.10.185.128`

**results :**
```yaml
Nmap scan report for 10.10.185.128
Host is up (0.15s latency).

PORT    STATE    SERVICE   VERSION
22/tcp open
ssh
OpenSSH 8.2p1 Ubuntu 4ubuntuo.13 (Ubuntu Linux; protocol 2.0)
ssh-hostkey:
3072 3d:32:ee: bc: 89:df:94:02:75:69:d9:ac: f6:81:55:7e (RSA)
256 85: cb:50:eb: 56:55:ae: c6: e4:b2:87:40:75:b6:4c:94 (ECDSA)
256 b5:55:54:96:8a:5b: fa: 42:5d:0c:1a:7b: cc: ff: ff:05 (ED25519)

80/tcp open
http
Apache httpd 2.4.41 ((Ubuntu))
I_http-title: Publisher's Pulse: SPIP Insights & Tips
I_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: 0S: Linux; CPE: cpe:/o:linux: linux_kernel
```

<br>

The homepage of the website found on `port 80`

<img src="./assets/Publisher_homepage.png">


<br>

With this website discovered, and a SSH port open, we can then fuzzing the website directories to find interesting informations.

ffuf command : `ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.185.128/FUZZ -recursion`

**results :**
```yaml
images                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 3080ms]
[INFO] Adding a new job to the queue: http://10.10.185.128/images/FUZZ
                        [Status: 200, Size: 8686, Words: 1334, Lines: 151, Duration: 2119ms]

spip                    [Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 165ms]
[INFO] Adding a new job to the queue: http://10.10.185.128/spip/FUZZ
                        [Status: 200, Size: 8686, Words: 1334, Lines: 151, Duration: 150ms]

server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 156ms]
```


<br>

So, we find the images directory, not really interesting... but the next one is ! "SPIP"

**/spip page :**

<img src="./assets/Publisher_spip-page.png">

<br>

### 2. Exploit

On the new webpage found, the CMS version of SPIP is written in source code : `spip 4.2.0`

The next step is to check if there's not a possible vulnerability and exploit for this configuration and... bingo ! 

**CVE-2023-27372** ([link to searchsploit](https://www.exploit-db.com/exploits/51536))

<br>

While searching about this CVE, I found a Github repo containing a python tool to facilitate this exploit utilisation

**[Python tools from nuts7](https://github.com/nuts7/CVE-2023-27372)**

Downloading the tool and testing it through a *test.txt* file to check the file inclusion.

<img src="./assets/Publisher_test-exploit.png">

<br>

**results:**

<img src="./assets/Publisher_test-result.png">

<br>
<br>

Well! the exploit is functioning, so now we could think to a more elaborate inclusion, like a webshell or reverse shell. While trying to implement a reverse shell, i did'nt got a clear so i had to test the webshell which is maybe more easy to include : 

<img src="./assets/Publisher_webshell-exploit.png">

<br>

**results:**

<img src="./assets/Publisher_webshell-result.png">

<br>

Ok now we're connected to the *www-data* user, we can try to enumerate interesting information, starting by the home's directories. The first and only one we found is **think**.
Listing he's home directory gives us the first user flag `user.txt`.

<img src="./assets/Publisher_think-home.png">

### 3. PrivEsc


