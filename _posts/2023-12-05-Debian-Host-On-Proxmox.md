---
title: Building a Debian host on Proxmox
date: 2023-12-05
categories: [Homelab, Proxmox, Debian Host]
tags: [homelab, proxmox]     # TAG names should always be lowercase
image:
   path: ../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Installed_Applications.webp
---

## Prerequisites

1. A running [Proxmox](https://www.proxmox.com/en/downloads) install, see my [Proxmox-Build](https://thebloody.cloud/posts/Cheap-Home-Proxmox-Server/index.html)
2. A downloaded debian net install from the debian site [here](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/).

Select the following when running through the startup wizard.

| **General**  |  |  
|--|--|
| **Node:**  | (Whatever that happens to be)  |
| **VM ID:** | (Pick your number) |
| **Machine:** | Debian-Server  |
| **Start at boot** | Dependent on your needs |

![General Settings](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/General.webp)

| **OS** | |
|--|--|
| **Use CD/DVD disc image file (iso)**| |
| **Storage:** | (where you saved your ISO) |
| **ISO Image:** | debian-12.1.0-amd64-netinst.iso |  
| **Guest OS**# | |
| **Type:**|    Linux |
| **Version:**| 6.x - 2.6 Kernel |

![OS](/assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/OS.webp)

| **Disks** | |
|--|--|
| **Bus/Device:** | SCSI |
| **SCSI Controller:** | VirtIO SCSI Single |
| **Storage:** | (Select your storage pool) |
| **Disk Size (GiB):** | 32 (This is up to you) |
| **Format:** | QEMU image format (qcow2) |
| **Cache:** | Default (No Cache) |
| **Discard:** | Default unticked |
| **IO thread:** | Default ticked |
| **Backup:** | Default ticked (This is up to you) |

![Virtual Disk Setup](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Disks.webp)

| **CPU**| |
|--|--|
| **Socket:** | 1 |
| **Cores:** | 2 |
| **Type:** | x86-64-v2-AES |

![CPU Type](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/CPU.webp)

| **Memory** | |
|--|--|
| **Memory (MiB):** | 2048 (Workload Dependent) |
| **Minimum memory (MB)** | 2048 |
| **Balloning** | Default ticked (If a mimumum is specified, them the allocation will vary between minimum and maximum when required)|

![Memory Allocation](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Network.webp)

| **Network** | |
|--|--|
| **Bridge:** | vrbr1 (Yours may be different) |
| **VLAN Tag:** | Default no VLANS |
| **Firewall:** | Default tick |
| **Model:** | VirtIO (paravirtualized) |
| **MAC Address:** | auto |

![Network Settings](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Network.webp)
**Confirm**

![Confirm](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Confirm.webp)

Run through the installation until you get to this screen. Just press the continue button and continue, this will install the sudo package and add next account to that group.

![Root Password](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Root_Password.webp)

With regards to server installations, these are the ones that I would normally choose as a base installation.

![Pick applications](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Installed_Applications.webp)

### Install and setup Automatic Updates

The purpose of unattended-upgrades is to keep the computer current with the latest security (and other) updates automatically. If you plan to use it, you should have some means to monitor your systems, such as installing the apt-listchanges package and configuring it to send you emails about updates.

1. Install it running the following command

   ```bash
   sudo apt install unattended-upgrades
   ```

2. After the installation is complete, you can enable and start the unattended-upgrades service by running the following command.

   ```bash
   sudo systemctl enable unattended-upgrades
   sudo systemctl start unattended-upgrades
   ```

   This ensures that the service runs on system startup and is persistent throughout reboots.

3. You now need to make changes to the configuretion file. The default configuration file can be found here at */etc/apt/apt.conf.d/50unattendd-upgrades*. Open it with the text editor of your choice (for me it's nano).

    Note that the unattended-upgrades package ignores line
 that start with **//**, as that line is considered to be a comment.
 *File: /etc/apt/apt.conf.d/50unattended-upgrades*

   ```bash
    Unattended-Upgrade::Origins-Pattern {
    // Codename based matching:
    // This will follow the migration of a release through different
    // archives (e.g. from testing to stable and later oldstable).
    // Software will be the latest available for the named release,
    // but the Debian release itself will not be automatically upgraded.
    // "origin=Debian,codename=${distro_codename}-updates";
    
    // "origin=Debian,codename=${distro_codename}-proposed-updates";
    "origin=Debian,codename=${distro_codename},label=Debian";
    "origin=Debian,codename=${distro_codename},label=Debian-Security";

    // Archive or Suite based matching:
    // Note that this will silently match a different release after
    // migration to the specified archive (e.g. testing becomes the
    // new stable).
    // "o=Debian,a=stable";
    // "o=Debian,a=stable-updates";
    // "o=Debian,a=proposed-updates";
    // "o=Debian Backports,a=${distro_codename}-backports,l=Debian Backports";
    };   
   ```

4. Deleting Dependencies

      You can explicity set up the unattended-upgrades service to remove unused dependencies by changing the *Remove-Unused-Kernel-Packages*, *Remove-New-Unused-Dependencies* and *Remove-Unused-Dependencies* option to true. Remember to remove **//** to uncomment these lines.  

    *File: /etc/apt/apt.conf.d/50unattended.upgrades*

    ```bash
    // Remove unused automatically installed kernel-related packages
    // (kernel images, kernel headers and kernel version locked tools).
    Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";

    // Do automatic removal of newly unused dependencies after the upgrade
    Unattended-Upgrade::Remove-New-Unused-Dependencies "true";

    // Do automatic removal of unused packages after the upgrade
    // (equivalent to apt-get autoremove)
    Unattended-Upgrade::Remove-Unused-Dependencies "true";
   ```

5. Blacklisting Packages

   Not something I'm lookin at doing at the moment, but I'll add it for completess. The *Unattended-Upgrade::Package-Blacklist* section of the configuration file allows you to block upgrades for specific packages.

   To block upgrades for specific packages, add the desired package name to the list, in this example, add "apache2" and "vim":  

   *File: /etc/apt/apt.conf.d/50unattended-upgrades*

   ```bash
    Unattended-Upgrade::Package-Blacklist {
    // The following matches all packages starting with linux-
    //  "linux-";
    "apache2";
    "vim";
    // Use $ to explicitely define the end of a package name. Without
    // the $, "libc6" would match all of them.
    //  "libc6$";
    //  "libc6-dev$";
    //  "libc6-i686$";

    // Special characters need escaping
    //  "libstdc\+\+6$";

    // The following matches packages like xen-system-amd64, xen-utils-4.1,
    // xenstore-utils and libxenstore3.0
    //  "(lib)?xen(store)?";

    // For more information about Python regular expressions, see
    // https://docs.python.org/3/howto/regex.html
    };

    ```

6. Enable Automatic Updates

   To enable automatic updates create a new auto-upgrades  **file: /etc/apt/apt.conf.d/20auto-upgrades** using text editor of your choice, my favorites being vim or nano.

    *File: /etc/apt/apt.conf.d/20auto-upgrades*

   ```bash
    APT::Periodic::Update-Package-Lists "1";
    APT::Periodic::Unattended-Upgrade "1";
    APT::Periodic::AutocleanInterval "7";
   ```

    Update-Package-Lists: 1 enables auto-update, 0 disables.  
    Unattended-Upgrade: 1 enables auto-upgrade, 0 disables.  
    AutocleanInterval: Enables auto clean packages for X days.  
    The above configuration displays 7 days

7. Testing the Configuration  

    You can perform a dry run to test the configuration. The dry run command runs a test update but no actual changes take place.

    ```bash
    sudo unattended-upgrades --dry-run --debug
    ```

### Install and setup UFW firewall

1. Install UFW (Uncomplicated FireWall) on your Debian system.

   ```bash
   sudo apt install ufw
   ```

   ![Install UFW](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Install_UFW.webp)

2. Enable IPv6 support in ufw

   I don't use IPv6 at my work or in my homelab, but at some point I might, so I see no downside to enabling IPv6 just now. It will probably save me some troubleshooting issues further down the line. I think that this might be especially handy if I intend to use these instructions on a cloud Linux instance.

   ```bash
   sudo nano /etc/default/ufw
   ```

      ![Edit UFW IPv6](/assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/UFW_IPv6.webp)

   Ensure that IPv6 is enabled.

   ![IPv6 enabled](/assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/UFW_Config.webp)

      Allow ssh prior to enabling the firewall (just in case it's in the cloud, you don't want to lock yourself out)

   ```bash
   sudo ufw allow ssh
   ```

   Start and enable ufw on boot.

   ```bash
   sudo ufw enable
   ```

   ```bash
   sudo ufw status
   ```

   ![UFW Status](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/Status.webp)

### Install Docker (Optional)

1. Add Docker's official GPG key

   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl gnupg
   sudo install -m 0755 -d /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   sudo chmod a+r /etc/apt/keyrings/docker.gpg
   ```

2. Add the repository to Apt sources

   ```bash
   echo \
     "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
     "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt-get update
   ```

3. To install the latest version, run

   ```bash
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

4. Verify that the installation is successful by running the hello-world image.

   ```bash
    sudo docker run hello-world
   ```

5. The output that you should get.
![Hello World](../assets/img/posts/2023-12-05-Debian-Host-On-Proxmox/hello_world.webp)

6. Running docker as a non root user (add a user to the docker group). After running the command, you will have to log off and on for the groups new membership to be applied

   ``` bash
   sudo usermod -a -G docker $USER
   ```

   At this point you should be able to run docker commands withough the sudo pre-requisite.

   ``` bash
   docker run hello-world
   ```

## References

- Akamai - [Automating Security Updated](https://www.linode.com/docs/guides/how-to-configure-automated-security-updates-debian/)
- Debian Wiki - [Automatic Updates](https://wiki.debian.org/UnattendedUpgrades#:~:text=The%20purpose%20of%20unattended%2Dupgrades,send%20you%20emails%20about%20updates.)
- Digital Ocean - How To Setup a Firewall with UFW on an [Ubuntu and Debian](https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server) Cloud Server
- How to install UFW on [Debian 12](https://www.cyberciti.biz/faq/set-up-a-firewall-with-ufw-on-debian-12-linux/)
- How to install docker on [Debian 12](https://docs.docker.com/engine/install/debian/)
- Running the docker daemon as a [non root](https://docs.docker.com/engine/security/rootless/) user
