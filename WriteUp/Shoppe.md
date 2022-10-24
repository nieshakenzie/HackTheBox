## Shoppe | HackTheBox

You can access machine [here](https://app.hackthebox.com/machines/Shoppy)

<img src="https://user-images.githubusercontent.com/67650329/197530588-13e69573-b288-4ad0-82ab-ac8d0ad1f0ed.png" width="500px" align="center">

---
Let's do this.

First, must access the website first.

<img src="https://user-images.githubusercontent.com/67650329/197530861-7ccdc53a-0625-49d5-9c99-3056ac65409e.png" width="500px" align="center">

We can scan the network use NMAP.

`nmap -sC -sV -p- -T4 {IP}`

-sC = Run the default script.

-sV = Version of services.

-p- = Checking for all port.

-T4 = Scanning speed.

This is the result for the scanning with nmap tools.

<img src="https://user-images.githubusercontent.com/67650329/197530992-617478dd-5710-4bc9-92ec-5a711e3d4c01.png" width="500px" align="center">

Port open:

- 22 = SSH.
- 80 = HTTP.
- 9093 = copycat?.

Hmmm, We can search another page with GOBUSTER.

`gobuster dir -u [TARGET_IP] -w [WORDLIST] -f -n`

- u = Url/Link Target.
- w = Wordlist to use.
- f = Nospam.
- n = No Status just result (Nospam).

This is the result for the scanning with gobuster tools.

<img src="https://user-images.githubusercontent.com/67650329/197532136-58ccbefd-1d30-42e0-b006-f6fc327f54a7.png" width="500px" align="center">

We got the /login/ page.

<img src="https://user-images.githubusercontent.com/67650329/197532349-cee2cede-102a-4648-ba19-47a470063be7.png" width="250px" align="center">

