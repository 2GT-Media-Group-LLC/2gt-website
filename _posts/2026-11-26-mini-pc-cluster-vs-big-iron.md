---
layout: post
title: "I Built a Mini PC Cluster. Here's Why I'm Going Back to Big Iron"
date: 2026-11-26
categories: [Homelab, Infrastructure, Virtualization]
tags: [Homelab, MiniRack, MiniHomelab, Minisforum, MS02, Proxmox, 45Drives, Proxinator, MikroTik, GLiNet, TecMojo, GeeekPi, PowerConsumption, Rackula]
description: "The internet keeps telling me a 10-inch mini rack is the smarter way to homelab. So I built one with three Minisforum MS-02 Ultras and put it head to head with my 45Drives Proxinator on power, features and cost. The results were not what I expected."
banner:
  image: https://img.youtube.com/vi/KukIITMfj58/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/KukIITMfj58)

My homelab is a 42U cabinet full of enterprise gear. It pulls about **1.8 kilowatts** of power, all day, every day. At what I pay for power, that's roughly **$3,300 a year**. It's an expensive hobby.

The internet keeps telling me that a 10-inch mini rack with a few tiny PCs in it is the smarter way to homelab, because it's smaller, cheaper, and quieter. I keep telling the internet that's cool, but I don't think it's worth the trade-offs.

So I built one. And I put it up against my main Proxmox server, watt for watt and dollar for dollar, to find out if I'm the idiot here.

---

## I'm a Big Iron Guy

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here!

If you've followed 2GT for more than five minutes, you know I'm a big iron kinda guy. Big racks, big servers, and unfortunately, big power bills. For me, homelabbing is as much visceral as it is technical. I like full-sized server cabinets, and I like those cabs filled with enterprise servers and network gear. Is that rational? Absolutely not. But here we are.

Mini racks have blown up over the last couple of years, and on pretty much every big server video I make, someone in the comments asks when I'm going to try one. Up until now, I've dodged the question. After all, when you have a cab full of big iron, why would you miniaturize?

The truth is I didn't have a good answer beyond "because I like enterprise gear." If I'm being honest, it was more of an emotional reaction than anything else. Time to fix that.

## The Contenders

**In this corner:** my main Proxmox host, a **45Drives Proxinator VM16 Turbo**.

- AMD EPYC 8324P: 32 cores, 64 threads
- 256GB DDR5 ECC
- NVIDIA Quadro P5000
- 100GbE ConnectX-5
- 16 Intel datacenter SSDs on a pair of SAS HBAs

This one server does *everything* for me. It runs my VMs and Docker containers for both 2GT and my personal homelabbing adventures.

**In the other corner:** three **Minisforum MS-02 Ultras** in a 12U, 10-inch rack. It's smaller, portable, much quieter, and very likely more energy efficient. Very likely.

I'm comparing the two on power consumption, features, and actual cost. Then I'll tell you what it was like to live with the little guy, and whether I'm ready to join the #minihomelab life. But first, let's build it.

---

## The Hardware

### Minisforum MS-02 Ultra

Calling this thing a mini PC is generous. It's **4.8 liters** with a **built-in 350-watt power supply**, and it has a full **PCIe 5.0 x16 slot** with room for a low-profile, dual-slot GPU. So each node gets an **ASUS RTX 5060**.

Each node also gets **64GB of RAM** and **a pair of 512GB NVMe drives**. Across all three hosts, that's **192GB of memory, 3TB of raw storage, and 42 cores**. On paper, that's more cores than the EPYC in my Proxinator. But not all cores are created equal.

I bought the **Core Ultra 5 235HX** version, the entry-level chip in the lineup. Minisforum saves a lot of the server-flavored features for the top-end **285HX** model, which I skipped because it costs roughly double. Here's what the 235HX gives up:

- **No ECC memory.** That's 285HX only, and every DIMM in my Proxinator is ECC.
- **No vPro.** Also a 285HX feature, which means no built-in remote management. That's a real bummer, and I'll come back to it in a second.
- **Two M.2 slots per node instead of four.** The 285HX also gets 25GbE. I get 10GbE and 2.5GbE copper.
- **Not all 42 cores are the same.** Each 235HX has 14 cores and 14 threads: six performance cores and eight efficiency cores. The EPYC's 32 cores are all identical Zen 4c cores, and each runs two threads.
- **Every node lives inside a 350-watt budget.** The 235HX is rated at 80 watts sustained and 100 watts boost. The RTX 5060 is rated at 145 watts on a single 8-pin connector. Add RAM, drives and NICs, and that power supply doesn't have a lot of room to spare.

