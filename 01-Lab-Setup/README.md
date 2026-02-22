Lab Setup
Objective

Build a realistic enterprise Active Directory environment using modern Windows systems to simulate real-world internal network attacks.

This lab mirrors a small corporate domain with centralized authentication and multiple domain-joined workstations.

Lab Architecture

Machines:

Domain Controller — Windows Server 2025 Enterprise

Client 1 — Windows 11 Enterprise

Client 2 — Windows 11 Enterprise

Attacker Machine — Kali Linux

Domain Name: MARVEL.local
Virtualization: VirtualBox
Lab Disk Allocation: 300 GB

Host Machine Requirements

Recommended host specs to run the lab smoothly:

16–32 GB RAM

6+ CPU cores

300 GB free disk space

VirtualBox installed

This lab is resource-heavy because multiple enterprise Windows machines run simultaneously.

Virtual Machine Configuration
Domain Controller — Windows Server 2025

6 GB RAM

2–4 CPUs

80–100 GB Disk

Static IP configured

DNS configured locally

Windows 11 Client 1

4 GB RAM

2 CPUs

60 GB Disk

Windows 11 Client 2

4 GB RAM

2 CPUs

60 GB Disk

Kali Linux Attacker Machine

4 GB RAM

2 CPUs

40 GB Disk

Domain Controller Installation

Steps performed:

Installed Windows Server 2025 Enterprise.

Set a static IP address.

Installed Active Directory Domain Services (AD DS) role.

Promoted server to Domain Controller.

Created new forest:

MARVEL.local

DNS configured automatically during promotion.

Domain Configuration

Created multiple domain users to simulate a corporate environment:

peterparker

tony.stark

bruce.banner

administrator

Configured basic permissions and enabled file sharing to simulate internal resources.

Client Domain Join

For both Windows 11 clients:

Set DNS to Domain Controller IP address.

Joined domain MARVEL.local.

Logged in using domain credentials.

Verified authentication and connectivity.

Network Validation

Confirmed:

Clients successfully joined the domain

Domain users can log in on workstations

Machines communicate within the lab network

SMB and authentication services working correctly
