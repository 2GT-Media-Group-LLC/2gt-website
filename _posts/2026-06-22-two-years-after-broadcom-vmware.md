---
layout: post
title: "Two Years After Broadcom Killed VMware: Where the Pieces Fell"
date: 2026-06-22
categories: [Virtualization, Infrastructure]
tags: [VMware, Broadcom, Nutanix, Proxmox, OpenShift, VergeIO, XCP-ng, Scale-Computing, Hyper-V, virtualization]
description: "It's been two years since Broadcom acquired VMware and detonated the virtualization market. Now that the dust has settled, here's a pragmatic look at who won, who lost, and where the industry actually landed."
banner:
  image: https://img.youtube.com/vi/lpHsSaL-NJw/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/lpHsSaL-NJw)

It has been two years since the Broadcom axe fell on VMware and I released my *Life After VMware* series. So very much has changed, and I think it's time to check in and see where the pieces fell and how everything settled out.

---

## Introduction

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here! A little over two years back, the virtualization world as we knew it was basically blown apart, and the 800-pound gorilla fell. This left a lot of companies and engineers — myself included — scrambling to figure out what to do and where to go next.

As they say, time heals all wounds, and now that the dust has settled, let's take a more pragmatic look at how everything has shaken out, who the winners are up to this point, and where the industry seems to have landed. Let's get to it.

---

## Broadcom: How Did It Actually Work Out?

What better place to start than with Broadcom? Financially, their strategy paid off — for them.

In a single year, Broadcom's total revenue jumped from **$36 billion to $52 billion**, the biggest leap in the company's history. A huge chunk of that came from one place: VMware. The software division VMware now lives in is pulling in **$6.8 billion in a single quarter**, up 17% year over year.

So from Broadcom's perspective, two years into the most hated acquisition in enterprise IT history — with the price hikes, the lawsuits, and the customers running for the exits — the money is going **up**, not down.

Here's the thing: Broadcom never bought VMware to keep every customer. They bought it to keep the big ones, the enterprises locked in deep enough that leaving wasn't realistic. Everybody else? We were the ones they were fine with losing.

And the numbers show it worked. Gartner expects VMware's slice of the market to fall from **70% in 2024 to 40% by 2029** — damn near cut in half. A Rimini Street survey put it even sharper: **98% of VMware customers** are already using, planning, or at least considering alternatives, and **36% have already started heading for the exits**.

So Broadcom is losing customers and making more money than ever, at the same time, on purpose.

### How they got here

You already know the playbook, so I won't relitigate all of it:

- **Perpetual licensing?** Dead.
- **The old catalog of 168 products** you could pick and choose from? Gutted down to four bundles.
- **Pricing?** Up anywhere from 150% to over 1,000%, depending on your setup.

During these two years, Broadcom also aggressively audited and sent its lawyers after current and former customers who dared to resist:

- **AT&T** was reportedly quoted an increase over 1,000% and took Broadcom to court before settling quietly.
- **Siemens** got sued for copyright infringement over allegedly running thousands of unlicensed copies.
- Plenty of customers received audit letters with **three-day deadlines**, demanding they strip out security patches they'd installed after support lapsed — or face damages. Pretty gross.

### And yet... the product got better

With all of that, you'd figure the product itself must be falling apart too. It isn't — if anything, it's the opposite.

In June 2025, Broadcom shipped **vSphere 9 and Cloud Foundation 9**, and they're genuinely good. Live patching with no reboots. Faster lifecycle management across an entire cluster. A cleaner, more predictable support model. Broadcom the company may be evil incarnate, but the engineers at VMware are still doing excellent work.

But when one door closes, another opens. Over these two years, a lot of virtualization companies living in the shadow of the now-fallen behemoth have been rising. Let's dig into them.

---

## The Winners

Not everyone who walked out on VMware landed in the same place. Some businesses wanted a straight swap, some wanted cheaper, some wanted to rethink the whole stack. Let's start at the top — the companies taking the biggest bites out of VMware's lunch.

### Nutanix

If there's a clear commercial winner two years in, it's these guys. Annual recurring revenue is **up 18% year over year**, and they just posted their best stretch ever for landing brand-new customers — a lot of them walking straight in from VMware.

