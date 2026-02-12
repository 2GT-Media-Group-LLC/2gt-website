---
layout: post
title: "Staying Ahead in the Virtualization Landscape: Late Spring 2024"
date: 2024-06-05
author: 2GT_Rich
tags: [virtualization, XCP-ng, Proxmox, Hyper-V, Nutanix, VMware]
banner:
  image: https://img.youtube.com/vi/SZWiQOIsY38/maxresdefault.jpg
---

![](//youtube.com/watch?v=SZWiQOIsY38)

At the end of our “Life after VMware” series, we started to get comments and suggestions that we should regularly release videos on the state of the virtualization landscape since it’s changing so rapidly that it’s hard for a lot of people to keep up. So, we’ll be doing these videos every few months to check in and see what’s changed and evolved in the virtualization landscape and hopefully keep everyone up to date as well. Let’s get to it!

## What’s New in Virtualization?

Hey there homelabbers, self-hosters, IT pros, and engineers, Rich here. Let’s get down to business on what’s happened lately in terms of features, changes, updates, and other news in the infrastructure virtualization space. I will endeavor to provide you with useful information in these videos from your favorite hypervisors like XCP-ng, Proxmox, Hyper-V, Nutanix, and yes…even VMware by Broadcom. If I miss anything, or if you have any options/thoughts/etc. toss them in the comments below and share the knowledge with others, since we’re all in this together!

## XCP-ng News and Updates

May has been a relatively quiet month in terms of news out of Vates and XCP-ng; however, April was pretty busy. XCP-ng announced the availability of its first SMAPIv3 driver in preview, specifically designed for ZFS-based storage repositories. This driver, included in XCP-ng 8.3, allows users to create and manage ZFS storage repositories with greater flexibility and efficiency. April also saw a security update for XCP-ng 8.2 that fixed host crashes, corrected logic to prevent unauthorized memory access by attackers, and mitigated Native Branch History Injection, which is an evolution of the Spectre vulnerability. If you haven’t patched, get on it.

One thing to note, the SMAPIv3 driver is only available in the beta XCP-ng 8.3, and not XCP-ng 8.2 LTS. Now, let’s dig into Proxmox.

## Proxmox News and Updates

Proxmox has released version 8.2 of its Virtual Environment, featuring an import wizard for migrating VMware ESXi guests, automated bare-metal installation, and a backup fleecing feature to improve VM performance during backups. This update also includes a modernized firewall using nftables, device passthrough via GUI, advanced backup settings, and support for custom ACME-enabled Certificate Authorities. The release enhances ease of use and performance, making it simpler to manage virtual environments. And anything that makes that GUI better is worth it in my book – go get it installed!

In other huge news, Veeam announced they will officially support Proxmox VE. Support for this virtualization platform has been a popular request from Veeam’s existing small and medium-sized business (SMB) customers and service providers. This is big news in terms of opening up Proxmox VE to businesses who rely on Veeam for their backup, disaster recovery, and immutability strategies and will go a long way to making Proxmox a viable alternative virtualization platform.

Two giant leaps forward for Proxmox – first, the release of 8.2 in late April brought with it their import wizard for VMware ESXi, something we’ll be trying out in the future, so look for videos on that. And then the announcement from Veeam that they’ll be releasing native support for PVE. This is a big win for Proxmox and Veeam.

## Nutanix News and Updates

The biggest news landed on May 21st. Nutanix and Dell announced a collaboration to enhance hybrid multicloud solutions, aiming to streamline IT operations and improve resiliency. The partnership will introduce two key solutions: a hyper-converged appliance combining Nutanix Cloud Platform and Dell servers, and Nutanix Cloud Platform for Dell PowerFlex, allowing independent scaling of compute and storage. Put simply – this is the first time we’ve seen Nutanix decouple storage from the compute stack.

Just in case I’m using too much business-speak here, let me clarify. When you go with Nutanix, you buy into hyperconverged 100%. You don’t get to use your sexy Pure Storage array or any SAN for that matter for your virtualization in Nutanix – but this announcement is saying, that’s not always the case now, at least if you buy into Dell PowerFlex running Nutanix on it. It looks like we might be seeing the ice thaw on Nutanix’s no SAN or external storage doctrine, and believe me folks, that’s really big news!

In other news, AHV, or Acropolis Hypervisor, turns 10 years old this year. AHV still looks to be running on top of CentOS7 though, and with the official end of CentOS coming later this year, I suspect we’ll see something from Nutanix in the near future announcing what base OS AHV and AOS as a whole moves to.

I’ve been getting a lot of questions and comments about Nutanix’s use of CentOS, and the short answer is I don’t know when they’ll move or what they’ll be moving their platform to, but I suspect we’ll see a lot of buzz about it soon. If any of you know and can share, drop it in a comment below.

## Hyper-V News and Updates

Hyper-V rarely sees a lot of big independent announcements since it’s always coupled with Windows Server announcements. So, the best I have for you was this announcement about Windows Server 2025 preview and a note about the massive improvements to performance and scalability in Server 2025. Stating:

Windows Server 2025 Hyper-V Virtual Machine Maximums:

- **Maximum Memory per VM**: 240 Terabytes* (10x previous)
- **Maximum Virtual Processors per VM**: 2048 VPs* (~8.5x previous)

*Requires Generation 2 VMs

Microsoft is on a mission to bridge the gap between on-premise virtualization and hybrid cloud deployments, and Hyper-V is playing a pivotal role in that. Server 2025 obviously isn’t out yet, but it’s good to see that Microsoft is continuing to develop and improve Hyper-V.

## VMware by Broadcom News and Updates

First, let’s start with some good news. On May 13th VMware announced that Fusion Pro and Workstation Pro are now available for free for personal use. Both software can be used free for personal use, but commercial use still falls under a subscription contract. Fusion Pro is desktop virtualization for MacOS, both Intel and ARM CPU, and Workstation Pro is for x86 Windows environments.

While it’s always nice to get something for free, this freebie has felt like a bit of a consolation prize for the loss of the ESXi hypervisor which many of us have been using in our homelabs for over a decade. Whether this fills the hole left by the loss of ESXi or not will remain to be seen, typically trading a dedicated type-1 hypervisor for a type-2 software package brings poorer performance and user experience, but having a good desktop hypervisor is always a good thing.

In more negative news, Computershare, a large financial products and services company in Australia announced that it’s pulling VMware out of their organization to the tune of 24 THOUSAND VMs. The significant licensing cost increase has forced the company to reconsider its virtualization strategy, moving away from VMware to more cost-effective alternatives which, at least, from the article, looks to be Nutanix AHV.

We’ve been hearing from Broadcom from the beginning that their strategy has been to focus on big enterprises, and news like this doesn’t reflect well on the realities of Broadcom’s strategy. At a minimum, this is a bit of egg on the face of Broadcom and Hok Tan, but big news like this also calls into question Broadcom’s indifference to its customer’s complaints is netting higher attrition than expected.

Two more things worth mentioning this round, on May 5th Broadcom shutdown the VMware customer connect website. Now all support for VMware customers, including accessing your entitlements like keys, downloads, and so on, have to be done via the Broadcom support portal. Hopefully, you created your account ahead of the cutover. Also, Broadcom has moved VMware’s knowledge base, which were fully public-facing, to within the Broadcom support portal. I suspect this will have a significant effect on google search results when looking for answers to issues online.

This is my personal take here, but the Broadcom support portal is a nightmare to find things that are VMware related. For example, when you do get logged in, you’re dropped into the ‘Mainframe Software’ section, which has nothing to do with VMware and is confusing. Broadcom knows what I’m licensed for; they should put me in that area when I log in. To get to the VMware downloads, one has to head over to the ‘VMware Cloud Foundation’ section, which again is also confusing since I’m not a VCF customer. It’s a pretty terrible user experience, but then again – so is dealing with VMware by Broadcom.

## Tell us what you think!

This is new for us here at 2GT, and we want to make sure we’re covering the hypervisors you care about for your homelab, and your job so please don’t hesitate to let me know what we’re missing and what more you’d like to see!
