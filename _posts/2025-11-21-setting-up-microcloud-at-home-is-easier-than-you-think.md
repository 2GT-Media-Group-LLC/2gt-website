---
layout: post
title: "Setting Up MicroCloud at Home is Easier Than You Think!"
date: 2025-11-21
author: 2GT_Rich
banner: https://img.youtube.com/vi/Pna4QINqo_Y/maxresdefault.jpg
tags: [microcloud, canonical, ubuntu, private cloud, homelab, edge computing, cluster computing, virtualization, containers, LXD, LXC, Ceph, OVN, Sponsored]

---

![](//youtu.be/Pna4QINqo_Y)

# Canonical MicroCloud Setup and Walkthrough Summary

## 🧩 Overview
- **MicroCloud** is a lightweight, self-hosted private cloud by Canonical (the makers of Ubuntu).  
- It combines **LXD**, **MicroCeph**, and **MicroOVN** into one automated stack.  
- Designed for **edge computing**, **homelabs**, and **small enterprise environments**.  
- Provides high-availability compute, storage, and networking — without the complexity of OpenStack or Kubernetes.

---

## ⚙️ System Requirements

### Minimum
- 8 GB RAM per node  
- One local disk (no partitions)  
- One network interface  
- Ubuntu 22.04 LTS or newer  

### Production Recommended
- 3 physical nodes for HA  
- 32 GB RAM per node  
- 3 disks per node (OS, local storage, distributed storage) — NVMe recommended  
- 2x 10Gb network interfaces per node (cluster + uplink)  

> 💡 For production: deploy on **bare metal**, separate networks for Ceph and OVN.

---

## 🧠 Planning the Deployment
1. Prepare a worksheet noting each node’s:
   - **IP address**
   - **Network interface assignments**
   - **Disk setup**
2. Configure your nodes' base OS and networking before installing MicroCloud.

---

## 🚀 Installation Steps

### Pre-Work
Your installation targets must have at least Ubuntu 22.04 LTS or newer installed and updated before attempting to install MicroCloud

### 1. Install MicroCloud Components
```bash
sudo snap install lxd microceph microovn microcloud --cohort="+"
```

### 2. Prevent Auto-updates
```bash
sudo snap refresh lxd microceph microovn microcloud --hold
```

### 3. Initialize the First Node
```bash
sudo microcloud init
```
- Configure internal network.
- Select local and distributed storage disks.
- Assign Ceph subnets (internal/public).
- Wait for initialization to finish.

---

## 🧩 Expanding the Cluster

1. On first node:
   ```bash
   sudo microcloud add
   ```
2. On each new node:
   ```bash
   sudo microcloud join
   ```
3. Provide cluster passphrase, select network interfaces, and assign disks.
4. Wait for nodes to sync and complete setup.

---

## 🖥️ Accessing the GUI (LXD UI)
- Access via: `https://<node-IP>:8443`
- Accept self-signed certificate.
- Authenticate using **certificate-based identity**:
  ```bash
  lxc auth identity create tls/lxd-ui --group admins
  ```
- Copy the generated token and paste it into the web UI.

---

## 🔍 LXD UI Tour

- **Dashboard Layout**  
  Left navigation panel with detailed sections per service area.

### Core Sections
- **Instances:** Manage containers and VMs.  
- **Profiles:** Define hardware and configuration templates.  
- **Networks:** Manage bridges, VLANs, and ACLs (acts as firewalls).  
- **Storage:** Manage pools, volumes, and S3-compatible buckets.  
- **Images:** Base OS templates for launching workloads.  
- **Clustering:** View nodes, groups, operations, and warnings.  
- **Permissions:** Manage identities, groups, and IDP integrations.  
- **Settings:** Control global options (including dark mode).

---

## 🧱 Creating Your First Instance

1. Click **“Create Instance.”**
2. Name and optionally describe it.
3. Choose an image (e.g., Ubuntu 24.04 LTS).
4. Set as **container** or **VM**.
5. Select desired cluster node or group.
6. Apply a profile (e.g., custom disk size).
7. Click **“Create and Start.”**

You can then:
- Manage configuration (disks, network, GPU passthrough).  
- Use **Terminal** or **Console** to access the instance.  
- View logs and snapshots within the GUI.

---

## 💬 Final Thoughts

### 👍 Pros
- Easy to deploy — runs with a few commands.  
- Intuitive, clean LXD UI.  
- Huge catalog of Linux images (even Windows supported).  
- Supports GPU, USB, PCI passthrough.  
- Great for homelab and small-scale production environments.

### 👎 Cons
- Documentation clarity needs improvement.  
- Lacks built-in monitoring — requires Prometheus/Grafana.  
- GUI only supports certificate-based login (no basic user/password option).

---

## 🔗 Learn More
Visit [Canonical MicroCloud](https://canonical.com/microcloud) for details, documentation, and setup guides.

---

**Summary:**  
Canonical’s MicroCloud is a simplified yet powerful private cloud platform. It provides automated setup for compute, storage, and networking using familiar Ubuntu tools, making it ideal for homelabbers and IT professionals seeking private cloud efficiency with minimal complexity.