---
layout: post
title: "MikroTik Manager Is Now Open Source — You Asked, Here It Is"
date: 2026-04-13
categories: [Homelab, Networking, Infrastructure]
tags: [Mikrotik, OpenSource, Docker, Networking, Management, SelfHosted, TypeScript]
description: "After 70+ comments asking me to release the MikroTik Manager I vibe-coded, I'm doing it. Here's a complete breakdown of every feature, the tech stack, and how to get it running in your homelab today."
banner:
  image: https://raw.githubusercontent.com/2GT-Media-Group-LLC/mikrotik-manager/main/.github/images/Dashboard.png
  opacity: 0.618
---
I said I probably wouldn't release it publicly.

You all had other plans.
---
## You Made Me Do This

A few days ago I published a post (and video) about a project I vibe-coded over a handful of nights using Claude Opus inside VS Code — a unified MikroTik management platform. Something like UniFi or Meraki, but for MikroTik hardware. I fully expected the response to be split between people who were excited about the concept and people who were ready to tar and feather me for writing production code with AI. And honestly, it was. But what I did *not* expect was **over 70 comments** asking me to open source and release the project.

Seventy. I've been doing this long enough to know that the community doesn't usually agree on anything in that kind of volume or with that kind of consistency. So I listened.

**[MikroTik Manager is now live on GitHub.](https://github.com/2GT-Media-Group-LLC/mikrotik-manager)**

Before we get into the how-to, I want to set some expectations up front, because I'd rather be honest with you than have you feeling burned later.
---
## Setting Expectations: This Is a Beta Release

This is version **0.10.0 Beta**. It works. I've been running it in my own homelab and I use it regularly. But it is beta software, and it was built by one person with an AI assistant over a few evenings — not by a team of engineers who went through a formal development and QA process.

**Updates will happen on my schedule.** I'm a dad, I work in IT full-time, and I make videos on the side. When I feel like adding features or fixing bugs, I will. I'm not committing to a roadmap, a release cadence, or SLA of any kind. If that's a dealbreaker for you, I totally get it — but I wanted to be upfront rather than ghost a community that's shown this much interest.

What I *will* say is this: contributions are welcome. If you're a developer and you want to add something, fix something, or improve something, the door is open. More on that later.

With that said — let's talk about what this thing actually does, because I think you're going to like it.
---
## What Is MikroTik Manager?

MikroTik Manager is a **self-hosted, full-stack network management platform** for MikroTik devices. It gives you a single web interface — a real one, with a modern UI — to monitor, configure, and manage your entire MikroTik infrastructure: routers, switches, and wireless access points.

If you've been relying on Winbox, the built-in web admin (which is basically Winbox in a browser), or raw SSH to manage your MikroTik gear, this is meant to be a significant quality-of-life upgrade. It communicates with your devices via the **RouterOS API** and SSH, so no agents, no special firmware, and no changes to your existing network are required beyond enabling the API service on each device you want to manage.
---
## Features: The Full Breakdown

Let me walk you through everything the platform does right now, section by section.

### Dashboard

The dashboard is your command center. At a glance you get:

- **Live KPI cards** — total devices in the system, online vs. offline count, total connected wireless clients, and active alert count
- **Device type distribution chart** — a quick visual breakdown of routers vs. switches vs. APs in your environment
- **Historical client count graph** — track connected client counts over time with adjustable ranges from 1 hour out to 30 days
- **Firmware update notifications** — if any of your devices have a newer firmware version available, it shows up here with per-device detail

This is the view I have open most of the time. It gives you a fast health check of the whole environment without having to dig into individual devices.

### Device Management

Adding a device is straightforward: give it a name, IP address, credentials, and API port, and the platform takes it from there. From that point on, it polls each device automatically for status, model, firmware version, and RouterOS version.

Beyond the basics, each device supports:
- **Per-device notes** — document whatever you want about that piece of gear
- **Rack location** — record the rack name, slot, and physical address
- **Map integration** — enter a physical address and the platform generates a map pin for that device, which also populates a global map on the dashboard showing where all your gear lives
- **Credential encryption at rest** — device passwords are encrypted in the database, not stored in plain text

The device list gives you a quick view of all your hardware: names, IPs, model, firmware, status, last-seen timestamps, and controls to refresh or remove a device.

### Per-Device Views

Each device has its own detail page with multiple tabs. Here's what each one covers.

#### Overview

The overview tab is the first thing you land on for any device. You get:
- Cards for current CPU load, memory usage, uptime, and OS version
- A full system info card covering everything from the model and firmware version down to the device type, API port, and when it was last contacted
- The physical details card for rack and location info
- An in-line map from the physical address you entered
- Buttons to open the device's native web admin, launch a **draggable in-browser SSH terminal** directly to the device, and force an immediate configuration sync

That SSH terminal is one of my favorite features. You're already in the management platform — being able to drop to a terminal on the device without opening a separate SSH client is a genuine quality-of-life improvement.

#### Ports (Switches)

This tab is where I spent the most time during the build, and it shows. The platform has no hard-coded knowledge of MikroTik hardware models. When you add a switch, it pulls port data from the device and **dynamically builds a visual port diagram** that represents the actual physical hardware layout. Port states are color-coded: red for offline, green for online, blue for selected.

Below the diagram is a full port list showing name, status, speed, MTU, default VLAN, MAC address, comments, and a per-port reload control.

Selecting a port drops you into:
- **Throughput and packet graphs** with 1, 3, 6, 12, and 24-hour time range selectors
- Detailed port info: state, rate, duplex, auto-negotiation, RX/TX flow control
- **Transceiver details** — for SFP+ ports, it identifies the connected cable type and reports all available optic information from the transceiver

Port configuration is done directly in the interface — enable/disable, comment, MTU, PoE settings (where supported), link settings, and VLAN assignment. **Select multiple ports at once** to configure LAGs and LACP trunks in a single operation.

#### VLANs (Switches)

A clean list of all configured VLANs, their IDs, names, associated bridges, and a tagged/untagged port breakdown per VLAN. Each row has edit and delete actions inline. The **Add VLAN** button opens a creation dialog to define a VLAN ID, associate it to a bridge, and assign tagged or untagged ports — no CLI required.

#### Routing (Routers)

For devices in router mode, this tab surfaces full routing management:
- **Route table** — view and manage static routes
- **OSPF** — configuration and management
- **BGP** — configuration and management
- **Route Filters and Route Tables**

#### Firewall

MikroTik's firewall capability is powerful, but navigating it in Winbox is not fun. This tab surfaces all configured firewall rules in a readable format and lets you create and manage rules through the UI.

#### Config

Your one-stop shop for device-level settings:
- Device name
- Date, time, timezone, and NTP server
- DNS servers
- Management IP configuration
- **Built-in firmware update checker** — click the button, find out if there's a new version, and if there is, you can install it and reboot the device directly from the platform

#### Hardware

Rich telemetry for the physical device:
- **CPU and memory usage graphs** with 6h, 12h, 24h, and 7-day ranges
- **Internal storage** details and utilization
- **Temperature readings** from all available sensors, with **Fahrenheit/Celsius toggle** for my friends outside the US
- **Fan status and RPM** for all internal fans
- **Power supply status**
- **Voltage monitoring** for barrel-powered and 2-wire devices

#### Tools

I wanted the platform to actually be useful for troubleshooting, so these diagnostics are built in:
- **Reboot** the device
- **Ping** any address from any interface on the device
- **Traceroute** from the device
- **IP Range Scan** with optional reverse DNS lookups
- **Wake-on-LAN** — transmit a WOL packet from the switch to any MAC address on your network
---
### Wireless Management

The wireless section is one of the more fully-featured parts of the platform, especially if you're running MikroTik APs alongside your switches.

**Per-AP SSID management** — Create, edit, enable/disable, and delete wireless interfaces on individual APs directly from the UI. No Winbox, no SSH.

**Bulk SSID deployment** — This is the one that will save you real time if you have more than a couple of APs. Push an SSID configuration to all managed APs simultaneously with a single action. Want every AP to have a guest SSID with consistent settings? Do it once instead of once per device.

**Security profile management** — WPA2/WPA3, PSK, and EAP configurations managed from the platform.

**Hardware radio information** — Band filtering and hardware radio details for both the RouterOS 7 `wifi` package and the legacy `wlan` package, so it works with older deployments too.

**Spectral scans** — Schedule or trigger on-demand spectral scans per radio to see what's going on in the RF environment around your APs.

**AP scans** — Schedule or run on-demand scans for nearby access points. Useful for surveying the wireless landscape and identifying interference sources.

**Real-time radio monitoring** — Live radio status as you'd expect.

**Wireless client tracking** — All wireless clients with vendor lookup via OUI database. Know what's connected and who made it.
---
### Network Services

This is where the platform goes from "nice device manager" to "real infrastructure tool." Four core network services are managed from a unified interface, and each one supports **multi-device management with conflict detection** — meaning you can manage the same service across multiple MikroTik devices without stepping on yourself.

| Service | What You Can Do |
|---|---|
| **DHCP** | IPv4 and IPv6 servers, address pools, static leases, live lease table |
| **DNS** | Upstream servers, static records (A/AAAA/CNAME/MX/NS/PTR/TXT/SRV), cache flush, DNS-over-HTTPS |
| **NTP** | Server (broadcast/manycast) and client (unicast/multicast) configuration, sync status |
| **WireGuard** | Interface management, peer configuration, public key display, RX/TX statistics |

The WireGuard management in particular is something I've wanted in a homelab tool for a long time. Having it alongside DHCP, DNS, and NTP in one place is genuinely useful.
---
### Network Topology

The topology view builds an **auto-discovered network map** of your infrastructure using LLDP, CDP, and MNDP neighbor data pulled from all connected devices. The result is an interactive node graph with device type icons and protocol-priority link deduplication — meaning if two devices report the same link via multiple protocols, you see one clean connection, not three overlapping ones.

It's one of those features that's genuinely impressive to look at the first time your topology renders correctly.
---
### Client Tracking

The clients section aggregates all known network clients seen across every device in the system into a single view. You can filter by device, client type, or active status, and search by MAC address, IP, or hostname. Each client has a detail page with connection history and vendor identification from the OUI database.

A historical client count metric gives you trend data over time — useful for spotting unusual spikes in connected devices.
---
### Backups

Trigger RouterOS configuration backups on demand via SSH for any connected device. Backup files are stored within the platform and can be downloaded or deleted from the UI. Simple, but the kind of thing that matters when something goes wrong at 2am.
---
### Alerts

Configurable alert rules with cooldown periods so you're not getting hammered with repeat notifications:

- Device online / offline events
- High CPU or memory usage (with configurable thresholds)
- SSL certificate expiry warnings
- Firmware update available
- RouterOS log errors and warnings
- New device discovered on the network
---
### Global Search

A universal search bar in the top navigation lets you search across devices, clients, and events from anywhere in the platform. Type an IP, a MAC address, a hostname, or a device name and it returns relevant results instantly.
---
### User Management and Access Control

Multi-user with role-based access control:

- **Admin** — full access to everything, including user management
- **Operator** — read/write access to devices and network features, no user admin
- **Viewer** — read-only access

JWT authentication with secure session handling. Admin-only user creation and role assignment. Default credentials on first run are `admin` / `admin` — **change these immediately**.
---
### TLS / HTTPS

The platform generates a **self-signed certificate automatically on first run**, so you're on HTTPS out of the box. If you have a real certificate (from Let's Encrypt or your internal CA), you can upload it through the Settings UI. nginx handles TLS termination and automatic HTTP→HTTPS redirect.
---
## The Tech Stack

