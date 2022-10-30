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

We can Inject this login page use `admin'||''==='`

Now we get in to the admin dashboard.

<img src="https://user-images.githubusercontent.com/67650329/198883833-1de0fe9d-4ea2-436d-ab9d-ef63f9a7bc51.png" width="500px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198883855-02c0e9bd-28be-452f-833d-9187312a6d14.png" width="400px" align="center">

We can use `admin'||''==='` again.

<img src="https://user-images.githubusercontent.com/67650329/198883908-6c8a1fea-5627-4167-8736-fead09b3ccfd.png" width="400px" align="center">

Oh wow, We got the username and hash password.

Now we must crack the hash password use hashcat.

<img src="https://user-images.githubusercontent.com/67650329/198884026-582f033b-19a3-4d28-ab97-f166a36d6807.png" width="500px" align="center">

Now we got the password of user admin.

We can enumeration the web /login/ use gobuster.

`gobuster vhost -u http://shoppy.htb/login -w /home/kali/Downloads/SecLists/Discovery/DNS/namelist.txt -t 500`

<img src="https://user-images.githubusercontent.com/67650329/198884180-e6204b93-7529-4420-85ec-411916d4d510.png" width="400px" align="center">

We got another web page, `mattermost.shoppy.htb` 

<img src="https://user-images.githubusercontent.com/67650329/198884233-0832c12d-edd9-4705-b287-1421420e4b68.png" width="350px" align="center">

Use the username and password before.

<img src="https://user-images.githubusercontent.com/67650329/198884342-9b46ff5b-95be-4222-9a9d-e98cc0b1a2b2.png" width="400px" align="center">

I think this it's the ssh username and password.

<img src="https://user-images.githubusercontent.com/67650329/198884358-ada8068b-b32f-4fed-9146-9caa9c252edb.png" width="500px" align="center">

We get in to the ssh.

<img src="https://user-images.githubusercontent.com/67650329/198884456-0fc7a074-737d-42eb-a623-cae2f6026b7c.png" width="250px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198884486-8dd81522-41eb-4262-bd1c-6ba4d2b357a6.png" width="500px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198884523-36bc0329-5f85-45c6-b1ae-1a4d6ffe66d3.png" width="500px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198884700-f82589cb-ad57-40fe-991b-395c40caf1ce.png" width="400px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198884556-3481f60d-c4a6-4431-bae3-37fc98476029.png" width="500px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/198884578-34f0fff1-193c-434f-bfaa-86eca68b4cc7.png" width="100px" align="center">

You can search on [gtfobins.github.io](https://gtfobins.github.io/)

<img src="https://user-images.githubusercontent.com/67650329/198884799-e2e7db09-a60b-477d-8e90-ee8def76ecb7.png" width="350px" align="center">

---
