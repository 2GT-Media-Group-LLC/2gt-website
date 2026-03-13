---
layout: post
title: "OpenMetal.io: Your Hosted Private Cloud Alternative to AWS, Azure, and GCP"
date: 2026-03-13
categories: [Cloud, Infrastructure]
tags: [OpenMetal, OpenStack, PrivateCloud, Cloud, Ceph, Infrastructure]
description: "A sponsored deep-dive into OpenMetal's hosted private cloud platform built on OpenStack and Ceph — and why it's a compelling alternative to AWS, Azure, and GCP for businesses with steady-state workloads."
banner: https://img.youtube.com/vi/8CLw9tEs8oc/maxresdefault.jpg
---

![](//youtu.be/8CLw9tEs8oc)

We talk a lot on this channel about on-premises virtualization and building your own private clouds. But there's a whole other side of the world that doesn't live in a rack in your company's server room — it lives in public cloud and hosted private clouds.

Late last year, I had the opportunity to meet the company **OpenMetal.io**, whose entire focus is on helping organizations regain control of their cloud infrastructure, especially when their public cloud costs start drifting towards unaffordable, and the benefits of their closed-source system start to look more like risks. OpenMetal delivers dedicated hosted private clouds built on OpenStack and Ceph, designed for teams that want performance consistency, architectural clarity, and cost boundaries they can actually plan around.

So, in this video, we're going to take a look at what that model looks like in practice, and how to recognize when your organization has reached the tipping point where elasticity stops being the advantage and predictability becomes the priority. Let's get to it!

---

## What Is OpenMetal?

Hey there homelabbers, self-hosters, IT-pros, and engineers. Rich here! When OpenMetal reached out to see if I'd be interested in checking out what life is like in a real hosted private cloud — where the servers, storage, and networks you're running on are dedicated to only you, and not shared with other tenants like in a public cloud — I couldn't turn it down!

When I think about the "cloud," I immediately think of being one of many users, all sharing the same hardware. But with OpenMetal, dedicated means dedicated to only you, down to the root-level of your environment.

Before we get into the good stuff, let's get the formalities out of the way. **OpenMetal.io is sponsoring this video**, and I have to say, I'm excited they have, because it's been a really eye-opening experience — not only to see what they've created, but also to learn how their hosted private cloud solves a lot of problems companies in public clouds face.

OpenMetal's Infrastructure-as-a-Service platform delivers a hosted private cloud built on **OpenStack and Ceph**, running on dedicated, single-tenant servers. They provide all of the cloud-native components you'd expect — compute, networking, block storage, APIs, and automation — with a focus on:

- **Fixed capacity** and predictable performance
- **Transparent costs** with no per-resource billing surprises
- **Full root access** to workloads, cluster configuration, and networking
- **Hardware visibility** — you know exactly what's running where and how it's performing

Unlike a traditional hyperscaler where every resource you deploy has an individual cost, OpenMetal provides your own private cloud on a hardware stack that isn't shared with anyone. This approach simplifies budgeting, planning, and forecasting of your cloud spend. It's infrastructure for businesses with steady workloads who want all the benefits of being in the cloud, but with ownership, consistency, and the ability to actually plan.

OpenMetal launched its IaaS platform in 2022 and has datacenters across three continents — US, Europe, and Asia — with key regions including Los Angeles, Ashburn, Amsterdam, and Singapore.

---

## The Inflection Point: When Public Cloud Stops Making Sense

There's an inflection point that almost every growing engineering team hits with their cloud infrastructure. In the beginning, the public cloud makes perfect sense. You move fast, you don't think about hardware, you spin things up, experiment, and iterate. But after that point, things change.

Workloads stabilize, usage and production move towards a steady-state, and you realize you're paying for elasticity you're not really using. Then your management starts asking harder questions about where your workloads are running, what they're actually consuming, and how predictable the bill is going to be.

That's really the problem OpenMetal was built to solve. It's not that AWS, Azure, or GCP are bad. Quite the opposite — they're incredible for the early growth and experimentation phase. But there's a stage at which predictability, visibility, and cost boundaries matter more than raw elasticity.

Here's a good example: in the public cloud, every single resource you deploy has a cost associated with it. Each VM, virtual disk, network interface, and each gigabyte of data sent and received. Something goes sideways, and all of a sudden you have a massive unexpected bill you didn't account for. In contrast, in a hosted private cloud, you're buying defined capacity on dedicated hardware, so your costs are going to be the same regardless of utilization in your stack. For those with more steady-state infrastructure, it's financially smarter to repatriate those workloads out of the public cloud and into a hosted private cloud.

### The OpenStack Advantage

Then there's the broader shift happening in the closed-source ecosystem — like we saw with the acquisition of VMware by Broadcom. For years, many teams built their operational muscle memory around VMware-style infrastructure. Those recent shifts have prompted a lot of organizations to re-evaluate their long-term strategy and rethink what platform stability actually means.

Moving towards a more open-source platform like OpenStack gives you the flexibility to move your workloads anywhere you please. OpenStack supports common VM image formats such as **VMDK, QCOW2, and more**, so you can migrate your workloads instead of rebuilding them.

Building on a widely adopted, API-driven, community-supported foundation where no single vendor controls your roadmap means greater certainty, future stability, and flexibility for your workloads — with no rug-pulls.

---

## OpenMetal's Three Offerings

OpenMetal has three offerings that allow companies to choose the right way to serve their business.

### 1. Hosted Private Cloud

The flagship offering — and the one we're going to dig into here — is their **Hosted Private Cloud**. Built on top of OpenStack and Ceph on dedicated servers that only serve your workloads, you pay for the service you want, you decide how to size your VMs, and you control workloads while OpenMetal takes complete care of the physical infrastructure.

OpenMetal has a [Private Cloud Deployment Pricing Calculator](https://openmetal.io/cloud-deployment-calculator/) to help you choose various flavors of the cloud, attach compute and storage, and see what your costs can look like.

### 2. Bare Metal

OpenMetal's bare metal offering gives you raw, dedicated hardware with full access down to BIOS/IPMI, predictable pricing, high-bandwidth networking, and a big list of modern server hardware options — everything from lots of RAM and multi-core CPUs to NVMe-heavy storage configurations.

It's built for heavy workloads like virtualization clusters, big data, high-performance computing, or anything that needs consistent I/O and no noisy-neighbor interference. OpenMetal also offers dedicated GPU infrastructure you can deploy standalone or integrate with your private cloud.

### 3. Ceph Storage Clusters

OpenMetal's storage clusters are standalone Ceph storage built for performance and scale — not an abstracted blob store or S3 bucket with hidden limits. You get a distributed storage cluster with block, object, and file capabilities that scales horizontally and keeps data replicated and highly available. You can tune performance and redundancy, add capacity on demand, and connect it back to your private cloud or bare metal environment over high-speed networking.

### Building Blocks, Not Silos

The best part is these aren't siloed products — they're building blocks. You can start with Hosted Private Cloud, add Storage Clusters when you need more capacity, and drop in Bare Metal for things like edge services, specialized appliances, or heavy compute — all interconnected over OpenMetal private networking so it feels like one unified infrastructure footprint.

---

## Diving Into OpenMetal's Horizon Interface

One of the big things that has always annoyed me about hyperscalers is how disconnected we've become from what's actually happening behind the scenes. As an infrastructure architect in my day job, it's always eaten at me that, as customers, we've given up the control of choosing what hardware and platforms our cloud workloads run on.

Up until I met with OpenMetal, I thought that's just the way it is. Turns out, that's not the case — and that's a big value add. When you know exactly what hardware your workloads are running on and how your networking is built, you can actually model performance and capacity. You're not guessing how many hidden metered services are attached to your architecture; you're running inside a defined infrastructure boundary.

Since OpenMetal's hosted private cloud is built on OpenStack, they give you direct access to **Horizon** — OpenStack's native management interface. You can also manage your environment through the OpenStack APIs, CLI tools, SDKs, and infrastructure-as-code workflows. For those familiar with OpenStack, you're going to feel right at home — other than some simple branding, you're getting direct access to Horizon with no abstracted UI to mess with.

Let's run through the key sections.

### Compute

**API Access** — This is where you work with OpenStack from the outside using CLI tools, SDKs, or automation. It lists the service endpoints for your project and gives you the option to download your OpenStack RC file so you can authenticate from the command line or scripts.

**Overview** — Your project's status dashboard. It gives you a concise summary of how many instances you're running, how much vCPU, RAM, and storage you're consuming, and how that usage compares to your quotas.

**Instances** — The primary view you'll live in. Here you manage virtual machines — their status, flavor, IP addresses, and power state. From this page you handle day-to-day lifecycle tasks: launching new instances, starting and stopping them, rebooting, connecting to the console, creating snapshots, and deleting workloads you no longer need.

**Images** — Your catalog of boot sources for new instances, including base OS templates, golden templates, and snapshots you've turned into reusable images. This is also where you'd manage workload migrations from other platforms. If you're moving off VMware or another platform, you can import converted images, turn them into templates, and redeploy your workloads inside your private cloud in a controlled, phased way.

OpenMetal has a solid list of pre-made images to start building from right out of the gate — CentOS Stream, Rocky, Debian, Fedora CoreOS, and Ubuntu. You can also create your own images and import a variety of image formats.

**Key Pairs** — Where you manage SSH keys used to log in to your instances without passwords. Create a new key pair to generate a private key for download, or import an existing public key. When you launch an instance, you attach one of these key pairs so the public key is injected into the guest.

**Server Groups** — Where you define placement policies for related instances. Create affinity and anti-affinity rules that govern where the scheduler places instances based on what groups you place them in. For people who've been using on-premises virtualization, affinity is one of those things that is incredibly important, and I'm glad to see that feature exists here.

### Volumes

**Volumes** — Where you manage block storage for your deployed instances. Create, resize, and delete volumes, view which instances they're attached to, and create snapshots for backup or cloning purposes.

**Backups** — Where you manage block storage backups for your volumes. These backups are stored in a separate Ceph pool. Ceph natively has a replication of 3, so there are always 3 copies of the data, giving you redundancy in case of node failure.

**Snapshots** — Point-in-time copies of your volumes. View, edit, and delete existing snapshots, use them as a source to create new volumes, or launch new instances from them. If you've spent any time in virtualization, you know the value of snapshots as a means of short-term recovery when testing updates or system changes.

**Groups** — Manage logical collections of related volumes so you can operate on them as a unit. Useful for applications that use multiple volumes and need consistent handling, such as a database and its log volume. Create group snapshots to capture consistent point-in-time copies across all volumes at once.

### Containers (Kubernetes)

OpenMetal's OpenStack platform also features container infrastructure, which means you can bring your Kubernetes right into your hosted private cloud and manage everything in one place.

**Clusters** — Manage your container orchestration clusters. Create new clusters based on predefined templates, scale worker nodes up or down, and download kubeconfig credentials.

**Cluster Templates** — Define the blueprint for how container clusters are built. OpenMetal provides a ready-to-use Kubernetes cluster template so you can spin up clusters quickly. This is native OpenStack container integration — not a custom OpenMetal layer. OpenMetal publishes [Kubernetes guides on their documentation site](https://openmetal.io/docs/manuals/kubernetes-guides) for those who want to dig deeper.

### Networking

The networking section is where the "private cloud" part gets real. You're not just clicking buttons — you're designing networks the same way you would on-prem: segmentation, isolation, routing, and controlled exposure, but in software.

**Network Topology** — A visual map of how your OpenStack networking is wired. It shows routers, networks, and subnets along with connected instances and floating IPs. You can quickly see how traffic flows in and out, and the graph tab creates a clear network diagram that helps you get the full picture. Love it.

**Networks** — Manage virtual networks available to your project. Create and edit networks and subnets, choose whether they are shared or project-scoped, and control IP ranges, gateways, and DHCP settings. Want a typical setup with a public-facing network for load balancers and a private application network behind it? You can model that cleanly.

**Routers** — Manage Layer 3 routing for your project's networks. Create and configure virtual routers, attach internal subnets as interfaces, and connect those routers to an external network for north-south traffic. These act as a simple NAT so that internal networks can access the Internet without burning through public IP addresses.

**Security Groups** — Virtual firewall rules that control traffic to and from your instances and ports. Rules are enforced at the virtual NIC level, letting you define clear, reusable access policies for different workload types. OpenMetal includes a few pre-created security groups right out of the gate as great examples.

**Load Balancers** — Configure and manage L4 and L7 load balancing for your applications. Create load balancers, define listeners on specific ports and protocols, and build pools of member instances. Perfect for exposing a service behind a single virtual IP and distributing traffic across multiple instances.

**Floating IPs** — Manage public IP addresses that can be mapped to instances or ports. Allocate new floating IPs from an external network, associate them with specific instances or ports, and release them when no longer needed.

OpenStack also includes **VLAN trunking** for carrying multiple segmented networks over a single interface, **Network QoS** for shaping traffic policies, and built-in **VPN support** for creating secure IPsec tunnels between your cloud networks and external environments.

The depth and flexibility in networking is something I have to admit I was pretty impressed by. In hyperscalers, every layer of networking feels like a separate product with a separate billable line item. In OpenMetal's private cloud, the tooling is part of the platform. And on the bandwidth side, OpenMetal's outbound traffic is typically billed using a **95th percentile model** rather than per-gigabyte micro-metering — making bandwidth costs far more predictable as you scale.

### Orchestration & Object Store

**Orchestration (Heat)** — Manage infrastructure as code. Work with templates that define instances, networks, volumes, and other resources as a single stack. Launch, monitor, update, and delete those stacks — and inspect individual resources and events when something goes wrong.

**Object Store (Swift)** — Work with object-based storage instead of block volumes. Create and manage containers (like buckets), upload, download, and organize objects, and control access by making containers private or public. Ideal for durable, scalable storage for files, backups, logs, or application assets.

### Admin & Identity

**Admin** — The control plane for the OpenStack cloud as a whole rather than a single project. Watch overall usage, manage global resources, work with hypervisors and host aggregates, define shared flavors and images, and review quotas and usage across projects.

**Identity** — Control who can access the cloud and what they're allowed to do. Manage domains, projects, users, and groups, and assign roles that define permissions for each. Work with application credentials and, in some deployments, federation or external identity sources.

**Workflow (Mistral)** — Manage multi-step operational task automation. Define workflows for common or complex operational procedures — provisioning, maintenance actions, or integrations that touch several OpenStack services — and run them in a controlled and auditable way.

---

## Final Thoughts

I gotta say, I'm really impressed with what OpenMetal has built here. The way they've combined ease of deployment and the performance of their hardware with the power of OpenStack makes for a very compelling offering.

The public cloud feels like it's getting less and less reliable as time goes on, while getting more and more expensive to operate in — which feels like it's moving in the wrong direction. OpenMetal's real value is giving you back the control, flexibility, and freedom that we all gave up by moving into the public cloud. You get your workloads in the cloud and the flexibility that provides, without losing any of the infrastructure primitives, root access, network segmentation, and more — while gaining predictable performance on dedicated hardware. To me, that's the perfect cloud scenario.

Then there's the cost structure. I know from personal experience that every single thing in the public cloud has a charge. The VM costs money, the network interface for the VM costs money, the disk costs money, the virtual network you connect the VM to costs money — and that's just one workload, not counting ingress and egress fees, VPN connections, and everything else. Hosted private cloud is the answer to that. You're operating inside defined infrastructure capacity on dedicated hardware. Performance is consistent, budgeting is predictable, and scaling decisions are deliberate, not reactive to billing alerts.

As an engineer whose heart is in infrastructure, this is the closest you can come to having the best of both worlds. You control every part of how you provision and deploy your private cloud. You understand exactly what hardware your environment runs on, so you can model capacity and make decisions with a lot more confidence. And on top of that, you get all of the benefits of being in the cloud, engineering support from OpenMetal when you need it, and no worries about the physical hardware.

And finally, being based on OpenStack means you have all of the API-driven Infrastructure as Code functionality, automation, true multi-tenancy, and — the biggest value — a standard open platform with no vendor lock-in.

---

## Closing

Special thanks to **OpenMetal** for giving me the opportunity to really dig into their hosted private cloud platform and see the possibilities for myself. It's really changed my views on how the cloud can actually benefit your business!

If you're a business that's heavily invested in the cloud and looking for how you can take back control of costs, avoid public cloud lock-in, and build the private cloud that works best for your business, head over to [openmetal.io](https://openmetal.io/) and get started today! They have also provided a generous offer just for my channel — link and details in the description below.

Thanks for watching!
