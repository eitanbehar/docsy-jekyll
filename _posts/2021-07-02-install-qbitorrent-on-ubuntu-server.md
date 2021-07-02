---
title:  "Installing qBitTorrent on Ubuntu Server"
date:   2021-07-02 
badges: 
 - type: info
   tag: ubuntu
---

Overall instructions for installing qBitTorrent on Ubuntu Server can be followed from this [link]
(https://www.linuxbabe.com/ubuntu/install-qbittorrent-ubuntu-18-04-desktop-server)

A few things missing:
1. You need to install the search plugins that matched your needs and are legal in your country
Open the Search Tab, and click on Search Plugins
2. If you customized the Downloads folder, it does not persist when saved from the UI
Open the config file `/home/qbittorrent-nox/.config/qBittorrent/qBittorrent.conf` and save it the **[Preferences]** section
`
Downloads\SavePath=/media/torrents/
`
3. Restart the service for the changes to take place `systemctl restart qbittorrent-nox`