None of that is a deal breaker. But it's the stuff you never have to think about with an enterprise rack server, like my Proxinator with its dual redundant 1,000-watt power supplies.

### Out-of-Band Management: GL.iNet Comet X KVM

One big advantage of enterprise gear is **out-of-band management**, like iDRAC, iLO or IPMI. It's a standard feature on servers, and it's critical whether the box is in your garage or on the other side of the world. Remember that missing vPro? This is the gap it would have filled. Desktop PCs don't usually have anything like it, and plugging a keyboard, mouse and monitor into the back of the rack every time something goes wrong just won't do.

Enter the **GL.iNet Comet X KVM**, and you have to admit it feels like it was made for mini racks. It supports up to four devices and it's network-connected, so you can reach the consoles of your connected PCs remotely. It does most of what we actually use a server's out-of-band management for.

It isn't IPMI, though. Out of the box, it can't hard power a system on or off. GL.iNet sells an ATX power board and a little button-pushing Fingerbot for that, but they're extra, and frankly it's janky. You also don't get hardware sensor data, fan curve control, or event logs.

In fairness, that's not what we use IPMI for 95% of the time. The day-to-day stuff is all built into the Comet X: checking what's on the console, Ctrl-Alt-Deleting a hung box, and installing an OS from remote media.

### Networking: MikroTik CRS310

Three MS-02s, the KVM and an uplink means at least five ports. For me, the **MikroTik CRS310** was the perfect answer: **eight 2.5GbE copper ports** and **two 10GbE SFP+ cages** for uplinks. It's small, quiet and fits a 10-inch rack. My only gripe is that the power plug is on the front instead of the back. Otherwise, it's a great, affordable little multi-gig switch.

### The Rack: TecMojo 12U, 10-Inch

The rack is a **TecMojo 12U, 10-inch rack** with smoked plexiglass sides to keep it enclosed. It has square-hole rails on one side and tapped round holes on the other, which gives you flexibility in how you mount your gear. The whole thing is **steel**, which adds weight over aluminum, but I'll take steel over aluminum for racks any day of the week. It also comes with two 1U shelves and two 1U blanks, one perforated and one solid.

Funny thing: it has handles on top for portability. That's hilarious to me, because at 12U and completely full of gear, it is a considerable lift.

### The Little Stuff

A few more things went into this rack that are worth calling out if you're building something similar:

- **GeeekPi 12-port keystone Cat6 patch panel** to clean up the Ethernet runs.
- **GeeekPi 10-inch half-U shelves** (2-pack). The MS-02s are a bit taller than 2U, which throws off the layout. Because these shelves are half a U, you can mount them at the top or bottom of a rack unit and absorb that extra height.
- **An 8-outlet pigtail PDU** from ElecVoztile, to power everything from one plug and one switch. I'd never heard of them, but they love their goofy company name so much they put it on the front of the PDU, which is annoying, to say the least. What I *do* like is that the outlets come off staggered pigtails instead of sitting flat on the back, so you can use all eight without power bricks running into each other. Still hate that their name is on the front, though.

---

## Building It

