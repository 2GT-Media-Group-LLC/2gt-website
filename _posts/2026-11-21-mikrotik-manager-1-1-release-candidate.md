---
layout: post
title: "MikroTik Manager 1.1 Is Feature Frozen, and You All Built Half of It"
date: 2026-11-21
categories: [Homelab, Networking, Infrastructure]
tags: [Mikrotik, MikroTikManager, OpenSource, Networking, Firewall, SelfHosted, Release, Community]
description: "MikroTik Manager 1.1 hit feature freeze and its first release candidate on November 20. Firewall zones, IPv6 firewall editing, Sessions, Tunnels & PPP, 802.1X, a rebuilt topology map, a Graphs tab, and a thank-you to the 22 people who helped make it."
banner:
  image: https://raw.githubusercontent.com/2GT-Media-Group-LLC/mikrotik-manager/main/.github/images/Topology.png
  opacity: 0.618
---

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here!

On Friday, November 20th, I did something that felt very grown up for a guy who didn't know what linting was six months ago: I declared a **feature freeze**. MikroTik Manager **1.1** is feature complete, the first release candidate is out, and from here on only fixes go in. No more "oh, just one more thing." (I am saying this mostly to myself.)

If testing goes well, **stable 1.1 should land in about a week.**

And here's the best part: **1.1 is mostly features you asked for.** A lot of it was also *built*, *tested*, or *bug-hunted* by people on the Internet who owe me absolutely nothing. Twenty-two of them get a shout-out at the end of this post, and they deserve every word of it!

But first, the goodies.

---

## The Headliner: Firewall Zones

If you've ever stared at a RouterOS filter table with 60 rules and a pile of jumps and thought "what is actually allowed to talk to what here?", this one's for you.

**Firewall zones** let you group a router's interfaces into zones (LAN, Guest, IoT, DMZ, whatever you like) and then set what may pass between each pair **in a matrix**. You pick the policy; MikroTik Manager writes the RouterOS rules and *keeps* them, all under Change Guard with **Review changes**, so you see exactly what's about to hit the router before it does.

- **Presets** to get you started: home or small office, an office with a guest network, a management network, or servers in a DMZ.
- **Custom rules** inside a zone pair can name ports, addresses, hostnames, one type of ICMP, or any IP protocol by number. NAT rules can name the WAN interface.
- **Log drops** per zone, capped at 5 a second so logging can't knock over a small router. You can also **Log** a single rule, or **Log some of it** on an allow-all pair.
- Zones can **limit what the manager itself can reach** on the router to just the API, API-SSL and SSH ports.
- With zones on, the Firewall tab reads as zones. The chains and jumps doing the real work are tucked behind **Show RouterOS rules** for when you want to peek under the hood.
- They work on routers without FastTrack offload, too.

This is the feature I'm most excited about in 1.1, and it's a great example of what this release is: an idea from the community that got pushed, prodded, tested, and refined way past where I'd have taken it on my own. [Zones docs are here.](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/devices/#zones)

## More Firewall Love

Zones weren't the only firewall work.

