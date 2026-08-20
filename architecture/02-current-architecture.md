# Current Architecture

## Purpose

This document describes the current architecture of the home lab based only on infrastructure that has been physically identified, configured, and verified.

The purpose is to establish a clear baseline before additional virtual machines, networking, routing, VLANs, and security controls are introduced.

The architecture is intentionally documented in terms of:

- What physically exists
- What has been implemented
- What has been verified
- What is planned
- What is not yet connected

This prevents future documentation from presenting planned infrastructure as though it already exists.

---

## Architectural Approach

The lab is being developed incrementally.

The current architecture is the result of several discovery and implementation steps:

```text
Physical Hardware
        ↓
Hardware Assessment
        ↓
Storage Identification
        ↓
Proxmox Installation
        ↓
Proxmox Network Configuration
        ↓
Connectivity Verification
        ↓
Current Architecture
        ↓
Future Virtual Infrastructure
```

Each stage provides information required for the next stage.

This approach allows individual components to be tested before adding additional complexity.

---

## Current Architecture Summary

The primary compute platform is the ASUS ExpertCenter PN52.

The PN52 currently contains two independent operating environments:

```text
ASUS PN52
│
├── NVMe SSD
│   └── Windows 11 25H2
│       └── Default UEFI boot OS
│
└── NVMe SSD
    └── Proxmox VE 9.2-1
        └── Lab platform
```

Windows and Proxmox are installed on separate physical NVMe drives.

This provides physical separation between the normal desktop environment and the virtualization environment.

---

## Physical Infrastructure

The current physical infrastructure includes:

- ASUS ExpertCenter PN52
- AMD Ryzen 5 5600H
- Approximately 32 GB RAM
- Two 1 TB Crucial P3 Plus NVMe SSDs
- Realtek 2.5 GbE Ethernet
- MediaTek Wi-Fi 6E
- USB Ethernet
- MikroTik hEX-S
- Ruckus wireless access point running Unleashed
- Existing home network
- MoCA infrastructure planned for future use

Not all components are currently connected to the lab.

---

## Windows Environment

Windows 11 25H2 is installed on one of the physical NVMe drives.

Verified version:

```text
Windows 11
Version 25H2
OS Build 26200.9168
```

Windows is configured as the default UEFI boot operating system.

This was an intentional design decision because the PN52 continues to serve as a normal Windows computer.

The Windows installation is independent of the Proxmox installation because the operating systems reside on separate physical NVMe drives.

---

## Proxmox Environment

Proxmox VE 9.2-1 is installed on the second physical NVMe drive.

The Proxmox host is configured with:

```text
Hostname: pve
Management address: private IPv4 address
Gateway: private network gateway
DNS: configured private-network DNS resolver
Search domain: local/private domain
```

The Proxmox management interface was successfully accessed through the configured management address on TCP port `8006`.

This verified that the Proxmox management service was operational.

The actual management address and local DNS information are intentionally omitted from this public documentation.

---

## Proxmox Network Architecture

The Proxmox installer created a Linux bridge named:

`vmbr0`

The current relationship is:

```text
Physical Realtek 2.5 GbE
          │
        nic0
          │
          ▼
       vmbr0
   Linux Bridge
          │
          ├── Proxmox Management
          │
          └── Future Virtual Machines
```

This is an important distinction.

`nic0` represents the physical network interface.

`vmbr0` is a software-defined Linux bridge.

The bridge behaves conceptually like a virtual Ethernet switch.

Virtual machines can eventually connect their virtual network interfaces to `vmbr0`.

---

## What vmbr0 Does Not Mean

The existence of `vmbr0` does not automatically mean that the lab has:

- VLANs
- Network segmentation
- Firewall isolation
- Multiple subnets
- Inter-VLAN routing
- Security boundaries

Those capabilities require additional configuration.

At the current stage, `vmbr0` provides a basic Layer 2 connection between the Proxmox host and the physical network interface.

Additional network functionality will be introduced deliberately rather than assumed.

---

## Current Proxmox Network Interfaces

The Proxmox host currently identifies the following network devices:

| Interface | Type | Current Role |
|---|---|---|
| `nic0` | Realtek 2.5 GbE | Physical interface used by `vmbr0` |
| `nic1` | USB Ethernet | Available physical interface |
| `nic2` | MediaTek Wi-Fi hardware | Available hardware interface |
| `wlp3s0` | Wi-Fi interface | Not used for Proxmox management |
| `vmbr0` | Linux bridge | Proxmox management network |