I planned the layout in **Rackula** first ([I've talked about it before]({% post_url 2026-05-05-server-rack-upgrade-tecmojo %})): **power and KVM at the bottom, compute in the middle, networking on top.** I'd love to hear how you'd have laid it out, so tell me in the video comments or on Discord.

The build went pretty smoothly, with a couple of annoyances:

1. **PDU** at the bottom. That made the most sense to me.
2. **KVM** above it. I nested it inside an upside-down 1U shelf, which uses that rack unit *and* gives the first MS-02 a solid platform to sit on. Super stable, and it worked out perfectly.
3. **MS-02 #1**, then a **half-U shelf**. The shelves come with little screw-on elephant ears, but they're not quite long enough to reach the back rails. Thankfully, they *are* long enough to rest on top of a mounting screw, so they're stable at all four corners.
4. **MS-02 #2**, another **half-U shelf**, then **MS-02 #3**.
5. That leaves half a U before the next full unit, so I designed a simple **half-U blank** and printed it on my 3D printer.
6. **MikroTik CRS310.** It fits the rack perfectly, but it only ships with 19-inch rack ears. MikroTik sells a separate 10-inch kit, but once again my 3D printer came to the rescue with ears that attach right to the side.
7. **12-port keystone patch panel** on top.

And there it is, in all its mini-rack glory. Pretty sexy little package, if I do say so myself. Even the cable management around the back isn't too bad, all things considered.

### Why Standards Matter

Overall, it went together OK. But little things frustrated me along the way, like half-U shelves meant to be secured front and back that didn't quite reach. Having to design and print rack ears and a blank panel reminded me why standards exist.

The [19-inch rack](https://en.wikipedia.org/wiki/19-inch_rack) is *old*. AT&T standardized it with the 1.75-inch rack unit back around 1922, and the EIA formalized it as **EIA-310** in 1965. Standards create a level playing field for competition, and they make life better for consumers. When I buy a 19-inch server from one company and rails from another, they fit. I don't have to double-check every measurement. People agreed on that standard over a hundred years ago, and we've been reaping the benefits ever since.

**The 10-inch rack has no published standard.** It's a de facto thing the hobby kind of agreed on: roughly 10 inches across the front, and about 8.75 inches between the rails. There was a push for a half-width rack standard a while back, but as far as I can tell it never caught on for 10-inch gear. With lots of different companies making 10-inch racks and accessories, the odds of ending up with a shelf that doesn't quite fit, like I did, are pretty good.

Does it really matter for a homelab? Mostly no. But if you're coming from the 19-inch world like me, know that you're leaving the land of guaranteed fit. **Measure before you hit Buy Now.**

---

## Size Doesn't Equal Efficiency

One of the big reasons people build mini racks is energy efficiency. So, how efficient is this thing?

**A quick note on method:** the Proxinator has dual redundant power supplies, so I read its draw from IPMI. For the mini rack I used a Kill-A-Watt, since everything runs off a single plug from the PDU. That's not a perfect apples-to-apples comparison, but my rack runs on 240 volts and my Kill-A-Watt is a 120-volt meter, so plugging it into the rack's PDU would have ended badly. It's close enough to tell the story. Also, the mini rack number is *the whole rack*: all three nodes, the switch and the KVM. The Proxinator number is just the server, with none of its networking.

| | Idle | Under stress test |
| --- | --- | --- |
| Proxinator (IPMI) | ~250 W | ~340–350 W |
| Mini rack, everything (Kill-A-Watt) | ~140 W | ~514 W |

At idle, the mini rack wins easily. But under load it hit a max of **514 watts** and hovered right around there. I gotta admit, I did not think three mini PCs would out-pull my Proxinator!

### What That Costs a Year

| | Idle | Max draw |
| --- | --- | --- |
| Proxinator | $460 / year | $644 / year |
| Mini rack | $258 / year | $946 / year |
| Difference | **Mini saves $202** | **Mini costs $302 more** |

At idle, the mini cluster saves me about **$202 a year**, which is great. At max draw, it flips. The mini cluster costs nearly a thousand dollars a year to run, **$302 more** than the Proxinator. Wow.

### Where's the Break-Even?

Those are the minimum and maximum. The real cost lands somewhere in between, depending on load. The line is at about **40 percent**: if these systems spent more than about 40% of their time pinned, the Proxinator would be cheaper to run. Below that, the cluster wins. In reality, most homelabs spend most of their lives a lot closer to idle than pinned.

Why does the cluster pull so much at full tilt? **It's three of everything.** Three CPUs boosting at once, three motherboards, three GPUs sitting idle but still sipping power, plus the switch and the KVM. That's about 170 watts per node at max. One EPYC rated at 180 watts just has less to feed.

---

## Features Face-Off

Comparing watts doesn't mean much unless both setups can do the same job, so let's line them up.

| | Proxinator | Mini cluster |
| --- | --- | --- |
| **Cores / threads** | 32C / 64T, all Zen 4c | 42C / 42T across 3 nodes (P-cores + E-cores) |
| **Biggest single VM** | Up to 64 threads, ~200+ GB RAM | 14 cores, 64GB RAM (one node) |
| **Memory** | 256GB DDR5 ECC | 192GB DDR5, non-ECC |
| **Storage** | 16 datacenter SATA SSDs, 12.8TB raw | 6 consumer NVMe, ~3TB raw / ~1.5TB usable (ZFS mirrors) |
| **GPU** | 1x Quadro P5000, 16GB | 3x RTX 5060, 8GB each |
| **Networking** | 100GbE ConnectX-5 | 10GbE + 2.5GbE per node |
| **Remote management** | Real BMC (IPMI) | GL.iNet Comet X KVM |
| **Power supplies** | Dual redundant 1,000 W | One 350 W per node |
| **Host fault tolerance** | Single box | Three-node cluster |

**Compute.** On paper, 42 cores beats 32, so the minis win, right? Not exactly. The EPYC has 64 threads, and every core is the same. The mini cluster has 42 threads, split between performance and efficiency cores. The bigger problem is that those cores aren't in one box. **No single VM on the cluster can ever get more than one node's 14 cores and 64GB of RAM.** On the Proxinator, I can hand one VM 64 threads and a couple hundred gigs of memory if I want to. Total capacity isn't the same thing as capacity per VM, and that's a big deal if you run anything chunky.

**Memory.** The Proxinator wins on both quantity and quality.

**Storage** is where it gets ugly. Mirroring each node's pair of drives with ZFS cuts the cluster to about 1.5TB usable. And the fact that they're *consumer* drives matters more than you might think. Consumer SSDs in a server mean no power-loss protection and a fraction of the write endurance of a datacenter drive. [Proxmox's own admin guide](https://pve.proxmox.com/pve-docs/pve-admin-guide.html) literally says using consumer SSDs is discouraged. That's part of what you give up with a mini rack.

**GPUs** are the one real trade. For **transcoding**, three cards win, because every node gets its own encoder. For **AI**, 8GB caps which models you can load, and you can't pool VRAM across three boxes. In fairness to the minis, my P5000 is a Pascal card, and NVIDIA has already put Pascal into driver maintenance mode. My big iron isn't exactly cutting edge either.

**Networking.** 100GbE versus 10GbE and 2.5GbE per node, because that's what the switch runs. Welcome to mini racks.

**Power.** If a supply dies in the Proxinator, it keeps running and I get an alert. If the one inside an MS-02 dies, that node is just gone.

### Keeping Score

If you're keeping score at home, the Proxinator wins almost every category: cores per VM, memory, ECC, storage, networking, management and power. That's a beatdown.

Except for one thing, and it's a pretty important one. **If the Proxinator dies, everything goes offline with it.** A big benefit of a cluster is fault tolerance. You can spread your VMs across hosts with redundancy, so at least your most critical workloads survive a dead host. The mini cluster wins that one.

---

## What Did It Actually Cost?

The internet's other big promise is that mini racks are cheaper. So let's talk money, and not just power.

| Item | Cost |
| --- | --- |
| 3x Minisforum MS-02 Ultra ($600 each) | $1,800 |
| 3x ASUS RTX 5060 (~$500 each) | $1,500 |
| Switch, KVM, rack, PDU, patch panel, shelves | $748 |
| **Total out of pocket** | **$4,048** |

That's **not** counting the RAM and SSDs, because I already had those lying around. If I had to buy those twelve 16GB sticks of DDR5 today, they're about **$240 apiece**. That's almost **$2,900 just in memory**. Add the SSDs, and building this exact rack from scratch right now lands somewhere between **$7,500 and $8,500**.

### Does It Ever Pay for Itself?

At idle, the mini rack saves me about $202 a year over the Proxinator. So if I replaced the Proxinator with this rack, the power savings would cover what I actually spent in about **20 years**. Price in the RAM and SSDs at today's rates, and it's closer to **40**. And that's the *best* case, because it assumes the rack sits at idle forever. None of this gear will be in service in 20 years, let alone 40.

The flip side is worse. *Way* worse. If you're starting from zero, the Proxinator is **way** more expensive to build today. Just the 256GB of ECC RAM in it would run you around **$9,000**. With today's AI-cocaine-fueled RAM and SSD prices, I estimate a brand-new Proxinator would cost roughly **$27,000**.

I ran the math the other way, too. Say I already owned the mini rack and wanted to justify the Proxinator on power alone. Running both at 100%, 24/7, it would take **91 years** to break even. At idle, it *never* breaks even, because the Proxinator costs more to run. God, I hate this timeline.

### The Real Takeaway

Over five years, purchase plus power, the gap between these two is around **$18,000 to $20,000**, no matter how hard I run them. **Power is a rounding error. What you pay up front is what actually matters.**

---

## Final Thoughts

I'm sure plenty of you are already typing that this isn't an apples-to-apples comparison, or that it's unfair to compare these two approaches. You're right, it's not, and it isn't meant to be. This is my perspective: a big iron guy building a mini rack. And I'll admit that when I started this project, I had a pretty negative opinion of mini racks as a whole.

So here's where I've landed, and I can't believe I'm saying this. **If I were starting fresh in homelabbing today, I wouldn't build a full-sized 42U server rack.** Not because mini racks are better across the board. They're not. But they're the cheaper way to get started, and that makes me sad, but it's the reality we live in.

Building this mini rack brand new was expensive on its own, but it pales next to building even a single brand-new enterprise-class server. And going with older, used mini desktops can save you a lot more. That's what I'd tell anyone just getting started.

And yes, I know. Used enterprise gear is cheap too. That's how most of us big iron folks got started. But you pay for it every month in power and noise, and older servers idle a lot higher than these little guys do.

A few other eye-openers for me:

- **Smaller doesn't automatically mean more efficient.** The minis won at idle, but not at full power. Your mileage may vary.
- **No standard means things won't always fit.** It's very much a measure-twice, cut-once kind of thing, especially when you mix brands like TecMojo and GeeekPi.

### So, Am I the Idiot?

Kinda? Half-idiot, maybe? If you're starting out fresh in 2026, the internet was right and I was wrong. **A mini rack is the cheaper way in. But it's not the cheaper way out.** For what I do, with what I already own, the big iron stays. The Proxinator does more per VM, per drive, per everything, and the power savings from switching would take longer to pay off than I'll be homelabbing.

How did it actually feel to live with? Honestly, mini labs don't feel like servers to me. They feel like really nice, really organized desk projects. There's something about server infrastructure that just captivates me, and it has since I first got started in IT. It's not rational, obviously. It's completely emotional, and I've got the crazy monthly power bills to prove it.

So am I joining the #minihomelab life? No. But I know someone who is.

## Where This Rack Is Going

Here's the thing: this rack was never staying with me. It's headed from Oregon to Colorado, to **Branden**, my co-host here on 2GT. When Branden moved, he had to downsize from his full 42U rack of server gear for apartment living. Which, when you think about it, is exactly who a mini rack is for: a big iron guy who can't have big iron anymore.

It'll be his new homelab, and it'll double as an **off-site DR site** for some of our 2GT projects. So that fault tolerance I said was the cluster's one big win? It's about to get a whole lot better, because it'll be sitting a couple of states away.

---

## The Parts List

If you want to build something similar, here's everything in the mini rack.

*Heads up: 2GT participates in the Amazon Affiliates program, so we may earn an affiliate commission on any purchase made through these links.*

- **3x Minisforum MS-02 Ultra** (Core Ultra 5 235HX, 64GB, 2x 512GB NVMe): [Amazon](https://a.co/d/0fVpbBHZ)
- **3x ASUS RTX 5060 Low Profile:** [Amazon](https://link.amazon/B0aJby6Js)
- **MikroTik CRS310-8G+2S+IN:** [Amazon](https://link.amazon/B06RL892w)
- **GL.iNet Comet X KVM:** [Amazon](https://link.amazon/B01KKsMQu)
- **TecMojo 12U 10-inch rack:** [Amazon](https://link.amazon/B0fJTPxce)
- **GeeekPi 12-port keystone patch panel:** [Amazon](https://link.amazon/B0cXwdnXl)
- **GeeekPi 10-inch half-U shelves (2-pack):** [Amazon](https://a.co/d/0fgIsBhH)
- **8-outlet pigtail PDU:** [Amazon](https://link.amazon/B0b8YXmWD)

And the big iron: a **45Drives Proxinator VM16 Turbo** with an AMD EPYC 8324P (32C/64T), 256GB DDR5 ECC, a Quadro P5000, a ConnectX-5 100GbE NIC and 16 Intel datacenter SATA SSDs.

As always, a huge thank you to my Patreon supporters and YouTube Members for making projects like this possible. And come hang out in the Discord community. Tell me how you'd have laid out the rack, and whether you're team big iron or team mini. See you on the next one!
