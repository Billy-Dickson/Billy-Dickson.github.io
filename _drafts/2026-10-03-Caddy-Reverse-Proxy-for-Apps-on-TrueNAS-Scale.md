---
title: Caddy Reverse Proxy for Apps on TrueNAS Scale
date: 2026-10-03
draft: true
categories: [Homelab, Docker, TrueNAS, Caddy]
tags: [homelab, docker, truenas, caddy]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2026/2026-10-03-Caddy-Reverse-Proxy-for-Apps-on-TrueNAS-Scale/header.webp
---

We have a bit of work to do before we install Caddy as an app, see the diagram below that give you a visualisation of what we are trying to do. Do bare in mind that the IP addresses referenced below are what my network uses, yours will mostly likely be different so don't follow the instructions blindly.

## Prerequisites

- A working knowledge of networking and linux
- The ability to change your home networks DHCP and DNS settings.

![TrueNAS Diagram](../assets/img/posts/2026/2026-10-03-Caddy-Reverse-Proxy-for-Apps-on-TrueNAS-Scale/TrueNAS-Diagram.webp)

## Resolve port conflicts and create a specific IP for apps

### 1. Resolve Port 80/443 Conflicts

By default the TrueNAS web GUI claims ports 80 and 443 on IP address [0.0.0.0](https://superuser.com/questions/949428/whats-the-difference-between-127-0-0-1-and-0-0-0-0). Because Caddy must bind to these ports to enable administration and provisioning, you will have to change the IP address, to the IP address of your TrueNAS Server, then you will need to create another IP address on the same network to be used by Caddy.

1. Navigate to **System Settings** > **General Settings** > **GUI**.
2. Change it from 0.0.0.0 to the IP address of your TrueNAS GUI.
![](../assets/img/posts/2026/2026-10-03-Caddy-Reverse-Proxy-for-Apps-on-TrueNAS-Scale/GUI_IP_Address.webp)
