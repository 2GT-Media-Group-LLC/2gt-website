---
layout: post
title: "Will €30 Million Change XCP-ng? I Asked the CEO"
date: 2026-11-22
categories: [Virtualization, Infrastructure, Homelab]
tags: [XCPng, XenOrchestra, Vates, Xen, Proxmox, VMware, Broadcom, OpenSource, Virtualization, DigitalSovereignty, Veeam, Interview]
description: "Vates just raised €30 million in its first outside funding round. I sat down with CEO and co-founder Olivier Lambert to ask what that means for XCP-ng and Xen Orchestra, plus digital autonomy, XCP-ng 9, ARM and RISC-V, Veeam, AI-driven vulnerability reports, and XCP-ng vs. Proxmox."
banner:
  image: https://img.youtube.com/vi/vQxSnIDLLNk/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/vQxSnIDLLNk)

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here!

In early October, **Vates**, the company behind **XCP-ng** and **Xen Orchestra**, announced it had raised **€30 million** in its first outside funding round. And if you're anything like me, your first reaction wasn't "congratulations!" It was "uh oh."

We've all watched what happens to open source projects once the money shows up. The license changes, the "community edition" gets a little smaller every release, and one day the thing you built your homelab on is behind a paywall.

So I did the only reasonable thing: I sat down with **Olivier Lambert**, CEO and co-founder of Vates, and asked him point blank what the catch was. He didn't dodge it. This post is a high-level rundown of what we talked about. The full hour-long conversation is in the video above, and I really recommend watching it, because Olivier was incredibly open and generous with his time.

---

## From "Where Are My VMs?" to a 150-Person Company

Olivier started Xen Orchestra back in 2009 as a summer side project. He was a sysadmin, and there was no decent web UI for Xen that could answer a simple question: *which host is my VM on?* Vates came along a few years later as an open source consulting company, and when he came back to that "where are my VMs" problem around 2012 or 2013 with his co-founders, they realized it wasn't just him. Lots of people wanted the same thing.

Backup wasn't even on the radar back then. People asked to export a VM, then to schedule the exports, and suddenly it was a backup product. That kind of feature pile-up, built on a UI that was never designed for it, is a big part of why the **Xen Orchestra 6** redesign happened. Then in 2018, when Citrix changed its model for XenServer, they forked it, and **XCP-ng** was born.

Today Vates is **around 150 people**, up from about 70 a year ago.

## The €30 Million Question

This is the part I really wanted answered. Why take outside money after a decade of being self-funded? Olivier gave three reasons:

1. **The Broadcom window.** He sees the next four or five years as the period where people move off VMware. And once someone migrates, they generally don't migrate again. Vates needed to go faster than it was going.
2. **Credibility.** Investors putting serious money in is a signal to the market, on top of things like the Gartner Magic Quadrant listing.
3. **Growing up as a company.** Doubling headcount comes with responsibility to the people who work there. Olivier was candid that, like a lot of European companies, Vates was very product-focused and lagging on marketing and sales. The funding lets them do all of it at once, instead of picking one.

The part that matters most for us, though, is *how* they took it. Because Vates was already profitable and growing, it didn't *need* the money, so it got to pick its investors. They went with **European VCs**. Olivier said the US VCs they talked to wanted a majority stake and to burn a lot of cash fast. Instead, **the three co-founders kept majority ownership** of the company.

## Is XCP-ng Staying Open Source?

His answer, in short: look at the track record, and remember how XCP-ng got here.

Vates *is* the proof that a fully open source project can be forked when its steward stops serving its users. They did exactly that to XenServer. Olivier pointed out that the warning signs are usually visible early, and you can spot them: the model shifts to open core, the "open" part shrinks, code for some features stops getting published. We saw it with XenServer, and we've seen it more recently with projects like MinIO.

Because XCP-ng is fully open source, someone could fork Vates the same way, and he knows it better than anyone. Add in that they picked investors who were already on board with open source, and that the founders hold the majority of shares, and he says there's no reason for that to change. His advice if you're worried? Keep an eye out for those signs. If it ever happens, it'll be obvious.

I'll be honest, that's a better answer than I expected, and I appreciated how directly he gave it.

## Digital Sovereignty, or "Digital Autonomy"

Olivier actually prefers the term **digital autonomy**. Sovereignty is a word for states, he said. For a company or a person, it's about **having the choice**: deciding what you build and where you go, without anyone twisting your arm because you depend on their software.

A few points from this part of the chat that stuck with me:

- **It's a spectrum**, not a hard line between "European vendor good, US vendor bad."
- **Open source is a good protection, but it's not enough on its own.** If a vendor just consumes open source without contributing much, do they really control it? Vates chose the hard, expensive path: it's the **fourth-largest contributor to the Xen project**, so it controls the whole stack from the hypervisor up to the orchestration layer.
- **Diversity is resilience.** One of the big lessons from the Broadcom mess, on both sides of the Atlantic, is that leaning on one near-monopoly vendor means you get hit hard when something changes. Spreading your trust across vendors is good practice no matter where they're based.

And for those of us in North America: the US and Canada already make up **about half of Vates' revenue**, and they've been the main growth driver for the last two years. Part of the funding is going toward a real physical presence here. About time!

## What's Coming: XCP-ng 9, ARM, RISC-V and TwinStore

