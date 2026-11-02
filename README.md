# Mini Data Center Infrastructure Lab

> 🚧 **Project Status: Active / Work in Progress**

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

### OPNsense Firewall / Router

Provides routing and network services between the lab LAN and VMware
NAT network.

Current responsibilities include:

- LAN/WAN routing
- DHCP
- DNS
- NAT
- Internet connectivity for lab systems

### ubuntu-srv01

Ubuntu Server providing Linux administration and server-service
practice.

Current configuration includes:

- Ubuntu Server 24.04
- OpenSSH
- Nginx
- Secondary virtual storage
- ext4 filesystem
- Persistent `/data` mount

### admin-ws01

Windows 11 administrative workstation used to remotely manage and
validate services within the lab.

Current tools and functionality include:

- PowerShell
- SSH client
- ICMP connectivity testing
- TCP port testing
- DNS testing
- Remote Ubuntu administration

## Current Architecture

 VMware Host

                 Internet
                    |
                    |
             VMware NAT / VMnet8
              192.168.3.0/24
                    |
                   WAN
                    |
          OPNsense Firewall / Router
          WAN: 192.168.3.133/24
          LAN: 192.168.1.1/24
                    |
                   LAN
                    |
             VMnet2 - Lab LAN
              192.168.1.0/24
                  /    \
                 /      \
                /        \
        admin-ws01      ubuntu-srv01
       Windows 11       Ubuntu Server
     192.168.1.111     192.168.1.104
           |                 |
       PowerShell         SSH / Nginx
       SSH Client             |
                              |
                       Secondary Disk
                         /dev/sdb
                          10 GB
                            |
                           ext4
                            |
                          /data



The physical computer serves only as the VMware Workstation host.
Administration and testing within the documented lab are performed
from `admin-ws01`.

## Completed Work

- [x] Deployed OPNsense
- [x] Configured WAN and LAN interfaces
- [x] Configured VMware virtual networking
- [x] Validated DHCP, DNS, NAT, and Internet connectivity
- [x] Deployed Ubuntu Server
- [x] Configured OpenSSH
- [x] Installed and validated Nginx
- [x] Deployed Windows administrative workstation
- [x] Validated SSH remote administration
- [x] Validated TCP/22 and TCP/80 connectivity
- [x] Added a secondary virtual disk to Ubuntu
- [x] Partitioned secondary storage
- [x] Created an ext4 filesystem
- [x] Mounted additional storage at `/data`
- [x] Configured persistent mounting using `/etc/fstab`
- [x] Validated storage persistence after reboot

## Troubleshooting Experience

### OPNsense WAN Interface

During the initial deployment, the OPNsense WAN interface failed to
receive an IPv4 address through DHCP.

Troubleshooting included:

1. Verifying virtual NIC link status.
2. Confirming interface assignments inside OPNsense.
3. Verifying WAN DHCP configuration.
4. Checking VMware NAT and DHCP configuration.
5. Comparing VMware virtual NIC MAC addresses against OPNsense
   interfaces.
6. Identifying an incorrect virtual NIC-to-network mapping.
7. Correcting the VMware network assignments.
8. Confirming that the WAN interface successfully received an address.

This exercise reinforced the importance of validating interface
identity rather than assuming hypervisor adapter numbering matches
operating-system interface numbering.

### Incident 001: Web Service Unavailable

A simulated service outage was created on `ubuntu-srv01`.

Testing confirmed that the server remained reachable while TCP/80 was
unavailable. Investigation of the Nginx service found that the service
was inactive.

The service was restored and validated using TCP connectivity testing
and an HTTP request.

**Key takeaway:** Host availability does not guarantee application or
service availability.

## Added Linux Storage

A second 10 GB virtual disk was added to `ubuntu-srv01`.

The storage workflow included:

1. Identifying the new block device with `lsblk`.
2. Creating a partition using `fdisk`.
3. Creating an ext4 filesystem.
4. Creating `/data` as the mount point.
5. Mounting the filesystem.
6. Verifying capacity with `df`.
7. Identifying the filesystem UUID.
8. Adding the filesystem to `/etc/fstab`.
9. Testing the configuration.
10. Rebooting and validating the persistent mount.

This exercise helped reinforce the relationship between:

**Disk → Partition → Filesystem → Mount Point → Persistent Storage**

## Project Roadmap

### Phase 1 - Core Infrastructure ✅
- OPNsense
- Ubuntu Server
- Windows administration workstation
- Networking
- SSH
- Nginx

### Phase 2 - Linux Server Administration 🚧
- Storage
- CPU and memory inspection
- Processes and services
- Logs
- Permissions
- Resource monitoring

### Phase 3 - Network Segmentation
- Management network
- Server network
- Inter-subnet routing
- IP addressing plan

### Phase 4 - Firewall Policy
- Management-to-server access
- Service-specific rules
- Connectivity validation

### Phase 5 - Network Analysis
- Wireshark
- ARP
- ICMP
- DNS
- TCP handshake
- HTTP
- SSH traffic analysis

### Phase 6 - Cloud Extension
Planned future expansion of the project into cloud infrastructure to
compare traditional virtualized networking, compute, storage, and
security concepts with cloud equivalents.

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
