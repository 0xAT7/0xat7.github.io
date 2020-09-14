---
title: ASCWG Final Round Machine Writeup
published: true
---

> ![logo](https://www.ascyberwargames.com/wp-content/uploads/2020/01/MainLogo.png)

* Before we start, I will try to explain everything as a player since I'm the creator of this machine. 
# []() Methodology

* Nmap scan.
* find wordpress.
* find out of date plugin.
* exploiting CVE-2019-9978 and got reverse shell.
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

**Checking the Web-Page on 8078**

* Nothing is here.

![](https://i.ibb.co/X8y46Tv/3.png)

* Doing some fuzzing with dirb and I found **Wordpress**.

![](https://i.ibb.co/bbPFXSC/4.png)

* Running wpscan and I found out of date plugin, which is vulnerable.

![](https://i.ibb.co/xXXKb94/5.png)

# []() CVE-2019-9978 RCE in Social WarFare < 3.5.3.

![](https://i.ibb.co/t8VYstH/6.png)

* I downloaded the exploit from github and exploited it by adding the following payload in my apache server.

![](https://i.ibb.co/10N7WX7/7.png)

* Running nc as listener and I got a reverse shell.

![reverse-shell](https://i.ibb.co/88k9wGt/8.png)

* Listing the **wordpress** directory and found strange file **config.php**

* I know that most of wordpress files start with **wp-**.

* Reading this file and I found a path in the last line of it **unknown_path**.

* I found that I can **cd** into **johny** user directory but I can't list anything.

![](https://i.ibb.co/10bL7Pk/9.png)

# []() Johny user

* I tried **cd /home/johny/unknown_path/** and worked for me!.

* Listing it with **ls -la** and I found **.ssh** directory which have ssh public and private key.

![](https://i.ibb.co/7bSrBWd/10.png)

> Copying the **id_rsa** and I cracked it on my machine and got **johny** user.

![](https://i.ibb.co/Y0VfczG/11.png)

# []() Root flag

> Listing its directory and I found an exploitable program which is basically doing **ls /root/root.txt**

* Doing path hijacking and I got the root flag.
![](https://i.ibb.co/K2sfxXk/12.png)


> * Explaining **Path Hijacking**:

* We want to just replace **cat** command with **ls** command.

* In Linux when you enter a command the system goes to the path env such as **/bin** to search about the command you entered if its exist or not to execute or run it.

* Doing **export PATH=/tmp:$PATH** made the **/tmp** as the first path env, so when the system search about **ls**

* It will find it inside **/tmp** which is by the way a **cd** command. 

* Thanks for reading hope you enjoyed this.