- **Quick rules**: common protections in one step, including [brute-force protection](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/devices/#brute-force-protection). It bans an address after repeated failed logins and shows the bans across your whole fleet on the Security page.
- **The IPv6 firewall is editable**, just like IPv4: filter rules, NAT (RouterOS 7), and address lists, with reordering, counters and Review changes. IPv6 is no longer the forgotten middle child.
- Rules that log get a **short tag in their log prefix**, so Firewall Logs can tell you exactly *which* rule logged a line.
- **Known RouterOS CVEs** now count against a device's hardening score. Patch your stuff.

## Firewall Logs and Sessions (Now Under Security)

Firewall Logs got a full redesign, and it moved into a new home under **Security** alongside a brand-new **Sessions** view.

- Logs open **grouped**: one line per device, rule, interfaces, protocol and port, with counts. Hit **Raw** if you want the firehose.
- It speaks zones, with **coloured zone badges** and **From any zone / To any zone** filters.
- Pick a period from 1 hour to 30 days, filter to one rule with **All rules**, or **Hide rules** you don't care about.
- An **Application** column names the service behind each port, and **Resolve names** swaps addresses for client hostnames and reverse DNS.
- **Sessions** gives you one row per connection from NetFlow: client, server, NAT, bytes each way, and how it ended. Routers behind NAT are told apart in NetFlow now, so their flows don't get lumped together.

## Traffic Got Smarter

- Click an application to see **the clients behind it**, each with its share, plus **Where it goes**: its destinations and Top Destinations.
- **Names behind addresses**, pulled from the DNS query logs of **AdGuard Home** or **Pi-hole**, including AdGuard Home running as a Home Assistant add-on.
- **Drag across a chart to zoom** the whole page into that window. This one is weirdly satisfying.
- Graphs as **rates** (bits and packets per second) instead of totals, if that's how your brain works.
- A new layout with Top Talkers beside Traffic by Application, and Top Destinations below.

## Network Services: Tunnels, PPP, Netinstall, MLAG and More

- **Tunnels & PPP**: every IPsec peer, PPPoE server and client, PPP session, and EoIP/GRE/IPIP tunnel, problems first, with a **Tunnel down** alert. You can disconnect PPP sessions and turn PPP users or PPPoE servers on and off, live.
- **Netinstall** from a router running RouterOS's netinstall package. Yes, really.
- **MLAG** monitoring: pair state and role, the peer link, settings that differ between peers, heartbeat and host-sync storms, and an **MLAG degraded** alert.
- **Reverse proxy** rules on your routers, with **Let's Encrypt** certificates for public hostnames.
- **WireGuard** peers now get a client `.conf` and a **QR code**, and Add Peer can generate the client's key pair for you. Phone onboarding just got a lot less annoying.
- **Routing rules** on the Routing tab, and **DNS over HTTPS** under Network Services → DNS.

## Switching, VLANs and Bridges

The switching folks were busy.

- **Add, edit and remove VLAN interfaces** from the VLANs tab, and if you're about to change the one the manager uses to reach the device, it'll predict the lockout *before* you cut your own legs out from under you.
- **Q-in-Q** and **MVRP** on bridges, bridge VLANs and VLAN interfaces.
- **Spanning tree you can actually set**, with a Spanning Tree card on the topology page and per-port priority, path cost, edge, BPDU guard and more.
- **802.1X** on switches and routers, with RADIUS from User Manager or your own server.
- **Drawn faceplates**: real-looking RJ45 jacks, SFP cages and QSFP ports, sized to the device with its model name. It's purely cosmetic, and I love it anyway.
- Config Health now catches **access ports left tagged in other VLANs**.

## Device Pages

- A new **Graphs** tab: history for every chart over one range (1 hour to 30 days) with a shared hover cursor, covering interfaces, connections, NAT, firewall drops and per-core CPU. **Peaks and drops** show CPU and memory spikes as a band above the 5-minute average.
- A dedicated **Firmware** tab for RouterOS and RouterBOOT.
- **Torch** in Tools: who's talking on an interface *right now*, flow by flow.
- An **SMS inbox** on the LTE tab.
- A **Wireless Systems** device type for outdoor PTP, PtMP and CPE radios, with an **Antenna Aligner** tab showing live signal from one or both ends of a link, and an optional tone. Climbing a tower with your laptop has never been so fancy.
- **Backups over the API** when SSH is off (RouterOS 7.13+), and **Force add** for devices that aren't reachable yet.
- The **Configuration changed** alert now shows you *what* changed, as a diff.

## Wireless, Topology, Clients and the Dashboard

- **Set up CAPsMAN** from the Wireless page: a controller and its APs, one band or both, with an optional separate 5 GHz SSID, on wifi and legacy CAPsMAN.
- **The topology map got rebuilt**: curved links, a tiered layout (gateway, core, access, APs) top-down or left-to-right, a **Compact** view with role chips, port labels, merged parallel links, dotted links over tunnels, links from OSPF neighbours, your own firewall or ISP modem as a node, and **Export to PNG or SVG**. Your network diagram for the next change review is now one click away.
- **Clients**: hideable and resizable columns, the manager's own gear filtered out, and a **Connection Path** from a client's device all the way to the top of the site.
- **Sites** can be placed on the map by latitude and longitude, each with its own zoom.
- A much more readable **Capacity & health** card on the dashboard, busiest devices first, with 24-hour CPU graphs.

## Operations and the Manager Itself

- **[Jump hosts](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/jump-hosts/)**: reach devices through an SSH connection to another router.
- **Scheduled bulk commands**, once or repeating, and **templates built from a device's existing config**.
- **[AI connections](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/ai-connections/)**: read-only fleet review for AI tools over MCP or OpenAPI. Read-only, on purpose. I'm not handing an LLM the keys to your core router, and you shouldn't either.
- **Let's Encrypt** for the manager itself.
- **12- or 24-hour time**, fleet-wide or per account. Settle the debate in your own household.
- **Appearance**: a System theme that follows your computer, seven accent colours (or your own), and **Midnight** dark surfaces.

## Under the Hood

Not glamorous, but it matters if you've got a big fleet:

- **Polls no longer stall on large networks**, because clients are written in batches.
- **Fewer false offline alerts.** A device is marked offline only after a number of missed polls in a row (2 by default). When most of a site goes quiet at once you get *one* alert for the site, not forty. And reboots the manager sent, rollout steps and maintenance windows don't page you.
- Device Logs and Firewall events open quickly even on huge log tables.
- New major versions underneath (Express 5, BullMQ, openid-client 6, recharts 3, zustand 5, TypeScript 6), plus **property-based fuzz tests** on code that reads outside input. My wife would be proud. Probably. Maybe.

---

## Heads Up: Things You Might Notice

A couple of changes on purpose, so they don't surprise you:

- **Settings permissions got tighter.** Admins change everything. Fleet operators see Settings read-only (SSH Keys apart) and can no longer change alerting, webhooks, report schedules, or clear the poll queue. Write API tokens act as an operator, so they lose those too. Viewers and site-limited accounts see only their own account. [Details here.](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/configuration/#who-sees-settings)
- **Netinstall and scheduled commands need an admin.** A scheduled bulk command runs only while the account that made it is still an admin.
- **Firewall Logs and Sessions moved** under **Security** in the menu.
- **With zones on, the Firewall tab reads as zones.** Your RouterOS rules are still there behind **Show RouterOS rules**.

## Want to Help Test the RC?

Please do! That's the whole point of a release candidate. The RCs live on the `dev` branch and the `:beta` images ([release channels](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/configuration/#release-channels)). Grab the latest RC, and if something breaks, [open an issue](https://github.com/2GT-Media-Group-LLC/mikrotik-manager/issues).

If you're coming from 1.0:

1. Update to the latest **1.0.x** first if you can, then take a **[platform backup](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/platform-backup/)** (Settings → Backup & Restore). **Do not skip this.**
2. Get the **1.1 compose file** along with the images, and keep your `.env` ([updating and image versions](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/configuration/#updating-and-image-versions)). A 1.0 compose file with 1.1 images will still start, so a half-finished update won't leave you stuck.
3. Start it. Database migrations run on their own. PostgreSQL and Redis restart once, because the 1.1 compose file pins newer builds of their images (still PostgreSQL 15 and Redis 7).

One important note: **migrations only go forwards.** Rolling back to 1.0 means restoring that platform backup. That's why step 1 matters.

If you'd rather wait for stable, that's totally fine. Stay on 1.0. Every security fix made during 1.1 also shipped on 1.0.x, so an up-to-date 1.0 install already has them.

The full [1.1 release notes](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/release-notes/) have every change, release by release.

---

## Thank You. Seriously.

When I open-sourced this thing back in April, I figured maybe a handful of people would try it. What actually happened is that people from all over the world started showing up with ideas, bug reports, test results, and pull requests. A big chunk of 1.1 exists because of them.

So, thank you to everyone who suggested a feature, tested a beta, sent a fix, or reported a problem. A few people deserve a special call-out:

**[@ronchan44](https://github.com/ronchan44) (Ron)**, the MVP of this release, full stop. Firewall zones and everything around them (custom rules with addresses, hostnames and protocols, logging per zone and per rule, which rule logged a line, presets), the Firewall Logs redesign and Sessions, Tunnels & PPP and Netinstall, the reverse proxy with Let's Encrypt, scheduled bulk commands, 802.1X with User Manager, placing sites on the map, routing rules, backups without SSH, drawn ports, Torch, the CAPsMAN quick set, NAT rules naming the WAN, dashboard metrics that open their graph, Q-in-Q and MVRP, zooming into Traffic, bridge addresses and interfaces from the Bridges tab, DNS over HTTPS, AP Radio TX Retries... **and testing nearly every single beta.** Ron, I don't know how you have time for a day job. Thank you.

**[@azizfayziev](https://github.com/azizfayziev) (aziz)**: Traffic from an application down to its clients, destinations and names; the far more readable topology map with its tiered layout, compact view, port labels and export; the Capacity & health card; MLAG monitoring; the Graphs tab with peaks and drops; Clients leaving out network devices and the Connection Path; and actual code for the Firewall Logs filters and for fewer false offline alerts.

**[@eskyuu](https://github.com/eskyuu) (Steven Wilton)**: Let's Encrypt for the manager, source builds behind a proxy, adding a device without checking it first, fewer refreshes from busy pages, the RouterBOOT restart check, and Config Health's check of VLAN interfaces as bridge ports.

And a huge thank-you to:

- **[@KevinGinger](https://github.com/KevinGinger)**: jump hosts, and RouterOS's reason when one can't forward.
- **[@jenserling](https://github.com/jenserling)**: 12- or 24-hour time, and Wireless Systems with the antenna aligner.
- **[@JohnDepon](https://github.com/JohnDepon)**: traffic graphs as rates, and performance on large fleets.
- **[@davidcoulson](https://github.com/davidcoulson)**: the Firmware tab, and security reports.
- **[@Bigben83](https://github.com/Bigben83)**: the read-only MCP connector that AI connections grew from.
- **[@cybr4nx](https://github.com/cybr4nx)**: Quick firewall rules, zone presets, and resizable client columns.
- **[@Lithiuhm](https://github.com/Lithiuhm)**: WireGuard client configs and QR codes.
- **[@thanhtrung5763](https://github.com/thanhtrung5763)**: log tables that open quickly.
- **[@pimlie](https://github.com/pimlie)**: topology links from OSPF, and security reports.
- **[@dlemery](https://github.com/dlemery)**: editing the IPv6 firewall.
- **[@rvanderwouw](https://github.com/rvanderwouw)**: VLAN interfaces on the VLANs tab.
- **[@kraken-XIV](https://github.com/kraken-XIV)**: the System theme and accent colours.
- **[@simonkrebsd3](https://github.com/simonkrebsd3)**: brute-force protection.
- **[@kamils777](https://github.com/kamils777)**: the Configuration changed alert's diff.
- **[@HectorUrrea24](https://github.com/HectorUrrea24) (Hector)**: PPP users and sessions.
- **[@Toryius](https://github.com/Toryius) (Manuel)**: a template from a device.
- **[@trackersoft](https://github.com/trackersoft) (Stanley)**: the SMS inbox, deleting the carrier's replies, and readable chart labels.
- **[@schmitt330](https://github.com/schmitt330)**: device tools on RouterOS 6.
- **[@orshih6](https://github.com/orshih6)**: a security report on the release workflow.

A special nod to everyone who sent in **security reports**. That's not the fun, flashy kind of contribution, but it's the kind that keeps people's networks safe, and I don't take it for granted.

This is what open source is supposed to feel like. I started this project as a guy vibecoding a tool for his own homelab. It's turned into something a whole community is building together, and I'm genuinely grateful.

---

## What's Next

Stable 1.1 in about a week, assuming the RCs hold up. So go break them! Seriously, the more people who test the RC on real networks, the better 1.1 stable will be.

[MikroTik Manager](https://github.com/2GT-Media-Group-LLC/mikrotik-manager) is open source under AGPL-3.0 and completely free. The [1.1 documentation is here](https://2gt-media-group-llc.github.io/mikrotik-manager/1.1/). If you've got an idea for 1.2, open an issue. Apparently that's how features get built around here.

As always, a huge thank you to my Patreon supporters and YouTube Members. You make it possible for me to spend time on projects like this one. And if you want to talk MikroTik, homelab, or anything else, come hang out in the Discord community. See you there!
