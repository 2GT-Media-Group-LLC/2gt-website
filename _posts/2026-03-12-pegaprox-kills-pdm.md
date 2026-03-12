---
layout: post
title: Is Proxmox Datacenter Manager Dead? PegaProx May Have Just Killed It
date: 2026-03-12
description: Exploring how PegaProx, an open-source management platform, is revolutionizing Proxmox environments and potentially replacing PDM entirely.
tags: [Proxmox, PegaProx, PDM, Virtualization, Homelab]
categories: [Virtualization]
banner:
  image: https://img.youtube.com/vi/qnq0Y9mJgXA/maxresdefault.jpg
  opacity: 0.618
---

## Watch the Full Video

![](//youtu.be/qnq0Y9mJgXA)

---

## Introduction

Proxmox Datacenter Manager might already be dead, and an open source project may have just killed it. Buckle up, friends, cause this is going to be wild!

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here! I get a lot of emails from vendors, software makers, and people who are hoping I'll create a video about their product, and as much as I appreciate the emails, I don't usually do so because, well it's either not in my niche or it's not something I want to put my name next to if you get what I mean. Earlier this week I got an email from the creators of an open source project called **PegaProx** introducing me their work.

I did want I normally do, I read the email, then I went to take a cursory look at their site. What I found was something that truly blew me away. PegaProx may single handedly just not only have killed PDM, but also also changed the game for what a modern PVE GUI should be. Enough hyperbole, let's dig into PegaProx and it's features, see if this is something you should be running if you're using Proxmox, and at the end I'll show you how to deploy it. Let's get to it!

---

## What is PegaProx?

PegaProx is an open-source, web-based management platform for Proxmox environments that gives you a single plane of glass for multiple clusters, nodes, and workloads. It provides:

- **Centralized Monitoring**: Overview of all your clusters and workloads from one dashboard
- **VM and Container Management**: Complete lifecycle management of your virtual machines and LXC containers
- **Automated Load Balancing**: Distribute workloads across your cluster automatically
- **Cross-Cluster Migrations**: Move workloads between clusters seamlessly
- **High Availability Orchestration**: Advanced HA functionality beyond standard PVE capabilities
- **Role-Based Access Control**: Fine-grained permissions management through a unified dashboard

All this is designed to reduce operational complexity and give administrators better visibility and control over distributed infrastructure.

### PegaProx vs Proxmox Datacenter Manager

If you thought this sounds a lot like what Proxmox Datacenter Manager does, you'd be right. But unlike PDM's limited feature set, PegaProx, with the exception of PBS visibility, can do practically everything a Proxmox user would want to do and more. 

**Key Advantages Over PDM:**

- Create and customize VMs and containers directly in the interface
- Manage PVE host configurations
- Set up automated load balancing across your cluster
- Carry out cross-cluster migrations
- Beautiful, modern user interface
- No "No Valid Subscription" warnings (it's open source!)

---

## PegaProx Feature Walkthrough

### Dashboard and Overview

This is the login screen for PegaProx. Once logged in, we land on the **All Clusters Overview** page. Which, can we just take a moment to take in the UI here? Seriously, beautiful! 

This page is full of cards that provide you with macro details about all of the hosts and clusters you've added to PegaProx, including:
- All clusters added
- Top resource consumers in a scrollable list

You can also sort this information by name, health, nodes, VMs, CPU, and RAM.

### Cluster Overview

Clicking on a cluster lands you on the overview page, with:
- Cards for each node in your cluster in the middle left
- Overall cluster health and last migrations cards on the right

Clicking 'show all' in the card gives you the full details of the current state of your node's resource usage.

### Resources Tab

The Resources tab is where we get a complete look at all of the workloads running. Both VMs and LXC containers are listed here, and while the cards view is nice, PegaProx provides helpful filters:

- **State Filters**: All, Active, Stopped
- **Type Filters**: VMs, LXC containers
- **View Options**: Cards view, list view, and the awesome compact view

In the compact view, once you select one of your workloads, you get:
- Macro details (CPU, RAM, Disk, Uptime)
- Quick actions (shutdown, reboot, console access)
- Configuration modification - all configurable options available in PVE are accessible here

You can:
- Change hardware and disks
- Manage network settings
- Manage snapshots, backups, and replication
- View history and change options
- Access historical graphs
- Trigger migrations between nodes or across clusters
- Clone, force reset, or delete workloads
- Create new VMs and containers with the same fields as PVE

### Snapshot Management

The **snapshot overview** is something that PVE has been missing forever. The snapshot view shows you all of the snapshots in your cluster. This is one of those quality of life features that exist in enterprise virtualization platforms to make admins' lives easier and help you clean up wasted space due to forgotten snapshots.

### Datacenter Management

#### Summary
The summary page gives you macro information about the datacenter including:
- Cluster quorum status
- Node state
- Guests and container state
- Macro resource usage

#### Cluster Tab
Details about your cluster configuration and status.

#### Options
View and edit all of your datacenter options configured.

#### Storage
Detailed view of all storage configured, including:
- Name, type, content, and path
- Connected nodes
- Shared storage status
- Storage operations (add, modify, delete)
- Multipath redundancy configuration with a single click

#### SDN (Software-Defined Networking)
Manage all your software-defined networking:
- Add, change, and remove zones and VNets
- Apply settings to all nodes in your cluster

#### Backups
- Manage configured backups
- Create new backup jobs
- Delete backup jobs

#### Replication
Same functionality as backups but for replication jobs.

#### Proxmox Native HA
- Manage and control your PVE HA functionality
- Add and remove resources to HA
- Create HA groups

#### CPU Compatibility
Change the default CPU compatibility mode for your datacenter.

#### Firewall
Create and manage firewall rules for your datacenter.

### Datastore Management

The **Datastore** tab allows you to browse your provisioned datastores and see what's held within them:
- VM and container disks
- Backups
- ISOs

#### Storage Balancing (Experimental)
Enable automated balancing of your workloads across storage for better performance. (*Use at your own risk!*)

### Automation Features

The **Automation** tab allows you to:
- Configure scheduled actions
- View tags and labels
- Create email alerts when thresholds are exceeded
- Create affinity and anti-affinity rules for workloads
- Configure and run custom scripts on cluster nodes

### Reports

Get usage information over time, selectable by hour, day, and week, to see historically who the heavy-hitters are.

### Settings

Configure:
- Workload balancing for your cluster
- Node updates
- Rolling cluster updates
- And more

---

## UI and Design Philosophy

I've said this before, and I'll say it again - I'm not a fan of the PVE UI. I think it's dated, difficult to find the actual settings you want, and it's downright ugly. In contrast, PegaProx has kinda blown away by its entire reimagining of what the Proxmox user experience could be like.

### Important Notes

I don't mean to paint PegaProx as a production-ready replacement for all your PVE and PDM activities. At the time this video was made, PegaProx is considered **beta**, and there are things that may behave differently from the production version.

**Known Issues:**
- Some UI elements may appear in German even with English selected
- Still missing PBS (Proxmox Backup Server) visibility

**Additional Features Not Covered:**
- Multiple user-selectable themes
- Extensive security and user management
- AD domain integration
- OIDC for single sign-on
- Two-factor authentication
- Granular permissions for users and groups
- Tenant creation for logical segmentation

---

## How to Deploy PegaProx

### Step 1: Create the Container in PVE

1. Log into your PVE host and click the **Create CT** button in the top right corner
2. Give the container a hostname (e.g., "PegaProx") as an FQDN
3. Set a root account password
4. Select your preferred Linux template (Ubuntu 24.04 recommended)
5. **Disk Space**: Default 8GB is fine, but 16GB provides room for OS updates
6. **CPU Cores**: Minimum 1 core for small homelab, but 4 cores recommended
7. **RAM**: Minimum 1GB required, but 4GB recommended
8. **Network**: Set a **static IP address** (don't use DHCP for servers)
9. **DNS**: Configure as needed or use host settings
10. Check **"Start after created"** to have the container ready
11. Click **Finish**

### Step 2: Prepare the Container

1. Open the container console in PVE
2. Log in with root and your set password
3. Install curl: `apt install curl -y`
4. Clear the console: `clear`

### Step 3: Download and Run the Install Script

1. Visit https://pegaprox.com/
2. Click any of the 3 download buttons
3. Copy the install script link
4. In the container console, paste and run the install command
5. Wait for supporting packages to install

### Step 4: Configure Installation

When prompted (Step 6), choose your port configuration:
- Option 1: Default ports
- **Option 2: Professional setup** (standard SSL ports) - Recommended
- Option 3: Custom ports

The installation will complete and provide you with the web interface URL.

### Step 5: Access and Login

1. Copy the web interface URL from the installation completion message
2. Open it in a browser
3. Accept the self-signed certificate warning (you can add a valid cert later)
4. Login with:
   - **Username**: `pegaprox`
   - **Password**: `admin`
5. Change the default password when prompted

### Step 6: Add Your Proxmox Cluster

1. Click **"Add Cluster"** in the top right
2. Enter a cluster name (e.g., "2GT-PVE")
3. Enter the IP address or DNS name of your Proxmox cluster/host
4. Enter login credentials (root@pam recommended with root password)
5. Click **"Add Cluster"**

You can now drill down into your cluster's usage, health, and manage all your VMs and containers!

### Sizing Recommendations

I'm using generous resources in this walkthrough, but PegaProx has a comprehensive docs site with minimum sizing requirements and prerequisites. **Highly recommend checking their documentation** to properly size PegaProx for your environment.

---

## Final Thoughts

**Holy shit.** PegaProx effectively replaces both PVE and PDM's user interfaces, and does it in style! I can't say enough nice things about what PegaProx is building here.

### Message to Proxmox Server Solutions

Proxmox Server Solutions, listen up… This is the GUI I want for PVE, PDM, and PBS. Reach out to these people, pay them a ton of money, and adopt this as your next-generation UI, **seriously**.

### PegaProx as a PDM Replacement

PegaProx looks like it effectively kills PDM as it exists at this time entirely. There's no need to use PDM when PegaProx does everything it's doing, and does it so much more beautifully. But saying it's a PDM replacement doesn't really do it justice. **PegaProx kills the PVE GUI as well** since you can do practically every single thing you'd do there, here.

### Real-World Impact

I've done test deploys of Proxmox in business settings, and one of the biggest issues I have to overcome with non-technical people who have to administer PVE is the UI of Proxmox. One of the biggest barriers to entry is the dated interface, and after seeing PegaProx, I realize my previous suggestions for UI redesign were insufficient.

### Current Limitations

**Eye candy aside**, PegaProx brings you cluster load balancing as a standard feature - something that is **not available in PVE**, still…after all of these years. And gives you PDM features like cross-cluster migration functionality as well.

**Missing Features:**
- PBS (Proxmox Backup Server) support

**Beta Considerations:**
- Some features may break
- Continue to monitor for updates

### Bottom Line

It's **open source and free to use**, and you won't see anymore 'No Valid Subscription' warnings! Spin up a container, deploy PegaProx right now, kick the tires on it, use it, find bugs, and report your findings to the community.

---

## Closing

Thanks for watching this video, folks, and thank you to the fine people who support us through Patreon and the YouTube Membership program. If you'd like to support what we do here, consider checking those out. Join our community Discord and chat with me and like-minded homelabbers, geeks, and nerds, and as always, we'll see you on the next one!
