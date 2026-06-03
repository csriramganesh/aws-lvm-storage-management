# AWS EBS & Linux LVM Storage Management Project

## Overview

This project demonstrates how to use AWS Elastic Block Store (EBS) and Linux Logical Volume Manager (LVM) to create scalable and flexible storage on a running EC2 instance.

The project simulates a real-world scenario where additional storage is required for a Linux server. Instead of rebuilding the server or migrating data, storage is expanded dynamically using LVM while preserving existing data.

---

## Technologies Used

* AWS EC2
* AWS EBS
* Ubuntu Linux
* LVM (Logical Volume Manager)
* EXT4 Filesystem
* Linux Storage Administration

---

## Project Architecture

```text
                AWS EC2 Instance
                        │
                        ▼
          ┌─────────────────────────┐
          │      Volume Group       │
          │       vg_data           │
          └─────────────────────────┘
                   ▲        ▲
                   │        │
             PV (xvdf)  PV (xvdg)
               5 GB       6 GB

                        │
                        ▼

          ┌─────────────────────────┐
          │     Logical Volume      │
          │        lv_data          │
          └─────────────────────────┘
                        │
                        ▼
                 EXT4 Filesystem
                        │
                        ▼
                      /data
```

---

# Project Implementation

## Step 1 - Launch EC2 Instance

Created an Ubuntu EC2 instance in AWS that serves as the Linux server for the project.

Screenshot:

![EC2 Creation](Step-01-EC2-Creation.png)

---

## Step 2 - Verify Initial Storage Layout

Verified the default storage configuration and filesystem usage before attaching additional storage.

Commands Used:

```bash
lsblk
df -h
```

Screenshot:

![Initial Storage](Step-02-Initial-Storage.png)

---

## Step 3 - Create and Attach EBS Volumes

Created and attached additional EBS volumes to the running EC2 instance.

Screenshot:

![EBS Volumes Attached](03-EBS-Volumes-Attached.png)

---

## Step 4 - Verify New Disks

Verified that Linux detected the newly attached EBS volumes.

Command:

```bash
lsblk
```

Screenshot:

![New Disks Detected](04-New-Disks-Detected.png)

---

## Step 5 - Create Physical Volumes (PV)

Initialized the attached EBS volumes as LVM Physical Volumes.

Commands:

```bash
sudo pvcreate /dev/xvdf
sudo pvcreate /dev/xvdg
```

Verification:

```bash
sudo pvs
```

Screenshot:

![Physical Volumes](05-Physical-Volumes-Created.png)

---

## Step 6 - Create Volume Group (VG)

Combined the Physical Volumes into a single Volume Group named `vg_data`.

Command:

```bash
sudo vgcreate vg_data /dev/xvdf /dev/xvdg
```

Verification:

```bash
sudo vgs
```

Screenshot:

![Volume Group](06-Volume-Group-Created.png)

---

## Step 7 - Create Logical Volume (LV)

Created a Logical Volume named `lv_data` from the available storage pool.

Command:

```bash
sudo lvcreate -L 8G -n lv_data vg_data
```

Verification:

```bash
sudo lvs
```

Screenshot:

![Logical Volume](07-Logical-Volume-Created.png)

---

## Step 8 - Create EXT4 Filesystem

Formatted the Logical Volume with the EXT4 filesystem.

Command:

```bash
sudo mkfs.ext4 /dev/vg_data/lv_data
```

Verification:

```bash
sudo blkid /dev/vg_data/lv_data
```

Screenshot:

![Filesystem Created](08-Filesystem-Created.png)

---

## Step 9 - Mount the Logical Volume

Mounted the filesystem to `/data`.

Commands:

```bash
sudo mkdir /data
sudo mount /dev/vg_data/lv_data /data
```

Verification:

```bash
df -h
```

Screenshot:

![Volume Mounted](9-Volume-Mounted.png)

---

## Step 10 - Create Test Data

Created sample files to verify read and write operations on the mounted volume.

Commands:

```bash
cd /data
echo "LVM Storage Project on AWS" > project.txt
echo "Testing Logical Volume Storage" > notes.txt
```

Verification:

```bash
ls -lh
cat project.txt
```

Screenshot:

![Test Data](10-Test-Data-Created.png)

---

## Step 11 - Add Additional Storage

Created and attached an additional EBS volume to the running EC2 instance.

Screenshot:

![Third EBS Volume](11-Third-EBS-Volume-Attached.png)

---

## Step 12 - Extend Volume Group

Added the new EBS volume to the existing Volume Group.

Commands:

```bash
sudo pvcreate /dev/xvdh
sudo vgextend vg_data /dev/xvdh
```

Verification:

```bash
sudo vgs
sudo pvs
```

Screenshot:

![VG Extended](12-Volume-Group-Extended.png)

---

## Step 13 - Extend Logical Volume

Extended the Logical Volume using the newly available space.

Command:

```bash
sudo lvextend -l +100%FREE /dev/vg_data/lv_data
```

Verification:

```bash
sudo lvs
```

Screenshot:

![LV Extended](13-Logical-Volume-Extended.png)

---

## Step 14 - Resize the Filesystem

Expanded the EXT4 filesystem online to utilize the newly allocated storage.

Command:

```bash
sudo resize2fs /dev/vg_data/lv_data
```

Verification:

```bash
df -h /data
```

Screenshot:

![Filesystem Resized](14-Filesystem-Resized.png)

---

# Key Skills Demonstrated

* AWS EC2 Administration
* AWS EBS Management
* Linux Storage Administration
* Logical Volume Manager (LVM)
* Physical Volumes (PV)
* Volume Groups (VG)
* Logical Volumes (LV)
* EXT4 Filesystem Management
* Online Storage Expansion
* Production-Style Infrastructure Operations

---

# Project Outcome

Successfully implemented a scalable storage solution using AWS EBS and Linux LVM. The storage capacity was expanded dynamically without data loss, demonstrating a common real-world system administration and cloud infrastructure task.
