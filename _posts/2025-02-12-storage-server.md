---
layout: post
title: Building My Own Storage Server - A DIY Journey
subtitle: Each post also has a subtitle
categories: markdown
date: 2025-02-12
author: 2GT_Rich
banner: https://img.youtube.com/vi/lPkQy5x_qDc/maxresdefault.jpg
tags: [storage]
---

![](//youtu.be/lPkQy5x_qDc)

There are some incredibly well-built storage platforms, like TrueNAS SCALE, that allow you to take your own hardware and turn it into a complete, turn-key storage solution. These platforms remove the complexity of building a storage server manually. I use TrueNAS SCALE for all things storage in my homelab, and it’s great. But this got me thinking—could I build my own complete storage system myself? What would that even look like? Let’s find out!

## Defining the Features of a DIY Storage Server

If I want to build a storage system that competes with solutions like [TrueNAS SCALE](https://www.truenas.com/), [Unraid](https://unraid.net/), or [OpenMediaVault](https://www.openmediavault.org/), it needs to have a solid feature set. Here’s what my storage server must include:

- **File Serving**: Support for both SMB (Windows) and NFS (Linux/Unix).
- **Virtualization**: Ability to run virtual machines (KVM/QEMU).
- **Containerization**: Support for running containers (Podman/Docker alternative).
- **ZFS Support**: Protection, snapshotting, and performance benefits.
- **Hardware Monitoring**: Ability to view system temps and sensor data.
- **Web GUI**: A graphical user interface for management.

## Choosing the Operating System

For my project, I need a Linux-based operating system. I have always been a heavy Debian/Ubuntu user, so naturally, I chose **Ubuntu Server 24.04 LTS** for its long-term support and security updates. While there are many Linux distributions available, Ubuntu has the right balance of stability and flexibility.

## Installing Key Software Packages

Once the OS was installed, the next step was to install all the necessary software packages:

- **Samba (SMB support)**: 
  ```bash
  apt install samba -y
  ```
- **NFS Server (NFS support)**: 
  ```bash
  apt install nfs-kernel-server -y
  ```
- **Virtualization tools (KVM/QEMU)**: 
  ```bash
  apt install qemu-kvm virt-manager libvirt-clients bridge-utils libvirt-daemon-system virtinst -y
  ```
- **Containerization (Podman)**: 
  ```bash
  apt install podman -y
  ```
- **ZFS Utilities**: 
  ```bash
  apt install zfsutils-linux -y
  ```
- **Hardware Monitoring**: 
  ```bash
  apt install lm-sensors -y
  ```

To enable hardware monitoring, I ran `sensors-detect` and confirmed the configuration. Say ‘YES’ to every option until it completes.

## Selecting a Web GUI for Management

A storage platform must be easy to manage. Instead of using CLI for everything, I explored two main web-based management tools:

- **Cockpit**: A lightweight, modular server management tool with a modern interface.
- **Webmin**: A powerful but older tool with support for more services but lacking ZFS integration.

Since my project relies on ZFS, I opted for **Cockpit**. I installed it with:
```bash
apt install cockpit -y
systemctl enable --now cockpit.socket
```

## Installing Additional Cockpit Modules

To extend Cockpit’s functionality, I installed additional modules:

- **Cockpit Virtual Machines**: 
  ```bash
  apt install cockpit-machines -y
  ```
- **Cockpit Podman Containers**: 
  ```bash
  apt install cockpit-podman -y
  ```
- **Cockpit Diagnostic Reports**: 
  ```bash
  apt install cockpit-sosreport -y
  ```
- **45Drives ZFS Manager**:
  ```bash
  git clone https://github.com/45drives/cockpit-zfs-manager.git
  sudo cp -r cockpit-zfs-manager/zfs /usr/share/cockpit
  ```
- **45Drives File Sharing**:
  ```bash
  curl -sSL https://repo.45drives.com/setup | sudo bash
  apt update
  apt install cockpit-file-sharing -y
  ```
- **45Drives Identities**:
  ```bash
  apt install cockpit-identities -y
  ```
- **45Drives Navigator**: 
  ```bash
  apt install cockpit-navigator -y
  ```
- **Cockpit Podman Module**: 
  ```bash
  apt install cockpit-podman -y
  ```
- **Cockpit LM-Sensors**:
  ```bash
  wget https://github.com/ocristopfer/cockpit-sensors/releases/latest/download/cockpit-sensors.tar.xz && \
  tar -xf cockpit-sensors.tar.xz cockpit-sensors/dist && \
  mv cockpit-sensors/dist /usr/share/cockpit/sensors && \
  rm -r cockpit-sensors && \
  rm cockpit-sensors.tar.xz
  ```

## Troubleshooting & Challenges

- **RAID1 for Boot Drive**: Creating a software RAID1 for Ubuntu’s boot disk turned out to be far more difficult than expected.
- **Cockpit Software Updates Bug**: The updates module failed with a `Cannot refresh cache whilst offline` error. The fix involved modifying the netplan configuration.
- **Container Issues**: Updating Podman caused conflicts, requiring manual intervention.

## Final Thoughts: Is a DIY Storage Server Worth It?

Building this system from scratch was a great learning experience, but it reinforced my appreciation for turn-key solutions like TrueNAS SCALE and Unraid. Here’s what I learned:

- Turn-key solutions exist for a reason. They save time and effort.
- GUI management is still fragmented. Even with Cockpit, a truly unified experience isn’t possible.
- Troubleshooting issues is time-consuming. Maintaining a DIY storage server requires constant attention.

Would I recommend doing this yourself? If you enjoy tinkering and learning, absolutely. But if you just want a reliable storage solution without the headaches, stick to a purpose-built storage OS.

## Watch the Full Build Video!

If you want to see the entire journey, check out my YouTube channel **2GuysTek** where I document the full setup and troubleshooting process!

What do you think? Have you built your own storage server? Let me know in the comments!
