---
layout: post
title: "I Vibe-Coded a Mikrotik Management Platform — And It Completely Changed How I Think About AI"
date: 2026-04-08
categories: [Homelab, Networking, Infrastructure]
tags: [Mikrotik, AI, VibeCoding, Claude, Networking, Docker, Management]
description: "I spent a few nights chatting with Claude Opus and accidentally built a full Mikrotik unified management platform. Here's what I made, how I made it, and the existential crisis that came with it."
banner: https://img.youtube.com/vi/NXUKyWIH90c/maxresdefault.jpg
---

![](//youtu.be/NXUKyWIH90c)

In all the years I've been working on my homelab, working in IT, and making videos, I don't think I've ever been as personally conflicted as I am right now. I'm equally 50% excited and stunned, and 50% uneasy and uncomfortable with what I'm about to show you. Stick around, friends — this one's gonna be an interesting one.

---

## Introduction

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here! A few days ago, I created something that didn't exist. I did it on a whim just to see if I could, and what resulted inadvertently completely changed how I feel about homelabbing, what I think about the future, and how I feel about AI and the future of creating.

For those of you who are regular viewers, you've probably heard me talk a lot more about AI in the live show. Recently I put together a local AI stack running Ollama and Open WebUI, and while that's been a fun side project, it really hadn't moved the bar for me in terms of how I felt about using or creating with AI.

Then a few weeks back, **PegaProx** hit the scene, and I made a video about it because I was truly impressed by what that small team of three people built. The comments were split — plenty of people pushed back hard on vibecoding, argued about security concerns, and insisted the original Proxmox UI was good enough. But there was no shortage of positive reactions either. No matter how you feel about AI in software engineering, it's hard to argue with what PegaProx produced, because what they made is objectively incredible.

That experience sent me down my own path.

---

## The Problem: Mikrotik is Powerful and Painful

I started looking around my homelab and seeing problems I could potentially solve. I landed on one that's been bothering me for a while: **Mikrotik**.

I love my UniFi stack — the gear is good, it's self-hosted, and the single pane of glass is great. But Ubiquiti's higher-end switching is expensive, and their stock availability has been a recurring headache. My alternative is a **Mikrotik switch with dual 100Gig ports and 8 25Gig ports** — and that little switch costs less than $1,000. It runs my entire backbone between my Proxmox server and my TrueNAS storage server.

The fact that a mere mortal can have native 100Gig throughput between two servers, with 8 25Gig ports left over, for under a grand is absolutely mind-blowing. But there's a massive catch.

**Mikrotik is kind of a household name in Europe**, and the hardware and features you get for the price can't be matched anywhere I know of. But if I'm being blunt: configuring and managing these devices is a huge pain in the ass. Your options are their **Winbox software**, the built-in web admin (which is effectively the same as Winbox), or straight SSH. None of these options are anywhere near user-friendly, and unless you're patient and have strong Google-fu, you're going to struggle.

---

## The Idea: A UniFi-Style Manager for Mikrotik

Punch drunk off the possibilities I'd seen with PegaProx, I decided to see if I could vibe-code a singular management platform — something like UniFi or Meraki — for Mikrotik hardware. A single pane of glass that lets me configure, manage, and monitor at least 85% of the general functionality you'd expect from those other platforms.

After talking through AI project ideas with friends, doing some light research, and a fair bit of just winging it, I decided to build using **Claude Opus 4.6 inside Visual Studio Code**. I signed up for a $20/month Claude Pro subscription, integrated it into VS Code, and sat down to give it a shot.

### The Prompt

The hardest part was articulating exactly what I wanted. Here's the prompt I ultimately landed on:

> *"I have network devices made by a company called Mikrotik. I want to build a unified, singular management system that allows me to configure, manage, and monitor all aspects of Mikrotik devices. I need to be able to add the devices to the system, configure their addressing, ports, VLANs, and monitor port traffic and device health. I want the system to display a port diagram for the device with selectable ports for individual configuration, as well as be able to select multiple ports for mass editing of port configs.*
>
> *I want a dashboard landing page that shows overall device status and health, active clients, and aggregated alerts from the devices.*
>
> *I want the system to be multi-user, with the ability to assign users to roles like admin and read-only.*
>
> *Lastly, I want this deployed using Docker containers. The system you're running on has Docker installed for you to test in, and I want the platform written using modern frameworks with a modern look and feel.*
>
> *Ask any questions you have."*

Then I pressed enter.

---

## The Build: 3–4 Nights of Iteration

Over the course of a few days, working on and off, I collaborated with Claude as it built and deployed the platform. I'd test, find bugs or design elements I didn't like, Claude would fix them, and we'd keep going. The process was deeply iterative — I'd have a deployed feature I liked, realize I wanted something additional, tell Claude, it would implement it, and around and around we went.

I even went out and bought another cheap Mikrotik device, put it in router mode, and added it to the platform specifically so I could work with Claude to add router management features. All of this while writing video scripts, playing D&D with friends, and watching Plex.

In the end, I had something that blew me away. Never in my wildest dreams could I have imagined the finished product I created.

---

## Mikrotik Manager: A Full Walkthrough

### Login and Dashboard

The login screen features a living, animated network-like pattern that dynamically changes on every page load — inspired by Nutanix Prism. Dark mode is, of course, included, and it looks particularly sharp against the animation.

Once logged in, you land on the **Dashboard** with a familiar layout: collapsible left navigation and content on the right. At the top sits a **universal search bar** — type in IP addresses, device names, events, and the system returns matching results instantly.

The main dashboard area includes:
- **Macro cards** for total devices, active clients, alerts in the last 24 hours, and online vs. offline device counts
- A **running graph** of total connected clients detected across the network
- A **device type breakdown pie chart**
- A **map** showing the physical locations of all added devices
- A **recent events card** showing aggregated logs from all devices, filterable by error, warning, and info level

### Device Management

The **Devices** section lists all added Mikrotik hardware with names, IP addresses, model, firmware version, status, last-seen timestamps, and refresh/delete controls. Adding a device is as simple as clicking **Add Device**, filling in the connection details, and saving.

Each device has its own detail view:

**Overview Tab**
- Cards for current CPU load, memory usage, device uptime, and OS version
- A detailed system info card: name, IP, model, OS version, firmware version, device type, API port, date added, and last contact
- An **editable physical details card** for location, rack name, rack slot, and notes
- A **map generated from the entered physical address** — the same data that populates the main dashboard map
- Buttons to open the device's web admin, launch a **draggable in-browser SSH terminal** directly to the device, and force an immediate configuration sync

### Ports Tab

This is the part I'm most proud of. The manager has no built-in knowledge of Mikrotik hardware models. When you first add a device, the system pulls port details from the switch and **dynamically builds a visual port diagram** representing the actual hardware. Port states are color-coded: red for offline, green for online, and blue when selected.

Below the diagram is a full port list showing name, status, speed, MTU, default VLAN, MAC address, comments, and a per-port reload button.

**Selecting a port** gives you:
- **Throughput and packet graphs** with 1, 3, 6, 12, and 24-hour time selectors
- Detailed port info: state, rate, duplex, auto-negotiation, RX/TX flow control
- **Transceiver details** — for SFP ports it will identify the connected cable type and report all available optic information

**Configuring a port** is done directly in the interface — enable/disable, comment, MTU, PoE (if supported), link settings, and VLAN configuration. **Select multiple ports** together and you can configure LAGs and LACP trunks from the same view.

### VLANs Tab

A full list of configured VLANs, their IDs, names, associated bridges, and a tagged/untagged port breakdown per VLAN. Each row has edit and delete actions. The **Add VLAN** button opens a creation card to define a VLAN ID, associate it to a bridge, and assign tagged or untagged ports — all without touching the CLI.

### Routing Tab

For Mikrotik devices running in router mode, this tab surfaces full routing functionality:
- **Route table** for managing static routes
- **OSPF** configuration and management
- **BGP** configuration and management
- **Route Filters** and **Route Tables**

### Firewall Tab

All Mikrotik devices have built-in firewall capability. This tab lets you view, manage, and create firewall rules through the UI — no Winbox required.

### Config Tab

Your one-stop shop for device-level settings:
- Device name editing
- Date, time, timezone, and NTP server configuration
- DNS server configuration
- Management IP address configuration
- **Built-in update checker** — click **Check for Update**, and if a new firmware is available, you can install and reboot the device right from the platform

### Hardware Tab

Rich hardware telemetry for the device:
- **CPU and memory usage graphs** with 6, 12, 24-hour, and 7-day time scales
- **Internal storage** details and utilization
- **Temperature readings** from all available sensors, with **Fahrenheit/Celsius toggle** for my metric friends outside the US
- **Fan status and RPM** for all internal fans
- **Power supply status**
- **Voltage monitoring** for devices powered via barrel connectors or 2-wire interfaces

### Tools Tab

I wanted the platform to be a real troubleshooting tool, so I surfaced the diagnostics already built into Mikrotik hardware:
- **Reboot** the device
- **Ping** any address from any interface on the device
- **Traceroute** from the device
- **IP Range Scan** with optional reverse DNS lookups
- **Wake-on-LAN** packet transmission from the switch

---

## Beyond Devices: The Rest of the Platform

**Clients** — All known network clients seen by any Mikrotik device in the system. Each entry is configurable with custom names, and reverse DNS names auto-populate if you have DNS configured on your network.

**Events** — Aggregated logs from all connected hardware, searchable and filterable by topic, log level, and individual device. A **clear button** lets you wipe all stored events.

**Topology** — Still a work in progress. I'm building a dynamic network topology map from LLDP data pulled from all connected devices. The foundations are there and it's kinda-sorta working — I just need to spend more time with Claude ironing out interface mapping and data interpretation.

**Backups** — Create configuration backups for any connected device, stored within the platform. Download, restore to the source device, or delete — all from the UI. This was actually the very first feature I built.

**Switches / Routers** — Dedicated sections that aggregate all switch-type or router-type devices respectively, with macro cards linking back to individual device pages. Each section has its own **Settings** tab for platform-wide configurations like LLDP and SNMP that are applied globally to all devices of that type.

**Platform Settings** — Control system-wide defaults:
- Default theme
- Backend polling intervals
- MAC scan settings (with enable/disable toggle)
- Automatic reverse DNS lookup (with enable/disable toggle)
- Maximum event data retention in days
- **Users and Roles** — manage user accounts, assign roles (Admin, Operator, Read-Only), create passwords
- **My Password** — self-service password change for the current user

---

## The Dilemma: The Excitement Wore Off

You'd think building something like this and seeing how well it turned out would fill me with only pure joy. It didn't — not entirely.

After the excitement wore off, it left me genuinely conflicted. For more than a few nights, I'd be lying in bed thinking of the next cool feature to add, only for a wave of existential dread to follow. I spent real time trying to figure out what those feelings were, and the best I can describe them is **fear**.

Anthropic — the company behind Claude, the AI I used to build this — published a report on the current and projected effects of AI on the labor market. It doesn't paint a rosy picture for skilled technology workers in the long run. And we're all seeing the headlines: large tech companies announcing layoffs while simultaneously pouring that headcount savings directly into AI programs. It's hard not to feel like AI is a slow-moving threat, growing quietly until it's turned loose on all of us as a weapon of corporate cost-cutting.

I don't have the answers. There's nothing I can personally do to control the future — only the decisions I make. Here's what I've come to accept: **it doesn't matter if you don't like AI, or think vibecoding is trash.** These tools are coming, faster than anything I've seen move in my adult life. For me, the only real option is to embrace them as quickly as possible.

---

## The Realization: I'm No Longer Just a Consumer

Building the Mikrotik Manager hit me with something that hadn't occurred to me before. All these years of homelabbing, I've been a **consumer**. If something didn't exist, I couldn't create it — I'm not a software engineer, so everything I've run has been the fruit of someone else's labor. That was just the way it was.

But not anymore. Now I can build the things I actually want.

The analogy that keeps coming back to me is **3D printing**. Before I got into 3D printing, if I needed something, I'd go to Amazon and hope someone else had already made it. With a printer, if I need a specific part or have a crazy idea, I design it, print it, realize my measurements were off, redesign it, print it again, and eventually I have exactly what I needed. No compromises, no waiting.

Creating with AI is exactly that. The accessibility barrier is lower, the iteration cycle is faster, and now my homelab can run things I built myself, customized exactly to my needs. I no longer have to wait for the next cool open source project to drop. I can create that project.

---

## "But Is It Secure?"

I can already hear a significant portion of you screaming at the screen: *"Your vibecoded apps aren't secure!"*

You might be right. But here's the point I think everyone is missing:

I wouldn't 3D print a structural load-bearing component, bolt it onto an aircraft, and fly in it without extensive testing. Likewise, I wouldn't deploy a vibecoded app into my production day job or expose it to the internet without significant vulnerability and security testing first.

I'm not claiming everything AI generates is production-ready out of the box. I'm saying that **you need to put in the effort to validate that what you're creating aligns with its intended use case.** That's the same thing we've been doing with software and hardware for years. Nothing about that responsibility changes just because AI helped write the code.

---

## What's Next for Mikrotik Manager

I have no current plans to release it publicly. If there's enough interest in the community, **open source and free** is the only way I'd do it. But I'm realistic about my bandwidth — being a dad, an engineer in my day job, and a YouTuber doesn't leave a lot of room for maintaining a public GitHub repository.

That said, never say never.

---

## Final Thoughts

I opened this by telling you I was 50% excited and 50% uneasy. Honestly? I'm still sitting right there. I don't think that feeling fully goes away, and I'm not sure it should. But what I do know is that for the first time, I can see a path to make my ideas real in a way I didn't have before. For my homelab, that means more things made by me, built exactly the way I want them.

Whatever the future looks like for all of us in tech, leaning into these tools feels like the right response.

I'd genuinely love to know where you stand on all of this — what you think about AI in software engineering, what it means for your career, your homelab, and your future. Drop it in the comments. This is a conversation worth having.

---

Thanks for reading, folks, and thank you to the fine people who support us through **Patreon** and the **YouTube Membership** program. If you'd like to support what we do here, consider checking those out. Join our **community Discord** and chat with me and like-minded homelabbers, geeks, and nerds — and as always, we'll see you on the next one!