Here's what's running under the hood, for those who want to know what they're deploying:

| Layer | Technology |
|---|---|
| **Frontend** | React 18, TypeScript, Vite, Tailwind CSS |
| **State / Data** | TanStack Query v5, React Router v6, Zustand |
| **Charts** | Recharts |
| **Topology** | @xyflow/react |
| **Maps** | Leaflet |
| **Terminal** | xterm.js |
| **Backend** | Node.js, Express, TypeScript |
| **Primary DB** | PostgreSQL 15 |
| **Time-series DB** | InfluxDB 2.7 |
| **Cache / Queue** | Redis 7, BullMQ |
| **Real-time** | Socket.IO |
| **Device comms** | RouterOS API (port 8728), SSH2 |
| **Proxy** | nginx (TLS termination, static file serving) |
| **Container** | Docker Compose |

Everything ships as a Docker Compose stack. PostgreSQL for relational data, InfluxDB for the time-series metrics that power all the graphs, Redis for caching and the job queue, and nginx in front of everything. It's a real stack — not a SQLite file and a Python script.
---
## Requirements

Before you deploy:

- **Docker and Docker Compose v2+** on the host you're deploying to
- **MikroTik devices running RouterOS 6.x or 7.x** with the API service enabled
- **Network access** from the host running MikroTik Manager to your devices on port **8728** (or your configured API port)

