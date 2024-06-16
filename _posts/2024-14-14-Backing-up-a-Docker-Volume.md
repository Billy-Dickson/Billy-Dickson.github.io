---
title: Backing up a Docker Volume
date: 2024-06-14
categories: [Homelab, Docker]
tags: [homelab, docker]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/headers/docker-introduction-01.jpg
---

This is a quick and dirty approach to occasionally backing up a docker volume and the docker compose file to another machine.

I've done this a number of times with no problems, all you need to do is copy the docker compose file and the tarred volume backup to you new machine, then untar the volume backup to the appropriate directory and re-create the docker container.

## How to back up a docker volume

Stop the docker volume you want to back up

```bash
docker stop <docker-name>
```

su to root (assuming your using Debian)

```bash
su
```

su to root (assuming your using Ubuntu and in the sudo group)

```bash
sudo -i
```

I'm going assume that you've installed the volume in the default location, change to suite your needs.

```bash
cd /var/lib/docker/volumes/
```

Create a tar file of the volume

```bash
tar -cf <tarfilename.tar> <volume-directory>
```

Move the tar file to a normal your non root directory (home directory)

```bash
mv /home/username/<your backup directory>
```

## Move you volume to another server (Optional)

Change the owner and scp it to another machine

```bash
chown billy:billy <tarfilename.tar>
scp <tarfilename.tar> billy@myothermachine:/home/billy
```

## Reference

* Bacula Systems - [Docker Volume Backup and Restore](https://www.baculasystems.com/blog/docker-backup-containers/)  
* Howto Geek - [How to Backup you Docker Volume](https://www.howtogeek.com/devops/how-to-back-up-your-docker-volumes/#:~:text=Docker%20doesn't%20have%20a,data%20to%20your%20backup%20destination.&text=to%20deposit%20an%20archive%20of%20the%20volume's%20content%20into%20your%20working%20directory.&text=flag)
