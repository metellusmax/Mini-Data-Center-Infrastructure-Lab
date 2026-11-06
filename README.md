# Mini Data Center Infrastructure Lab

🚧 **Project Status: Active / Work in Progress**

**Current milestone:** Local infrastructure build complete through
network segmentation, firewall policy, and packet-level traffic
analysis.

**Next:** Cloud infrastructure extension.

## Overview

This project documents the design and implementation of a virtualized
mini data center environment built to develop practical infrastructure
skills relevant to Data Center Technician, Infrastructure Support, and
systems administration roles.

The lab is built in VMware Workstation and currently includes an
OPNsense firewall/router, Ubuntu Server, and a Windows administrative
workstation.

Rather than focusing only on deployment, this project documents the
configuration, validation, troubleshooting, and administration of the
environment.

## Lab Objectives

The primary objectives of this project are to develop hands-on
experience with:

- Virtualization
- TCP/IP networking
- DHCP and DNS
- Routing and NAT
- Firewall administration
- Linux server administration
- Windows administration
- SSH remote management
- Linux storage and filesystems
- Infrastructure troubleshooting
- Network segmentation
- Packet analysis

A future phase of the project will extend the environment into cloud
infrastructure.

## Current Environment

| System | Role | Network |
|---|---|---|
| OPNsense | Router, firewall, DNS, NAT, DHCP | WAN / ADMIN / SERVER |
| `admin-ws01` | Windows administrative workstation | ADMIN |
| `ubuntu-srv01` | Linux application/server workload | SERVER |

### Key Technologies

- VMware Workstation
- OPNsense
- Windows 11 Enterprise Evaluation
- Ubuntu Server 24.04
- OpenSSH
- Nginx
- Linux ext4 storage
- PowerShell

## Current Architecture

The lab has evolved from a single flat LAN into separate ADMIN and
SERVER networks routed and protected by OPNsense.

| Zone | VMware Network | Subnet | OPNsense Interface |
|---|---|---|---|
| WAN | VMnet8 | `192.168.3.0/24` | `192.168.3.133` |
| ADMIN | VMnet2 | `192.168.1.0/24` | `192.168.1.1` |
| SERVER | VMnet3 | `10.10.20.0/24` | `10.10.20.1` |

**Key systems**

- `admin-ws01` - `192.168.1.111/24`
- `ubuntu-srv01` - `10.10.20.10/24`

Traffic between the ADMIN and SERVER subnets is routed through OPNsense
and controlled using stateful firewall policies.

➡️ [View Current Architecture](Architecture/Current-Architecture.md)

 
## Project Documentation

Detailed implementation and troubleshooting documentation is maintained
separately so the main README can remain concise and easy to navigate.

### Architecture

- [Current Lab Architecture](Architecture/Current-Architecture.md)

### Linux Administration

- [Linux Secondary Storage Configuration](Linux/storage-configuration.md)

### Network Analysis
- [Wireshark Traffic Analysis](Network-Analysis/Wireshark-Traffic-Analysis.md)

### Troubleshooting

- [OPNsense WAN Interface Troubleshooting](Troubleshooting/Opnsense-wan-interface.md)
- [Nginx Web Service Outage](Troubleshooting/nginx-service-outage.md)
  
## Completed Work

### Virtualization & Infrastructure
- [x] Built multi-VM environment in VMware Workstation
- [x] Deployed OPNsense firewall/router
- [x] Deployed Windows administrative workstation
- [x] Deployed Ubuntu Server

### Networking
- [x] Configured WAN connectivity through VMware NAT
- [x] Configured DHCP, DNS, and NAT
- [x] Created dedicated ADMIN and SERVER networks
- [x] Configured inter-subnet routing through OPNsense
- [x] Validated Internet and DNS connectivity across segmented networks
- [x] Captured and analyzed ARP, DNS, TCP, SSH, and HTTP traffic with Wireshark
- [x] Compared permitted TCP connections against firewall-blocked traffic
- [x] Validated segmented-network behavior at the packet level

### Firewall Policy
- [x] Allowed ADMIN → SERVER SSH (TCP/22)
- [x] Allowed ADMIN → SERVER HTTP (TCP/80)
- [x] Blocked other ADMIN → SERVER traffic
- [x] Blocked SERVER-initiated connections into ADMIN
- [x] Validated stateful firewall behavior

### Linux Administration
- [x] Configured SSH remote administration
- [x] Deployed Nginx
- [x] Added and partitioned secondary storage
- [x] Created ext4 filesystem
- [x] Configured persistent `/data` mount



## Project Roadmap

### Phase 1 - Core Infrastructure ✅
- [x] OPNsense firewall/router
- [x] Ubuntu Server
- [x] Windows administrative workstation
- [x] DHCP, DNS, NAT, and Internet connectivity
- [x] SSH and Nginx

### Phase 2 - Linux Server Administration & Storage ✅
- [x] Linux resource and system inspection
- [x] Secondary virtual disk
- [x] Disk partitioning
- [x] ext4 filesystem
- [x] `/data` mount point
- [x] Persistent mounting with `/etc/fstab`
- [x] Reboot validation

### Phase 3 - Network Segmentation ✅
- [x] Dedicated ADMIN network
- [x] Dedicated SERVER network
- [x] Additional OPNsense network interface
- [x] Static server addressing
- [x] Inter-subnet routing
- [x] Internet and DNS validation

### Phase 4 - Firewall Policy ✅
- [x] Stateful inter-network firewall policy
- [x] ADMIN → SERVER SSH TCP/22 allowed
- [x] ADMIN → SERVER HTTP TCP/80 allowed
- [x] Other ADMIN → SERVER traffic blocked
- [x] SERVER → ADMIN initiated connections blocked
- [x] SERVER → Internet connectivity retained
- [x] Firewall policy validation

### Phase 5 - Network Traffic Analysis 🚧
- [x] Wireshark
- [x] ARP
- [x] ICMP
- [x] DNS
- [x] TCP three-way handshake
- [x] HTTP
- [x] SSH
- [x] Allowed vs. blocked traffic analysis

### Phase 6 - Microsoft Azure Cloud Extension 🚧

The next phase will extend the infrastructure concepts developed in the
local VMware environment into Microsoft Azure.
- [ ] Create an Azure Resource Group for the lab
- [ ] Create an Azure Virtual Network (VNet)
- [ ] Design and configure Azure subnets
- [ ] Deploy an Ubuntu Server virtual machine
- [ ] Configure private IP addressing
- [ ] Configure Network Security Groups (NSGs)
- [ ] Restrict administrative access to required services
- [ ] Add and configure an Azure managed data disk
- [ ] Validate SSH and network connectivity
- [ ] Configure Azure Monitor / virtual machine monitoring
- [ ] Review network and system logs
- [ ] Document the Azure architecture
- [ ] Compare VMware infrastructure concepts with Azure equivalents

## Skills Being Developed

- VMware Workstation
- OPNsense
- Ubuntu Server
- Windows administration
- Linux administration
- TCP/IP
- DHCP
- DNS
- NAT
- Routing
- SSH
- HTTP
- Linux filesystems
- Infrastructure troubleshooting

---

This repository documents a hands-on learning environment and will
continue to evolve as additional infrastructure, networking, and cloud
components are implemented.
