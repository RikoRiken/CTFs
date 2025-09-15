<br>

<p align="center">
    <img src="./assets/DarkMatter_logo.png" width="15%">
</p>

<br>

# TryHackMe - Dark Matter (Easy)

💡 TryHackMe room : https://tryhackme.com/room/darkmatter

💻 OS : Ubuntu

## Table of contents 

- **[Setup & Tools](#setup-and-tools)**
- **[Write-up](#write-up)**

<br>

## Setup and Tools 

- Ubuntu machine
- RSA decoder
- Encryption knowledges

## Write-Up

### 1. Discover the infected machine

To begin, we're welcomed with a popup asking us to send 0.5 bitcoins to get our files decrypted 🤡

<img src="./assets/DarkMatter_Ransomware-note.png">

<br>

Effectively, we found to `.docx` documents on the desktop that are encrypted :

<img src="./assets/DarkMatter_Encrypted-docs.png" width="67%">

<br>

### 2. Find the key and decrypt our files

We got a quick hint in the description of the challenge, that there is interesting things in the `/tmp` folder. Let's see what's inside : 

```
cd /tmp                 (we find public_key.txt in it)

cat public_key.txt
```

<img src="./assets/DarkMatter_RSA-public-key.png" width="67%">