Only `nic0` is currently connected to `vmbr0`.

The additional interfaces have not been assigned a lab networking role.

---

## Proxmox Management Network

The Proxmox management network currently uses a private IPv4 subnet.

The Proxmox host has a static management address within that subnet.

The configured gateway is the corresponding private network gateway.

These addresses were verified during the Proxmox configuration process but are intentionally not published in this repository.

This is an important distinction between documenting a technical configuration and publishing the actual addressing used by a private home network.

---

## Temporary Direct Ethernet Test

Before the physical MikroTik connection was available, the PN52 was directly connected to another laptop using Ethernet.

The laptop's Ethernet adapter was temporarily configured with an address in the same private management subnet as the Proxmox host.

The laptop successfully reached the Proxmox management address.

The Proxmox web interface was then successfully accessed through TCP port `8006`.

This test verified several important components independently of the MikroTik:

```text
Laptop
  │
Ethernet
  │
PN52 Realtek NIC
  │
nic0
  │
vmbr0
  │
Proxmox Management
```

This demonstrated that the Proxmox management networking was functional.

The temporary private addresses used during this test are intentionally omitted.

---

## Why the Direct Ethernet Test Was Useful

The direct Ethernet test isolated the Proxmox host from the rest of the home network.

This allowed the following question to be answered:

> Can the Proxmox host communicate over Ethernet and provide its management interface without relying on the MikroTik or Internet connectivity?

The successful connectivity and web interface connection demonstrated that it could.

This is an example of methodical troubleshooting through isolation.

Rather than troubleshooting multiple systems simultaneously, the test reduced the number of variables.

---

## MikroTik Network

The MikroTik hEX-S is intended to become the primary router for the lab environment.

Previously verified configuration includes:

- A WAN-facing interface
- A private bridge interface
- DHCP service for the private network
- DNS configuration

The MikroTik configuration will not be assumed beyond the values that have been explicitly verified.

In particular, the following have not yet been treated as established lab configuration:

- VLANs
- Firewall rules
- Additional routes
- Additional subnets
- Detailed interface membership
- Final DHCP scope configuration

These will be discovered and documented when required.

Actual home-network IP addressing and DNS values are intentionally excluded from this repository.

---

## Physical Connection to the MikroTik

The PN52 is not currently physically connected to the MikroTik.

The intended physical path is:

```text
MikroTik hEX-S
      │
   Ethernet
      │
 MoCA Adapter
      │
    Coax
      │
 MoCA Adapter
      │
   Ethernet
      │
PN52 Realtek 2.5GbE
      │
     nic0
      │
    vmbr0
      │
   Proxmox
```

The required MoCA adapters are not currently available.

Therefore, this path is a planned architecture rather than an implemented connection.

---

## Existing Home Network

The home network exists independently of the lab environment.

The Ruckus wireless access point runs Ruckus Unleashed and currently provides Wi-Fi connectivity for devices on the home network.

The access point is therefore part of the physical networking environment but is not currently part of the implemented lab topology.

This distinction prevents the architecture from incorrectly implying that wireless VLANs, lab SSIDs, or wireless segmentation have already been implemented.

---

## Ruckus Access Point

The Ruckus AP may eventually become useful for lab experiments involving:

- SSIDs
- VLANs
- Wireless client isolation
- Wireless-to-wired connectivity
- Network segmentation
- Authentication
- Troubleshooting Layer 2 and Layer 3 connectivity

At the current stage, none of these lab functions should be considered implemented.

The AP currently performs its existing home-network role.

---

## Current Logical Network Model

The current known network relationship can be represented as:

```text
                    Existing Home Network
                            │
                            │
                      Ruckus AP
                   Unleashed Wi-Fi
                            │
                      Home Clients


                    MikroTik hEX-S
                            │
                    Private Network
                            │
                     [Not Connected]
                            │
                       Future MoCA
                            │
                            │
                     ASUS PN52
                            │
                     Realtek 2.5GbE
                            │
                           nic0
                            │
                          vmbr0
                            │
                    Private Management
                            │
                         Proxmox
```

The diagram represents the current understanding of the environment.

It deliberately does not show VLANs, firewall zones, additional subnets, or virtual machines because those have not yet been implemented.

Actual home-network addressing is intentionally omitted.

---

