## Photobomb | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/photobomb)

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
