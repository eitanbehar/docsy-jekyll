---
title:  "Fedora Server on Minix NEO Z64-W"
date:   2023-11-01
badges: 
 - type: info
   tag: linux
 - type: info
   tag: fedora
tags:
  - linux  
---

The Minix NEO Z64-W is intended to run Windows, but, since it has an Intel chipset, it runs Linux Server pretty well.
Until a few days ago, I had a stable version of Ubuntu Server 20 LTS running there. A failed attempt to upgrade to Ubuntu LTS 22 brings me here.

<!--more-->

The preparation process is straighforward:
* Download **Fedora Server** iso image
* Use **Rufus** to create bootable USB drive
* Disable `Bit Execute` on the BIOS to boot from the USB drive
* Next, Next, Next (be sue to enable root for ssh if you intend to use it headless)

**Warning**: `dnf upgrade` broke the whole installation, so I had to start over   

## Stuff I installed

`dnf install` nano samba git qbitorrent-nox pip3 nfs-utils

# Mounting External USB Drives
* `mkdir /media/external /media/backup`
* `mount /dev/sda1 /media/external`
* `mount /dev/sda2 /media/backup`
* `blkid /dev/sda1` 
* `cp /etc/fstab /etc/fstab.backup`
* `nano /etc/fstab`
```
UUID=384A8DB912EAB6E6 /media/backup auto nosuid,nodev,nofail 0 0
UUID=96A803ADA8038AC7 /media/external auto nosuid,nodev,nofail 0 0
```

## NFS Configuration for everyone

* `systemctl enable --now rpcbind nfs-server`
* `systemctl start nfs-server`
* `nano /etc/exports`
```
media/external/transmission 192.168.1.0/24(rw,sync,insecure,all_squash,anonuid=0,anongi>
/media/backup 192.168.1.0/24(rw,sync,insecure,all_squash,anonuid=0,anongid=0)
```
* exportfs -ra

## Disable Firewall
* `systemctl stop firewalld`
* `systemctl disable firewalld`
 
