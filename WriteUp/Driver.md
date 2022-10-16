# Hack The Box Driver
<img src="https://user-images.githubusercontent.com/67650329/149864630-ade215ee-ffb6-4485-a672-28d0f7161a19.png" width="385px" align="center">
This is my first pentest, here I will pentest HTB Driver. Why I use HTB (Hack the box) because my opinion HTB it’s the best platform to pentest and have so many machine to practice. So I'm having trouble with the exploit flag, but after I tried and searched many times, I finally found the exploit flag.


# Service and Web Enumeration
I used NMAP to scan the target.

Target IP  : 10.10.11.106

Machine IP : 10.10.14.37

<img src="https://user-images.githubusercontent.com/67650329/149865341-4be7b64b-f731-4ac6-8dfb-02e44511b59c.png" width="385px" align="center">

NMAP Command I use :

•	- sC : Performs a script scan using default scripts available in NMAP.

•	- sV : Performs version detection for the services.

NMAP scan found port and services :

•	Port 80 : Apache

•	Port 135 : Transmission Control Protocol

•	Port 445 : Direct TCP/IP MS Networking access

I search the IP and I got the login username and password

<img src="https://user-images.githubusercontent.com/67650329/149864780-b9853f5c-c57a-406b-a8fc-1d7c3d464a6d.png" width="385px" align="center">

In this target I try first use common password for admin:

Admin : admin

Admin : admin123

Admin : password

Admin : password123

Admin : qwert

Admin : qwert123

And I got the password for admin its admin.

<img src="https://user-images.githubusercontent.com/67650329/149864830-ab5fd0f8-63d6-4a59-bcdd-fb0d8e246332.png" width="385px" align="center">

And this it’s the website target 10.10.11.106.

<img src="https://user-images.githubusercontent.com/67650329/149864892-282eb0e6-057b-4e09-8860-f730c15a2cdc.png" width="385px" align="center">

I got the upload website, maybe I can input script in here.


# Authenticate With the Cracked Password
Make the script first

<img src="https://user-images.githubusercontent.com/67650329/149864931-31657837-315a-4a32-830d-b1ce84e09c58.png" width="385px" align="center">

<img src="https://user-images.githubusercontent.com/67650329/149864972-b526872e-b063-4c4d-83c5-9ad3ec0d2649.png" width="385px" align="center">

This it’s the script and save it @PoC.scf

And run the listener I use responder to listen the script.

<img src="https://user-images.githubusercontent.com/67650329/149865020-1a9ef447-081c-421c-a460-684b903cdac8.png" width="385px" align="center">

And I got the hash user tony.

<img src="https://user-images.githubusercontent.com/67650329/149865030-22a99d8a-3116-43bc-9516-3d6aada5fc3b.png" width="385px" align="center">

After I got the hash user tony, 

I must crack the hash use hashcat.

<img src="https://user-images.githubusercontent.com/67650329/149865044-42a98db9-bf65-430f-90e1-8fa28193e004.png" width="385px" align="center">

I got liltony maybe this it’s the password user tony.

Lets go access to target 10.10.11.106

I use evil-wirnm to access the target.

<img src="https://user-images.githubusercontent.com/67650329/149865063-7743a326-cd55-4e03-ab06-7d9beb35f84a.png" width="385px" align="center">

Now I am inside the machine target.

<img src="https://user-images.githubusercontent.com/67650329/149865081-3a048baf-689e-465b-909e-53113c912b51.png" width="385px" align="center">

I got the user.txt on C:\Users\tony\Desktop.


# Privilege Escalation
After I got user.txt now I search the flag.

I use reverseshell to execute the flag.

<img src="https://user-images.githubusercontent.com/67650329/149865113-b10c8655-8f5c-4011-9dca-7e37b0370e24.png" width="385px" align="center">

Download the exploit first from github:

https://raw.githubusercontent.com/cube0x0/CVE-2021-1675/main/CVE-2021-1675.py.

<img src="https://user-images.githubusercontent.com/67650329/149865132-5e6c0cb3-9845-492f-a917-5e0845302a00.png" width="385px" align="center">
 
Run smb for run reverseshell.

<img src="https://user-images.githubusercontent.com/67650329/149865163-99c66c52-bfc7-4aaf-845d-563ccb971d16.png" width="385px" align="center">

Run listerner.

<img src="https://user-images.githubusercontent.com/67650329/149865182-54f1e411-5cd3-474c-ba9c-91d21b8643f7.png" width="385px" align="center">

Run the exploit download on github.

<img src="https://user-images.githubusercontent.com/67650329/149865192-3318bba8-9989-437d-8eb1-81412291b1c3.png" width="385px" align="center">

And baam!

<img src="https://user-images.githubusercontent.com/67650329/149865214-f45255c3-25c1-48ee-b8d2-d3bec1632cc2.png" width="385px" align="center">

I am inside the target machine.

<img src="https://user-images.githubusercontent.com/67650329/149865222-6f6a03a1-1e45-4397-ada3-6550f4bab527.png" width="385px" align="center">

And I got the root or flag.txt

On C:\Users\Administrator\Desktop\root.txt.

---
