# Active Directory Attack Lab

This repository documents a structured, hands-on Active Directory lab designed to demonstrate common attack techniques against a Windows domain environment and the corresponding security implications.

The lab follows a realistic attack progression from initial network misconfiguration abuse to credential capture, lateral movement, and remote command execution. Each phase is documented in a dedicated section to clearly illustrate attacker behavior and its impact on a domain environment.

This project is intended for educational purposes and to demonstrate understanding of Active Directory attack paths and security monitoring considerations.

---

## Lab Environment

- Windows Server (Domain Controller)
- Windows Client Machine(s)
- Kali Linux Attacker Machine
- Active Directory Domain Environment
- Virtualized Lab Setup

---

## Lab Structure

The lab is organized into the following phases:

### 01 – Lab Setup
Initial environment configuration, including domain setup, user accounts, networking, and required services.

### 02 – Active Directory Enumeration
Enumeration of domain information to identify users, systems, and potential attack paths.

### 03 – LLMNR Poisoning
Abuse of Link-Local Multicast Name Resolution (LLMNR) to capture authentication material from the network.

### 04 – Gaining Shell Access
Use of captured credentials to obtain remote command execution and interactive access within the domain.

### SMB Relay Attack
Demonstration of SMB relay techniques to authenticate to remote systems without cracking credentials.

---

## Key Concepts Demonstrated

- Active Directory enumeration techniques
- Insecure name resolution protocols
- Credential capture and reuse
- Lateral movement within a domain
- Remote service execution over SMB
- Attack chain progression in enterprise environments

---

## Security Considerations

Each phase of the lab highlights common security weaknesses that are frequently abused in real-world attacks. These include:

- Weak or misconfigured name resolution protocols
- Excessive credential exposure
- Insufficient logging and monitoring
- Over-privileged accounts
- Insecure lateral movement paths

Understanding these weaknesses is critical for improving detection, monitoring, and defensive controls in enterprise environments.

---

## Disclaimer

This project is for educational and defensive awareness purposes only.  
All testing was performed in a controlled lab environment owned and operated by the author.

No techniques demonstrated here should be used against systems without explicit authorization.
