<div align="center">

# Mini Data Center Infrastructure Lab

**A virtualized enterprise-style lab for networking, systems administration, and infrastructure troubleshooting.**

[![Status: Complete](https://img.shields.io/badge/status-core%20infrastructure%20complete-2ea44f?style=for-the-badge)](https://github.com/metellusmax/Mini-Data-Center-Infrastructure-Lab)
[![Virtualization](https://img.shields.io/badge/platform-VMware%20Workstation-607d8b?style=for-the-badge)](https://github.com/metellusmax/Mini-Data-Center-Infrastructure-Lab)
[![Networking](https://img.shields.io/badge/networking-segmented%20lab-2563eb?style=for-the-badge)](https://github.com/metellusmax/Mini-Data-Center-Infrastructure-Lab)

[Explore the architecture](Architecture/Current-Architecture.md) · [Browse project documentation](#project-documentation)

</div>

---

A hands-on virtualized infrastructure lab built with VMware Workstation, OPNsense, Windows 11, and Ubuntu Server. It demonstrates network segmentation, inter-subnet routing, stateful firewall policy, Linux storage administration, remote server management, troubleshooting, and packet-level traffic analysis.

## Overview

This lab was designed to build practical skills relevant to data center technician, infrastructure support, and systems administration roles. It evolved from a basic virtual network into a segmented environment in which services and network paths were configured, tested, and documented.

The local infrastructure project is complete. Future learning will continue in a separate Microsoft Azure project.

## Environment

| System | Role | Network |
|---|---|---|
| OPNsense | Router, firewall, DNS, DHCP, and NAT | WAN / ADMIN / SERVER |
| `admin-ws01` | Windows administrative workstation | ADMIN |
| `ubuntu-srv01` | Linux server and application workload | SERVER |

### Technologies

- VMware Workstation
- OPNsense
- Windows 11 Enterprise Evaluation
- Ubuntu Server 24.04
- OpenSSH and Nginx
- Linux ext4 filesystem
- PowerShell
- Wireshark

## Network Architecture

OPNsense routes between the ADMIN and SERVER networks and enforces stateful firewall rules. The WAN interface connects through VMware NAT.

| Zone | VMware network | Subnet | OPNsense address |
|---|---|---|---|
| WAN | VMnet8 | `192.168.3.0/24` | `192.168.3.133` |
| ADMIN | VMnet2 | `192.168.1.0/24` | `192.168.1.1` |
| SERVER | VMnet3 | `10.10.20.0/24` | `10.10.20.1` |

### Key systems

- `admin-ws01` — `192.168.1.111/24`
- `ubuntu-srv01` — `10.10.20.10/24`

### Inter-network policy

- Allow ADMIN-to-SERVER SSH on TCP/22.
- Allow ADMIN-to-SERVER HTTP on TCP/80.
- Block other ADMIN-to-SERVER traffic.
- Block connections initiated from SERVER to ADMIN.
- Preserve SERVER-to-Internet access and stateful return traffic.

See the [current lab architecture](Architecture/Current-Architecture.md) for additional details.

## Work Completed

### Infrastructure and Linux administration

- Built a multi-VM environment and deployed OPNsense, Windows, and Ubuntu Server.
- Configured VMware virtual networking, WAN connectivity, DHCP, DNS, and NAT.
- Set up SSH remote administration and deployed Nginx.
- Added a 10 GB virtual disk, partitioned it, created an ext4 filesystem, and mounted it at `/data`.
- Configured `/etc/fstab` and validated that the mount persisted after reboot.

### Networking and security

- Created dedicated ADMIN and SERVER networks.
- Configured static server addressing and inter-subnet routing through OPNsense.
- Applied and validated stateful firewall rules for permitted and blocked traffic.
- Verified Internet and DNS connectivity from the segmented networks.

### Traffic analysis and troubleshooting

- Captured and analyzed ARP, DNS, TCP, SSH, and HTTP traffic with Wireshark.
- Observed the TCP three-way handshake and HTTP `GET` and `200 OK` responses.
- Compared permitted connections with traffic blocked by firewall policy, including TCP/443.
- Documented infrastructure and service troubleshooting.

## Project Documentation

- **Architecture:** [Current Lab Architecture](Architecture/Current-Architecture.md)
- **Linux administration:** [Secondary Storage Configuration](Linux/storage-configuration.md)
- **Network analysis:** [Wireshark Traffic Analysis](Network-Analysis/Wireshark-Traffic-Analysis.md)
- **Troubleshooting:** [OPNsense WAN Interface](Troubleshooting/Opnsense-wan-interface.md) · [Nginx Web Service Outage](Troubleshooting/nginx-service-outage.md)

## Project Phases

1. **Core infrastructure** — Deployed the virtual machines, configured virtual networking, DHCP, DNS, NAT, SSH, and Nginx.
2. **Linux administration and storage** — Inspected system resources, added and formatted secondary storage, configured the persistent `/data` mount, and tested it after reboot.
3. **Network segmentation** — Created the ADMIN and SERVER networks and configured and tested routed connectivity.
4. **Firewall policy** — Allowed required SSH and HTTP access, blocked other ADMIN-to-SERVER traffic and SERVER-initiated access to ADMIN, and verified stateful return traffic.
5. **Network traffic analysis** — Captured and examined traffic to validate network behavior and firewall policy at the packet level.

## Next Steps

The next learning project will focus on Microsoft Azure infrastructure, applying these concepts to cloud networking, virtual machines, storage, network security, and monitoring.
