---
title:  "Fix apt upgrade error when connecting to fr.archive.ubuntu.com"
date:   2021-12-03 
badges: 
 - type: info
   tag: apt-get
tags:
  - ubuntu
---

Recently, when trying to upgrade my tiny Ubuntu Server, got this error:

```
Failed to fetch http://fr.archive.ubuntu.com/ubuntu/dists/focal-updates/InRelease  Cannot initiate the connection to fr.archive.ubuntu.com:80 (2001:bc8:1600:4:63f:72ff:feaf:a2de). - connect (101: Network is unreachable)
```

I thought that the issue was related to IPv6, so I disabled it:

`nano /etc/sysctl.conf1`

Add:

```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```

Run `sysctl -p`

Confirm `cat /proc/sys/net/ipv6/conf/all/disable_ipv6`
It should return 1 (disabled)

This didn't help (also got error with IPv4)
```
W: Failed to fetch http://fr.archive.ubuntu.com/ubuntu/dists/focal/InRelease  Could not connect to fr.archive.ubuntu.com:80 (51.158.154.169), connection timed out
```

So, after doing a ping to `fr.archive.ubuntu.com` and confirming it's down, just replaced it with `us.archive.ubuntu.com`:

```
nano /etc/apt/sources.list
```

Finally packages upgrade is running

```
apt-get update
apt upgrade
```
