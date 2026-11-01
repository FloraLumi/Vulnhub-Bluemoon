# Vulnhub Bluemoon

**References:**
* https://medium.com/@amandogra4867/bluemoon-2021-a-deep-walkthrough-from-zero-to-root-8d72c033b5b9 
* https://blog.razrsec.uk/vulnhub-bluemoon-walkthrough/ 

## Start the Machine
<img width="940" height="697" alt="image" src="https://github.com/user-attachments/assets/4a4cfd4e-67e9-4bd2-9773-a7d1e56af1a3" />

Recognize your own IP!! This is important considering you do not want to ended up hack yourself instead !!
<img width="940" height="514" alt="image" src="https://github.com/user-attachments/assets/15046377-c986-4ba2-b9e9-74bff5b6ff2d" />

**Attacker IP :** 192.168.100.78

## Reconnaisance

Nmap -sn ping scan : Tell nmap to skip port probing and only perform host discovery
<img width="850" height="78" alt="image" src="https://github.com/user-attachments/assets/8356abf6-46a0-40ef-bb40-13a2782b0c92" />
<img width="808" height="125" alt="image" src="https://github.com/user-attachments/assets/f06d365a-691a-44b0-b699-4d07dc45cfdf" />

**Target IP :** 192.168.100.77

## Scanning - Nmap
<img width="867" height="341" alt="image" src="https://github.com/user-attachments/assets/effd4ea3-1240-4c1e-85ee-e3529963dcc5" />

Go to their website since we discovered http state is opened
<img width="940" height="485" alt="image" src="https://github.com/user-attachments/assets/df417eff-9e59-4f52-87b6-f8c2a54ffbc1" />

Usually, we would check their source code but it doesn’t reveal anything, so we need to brute-force some directories:
<img width="940" height="308" alt="image" src="https://github.com/user-attachments/assets/a1325ae6-7f6b-49cf-a687-140fd8f2bcfa" />

## Scanning – Gobuster
```bash
gobuster dir -u <target> -w /usr/share/wordlists/dirb/common.txt
```
<img width="940" height="636" alt="image" src="https://github.com/user-attachments/assets/37a3589d-4ffa-477e-92a0-8c11a61e9c82" />

