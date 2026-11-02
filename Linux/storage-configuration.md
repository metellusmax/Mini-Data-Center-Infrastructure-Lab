# Linux Secondary Storage Configuration

## Overview

This exercise expanded the storage available to `ubuntu-srv01` by adding
a secondary virtual disk through VMware Workstation and configuring the
disk for persistent use within Ubuntu Server.

The objective was to understand the relationship between:

**Virtual Disk → Linux Block Device → Partition → Filesystem → Mount Point → Persistent Storage**

---

## Environment

**Server:** `ubuntu-srv01`  
**Operating System:** Ubuntu Server 24.04  
**Hypervisor:** VMware Workstation

### Existing Storage

The server was originally configured with a 30 GB virtual disk containing
the Ubuntu operating system.

```text
/dev/sda - 30 GB
```

### Additional Storage

A second 10 GB virtual disk was added through VMware Workstation.

After booting the server, the new disk appeared as:

```text
/dev/sdb - 10 GB
```

The existing operating-system disk remained `/dev/sda`.

---

## 1. Identify the New Disk

I used the following command to inspect the block devices detected by Linux:

```bash
lsblk
```

The output showed the existing 30 GB operating-system disk and the newly
added 10 GB disk.

```text
sda      30G  disk
├─sda1
├─sda2
└─sda3

sdb      10G  disk
```
<img width="510" height="262" alt="Screenshot 2026-11-01 132253" src="https://github.com/user-attachments/assets/c2f75d93-19bf-42be-97db-f68cb59ceaaa" />


Before making storage changes, I verified that `/dev/sdb` was the newly
added disk and that `/dev/sda` contained the operating system.

### Key Takeaway

Storage devices should be identified from the actual system before
partitioning or formatting them. Device names should not be assumed.

---

## 2. Partition the Disk

I opened the new disk with `fdisk`:

```bash
sudo fdisk /dev/sdb
```

A new partition was created using the available space on the disk.

Before writing the changes, I reviewed the proposed partition table.

After the changes were written, I verified the result with:

```bash
lsblk
```

The storage layout now included:

```text
/dev/sdb
└── /dev/sdb1
```

`/dev/sdb` represents the disk, while `/dev/sdb1` represents the first
partition created on that disk.


---

## 3. Create the Filesystem

The new partition still required a filesystem before it could be used
to store files.

I created an ext4 filesystem on `/dev/sdb1`:

```bash
sudo mkfs.ext4 /dev/sdb1
```

I then verified the filesystem information with:

```bash
lsblk -f
```
<img width="1107" height="167" alt="Screenshot 2026-11-01 140611" src="https://github.com/user-attachments/assets/9be28e96-1415-412f-8910-2cfca8b9e3c5" />


The partition now reported:

```text
Device: /dev/sdb1
Filesystem: ext4
```

A filesystem UUID was also assigned to the new filesystem.

---

## 4. Create the Mount Point

Linux filesystems are made accessible by attaching them to a directory
within the Linux directory tree.

I created `/data` as the mount point:

```bash
sudo mkdir -p /data
```

The new filesystem was then mounted:

```bash
sudo mount /dev/sdb1 /data
```

I verified the mount with:

```bash
findmnt /data
```

and:

```bash
df -h /data
```

The new filesystem was now accessible through:

```text
/data
```

---

## 5. Validate Storage

To verify that the filesystem was usable, I created a test file:

```bash
sudo touch /data/storage-test.txt
```

I verified the contents with:

```bash
ls -l /data
```

The directory also contained:

```text
lost+found
```

`lost+found` was created as part of the ext4 filesystem rather than
being manually created during the lab.

---

## 6. Test Mount Behavior After Reboot

The server was rebooted after manually mounting the filesystem.

After reconnecting to the server, I checked:

```bash
findmnt /data
```

The filesystem was no longer mounted at `/data`.

This demonstrated an important distinction:

A manual `mount` operation attaches a filesystem to the running system,
but that alone does not configure the filesystem to be mounted
automatically during future boots.

