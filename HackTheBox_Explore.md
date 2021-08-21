This is my third HackTheBox machine. Unlike the first two boxes that I did, this one is an android machine. Let's give it a go!

I started off with an nmap scan and got this:
PORT      STATE    SERVICE VERSION
2222/tcp  open     ssh     (protocol 2.0)
5555/tcp  filtered freeciv
35923/tcp open     unknown
42135/tcp open     http    ES File Explorer Name Response httpd
59777/tcp open     http    Bukkit JSONAPI httpd for Minecraft game server 3.6.0 or older

Port 59777 looks interesting. I googled port 59777 exploit android and got this link: https://www.exploit-db.com/exploits/50070.
This exploit had lots of commands that could be used to get more information. I used getDeviceInfo to get more information about the device:
name : VMware Virtual Platform
ftpRoot : /sdcard
ftpPort : 3721

So it seems that files were stored in the /sdcard directory. However, I had no way to verify this. The listFiles commands would only list folders and not list their contents. I did some more googling and found this website: https://medium.com/@knownsec404team/analysis-of-es-file-explorer-security-vulnerability-cve-2019-6447-7f34407ed566. It described the vulnerability in more detail.

I saw this command:
curl --header "Content-Type: application/json" --request POST --data "{\"command\":\"listFiles\"}" http://127.0.0.1:59777
and figured I could use this command to list files that are also in directories. I added /sdcard to the end of the command and saw that user.txt was in that directory.

Finally, to get user.txt onto my machine, I ran:
sudo python3 exploit.py getFile 10.10.10.247 /sdcard/user.txt  
The files was saved as out.dat, and I was able to obtain the hash: f32017174c7c7e8f50c6da52891ae250

At this point I had no idea what to do. I was only familiar with hacking windows and linux servers; an android device was an entirely foreign concept to me. I'll come back to get this box's root flag once I get better at hacking overall.
