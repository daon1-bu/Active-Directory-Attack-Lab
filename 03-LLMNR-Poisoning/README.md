LLMNR Poisoning Attack
Objective

Exploit Link-Local Multicast Name Resolution (LLMNR) to capture NTLMv2 authentication hashes from domain users inside the network.

LLMNR is enabled by default in many Windows environments and can be abused when DNS resolution fails.

What is LLMNR?

When a Windows machine cannot resolve a hostname using DNS, it broadcasts a request on the local network using LLMNR.

An attacker on the same network can respond to that request and trick the victim into sending authentication credentials.

Tool Used

Responder (Kali Linux)

Step 1 – Start Responder

  On the Kali attacker machine:
  
  sudo responder -I eth0
  
  Purpose:
  
  Listen for LLMNR/NBT-NS broadcast traffic
  
  Respond maliciously
  
  Capture NTLMv2 hashes

Step 2 – Trigger Authentication

  From a domain-joined Windows client:
  
  Attempted to access a non-existent share:
  
  \\fake-share
  
  This forces Windows to broadcast a name resolution request.
  
  Responder replies to the broadcast, pretending to be the requested host.

Step 3 – Hash Capture

  Responder captured NTLMv2 hash:
  
  Example output:
  
  [SMB] NTLMv2-SSP Hash captured from MARVEL\peterparker
  
  The captured hash was saved for offline cracking.
  
  Why This Works
  
  LLMNR allows unauthenticated local name resolution.
  
  When an attacker replies first to a broadcast request, Windows automatically attempts to authenticate to the attacker’s machine.
  
  This results in NTLM challenge-response hashes being exposed.
  
  Impact
  
  If the password is weak:
  
  Hash can be cracked offline
  
  Attacker gains valid domain credentials
  
  Enables lateral movement
  
  Potential privilege escalation
  
  Defensive Mitigation
  
  To prevent this attack:
  
  Disable LLMNR via Group Policy
  
  Disable NBT-NS
  
  Enforce SMB signing
  
  Use strong password policies
  
  Outcome
  
  Successfully captured NTLMv2 hash from a domain user in the MARVEL.local environment.

Next step: crack the captured hash using Hashcat.
