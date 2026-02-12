---
layout: post
title: "Evaluating Nutanix as an Alternative to VMware ESXi"
date: 2024-05-21
author: 2GT_Rich
tags: [VMware, Nutanix, virtualization]
banner:
  image: https://img.youtube.com/vi/rMzbuxd5b_Y/maxresdefault.jpg
---

![](//youtu.be/rMzbuxd5b_Y)

Welcome, friends! If you can believe it, this is the fourth installment of our series on evaluating alternative hypervisors now that Broadcom is making VMware untenable for homelabs and small to medium businesses. In the last three videos, we focused on XCP-ng, Proxmox, and Hyper-V as alternatives to ESXi. If you haven’t seen those videos, check them out.

## Introduction to Nutanix

Now it’s time to focus on the fourth most requested hypervisor, Nutanix. Let’s get to it!

Hey there, homelabbers and self-hosters, Rich here. This video is the fourth in our series focused on evaluating your options if you’re coming from the world of VMware and ESXi, with this video, in particular, being focused on Nutanix AOS. As always, I’ll be looking at this from a VMware perspective and sharing my thoughts and opinions along the way. Let’s start with some background on Nutanix and the history of the product.

## The Nutanix Origin Story

- **2009:** Nutanix was founded by Dheeraj Pandey, Mohit Aron, and Ajeet Singh with the vision of simplifying data centers by integrating compute, storage, and virtualization into a single solution.
- **2011:** Nutanix launched its first product, Nutanix Complete Cluster, marking its entry into the hyper-converged infrastructure (HCI) market. This innovative solution combined compute and storage into a single, scalable appliance.
- **2015:** Nutanix introduced its own hypervisor, AHV (Acropolis Hypervisor), based on KVM and running on CentOS 7. AHV offered a cost-effective alternative to VMware and Hyper-V, emphasizing software-defined solutions.
- **2016:** Nutanix went public with one of the most successful IPOs of the year, underlining the company’s growth and the market’s confidence in its technology.
- **2017 & 2018:** Nutanix broadened its portfolio, introducing Nutanix Calm for application automation and orchestration and Xi Cloud services for hybrid cloud environments. The company also acquired several firms to enhance its cloud capabilities.
- **2020:** Nutanix transitioned to a subscription model for licensing, intending to provide more flexibility to customers and predictable revenue for investors. However, this shift resulted in lower upfront revenue compared to traditional licensing. 2020 also saw a leadership change, with co-founder Dheeraj Pandey stepping down as CEO, succeeded by Rajiv Ramaswami.
- **2023 and Beyond:** Nutanix continues to expand its cloud offerings, introducing new services to support multi-cloud and hybrid cloud strategies. With VMware’s recent ownership change, Nutanix is poised to gain market share, especially through partnerships like the one with Cisco.

## Comparing Nutanix to VMware ESXi

### Architecture

Both Nutanix and VMware ESXi are type-1 hypervisors but differ in deployment methodologies. ESXi is lightweight, running from RAM with a closed-source kernel. Nutanix AOS and AHV are based on CentOS 7, using disk storage for various operational needs, with closed-source intellectual property.

### Performance

Performance between the two hypervisors is nearly equivalent. ESXi supports up to 896 logical CPUs and 24TB of RAM per host, while Nutanix AHV does not have published maximums for CPU cores or RAM.

### Usability

- **VMware ESXi:** Uses a web-based HTML5 GUI for host and VM management, with extended functionality requiring vCenter.
- **Nutanix:** Uses Prism Element, an HTML5 management interface, supporting host and VM management, clustering, live migrations, and DR functionality. Prism Element can manage all aspects of HCI, similar to vCenter but without the need for additional software.

### Features

- **VMware ESXi/vCenter:** Offers advanced features like distributed resource scheduling, high availability, fault tolerance, vMotion, and API control, requiring additional licensing.
- **Nutanix:** Provides clustering, live workload migrations, high availability, hyper-converged storage and networking, API control, and workload balancing out of the box.

### Scalability

- **VMware ESXi:** Manages thousands of VMs, scaling clusters up to hundreds of hosts with many thousands of VMs.
- **Nutanix:** Supports up to 32 nodes per cluster, with the only limit on VMs being the physical CPU and memory. Nutanix is designed solely for HCI and does not support external SAN or NAS storage.

### Support

- **VMware:** Offers extensive professional support, training, certifications, and a large community.
- **Nutanix:** Provides professional support, training, certifications, a public knowledge base, and a large community.

### Cost

- **VMware ESXi:** Recent licensing changes make it less accessible, with the free hypervisor no longer available as of February 12, 2024.
- **Nutanix:** Offers subscription-based licensing on a per-core basis. Nutanix CE (Community Edition) is available for free, allowing homelab users to experiment and learn with some limitations.

## Real-World Examples and Interface Comparisons

### Console Interfaces

- **VMware ESXi Console:** Provides basic host information and minimal management functionality.
- **Nutanix AHV Console:** A typical Linux console with most management done via the web GUI.

### Management GUIs

- **VMware ESXi:** Features a web management UI with detailed host, VM, storage, and networking information.
- **Nutanix Prism Element:** An all-encompassing management solution for HCI, similar to vCenter, but integrated into the Nutanix platform.

## Likes and Dislikes

### What I Like

- **User Interface:** Nutanix Prism Element is well-designed with clear overviews and detailed sub-tabs. It includes a fun touch with a built-in web-based game called 2048.
- **Detailed Stats and Logging:** Nutanix provides deep insights into performance and operations, rivaling what you’d need VMware Aria Operations for in a VMware environment.

### What I Dislike

- **No Support for External SAN Storage:** Nutanix’s strict HCI approach limits flexibility for those with traditional SAN setups.
- **Lack of Passthrough:** Nutanix doesn’t support passthrough for devices, making it less suitable for certain advanced setups.
- **Complex Setup:** Installing and setting up Nutanix AHV and AOS involves multiple steps and requires SSH and command-line interaction.
- **High Resource Requirements:** The CVM (Controller Virtual Machine) has significant RAM requirements, starting at 16GB and potentially up to 64GB, which can be burdensome for smaller setups.

## Conclusion

Nutanix offers a robust alternative to VMware ESXi, especially for businesses already invested in HCI. However, certain limitations, like no external SAN support and complex setup, may be deal-breakers for homelab enthusiasts. Despite these caveats, Nutanix is a strong contender in the hyper-converged infrastructure space.
