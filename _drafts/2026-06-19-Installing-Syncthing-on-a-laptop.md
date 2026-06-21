---
title: Setting up Syncthing on a Laptop
date: 2026-06-19
categories: [Homelab, Laptop, Syncthing]
tags: [homelab, laptop, backup, syncthing]     # TAG names should always be lowercase
draft: true
image:
   path: ../assets/img/posts/2026/2026-06-19-Installing-Syncthing-on-a-laptop/Syncthing_Logo.png
---

As I do most of my web devlopment on my Linux laptop, I thought it prudent to install Syncthing on laptop to backup up my work to my home TrueNAS serversyncthing to back it up to my TrueNAS home server. I'm currently running Ubuntu 24.04.4 LTS, but no doubt these instructions will work on newer Ubuntu distributions going forward.

Running and Managing SyncthingSyncthing runs per user rather than as a system-wide service. To start it automatically on boot for your user account.

sudo apt install syncthing

```bash
# Enable the service for your specific username
systemctl --user enable syncthing.service
systemctl --user start syncthing.service
```

Fire up a web browser and to to the following url on your laptop.

Bare in mind the address below is localhost.

[Local laptop URLl](http://localhost:8384)

References

- Syncthing's great [documention pages](https://docs.syncthing.net/index.html)
- Syncthing [Firewall Settings](https://docs.syncthing.net/users/firewall.html)
- Lawrence Systems great [Syncthing Video](https://www.youtube.com/watch?v=se4V-CgO7ZM&vl=en)
