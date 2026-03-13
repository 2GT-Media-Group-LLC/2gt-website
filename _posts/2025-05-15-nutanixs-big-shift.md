---
layout: post
title: "Nutanix’s Big Shift: Disaggregated Storage Is (Finally) Here – But There’s a Catch"
categories: markdown
date: 2025-05-15
author: 2GT_Rich
tags: [nutanix, storage, virtualization]
---

![](https://youtu.be/48unJcB_Nuc)

Fresh off the showroom floor at Nutanix .NEXT 2025 in Washington, D.C., the worst-kept secret in the virtualization world has finally been confirmed: Nutanix is officially embracing disaggregated storage by allowing select third-party storage vendors into its once tightly controlled ecosystem. And the first major player through the gates? Pure Storage.
This is a monumental change for Nutanix—an organization long known for its firm commitment to the Hyperconverged Infrastructure (HCI) model. But, as with all things in enterprise IT, the real story lies in the details. Let’s break it down.

## From HCI-Only to Selective Openness
At this year’s .NEXT conference—Nutanix’s equivalent of VMware Explore or Microsoft Ignite—the company unveiled a major strategic pivot: official support for disaggregated storage in its virtualization stack. This is not the first time Nutanix has tested the waters. In fact, [Dell PowerFlex](https://www.dell.com/en-us/dt/storage/powerflex/index.htm) previously received early (albeit limited) integration.
But this time, the spotlight is on [Pure Storage](https://www.purestorage.com/company/newsroom/press-releases/nutanix-pure-storage-partner-to-deliver-greater-customer-choice.html) as Nutanix’s premier partner in this new venture. The integration isn’t just a symbolic nod to open ecosystems—it’s a deliberate technical partnership focused on performance, modernization, and (we assume) market differentiation in a post-VMware world.

## The Fine Print: Not All Pure Storage Arrays Are Welcome
Let’s not get too excited just yet.
At launch, only Pure Storage X and XL arrays are officially supported. That means if you’re running any of Pure’s C, E, or S series arrays, you’re out of luck. Despite being highly capable (and all-flash), these arrays are currently excluded from Nutanix’s compatibility list.
To make matters more complicated, the supported compute platforms are also limited. Only third-party vendors—Dell, HPE, Cisco, and Lenovo—can participate in this initial rollout. If you’re running Nutanix software on Nutanix’s own NX hardware, you won’t be able to use disaggregated Pure Storage arrays.
That’s right. Nutanix’s own servers are not supported for their own disaggregated storage solution. Bizarre? Definitely. Strategic? Probably. Beneficial to the customer? Not so much.

## Why the Hardware Exclusion?
This decision left many (myself included) scratching their heads. Why exclude Nutanix-branded hardware from this groundbreaking architectural evolution?
The answer, based on conversations I had on-site, is murky at best. Some Nutanix employees hinted that support might come later. Others flat-out denied any plans to include NX hardware. My take? This is likely a move to create differentiation and offer partner vendors like Dell and HPE a reason to stick around.
Nutanix hardware is often sold at cost, with no real markup, meaning third-party vendors have a hard time competing on price. By limiting disaggregation to partner hardware, Nutanix may be giving those vendors an edge as a form of ecosystem appeasement.
But as a customer? It’s frustrating.

## Let’s Talk Tech: NVMe over TCP Only
Nutanix’s disaggregated storage architecture is based entirely on NVMe over TCP—a fast, modern protocol that enables high-throughput, low-latency connectivity between storage arrays and compute nodes over standard IP networks.
There’s no iSCSI. No NFS. No Fibre Channel. Just NVMe/TCP.
From a performance standpoint, I get it. Nutanix wants to preserve the high IOPS expectations their customers are used to in a tightly integrated HCI environment. NVMe over TCP allows them to offload storage while still delivering near-local disk performance.
But it’s still a bold, potentially alienating decision. Not every shop needs that level of performance. Some customers may have perfectly good arrays that support iSCSI or NFS but are now effectively locked out unless they upgrade—often unnecessarily.

## How It Works: A Whole New Storage Model
Let’s dive into the architecture.
Instead of traditional shared storage (like a big shared NFS mount that multiple hypervisors access), Nutanix is implementing something closer to VMware VVOLs. Each VM running on Nutanix’s AHV hypervisor gets its own dedicated virtual volume provisioned from the Pure Storage array.
These volumes are self-contained and follow the VM between hosts. That means you don’t have a single large pool of shared storage, but rather an architecture where each VM is its own unit of storage provisioning. This could have interesting implications for cloning, backup, and mobility.
It’s different. It’s modern. And for some use cases, it could be a huge win.

## What About Dell PowerFlex?
Nutanix’s previous attempt at disaggregated storage came via Dell’s PowerFlex. But the architecture there was more of a hack: the compute nodes used their network HBAs to mount remote disks and trick AHV into thinking they were local.
That may change. Nutanix and Dell are reportedly working on evolving that approach into a native, direct integration similar to what Pure is doing now. However, as of today, the implementations are completely different.
So if you’re comparing vendors, don’t assume feature parity between PowerFlex and Pure Storage.

## Who’s Next?
Nutanix stayed tight-lipped when I asked about future partners. But if you look at the [Gartner Magic Quadrant for Storage](https://www.gartner.com/en/research/methodologies/magic-quadrants-research), you can probably guess the most likely candidates—assuming those vendors support NVMe over TCP.
If I had to bet, we’ll see support for another major player before year’s end.

## Final Thoughts: The Good, the Bad, and the Disappointing
Let’s start with the good.
Nutanix embracing disaggregated storage is a huge win for customers and a smart strategic move. In a world where VMware’s future feels uncertain, customers want flexibility, especially in how they source and scale their infrastructure. Nutanix is showing they’re willing to open the gates—at least a little.
Now, the not-so-good.
The execution here feels too limited and too bespoke. Every integration appears to be a custom implementation, not a standard protocol-based solution like most virtualization vendors support now. That’s going to slow adoption, limit compatibility, and raise costs for both customers and partners.
And the NVMe-only approach? It alienates customers who don’t need bleeding-edge performance but still want the benefits of disaggregation. Not every workload is latency-sensitive. Give us iSCSI, NFS, or something standardized. Let the customer decide what performance level they need.
Lastly, the lack of support for Nutanix’s own hardware is just baffling. It creates artificial constraints in a space that should be all about choice and flexibility. I hope this changes—and soon.

## In Summary
Nutanix’s move to support disaggregated storage is an important evolution in the virtualization market. It’s a signal that even the most HCI-focused vendors understand the value of freedom of infrastructure.
But if Nutanix really wants to compete in the new post-VMware world, they need to go further:
- Open support for standard protocols
- Remove hardware vendor lock-in
- Treat disaggregation as a core feature, not a premium partnership
This is a great first step. But if Nutanix wants the future, they need to walk the walk.

## Watch the Full Breakdown
👉 Check out our full video on this topic here: [Watch on YouTube](https://youtu.be/48unJcB_Nuc)
🎥 Looking for more virtualization insights? Dive into our [Virtualization Playlist](https://www.youtube.com/playlist?list=PLblpZ56f6Rwq4mRNJ0uecpAGgO_CBHiQ5) for more deep dives, walkthroughs, and honest takes.
💬 Got thoughts? Drop them in the comments or come yell at me on Twitter (@2GuysTek). I want to hear from the IT pros, homelabbers, and engineers in the trenches.
