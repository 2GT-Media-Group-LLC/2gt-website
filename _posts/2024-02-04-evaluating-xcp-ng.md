---
layout: post
title: "Evaluating XCP-ng as an Alternative to VMware ESXi"
date: 2024-02-04
author: 2GT_Rich
tags: [VMware, XCP-ng, virtualization]
banner: https://img.youtube.com/vi/cSwmtg7sJb4/maxresdefault.jpg

---

![](//youtu.be/cSwmtg7sJb4)

With Broadcom’s acquisition of VMware, many homelab enthusiasts and small to medium businesses are reconsidering their virtualization options. VMware’s uncertain future has prompted a search for reliable, open-source alternatives. One such option is XCP-ng, a powerful and cost-effective alternative to VMware ESXi.

## Background on XCP-ng

XCP-ng originated from the Xen Cloud Platform and Citrix XenServer. Citrix’s decision to open-source XenServer aimed to cut costs and compete with VMware and Microsoft. However, users were reluctant to pay for maintenance and support, leading Citrix to reintroduce limitations on the free version by 2017. This shift catalyzed the birth of XCP-ng, officially released on March 31, 2018. Since then, XCP-ng has grown, offering multiple versions and an active community.

## Comparing XCP-ng and ESXi

### Architecture

Both XCP-ng and VMware ESXi are type-1 hypervisors. VMware ESXi is lightweight, running from RAM after boot, with a closed-source kernel. XCP-ng, based on the Linux kernel, also uses disk storage for operational needs after booting.

### Performance

Performance between XCP-ng and ESXi is nearly equivalent, though historical reports indicated I/O performance issues with XCP-ng under certain workloads. However, modern updates have largely mitigated these differences. It’s essential to consider the specific workloads and configurations when evaluating performance.

### Usability

- **VMware ESXi:** Excels in usability with a built-in web-based HTML5 GUI, allowing complete management of a single host without additional steps. This intuitive interface simplifies tasks like building and managing VMs, configuring vSwitches, and handling datastores.
- **XCP-ng:** A fresh deployment lacks a local web GUI for host management. Instead, users must deploy XenOrchestra (XOA), which, while offering a rich feature set, adds complexity to the initial setup.

### Features

- **ESXi:** Advanced features, including distributed resource scheduling (DRS), high availability (HA), fault tolerance, vMotion (live migration of VMs), storage vMotion, and API control, require additional licensing.
- **XCP-ng:** Includes clustering, live migrations, VM backup functionality, and automation via API calls out-of-the-box for free. With the exception of DRS, XCP-ng’s feature set closely matches that of VMware’s licensable features, providing a compelling alternative for users on a budget.

### Scalability

- **ESXi:** Renowned for its scalability, capable of managing thousands of VMs and extensive clusters. It is used in some of the largest virtual environments globally, thanks to its robust architecture and comprehensive management tools.
- **XCP-ng:** Also supports large environments, though its adoption in complex setups is less common compared to ESXi. Xen Orchestra’s concept of pools (analogous to VMware’s clusters) allows for the management of large collections of hosts and VMs, supporting significant scaling needs.

### Support

- **VMware:** Offers extensive professional support, training, certifications, a well-maintained public knowledge base, and a large community. However, with Broadcom’s acquisition, the future of this support structure is uncertain.
- **XCP-ng:** Relies more on community support, which is active and growing. Professional support services are available through vendors like Vates, the company actively developing XCP-ng. This blend of community-driven and professional support ensures users can find help when needed.

### Cost

- **VMware:** Recent licensing changes make it less accessible to smaller users. The ESXi free version has limitations, and additional features require costly licenses.
- **XCP-ng:** Entirely free and open-source, making it more cost-effective, especially for smaller deployments. Professional support services are available for a fee, but overall, XCP-ng remains budget-friendly, providing significant value without compromising functionality.

## Real-World Comparison

### Console and Management Interface

- **ESXi:** Provides basic host information and management functions through a console, with extensive management via a web-based GUI. The ESXi console offers details about the physical host, including version information, hardware specifications, and management interface settings. Basic management functions like configuring the management interface, enabling SSH, and managing host power states are available from the console.
- **XCP-ng:** Offers comprehensive host management from the console, including VM management, storage, and network configuration, surpassing ESXi in console capabilities. XCP-ng’s console allows users to start and stop VMs, manage storage, join or leave resource pools, and view detailed hardware information. This robust console management is a significant advantage for users who prefer direct control over their hosts.

### Web Management Interface

- **ESXi:** The web UI offers a polished experience with detailed host, VM, storage, and network management. The interface is intuitive, providing a clear overview of the host’s state, usage, vSwitch and port group configurations, datastores, and system information. Detailed tabs allow for in-depth management of virtual machines, storage systems, and network configurations.
- **XCP-ng (XOA):** The interface feels cluttered but provides extensive functionality, including managing multiple hosts, pools, and VM backups. The XOA dashboard offers an overview of pools, hosts, and VMs, with detailed statistics and performance graphs available through various tabs. While the interface is less polished than ESXi’s, it includes advanced features like VM backup and restore, which are not available in ESXi’s free version.

## Detailed Evaluation

### User Experience

Xen Orchestra, as an interface, leaves much to be desired in terms of aesthetics and user experience. The first impression is that it looks like many other generic open-source web GUIs, with wasted space and sections that feel disorganized. Despite its functional capabilities, the user experience could be significantly improved to make it more enjoyable to use. The organization of information and the presentation in XOA often feels like an afterthought compared to VMware’s polished interface.

### Premium Features and Paywalls

One of the frustrations with XOA is the seemingly arbitrary paywalls for certain features. For example, visualizations and statistics on the dashboard are behind a premium license, while similar information is accessible in other sections. Host updates require a paid license, though XOA updates do not. These inconsistencies can be annoying, especially for users accustomed to fully-featured open-source solutions. However, it’s worth noting that compiling Xen Orchestra from source can unlock these features without additional cost, though this approach requires technical proficiency.

### Functionality and Configurability

Despite these drawbacks, XCP-ng offers impressive functionality. The extensive host management capabilities from the console, the ability to create pools of hosts, live migrate workloads, enable high availability, and built-in VM backup functionality are standout features. XCP-ng’s native backup capabilities eliminate the need for third-party software, providing a significant advantage over VMware, which requires additional licensing for similar features.

### Network Configuration

ESXi’s virtual network configuration is more complex and offers greater configurability, with visual representations of how VMs connect to virtual networks. XCP-ng’s approach is simpler but effective, with PIFs (physical interface configurations) analogous to port groups and vSwitches in ESXi. Private networks in XCP-ng provide segmented internal communication for VMs within a host, adding flexibility to network configurations.

## Conclusion

After extensive use, XCP-ng proves to be a robust and feature-rich alternative to VMware ESXi. It meets the general needs of host and VM management and offers many advanced features without additional cost. The user experience and interface design could benefit from improvements, but the core functionality and configurability make XCP-ng a compelling choice.

For those seeking a cost-effective, open-source virtualization solution, XCP-ng is a strong contender. It offers significant value, especially for homelab enthusiasts and small to medium businesses with budget constraints. The next step in our exploration will be evaluating Proxmox, so stay tuned for that assessment.
