---
layout: post
title: "The Minisforum NAB9 Plus: The Perfect Starter Homelab Server?"
date: 2025-06-07
author: 2GT_Rich
categories: markdown
banner: https://img.youtube.com/vi/J1Mp87HDl4k/maxresdefault.jpg
tags: [mini pc, hardware, virtualization]
---

![](//youtu.be/J1Mp87HDl4k)

If you’re thinking about building your first homelab or self-hosting environment, you’ve probably asked yourself: *What’s the ideal system to start with?* Big servers are great, but they can be power-hungry, expensive, and take up too much space. Enter the **Minisforum NAB9 Plus** — a mini-PC that just might check all the right boxes for your first homelab server. In this post, I’ll walk you through my experience testing this unit for homelabbing and show you why I think it’s an excellent place to start.

## Disclosure

Minisforum did send me the NAB9 Plus for free. However, this is **not a sponsored post** — they had no say in its content and are seeing it for the first time just like you. As always, these are my own opinions.

## Why I’m Excited About This System

The NAB9 Plus delivers a rare combination of **power**, **storage options**, and **efficiency** — making it a compelling homelab starter system.

Let’s start with the specs:

- **CPU**: 12th Gen Intel Core i9-12900HK  
  - 14 cores / 20 threads  
  - 6 performance cores + 8 efficiency cores  
  - 2.5 GHz base / up to 5 GHz turbo  
  - 45W typical TDP / 115W max TDP
- **RAM**: 32GB DDR4 3200MHz
- **Storage**: 1TB NVMe SSD
- **Connectivity**: WiFi 6E + Bluetooth 5.2

## Size & Connectivity

The NAB9 Plus is compact:

- **Dimensions**: 127mm (W) x 127mm (D) x 51mm (H)
- **Build**: Plastic, but has a premium aluminum look

### Front Ports

- 2x USB 3.2 Gen2 Type-A  
- Combo mic/headphone jack  
- Power button  
- Pinhole reset  

### Rear Ports

- 2x USB 2.0 Type-A  
- 1x HDMI 2.1 (4K @ 60Hz)  
- 1x OCuLink (PCIe 4.0 x4 — great for external GPUs or RAID)  
- 2x 2.5Gb Ethernet  
- 1x DisplayPort 1.4 (4K @ 120Hz)  
- 1x USB 4 Type-C (also supports power delivery)  
- 1x Barrel connector for power  

## Easy Access to Internals

I love the design here — no screwdriver required. Just press down on the two front edges and the lid pops open.

Inside, you’ll find:

- 1TB NVMe SSD  
- WiFi 6E card  
- 2x 16GB DDR4 SO-DIMMs (for 32GB total RAM)  
- SATA connector header (we’ll get to that in a moment)

## Price Point

The NAB9 Plus typically retails for around **$480** on [Amazon](https://amzn.to/3FCTQnl), but I’ve seen it on sale for even less — keep an eye out!


## That OCuLink Port

**OCuLink** is essentially an external PCIe 4.0 x4 connector. While some use it for external GPUs, I think its real value here could be attaching a RAID card and external storage for homelab expansion. A potential topic for a future video!


## Setting It Up as a Homelab Server

For the OS, **Proxmox Virtual Environment (PVE)** is a natural choice — it allows you to run multiple VMs and LXC containers on one system.

Before installing Proxmox, there’s a great feature to take advantage of...

### Additional 2.5" SSD Support

You can add a **2.5" SATA SSD** via the included cable and port on the mainboard. The SSD mounts into the lid itself — a clever design.

I added a **500GB WD Blue SSD** as the Proxmox boot drive so I could save the ultra-fast NVMe storage for my VMs and containers.

Installing it was simple:

1. Plug the SATA cable into the mainboard  
2. Mount SSD into lid  
3. Close it up  

## First Boot & BIOS

Out of the box, the NAB9 Plus ships with **Windows 11 Home** — nice if you want a turnkey Windows PC.

I also explored the BIOS, which is fully modern with a simple GUI and mouse support. Well done, Minisforum.

## Installing Proxmox

The Proxmox install went off without a hitch:

- Booted from USB  
- Selected **500GB SATA SSD** as boot drive  
- Full install completed in about **6 minutes**  

After booting, I accessed Proxmox’s web UI and immediately wiped the NVMe disk to repurpose it for VM/container storage.

I then:

- Initialized the NVMe disk with **GPT**  
- Created an **LVM volume group** named `NVMe`  

Now ready for workloads!

## Performance Testing

### Network Throughput

I ran `iperf3` from an Ubuntu 24.04 LXC container:

- Result: ~**2.3 Gbps** — excellent for the built-in **2.5Gb Ethernet**.

### Disk Performance

#### Read Speed (NVMe SSD)

- Average: **4,202 MiB/s (~4.3 GB/s)** — blazing fast!

#### Write Speed (NVMe SSD)

- Average: **3,570 MiB/s (~3.66 GB/s)** — equally impressive.

## Power Consumption

I tested the system at various loads:

- **Off**: ~1.5W  
- **Idle**: ~12–14W  
- **Peak** (100% CPU + disk IO): ~101W  

Very **energy-efficient** compared to traditional rack servers.


## Final Thoughts

### Pros

- Solid performance for a mini-PC  
- Expandable with an extra 2.5" SSD  
- OCuLink adds flexibility  
- Affordable price point  
- Very energy efficient  
- Simple to work on  

### Cons

- 12th Gen CPU is no longer the latest  
- DDR4 RAM is a generation behind (though cheaper)  
- Lightweight — some might expect a "heavier" feel  

### CPU Core Considerations

The i9-12900HK features a mix of **performance** and **efficiency** cores. You can manually pin VMs to specific cores via **Proxmox CPU affinity** if needed, but for most homelab workloads, this shouldn’t be necessary. For reference, the first six cores of the CPU are the **performance cores**.

## Should You Buy It?

If you’re looking for a compact, affordable, and capable **first homelab server**, the **Minisforum NAB9 Plus** is an excellent option. It’s not going to compete with the big iron servers in my rack — but for starting out, it’s hard not to love this little box. Plus, if you ever change your mind about homelabbing, you can always reinstall Windows and use it as a fast mini-PC!

*Happy homelabbing! If you enjoyed this post, be sure to check out more of my homelab content on [2GuysTek on YouTube](https://www.youtube.com/@2GuysTek).*


