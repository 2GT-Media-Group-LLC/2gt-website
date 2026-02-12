---
layout: post
title: "The Ultimate Storage Server Upgrade for 2025"
date: 2025-01-16
author: 2GT_Rich
tags: [storage, hardware]
banner:
  image: https://img.youtube.com/vi/ql-G2T2iDEE/maxresdefault.jpg
---

![](//youtube.com/watch?v=ql-G2T2iDEE)

If you’ve been following 2GuysTek for any length of time, you know how much I enjoy unique and interesting servers in my homelab. From my first dual-node Supermicro server to the quad-node setup, I’ve always sought out hardware that brings something special to the table. This year is no different, as I’ve kicked off 2025 with a significant upgrade to my storage server, SuperSAN. Enter the Supermicro SSG-6029P-E1CR24L—a beast of a machine with some serious hidden tricks up its sleeve.

## First Impressions and Specs

At first glance, the server may not seem all that special. It’s a dual-socket Intel Xeon Gold 6130 system with 192GB of DDR4 ECC memory. But take a closer look, and you’ll find it has a secret weapon: 12 additional internal drive bays, bringing the total to 24 3.5″ bays in a 2U chassis. Add to that two 2.5″ SSD bays in the rear for OS storage, and you’ve got a truly unique setup.

I picked up this server on eBay for $1,100 shipped. For that price, you get a chassis loaded with features, two CPUs, and a generous amount of ECC memory. If you’re looking for a similar deal, you can find the listing [here](https://ebay.us/mByzAp).

## Why This Server?

There were a few key factors that made this server a must-have for my homelab:

- **Massive Storage Capacity**: 24 3.5″ drive bays in a 2U form factor.
- **Modern Platform**: Built on Intel’s Gen1 and Gen2 Scalable architecture.
- **High-Speed Connectivity**: Equipped with a Supermicro SIOM card featuring dual 25Gb SFP28 and dual 10Gb Base-T ports.
- **Upgrade Potential**: The Xeon Gold 6130 CPUs are great for storage duties, but there’s room for future CPU and RAM upgrades.

With my older SuperSAN running on 9-year-old Intel Xeon E5-2680v4 CPUs, this was the perfect time for an upgrade. The new server offers more storage capacity, improved performance, and modern connectivity options.

## Design and Build

The Supermicro SSG-6029P-E1CR24L is deeper than your average 2U server, measuring 34 inches in length. It’s designed for standard 19” racks, but be sure your cabinet can accommodate its depth. Here’s a quick rundown of the hardware:

- **Front**: 12 visible 3.5” drive bays.
- **Hidden**: 12 internal 3.5” bays.
- **Rear**: 2 hot-swap 2.5” SATA bays for OS drives.
- **Networking**: Supermicro SIOM with dual 25Gb SFP28 and dual 10Gb Base-T.
- **Power**: Dual Titanium 1600W PSUs (1000W at 120V).
- **Mainboard**: Supermicro X11DSC+, based on the Intel C621 chipset.
- **CPUs**: Dual Intel Xeon Gold 6130 (16 cores/32 threads each).
- **RAM**: 192GB of DDR4-2666 ECC, expandable to 6TB.

## The Challenge of Maintenance

Accessing the internals of this server is not for the faint of heart. The power distribution sits atop the mainboard, CPUs, and RAM, requiring the removal of PSUs, shrouds, airflow guides, and numerous power cables to reach the components. It took me about eight minutes—and several attempts—to fully expose the mainboard.

While this design ensures efficient power distribution and cooling, it’s not ideal for those who need frequent access to internal components. Consider this if you’re thinking about purchasing one of these systems.

## Deployment Plans

This server will eventually replace SuperSAN as my primary storage system. Here’s the configuration plan:

- **Storage**: 24 x 7.68TB SAS3 SSDs, yielding roughly 169TB of usable space.
- **OS Drives**: 2 x 480GB Intel Datacenter SATA SSDs in a mirrored pair.

For those considering similar builds, note that the Supermicro caddies do not support 2.5″ drives out of the box. I used 3D-printed brackets to adapt my SSDs, saving significant costs. You can find the STL files [here](https://www.printables.com/).

## What’s Next?

Before deploying this server in production, I plan to test it extensively. While TrueNAS SCALE is the leading contender for the OS, I’m also considering building a custom solution using Debian or Ubuntu with Cockpit. If you have suggestions, drop them in the comments below!

And one last thing—this server needs a name! SuperSAN served me well, but it’s time for a new moniker. Got any ideas? Let me know!

## Final Thoughts

The Supermicro SSG-6029P-E1CR24L is a fascinating piece of hardware. Its unique design, modern platform, and incredible storage capacity make it a standout choice for homelabbers and IT pros. While maintenance isn’t straightforward, the benefits far outweigh the challenges for those who love tinkering with enterprise-grade equipment.

If you’re interested in building your own version of this server, check out the links in the description. And don’t forget to subscribe to 2GuysTek for more homelab content!

Thanks for reading, and happy homelabbing!