- **XCP-ng 9** is a new foundation more than a revolution: a modern Dom0 kernel, better drivers, and the base that a lot of future features need. Olivier said to expect a more widely distributed test version by the end of this year, with GA next year.
- **ARM and RISC-V** are where 9 really matters. Vates already has demonstrators running the whole XCP-ng platform (not just Xen) on ARM and RISC-V CPUs. It's early, but it's real, and it fits that diversity theme of not betting everything on x86.
- A lot of good stuff **doesn't have to wait for 9**. **TwinStore**, which gives you two-node HA without shared storage, is already in tech preview on 8.3. It's built for places like retail chains that ran hundreds of two-node VMware clusters. Micro-segmentation and Kubernetes features are also on the way.

## Veeam: Partner, Not Competitor

As I've said plenty of times, backup is usually the long pole in the tent for enterprises trying to leave VMware. That makes the **Veeam 13.1** integration a big deal. Olivier credited Veeam for being one of the first big backup vendors to realize that virtualization is going to be heavily fragmented, and that a backup vendor that supports many platforms becomes the customer's escape hatch.

So does Xen Orchestra's built-in backup compete with Veeam? He says no, and the line is pretty clear:

- **Xen Orchestra only backs up XCP-ng.** Veeam's whole value is working across many platforms.
- **Xen Orchestra backs up VMs, full stop.** It's never going to learn how to restore a single Exchange mailbox or an Active Directory object. That's application-aware backup, and that's Veeam's territory.

The two teams even share a chat room, so Vates' backup engineers can help Veeam's QA folks directly. That's not something you'd do with a competitor.

He also mentioned the **[Vates Alliance Network](https://vates.tech/en/vates-alliance-network/)**, a hub for partner integrations, success stories and white papers. If you're coming from VMware and wondering whether your expensive storage array will work, the answer is probably there. Partners include names like DataCore, AMD, Storware, Stormshield, Rubrik and Commvault.

## AI: Faster R&D, and a Flood of Vulnerability Reports

On AI workloads, Olivier sees it mostly as an opportunity: more machines means more XCP-ng installs. Most AI setups he sees pass whole GPUs through to one big VM rather than slicing them up, though Vates is still working on vGPU support across GPU brands for cloud providers.

The bigger impact is on how Vates builds things. Prototyping an idea used to take months of benchmarks and hardware. Now an LLM session can rent a machine, install XCP-ng on it and test an idea almost immediately. Olivier says that has already turned up bottlenecks that will **double performance in some parts of the platform** before the end of the year.

The flip side is security. Researchers using AI are finding issues **far faster** than before. A year ago Vates shipped roughly **one security update a month**. Over the last six months it's been closer to **three a month**. They're using LLMs to help with triage, reproduction and writing security advisories, but it's more demanding than it's ever been. Olivier's long-term take is optimistic, though: AI is an equalizer. A project the size of Xen can now get to a level of polish and test coverage that used to take billions in R&D budget. We're just in the painful transition right now.

## XCP-ng vs. Proxmox

I had to ask. Here's Olivier's pitch, paraphrased:

- **They build the whole platform**, including the hypervisor. It's Xen, not KVM, and he thinks its security model is better by design. Xen works a bit like a big microkernel, so a flaw tends to be more contained.
- **It's built for people leaving VMware**, not for Linux experts. Xen Orchestra grew up on top of a platform used mostly by Microsoft shops, so it had to be turnkey for people who don't know (or care about) LVM and SSH. XCP-ng sits in the middle: open source, but with the look, feel and partner ecosystem of a commercial product.
- **They work in the open.** Xen Orchestra lives on GitHub, not mailing lists, and he joked that it's probably a lot easier to get a conversation with him than with the CEO of Proxmox.

He was really respectful about Proxmox throughout, calling it a very good, Debian-based product that keeps getting better. His bigger point was that too much of the market is "something built on KVM" (Proxmox, Nutanix, HPE). Very few companies build everything from the hypervisor to the orchestration layer. More diversity, including things like FreeBSD's bhyve, is good for everyone.

Fun fact from the interview: Hyper-V is close to Xen in its design, because Xen developers helped Microsoft build it back in the Citrix days.

## Gartner, and What Critics Get Wrong

Vates landed in the **Gartner Magic Quadrant** as a Niche Player, one of 17 vendors chosen out of about 50 considered, and the only French company in it. Olivier also enjoyed pointing out, with a bit of a grin, that the XenServer fork ended up placed *ahead* of Citrix. Next up is growing market share and adding products like micro-segmentation and Kubernetes management, to move up and to the right.

And the thing critics get wrong? Mostly "they're *still* using Xen?" His answer is that people leaving VMware don't care which hypervisor is underneath, as long as it works and it's secure. And people who know Xen's design understand why it's a strength, especially now that new CVEs show up constantly.

---

## My Take

I went into this conversation a little nervous, and I came out of it reassured. The founders keeping control, choosing investors who already back open source, and a clear explanation of the warning signs to watch for is exactly the kind of transparency I wish more vendors offered. Olivier also said he'd be happy to come back and keep us updated on the roadmap, and I'm going to hold him to that.

Huge thanks to Olivier for taking the time, especially with the time zone gap between the US West Coast and France.

Want to dig in yourself?

- **XCP-ng:** [xcp-ng.org](https://xcp-ng.org)
- **Xen Orchestra:** [xen-orchestra.com](https://xen-orchestra.com)
- **Vates:** [vates.tech](https://vates.tech)
- **Vates Alliance Network:** [vates.tech/en/vates-alliance-network](https://vates.tech/en/vates-alliance-network/)

As always, a big thank you to my Patreon supporters and YouTube Members for making videos like this possible. And come hang out in the Discord community to chat with me and a bunch of like-minded homelabbers, geeks and nerds. Let me know there what you think about the funding, and whether XCP-ng is on your shortlist. See you on the next one!