That's it. No special networking, no agents on the MikroTik side, just the RouterOS API service turned on.
---
## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/2GT-Media-Group-LLC/mikrotik-manager.git
cd mikrotik-manager
```

### 2. Configure Your Environment

```bash
cp .env.example .env
```

Open `.env` and at minimum change these two values — and I mean it, actually change them:

```env
JWT_SECRET=your_long_random_jwt_secret_here
ENCRYPTION_KEY=your_32_character_encryption_key_
```

The `JWT_SECRET` signs your authentication tokens. The `ENCRYPTION_KEY` is the key used to encrypt device credentials at rest in the database. If you leave these as the defaults and your box is ever compromised, you've given away the keys to your network. Use a password manager to generate proper random strings.

The other defaults are fine for a local homelab deployment:

| Variable | Default | What It Does |
|---|---|---|
| `JWT_SECRET` | *(change this)* | Signs JWT tokens |
| `ENCRYPTION_KEY` | *(change this)* | Encrypts device passwords at rest |
| `DB_PASSWORD` | `mikrotik_secure_pw` | PostgreSQL password |
| `INFLUXDB_TOKEN` | `mytoken123456789` | InfluxDB admin token |
| `HTTP_PORT` | `80` | Host port for HTTP (redirects to HTTPS) |
| `HTTPS_PORT` | `443` | Host port for HTTPS |

Never commit your `.env` file to version control. The `.gitignore` already excludes it, but worth saying explicitly.

### 3. Start the Stack

```bash
docker compose up -d
```

On the first run, Docker will:
- Build the React frontend into static files
- Build the TypeScript backend into a Node.js application
- Initialize PostgreSQL with the database schema
- Initialize InfluxDB
- Generate a self-signed TLS certificate

This takes a few minutes the first time. Once it's done:

### 4. Open the App

Navigate to **https://localhost** (or your server's IP or hostname). Accept the self-signed certificate warning in your browser — or upload a real certificate in **Settings → TLS Certificate** if you want to skip that permanently.

### 5. Log In

| Username | Password | Role |
|---|---|---|
| `admin` | `admin` | Admin |

Go to **Settings → Users** and change the admin password before you do anything else.

### Updating

When a new version drops:

```bash
git pull
docker compose up -d --build backend nginx
```

Database migrations run automatically on backend startup, so there's nothing else to do.
---
## A Word on Security

I know some of you are already typing the comment. *"This was vibecoded, how do you know it's secure?"*

Fair question. Here's my honest answer: I don't have a penetration test report for this. What I can tell you is that it was built with secure practices in mind — credentials encrypted at rest, JWT-based auth, HTTPS enforced, role-based access control. But this is beta software. **Don't expose it directly to the internet.** Run it inside your homelab, behind a VPN, or behind an authenticated reverse proxy if you need remote access. The same rules you'd apply to any self-hosted management tool apply here.

If you find a security issue, open an issue on GitHub. I'd rather know about it.
---
## Contributing

The project is open source under the **AGPLv3 license**. If you want to contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes
4. Open a pull request

The one ask: **open an issue before submitting a PR** so we can discuss the approach first. Nothing worse than putting real time into a feature only to have it not fit the direction of the project.

If you're not a developer but you find a bug, open an issue. That's contributing too.

The **AGPLv3** license means the code is free to use, modify, and distribute. If you run a modified version as a hosted service, you're required to make your modified source available to users of that service under the same license. I chose AGPLv3 specifically to make sure improvements stay in the open.
---
## What's Next

I'll be honest with you: I don't have a formal roadmap. The topology view needs more polish. There are probably bugs I haven't hit yet. And I've got a list of features I'd still like to add when I find the time — but I'm not making promises about when that happens.

What I do know is that the foundation is solid. The stack is real, the features are genuinely useful, and it's the tool I actually run in my own homelab. That's the best endorsement I can give it.

If enough of you use it, find issues, and contribute fixes, it'll get better faster. That's the whole point of open source.
---
Head over to **[GitHub](https://github.com/2GT-Media-Group-LLC/mikrotik-manager)**, give it a star if you find it useful, and let me know in the comments how it goes in your environment. And if you missed the original post about how this whole thing came to be — including the part where I have a mild existential crisis about AI — [check that one out first](/_posts/2026-04-08-i-vibecoded-a-mikrotik-manager.md).
---
Thanks as always to everyone who's supporting us through **Patreon** and the **YouTube Membership** program — none of this happens without you. Come hang out in the **community Discord** if you want to talk through your setup or just want to nerd out with like-minded homelabbers and engineers. We'll see you on the next one!