They also spent these two years closing the gaps that used to hold them back. At their .NEXT conference in 2025, they opened the platform up to **external storage** for the first time, starting with Dell PowerFlex and PowerStore, then Pure Storage, and now in 2026 they've announced NetApp support with more vendors on the roadmap. That's a massive shift for a company whose mantra used to be "hyperconverged or death," and it shows a real willingness to meet enterprises where they are.

Nutanix also dove hard into Kubernetes, picked up support from **Omnissa** (the Horizon VDI castoff from VMware), and branched heavily into sovereign cloud, cloud DR, and private AI infrastructure.

The catch: Nutanix is **not** the cheap option. But businesses looking for a way out from under Broadcom have been happy to pay for a polished, reliable platform — and more importantly, they trust Nutanix to be a better partner than Broadcom could ever be.

### Red Hat OpenShift

Believe it or not, OpenShift is one of the biggest winners here, and the numbers are wild. The count of virtual machines running on OpenShift **jumped 417% in 2025**. Red Hat booked $300 million on the platform in twelve months and assessed roughly **1.4 million VMs** for migration. Even NASA moved over.

OpenShift runs your virtual machines and your containers side by side on the same platform, so businesses already leaning hard into containers saw it as a way to ramp up that transition while ditching VMware.

The catch: this only lands for companies already headed toward a container-first future. For shops that just want to run traditional VMs and be left alone, OpenShift is a *lot* more platform — and a much steeper learning curve — than they actually need.

### Proxmox

And then there's the one a lot of you have already spun up in a lab. For years Proxmox has been the darling of the homelab crowd: free, open source, and rock solid. But it always had a few things holding it back — no centralized way to manage multiple clusters, no automated cluster workload balancing, and no true enterprise backup support.

A lot of those limitations have now been broken:

- **Proxmox Datacenter Manager (PDM)** helps companies manage multiple clusters from one place.
- The latest version of PVE now supports **cluster workload balancing**.
- Earlier this year, **Veeam officially released native support** for PVE.

In the open-source virtualization world, Proxmox has become a far more viable VMware alternative, and licensing costs are incredibly affordable — an enticing, cost-effective option for businesses chasing performance and savings.

Now I'm going to be critical here, so get your angry comments ready. PVE clearly won first place in the open-source battle, but it still suffers from some of the same issues it had before VMware fell. Top-tier support is still *two hours within a business day*. There are still very few certified partners, less formal training, and fewer big-name customer references. PDM is a great step, but it's nowhere near a feature-complete vCenter alternative. If you need a vendor to hold your hand at 3 a.m., your safety net here is a lot smaller. But the price is basically free, and a whole lot of people are deciding that tradeoff is more than worth it.

---

## The Specialists

Not every shop needs a do-everything platform. Some of the smartest moves over these two years came from companies that picked one specific job and did it better than VMware ever did.

### Scale Computing — the edge

If your world is hundreds of locations — retail stores, factory floors, branch offices, places with no IT staff on site where the hardware just has to *run* — that's Scale Computing's home turf, and they leaned all the way in.

This year they tightened up their partnership with **Lenovo**, dropping their HyperCore software onto Lenovo's tiny ThinkEdge boxes, and got aggressive on price with VMware-alternative deals starting around **$10,000 for a three-node cluster**. For distributed shops bleeding out on per-site VMware renewals, that math adds up fast. They also picked up Veeam enterprise backup support.

The wrinkle: in July 2025, Scale Computing itself got acquired by a company called **Acumera** and came out the other side with a new name on the door and a new executive team. The edge story still makes sense on paper, but after living through Broadcom and VMware, anytime a company you're trusting with your infrastructure gets swallowed and reshuffled, you watch it closely. So far, so good — but keep your eyes on it.

### VergeIO — the one that impresses me most

A small company that was barely on the radar two years ago just put up a year most startups would kill for: **recurring revenue up more than 80%**, and a new customer list that would blow any CTO away — Boeing, Raytheon, General Dynamics, NASA, Dana-Farber, and Topgolf.

And they've got the migration receipts to back it up:

