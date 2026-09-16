---
title: Moving from Overseerr to Seerr
date: 2026-09-11
categories: [Homelab, Docker]
draft: false
tags: [homelab, docker, seerr]     # TAG names should always be lowercase
image: 
   path: ../assets/img/posts/2026/2026-09-11-Moving-from-Overseerr-to-Seerr/preview.webp
---
I'm moving from Overseer to Seer, it looks like Overseerr if being depricated so I may as well move sooner rather that later, I'm also the process of moving from Plex to Jellyfin for home viewing (Plex is getting worse, [enshitification](https://www.cloudfest.com/blog/what-is-enshittification-cory-doctorow-cloudfest-keynote) at is best).

## What is Seer

Seerr (often spelled "seer") on Docker is a free, open-source application used to manage media requests and discovery for self-hosted home media server.

This docker container is running on my cheap [Chinese proxmox server](https://thebloody.cloud/posts/Cheap-Home-Proxmox-Server/) which I don't intend to upgrade any time soon because of [AI and memory prices](https://www.jpmorgan.com/insights/global-research/artificial-intelligence/dram-memory-shortage-from-ai).

I'll keep [Plex](https://watch.plex.tv/en-GB/me) for as long as Plexamp keeps working, I do love listening to music while I commute from home to work.

> We're excited to announce a major update: the Jellyseerr and Overseerr teams are officially merging into a single team called Seerr. This unification marks an important step forward as we bring our efforts together under one banner.
>
> For users, this means one shared codebase combining all existing Overseerr functionalities with the latest Jellyseerr features, along with Jellyfin and Emby support, allowing us to deliver updates more efficiently and keep the project moving forward.
>
> Please check how to migrate to Seerr in our migration guide and stay tuned for more updates on the project!

## Setup

> * The container runs as user node (UID 1000) by default
> * Ensure the config directory is owned by UID 1000:1000:
Command below
{: .prompt-info }

```bash
mkdir -p ./seerr/config
sudo chown -R 1000:1000 ./seerr/config
```

Below is my `docker-config.yml` file for completeness and as an aid to my own memory. Feel free to copy and/or change to suit your circumstances. I've commented out a couple of things that I'm using in my own home lab.

```yaml
services:
  seerr:
    image: ghcr.io/seerr-team/seerr:latest
#    networks:
#      - blackhole
    init: true
    container_name: seerr
    environment:
      - LOG_LEVEL=debug
      - TZ=Europe/London
      - PORT=5055
    ports:
      - 5055:5055
    volumes:
      - ./seerr/config:/app/config
    healthcheck:
      test: wget --no-verbose --tries=1 --spider http://localhost:5055/api/v1/settings/public || exit 1
      start_period: 20s
      timeout: 3s
      interval: 15s
      retries: 3
    security_opt:
      - no-new-privileges
    cap_drop:
      - ALL
    restart: unless-stopped

volumes:
   seerr-data:
      external: true

#networks:
#   blackhole:
#      name: blackhole
#      external: true
```

As an addition to the instructions above, I'm also running caddy as a reverse proxy at home, the link for setting that up is [here](https://thebloody.cloud/posts/Installing-Caddy-Docker-Container/).

## References

* Seerr - [Installation](https://jellywatch.app/blog/seerr-installation-guide-docker-linux-unraid-windows-2026)
* Overseerr and [Seerr](https://docs.seerr.dev/blog/seerr-release/)
* Installing - [Caddy for Home](https://thebloody.cloud/posts/Installing-Caddy-Docker-Container/)
