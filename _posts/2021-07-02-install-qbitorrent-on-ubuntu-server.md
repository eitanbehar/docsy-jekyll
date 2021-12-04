---
title:  "Installing qBitTorrent on Ubuntu Server"
date:   2021-07-02 
badges: 
 - type: info
   tag: qBitTorrent
tags:
  - ubuntu
  - linux
---

**qBittorrent-nox** includes only the Web UI `http://localhost:8080`

## Installation instructions

sudo:
* `add-apt-repository ppa:qbittorrent-team/qbittorrent-stable`
* `apt install qbittorrent-nox`
* `adduser --system --group qbittorrent-nox`
* `sudo adduser your-username qbittorrent-nox`
* `sudo nano /etc/systemd/system/qbittorrent-nox.service`

```
[Unit]
Description=qBittorrent Command Line Client
After=network.target

[Service]
#Do not change to "simple"
Type=forking
User=qbittorrent-nox
Group=qbittorrent-nox
UMask=007
ExecStart=/usr/bin/qbittorrent-nox -d --webui-port=8080
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## Service operations
* `sudo systemctl start qbittorrent-nox`
* `sudo systemctl daemon-reload`
* `sudo systemctl enable qbittorrent-nox`
* `systemctl status qbittorrent-nox`

## Accessing it after install

* `192.168.0.102:8080`
* Username is `admin`. Default password is `adminadmin`.


## A few things missing
1. You need to install the search plugins that matched your needs and are legal in your country
Open the Search Tab, and click on Search Plugins
2. If you customized the Downloads folder, it does not persist when saved from the UI
Open the config file `/home/qbittorrent-nox/.config/qBittorrent/qBittorrent.conf` and save it the **[Preferences]** section
`
Downloads\SavePath=/media/torrents/
`
3. Restart the service for the changes to take place `systemctl restart qbittorrent-nox`

## Reference

Overall instructions for installing qBitTorrent on Ubuntu Server can be followed from this [link]
(https://www.linuxbabe.com/ubuntu/install-qbittorrent-ubuntu-18-04-desktop-server)

 

