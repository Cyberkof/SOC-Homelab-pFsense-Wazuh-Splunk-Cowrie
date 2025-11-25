1. Overview

This document describes the full installation process for pfSense 2.7.2 inside VirtualBox, including:

Virtual machine creation

Network adapter configuration (WAN/LAN/OPT1)

pfSense disk partitioning

Initial setup and IP addressing

WebConfigurator access

This firewall acts as the core of the SOC lab, providing routing, segmentation, DHCP, DNS, NAT, and security monitoring.

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

pfSense requires at least 2 NICs:

Adapter	Type	Purpose
Adapter 1	NAT	Provides WAN Internet
Adapter 2	Host-Only Network (SOC_LAN)	Provides LAN access to internal VMs
Adapter 3 (optional)	Host-Only Network (HONEYPOT_DMZ)	Used for Honeypot VLAN/DMZ

SOC lab design
WAN (NAT) → pfSense → LAN (192.168.x.0/24) → Windows / Wazuh / Kali  
                                    ↳ OPT1 → Honeypot DMZ

             3.2 Partitioning

Choose:

Auto (ZFS) → Guided Root-on-ZFS

Partition scheme:

GPT Partition Table (recommended)

You will see partitions like:

freebsd-boot

freebsd-ufs

freebsd-swap

Click Finish → Commit

3.3 Complete Installation

Allow distribution files to extract

Reboot system

When prompted, remove the ISO so it boots from the VDI

pfSense will load into the console menu

#️⃣ 4. Initial pfSense Console Configuration

After reboot, pfSense shows:

WAN (em0)  
LAN (em1)
OPT1 (em2)  ← if enabled

4.1 Assign Interfaces

From console menu, choose:

1) Assign Interfaces

Typically:

em0 → WAN (NAT)

em1 → LAN (Host-Only ‘SOC_LAN’)

em2 → OPT1 (Host-Only ‘HONEYPOT_DMZ’) (optional)

4.2 Configure LAN IP

Menu → 2) Set interface(s) IP address

Use:

LAN IPv4: 192.168.56.1
Subnet: /24
DHCP: Yes
DHCP Range: 192.168.56.100 – 192.168.56.200
IPv6: Disabled


This allows:

Windows Endpoint

Wazuh Server

Kali Linux

—all to receive LAN IPs.

4.3 Configure OPT1 (Optional DMZ)
OPT1 IPv4: 192.168.20.1
Subnet: /24
DHCP: Yes
DHCP Range: 192.168.20.100 – 192.168.20.200
IPv6: Disabled


This network is used for Cowrie honeypot / Dionaea / malware traps.

5. Accessing the WebConfigurator

After reboot, pfSense displays:

LAN address: https://192.168.56.1/

Steps:

Open browser on host machine

Enter:
https://192.168.56.1/

Accept self-signed certificate warning

Login:

Username: admin

Password: pfsense


6. Post-Installation Steps

Create firewall rules

Add DNS configuration

Enable NTP

Configure NAT for outbound

Set up logging export for Splunk/Wazuh

Create VLANs

Create DMZ rules (honeypot isolation)


8. Completion

pfSense installation is now successfully completed and ready for SOC lab integration.

Next steps will cover:

Interface documentation

DHCP settings

NAT settings

Firewall rules

Connecting Wazuh + Splunk + Windows Endpoint + Kali

Honeypot DMZ configuration
