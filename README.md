<p align="center">
    •<a href="#enumeration">Enumeration</a>•
    <a href="#ccess">Access</a>•
    <a href="#privilege escalation">Privilege Escalation</a>•
</p><br>

# Inferno-Writeup-Tryhackme
Writeup of the <a href="https://tryhackme.com/room/inferno" target="_blank">Inferno</a>(made by @mindsflee) machine on <a href="https://tryhackme.com" targer="_blank">TryHackMe</a>


# Enumeration
Running a rustscan on the targer gives the following result:
<br><strong>rustscan -a 10.10.121.130 -- -A -sC -sV -oN scan.txt</strong>

```
# Nmap 7.91 scan initiated Sun Feb 14 17:55:24 2021 as: nmap -vvv -p 21,22,23,25,80,88,106,110,636,750,775,777,779,783,808,873,1001,1178,1210,1236,194,1300,1314,1313,1529,2000,2003,2121,2150,2601,2602,2600,2604,2603,2605,2606,2607,2608,2988,2989,4224,4557,4559,4600,5051,5052,5151,5354,5355,5432,5555,5667,5666,5674,5675,5680,4949,6346,6514,6566,6667,8021,8081,8088,8990,9098,9359,9418,9673,10081,10082,10083,11201,15345,17001,17002,17003,17004,20011,20012,24554,27374,30865,57000,60177,60179 -A -sC -sV -oN scan.txt 10.10.121.130
Nmap scan report for 10.10.121.130
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
<p align="center"><img src="webimg.png" height="700" width="800"></p>
Just lines from Dante's Inferno(canto XXXIV) and the 9 circles of Hell.<br>
Ok, moving on...<br>
Running a gobuster scan on the site gives the following result:<br>

===============================================================<br>
Gobuster v3.0.1<br>
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)<br>
===============================================================<br>
[+] Url:            http://10.10.121.130/<br>
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



