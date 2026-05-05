---
layout: post
title: "I Upgraded My Server Rack and It's the Most Beautiful Thing in My Garage"
date: 2026-05-05
categories: [Homelab, Infrastructure]
tags: [Homelab, Rack, ServerCabinet, TecMojo, CableManagement, RGB, Rackula]
description: "After years of fighting cable management in my old APC rack, I planned and executed a full server rack upgrade to a stunning white TecMojo cabinet — complete with RGB lighting, a vinyl logo, and a proper name."
banner: https://img.youtube.com/vi/k7_byEyWWYs/maxresdefault.jpg
---

![](//youtu.be/k7_byEyWWYs)

I've run a 42U server rack in my garage for a very long time. My old APC rack served me well — for a while. But it no longer does, and it was time for an upgrade.

---

## Introduction

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here! I'm going to take you on a journey, friends. I've decided it's time to upgrade my server cabinet because it no longer meets my needs, and with the benefit of hindsight from using the existing one for literally years, I know exactly what I need to make my homelab hardware so much easier to work with, manage, and look dead sexy in the process.

In this post (and the video above), I'm going to walk you through the planning, finding a replacement, laying out the new rack, doing the heavy lifting of moving all my gear from the old cab to the new one, and a few surprises along the way. Let's get to it.

---

## The Problem with the Old Rack

My existing APC rack has been a decent cabinet. APC makes genuinely fantastic infrastructure gear, but it was never designed for a mixed homelab — it's built to hold servers and nothing else. My homelab is a blend of servers and networking gear, and that distinction matters a lot.

My biggest complaint was cable management. The edges of the cabinet directly meet the 19" rack mounting area, so the only way to manage cabling is through a patch panel (fine for Ethernet) or brush passthroughs for SFP twinax cables — which just looks messy and gross. My dream rack would have more width and vertical side pass-throughs for cleanly routing cables.

Then there's the back. I now have some big servers that extend far enough to come into contact with vertical PDUs, making it a genuine fight to plug anything in. No side cable routing plus deep servers means the back gets chaotic fast. Playing games to get things plugged and unplugged in a rack you're supposed to rely on is not a good time.

---

## Finding a Replacement

My requirements were simple: better cable management, more width for working space in the rear, and — just for fun — I wanted to switch to a white cabinet this time.

The rack market, it turns out, is a mess. Random storefronts, questionable sellers, poor descriptions, and zero confidence in quality. The brands I know and trust — APC, Tripplite — require going through a 3rd party like CDW. Chatsworth makes beautiful gear, but at the dimensions and feature level I wanted, they were running into the thousands of dollars.

As I kept searching, one company kept coming up: **[TecMojo](https://tecmojo.com/)**. Consistently recommended, with strong community backing. After digging in, I found the perfect rack: their [42U assembled 39" depth white server cabinet](https://tecmojo.com/products/tecmojo-42u-assembled-39inch-depth-white-server-cabinet-with-casters-3000lbs-weight-capacity).

This rack hits everything on my list:
- **42U** — matches my existing rack height
- **31.5" wide and 43" deep** — wide enough for real rear working space
- **Vertical cable management up front** — solves my cabling nightmare from day one
- **White powder coat** — yes, please

The more I looked at this cabinet, the more I fell in love with it.

---

## Disclosure

I did what any self-respecting YouTuber would do — I reached out to TecMojo and offered to make a video on the cabinet in exchange for the hardware. They loved the idea. Full disclosure: **TecMojo sent me this cabinet for free, making this a sponsored video.** That said, they have zero editorial control here — they're seeing this the same time you are. My loyalty is to you first, always. If they'd said no, I would have paid the $1,559 myself without hesitation.

---

## Cabinet Specs and First Impressions

Right away: this cabinet is incredibly well-built. Every edge, every panel, every component is frankly perfect. Panel fitment is fantastic, the structure is sound, the white powder coat is flawless, and the black rack numbering is sharp and easily readable.

Here are the key specs:
- **Height:** 78.5"
- **Width:** 31.5"
- **Depth:** 43"
- Single removable **front door** with lockable handle
- **19" square-hole rack** flanked on both sides by **vertical cable management** with evenly spaced openings to route cables cleanly through to the rear
- **Dual split rear doors** with lockable handle and vertical 0U PDU mounting hardware
- **Brush cable passthroughs on top** for structured cable runs

I also want to call out the shipping. If you've been in IT long enough, you know what a crapshoot it is to get a server cabinet delivered in one piece. This rack was wrapped better than anything I've seen shipped — and it arrived in perfect condition.

---

## Planning the Layout with Rackula

Before touching a single piece of gear, I sat down with **Rackula** — an open-source rack diagramming and visualization tool you can self-host in your own homelab. If you don't have it running already, go fix that.

Looking at my current layout, I spotted several mistakes I wasn't going to repeat:

1. I started gear at the absolute top of the cabinet with zero expansion room above it — never again.
2. Putting anything at the very top of a 42U cabinet is miserable if you're not particularly tall. Step stools are for cowards (or at least for past-me).
3. My 12-bay Synology NAS was sitting in the middle eating 7U — that's going away and giving me space back.

Here's how the new layout shapes up, top to bottom:

**Networking Stack**
- 2U blank — intentional buffer to bring working gear closer to eye level
- UniFi EFG firewall
- USW-Pro aggregation switch
- 1U horizontal cable manager — tames the SFP+ twinax runs from the Pro Agg into the vertical cable management
- 24-port top-of-rack switch
- Keystone patch panel
- MikroTik CRS510 — previously hiding in the back, now front and center where it belongs
- 1U horizontal cable manager
- Another MikroTik switch

**Compute Stack** (separated by a 2U blank)
- The Proxinator — my most powerful server, heart of the homelab, rightfully at the top
- Dell R440 1U
- Dell R730xd — goes offline mostly, fires up nightly for PBS backups
- SuperSAN — my primary Supermicro NAS
- 4-node SMC virtualization test server
- Bertha — 36-bay SMC storage that handles on-site and off-site backups for all 2GT data
- Empty Sliger case — future home of a local AI project. Stay tuned for that one.
- 1U horizontal cable manager
- 8U blanks
- 4U APC UPS

Clean, logical, and actually manageable.

---

## Sourcing the Missing Pieces

Finding white 2U rack blanks on Amazon was easy. Finding a white 8U blank? Impossible. So I grabbed a rattle can, sanded down a black blank, and painted it myself. Spray paint technology has genuinely caught up with the modern era — take your time, use a heat gun between layers, and the results are great. I did the same with the removable bezel on the Sliger case. Both came out perfect.

---

## The Unrack and Rerack

The unracking and reracking was done as a live stream — one of our community members requested it, and honestly that was a great call. I enlisted the help of **2GT Jon** for the heavy lifting. The entire process — tearing down the old rack and rebuilding the new one — took nearly four hours of manual labor.

After Jon headed out, I got to work on cabling. It took a solid two hours to get everything reconnected and back online. (If you watch the video, you can actually see the sun setting behind me in the background. That was a long day.)

The vertical cable management on the TecMojo pays off immediately. Network cabling from the switching gear routes cleanly into the vertical channels and through to the server connections in the rear without a single brush passthrough in sight. Paired with the horizontal cable managers between sections, the front of this cabinet looks immaculate.

---

## The Surprises

### Surprise #1: RGB Lighting

Yes, RGB. No apologies. The white powder coat reflects the lighting so well that I'm running **Govee LED strips at only 50% brightness** — they're that vibrant. If this doesn't make you want a white cabinet, nothing will.

### Surprise #2: The 2GT Logo

I wanted to do something special to make this cabinet truly mine. I headed to a local sign shop here in my hometown — **Sign-on** — showed them what I wanted, and a few days later Chandler showed up and installed a full vinyl decal of the 2GT logo across the right side panel. He cut around the doors, handled the release clasps so everything still opens, and the finished result is absolutely stunning. I couldn't be happier with how it turned out.

---

## Naming the Cabinet

Early in the planning stages, during one of our community live shows, the chat collectively decided the new cabinet should be named **Jeff**. Who am I to argue with the community? Jeff it is. I even had a 3D-printed nameplate made to make it official.

---

## Final Thoughts

Let me start with the cabinet itself. I've worked in infrastructure for most of my career, built plenty of server rooms and small data centers, and had my hands on a lot of different cabinets. **Hands down, the TecMojo is the best I've worked with.** Solid build, fantastic fit-and-finish, no paint or marking issues, best shipping protection I've ever seen on a cabinet, and the quality is simply top-notch. At **$1,559 USD with free shipping**, you absolutely cannot go wrong. The results of this build speak for themselves.

That said, here's the real lesson from this project: **plan before you buy**. Before you order a single piece of hardware:

- Identify the problems you currently have and what you're trying to solve
- Spin up a Docker container of Rackula and plan out your layout
- List every additional piece you'll need: blanks, cable managers, patch cables, all of it

Doing that work up front is what separates a clean build from a messy one.

Also — this was a lot of work. I compressed it into a watchable video, but be realistic: depending on your setup, expect to spend **at least a full day or two** moving into a new cabinet. Having Jon's help made a massive difference, and I'm genuinely grateful he showed up.

---

Thanks for reading, folks, and a very special thank you to **TecMojo** for sponsoring this project. Head over to **[tecmojo.com](https://tecmojo.com/)** to find the cabinet of your dreams, just like I did. And thank you to the incredible people who support 2GT through our **YouTube Membership** and **Patreon** programs — you make all of this possible. If you'd like to support what I do here, check those out, join our **Discord community**, and as always, I'll see you on the next one!