- **Topgolf** ripped out their VMware-on-VxRail deployment and replaced it with VergeOS.
- An insurance company called **Alinsco** did a full cutover off VMware *during normal business hours* — zero downtime, no maintenance window.

They're also innovating with native AI infrastructure baked into the platform, and they recently announced Veeam enterprise backup support.

In fairness, 80% growth is incredible, but it's 80% off a small base — don't confuse momentum with market share. They're still a small company with a single product. But they're making a real impact, they're on the board, and they're charting a trajectory that's hard to ignore.

### XCP-ng — for the open-source diehards

If Proxmox is the *popular* open-source pick, **XCP-ng** (and the company behind it, **Vates**) is the *principled* one. It's built on the Xen hypervisor, it's fully open, and there's a real story here for anyone who cares about never being locked into a single vendor again. After the last two years, that's a lot of you.

Vates spent this stretch growing up. They **tripled their headcount**, took their 8.3 release to long-term support in 2025, and got their hyperconverged storage officially supported. There's also a strong **European data-sovereignty** angle pulling in customers who specifically want infrastructure that isn't owned by a US megacorp. XCP-ng also cleared its own hurdles — finally beating the old 2 TB max volume limit — and picked up Veeam enterprise backup support.

The honest catch: XCP-ng is betting on **Xen**, while most of the industry's momentum is behind **KVM**. In fact, every hypervisor I've mentioned so far, except VMware, is running KVM under the covers. That's not automatically wrong, but it's a smaller pond. For the right shop — especially in Europe — it's a fantastic fit, and its shared history with Citrix means it'll feel comfortable to anyone who remembers how VMware used to work back in the day.

---

## The Newcomers

The chaos of the last two years didn't just lift up the players already in the game. It pulled in brand-new ones — and a few old names you forgot were even in this fight.

### HPE Morpheus VM Essentials — the serious newcomer

When HPE looked at the bonfire Broadcom started, they basically said, "You know what? We'll take some of that." And they came in swinging at the exact thing everyone's furious about: the bill.

**VM Essentials is priced at $600 per CPU socket per year, support included.** That word — *socket* — is the whole trick. Most vendors charge per core, and modern chips pack in a *ton* of cores. HPE's per-socket pricing means that on a big, high-core server, the cost comes out dramatically cheaper. HPE claims up to a **90% cut in licensing costs** versus what you were paying VMware.

It's built on KVM, it can manage your new clusters and your existing VMware side by side while you transition, and Veeam already supports it.

The catch is simply time. It's new. It hasn't been battle-tested at massive scale yet, and it's still filling in features to match vSphere. But here's why it matters anyway: this is **HPE**. They've got the sales force, the support org, and the enterprise relationships to put this in front of every VMware customer on the planet. That alone makes them a threat.

### Citrix XenServer — a name from the past

Yes, *that* XenServer — the one most people assumed was on life support. In 2025, Citrix reopened it to run **all** workloads (not just its own) and promised a XenServer 9 on the way.

The reception was… polite. The general press take was that Citrix wandered back to the hypervisor party without really bringing anything new. So file this one less under "serious contender" and more under "proof of how hot this market suddenly got." When even Citrix wants back in, you know there's blood in the water.

### OpenStack — the comeback nobody saw coming

For years, OpenStack was the thing companies deployed and then quietly regretted. But the VMware exodus handed it a second life. It's shipped three solid releases in this window — **Dalmatian, Epoxy, and Flamingo** — and it's openly chasing VMware refugees, even building in tooling that monitors your VMware and OpenStack environments together to help you plan the jump.

But I've got to be straight with you, because this is the one people get burned on. OpenStack is incredibly powerful and incredibly complicated. It is **not** a VMware drop-in. If you don't have a real platform team that lives and breathes this stuff, OpenStack will eat you alive. For big telcos and sovereign clouds that have that team, it's a fantastic fit. For a three-node shop? Move along — or lean on **Platform9** and others who make deploying and managing OpenStack easier.

### Oracle — it exists

Oracle has a virtualization product too. It exists. You *can* migrate to it. But I wouldn't. If you don't trust Broadcom, you definitely shouldn't trust Oracle.

### Microsoft Hyper-V

