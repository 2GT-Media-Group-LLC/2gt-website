---
layout: post
title: "VergeOS Installation Walkthrough: From ISO to Your First VM"
date: 2026-02-17
categories: [Virtualization, Infrastructure, Homelab]
tags: [VergeOS, VMware, Virtualization, HCI, PrivateCloud, Installation, Sponsored]
description: "A complete step-by-step VergeOS installation guide — from burning the ISO and installing the OS to licensing the cluster and spinning up your first virtual machine."
banner: https://img.youtube.com/vi/El-11ydk-Jo/maxresdefault.jpg
---

![](//youtu.be/El-11ydk-Jo)

Back in October 2024, I introduced you to **VergeOS** as a compelling alternative to VMware, covering its architecture, user interface, and overall user experience. This time, we're going to walk through the installation of VergeOS — step by step — from burning the ISO and installing the OS all the way to spinning up your first virtual machine. Let's get to it!

---

Hey there, homelabbers, self-hosters, IT-Pros, and Engineers. Rich here! It had always been my plan to take you through the installation process, start to finish, for VergeOS so you could try it out for yourself. Thanks to this video's sponsor, **Verge.IO** — the makers of VergeOS — I'm able to bring this to you right now!

Before we get into the step-by-step how-to, let's get a quick recap of VergeOS out of the way first.

---

## What Is VergeOS?

VergeOS is essentially a data-center operating system that collapses compute, storage, networking, backup, and even DR into one unified platform. Unlike traditional HCI or the classic VMware stack, VergeOS is one unified codebase with one UI and one lifecycle — and is not a cobbled-together pile of different products. That's what makes it special: it's a true private cloud operating system, which massively simplifies operations and slashes costs. And in a post-VMware world, that actually matters.

VergeOS is compelling because it lets you:

- Modernize your private cloud
- Reuse existing hardware
- Migrate off VMware at your own pace
- Run the whole data center like a single system instead of babysitting a stack of products

This install how-to is going to walk you through every part of the installation and setup process, so by the end you should have VergeOS completely set up and ready to use. Our first stop on the installation journey is hardware requirements.

---

## Hardware Requirements for VergeOS

Let's get the minimum and recommended hardware requirements out of the way first.

### CPU

VergeOS requires a **64-bit x86 CPU** with a minimum clock speed of **2.7 GHz**. Both AMD and Intel CPUs are supported, and hardware virtualization must be enabled in the BIOS.

### Memory

VergeOS requires a **minimum of 16GB of RAM per node** dedicated to VergeOS itself. You'll need an additional **1.5GB of RAM for each terabyte of raw vSAN storage**, and on top of the base requirements, you'll also need additional RAM for your virtual workloads.

### Storage

VergeOS requires **NVMe or SSD disks** for metadata and workload storage, with NVMe direct-attached storage recommended for optimal performance. For systems with storage controllers, Verge.io recommends a dedicated HBA configured in IT mode or JBOD mode for direct disk access. Mechanical disks are only recommended for use in an archive tier and never for primary storage.

### Networking

- **Minimum 1 GbE per node** for management UI access and guest VM external network access
- **Minimum 1 x 10 GbE per node** for the internal core network used for storage replication and synchronization

One of the nice things about VergeOS is that it's designed to run on vanilla x86 hardware and doesn't require a specific vendor's HCL — meaning you can often deploy VergeOS on servers you already own and it'll work great.

For those who don't have hardware available, **Verge.io offers a free test-drive lab** where you can explore VergeOS without needing physical hardware or committing to an install: [verge.io/the-ultimate-test-drive/](https://www.verge.io/the-ultimate-test-drive/)

Alright, prereqs out of the way. Before we kick this off, make sure you have a **USB stick of at least 8GB** in size. Let's get to it!

---

## Step 1: Download the VergeOS ISO

Open your browser and head over to [updates.vergeos.com/download/](https://updates.vergeos.com/download/) and press enter. The download should start automatically. Once the download is complete, we'll move on to creating the bootable USB stick.

---

## Step 2: Create a Bootable USB Stick with Rufus

There are a variety of tools for building bootable USB sticks from ISOs. On Windows, my go-to is the free tool **Rufus**. Head over to [rufus.ie](https://rufus.ie/en/) and click Download at the top, then select the top link in the list to start the download.

Once Rufus is downloaded:

1. Open Rufus and plug in your 8GB or larger USB stick
2. Click the **Select** button and choose the VergeOS ISO you just downloaded, then click **Open**
3. Click **Start** to begin
4. When prompted, select **"Write in DD image mode"** and click **OK**
5. Rufus will warn you that all data on the USB stick will be wiped — if you're good with that, click **OK**

Sit back and let Rufus build the bootable USB. This can take some time to complete. Once it's done, click **Close** and we're ready to install!

---

## Step 3: Install VergeOS

Now it's time to install VergeOS on hardware. Booting from a USB stick is typically straightforward, though the exact method depends on your system's manufacturer. I'll be using a Supermicro server in this walkthrough, but the general process is the same across most systems.

Insert the bootable USB stick, power on the host, and tell the system to boot from the USB. Once the initial boot completes, you'll see the **VergeOS boot loader screen** — wait the 10 seconds or press Enter to continue. The installer will take a moment to start up depending on your hardware.

### Node Type

The first step is choosing the type of node you're deploying. Regardless of whether you're deploying a single system or a multi-node cluster, **the first system must be a controller**. In a multi-node cluster, your first two nodes must both be controllers for redundancy and fault tolerance. Press Enter to select **Controller**.

### New Install or Join Existing?

You're asked if this is a new install or if you're joining an existing system. Since we're deploying a new cluster, leave this on **Yes** and press Enter.

### Timezone Configuration

1. **Region** — Select your region (e.g., America) and press Enter
2. **Timezone** — Scroll to your timezone (e.g., Los_Angeles) and press Enter
3. **NTP Servers** — VergeOS offers official NTP servers by default, which work for most setups. If you have dedicated NTP servers on your network, enter them here. Otherwise, leave the defaults and press Enter
4. **Date** — Verify the year, month, and day are correct and press Enter
5. **Time** — The system expects 24-hour clock format. Adjust if necessary and press Enter

### Cluster Name and Admin Account

- **Cluster Name** — This is the name of the cluster itself, not an individual node. Choose accordingly (e.g., `2gt-cluster`) and press Enter
- **Admin Username** — VergeOS defaults to `admin`, which is fine for most setups. Press Enter to accept
- **Admin Password** — Set and confirm your admin password, then press Enter
- **Admin Email** — Enter an email address for the admin user and press Enter

### Network Configuration

Your host needs at least two network interfaces. The first configuration is for the **core network**, used for inter-node storage replication and cluster health management.

On my system, the first NIC is connected to my LAN and the second is on a dedicated layer-2 VLAN for the core network. To select the correct interface:

1. Press **Spacebar** to deselect the first NIC
2. Arrow down to the second NIC and press **Spacebar** to select it
3. Press **Enter** to continue

Next, configure the physical switch settings for the core network interface. I recommend giving switches descriptive names. Press Enter, clear **Switch 1**, type **Core Network**, and press Enter. Arrow to **Finish** and press Enter.

VergeOS will check the interface for an existing core network, then move on to the remaining network interface. Select it and similarly rename **Switch 2** to **LAN**. Then, arrow down to the **Core-Network** field, press Enter to edit it, clear **yes**, type **no**, and press Enter. Arrow to **Finish** and press Enter.

### External Network

VergeOS will now ask which physical network provides external access to the UI, LAN, and WAN. Since we named our switch **LAN**, VergeOS can easily identify the right one — leave it set to **LAN** and press Enter.

Now configure the external network interface:

- **VLAN ID** — If your external connection doesn't use a tagged VLAN, leave this blank and press Enter (most home/lab setups fall here)
- **IP Address** — I strongly recommend a static IP. Enter your address in CIDR notation (e.g., `172.24.1.50/24`) and press Enter
- **Default Gateway** — Enter or confirm your default gateway (e.g., `172.24.1.1`) and press Enter
- **DNS Servers** — Verify and add any additional DNS servers for redundancy, then press Enter
- **IPMI** — If your server has IPMI and you'd like to configure it here, do so. Otherwise, press Enter to skip

### Disk Configuration

- **Disk Encryption** — Choose whether to enable disk-level encryption for vSAN storage based on your security requirements. We'll leave this set to **No** and press Enter
- **Storage Layout** — VergeOS automatically builds storage tiers based on the disks in your system. You can accept the default configuration (which is what I'll do) or customize. Press Enter to continue
- **Manual Tier Assignment** — If you want to reorganize disks into different tier groups, say Yes here. I'm happy with the VergeOS suggestions, so I'll arrow to **No** and press Enter
- **Over-Provisioning Tier** — Select a storage tier to use for over-provisioning and failover in the event of memory overcommitment. I'll select my Tier 3 (largest capacity) and press Enter
- **Over-Provisioning Per Drive** — Define the storage space per drive to allocate. The default of 4GB per drive is fine, so press Enter
- **Confirm Total** — Review the total over-provisioning space and press Enter to accept

VergeOS will now format the drives, prepare them for vSAN, and complete the vSAN implementation. This can take a while depending on how many disks you have, so give it time. Once vSAN is built, VergeOS will install and prepare the OS packages — also potentially time-consuming.

### Final Steps

When installation completes, you'll be asked if you want to register the EFI boot partitions with your system's BIOS. Leave this on **Yes** and press Enter.

**Congratulations!** You've completed the initial installation of VergeOS. Remove the bootable USB stick and press Enter to reboot. After the system restarts and VergeOS starts up, you'll land on the VergeOS console screen. Great work!

---

## Step 4: Access the Management UI

Pop open a browser and navigate to the IP address you configured during setup. For me, that's `172.24.1.50`.

Fresh installs use a self-signed certificate, so your browser will alert you. Click **Advanced**, then **Proceed to the website**. You'll land on the VergeOS login page. Enter the admin username and password you set during installation and click **Sign In**.

Welcome to the VergeOS web management UI! By default it's in Light Mode — head to the top right corner, click the sun icon, and select the **Dark Theme**. Much better.

### UI Overview

The VergeOS UI is broken into three main sections:

- **Top navigation** — Organizes the major sections of the platform
- **Main content window** — The primary working area
- **Left sub-menu** — Subcategories, options, and configurations for the current section

The **main dashboard** is your high-level overview of the entire VergeOS cluster, broken into easy-to-understand cards covering VMs, networks, nodes, alerts, storage health and performance, and logs.

Here's a quick tour of the major sections:

- **Virtual Machines** — Build, run, and operate workloads end-to-end. Spin up VMs from ISOs or templates, assign CPU, RAM, and storage tiers, attach networks, and manage lifecycle actions like snapshots, cloning, live migration, and HA.
- **Files** — Upload data including ISOs, manage downloaded marketplace images, create and share data hosted in the cluster.
- **Tenants** — True multi-tenancy with completely isolated logical tenants — their own users, quotas, networks, and workloads. Ideal for MSPs, service providers, or teams needing separated environments.
- **NAS** — Leverage cluster storage for shared network storage — Windows file shares, NFS shares, and more, just like a dedicated NAS appliance.
- **Networks** — Define VLANs, bridges, and network segments and attach them to VMs, tenants, and services.
- **Backup / DR** — Configure snapshots and replication for VMs and storage. Platform-level, agent-free recovery and availability focused on replication to another VergeOS system.
- **Infrastructure** — Manage the underlying cluster: nodes, disks, networks, capacity, and hardware health.
- **Import/Export** — Move workloads in and out of VergeOS. Import from external platforms or export existing VMs as portable images.
- **Repositories** — Store install media and images like ISOs and VM templates. Add catalogs or manage third-party repos from the marketplace.
- **AI** *(New in v26)* — Private AI features built into the platform. Pick a model, start chat sessions, and use an OpenAI-compatible API so existing tools can point at your VergeOS system.
- **Logs** — All platform logging combined and searchable in one place.
- **System** — Configure the platform itself: users and roles, licensing, updates, time settings, notifications, certificates, and system-wide behavior.

VergeOS has a lot of depth, and I highly recommend digging into their public docs for any features you want to explore further. VergeIO has also added an AI assistant right inside the platform that can answer real questions like *"How can I configure my VM to access the Internet?"* — quick, accurate, and helpful.

---

## Step 5: License the Cluster

Before VergeOS will allow you to start virtual machines, you need to license the cluster. I'm using a trial license here, but the process is very similar for production licenses as well.

1. Head to **System** → **Settings**
2. On the left, select **Updates Settings**
3. Enter the username and password supplied with your license in the **User** and **Password** fields
4. Click **Submit** to apply the license
5. Verify by heading to **System** → **Updates** — you should see confirmation that your credentials were accepted and access to the update server has been granted
6. Head back to **Settings** and in the License card, confirm your system is licensed and valid

Right on! Let's build our first VM!

---

## Step 6: Create Your First VM

Head over to the **Virtual Machines** section from the main dashboard, then click **New VM** on the left.

VergeOS offers multiple VM creation methods:

- **New VM Wizard / Advanced / Clone** — Start from scratch
- **Import** — From a media image, shared object, or volume
- **Catalogs / Recipes** — Pre-prepared templates (my favorite)

**Catalogs is the fastest and easiest approach.** Templates are organized into sections including:

- **Applications** — Ready-to-go deployments for Docker, Grafana, Kubernetes K3S, LAMP stack, and OpenVPN-AS
- **Operating Systems Marketplace** — A large list of ready-to-deploy Linux distributions

Let's deploy an **Ubuntu Server 24.04** VM using a marketplace template. Select it from the list and click **Next**.

### VM Configuration

- **VM Name** — Enter a name for the VM inside VergeOS (e.g., `Ubuntu Server VM`)
- **CPU Cores** — 4 cores is plenty for our purposes
- **RAM** — I'll bump this up to 8GB
- **Hostname** — The actual hostname of the VM. I'll match the VM name for simplicity

**User Configuration:**
Create an admin user and set a password for it.

**Network:**
Leave set to DHCP and select **External** for the network to give the VM direct access to your LAN.

**Drives:**
Set the OS disk size (50GB is fine) and select your storage tier. I'll leave it on **Tier 1** for the fastest disks.

Click **Submit** to kick off the VM build.

You'll see the disk being initialized in the **Drives** section. Once complete, head up and click the **Play button** in the top-left of the content window to power on the VM and confirm.

The dashboard will start populating with CPU, RAM, disk, and network throughput stats as the VM comes to life. Click the **Console** button in the upper-left to open the VM console in a new browser tab.

Once the VM boots, log in with the user credentials you set. Verify the IP address looks correct for your LAN, then run a quick ping to confirm internet access.

**Congratulations on creating your first VM in VergeOS!**

---

## Closing

Thanks for watching this video, folks, and thank you to everyone who supports the channel through **Patreon** and the **YouTube Membership** program. If you'd like to support what we do here, consider checking those out. Join our community **Discord** to chat with me and like-minded homelabbers, geeks, and nerds — and as always, we'll see you on the next one!