As we can see only index.html is in status 200 which means ok meanwhile other directory is in status 403 which means forbidden, we do not need the index.html since it's the website that we opened earlier.
So we need to brute force directory with a larger wordlists.
Command : 
```bash
gobuster dir -u http://<target> \ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
<img width="940" height="491" alt="image" src="https://github.com/user-attachments/assets/92ae5286-3b73-4ef6-9b5b-2a76430d2ad3" />

hidden_text discovered, so go to the same link as previously then add the hidden_txt
<img width="403" height="69" alt="image" src="https://github.com/user-attachments/assets/a43cbfa7-1ab3-41b0-be72-12df59de525c" />

<img width="940" height="231" alt="image" src="https://github.com/user-attachments/assets/0b98b222-32f0-4b38-b7c7-b15aaa430eac" />

After u press “Thank You…” it revealed a qr code
<img width="563" height="455" alt="image" src="https://github.com/user-attachments/assets/8cd9eec3-d51e-4698-a24a-fdcb9faedd5f" />

You decided to paste it into qr decoder
<img width="940" height="333" alt="image" src="https://github.com/user-attachments/assets/4c8694a0-3848-4045-bf42-8c1a82207760" />

```bash
#!/bin/bash
HOST=ip
USER=userftp
PASSWORD=ftpp@ssword
ftp -inv $HOST user $USER $PASSWORD
bye
EOF
```
As you can see we acquired the user and password for the ftp
which is 

USER = userftp
PASSWORD = ftpp@ssword

<img width="486" height="313" alt="image" src="https://github.com/user-attachments/assets/c2b9f797-23b6-4166-9266-b2be59e9879a" />

Successfully connected into ftp
Then listed files with `ls`
<img width="908" height="158" alt="image" src="https://github.com/user-attachments/assets/1ced3cda-229e-4d4d-8974-59bc5ff68395" />

Download the listed files, then exit from the ftp
<img width="940" height="225" alt="image" src="https://github.com/user-attachments/assets/e552ce86-b3a2-4ced-8fa9-67b4619be7ab" />

Read files contents
<img width="940" height="116" alt="image" src="https://github.com/user-attachments/assets/aadb94ad-064f-4b27-a3a7-db64e5bf8891" />

Then you should discovered Username:robin

Run msfconsole
<img width="878" height="125" alt="image" src="https://github.com/user-attachments/assets/8ecc5034-0064-48b6-a542-c5416f5a9048" />

Port 22 was open, so we used Metasploit’s SSH login module

"Early nmap scan"
<img width="141" height="19" alt="image" src="https://github.com/user-attachments/assets/c3c9f10a-1bb3-435d-8a4c-275a63459447" />

<img width="940" height="209" alt="image" src="https://github.com/user-attachments/assets/94e5fe89-645b-4ba7-94e1-7ad94286b661" />

Then, configure options.
using set RHOSTS, set PASS_FILE, set USERNAME - this enabling us to testing all the user/pass combinations to figure out which password is correct.
<img width="940" height="257" alt="image" src="https://github.com/user-attachments/assets/5bb1db0a-33cb-4512-9a25-f73013aa6013" />

Logging in as robin
<img width="940" height="414" alt="image" src="https://github.com/user-attachments/assets/689db421-4b60-4e31-916d-ee9380eb5000" />

Capture the 1st flag
<img width="586" height="184" alt="image" src="https://github.com/user-attachments/assets/418d207a-dd3c-4a46-9f66-4904c736df7c" />

Explore the files more to discover more secrets
<img width="856" height="361" alt="image" src="https://github.com/user-attachments/assets/4ba09ffe-6d97-4118-bcb1-90d6e71fb7e3" />

Running `sudo -l` (list) displays the exact commands the current user is permitted to execute with root privileges.
<img width="940" height="60" alt="image" src="https://github.com/user-attachments/assets/e76b8a6b-ec9d-400e-a121-38973d632e4d" />

Run the feedback.sh as jerry
<img width="691" height="34" alt="image" src="https://github.com/user-attachments/assets/47b0fc26-8018-466c-8845-d04c4b76501a" />

Capture the 2nd flag in home directory of jerry
Now you are operating as jerry
This is called horizontal privilege escalation (user --> another user)
<img width="673" height="541" alt="image" src="https://github.com/user-attachments/assets/12390c93-715c-4bc1-a371-745e58db7856" />

Use `id` to identify jerry’s identity
<img width="844" height="97" alt="image" src="https://github.com/user-attachments/assets/13ad2c24-08f4-4d51-8dcc-2a0821c2fc30" />

Based on the docker access we can exploit it using command from GTFOBins https://gtfobins.org/gtfobins/docker/?ref=blog.razrsec.uk website that gives us the information that will allow us to mount the root directory within a docker container and spawn a root shell

<img width="940" height="144" alt="image" src="https://github.com/user-attachments/assets/579d9a8f-ac72-4c1d-9863-03d4f5641242" />
<img width="688" height="84" alt="image" src="https://github.com/user-attachments/assets/027d2a0a-c0dd-4e24-95d7-f0b4015ba926" />

`#` = acquired root access
<img width="940" height="487" alt="image" src="https://github.com/user-attachments/assets/e1d85be8-49f6-4514-9e95-08b8c31560c7" />

## What happened?
If a user can run Docker as root (or is in the docker group), it’s basically root access.

**Why?**
* Docker can mount the host filesystem using `-v /:/mnt`
* meaning: attach the host’s `/` directory inside the container
* Then `chroot /mnt` changes your root directory to the mounted host filesystem
* Now you are effectively inside the host system’s root filesystem as root

## Final Attack Chain Summary
Here’s the full flow in one clean chain:
* Discover IP using nmap -sn
* Scan ports (nmap) → find HTTP + FTP + SSH
* Website looks empty → directory brute force (gobuster)
* Find hidden directory → QR code clue
* Use FTP → download sensitive files → get robin + password list
* Use SSH brute module → login as robin
* Find feedback.sh → sudo -l reveals running as jerry
* Escalate to jerry → grab second flag
* Docker misconfig → mount host filesystem → chroot → root shell
