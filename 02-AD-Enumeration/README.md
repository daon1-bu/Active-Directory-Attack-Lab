Active Directory Enumeration
Objective

  The goal of this phase is to simulate the first stage of an internal attacker after gaining network access: discovering domain resources, users, and services.
  
  Enumeration is critical because attackers need to understand the environment before attempting privilege escalation or lateral movement.
  
  Attacker Machine
  
  All enumeration was performed from the Kali Linux attacker VM inside the lab network.
  
  Network Discovery
  
  First step was identifying live hosts inside the network.

Command used:

  nmap -sn 10.0.2.0/24
  
  This scan identifies active machines on the network without performing a port scan.
  
  Discovered hosts:
  
  Domain Controller
  
  Windows Client 1
  
  Windows Client 2
  
  Port Scanning
  
  After identifying live hosts, a full port scan was performed against the Domain Controller.
  
  nmap -sC -sV -p- <DC-IP>

Purpose:

  Identify running services
  
  Detect domain-related services
  
  Key services discovered:
  
  Port	Service
  53	DNS
  88	Kerberos
  135	RPC
  139	NetBIOS
  389	LDAP
  445	SMB
  3389	RDP
  
  These services confirm the target is an Active Directory Domain Controller.
  
  SMB Enumeration
  
  Next step was checking for accessible SMB shares.
  
  smbclient -L //<DC-IP> -N
  
  This attempts anonymous SMB access.

Result:

  SMB shares identified
  
  SMB confirmed enabled on the Domain Controller
  
  Domain Information Gathering
  
  Used built-in Windows commands from a domain-joined machine to gather information.
  
  Commands used:
  
  net user /domain
  
  Purpose:
  Enumerate domain users.
  
  net group "Domain Admins" /domain

Purpose:
  Identify privileged accounts.
  
  This step simulates an attacker who gained access to a low-privileged domain workstation.
  
  Key Findings
  
  During enumeration, the following information was discovered:
  
  Active Directory domain confirmed
  
  Multiple domain users identified
  
  SMB file sharing enabled
  
  Kerberos authentication present
  
  Domain services exposed internally
  
  This information prepares the attacker for credential attacks and lateral movement.
