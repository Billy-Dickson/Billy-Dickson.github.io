---
title:  Installing xrdp on Ubuntu 22.04.1 LTS
date:   2023-10-26
categories: [Homelab, Linux, Web]
tags: [homelab, linux, proxmox]
image:
    path: ../assets/img/posts/2023/2023-10-26-Installing-xrdp-on-Ubuntu%2024.04.1-LTS/Proxmox.webp
---

Why would you want to?

1. Well if you're main work PC is running Windows 10 or Windows 11, then it makes total sense to set your Linux PC up this way, as Windows natively supports RDP (Remote Desktop Protocol). So you wouldn’t need to install any extra software to connect.
2. You can then connect your Linux PC through Guacamole and connect to it via a browser.
3. If you own your own domain and it's hosted by cloudflare, you could use a Cloudflare tunnel for secure connection over https with an email for 2FA.

This is what I do and it does work quite well for light development, browsing and email use. You wouldn't want to use it for say gaming but it works fine for my needs.

## Prerequistes

1. A working knowledge of Linux and how to install it on real hardware or in a Virtual Environment.
2. A copy of Ubuntu Desktop 22.04.01 is available for download from [here](https://www.releases.ubuntu.com/22.04/)
3. A running [Ubuntu Gnome Desktop](https://ubuntu.com/download/desktop) installed on hardware or a virtual machine, (mine is running on a [Proxmox Virtual Machine](https://www.proxmox.com/en/downloads)).

## Setting up Gnome with xrdp

Download and install the [Gnome Desktop](https://ubuntu.com/download/desktop)

The next parts of the installation will need to be done on the Ubuntu Desktop, if it's a virtual install then via the virtual console, if it's on real hardware then with a keyboard, mouse and monitor connected. Depending on how you installed Ubuntu, you'll have to add yourself to the sudo group, to check that you're in the sudo group type the following at the prompt.

```bash
id
```

Instructructions on adding yourself to the group are [here](https://askubuntu.com/questions/124166/how-do-i-add-myself-into-the-sudoers-group#124200)

### Running on Proxmox (Optional)

If you're running it on Proxmox, then it's always a good idea to install the Qemu-guest-agent

```bash
sudo apt install qemu-guest-agent
```

#### Run through the Ubuntu installer and update then install xrdp

```bash
apt update && apt upgrade
apt install xrdp
```

#### Add the xrdp user to the ssl-cert group

```bash
sudo usermod -a -G ssl-cert xrdp
```

#### Setup Ubuntu Gnome Looks and Feel

At the prompt type

```plaintext
nano .xsessionrc
```

Copy and past the following.

```plaintext
export GNOME_SHELL_SESSION_MODE=ubuntu
export XDG_CURRENT_DESKTOP=ubuntu:GNOME
export XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
```

#### Restart xrdp

```bash
sudo systemctl restart xrdp
```

![My Current Desktop](../assets/img/posts/2023/2023-10-26-Installing-xrdp-on-Ubuntu%2024.04.1-LTS/Desktop.webp)

### Allow Colord without the admin prompt at login

``` bash
sudo nano /etc/polkit-1/localauthority/50-local.d/40-allow-colord.pkla
```

#### Paste the following

```plaintext
[Allow Colord]
Identity=unix-user:*
Action=org.freedesktop.color-manager.*
ResultAny=no
ResultInactive=no
ResultActive=yes
```

### Allow printer access in Gnome

```bash
sudo nano /etc/polkit-1/localauthority/50-local.d/50-printer-open-access.pkla
```

#### Add the following

```plaintext
[Printer Administration]
Identity=unix-group:lpadmin
Action=org.opensuse.cupspkhelper.mechanism.*
ResultAny=auth_admin_keep
ResultInactive=no
ResultActive=no
```

### Allow adminstrative functions under Gnome

```bash
sudo nano /etc/polkit-1/localauthority/50-local.d/47-user-admin.pkla
```

#### Paste in the text below

```plaintext
[user admin]
Identity=unix-user:*
Action=org.gnome.controlcenter.user-accounts.administration
ResultAny=auth_admin_keep
ResultInactive=no
ResultActive=no
```

### Enable Sound Redirection

#### Download the build tools

```bash
sudo apt install build-essential dpkg-dev libpulse-dev git autoconf libtool
```

#### Install git and download the github repositery

```bash
sudo apt install git && git clone https://github.com/neutrinolabs/pulseaudio-module-xrdp.git
```

#### Change directory into the repository

```bash
cd pulseaudio-module-xrdp
```

#### Run the setup script

```bash
scripts/install_pulseaudio_sources_apt_wrapper.sh
```

Go for a coffee or something because depending on your hardware, it may take a long time. If your running it on a cheap Chinese PC under a [proxmox hypervisor](https://www.proxmox.com/en/proxmox-ve) then....Coffee!

#### Run Configure script

```bash
./bootstrap && ./configure PULSE_DIR=~/pulseaudio.src
```

#### Run the make script

```bash
make
```

#### Run the make install script

```bash
sudo make install
```

#### Check to see if the modules have been installed

```bash
ls $(pkg-config --variable=modlibexecdir libpulse) | grep xrdp
```

#### You should get the following output

```bash
module-xrdp-sink.la
module-xrdp-sink.so
module-xrdp-source.la
module-xrdp-source.so
```

#### Reboot and Test

```bash
sudo reboot
```

#### Cleaning up afterwards, removing dependecies and files

```bash
sudo apt purge debootstrap schroot && sudo apt autoremove
```

```bash
cd ~
rm -rif pulseaudio-module-xrdp && rm -rif pulseautio.src
```

All going well, your sound setting in [Ubuntu Gnome](https://ubuntu.com/download/desktop) should look like this, feel free to play a wave or mp3 file to test.

![Gnome Sound settings](../assets/img/posts/2023/2023-10-26-Installing-xrdp-on-Ubuntu%2024.04.1-LTS/Sound.webp)  

#### Installing Microsoft TTF (Optional Truetype Fonts)

This is quite handy if you happen to use LibraOffice, as it will add the fonts to the font library and render and print Microsoft Fonts correctly.

```bash
sudo apt install ttf-mscorefonts-installer
```

### Speeding up XRDP

#### Configure the TCP send buffer size

Edit the file /etc/xrdp/xrdp.ini

```bash
sudo nano /etc/xrdp/xrdp.ini
```

Locate the line that reads "tcp_send_buffer_bytes", remove the comment character ("#") and increase the size

```plaintext
tcp_send_buffer_bytes=8388608
```

Restart the service

```bash
sudo systemctl restart xrdp
```

#### Configure the kernel network buffer size (Optional)

Change the current network buffer size to a larger value with the following command

sysctl -w net.core.wmem_max=16777216

Create a new file /etc/sysctl.d/xrdp.conf with the following content

```bash
net.core.wmem_max = 16777216
```

This will ensure that the value is properly set between reboots.

#### Lower the colour resolution from 32 bpp to 24 bbp and the crypt level from hight to medium

Changed the display from 32 bits per pixel to 24 as it’s pinning the CPU to 80% and uploads speed to on average 3.5 Mbps on my 2K monitor (to be expected). You can of course got a low as 16 bit if you’re not that fussy about resolution.

```bash
sudo nano /etc/xrdp/xrdp.ini
```

```plaintext
# max_bpp=32
max_bpp=24
```

```plaintext
# crypt_level=high
crypt_level=medium
```

## References

- [How to install XRDP on Ubuntu 22.04](https://youtu.be/U2xHSRuwJ-A?si=1Xs84HscmLwlZAtj) - Easy Hacks
- [neutrinalobs pulseaudio module xrdp](https://github.com/neutrinolabs/pulseaudio-module-xrdp) - github page
- Griffon's [IT Library](https://c-nergy.be/blog/?cat=8)
- Gnome [Policy Kit](https://manpages.ubuntu.com/manpages/focal/man8/pklocalauthority.8.html)
- Speeding up [xrdp on Ubuntu 20.04](https://devicetests.com/speeding-up-xrdp-ubuntu-20-04-xorg-tips-tricks) - Device Test Website
- Proxmox [qemu guest agent](https://pve.proxmox.com/wiki/Qemu-guest-agent)
- Low network performance while accessing [xrdp server](https://www.suse.com/support/kb/doc/?id=000021159)
- XRDP is quite slow [Ask Ubuntu](https://askubuntu.com/questions/1323601/xrdp-is-quite-slow)
- CrownCrowd Wiki - How to install and configure [XRDP on Gnome](https://wiki.crowncloud.net/?How_to_Install_and_Configure_XRDP_with_GNOME_or_LXDE_on_Ubuntu_24_04)
