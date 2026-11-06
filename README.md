# Mini Data Center Infrastructure Lab

✅ **Project Status: Core Infrastructure Build Complete**

This project demonstrates the design, implementation, administration,
and validation of a virtualized mini data center environment using
VMware Workstation, OPNsense, Windows, and Ubuntu Server.

The completed environment includes Linux storage administration,
network segmentation, inter-subnet routing, stateful firewall policy,
remote server administration, infrastructure troubleshooting, and
packet-level network analysis with Wireshark.

Future infrastructure learning will continue in a separate Microsoft
Azure cloud infrastructure project.

## Overview

This project documents the design and implementation of a virtualized
mini data center environment built to develop practical infrastructure
skills relevant to Data Center Technician, Infrastructure Support, and
systems administration roles.

The environment was built in VMware Workstation using OPNsense,
Windows 11, and Ubuntu Server.

The project progressed from basic virtual networking into a segmented
infrastructure environment incorporating Linux storage, inter-subnet
routing, stateful firewall policy, remote administration, infrastructure
troubleshooting, and packet-level network analysis.

The focus of the project was not only deploying infrastructure, but
understanding how the individual components interact and validating the
environment through hands-on testing and troubleshooting.

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


## Environment

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
- Wireshark

## Architecture

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

- [x] Deploy OPNsense firewall/router
- [x] Deploy Ubuntu Server
- [x] Deploy Windows administrative workstation
- [x] Configure VMware virtual networking
- [x] Configure DHCP and DNS
- [x] Configure NAT and Internet connectivity
- [x] Configure SSH remote administration
- [x] Install and validate Nginx

### Phase 2 - Linux Server Administration & Storage ✅

- [x] Inspect CPU, memory, storage, networking, and services
- [x] Add a secondary 10 GB virtual disk
- [x] Identify the new Linux block device
- [x] Partition the secondary disk
- [x] Create an ext4 filesystem
- [x] Mount storage at `/data`
- [x] Configure persistent mounting with `/etc/fstab`
- [x] Validate storage persistence after reboot

### Phase 3 - Network Segmentation ✅

- [x] Create dedicated ADMIN network
- [x] Create dedicated SERVER network
- [x] Add and identify an additional OPNsense network interface
- [x] Configure static server addressing
- [x] Configure inter-subnet routing
- [x] Validate Internet and DNS connectivity from the SERVER network
- [x] Validate ADMIN-to-SERVER routed connectivity

### Phase 4 - Stateful Firewall Policy ✅

- [x] Configure firewall policy between ADMIN and SERVER networks
- [x] Allow ADMIN → SERVER SSH over TCP/22
- [x] Allow ADMIN → SERVER HTTP over TCP/80
- [x] Block other ADMIN → SERVER traffic
- [x] Block SERVER-initiated connections into ADMIN
- [x] Maintain SERVER → Internet connectivity
- [x] Validate stateful return traffic

### Phase 5 - Network Traffic Analysis ✅

- [x] Capture traffic with Wireshark
- [x] Analyze ARP and default-gateway resolution
- [x] Analyze DNS name resolution
- [x] Observe the TCP three-way handshake
- [x] Analyze SSH traffic over TCP/22
- [x] Analyze HTTP traffic over TCP/80
- [x] Observe HTTP `GET` and `200 OK` responses
- [x] Compare permitted TCP traffic with firewall-blocked TCP/443 traffic
- [x] Validate network and firewall behavior at the packet level

---

## Core Project Complete ✅

The local Mini Data Center Infrastructure Lab is now considered complete.

The project progressed from a basic virtual network into a segmented
infrastructure environment with:

- VMware virtualization
- OPNsense routing and firewalling
- Windows and Linux administration
- DHCP, DNS, and NAT
- Linux storage and filesystems
- SSH and Nginx services
- ADMIN and SERVER network segmentation
- Inter-subnet routing
- Stateful firewall access controls
- Infrastructure troubleshooting
- Wireshark packet analysis

The next learning project will focus on Microsoft Azure cloud
infrastructure and apply many of these same infrastructure concepts to
cloud networking, virtual machines, storage, network security, and
monitoring.



