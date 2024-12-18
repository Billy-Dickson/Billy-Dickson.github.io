---
title: Cheap Home Proxmox Server
date: 2023-01-12
categories: [Homelab, Proxmox]
tags: [homelab, proxmox, home server]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/2023/2023-01-12-Cheap-Home-Proxmox-Server/proxmox.webp
---

 In January this year (2023) bought myself a cheap PC from AliExpress £157, 16G of memory and a cheap NVME and SSD Drive. Total spend was about £250, with the hope of teaching myself about Proxmox, Virtualization and Docker.

## PC Specification  

| Hardware | Description |
|-----|----|
 | [Firewall Mini PC Pentium N6005 N5105](https://www.aliexpress.com/item/1005003991560461.html?spm=a2g0o.order_list.order_list_main.4.37b51802ONX0sX)  | 4 x Intel i226-V 2.5G Lans 2*DDR4 2*M.2 NVMe AES-NI Home Router PC Network Security Appliance. |
 | [WD Blue 256G NVME](https://www.amazon.co.uk/gp/product/B09HKGGPLR/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1) | M.2 2280 PCIe Gen3 NVMe up to 3300 MB/s read speed |
 | [Samsung 1TB 870 QVO](https://www.amazon.co.uk/gp/product/B089QXQ1TV/ref=ppx_yo_dt_b_asin_title_o02_s00?ie=UTF8&th=1) | 2.5 Inch Internal Solid State (SSD) |
| [2 x Crucial RAM 8GB DDR4](<https://www.amazon.co.uk/gp/product/B08C4Z69LN/ref=ppx_yo_dt_b_asin_title_o08_s00?ie=UTF8&psc=1>) | 3200MHz CL22 (or 2933MHz or 2666MHz) Portable Memory CT8G4SFRA32A |

## Installation Instructions

I've installed Proxmox on the NVME drive and used the SSD drive to store ISO images, snapshots and virtual machines. I've been using it for a number of months now and I've had no problems so far. As far as backups goes, I've setup proxmox to backup my VM's every day at 21:00, just in case things go south.

I've also created a Debian VM which I'm currently running Docker on, thankfully no issues to speak of, which is great as this my homelab, I don't imagine that I'll be running huge numbers of VM's on it. Saying that, at some point in the future, I do intend to upgrade it to something more powerful and energy efficient.

I followed Techo Tims Instructions - [Before I do anything on Proxmox, I do this first...](https://www.youtube.com/watch?v=GoZaMgEgrHw) video, and it work great. Bare in mind that these instructions are written for Proxmox 8.0.4 and are for my consumption, so if your using a newer version, best to make sure the following is still applicable.

Add the following at the bottom of /etc/apt/sources.list

```shell
# Added by Billy Dickson 14/04/2023
# not for production use

deb http://download.proxmox.com/debian bookworm  pve-no-subscription
```

Comment out the following in /etc/apt/source.list.d/pve-enterprise.list

```shell
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

At the the prompt, type the following

```shell
apt-get update
apt dist-upgrade
apt autoremove
```

Reboot proxmox.

```shell
reboot
```

Comment out and add the following in /etc/default/grub

```shell
# GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```

Update the grub bootloader

```shell
upgrade-grub
```

Add the following to /etc/modules

```shell
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```

Reboot proxmox.

```shell
reboot
```

## Optional setting up a Let's Encrypt Certificate

### Prerequisites

1. A public domain name, purchased from a domain register the likes of [Cloudflare](https://www.cloudflare.com/products/registrar/) (my choice) or another reputable [company](https://www.top10.com/hosting/domainhosting-comparison-uk?utm_source=google&kw=purchasing%20a%20domain&c=634648085736&t=search&p=&m=e&adpos=&dev=c&devmod=&mobval=0&network=g&campaignid=18915521216&groupid=151971921508&targetid=kwd-106697532&interest=&physical=9046881&feedid=&a=11181&ts=hi&topic=&test=google_uk_domain&clicktype=&camtype=&gclid=CjwKCAjwmbqoBhAgEiwACIjzEAfKVmB29XPDx9TJojlDq6qPOR0BKBs5-oivYm3M6T3hg7kxunNWVBoCf_QQAvD_BwE).
2. An API key to use from your domain register.
3. A local DNS resolver on your network eg. [Pi Hole](https://pi-hole.net/), [OPNsense](https://opnsense.org/), [Pfsense](https://www.pfsense.org/).
4. A working Proxmox Server

![My Proxmox screen with a certificate](../assets/img/posts/2023/2023-01-12-Cheap-Home-Proxmox-Server/certificate.webp "My Proxmox screen with a valid certificate")

Run through the video in the reference, works great and will gives you a working certificate. Saying that, it only saves you a click. 😊

## Useful bits and bobs

I found a useful script online that shows you the hardware layout of the OMMU groups if you need to pass through some of your hardware to a virtual machine.

```shell
# Script found at https://wiki.archlinux.org/title/PCI_passthrough_via_OVMF
# The following script should allow you to see how your various PCI devices 
# are mapped to IOMMU groups.
# 
# If it does not return anything, you either have not enabled IOMMU support properly. 
# Or your hardware does not support it.
#
# Filename - iommu.sh
#
#!/bin/bash
#

shopt -s nullglob
for g in $(find /sys/kernel/iommu_groups/* -maxdepth 0 -type d | sort -V); do
    echo "IOMMU Group ${g##*/}:"
    for d in $g/devices/*; do
        echo -e "\t$(lspci -nns ${d##*/})"
    done;
done;
```

As you can see from the output, the PC has 4 built in NICS, which will be ideal, if I decide at some point, replace it and repurpose it as a home router.

```plaintext
IOMMU Group 0:
        00:02.0 VGA compatible controller [0300]: Intel Corporation JasperLake [UHD Graphics] [8086:4e61] (rev 01)
IOMMU Group 1:
        00:00.0 Host bridge [0600]: Intel Corporation Device [8086:4e24]
IOMMU Group 2:
        00:14.0 USB controller [0c03]: Intel Corporation Device [8086:4ded] (rev 01)
        00:14.2 RAM memory [0500]: Intel Corporation Device [8086:4def] (rev 01)
        00:14.5 SD Host controller [0805]: Intel Corporation Device [8086:4df8] (rev 01)
IOMMU Group 3:
        00:15.0 Serial bus controller [0c80]: Intel Corporation Serial IO I2C Host Controller [8086:4de8] (rev 01)
        00:15.2 Serial bus controller [0c80]: Intel Corporation Device [8086:4dea] (rev 01)
IOMMU Group 4:
        00:16.0 Communication controller [0780]: Intel Corporation Management Engine Interface [8086:4de0] (rev 01)
IOMMU Group 5:
        00:17.0 SATA controller [0106]: Intel Corporation Device [8086:4dd3] (rev 01)
IOMMU Group 6:
        00:1c.0 PCI bridge [0604]: Intel Corporation Device [8086:4dba] (rev 01)
IOMMU Group 7:
        00:1c.4 PCI bridge [0604]: Intel Corporation Device [8086:4dbc] (rev 01)
IOMMU Group 8:
        00:1c.5 PCI bridge [0604]: Intel Corporation Device [8086:4dbd] (rev 01)
IOMMU Group 9:
        00:1c.6 PCI bridge [0604]: Intel Corporation Device [8086:4dbe] (rev 01)
IOMMU Group 10:
        00:1c.7 PCI bridge [0604]: Intel Corporation Device [8086:4dbf] (rev 01)
IOMMU Group 11:
        00:1f.0 ISA bridge [0601]: Intel Corporation Device [8086:4d87] (rev 01)
        00:1f.3 Audio device [0403]: Intel Corporation Jasper Lake HD Audio [8086:4dc8] (rev 01)
        00:1f.4 SMBus [0c05]: Intel Corporation Jasper Lake SMBus [8086:4da3] (rev 01)
        00:1f.5 Serial bus controller [0c80]: Intel Corporation Jasper Lake SPI Controller [8086:4da4] (rev 01)
IOMMU Group 12:
        01:00.0 Non-Volatile memory controller [0108]: Sandisk Corp WD Blue SN570 NVMe SSD 1TB [15b7:501a]
IOMMU Group 13:
        02:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
IOMMU Group 14:
        03:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
IOMMU Group 15:
        04:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
IOMMU Group 16:
        05:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
```

![My Proxmox screen with 3 running Virtual Machines](../assets/img/posts/2023/2023-01-12-Cheap-Home-Proxmox-Server/PVE.webp) "My Proxmox screen with 3 running Virtual Machines"

## References

- Download [Proxmox Software](https://hometechhacker.com/5-great-proxmox-small-form-factor-hardware-options/)
- Proxmox VE [Documentation](https://pve.proxmox.com/pve-docs/)
- Proxmox [VLAN Networking](https://www.youtube.com/watch?v=zx5LFqyMPMU)
- LevelOneTech  [Forum](https://forum.level1techs.com/)
- HomeTech Hacker - 5 Great [Proxmox Small Form Factor Hardware Alternatives](https://hometechhacker.com/5-great-proxmox-small-form-factor-hardware-options/)
- Beelink GTR5 review  [A mini Ryzen](https://www.tomsguide.com/reviews/beelink-gtr5)
- Techno Tim  [Documentation](https://technotim.live/)
- Proxmox 8.0 [Let's Encrypt Tutorial](https://www.youtube.com/watch?v=CDmklu67nSU) from Trey Does Devops
- Proxmox 8.0 [PCIe Passthrough Tutorial](https://youtu.be/_hOBAGKLQkI?si=CK0bAtgs9L1gtcOI) - Craft Computing
