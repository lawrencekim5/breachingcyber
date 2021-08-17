This is my first time trying a HackTheBox machine. I will be going into this blind, let's see how I do.

First thing I did was an nmap scan of the machine's IP. Reconnaissance is always the first step; know your enemy or something like that. Identifying open ports on a machine provides possible attack vectors.
I used the command "nmap -sS -sV -p- -T5 10.10.10.245
-sS to do a TCP SYN scan
-sV to get version info
-p- to scan all ports
-T5 to make the scan faster because I'm impatient

This is the result of the scan:
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    gunicorn

Ports for FTP, SSH, and HTTP are open. Trying to gain access through FTP and SSH didn't work, so I decided to take a look at the gunicorn web server.
The HTTP web server looked to be doing some sort of network logging. It had a dashboard that graphically visualized failed login attepts, port scans, and what not. 
There is a tab that says "Security Snapshot (5 Second PCAP + Analysis). In that tab, there is a download button that lets you download the PCAP file.

After analyzing the PCAP file in Wireshark, I still wasn't able to find anything that useful. I must have missed something, right?

I went back to the web server PCAP page and saw that the number of packets shown on that page changed. I noticed that the URL showed a different number each time I navigated to the page (http://10.10.10.245/data/4 to http://10.10.10.245/data/3), and the displayed count of the number of packets would change as well. I assumed that since the tab was titled "5 Second PCAP," each number in the URL corresponded to a five second timeframe in which packets were logged. I headed over to http://10.10.10.245/data/1 and saw that 0 packets were collected in that time frame. Then, I tried http://10.10.10.245/data/0 thinking that the number 0 would indicated an aggregation of all the logged packets. The count of the number of packets displayed as 72, the highest of which I had ever seen on this web server. I guess it worked.

I opened the file in Wireshark, navigated to the credentials tool, and saw that a user named "nathan" accessed port 69 and that their password was "Buck3tH4TF0RM3!". This port is usually used for TFTP, but showed up in Wireshark as just FTP. I'm not sure why that was, but it seemed like enough of a lead to go on.

I used Nathan's credentials and logged into the FTP server. After using the command "ls," I saw that there was a file on the FTP server called "user.txt." I used a "get" command to transfer the file to my Kali machine.

After opening user.txt, I saw the string, "35cda8e779ac73eab2bc55cd5933273f". I had no idea of what this was, but is sure looked important.
Since there wasn't much else on the FTP server, I sshed into box using Nathan's credentials.

getcap -r / 2>/dev/null

