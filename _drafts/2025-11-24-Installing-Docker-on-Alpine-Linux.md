---
title: Installing Docker on Alpine Linux
date: 2025-11-24
draft: true
categories: [Homelab, Proxmox, Alpine Linux]
tags: [homelab, proxmox, alpine linux, docker]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2025/2025-11-24-Installing-Docker-on-Alpine-Linux/header.webc
---


To install Docker on Alpine Linux, first update the package index with apk update, then add the Docker package using apk add docker. Finally, start the Docker service and enable it to run at boot with rc-update add docker boot and service docker start.

## References

- Ten Cent Cloud - Install [Docker on Alpine Linux](https://www.tencentcloud.com/techpedia/100542)
- Alpine Wiki - [Installing Docker](https://wiki.alpinelinux.org/wiki/Docker)
- The Linux Code - [Install Docker on Alpine Linux](https://wiki.alpinelinux.org/wiki/Docker)
- Great starting point [installing and setting up Alpine linux](https://cleberg.net/blog/alpine-linux.html)
