---
title:  "Home Server with Ubuntu and a Streaming Device"
date:   2021-02-15 
categories: linux
tags:
 - linux
---

# Installing Ubuntu and Tranmission in a Minix Neo Z64-W

Got a Minix Neo Z64-W from a friend, it comes with Windows, and runs pretty sluggish
My old XtreamerTV device managed to burn two internal 2.5" hard drives (this is another story)
So, project at hand:
* Install Ubuntu Server on the Minix Neo Z64-W
* Configure a few critical services:
  * SSH
  * Samba (to access content in the local network, upnp is better, but VLC cannot get subtitles using upnp)
  * duf (just because)
* Mount external USB disk, and make sure it's mounted after restart
* Install Transmission

<!--more-->

## Installing Ubuntu Server

It was pretty straighforward, just followed (some of) the instructions here:
<http://hwswbits.blogspot.com/2015/03/getting-linux-on-minix-neo-z64.html>

Basically it was:
* Download Ubuntu Server 
* Download Rufus
* Create USB Boot disk
* Enter BIOS and configure Boot from USB
* Boot from USB
* Clean the hard drive (except the UEFI partitions)
* Install Ubuntu


## Mounting USB external drive

It is something like this:

```
mkdir /media/external
mount /dev/sda1 /media/external
ls /media/external
sudo blkid /dev/sda1 | awk -F'"' '{print $2}'
cp /etc/fstab /etc/fstab.backup
nano /etc/fstab
```

fstab file:
``` config
# 96A803ADA8038AC7 sudo blkid /dev/sda1 | awk -F'"' '{print $2}'
UUID=96A803ADA8038AC7 /media auto nosuid,nodev,nofail 0 0
```


## Configure Samba

`nano /etc/samba/smb.conf`

``` config
# media share usb
[media]
   path = /media/transmission
   browsable = yes
   writable = yes
   read only = no
   guest ok = yes
```

`service smbd restart`

### Avoid USB powering-off the external drive

`nano /etc/default/grub`

`GRUB_CMDLINE_LINUX_DEFAULT="usbcore.autosuspend=-1"`

`update-grub`

`cat /sys/module/usbcore/parameters/autosuspend`


## Install Transmission

Ref:

Basically, it's just running a bunch of commands

~~~ bash
apt-get update
apt-get install transmission-cli transmission-common transmission-daemon

cd /home/<my_user>/Downloads
mkdir transmission
cd transmission
mkdir completed incomplete torrents

usermod -a -G debian-transmission <my_user>
chgrp -R debian-transmission /home/<my_user>/Downloads/transmission
chmod -R 775 /home/<my_user>/Downloads/transmission

service transmission-daemon start
service transmission-daemon stop

service transmission-daemon reload
~~~

There is plenty of documentation about configuration settings for Transmision

## Install duf

```
curl -s https://api.github.com/repos/muesli/duf/releases/latest | grep browser_download_url | grep linux_amd64.deb | cut -d '"' -f 4 | wget -i -
dpkg -i duf_*_linux_amd64.deb
duf
```

---