I want to be fair here, because plenty of you went this way. If you're already a Microsoft shop, Hyper-V is right there, bundled into the Windows Server licensing you're already paying for — and that "free" got awfully tempting the moment Broadcom's invoice landed.

Credit where it's due: Microsoft didn't let it rot. Windows Server 2025 was a real step up — VMs that scale to **240 TB of memory** and over **2,000 virtual CPUs**, GPU partitioning, and even live migration of a VM with a GPU attached. On paper, that's a serious platform.

So why can't I recommend it? Because of where Microsoft is actually steering you. Almost all of their energy is going into **Azure Local** (formerly Azure Stack HCI), which is basically Hyper-V with an on-ramp into Azure. And here's the catch: Azure Local runs on your own hardware, but it only works tethered to an Azure subscription — billed through the cloud, managed through the cloud, dependent on the cloud.

If we learned one thing from the Broadcom mess, it's that you do **not** want to park your workloads on a platform quietly engineered to lock you in. Hyper-V the hypervisor is fine. The direction it's pulling you is the problem. Keep your options open.

---

## The Verdict

So, two years later, what's the verdict?

Let's start with the uncomfortable one. **Broadcom won.** Not with us, not with the industry as a whole, but on their own terms. They set out to trade a giant pile of small and midsize customers for a smaller pile of huge ones paying a whole lot more — and that's exactly what happened. If you were hoping this story ends with Broadcom getting punished by the market, it doesn't. At least not yet. The money's up, the strategy worked, and they just decided most of us weren't worth keeping.

But here's the part they probably didn't intend: in trying to squeeze the market, they cracked it wide open.

Because the other big lesson is this — **there is no single VMware killer**. And honestly? That's the good news. Two years ago we had one obvious answer for everything. Today you've got a real menu, and the right pick depends entirely on who you are:

- **Big enterprise that just wants something that works?** → Nutanix
- **Already heading toward containers and Kubernetes?** → Red Hat OpenShift
- **Small shop, homelab, or just sick of the giant bills?** → Proxmox
- **Hundreds of edge sites with nobody on the ground?** → Scale Computing
- **Care about open source, data sovereignty, and never getting locked in again?** → XCP-ng
- **Want a lean private cloud with a surprisingly serious customer list and real energy?** → VergeIO
- **Already an HPE shop chasing the lowest possible licensing bill?** → Morpheus VM Essentials

For the first time in a long time, you actually get to choose the right platform for *you* — and you've got a lot of genuinely great choices.

And that's the real story of these two years. It was never really about VMware getting worse, because the product didn't. It was about a monopoly that got too comfortable, pushed too hard, and woke up an entire industry that had stopped competing. Broadcom didn't kill virtualization. They restarted it.

So, two years ago, I told you this was going to blow the virtualization world apart. Was I right? … Partially. It *did* blow apart — the VMware we knew is gone. But what grew back in its place is healthier, more competitive, and frankly a lot more interesting than what we had before.

Now I want to hear from you. **What did you migrate to — and be honest, do you regret it?** Drop it in the comments, and feel free to tell me how wrong I am while you're at it. Perspectives matter, and I'd love to hear your story.

---

Thanks for reading, folks, and thank you to the fine people who support us through **Patreon** and the **YouTube Membership** program. If you'd like to support what we do here, consider checking those out. Join our community **Discord** and chat with me and like-minded homelabbers, geeks, and nerds — and as always, we'll see you on the next one!

---

## References

