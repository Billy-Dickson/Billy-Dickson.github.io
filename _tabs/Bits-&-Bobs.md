---
layout: default
icon: fas fa fa-external-link
order: 5
---

# Bit & Bobs

I'm going to use this page as a kind of scratch page, to jot down idea’s and partial bit of information, that I’ll use for blog pages going forward, it’s intended to be fairly fluid and thing will be added and removed from the page as and when needed.

## Running

Marathon [World Listing](https://www.goandrace.com/en/marathons-2025-calendar-worldwide.php)

## Home Network

### IT Tools Docker Config

This Docker Compose configuration defines a service named `it-tools` that uses the image `corentinth/it-tools`. Below is a breakdown of the configuration:

#### Docker Compose File

```text
#
# Filename docker-compose.yml
#
services:
  it-tools:
    image: corentinth/it-tools
    networks:
       - blackhole
    container_name: it-tools
    ports: 
    - 8081:80
    restart: unless-stopped

networks:
   blackhole:
    name: blackhole
    external: true
```

#### Network Configuration

- **Network Name**: `blackhole`
- **External**: `true`
  This indicates that the network is not created by this Docker Compose file but is an existing external network.

#### Ports Configuration

In the Docker Compose configuration, the `ports` section is used to define how the container's internal ports are mapped to the host machine's ports. This allows external access to the services running inside the container.

#### Configuration Breakdown

- **Container Port**: `80`
  - This is the port on which the application inside the container is listening. In this case, it is the default HTTP port, which is commonly used for web applications.

- **Host Port**: `8081`
  - This is the port on the host machine that will be mapped to the container's port. When you access `http://localhost:8081` in your web browser, the request will be forwarded to the container's port `80`.

#### Start the container

docker-compose up -d starts the containers in the background and leaves them running. (this means that if you want to see the logs of the containers you will have to use docker-compose logs -f)

```bash
docker-compose up -d
```

## References

- Stack Overflow - [docker-compose](https://stackoverflow.com/questions/52111190/whats-the-difference-between-docker-compose-up-d-and-docker-compose-up-build) command
