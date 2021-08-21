Here's another HackTheBox box. Let's jump in.

Let's begin by enumerating. Enumerate, enumerate, enumerate.
Classic nmap scan to start the attack.
PORT      STATE    SERVICE  VERSION
22/tcp    open     ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http     Apache httpd 2.4.41 ((Ubuntu))
8554/tcp  filtered rtsp-alt
15484/tcp filtered unknown
22892/tcp filtered unknown
24853/tcp filtered unknown
32586/tcp filtered unknown
33926/tcp filtered unknown
38270/tcp filtered unknown
42692/tcp filtered unknown
52269/tcp filtered unknown
58223/tcp filtered unknown
65003/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Lots of unknown services. The open Apache server is interesting though.
The web page is a site for the company, BountyHunters. There is a contact section where you can email the team, and there is a bounty report portal. A preliminary guess of mine is that there will be some form of injection attacks for this box.

I ran dirsearch -u 10.10.11.100 to start seeing if there are any open directories on the web server that can be exploited. 10.10.11.100/resources worked. It led to an index of files, including a README.txt, which containted:
Tasks:

[ ] Disable 'test' account on portal and switch to hashed password. Disable nopass.
[X] Write tracker submit script
[ ] Connect tracker submit script to the database
[X] Fix developer group permissions

Very interesting. It looks like there is a test account on the portal that can be exploited. However, since the tracker submit script is not connected to the database, it may be impossible to do SQL injection.

Viewing the page source revealed the the Apache server version is: Apache/2.4.41 (Ubuntu)
 