I make a lot of assertions throughout the video and in this blog post. Below is the full list of reference articles that were part of my researching and reading.
https://www.networkworld.com/article/4053783/broadcoms-vmware-strategy-pays-off-financially-but-customers-not-as-keen-as-wall-street.html
https://www.sec.gov/Archives/edgar/data/0001730168/000173016825000094/avgo-08032025x8kxex99.htm
https://www.softwareseni.com/broadcom-vmware-pricing-changes-understanding-the-licensing-crisis-driving-migration/
https://vmwaremadesimple.com/articles/broadcom-vmware-licensing-breakdown.html
https://www.starwindsoftware.com/blog/vmware-licensing-changes/
https://www.ciodive.com/news/broadcom-vmware-siemens-licensing-lawsuit/744414/
https://www.cfodive.com/news/broadcom-vmware-siemens-licensing-lawsuit/744716/
https://www.redwoodcompliance.com/broadcom-vmware-suing-siemens-legal-battle-over-vmware-licensing-heats-up/
https://allaboutlawyer.com/broadcom-lawsuit-2025-fidelity-warns-of-massive-outages-as-vmware-price-war-explodes-into-legal-case/
https://www.networkworld.com/article/4028032/broadcom-blocks-vmware-patch-access-for-perpetual-license-holders.html
https://www.networkworld.com/article/3982111/broadcoms-licensing-clampdown-subscription-less-vmware-users-face-legal-ultimatum.html
https://www.techzine.eu/news/privacy-compliance/131220/broadcom-sends-cease-and-desist-letter-to-holders-of-perpetual-vmware-licenses/
https://www.computerweekly.com/news/366623726/Broadcom-letters-demonstrate-push-to-VMware-subscriptions
https://blogs.vmware.com/cloud-foundation/2025/07/16/vmware-cloud-foundation-9-ushers-in-new-support-model-and-release-cadence/
https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsphere.html
https://vninja.net/2025/06/17/vcf9-is-here-what-does-that-mean/
https://www.theregister.com/2025/04/14/vmware_free_esxi_returns/
https://virtualizationreview.com/articles/2025/04/18/esxi-is-free-again.aspx
https://www.techtarget.com/searchcloudcomputing/news/366623684/Nutanix-expands-storage-Kubernetes-and-AI-platforms-at-Next
https://www.networkworld.com/article/3982198/nutanix-partnerships-target-storage-ai-workloads-as-it-aims-to-take-on-vmware.html
https://www.nutanix.com/blog/top-next-2025-news
https://www.nutanix.com/press-releases/2025/nutanix-announces-cloud-native-aos
https://juliendumur.fr/en/nutanix-next-2025-keynote-dell-and-pure-storage-external-storage-support/
https://dcig.com/2026/04/nutanix-external-storage-qualifications/
https://vmblog.com/bylines/nutanix-rewrites-the-storage-rulebook-at-next-2026-netapp-everpure-dell-and-a-partner-ecosystem-built-for-the-ai-era/
https://www.dell.com/en-us/blog/new-chapter-in-choice-dell-powerstore-coming-to-nutanix/
https://www.sec.gov/Archives/edgar/data/0001618732/000117184325007590/exh_991.htm
https://adtmag.com/articles/2026/05/15/red-hat-expands-openshift-virtualization.aspx
https://www.techtarget.com/searchitoperations/news/366624593/Red-Hat-OpenShift-Virtualization-roadmap-chases-VMware
https://www.redhat.com/en/blog/join-red-hat-openshift-virtualizations-momentum-2025
https://www.computerweekly.com/news/366623794/Red-Hat-touts-OpenShift-Virtualization-momentum
https://www.theregister.com/2025/12/05/proxmox_datacenter_manager_1_stable/
https://www.proxmox.com/en/about/company-details/press-releases/proxmox-datacenter-manager-1-0
https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-8-3
https://www.sdxcentral.com/news/proxmox-unveils-data-center-manager-to-entice-vmware-users/
https://www.dawnliphardt.com/veeam-accelerates-hypervisor-strategy-whats-planned-for-2026/
https://2guystek.tv/backup/virtualization/proxmox/veeam/2026/01/12/veeam-v13-linux-appliance-review.html
https://www.scalecomputing.com/press-releases/scale-computing-strengthens-edge-partnership-with-lenovo-thinkedge-se100-solution-priced-for-vmware-alternative-and-edge
https://www.sdxcentral.com/news/lenovo-scale-computing-join-vmware-substitute-bandwagon/
https://www.prnewswire.com/news-releases/scale-computing-strengthens-edge-partnership-with-lenovo-and-expands-availability-of-thinkedge-se100-solutions-302644167.html
https://www.scalecomputing.com/press-releases/acumera-acquires-scale-computing
https://www.datacenterdynamics.com/en/news/edge-compute-firm-scale-computing-acquired-by-acumera/
https://blocksandfiles.com/2025/07/31/scale-computing-acquired-by-acumera-which-becomes-scale-computing/
https://forums.veeam.com/rhv-olvm-sc-hypercore-xcp-ng-hpe-vme-f62/scale-computing-support-t66239.html
https://www.businesswire.com/news/home/20260203704161/en/VergeIO-Records-Strong-Growth-as-Private-Cloud-Operating-System-Strategy-Accelerates
https://techintelpro.com/news/ai/enterprise-ai/vergeio-achieves-80-arr-growth-in-2025-as-vmware-alternative
https://www.businesswire.com/news/home/20251021189410/en/VergeIO-Delivers-Complete-Infrastructure-Modernization-with-VergeOS-26
https://www.businesswire.com/news/home/20260224622661/en/VergeIO-Strengthens-Disaster-Recovery-and-Performance-with-VergeOS-26.1
https://www.verge.io/press-release/vergeio-simplifies-vmware-migration-100-vms-migrated-in-seconds/
https://www.verge.io/resources/news/
https://xcp-ng.org/blog/2025/06/16/xcp-ng-8-3-is-now-lts/
https://docs.xcp-ng.org/releases/release-8-3/
https://xcp-ng.org/blog/2024/10/07/xcp-ng-8-3/
https://www.vladan.fr/veeam-backup-and-replication-plug-in-for-xcp-ng-enters-public-beta-powering-up-xen-hypervisor-backups/
https://www.starwindsoftware.com/blog/veeam-xcp-ng-vmware-backup-alternative/
https://helpcenter.veeam.com/rn/veeam_backup_13_0_2_release_notes.html
https://xen-orchestra.com/blog/xen-orchestra-6-1/
https://www.nextplatform.com/2025/05/14/taking-on-vmware-hpe-mashes-up-vm-essentials-with-morpheus-cloud-controller/
https://www.hpe.com/us/en/morpheus-vm-essentials-software/vmware-alternative.html
https://www.hpe.com/us/en/morpheus-vm-essentials-software.html
https://www.theregister.com/2025/12/03/hpe_morpheus_discover/
https://www.starwindsoftware.com/blog/hpe-vm-essentials-overview/
https://www.techzine.eu/news/infrastructure/139430/veeam-officially-launches-its-hpe-morpheus-vm-essentials-support/
https://www.theregister.com/2025/07/10/citrix_returns_to_mainstream_hypervisors/
https://www.citrix.com/blogs/2025/07/09/xenserver-now-available-for-all-workloads/
https://www.xenserver.com/latest-updates
https://www.theregister.com/2025/10/01/openstack_flamingo_release/
https://www.theregister.com/2025/04/03/openstack_epoxy_released/
https://www.itprotoday.com/openstack-cloud/openstack-epoxy-2025-1-release-expands-vmware-migration-ai-performance
https://www.openstack.org/software/openstack-dalmatian-2/
https://releases.openstack.org/flamingo/schedule.html
https://www.gartner.com/reviews/market/server-virtualization/compare/citrix-vs-oracle
https://www.vinchin.com/vinchin-help-tutorials/migrate-vm-from-xenserver-to-olvm-in-vinchin-backup-recovery.html
https://storageioblog.com/hyper-v-is-alive-enhanced-with-windows-server-2025/
https://www.microsoft.com/en-us/windows-server/blog/2024/11/04/windows-server-2025-now-generally-available-with-advanced-security-improved-performance-and-cloud-agility/
https://virtualizationreview.com/articles/2025/07/28/windows-server-2025-nine-months-in.aspx
https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/overview
https://www.ciodive.com/news/broadcom-att-vmware-settlement-licensing-support-lawsuit/733763/
https://blogs.vmware.com/cloud-foundation/2025/06/17/whats-new-in-vmware-cloud-foundation-9-0/
https://www.nutanix.com/blog/netapp-and-nutanix-announce-technical-partnership#
https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-2
https://www.veeam.com/blog/veeam-backup-for-proxmox.html
https://www.businesswire.com/news/home/20260106547490/en/Alinsco-Insurance-Completes-Zero-Downtime-VMware-Exit-with-VergeOS