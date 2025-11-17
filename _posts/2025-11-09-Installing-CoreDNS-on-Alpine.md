---
title: Installing CoreDNS on Alpine Linux
date: 2025-11-09
categories: [Homelab, Proxmox, Alpine Linux]
tags: [homelab, proxmox, alpine linux]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/header.webp
---

Having recently installed CoreDNS in a [docker container](https://thebloody.cloud/posts/Docker-CoreDNS/), I though that I would also try to install it on [Alpine Linux](https://www.alpinelinux.org/) in a Proxmox Virtual Machine, I might move it to proper hardware at some point, we will see. Saying that, it's working incredibly well and has a really small memory footprint, even with CoreDNS installed and setup.

The virtual image I'm using works great in my homelab, but if I was going to be using it outside or in a externally hosted scenario, I would definately install and configure auditing.

![Memory Footprint](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/Memory-Usage.webp)

Below are the setting's I'm using for my container, do feel free to use them as a template. I'm using the **Virtual X86_64** version downloadable from [this page](https://alpinelinux.org/downloads/)

## Proxmox Settings

### General Settings

![General Settings](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/General.webp)_General Settings_

### OS Settings

![OS](../assets/img/posts/2025-11-09-Installing-CoreDNS-on-Alpine/OS.webp)_OS Settings_

### Disk

![Disk](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/Disk.webp)_Disk_

### CPU

![CPU](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/CPU.webp)_CPU_

### Memory

![Memory](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/Memory.webp)_Memory_

### Network

This will probably be different for you, this docker container is resident on VLAN 20 on my class C private network 192.168.20.0/24

![Network](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/Network.webp)_Network_

### Hardware

A screenshot of my hardware, your hardware will be quite similar, but no doubt you'll change it to suite your own circumstances.

![Hardware](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/Hardware.webp)_Hardware_

## Initial Setup

Start the container and Login as root with no password then follow the following instuctions to install.

1. Type: setup-alpine
2. Select Keyboard layout: [none] gb
3. Select variant (or `abort`): gb
4. Enter System hostname (fully qualified form, e.g. `foo.example.org`) [localhost] ns1.local.lan
5. Available interfaces are: eth0
   Enter '?' for help on bridges, bonding and vlans.
6. Which on do you want to initialize? (or '?' or 'done') [eth0]
7. Ip address for eth0? (or 'dhcp', 'none' '?') [dhcp]
8. Do you want to do any manual network configuration? (y/n) (n)
9. Root password
10. Timezone GB
11. Which timezone are you in? (or '?' or 'none') [UTC] GB
12. Proxy none
13. PK Mirror
    Enter mirror number of URL: [1]
14. Setup a user? (enter a lower-case loginname, or 'no') billy
15. Full Name for user billy (billy) Billy Dickson
16. New Password
17. Retype Password
18. Enter ssh key or URL for billy (or `'none') [none]
19. Which ssh server? 'openssh', 'dropbear' or 'none') [openssh]
20. Which disk(s) would you like to use (or ? for help or none) [none] sda
21. How would you like to use it? ('sys', 'data', 'crypt', 'lvm' or '?' for help) sys
22. Warning: Erase the above disk(s) and continue (y/n) [n] y

## Configuring Alpine

### Update Alpine

Login as root, update the disto

```bash
apk update && apk upgrade
```

### Installing nano

```bash
apk add nano
```

### Install fastfetch (optional)

```bash
apk add fastfetch
```

![Fastfetch](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/fastfetch.webp)

### Adding an admin user

```bash
adduser <username> wheel
```

### Installing doas

**doas** is a simplified and lightweight utility that provides a way to execute commands as another user.

```bash
apk add doas
```

Configuration in the default config file /etc/doas.conf may be overridden by /etc/doas.d/*.conf if files exist.

To allow the members of the wheel group to use root privileges with doas command, a config file /etc/doas.d/20-wheel.conf can be created as follows:

```bash
nano /etc/doas.d/20-wheel.conf
```

Add the following:

```bash
permit persist  :wheel
```

### Installing the QEMU tools

Installing the QEMU tools to manage the Alpine Guest OS, if you're running it on a [Proxmox](https://www.proxmox.com) hypervisor.

Edit the repositor and enable community

```bash
doas nano /etc/apk/repositories
```

Install the qemu guest agent

```bash
doas apk add qemu-guest-agent
```

Make it presistant, enable it on reboot

```bash
doas rc-update add qemu-guest-agent
```

## Securing Alpine Linux

### Enabling automatic updates

Based on an excellent article by [Isaac Bythewood](https://blog.bythewood.me/posts/minimal-automated-updates-for-alpine-linux/) we're going to create a bash script that will be run by a cron job every night at 2:00 in the morning, that checks for updates and applies them.

Change directory to **/etc/periodic/daily/**

```bash
cd /etc/periodic/daily
```

Create a file called apk-autoupgrade

```bash
doas nano apk-autoupgrade
```

Add the following

```bash
apk upgrade --update | sed "s/^/[`date`] /" >> /var/log/apk-autoupgrade.log"
```

Tighten up the permissions to allow the script to run

```bash
doas chmod 700 /etc/periodic/daily/apk-autoupgrade
```

### Installing and setting up UFW Firewall

UFW stands for Uncomplicated Firewall, and is a program for managing a netfilter firewall. It provides a command line interface and aims to be uncomplicated and easy to use. I'm using the instructions provided by the [alpinelinux wiki](https://wiki.alpinelinux.org/wiki/Uncomplicated_Firewall) to install and configure to my needs.

Check to make sure that the community repository is uncommented.

```bash
doas nano /etc/apk/repositories
```

```bash
#/media/cdrom/apks
http://dl-cdn.alpinelinux.org/alpine/v3.22/main
http://dl-cdn.alpinelinux.org/alpine/v3.22/community
```

If it's the same as above, all is well, you can now install the package.

```bash
doas apk add ip6tables iptables ufw
```

Reboot the VM, then continue with the rest of the instructions.

```bash
doas reboot
```

Some basic setup before enabling the firewall, you don't want to lock yourself out.

```bash
doas ufw default deny incoming
doas ufw default allow outgoing
```

Open SSH port and protect against brute-force login attacks

```bash
doas ufw limit SSH
```

Allow inbound NTP

```bash
doas ufw allow in ntp
```

Allow inbound DNS

```bash
doas ufw allow in dns
```

Allow inbound DNS over TLS

```bash
doeas ufw allow in 853/tcp
```

Enable the firewall

```bash
doas ufw enable
```

Enable the UFW startup init script

```bash
doas rc-update add ufw
```

A list firewall rules.

![Firewall List](../assets/img/posts/2025/2025-11-09-Installing-CoreDNS-on-Alpine/ufw-status-numbered.webp)

## Installing CoreDNS

Why bother? In the UK we have what's know affectionately as the [snoopers charter](https://www.libertyhumanrights.org.uk/fundamental/mass-surveillance-snoopers-charter/)

DNS queries are sent in plaintext, which means anyone can read them. DNS over HTTPS and DNS over TLS encrypt DNS queries and responses to keep user browsing secure and private. However, both approaches have their pros and cons.

```bash
doas apk add coredns
```

Always good to have the DNS tools installed as well, for testing.

```bash
doas apk add bind-tools
```

Create a CoreDNS config

```bash
doas nano /etc/coredns/Corefile
```

This is my Corefile below, I'm forwarding my internal requests (forward and reverse) to my home router to resolve.  

My CoreDNS install only handles external queries, forwarding them to Quad 9 and Cloudflare using [DOT (DNS over TLS)](https://www.cloudflare.com/en-gb/learning/dns/dns-over-tls/).

```yaml
# Authoritative zone for linuxhome.co.uk
# https://www.ibm.com/think/topics/dns-records

# define a snippet
(snip) {
    acl {
        allow net 127.0.0.0/8 
        allow net 192.168.0.0/16 
        allow net 172.16.0.0/20 
        allow net 10.0.0.0/8
        block
    }
    whoami
    log
    errors }

example.com:53 {
    forward . 192.168.20.1
    import snip 
}

# Reverse zone for linuxhome.co.uk
20.168.192.in-addr.arpa:53 {
    forward . 192.168.20.1
    import snip
}

# Reverse zone for linuxhome.co.uk
69.16.172.in-addr.arpa:53 {
    forward . 192.168.20.1
    import snip 
}

# https://coredns.io/plugins/forward/
.:53 {
    # Quad 9 DOT
    forward . 127.0.0.1:5301 
    # Cloudflare DOT
    forward . 127.0.0.1:5302
#   Google DOT 
#   forward . 127.0.0.1:5303
    import snip 
}

# Quad 9 DOT 
.:5301 {
    bind lo
    forward . tls://9.9.9.9 tls://149.112.112.112 {
    tls_servername dns.quad9.net }
    import snip
}

# Cloudflare DOT
.:5302 {
    bind lo
    forward . tls://1.1.1.1 tls://1.0.0.1 {
    tls_servername cloudflare-dns.com }
    import snip 
}

