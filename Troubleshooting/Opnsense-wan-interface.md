# OPNsense WAN Interface Troubleshooting

## Overview

During the initial deployment of the Mini Data Center Infrastructure Lab,
the OPNsense WAN interface failed to obtain an IPv4 address.

The objective was to identify why the WAN interface had an active network
connection but was not receiving an address from the VMware NAT network.

This became a useful exercise in troubleshooting virtual network
interfaces systematically rather than reinstalling or randomly changing
configuration.

---

## Environment

**Hypervisor:** VMware Workstation  
**Firewall / Router:** OPNsense

### Intended Network Design

```text
Internet
   |
VMware NAT / VMnet8
192.168.3.0/24
   |
OPNsense WAN
   |
OPNsense Firewall / Router
   |
OPNsense LAN
192.168.1.1/24
   |
VMnet2
192.168.1.0/24
```

The intended configuration was:

- VMware NAT / VMnet8 connected to the OPNsense WAN interface
- VMnet2 connected to the OPNsense LAN interface
- WAN configured to obtain an IPv4 address through DHCP
- LAN configured as `192.168.1.1/24`

---

## Problem

After booting OPNsense, the console showed:

```text
LAN (em0) -> 192.168.1.1/24
WAN (em1) ->
```
<img width="688" height="349" alt="image" src="https://github.com/user-attachments/assets/4e412c60-d146-4291-a207-84a2ddc29c10" />


The LAN interface had an IPv4 address, but the WAN interface did not.

Because the WAN interface had no IPv4 address, OPNsense did not have the
expected connectivity through the VMware NAT network.

---

## Troubleshooting Process

### 1. Verify Network Interface Status

The first step was determining whether OPNsense could detect both virtual
network interfaces.

The interfaces were inspected with:

```bash
ifconfig em0
```

and:

```bash
ifconfig em1
```
<img width="788" height="430" alt="Screenshot 2026-10-30 094551" src="https://github.com/user-attachments/assets/c359fe1c-6694-45d0-b491-5caf4f34a38c" />


Both interfaces showed an active link.

This established that the virtual NICs existed and were connected.

The problem therefore appeared to be above the basic interface/link
level.

---

### 2. Verify OPNsense Interface Assignments

OPNsense was configured so that:

```text
LAN = em0
WAN = em1
```

The LAN interface had:

```text
192.168.1.1/24
```

The WAN interface remained without an IPv4 address.

---

### 3. Verify WAN DHCP Configuration

The WAN interface was checked to ensure that IPv4 addressing was
configured through DHCP.

WAN was configured as a DHCP client, but the interface still did not
receive an IPv4 address.

At this point, the issue was narrowed further:

```text
Interface detected       ✓
Link active              ✓
WAN interface assigned   ✓
WAN configured for DHCP  ✓
IPv4 lease received      ✗
```

---

## 4. Inspect VMware Networking

The VMware Virtual Network Editor was reviewed.

The VMware NAT network was configured as:

```text
VMnet8
Network: 192.168.3.0/24
Type: NAT
DHCP: Enabled
```

This confirmed that a VMware NAT network with DHCP was available.

The problem was therefore not simply that the expected WAN-side network
did not exist.

<img width="718" height="706" alt="Screenshot 2026-10-30 101253" src="https://github.com/user-attachments/assets/fd707671-aa98-4cd4-951a-dd8b9a3128c1" />

---

## 5. Verify Interface Mapping Using MAC Addresses

The next step was determining exactly which VMware virtual network
adapter corresponded to each OPNsense interface.

The OPNsense interfaces reported the following MAC addresses:

```text
em0
00:0C:29:EB:E8:63

em1
00:0C:29:EB:E8:6D
```

The VMware virtual network adapters were then inspected.

The comparison established:

```text
VMware Network Adapter 1
MAC: 00:0C:29:EB:E8:63
        |
        └── OPNsense em0


VMware Network Adapter 2
MAC: 00:0C:29:EB:E8:6D
        |
        └── OPNsense em1
```

This was the key troubleshooting step.

Instead of assuming VMware Network Adapter 1 corresponded to a particular
OPNsense interface based on numbering, the MAC addresses were used to
identify the interfaces directly.

