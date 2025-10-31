---
title: Installing CoreDNS in a docker container
date: 2025-09-08
categories: [Homelab, Docker]
tags: [homelab, docker]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2025/2025-09-08-Docker-CoreDNS/header.webp
---

I've just bought  a [Ubiquiti Cloud Gateway Ultra](https://www.amazon.com/Ubiquiti-Cloud-Gateway-Ultra-UCG-Ultra/dp/B0CWLKD9RP) to replace my 10 year old [pfsense](https://www.pfsense.org/) appliance which has been great, but understandably the hardware was starting to show it’s age, and as I already have Unifi Access points in my house, so it made sense to go the whole hog and settle on the one platform.

 Yes the Unifi Gateway Ultra does have DOH (DNS over HTTPS), but if I decide at some point to change my router, its another thing that I wouldn't have to think about, that and I do like leaning new things. So I did a bit of research and decided to spin up a docker container running CoreDNS to do name resolution for my bit of the home network, I created my own VLAN many years ago so that I can mess about, and not affect the rest of the house. My better looking half works from home and needs things to just work, and she didn't need a kaos monkey (like me) breaking things.

I have bought a domain name from Cloudflare that I use internally, but for brevity and security, I've replaced it in the documentation with (internal). I did use chatGPT initially, but I was finding that some of the answers and syntax provided in the answers was just plain wrong, so in the end I just ended up reading the documentation (which is surprisingly good) and combined it with some good tech blogs on the subject. As you can see below, I'm using a docker container, this is running on a Debian Host which in turn, is running on a [Proxmox](https://pve.proxmox.com/wiki/Main_Page) hypervisor.

1. Cheap Home [Proxmox Server](https://thebloody.cloud/posts/Cheap-Home-Proxmox-Server/)
2. Building a Debian host on [Proxmox](https://thebloody.cloud/posts/Debian-Host-On-Proxmox/) with a docker installed.

![Network Diagram](../assets/img/posts/2025/2025-09-08-Docker-CoreDNS/Network_Diagram.svg)_Network Diagram_

## Directory Structure

Although I don't use the forward or reverse DNS lookup on his setup, I did use it when testing. So I've left it in for completeness.

```text
├── Corefile
├── docker-compose.yml
└── zones
    ├── 172.16.69.zone
    ├── 192.168.20.zone
    ├── 192.168.30.zone
    └── internal.zone
```

## Corefile

```yaml
# Authoritative zone for home.lan
# https://www.ibm.com/think/topics/dns-records
example.com:53 {
    forward . 192.168.20.1
    log
    errors
}

# Reverse zone for example.com
20.168.192.in-addr.arpa:53 {
    forward . 192.168.20.1
    log
    errors
}

# Reverse zone for example.com
69.16.172.in-addr.arpa:53 {
    forward . 192.168.20.1
    log
    errors
}

# https://coredns.io/plugins/forward/
.:53 {
    forward . 127.0.0.1:5301 127.0.0.1:5302
    log
    errors
}

# Quad 9 DOT 
.:5301 {
    forward . tls://9.9.9.9 tls://149.112.112.112 {
    tls_servername dns.quad9.net }
    log
    errors
}

# Cloudflare DOT
.:5302 {
    forward . tls://1.1.1.1 tls://1.0.0.1 {
    tls_servername cloudflare-dns.com }
    log
    errors
}

    policy sequential
    cache 3600
    health localhost:8091 {
        lameduck 1s
    }
    dnssec
    reload
    prometheus :9153
```

## Docker Compose

 ```yaml
 services:
  coredns:
     image: coredns/coredns:latest
     networks:
      - blackhole
     container_name: coredns
     restart: always
     ports:
      - "53:53/udp"
      - "53:53/tcp"
      - "9153:9153"  # Prometheus metrics
     volumes:
      - ./Corefile:/Corefile:ro
      - ./zones:/zones:ro
     command: -conf /Corefile

networks:
   blackhole:
      name: blackhole
      external: true
 ```

### Detailed Breakdown

| Configuration Aspect | Details | Explanation |
|---------------------|---------|-------------|
| **Image** | `coredns/coredns:latest` | Uses the official CoreDNS Docker image with the most recent version |
| **Network** | `blackhole` (external) | Connects the container to a pre-existing Docker network |
| **Container Name** | `coredns` | Sets a specific name for the Docker container |
| **Restart Policy** | `always` | Automatically restarts the container if it stops |

### Port Mappings

- **53/UDP**: Standard DNS UDP port
- **53/TCP**: Standard DNS TCP port
- **9153**: Prometheus metrics endpoint

### Volume Mounts

1. `./Corefile:/Corefile:ro`
   - Mounts local CoreDNS configuration file
   - Read-only access

2. `./zones:/zones:ro`
   - Mounts local DNS zones directory
   - Read-only access

### Startup Command

```bash
command: -conf /Corefile
```

- Specifies the configuration file to use when launching CoreDNS

## Key Features

- **High Availability**: Always restart policy
- **Custom Configuration**: External Corefile and zones
- **Monitoring**: Prometheus metrics endpoint
- **Secure**: Read-only volume mounts

## References

- XDA - [Stop using your ISP's DNS](https://www.xda-developers.com/please-stop-using-your-isps-dns/)
- Unifi Cloud Gateway Ultra - [Review](https://lazyadmin.nl/network/unifi-cloud-gateway-ultra/)
- Some Guy and [his Mac](https://www.someguyandhismac.com/posts/corends-docker-multihosts/)
- Ben Soer - [Setup a prive homelald DNS Server](https://medium.com/@bensoer/setup-a-private-homelab-dns-server-using-coredns-and-docker-edcfdded841a)
- CoreDNS - [Manual](https://coredns.io/manual/toc/)
- CoreDNS - [Docker Hub](https://hub.docker.com/r/coredns/coredns/)
- CoreDND -  [Website](https://coredns.io/)
- ICANN - [Non Routable Domain Names](https://www.icann.org/en/board-activities-and-meetings/materials/approved-resolutions-special-meeting-of-the-icann-board-29-07-2024-en#section2.a)
- SomeGuyandhistmac - [Coredns Docker and Multihosts](https://someguyandhismac.com/posts/corends-docker-multihosts/)