# Google DOT
# .:5303 {
#    bind lo
#    forward . tls://8.8.8.8 {
#    tls_servername dns.google.com }
#    import snip
# }

    policy sequential
    cache 3600
    health localhost:8091 {
        lameduck 1s 
}
    dnssec
    reload

#    If your running Promethus to collect stats, do feel free to uncomment
#    the command below.
#    prometheus :9153
```

### Using CoreDNS on the system

Now that we have our DNS server, let's use it on our server!

If you use DHCP to get the ip address of your server, the DNS will always be used from the DHCP.

```bash
doas nano /etc/udhcpc/udhcpc.conf
```

Uncomment RESOLV_CONF="no"

```bash
# Do not overwrite /etc/resolv.conf
RESOLV_CONF="no"

# Use alternative path for resolv.conf
#RESOLV_CONF="/tmp/resolv.conf"

# Prevent overwriting of resolv.conf on a per-interface basis
#NO_DNS="eth1 wlan1"

# List of interfaces where DHCP routes are ignored
#NO_GATEWAY="eth1 wlan1"
```

Then edit resolv.conf

```bash
doas nano /etc/resolv.conf
```

You might find that nameserver is already populated with your router assigned DNS server, change it to the following.

```bash
nameserver 127.0.0.1
```

### Startup Script

Add at startup.

CoreDNS already has a service!

```bash
doas rc-update add coredns
```

Show services at startup

```bash
doas rc-status
```

For info only (howto remove service at startup)

```bash
doas rc-update del coredns
```

Start the service

```bash
doas rc-service coredns start
```

## References

- How to install [Alpine Linux on Proxmox](https://wiki.alpinelinux.org/wiki/Installing_Alpine_in_a_virtual_machine)
- How to prepare [Alpine Linux with Cloud init for Proxmox](https://5wire.co.uk/how-to-prepare-alpine-linux-image-with-cloud-init-ready-for-proxmox/)
- Self Hosting [CoreDNS on Alpine Linux](https://www.ipv6.rs/tutorial/Alpine_Linux_Latest/CoreDNS/)
- Installing [QEMU guest in Alpine](https://wiki.alpinelinux.org/wiki/Install_Alpine_in_QEMU#Create_the_Virtual_Machine)
- Setup CoreDNS on Alpine Linux - [philippeloncaux.com](https://philippeloctaux.com/blog/coredns-alpine/)
- Install and configure CoreDNS - [de-marco.net](https://di-marco.net/blog/it/2024-05-09-Intall_and_configure_coredns/)
- Blog.bythewood.me - [minimal automated update for alpine](https://blog.bythewood.me/posts/minimal-automated-updates-for-alpine-linux/)
- Just Serendipity - [apk autoupdate on Alpine](https://perrotta.dev/2024/08/apk-autoupdate-on-alpine-linux/)
- Philip Peloctaux - [Coredns-Alpine](https://philippeloctaux.com/blog/coredns-alpine/)
- Securing Alpine Linux - [Wiki](https://wiki.alpinelinux.org/wiki/Securing_Alpine_Linux)
- ICANN - [Non Routable Domain Names](https://www.icann.org/en/board-activities-and-meetings/materials/approved-resolutions-special-meeting-of-the-icann-board-29-07-2024-en#section2.a)
- SomeGuyandhistmac - [Coredns Docker and Multihosts](https://someguyandhismac.com/posts/corends-docker-multihosts/)
- European alternatives to US DNS resolvers - [Website](https://european-alternatives.eu/alternative-to/opendns)
- Setting up an [Alpine Linux workstation](https://whynothugo.nl/journal/2023/11/19/setting-up-an-alpine-linux-workstation/)
