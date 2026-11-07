# Current Lab Architecture

The Mini Data Center Infrastructure Lab currently uses VMware Workstation to host an OPNsense firewall/router, a Windows administrative workstation, and an Ubuntu Linux server.

<img width="2218" height="2268" alt="image" src="https://github.com/user-attachments/assets/b3f7e351-802d-4f3d-8750-6ef34cd3a920" />

## Network Segmentation

The original lab placed `admin-ws01` and `ubuntu-srv01` on the same
`192.168.1.0/24` network.

The environment was redesigned to separate administrative and server
systems into dedicated networks:

| Network | VMware Network | Subnet | OPNsense Interface |
|---|---|---|---|
| ADMIN | VMnet2 | `192.168.1.0/24` | `192.168.1.1` |
| SERVER | VMnet3 | `10.10.20.0/24` | `10.10.20.1` |
| WAN | VMnet8 | `192.168.3.0/24` | `192.168.3.133` |

`admin-ws01` resides on the ADMIN network at `192.168.1.111`.

`ubuntu-srv01` resides on the SERVER network at `10.10.20.10`.

Because the systems are now on different IP networks, communications
between ADMIN and SERVER are routed through OPNsense rather than occurring
directly on a shared Layer 2 network.

### WAN Network

- **Network:** `192.168.3.0/24`
- **VMware Network:** `VMnet8`
- **Purpose:** Provides the external/WAN-side network for OPNsense
- **OPNsense WAN:** `192.168.3.133/24`

### Administrative Workstation

**Hostname:** `admin-ws01`

- Windows 11 Enterprise Evaluation
- IP: `192.168.1.111/24`
- Gateway: `192.168.1.1`
- Used for PowerShell administration, SSH access, connectivity testing, and remote server management

### Linux Server

**Hostname:** `ubuntu-srv01`

- Ubuntu Server 24.04
- IP: `10.10.20.10/24`
- Gateway: `10.10.20.1`
- OpenSSH on TCP/22
- Nginx on TCP/80

### Linux Data Storage

A secondary 10 GB virtual disk was added to `ubuntu-srv01`.

Storage configuration:

```text
/dev/sdb
   |
   └── /dev/sdb1
          |
          └── ext4 filesystem
                 |
                 └── /data
```

The filesystem is configured to mount persistently at `/data`.

## Inter-Network Firewall Policy

OPNsense provides both routing and stateful firewall enforcement between
the ADMIN and SERVER networks.

The current policy is:

| Source | Destination | Service | Action |
|---|---|---|---|
| ADMIN | `ubuntu-srv01` | SSH TCP/22 | Allow |
| ADMIN | `ubuntu-srv01` | HTTP TCP/80 | Allow |
| ADMIN | SERVER network | Other traffic | Block |
| SERVER | ADMIN network | New connections | Block |
| SERVER | Internet | Outbound traffic | Allow |

The firewall rules are applied based on where new traffic enters
OPNsense.

Traffic initiated by `admin-ws01` enters OPNsense through the ADMIN
interface, while traffic initiated by `ubuntu-srv01` enters through the
SERVER interface. Stateful return traffic for permitted connections is
allowed.

## Future Architecture

The local infrastructure project is complete. Future learning will
continue in a separate Microsoft Azure project.

The Azure project will apply concepts developed in the VMware lab to cloud
infrastructure, including:

- Virtual networks and subnets
- Cloud virtual machines
- Network security controls
- Linux server administration
- Cloud storage
- Routing and connectivity
- Monitoring and logging

This separate project will provide an opportunity to compare traditional
virtualized infrastructure concepts with their cloud equivalents.
