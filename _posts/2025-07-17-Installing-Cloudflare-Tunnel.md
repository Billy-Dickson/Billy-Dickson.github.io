---
title: Installing a Cloudflared Docker Container
date: 2025-07-17
categories: [Homelab, Docker]
tags: [homelab, docker]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2025/2025-07-17-Installing-Cloudflare-Tunnel/Cloudflare_Tunnel.webp
---

Using Cloudflared in my homelab setup has provided me with several advantages, particularly in terms of security, accessibility, and performance. The most salient reason are listed [below](#why-use-cloudflare-tunnels), the main reason for me, is that it's currently **free.**

## Prerequisite

1. Running docker host on either bare metal or in a [Virtual Environment](https://thebloody.cloud/posts/Cheap-Home-Proxmox-Server/).
2. [Cloudflare registered](https://www.cloudflare.com/en-gb/products/registrar/) domain.
3. Feel free to use Jonas Merkle's github instructions in [this](https://github.com/jonas-merkle/container-cloudflare-tunnel) link.
4. Below is a video by Tom Lawrence on how to setup Cloudflare Tunnels.

{% include embed/youtube.html id='eojWaJQvqiw' %}

## My Docker Compose File

Below is my setup for completeness, it does contain settings specific to me, so I would be inclined to read the github link provided above for a starting point.

```yaml
# Filename docker-compose.yml
#
# Adapted from the work by Jonas Merkly link below, this works on my docker seteup.
# I would be inclined to goto his github repository and uses his work,
# remember to *Star* his work.
#
# https://github.com/jonas-merkle/container-cloudflare-tunnel

services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    networks:
      - blackhole
    container_name: cloudflare-tunnel
    restart: unless-stopped
    command: tunnel run

# Container labels for additional metadata
    labels:

# If you use Watchtower to update your containers then great.
# Othewise fee free to delete the lable below.
# Currently set to disable watchtower updates
      - "com.centurylinklabs.watchtower.enable=false"  

# Pass the Cloudflare Tunnel token from the  environment variable. 
# This is stored in a file called .env
    environment:
      - "TUNNEL_TOKEN=${CLOUDFLARE_TUNNEL_TOKEN}"
      - TZ=Europe/London

# Health check configuration to verify Cloudflare Tunnel readiness
    healthcheck:
      test: ["CMD", "cloudflared", "--version"] # Check if cloudflared version command works
      interval: 30s                             # Time between health check attempts
      timeout: 10s                              # Time to wait for a response
      retries: 3                                # Number of retries before marking as unhealthy
      start_period: 10s                         # Delay before health checks begin

networks:
  blackhole:
   name: blackhole
   external: true

```

Below is the format of my .env file which would reside in the same directory as the docker-compose.yml file above.

```yaml
#
# Cloudflare Tunnel Token (replace with your actual token)
CLOUDFLARE_TUNNEL_TOKEN='SomeRandomStringCombination'

```

## Why use Cloudflare Tunnels

### 1. Secure Remote Access

- **Zero Trust Security**: With Cloudflared, I can implement a Zero Trust security model, which means that access to my services is controlled and authenticated without relying on traditional VPNs.
- **TLS Encryption**: It provides secure connections using TLS, ensuring that the data transmitted between my devices and the cloud is encrypted.

### 2. Easy Access to Services

- **Cloudflare Tunnel**: I can create secure tunnels to my services running in the homelab, making them accessible over the internet without exposing my IP address or opening ports on my router.
- **Subdomain Management**: Managing subdomains for my services is straightforward, allowing for organized access (e.g., service1.mydomain.com).

### 2. DDoS Protection

- **Built-in Protection**: Cloudflare offers DDoS protection, which helps safeguard my homelab services from denial-of-service attacks, ensuring better uptime and reliability.

### 4. Performance Optimization

- **Content Delivery Network (CDN)**: By routing traffic through Cloudflare, I can take advantage of their CDN, which caches content and reduces latency for users accessing my services from different geographical locations.

- **Load Balancing**: Cloudflare helps distribute traffic across multiple instances of my services, improving performance and reliability.

### 5. Simplified DNS Management

- **Dynamic DNS**: Cloudflared helps me manage dynamic DNS updates, making it easier to access my homelab services even if my public IP address changes frequently.

### 6. Cost-Effective

- **Free Tier**: Cloudflare offers a free tier that includes many of the features I need, making it a cost-effective solution for my homelab.

### 7. Integration with Other Cloudflare Services

- **Firewall Rules**: I can set up firewall rules to control access to my services based on various criteria, enhancing security.

- **Analytics**: Cloudflare provides analytics on traffic and security events, giving me insights into how my services are being accessed and any potential threats.

## Reference

- Cloudflare blog - Setting up a [Cloudflare Tunnel](https://blog.cloudflare.com/ridiculously-easy-to-use-tunnels/)
- Cloudflare Tunnel [Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
- Cloudflare [Registered Domain](https://www.cloudflare.com/en-gb/products/registrar/)
- Jonas Merkly excellent cloudflared [github repository](https://github.com/jonas-merkle/container-cloudflare-tunnel).
