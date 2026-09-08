---
title: TrueNAS Scale Reverse Proxy with Nginx
date: 2026-09-06
draft: true
categories: [Homelab, Docker, TrueNAS]
tags: [homelab, docker, truenas, nginx]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2025/2025-06-11-Docker-Container-IT-Tools/IT-Tools.webp
---

Nginx Proxy Manager (NPM) provides a web interface to configure the popular web server Nginx as a reverse proxy. It can be used for many purposes, but this guide will describe using it to provide HTTPS/TLS termination for other applications running on your TrueNAS system.

## Requirements

1. You must own or control an actual public domain. That domain doesn't need to be accessible from the public Internet, but it must have public-facing DNS records. This guide will use example.com as a placeholder for this domain.

2. You do not need to open or forward any ports on your router, or otherwise make anything on your LAN accessible to the Internet. You can if you choose, but that is outside the scope of this guide and I wouldn't recommend it.

3. To follow this guide exactly, you must host your domain's DNS at Cloudflare. Cloudflare provides free DNS hosting, among many other services (not all of which are free).
    * Your DNS provider matters because we're going to be obtaining certificates from Let's Encrypt, and that requires they validate your control over your domain. In order to do that without exposing your apps to the public, NPM will need to be able to make updates to your DNS records.
    * NPM supports many other DNS hosts, and you should be able to adapt this guide to them, but this guide will be using Cloudflare.You've generated an API Token from Cloudflare that you'll use to allow NPM to make these changes.

4. You've generated an [API Token](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/) from Cloudflare that you'll use to allow NPM to make these changes.
   * This token must have permissions of _Zone:Zone:Read_ and _Zone:DNS:Edit_.

5. You can control your local DNS, making custom entries to point names of your choosing toward IP addresses on your LAN.
   * You can use router software like OPNsense or pfSense for this, use a local DNS server like Pi-Hole, or any other means (I use a [Unifi Cloud Gateway Ultra](https://lazyadmin.nl/network/unifi-cloud-gateway-ultra/)).
   * This is required because the certificate you're going to generate will cover names, not IP addresses. Browsing by IP (even if it were possible) would give you certificate errors.
   * This is also required because NPM will use the requested hostname to determine the proxy target. Browsing to the IP address will just give you the default NPM welcome page, not the app you're looking for.
   * As a distinctly suboptimal alternative, you can put entries pointing to your LAN devices in public DNS.


   The NAS now has two IP addresses, and the web UI is only using on of them (.99), the other one (.50) is available for apps to use.

## Install NPM

When installing NPM the Network Configuration section has a "Host IPs" section for each port, with an Add button for each one.

For the WebUI port (30020) you can leave this as is.

For the HTTP port and HTTPS ports you change the port numbers to 80 and 443 as described in this guide. Then for each one you click the Add button which will show a drop-down list of the IP addresses available on the NAS, including the alias added earlier.

Choose the alias (in my case, 192.168.2.50), then continue configuration per this guide.

NPM is now available on ports 80 and 443 on the aliased address instead of the NAS' primary address.

## References

* Jose G Perez - [Blog](https://josegperez.com/blog/truenas-scale-nginx/)
* Dan's - [Wiki](https://wiki.familybrown.org/en/fester/configure-apps/other/npm)