The data stored on `/dev/sdb1` had not been deleted. The filesystem was
simply no longer mounted at `/data`.

---

## 7. Configure Persistent Mounting

I identified the filesystem UUID using filesystem information from the
server.

Before editing the filesystem configuration, I created a backup:

```bash
sudo cp /etc/fstab /etc/fstab.backup
```

I then edited:

```text
/etc/fstab
```

and added an entry using the UUID associated with `/dev/sdb1`.

The entry followed this structure:

```text
UUID=<filesystem-uuid> /data ext4 defaults 0 2
```

The filesystem UUID is used to identify the filesystem independently of
its `/dev/sdX` device name.

> The lab-specific UUID is intentionally not required for reproducing
> this project. The correct UUID should always be obtained from the
> system being configured.

---

## 8. Test the fstab Configuration

Before rebooting the server, I tested the configuration with:

```bash
sudo mount -a
```

I then verified the mount using:

```bash
findmnt /data
```

and:

```bash
df -h /data
```

Testing the configuration before rebooting provided an opportunity to
detect mount-configuration errors before relying on the configuration
during system startup.

---

## 9. Validate Persistence

I created another test file:

```bash
sudo touch /data/persistence-test.txt
```

The server was then rebooted.

After reconnecting, I validated the storage using:

```bash
findmnt /data
```

```bash
df -h /data
```

```bash
ls -l /data
```
<img width="471" height="216" alt="Screenshot 2026-11-02 082843" src="https://github.com/user-attachments/assets/8eb5dc91-d02d-4d2e-ad1e-ffb715bf89ca" />


The results confirmed that:

- `/dev/sdb1` automatically mounted at `/data`
- The ext4 filesystem remained available
- `storage-test.txt` remained present
- `persistence-test.txt` remained present

This confirmed that the secondary filesystem remained available after
the server restarted.

---

## Final Storage Layout

```text
VMware Workstation
        |
        └── 10 GB Virtual Disk
                 |
                 └── /dev/sdb
                        |
                        └── /dev/sdb1
                               |
                               └── ext4 filesystem
                                      |
                                      └── /data
```

The Linux storage configuration can be summarized as:

| Component | Configuration |
|---|---|
| Virtual Disk | 10 GB |
| Linux Disk | `/dev/sdb` |
| Partition | `/dev/sdb1` |
| Filesystem | ext4 |
| Mount Point | `/data` |
| Persistent Configuration | `/etc/fstab` |

---

## Concepts Reinforced

This exercise provided hands-on practice with:

- VMware virtual storage
- Linux block devices
- Disk identification
- Disk partitioning
- `fdisk`
- ext4 filesystems
- Mount points
- `lsblk`
- `df`
- `findmnt`
- Filesystem UUIDs
- `/etc/fstab`
- Persistent filesystem mounting
- Storage validation after reboot

### Disk vs Partition vs Filesystem vs Mount

One of the most important concepts reinforced during the exercise was
the difference between the layers of Linux storage.

**Disk**

```text
/dev/sdb
```

Represents the storage device detected by Linux.

**Partition**

```text
/dev/sdb1
```

Represents a partition created on that disk.

**Filesystem**

```text
ext4
```

Provides the structure used to organize and store files within the
partition.

**Mount Point**

```text
/data
```

Provides the location within the Linux directory tree where the
filesystem becomes accessible.

The complete relationship is:

```text
Disk
  ↓
Partition
  ↓
Filesystem
  ↓
Mount
  ↓
Files and Directories
```

---

## Key Takeaways

The main lesson from this exercise was that adding a disk to a virtual
machine does not automatically make the storage usable by the operating
system.

The storage must move through several layers:

1. Linux detects the disk.
2. The correct block device is identified.
3. The disk is partitioned.
4. A filesystem is created.
5. A mount point is created.
6. The filesystem is mounted.
7. The mount is validated.
8. Persistent mounting is configured.
9. The configuration is validated after reboot.

The exercise also reinforced the importance of verifying the correct
device before making storage changes, especially when commands such as
`fdisk` and `mkfs` are involved.