---

## Root Cause

The VMware virtual networks were connected to the opposite OPNsense
interfaces from the intended design.

OPNsense was configured as:

```text
em0 = LAN
em1 = WAN
```

However, the VMware adapter connected to `em1` was attached to the
internal VMnet2 network rather than the VMware NAT network.

As a result, the OPNsense WAN interface was configured to request an
address through DHCP but was connected to the wrong virtual network.

---

## Resolution

The VMware network assignments were corrected so that the interface
mapping became:

```text
VMware NAT / VMnet8
        |
VMware Network Adapter 2
MAC: 00:0C:29:EB:E8:6D
        |
OPNsense em1
        |
       WAN
```

and:

```text
VMnet2
        |
VMware Network Adapter 1
MAC: 00:0C:29:EB:E8:63
        |
OPNsense em0
        |
       LAN
```

OPNsense was then rebooted.

---

## Validation

After correcting the interface-to-network mapping, OPNsense successfully
obtained an IPv4 address on its WAN interface.

The resulting configuration was:

```text
LAN (em0)
192.168.1.1/24

WAN (em1)
192.168.3.133/24
DHCP
```
<img width="620" height="212" alt="Screenshot 2026-10-30 102819" src="https://github.com/user-attachments/assets/fd4d3364-13fa-45dc-bd44-e56d3e1937d4" />


The WAN interface receiving an address from the `192.168.3.0/24`
VMware NAT network confirmed that the interface mapping had been
corrected.

The completed traffic path became:

```text
Internet
   |
VMware NAT / VMnet8
192.168.3.0/24
   |
OPNsense WAN
192.168.3.133/24
   |
OPNsense
   |
OPNsense LAN
192.168.1.1/24
   |
VMnet2
192.168.1.0/24
```

---

## Troubleshooting Method

The issue was resolved by narrowing the problem one layer at a time.

```text
WAN has no IPv4 address
        |
        v
Are the NICs detected?
        |
       YES
        |
        v
Do the NICs have active links?
        |
       YES
        |
        v
Is em1 assigned as WAN?
        |
       YES
        |
        v
Is WAN configured for DHCP?
        |
       YES
        |
        v
Does VMware NAT/DHCP exist?
        |
       YES
        |
        v
Is em1 actually connected to that network?
        |
       NO
        |
        v
Compare MAC addresses
        |
        v
Incorrect NIC mapping identified
        |
        v
Correct VMware network assignments
        |
        v
WAN receives DHCP address
```

---

## Key Lessons Learned

### Do not assume interface numbering matches between systems

One of the biggest lessons from this issue was that:

```text
VMware Network Adapter 1
```

should not automatically be assumed to correspond to a particular
interface such as:

```text
em0
```

or to a particular role such as WAN.

The interfaces should be positively identified.

---

### MAC addresses provide a reliable way to identify interfaces

Comparing the MAC addresses seen by VMware and OPNsense made it possible
to determine which hypervisor NIC corresponded to each OPNsense
interface.

This changed the troubleshooting process from assumption to
verification.

---

### An active link does not guarantee correct network configuration

Both OPNsense interfaces showed active links even though the WAN
interface was connected to the wrong virtual network.

An interface being `UP` or active only established that a connection
existed. It did not prove that the interface was attached to the
intended network.

---

### Troubleshoot systematically

Instead of reinstalling OPNsense or making several unrelated
configuration changes, the issue was narrowed through a sequence of
checks:

1. Interface detection
2. Link status
3. OPNsense interface assignment
4. DHCP configuration
5. VMware NAT configuration
6. MAC-address comparison
7. Virtual-network mapping
8. Corrective action
9. Validation

This approach reduced unnecessary changes and ultimately identified the
actual root cause.

---

## Skills Reinforced

This troubleshooting exercise provided practical experience with:

- VMware Workstation virtual networking
- OPNsense
- Virtual network interfaces
- WAN and LAN interface assignments
- IPv4 addressing
- DHCP
- NAT networking
- MAC addresses
- Interface identification
- Linux/FreeBSD-style network interfaces
- Layered network troubleshooting
- Root-cause analysis
- Post-change validation
