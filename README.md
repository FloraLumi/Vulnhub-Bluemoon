# Vulnhub Bluemoon

**References:**
* https://medium.com/@amandogra4867/bluemoon-2021-a-deep-walkthrough-from-zero-to-root-8d72c033b5b9 
* https://blog.razrsec.uk/vulnhub-bluemoon-walkthrough/ 

## Start the Machine
Recognize your own IP!! This is important considering you do not want to ended up hack yourself instead !!

**Attacker IP :** 192.168.100.78

## Reconnaisance

Nmap -sn ping scan : Tell nmap to skip port probing and only perform host discovery

**Target IP :** 192.168.100.77

## Scanning - Nmap
Go to their website

Usually, we would check their source code but it doesn’t reveal anything, so we need to brute-force some directories:

## Scanning – Gobuster
```bash
gobuster dir -u <target> -w /usr/share/wordlists/dirb/common.txt
```

Command : 
```bash
gobuster dir -u http://<target> \ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

hidden_text discovered, so go to the same link as previously then add the hidden_txt

After u press “Thank You…” it revealed a qr code
You decided to paste it into qr decoder

```bash
#!/bin/bash
HOST=ip
USER=userftp
PASSWORD=ftpp@ssword
ftp -inv $HOST user $USER $PASSWORD
bye
EOF
```

Successfully connected into ftp
Then listed files with `ls`
Download the listed files, then exit from the ftp
Read files contents

Then you should discoveredUsername:robin

Run msfconsole
Port 22 was open, so we used Metasploit’s SSH login module
Then, configure options.
Logging in as robin

Capture the 1st flag

Explore the files more to discover more secrets

Running `sudo -l` (list) displays the exact commands the current user is permitted to execute with root privileges.

Run the feedback.sh as jerry

Capture the 2nd flag in home directory of jerry
Now you are operating as jerry
This is called horizontal privilege escalation (useranother user)

Use `id` to identify jerry’s identity

Based on the docker access we can exploit it using command from GTFOBins website that gives us the information that will allow us to mount the root directory within a docker container and spawn a root shell

`#` = acquired root access

## What happened?
If a user can run Docker as root (or is in the docker group), it’s basically root access.

**Why?**
* Docker can mount the host filesystem using `-v /:/mnt`
* meaning: attach the host’s `/` directory inside the container
* Then `chroot /mnt` changes your root directory to the mounted host filesystem
* Now you are effectively inside the host system’s root filesystem as root

## Final Attack Chain Summary
Here’s the full flow in one clean chain:
* Discover IP using ARP scanning (netdiscover)
* Scan ports (nmap) → find HTTP + FTP + SSH
* Website looks empty → directory brute force (gobuster)
* Find hidden directory → QR code clue
* Use FTP → download sensitive files → get robin + password list
* Use SSH brute module → login as robin
* Find feedback.sh → sudo -l reveals running as jerry
* Escalate to jerry → grab second flag
* Docker misconfig → mount host filesystem → chroot → root shell
