📘 pfSense Installation Guide
1. Overview

This document describes the complete installation process for pfSense 2.7.2 inside VirtualBox, including:
Virtual machine creation
Network adapter configuration (WAN / LAN / OPT1)
pfSense disk partitioning
Initial setup and IP addressing
WebConfigurator access
pfSense serves as the core firewall of the SOC homelab, providing:
Routing
Network segmentation
DHCP
DNS
NAT
Logging
Security monitoring

2. Virtual Machine Configuration
VM Software: VirtualBox
ISO Used: pfSense-CE-2.7.2-RELEASE-amd64.iso

2.1 VM Hardware Specs
Component	Setting
Name	pfSense
OS Type	BSD → FreeBSD (64-bit)
Base Memory	1024–2048 MB
CPUs	1 CPU
Chipset	PIIX3
Paravirtualization	Legacy
Video Memory	16 MB
Graphics Controller	VBoxVGA
Storage	20 GB VDI
Audio	Disabled (optional)
USB	OHCI/EHCI

2.2 VirtualBox Network Adapters
pfSense requires at least two NICs.

Adapter	Type	Purpose
Adapter 1	NAT	WAN Internet
Adapter 2	Host-Only Network (SOC_LAN)	LAN for internal VMs
Adapter 3 (optional)	Host-Only Network (HONEYPOT_DMZ)	DMZ / Honeypot VLAN
SOC Lab Network Design
INTERNET (NAT)
      ↓
  [ pfSense ]
      ↓
───────────────────────────────
 LAN (192.168.56.0/24)
   → Windows Endpoint
   → Wazuh Server
   → Kali Linux
      ↓
 OPT1 / DMZ (192.168.20.0/24)
   → Honeypot (Cowrie / Dionaea)

3. pfSense Installation

3.1 Start Installer
Boot VM using ISO
Select: Install pfSense

3.2 Disk Partitioning
Choose:
Auto (ZFS)
  → Guided Root-on-ZFS
Partition Scheme:
GPT (recommended)
Partitions created:
freebsd-boot
freebsd-ufs
freebsd-swap
Select Finish → Commit.

3.3 Complete Installation
Allow files to extract
Reboot system
Remove the ISO when prompted
pfSense loads into the console menu

4. Initial pfSense Console Configuration
When pfSense boots, interfaces appear:

WAN  = em0
LAN  = em1
OPT1 = em2   (optional)

4.1 Assign Interfaces
Console → Option 1
Typical mapping:
em0 → WAN (NAT)
em1 → LAN (SOC_LAN)
em2 → OPT1 (HONEYPOT_DMZ)

4.2 Configure LAN IP
Console → Option 2
LAN IPv4 Address: 192.168.56.1
Subnet: /24
Enable DHCP: Yes
DHCP Range: 192.168.56.100 – 192.168.56.200
IPv6: Disabled

This provides LAN connectivity to:
Windows endpoint
Wazuh server
Kali Linux

4.3 Configure OPT1 (DMZ / Honeypot) (Optional)
OPT1 IPv4 Address: 192.168.20.1
Subnet: /24
Enable DHCP: Yes
DHCP Range: 192.168.20.100 – 192.168.20.200
IPv6: Disabled
This network isolates the honeypot from the main LAN.

5. Accessing the WebConfigurator
pfSense console displays the LAN address:
https://192.168.56.1/

Steps:
Open a browser
Enter: https://192.168.56.1/
Accept certificate warning

Login:

Username: admin
Password: pfsense

6. Post-Installation Next Steps

(To be documented in later sections)

Create firewall rules
Configure DNS
Enable NTP
Configure outbound NAT
Enable logging to Splunk/Wazuh
Create VLANs
DMZ / Honeypot isolation rules

7. Completion
pfSense base installation is complete and ready for integration with:
- Wazuh XDR
- Splunk SIEM
- Windows endpoint
- Kali attacker VM
- Cowrie honeypot

Next documentation sections:
- Interfaces
- DHCP settings
- NAT
- Firewall rules
- Network topology diagram
- Honeypot DMZ segmentation