## Virtual Machine Architecture

No production or lab VM workloads have yet been deployed.

The next major virtualization phase is to create the first VM.

The intended conceptual architecture is:

```text
Physical PN52
      │
   Proxmox
      │
      ├── VM 1
      │
      ├── VM 2
      │
      └── Future VMs
```

Each VM will receive virtual resources from Proxmox.

These resources may include:

- Virtual CPU
- Virtual memory
- Virtual disk
- Virtual network interface

The exact allocation will be determined when the first VM is selected and deployed.

---

## Network Isolation

The lab does not currently have a completed network isolation architecture.

A separate virtual network does not automatically provide complete security isolation.

True isolation may require coordination between:

- Layer 2 topology
- VLANs
- IP subnets
- Routing
- Firewall rules
- VM network configuration
- Gateway placement

These concepts will be introduced incrementally.

---

## Current Architecture Boundaries

The following boundaries currently exist.

### Physical Storage Boundary

Windows and Proxmox use separate physical NVMe drives.

### Operating System Boundary

Windows and Proxmox are separate operating environments.

### Virtualization Boundary

Proxmox provides the platform on which future virtual machines will run.

### Network Interface Boundary

The Realtek Ethernet interface is currently assigned to the Proxmox bridge.

Other network interfaces are not currently part of the Proxmox network configuration.

### Home Network Boundary

The Ruckus AP continues to serve the existing home network and has not yet been integrated into the lab.

### Lab Router Boundary

The MikroTik is intended to provide the future physical network gateway for the lab, but the physical connection has not yet been established.

---

## Discovery and Decision Process

The current architecture was developed by answering individual infrastructure questions rather than implementing a complete design immediately.

Examples include:

### Question

Which physical disk can safely be used for Proxmox?

### Investigation

The two identical NVMe drives were identified using model, size, disk number, partition layout, and unique hardware identifiers.

### Decision

The drive containing the existing Windows installation was preserved.

The other drive was authorized for Proxmox.

---

### Question

Can Proxmox provide a functioning management network without the MikroTik?

### Investigation

The PN52 was directly connected to another laptop using Ethernet.

### Result

The laptop successfully reached the Proxmox management address and accessed the Proxmox web interface.

### Decision

The Proxmox management network was verified independently before physical integration with the MikroTik.

---

### Question

Which physical interface should provide the primary Proxmox connection?

### Investigation

The PN52's physical interfaces were identified.

### Decision

The Realtek 2.5 GbE interface was selected as the intended primary wired Proxmox interface.

Wi-Fi was not selected as the primary infrastructure interface.

---

## Current State

### Implemented

- Windows 11 25H2
- Proxmox VE 9.2-1
- Separate physical storage for Windows and Proxmox
- Proxmox hostname `pve`
- Private IPv4 management addressing
- Linux bridge `vmbr0`
- Realtek 2.5 GbE connected to `vmbr0`
- Proxmox web interface
- Direct Ethernet management test
- Windows as default UEFI boot OS

### Verified but Not Currently Connected

- MikroTik hEX-S
- Ruckus wireless access point as part of the home network
- Future MoCA path

### Not Yet Implemented

- First VM
- VM operating-system deployment
- VM networking
- Physical MikroTik integration
- Internet connectivity through the MikroTik
- VLANs
- Routing experiments
- Firewall policies
- Network segmentation
- Wireless lab integration
- Advanced testing
- Troubleshooting projects

---

## Architectural Principles

The lab will follow several principles as it develops.

### Verify Before Configuring

Configuration decisions should be based on observed infrastructure rather than assumptions.

### Minimize Variables During Troubleshooting

When testing a system, unnecessary components should be removed from the test path where possible.

### Separate Physical and Logical Concerns

Physical connectivity and logical network configuration should be documented separately.

### Introduce Complexity Incrementally

New technologies should be added only after the underlying layer has been demonstrated.

### Preserve Existing Functionality

The home Windows environment and existing home network should remain functional while the lab is developed.

### Document Decisions, Not Just Commands

Documentation should explain why a configuration was selected, what alternatives were considered when relevant, and how the result was verified.

---

## Next Step

The next major lab milestone is the deployment of the first virtual machine.

Before creating the VM, the operating-system ISO must be selected and verified.

The VM should not be created until the operating system, intended purpose, and required resources are understood.

The next virtualization documentation file will be:

`virtualization/01-proxmox-installation.md`
