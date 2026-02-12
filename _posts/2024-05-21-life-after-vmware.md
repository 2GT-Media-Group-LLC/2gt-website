---
layout: post
title: "Life After VMware: A Comprehensive Roundup of Alternative Hypervisors"
date: 2024-05-21
author: 2GT_Rich
tags: [VMware, XCP-ng, Proxmox, Hyper-V, Nutanix, virtualization]
banner:
  image: https://img.youtube.com/vi/eQgzITx1Sp8/maxresdefault.jpg
---

![](//youtu.be/eQgzITx1Sp8)

## How did we even get here?

Welcome to the final-final video on evaluating your options if you’re coming from the world of VMware and ESXi. This video is my opus, my coup de grâce, the finale of this series that has taken up practically four solid months of my time. In this video, I’m going to attempt to aggregate and summarize as much of the information from the last four videos as I can, put a nice little bow on it, and hand it over to you.

In the last four videos, I looked at XCP-ng, Proxmox, Hyper-V, and Nutanix as alternatives to VMware ESXi and vCenter in your homelab and for your business. All four platforms have pros and cons, so I’m going to attempt to compare them based on their features and limitations in the hopes that this summary gives you some ideas on which direction you want to take with your gear or business.

If you haven’t watched the videos, I encourage you to do so to understand the depth of research that went into this summary. So with that, it’s time to commit death by PowerPoint! Let’s get to it!

## Can These Products Replace VMware?

For the most part, all of these hypervisors will replace VMware ESXi and even vCenter, but the devil, friends, is always in the details. Let’s dig in.

- **XCP-ng:** Yes, hands-down. It’s the most analogous to ESXi and vCenter and is free.
- **Proxmox:** Yes, absolutely, with vCenter-equivalent features, added LXC container support, and, as a friend in our Discord is fond of saying, it will run on a potato, and is also free.
- **Hyper-V:** Mostly yes. With the exception of some Linux OS compatibility, it will serve Windows shops well and has vCenter-equivalent features.
- **Nutanix:** Yes, best suited for VMware users and businesses already invested in HCI or hyper-converged infrastructure.

All of these hypervisors will run VMs without issue, and with the exception of Hyper-V, are either free or have a free version you can run in your homelab or use to personally evaluate for your business. Now, let’s talk a bit about their underlying operating systems and how they’re deployed.

## Underlying Operating Systems and Deployment

- **XCP-ng:** Based on the Xen Hypervisor, it is entirely open-source. A standard deployment consists of one or more XCP-ng hosts for running virtual workloads, and either the Xen Orchestra Appliance (XOA) or a deployment of Xen Orchestra (XO) to manage XCP-ng.
- **Proxmox:** Based on Debian with a customized Linux kernel, uses KVM for running VMs and LXC for running Linux containers. Deployment consists of one or more independent Proxmox hosts, each having their own web-based management consoles.
- **Hyper-V:** A component of Windows Server, deployed after a complete setup of the Windows Server OS. Because Microsoft offers both a Core and the full Desktop experience version of Windows Server, footprints of a Hyper-V deployment can be dramatically different. Hyper-V is entirely closed source.
- **Nutanix:** Comprises several components. AHV (Acropolis Hypervisor) is based on CentOS 7 and uses KVM for virtualization. A typical deployment involves the hypervisor followed by the Controller Virtual Machine (CVM), responsible for all management aspects of Nutanix.

## Storage and Supported Storage Types

- **XCP-ng:** Supports local storage, NFS, iSCSI, and HCI storage using XOSAN.
- **Proxmox:** Supports local storage, NFS, Ceph for HCI storage, and many other storage formats.
- **Hyper-V:** Supports all the same storage types as Windows OS, including local storage and shared storage via iSCSI.
- **Nutanix:** Only supports hyper-converged storage from within the cluster itself. No external storage access is supported.

## Backup Solutions

- **XCP-ng:** Built-in backup and restore functionality, with third-party support from Commvault and talks of Veeam ongoing.
- **Proxmox:** Native backup and restore functionality through Proxmox Backup Server, with Veeam integration announced.
- **Hyper-V:** Supported by all major backup vendors, including Veeam, Commvault, Rubrik, and others.
- **Nutanix:** Native support for Veeam, Rubrik, Commvault, HYCU, and others.

## Live Migration, Workload Balancing, and High Availability

### Live Migration
All hypervisors support live migration of virtual machine workloads between different hosts in a cluster, with the exception of Proxmox and LXC containers, which must be shut down before migrating.

### Automated Workload Balancing

- **XCP-ng:** Automatically migrates virtual machine workloads between hosts to balance CPU load.
- **Proxmox:** Does not have built-in automated workload balancing but can use community scripts. Automated workload balancing is on their roadmap.
- **Hyper-V:** Supports workload balancing for both RAM and CPU utilization using Hyper-V Failover Cluster Manager.
- **Nutanix:** Natively supports workload balancing across the cluster.

All four hypervisors also support high availability as a core feature of their clustering and will restart VMs on different hosts in the cluster if a host fails or goes offline.

## User Interface and Experience

- **XCP-ng:** Xen Orchestra GUI is functional but feels dated. A refreshed UI/UX is in development.
- **Proxmox:** The UI/UX is cluttered and needs improvement, though it has great graphing and extensive functionality.
- **Hyper-V:** The worst experience, using the Microsoft Management Console framework, which is dull and fragmented.
- **Nutanix:** Prism Element and Prism Central provide a clean, elegant, and highly functional user experience.

## Minimum Hardware Requirements

- **XCP-ng:** Requires a 64-bit x86 CPU (1.5GHz minimum, 2GHz recommended), 2GB of RAM (4GB recommended), and 46GB of disk space (70GB recommended).
- **Proxmox:** Requires a 64-bit x86 CPU, 1GB of RAM (2GB recommended), and does not list a minimum storage requirement.
- **Hyper-V:** Requires a 64-bit x86 CPU (1.4GHz minimum), 2GB of RAM, and 32GB of storage space for the OS install.
- **Nutanix:** Requires Intel Sandy Bridge or newer, or AMD Zen or newer, 32GB of RAM, and specific storage requirements for cold and hot storage tiers.

## Cost and Support

- **XCP-ng:** Vates offers VMS Pro ($1000 per host per year) and VMS Enterprise ($1800 per host per year).
- **Proxmox:** Four support tiers: Community (€100 per socket per year), Basic (€340 per socket per year), Standard (€510 per socket per year), and Premium (€1020 per socket per year).
- **Hyper-V:** Pricing depends on the Windows Server version (Datacenter: $6,155 USD, Standard: $1,069 USD). Support is additional.
- **Nutanix:** Pricing is not publicly disclosed, sold through VARs, and available as a turn-key deployment or software-only solution.

## Final Thoughts

The hypervisor and on-premise virtualization space has seen incredible changes. Choosing the right hypervisor depends on your specific needs and priorities:

- **XCP-ng:** Best for those familiar with VMware.
- **Proxmox:** Ideal for older hardware and LXC container support.
- **Hyper-V:** Suitable for Windows-centric environments.
- **Nutanix:** Offers a stellar user experience but is limited to HCI.
