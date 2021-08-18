This is my second box I will be attempt to pwn on HackTheBox. Let's get into it!

I did an nmap scan of the box's ip. The results showed that ports 22 and 80 were open. Specifically, port 80 was being used for an Apache web server. This is probably te best lead to go on.
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))

I navigated to the web server and was greeted by an apparent medical site for a company called EMA. The web site was very bare bones. It contained the company's unfinished motto, "At EMA we're taking care to a whole new level . . .
Taking care of our " and had unclickable navigation links at the top right. In fact, nothing on the web server was interactable.

I was at a lost at what to do, so I did some googling and learned about gobuster. It's a package that attempts directory traversal through a wordlist. The exact command I used was: gobuster dir -u 10.10.10.242 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x txt
I found that /server-status was a valid directory, but I didn't have permissions to access it. Directory enumeration ended up to be a dead end.

Next, I decided to take a look at the web server using inspect element. By looking at the header of 10.10.10.242 in the network tab, I was able to see that the website was being powered by PHP/8.1.0-dev.
I searched for exploits regarding that PHP version and came across this: https://www.exploit-db.com/exploits/49933. Pretty sick find.
