---
layout: post
title: "Kasm Workspaces: Enterprise-Grade Remote Desktops for Your Homelab (and Your Day Job)"
date: 2026-04-03
categories: [Infrastructure, Homelab]
tags: [Kasm, VDI, RemoteDesktop, Containers, Security, BrowserIsolation, Sponsored]
description: "A sponsored deep-dive into Kasm Workspaces — the free, open-platform VDI and browser isolation tool that's equally at home in your homelab and your enterprise stack."
banner: https://img.youtube.com/vi/33Q_POCQcNk/maxresdefault.jpg
---

![](//youtu.be/33Q_POCQcNk)

Most people hear "remote workspaces" or "VDI" and immediately assume I'm about to talk about some giant enterprise stack that costs a fortune, requires a ton of hardware to run, and has absolutely nothing to do with the homelab. But that is not the case this time. Kasm Workspaces is one of those platforms that kind of breaks that mold, because once you start looking at what it can actually do, there are some very real use cases here for homelabbers and businesses alike. So, let's dig in!

---

## What Is Kasm Workspaces?

How many times have you wanted to look at something that might be a little bit sus — like a link or a website that gives you that not-so-safe feeling — or wanted to quickly spin up a Linux desktop to test something without having to spend a ton of time building a VM you're just going to throw away after? Or maybe you want a way to RDP into Windows machines as quickly as possible from any system with a web browser?

Well, **Kasm Workspaces** is the answer. Fully featured and completely free to run in your homelab, and enterprise-ready for your day job as well.

Let's get the formalities out of the way first — this video is sponsored by **[Kasm Technologies](https://kasm.com/)**, the company behind Kasm Workspaces, which is awesome for me because I've been using Kasm Workspaces in my homelab for a long time now, and I love working with companies that actually use their software at home!

In this post, I'm going to walk you through what Kasm Workspaces is, show you how to spin it up in your homelab, do a general walkthrough of the UI, its features, and configs, and then we'll deploy our first workspace to show you how easy it is to use. But first, let's take a quick look at Kasm Technologies, the company, and its background.

---

## Kasm Technologies: The Background

Kasm Technologies was founded in 2017, and their origin story is a little different from your typical software startup. The founding team came out of US Federal and DoD cybersecurity work, and the core problem they were solving was: how do you give people secure, isolated workspaces without the whole thing becoming an expensive, brittle mess? They figured that out for some of the most paranoid security environments on the planet, and then in 2020 they asked: why should only government agencies get access to this?

So they spun up the commercial entity, made it free for individuals and the homelab community, and built out enterprise tiers for businesses that need the full feature set. They've never taken outside funding either, which tells you something about how they're building this. It's engineers who built something for real-world security needs and decided to make it available to everyone.

---

## The Sweet Spot: Homelab and Enterprise, Same Technology

Kasm Workspaces hits a really interesting sweet spot. In the homelab, it gives you an easy way to spin up clean, isolated browsers, apps, and desktops completely on demand — great for testing, sandboxing, or just keeping your main system clean. But here's the thing: those same capabilities don't stop being useful when you walk into the office. Secure remote access, streamed apps and desktops, browser isolation that protects endpoints, all of it delivered through nothing more than a web browser. The homelab use case and the enterprise use case are literally the same technology. You're just scaling the stakes.

I've been using it in my homelab in three different ways:

- **Disposable browsers** — I use their disposable browsers as a way to test links and websites that I don't feel comfortable visiting on my main PC. If you get what I mean.
- **On-demand Linux distros** — I regularly spin up different Linux distributions to play around without having to go through the effort of building a VM, installing the distro, and so on. That immediacy of being able to, with a few clicks, start a Debian distro — for example — is great because I don't have to do a bunch of work just to check a thing and then throw it away.
- **Browser-based RDP** — This may upset you, so be prepared to clutch your pearls. I use it to access my Windows VMs and desktops via RDP. Accessing those systems through my browser, again with just a few clicks, is so much easier and streamlined than starting the Windows app and going that route.

---

## Hardware Requirements

Let's talk about the hardware requirements for running Kasm Workspaces in your homelab. The minimum requirements below are a solid starting point, but remember: the more concurrent sessions you plan to run, the more resources you're going to need.

Kasm can be installed on physical hardware or within a VM and supports modern versions of **Ubuntu, Debian, and Red Hat Linux**, on both **x86_64 and ARM64** architectures. Kasm does **not** support installation in an LXC container or on Windows via WSL/WSL2.

| Resource | Minimum | Recommended |
|---|---|---|
| CPU | 2 cores | 4+ cores |
| RAM | 4 GB | 8–16 GB |
| Storage | 50 GB | 100 GB+ |

The recommendations above are what I recommend for a solid homelab deployment. There aren't official recommendations from Kasm, but those are reasonable numbers to get you off to a great start.

One more thing worth noting: you can likely install Kasm on most any Linux distribution as long as **bash, openssl, Docker CE, and Docker Compose** are already installed. So give it a shot if Ubuntu, Debian, or RHEL aren't your thing.

---

## Installing Kasm Workspaces

I'm installing into an Ubuntu Server 24.04 VM that I've already created, following my own recommendations for hardware provisioning: 8 cores, 16GB of RAM, and 100GB of storage. Let's get started.

The first thing I like to do before installing anything into a fresh VM is make sure it's up to date on patches. Once that's done, the Kasm install is incredibly easy. You can find the install command in [Kasm's documentation](https://docs.kasm.com/docs/latest/install/single_server_install), but here's what it looks like:

```bash
cd /tmp
curl -O https://kasm-static-content.s3.amazonaws.com/kasm_release_1.18.1.tar.gz
tar -xf kasm_release_1.18.1.tar.gz
sudo bash kasm_release/install.sh
```

The command will `cd` into the temp directory, download the current Kasm release archive, untar it, and then execute the install script.

You'll need a user with sudoers privileges, as you'll be prompted for your password before the actual installation begins. Before the install starts, you'll also be hit with the Kasm Workspaces EULA — answer `Y` and press enter.

As the process runs, the installer takes care of everything: installing all necessary services and daemons (like Docker CE), downloading all necessary containers, and so on. This can take a while to complete, so let it run.

Once finished, the installer outputs a list of user and service account usernames and passwords that were generated during the install. **Copy these and save them somewhere safe** — you'll need them to log in for the first time. Don't worry, the user account passwords can be changed from within the platform once you're in.

---

## First Login and Admin Dashboard

Pop open your browser and enter `https://` followed by the IP address or hostname of your VM and press enter. By default, Kasm Workspaces is deployed with a self-signed certificate — head to **Advanced** and proceed to the site to bypass that screen.

Log in with the admin account (`admin@kasm.local`) and the generated password from the installer. Once in, you land on the **Admin Dashboard**, which gives you a top-level overview of the status and health of your Kasm Workspaces — cards for users and login status, created sessions, and current statistics and image usage. Fresh install, so it's all empty for now.

---

## Workspaces and the Registry

Expand **Workspaces** on the left and select the **Workspaces** subsection. This is where all of your created workspaces can be managed. With a fresh install, it's empty.

Over in **Registry** is where the good stuff is. Kasm Technologies has created essentially an app store-like experience where they list ready-to-download-and-run workspaces that they continually maintain and add to. They have a ton of different Linux distributions — AlmaLinux, Alpine, Debian, Oracle, RHEL 9, and more — as well as dedicated apps you can run: everything from your favorite browser in a box to Visual Studio Code. Everything sandboxed, contained, and adding them to your workspace is as easy as installing an app.

This one feature really brings it all together for the homelab. The ease with which you can install what you need and start using it is hands-down one of my favorite parts of Kasm Workspaces.

---

## Sessions Management

The **Sessions** section is where you manage session activity:

- **Sessions** — See active sessions currently running.
- **History** — A historical list of previously run sessions.
- **Session Staging** — Pre-provision workspace containers ahead of time instead of creating them only when a user clicks launch. Users can be handed a session from a ready-made pool instead of waiting for a fresh container to spin up.
- **Session Casting** — Create a special URL that launches a Kasm session directly for a specific workspace. That link can be used for normal authenticated users or opened up for anonymous access if you want a fast, frictionless way to hand someone a ready-to-go Kasm environment.

---

## Access Management

**Users** — Create users, edit account information, assign them to groups, manage login-related details, and tie in things like file mappings and other per-user settings. This is also a good time to change your admin password from the randomly generated one created on install.

**Groups** — Instead of configuring permissions and behavior one user at a time, you assign users to groups and apply policies, workspace access, and feature controls at the group level. By default, Kasm includes system groups like **Administrators** and **All Users**, with All Users acting as the baseline policy layer for everyone.

**Authentication** — Kasm supports **LDAP, SAML, OpenID, and physical tokens**, giving you the ability to tie Kasm into your identity stack. This lets you control SSO, security requirements, and group-based access mapping from your existing IdP — a huge deal for enterprise deployments.

---

## Infrastructure

**Docker Agents** — Where you manage the worker nodes that actually run user sessions. This is the infrastructure layer that determines where workspaces launch, how much CPU, memory, or GPU capacity is available, and how session workloads are distributed across your environment.

**Servers** — Where you define and manage actual servers your users can connect to, whether that's RDP, VNC, SSH, or KasmVNC-based systems. I have all of my Windows computers and VMs defined in my production instance so I can use Kasm to connect to them via RDP through the browser. It makes it trivial to connect without having to deal with RDP clients for different OSes.

**Server Enrollment Tokens** — Kasm's way of securely adding remote systems into the platform. Instead of manually trusting a server, you use a token to enroll it, which helps Kasm verify and manage that system before users can access it.

**Pools** — Where you group and manage compute resources that sessions can launch on, whether that's Docker agents, server pools, or autoscaled infrastructure. Kasm can tie into VM platforms like **Nutanix, Proxmox, VMware, and OpenStack**, as well as numerous cloud providers, so it can automatically spin up and manage virtual machines behind the scenes to scale workloads as needed.

**Managers** — The orchestration layer that coordinates the platform behind the scenes, handling things like session placement, communication with agents, and overall control of how workspace resources get assigned. Essentially the control plane that ties the whole environment together.

**Deployment Zones** — How you define where sessions should run based on location, infrastructure, or other operational requirements.

**Connection Proxies** — Manages the proxy layer that brokers traffic between users and remote resources like RDP, VNC, or SSH targets.

**Egress Providers** — How you control how a workspace accesses the internet. Instead of every session using whatever default route the host has, you can send traffic through a different gateway or exit point. Want a specific workspace to use a VPN connection to access the internet? Set up that connection, assign it to your workspace, and any internet browsing in that workspace will use only that connection. This is incredibly useful — if you want to test something from a different location in the world, set up your favorite VPN as an egress provider and launch a browser workspace configured to use it.

---

## Settings

**Global Settings** — Platform-wide defaults that affect the whole environment rather than just one user or group.

**Banners** — Place notices inside user sessions so people can immediately see things like user identity, workspace context, or security and classification warnings while they're working.

**Web Filters** — Control what websites users can and cannot reach from browser-based workspaces using allow lists, block lists, category filtering, and safe search controls.

**Branding** — Customize the look and feel of the platform to match your company's branding. A paid feature focused towards businesses.

**Storage Providers** — Configure backend storage that users and admins can map into workspace sessions: Google Drive, Dropbox, OneDrive, Nextcloud, S3, or custom volume-backed storage.

**API Keys** — Generate credentials for scripts, integrations, and external systems to talk to the Kasm API without using a user login.

**Logging** — All of your Kasm Workspace logs and events, filterable and searchable.

**System Info** — The admin view of the platform's health and identity, showing details about the deployment, installed version, licensing, and other status information.

---

## Deploying Your First Workspace

Alright, let's run through a quick deploy of a workspace so you can see how fast and easy it is. From the Admin dashboard, expand **Workspaces** on the left and click on **Registry**.

The list of available workspaces is large. Let's add a **Chrome Browser** workspace. Select it in the list and click **Install**. Kasm will download the workspace and prepare it for use in the background — swing over to the Workspaces tab to monitor the progress.

You'll notice a small red alert triangle in the top right corner of the new workspace tile. Hovering over it shows a message that it's still downloading. Give it a moment, refresh the page, and once the alert icon is gone, click on it to launch.

Before it launches, you're asked how you want to open the session: current tab, new tab, or new browser window. Click **Launch Session**.

Spawning new sessions is incredibly quick, and once it starts, you have a fresh Chrome browser in a sandboxed container — ready to test risky links, browse the web safely, or check out your favorite YouTuber's latest videos. Not biased or anything.

Every session window also includes the **Control Panel widget** on the left side. Inside it, you can pass through a webcam, enable/disable sound and microphone, go fullscreen, copy/paste text, redirect printers, upload and download files, manage multiple displays, adjust stream quality, share your session, manage advanced settings, leave a workspace, log out of Kasm, or delete a session entirely.

---

## Autoscaling: Beyond the Homelab

For your homelab, a single Kasm deployment is likely all you'll need to run a few concurrent sessions. But the instant you need to scale beyond that, Kasm has a great answer: **autoscaling**.

Kasm can fully integrate with a ton of different on-prem and cloud virtualization platforms. Recently, Kasm Technologies kicked off a partnership with [Nutanix](https://www.nutanix.com/partners/technology-alliances/kasm-technologies) to help businesses running Nutanix get all the modern isolated, ephemeral, zero-trust workspaces Kasm brings — fully automated and orchestrated in their own virtualization stack.

Autoscaling integrations live in the **Pools** section under Infrastructure. In my own configuration, I have Kasm set up to start up to four separate servers as needed to handle demand. Docker Agents shows the spun-up worker nodes in real time, and in Nutanix Prism Central, those worker VMs appear automatically — named `kasm-dynamic-agent` with a unique identifier — cloned from a `kasm-ubuntu-template` VM. All of this happens automatically based on demand. It's a great example of just how capable this platform is at scale.

---

## Final Thoughts

Alright, let me just say it plainly: **Kasm Workspaces is fantastic**, and that's true regardless of the sponsorship.

Let's start with the company. Kasm Technologies gives Kasm Workspaces away free, almost entirely unrestricted, to homelabbers. I've had conversations with them, and they not only care about their product — they're homelab geeks to the core, and that shows. This is how you build a business. You share it with the geeks and nerds, engineers, and IT pros to use at home, and then they go straight into work and say "I know how to solve our VDI problem!" or "Here's how we can let people test suspicious links without touching company assets!" and that's meaningful.

I've been using Kasm for a long time. I use it to RDP into my Windows systems, spin up disposable browsers to safely check links and sites I don't fully trust, and regularly spin up Linux distributions on demand without having to build and tear down VMs. It's one of those indispensable tools that everyone should have running in their stack. If you aren't using it, you should be.

One additional thing worth calling out: **Kasm has one of the best knowledge bases I've seen for any software product**. If you get stuck on something, or want to figure out how to configure Kasm to use your VPN as an egress provider, it's all there and incredibly easy to understand. If you want to see how to set up autoscaling for your hypervisor of choice, this is where to go.

And then there's the business side. Kasm is a mature platform that can solve real problems in a meaningful way. Its ability to integrate into your company's IAM for single sign-on, its support for autoscaling worker nodes across practically every virtualization platform (on-prem or in the cloud, including Kubernetes), its browser isolation capabilities for protecting endpoints, and its ability to deliver all of it through nothing more than the web browser you already use — makes it a genuinely compelling platform for businesses of any size.

So the next time someone tells you that remote workspaces and VDI are just expensive enterprise headaches, send them this video. Because Kasm Workspaces is proof that it doesn't have to be that way.

---

Special thanks to **Kasm Technologies** for sponsoring this video and for building a platform that's as at home in a homelab rack as it is in an enterprise data center. Head over to [kasmweb.com](https://www.kasmweb.com/) to get started — it's free for the homelab, and the enterprise tiers are there when your use case grows into them.

Thanks for watching!
