# Incident 001 - Nginx Web Service Unavailable

## Overview

This exercise focused on troubleshooting a web service failure on
`ubuntu-srv01`.

The server remained reachable across the network, but the Nginx web
service was unavailable.

The objective was to determine whether the problem was related to:

- Network connectivity
- TCP port availability
- The Ubuntu server
- The Nginx service

The troubleshooting process demonstrated that successful connectivity
to a server does not guarantee that every service running on the server
is available.

---

## Environment

**Server:** `ubuntu-srv01`  
**Server IP:** `192.168.1.104/24`  
**Operating System:** Ubuntu Server 24.04  
**Network:** `192.168.1.0/24`  
**Gateway:** `192.168.1.1`  
**Web Service:** Nginx  
**Protocol:** HTTP  
**Port:** TCP/80  

Testing and remote administration were performed from the Windows
administrative workstation.

---

## Reported Issue

The web service hosted on `ubuntu-srv01` was unavailable.

The initial question was whether the issue affected the entire server or
only the web service.

---

## Initial Connectivity Test

The first test checked basic connectivity to the Ubuntu server:

```powershell
ping 192.168.1.104
```

The server successfully responded to the ICMP requests.

This established that:

- `ubuntu-srv01` was reachable across the network
- Basic IP connectivity between the administrative workstation and the
  Ubuntu server was functioning

However, successful ping responses did not prove that the web service
was operational.

---

## Test TCP Port 80

The next step was testing the port used by HTTP:

```powershell
Test-NetConnection 192.168.1.104 -Port 80
```

The test showed that the host was reachable but TCP port 80 was not.

The result followed this pattern:

```text
PingSucceeded    : True
TcpTestSucceeded : False
```

This was an important distinction.

```text
Server reachable     ✓
HTTP TCP/80          ✗
```

Because general connectivity was functioning, the investigation moved
from basic network connectivity toward the web service.

---

## Investigate the Nginx Service

I connected to `ubuntu-srv01` using SSH and checked the Nginx service:

```bash
sudo systemctl status nginx
```

The service status showed:

```text
Active: inactive (dead)
```

This confirmed that the Ubuntu server itself remained online while the
Nginx web service was not running.

---

## Root Cause

The Nginx service on `ubuntu-srv01` was stopped.

Because Nginx was inactive, the server did not have the expected web
service available on TCP port 80.

The issue was therefore a **service availability problem**, rather than
a general server or network connectivity failure.

---

## Resolution

The Nginx service was started from the Ubuntu SSH session:

```bash
sudo systemctl start nginx
```

The service status was checked again:

```bash
sudo systemctl status nginx
```

Nginx now reported:

```text
Active: active (running)
```

---

## Validate TCP Connectivity

After restoring the Nginx service, TCP port 80 was tested again from
the administrative workstation:

```powershell
Test-NetConnection 192.168.1.104 -Port 80
```

The test now showed:

```text
TcpTestSucceeded : True
```

This established that the web-service port was reachable again.

---

## Validate the HTTP Service

Port connectivity alone was not used as the final validation.

An HTTP request was sent to the server:

```powershell
curl 192.168.1.104
```

The server returned an HTTP response with:

```text
StatusCode        : 200
StatusDescription : OK
```

The Nginx webpage was also returned.

This confirmed that:

```text
Server connectivity       ✓
TCP/80 connectivity       ✓
Nginx service             ✓
HTTP response             ✓
```

The web service was successfully restored.

---

## Troubleshooting Flow

The investigation followed this progression:

```text
Web Service Unavailable
        |
        v
Can the server be reached?
        |
       YES
        |
        v
Is TCP/80 reachable?
        |
       NO
        |
        v
Investigate Nginx
        |
        v
Service is inactive
        |
        v
Start Nginx
        |
        v
Retest TCP/80
        |
       PASS
        |
        v
Send HTTP request
        |
   HTTP 200 OK
        |
        v
Service Restored
```

---

## Key Lessons Learned

### Host availability does not equal service availability

The most important lesson from this exercise was:

> **A server can be online and reachable while an individual service on
> that server is unavailable.**

A successful ping established that the host was reachable, but it did
not establish that Nginx was functioning.

---

### Test the specific service

Testing TCP port 80 provided more specific information than ping alone.

The combination:

```text
Ping succeeds
TCP/80 fails
```

helped narrow the investigation from general network connectivity
toward the service/application layer.

---

### Validate at multiple levels

After restarting Nginx, validation was performed at more than one
level.

First:

```powershell
Test-NetConnection 192.168.1.104 -Port 80
```

confirmed that TCP/80 was reachable.

Then:

```powershell
curl 192.168.1.104
```

confirmed that the web server was actually responding to HTTP requests.

This reinforced the difference between:

```text
Host availability
       ↓
Port availability
       ↓
Service availability
       ↓
Application response
```

---

## Skills Reinforced

This exercise provided hands-on practice with:

- Windows PowerShell
- ICMP connectivity testing
- TCP port testing
- `Test-NetConnection`
- SSH remote administration
- Linux service management
- `systemctl`
- Nginx
- HTTP
- TCP/80
- HTTP response validation
- Layered troubleshooting
- Root-cause identification
- Post-resolution validation

---

## Main Takeaway

The troubleshooting process can be summarized as:

> **Start broad and progressively narrow the problem.**

Instead of assuming that an inaccessible website meant the server or
network was down, each test answered a more specific question:

1. Is the server reachable?
2. Is the required TCP port reachable?
3. Is the service running?
4. Does the application respond?
5. Does the service work after corrective action?

This approach helped isolate the actual failure while avoiding
unnecessary changes to the working network infrastructure.
