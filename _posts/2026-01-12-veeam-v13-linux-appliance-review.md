---
layout: post
title: "Veeam v13: Native Linux Appliance Review & Proxmox Backup Walkthrough"
date: 2026-01-12
categories: [Backup, Virtualization, Proxmox, Veeam]
tags: [Veeam, Proxmox, Linux Appliance, Backup, Review]
description: "Complete walkthrough of Veeam v13's new Linux appliance, deployment in Proxmox, and honest assessment of features and limitations."
banner: https://img.youtube.com/vi/IUsywSK9Miw/maxresdefault.jpg
---

![](//youtu.be/IUsywSK9Miw)

When Veeam began expanding beyond just VMware and Hyper-V, I was ecstatic. Backup is the long pole in the tent for enterprise virtualization, and that expansion finally gave us real freedom in where we could run — and move — our workloads.

But there was still a catch. Running Veeam meant running Windows. And for many organizations, that was a hard stop.

On November 19th, 2025, that finally changed. Veeam version 13 removed the Windows requirement entirely, or did they?

Let's dig in and take a closer look.

## Understanding Veeam v13

Hey there homelabbers, self-hosters, IT-pros, and engineers! Rich here. Now, before we get into feature lists or release notes, I want to level-set what Veeam v13 actually represents. This isn't about flashy UI changes or minor performance tweaks. This is about architecture.

For the first time, Veeam Backup & Replication no longer requires a Windows Server to run. You can now deploy Veeam as a Linux-based appliance, dramatically reducing overhead, complexity, and license costs — especially in environments that have already moved away from Windows wherever possible.

And that matters a lot right now. Because as organizations continue shifting away from VMware toward platforms like Proxmox, XCP-ng, HyperCore, and VergeOS, the backup layer needs to evolve right alongside them.

So in this article, we're going to look at what's new in Veeam v13, how to deploy the new Linux Backup and Replication appliance, build out a simple backup job for Proxmox users, and just as importantly, where the gaps still are.

## Headline Features of Veeam v13

Let's get the headline features of Veeam v13 out of the way first.

**First**, the biggest feature of v13 is the new **Linux-based Veeam Software Appliance**. This hardened Linux appliance means you no longer need to have a dedicated Windows Server to run VB&R. The appliance handles security patching and hardening updates automatically as well.

**Second**, alongside the Linux appliance, version 13 also brings a **new next-generation WebUI**. This browser-based management UI aims to reduce platform dependencies, adds modern filtering and search, brings an accessibility-ready design, and a built-in dashboard.

**Third**, Veeam now supports **active/passive backup-server clustering** to keep backup management available through outages and disasters. This feature only applies to the Linux appliance version and brings redundancy to critical backup operations that were unavailable to the Windows-only version.

**Fourth**, since we're focused on Proxmox here, Version 13 now has **Application-aware processing support** for backing up Windows VMs that run MSSQL, Oracle, and PostgreSQL. And also introduces malware detection for Proxmox VMs as well.

**Fifth**, V13 sees **native support for Scale Computing HyperCore**. Now customers running HyperCore can natively backup and restore VMs via Veeam without needing an agent and v13 also includes vTPM support for HyperCore as well.

### Additional Features

There are a lot of additional features in v13 that are nearly too numerous to really cover in detail, including:

- Full support for vSphere 9.0
- Universal CDP
- Improvements to agent-based backups
- Support for LTO-10 (36 terabytes per tape!)
- Immutability in GCP
- Instant recovery to Microsoft Azure
- And so many more

## Licensing Options

Before we deploy, let's talk licensing. I'm going to be using the Veeam NFR license, which gives me a total of 20 workloads for backup. If you qualify for the NFR for your homelab, you should get it. But for those who don't, Veeam still provides a free license for homelabs that's good for 10 workloads only.

## Minimum & Recommended Requirements

Before we install v13, we should probably get the minimum and recommended requirements out of the way first, right?

- **CPU**: Multi-core x86-64 processor (Recommended: 8-16 cores)
- **Memory**: 8GB RAM minimum (Recommended: 16GB + 500MB per concurrent job)
- **Storage**: 120GB OS disk minimum (Recommended: 240GB)
- **Backup Storage**: Additional local disk of at least 120GB required
- **Network**: 1Gb Ethernet minimum (Recommended: higher throughput for large environments)

## Deploying Veeam v13 Linux Appliance in Proxmox

Alright, we're going to be deploying this in Proxmox, as I mentioned earlier. I've already downloaded v13 and uploaded the installation ISO to my ISO storage in PVE, so let's build the VM.

Head over to the top right corner and click "create VM". We need to give our VM a name — I'll call mine "Veeam" — and hit next.

### System Configuration

1. **OS Tab**: Select your boot ISO for installation and hit next
2. **System Tab**: Change BIOS to UEFI from default, select a location to store your UEFI storage, and hit next
3. **Disks Tab**: We need to create two physical disks based on the software requirements
   - OS disk: 240GB (as recommended by Veeam)
   - Backup storage disk: 1TB
4. **CPU Tab**: Provision 16 cores (as recommended by Veeam)
5. **Memory Tab**: Allocate 16GB RAM (as recommended)
6. **Networking**: Add the VM to your server VLAN with planned static IP and DNS
7. **Summary**: Select "Start after created" and hit finish

### Installation Process

Now we'll head over to the console tab for our freshly built VM to continue the installation. We want Veeam Backup & Replication, so we'll hit enter to kick that off, and we'll choose "install - fresh install" and hit enter.

Now we wait while the installer starts up. This can take a bit depending on your hardware. We'll get a warning stating the installer will irreversibly wipe any data on the drives attached to this VM. Yes to continue.

Now comes the best part of this installer — **if you've met the minimum requirements for Veeam v13 all the way through, the installer will automatically build the VM**. Seriously, just sit back, relax, and wait for the installer to complete. This will take time depending on your hardware, so chill out and let it complete.

Once the automated installation is complete, the VM will reboot, and we'll get to configuring the basics:

1. **EULA**: Read it or not — tab to Accept and press Enter
2. **Hostname**: Set to match your VM name (e.g., "veeam" as an FQDN), then tab to Next
3. **Networking**: Don't leave it DHCP by default. Tab to "static" on the right, press Enter, fill in the addressing, then tab down to "apply"
4. **Timezone & NTP**: Set your timezone (e.g., America/Los Angeles) and run a sync, then tab to Next

### Security Configuration

Now let's talk passwords. **Veeam is serious about passwords for this hardened Linux appliance**. They've aligned their password policy to the DISA STIG password ruleset, which means you're going to be forced to set a very complex password for the veeamadmin account.

You'll struggle trying to find an acceptable complex password that works. The requirements are pretty strict — you can't use more than four of the same characters in a row. Once you find one that works, you'll move on.

After the password hurdle, Veeam requires you to setup **MFA (Multi-Factor Authentication)** for this account as well. While I appreciate the emphasis on security and agree it's important, I do have bones to pick about this, which we'll discuss later.

In any case, grab your MFA app of choice, scan the QR code, and enter the 6-digit PIN in your app, then tab down to OK.

Veeam v13 also has the concept of a **Security Officer** for approvals of admin actions in line with Zero Trust principles. If your organization requires this or actually has a designated security officer, you can set this up. Thankfully, you can choose to skip this function and use veeamadmin as the sole account. We'll check "skip setting up Security Officer" and tab to next.

Finally, we're shown the summary of our configuration. Tab to finish and press Enter. Veeam will now apply the settings, restart services, and start the web interfaces.

### The Installation Experience

You know, I find it kinda funny that the actual v13 installation process was so magically hands-off in the first half of the deployment, and then you're hit with the password requirements and having to repeatedly fumble through that, only to get hit with a mandatory MFA, completely erasing the efficiency gains in the first half of the install. But as an old English friend of mine used to say, "Into life a little rain must fall."

The Veeam appliance provides two different webUIs that have very different functions and purposes: the **Host management console** (used to manage the VM itself, updates, configurations) and the **Veeam Backup & Replication webUI** (for backup operations).

## Host Management Console

You can find the URL to the host management console by looking at the actual console of the v13 Linux VM itself. Once you toss that URL into a browser, you'll be greeted with a login. Since we opted to only have the veeamadmin account, that's the account we'll use. Enter your one-time PIN that you set up during the install.

The Host Management Console is refreshingly straightforward. Let's get through this quickly:

- **Overview**: Details on Remote Access, Network configuration, and time settings
- **Network**: Manage hostname, join Active Directory Domain, add DNS suffixes, modify the IP address of the Veeam host
- **Time**: Change timezone and manage NTP for your host
- **Remote Access**: Control access to local SSH and to the Host management GUI itself
- **Users and Roles**: Create local users and manage their roles in a granular RBAC fashion; apply roles to domain-joined sources
- **Backup Infrastructure**: Configure remote monitoring, configure high-availability, enable lockdown mode, and run configuration restore
- **Updates**: Manage updates (requires valid license; more on this later)
- **Logs and Services**: Status of running services, host configuration files, installed components, audit trail of events, and ability to create a support bundle

Host management is incredibly straightforward, and outside of troubleshooting, you'll spend very little time there.

## Veeam Backup & Replication WebUI

Similar to the Host management console, you can find the URL for VBR on the console of the active v13 VM. Unlike the management console that uses port 10443, the VBR console uses the default SSL port of 443.

This site, like the last, has a self-signed certificate, so click through it to get to the login page.

Once logged in, we land on the overview page and are greeted with a rather crucial message:

> "If you notice features missing, it likely hasn't made its way into the Web UI just yet. For now, feel free to use the Windows-based console whenever you need to manage settings that aren't available in the Web UI. We will iterate quickly and bring more features over to the new UI with every minor release, prioritizing workloads and features based on their actual usage."

### The Reality of the WebUI

I'm going to jump in here and get to the point: **the WebUI is not feature-complete to their full 'fat' client**. Right now, you can really only manage VMware and Hyper-V backups from the WebUI. You need to download and install the full client, **ON WINDOWS ONLY**, to configure and back up Proxmox, Nutanix, Scale Computing, and any agent-based backup jobs.

The next popup alerts you that you don't have a valid license installed in Veeam. Go ahead and take care of that by clicking "Install" on the left and double-clicking on your license file.

Veeam will import your license and start checking for updates. Once done, you land on the Overview page which provides detailed information like:

- Resiliency Overview
- Threat hunting
- Infrastructure Health
- Protected Workloads
- Protection Overview
- Top Repositories

Since this is a fresh install, it's all looking very blank.

### WebUI Sections

**Jobs**: See your currently configured backup jobs and backup copy jobs. Keep in mind that currently only VMware and Hyper-V jobs will show in this view. You can add new jobs by clicking "Add" in the middle.

**Backups**: See a list of backed up jobs that you can restore from — instant VM recovery, regular restore, access guest files, or delete.

**Repositories**: Create and manage your backup repositories. In Veeam language, a repository is a storage location for backups. You'll have a single default repository auto-generated during install. You can also manage Scale-out repositories and configure Veeam's Data Cloud Vault (Veeam's cloud storage offering).

**Proxies**: In Veeam, a Proxy is used to move data between VMs and the backup storage. Proxies increase concurrent backup capacity and distribute load across multiple systems. By default, the Linux system is also a VMware backup proxy. The current WebUI only supports VMware and Hyper-V proxy management.

**Managed Servers**: See your deployed Veeam server. This section is where you'd add virtual hosts and Linux hosts. Currently, the WebUI only supports adding Hyper-V, VMware, and individual Linux and Windows hosts.

**Logs and Events**: Full audit trail of backup job status, Authorization events for change control requests, and any discovered malware events. It's nice to have these easily accessible via the web.

**Veeam ONE**: Veeam ONE is Veeam's monitoring suite for their software. At this time, VeeamONE requires a Windows host to install, so it's behind on Veeam's VB&R track. You can integrate it into your Linux v13 deployment here.

### The WebUI Limitation

**I'm just going to come out and say it now: the Veeam WebUI is woefully lacking feature completeness at this time.** Now it makes sense why they hit us with that pop-up on first login. To say that I wasn't disappointed by the sheer lack of any backup management support for anything BUT VMware and Hyper-V was a pretty huge letdown, frankly.

So what do we do now? We need to install the v13 fat client on a Windows system and backup Proxmox since that's the only way to do it currently.

## Installing the Windows Console

To download the Windows v13 console, head over to configuration on the top right, then down to About, and then click on "Download Windows-based backup console." Once that's downloaded, run through the install.

The install takes some time to complete, but it's basically a next, next, finish sort of installation.

## Using the Windows Client

Once you've installed the fat client and launched it, you'll need to enter the IP address or hostname of your VBR Linux and click Connect.

Now enter the veeamadmin user and complex password, and click sign-in below.

For existing Veeam users, this is going to look and function essentially the same as version 12, so we won't spend a ton of time going through all the nuances of the full client. But let's do a quick once-over for completeness.

### Interface Overview

When we log in, we first land on the **Home** section, which gets straight to business with backup jobs and their status. Obviously, this is a fresh install with no backup jobs setup yet.

**Inventory Tab**: Shows discovered infrastructure and protected objects. Finally, we can see all of the virtual platforms that version 13 supports — from VMware and Hyper-V to Nutanix, Proxmox VE, and SC HyperCore.

**Backup Infrastructure Tab**: Where you design, register, and manage the components that do the backup work. If the Inventory tab is what exists, the Backup Infrastructure tab is how backups happen.

**History**: A history of the events that have occurred on your VBR host.

## Adding Proxmox & Creating Backup Jobs

Let's get our Proxmox host added to Veeam. Head back to the Inventory tab, then to the Proxmox VE section. Click "Add Server" in the top left.

First, tell Veeam the DNS name or IP address of your Proxmox server. I'll add "Proxinator" and hit next.

Veeam needs root credentials to interact with the Proxmox host. Click "add" on the far right, enter your root user and password, give it a description, and click OK. Note: starting in version 13, you can add non-root accounts that can do privilege escalation when needed.

Click "Next" to continue. Veeam will call out and connect to your host. Since this is the first time, you're asked if you wish to trust the SSH RSA key. Say yes to continue.

Next, you're asked if you're willing to trust the SSL certificate for your Proxmox host. If you're using a self-signed SSL cert (as I am), this message will appear. If you're using a publicly trusted cert, it won't. Click Continue.

Now select a storage location to save snapshots in case the original VM's storage doesn't support snapshotting. You can leave this set as-is or manually select a storage location. Once done, click Apply.

Sit back and let Veeam complete the server add process. The next screen shows a summary, and finish completes the wizard.

### Deploying the Worker Node

Veeam requires a worker node to move data from the PVE host to the backup system. A worker node is a lightweight VM that will be added to your PVE host. Click Yes to kick this off.

1. Give your worker a name
2. Choose which storage location on your PVE host this VM will live
3. Click next
4. Choose a network to attach the worker VM to. Click "add," select your PVE SDN network, leave the network config set to DHCP, and click OK
5. Click Finish to kick off the deployment

This can take a bit to complete. And boom, done!

For reference, this is essentially identical to how we added PVE to Veeam in v12, so no surprises at all.

### Building the Backup Job

Now we see the newly added "Proxinator" under the Proxmox VE section. If we click on it, we see the full list of virtual machine workloads running on the box.

> **Important Note:** Veeam v13 still does not support backing up LXC containers. Even in version 13, there's no way to backup LXC containers in Proxmox. PBS can backup LXC containers all day long, but Veeam still doesn't have complete support for all workloads.

Let's build our backup job for Proxmox. Head over to Home, then at the top, click "Backup Job" and select "Virtual Machine."

Give your backup job a name (e.g., "proxmox backup") and head down to Next.

Now select the VMs you want to backup. Click "add" on the right, expand your PVE host, and select the backups you wish to add. Once done, click OK. Everything looks good, so click Next.

Choose where you want to store these backups. We only have one backup repository that was provisioned automatically during deployment. Next, decide how long to retain your backups — I'm a fan of 15 days, so I'll enter in 15 and then click next.

Next is **guest processing**. V13 brings guest processing to PVE backups, which is a big deal. Administrators of SQL and others know the importance and sensitivity around being able to have the backup system reach into a VM and tell it to do something before backups run to guarantee the data being backed up is valid and correct. This is fantastic for PVE. If you don't have any applications requiring application awareness, click Next.

Now set up a schedule for the backup job to run automatically. I'll set this to run nightly at 4 AM. The default retries are fine as-is, so click Apply.

The final page is just a summary of your settings. There's a checkbox to kick off the first backup when you click finish. Check that as well, then hit finish to complete this work.

Now we can see the backup job was created and because we checked the box, it's running the backup job now.

This is all very much typical of the PVE experience in version 12 of Veeam Backup and Replication.

## Final Thoughts: The Good and the Bad

Let's get into the good and bad and final thoughts about Veeam v13 and its new native Linux appliance.

### The Good

**Veeam is doing the right thing here.** Divesting of their dependence on Windows is a smart thing to do, and I applaud them for making the effort. Version 13 really does raise the bar for Veeam as the premier enterprise backup platform of choice.

**Getting application awareness into the backup process for PVE backups is huge.** SQL needs transaction log truncation to happen or the backups created are worthless. This opens the option to move workloads that require application awareness into PVE, which I'm all for.

**The biggest deal here is what Veeam is becoming with v13.** Backups, yes, all day long. But I don't think many people realize the add-on advantage of using Veeam and the freedom it gives you.

Let me explain: VMware blows up, people leave — for good reason — and they all land somewhere, but their workloads were still in VMware. Every single alternative hypervisor on the market has an import tool or system to get your workloads OUT of VMware and into their platform, easy. But none of these alternative platforms import from any platform other than VMware. See the problem?

Where I think Veeam's true future is, isn't just backup — it's also being a universal workload migration platform for virtualization. One of the big things we learned with the Broadcom disruption of VMware was that we'd all become complacent and trusted that our infrastructure platforms would stay the same. It didn't. These days, the smart decision is to have a diverse virtualization plan. Maybe have Nutanix and Proxmox, or XCP-ng and VergeOS, but the point is don't have all your eggs in one basket.

**Veeam becomes the universal bridge between all of these different hypervisors, and ultimately gives you the freedom to move your workloads anywhere. And that's a really big deal.**

### The Bad

First off: **I don't consider the webUI production-ready yet.** At least not for anything but VMware and Hyper-V. I'm not sure if it was just easier for Veeam to knock out those two or if there was more customer pressure behind those platforms. Still, the tailwinds are not in VMware's or Hyper-V's favor.

All of the effort going into natively supporting Proxmox, Nutanix, SC HyperCore, and in the near future, VergeOS and XCP-ng — I feel it would have been better to release the webUI when you could support ALL of the current hypervisor platforms.

**Next, specifically a gripe for PVE: it's not a full-featured backup solution for PVE until Veeam can support backing up and restoring LXC containers**, which it still can't. If you're a PVE shop that has a lot of LXC containers and you're looking for backup solutions, PBS is still your best bet — hands down. Veeam, do both, please.

**Finally, a minor gripe: I was very annoyed with Veeam's presumption that I need this level of password security and mandatory MFA for the Linux appliance.** I understand the reasons behind it, and I'm not bagging on increased security — I'm advocating for customer choice. Ask me if my environment requires enhanced security configurations and let me make that choice. This isn't a problem with the Windows server version of Veeam, so don't make it one for the Linux appliance.

## Final Verdict

All of this said, **Veeam is on the right track and they're doing great things.** I've said it a thousand times now, but backup really is the long pole in the tent. With Veeam's native support for VMware, Hyper-V, Proxmox, and SC HyperCore — with even more coming online very soon — it really is becoming the backup and workload migration tool for business!

---

And that'll do it. A special thank you to a certain Discord member for the continual push to get this v13 article out. If you have feedback on anything I've said here, leave a comment below, subscribe to the channel, and join our free Discord.

**YouTube link**: [https://youtu.be/IUsywSK9Miw](https://youtu.be/IUsywSK9Miw)
