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

The PN52 contains two independent operating environments:

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

Windows and Proxmox use separate physical NVMe drives.

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
- Ruckus R730 wireless access point running Ruckus Unleashed
- ScreenBeam ECB6250 MoCA adapters
- Existing coax infrastructure
- Existing home network infrastructure

Not all physical components are currently integrated into the lab.

The distinction between equipment that physically exists and equipment that has been integrated into the lab is intentional.

---

## Windows Environment

Windows 11 25H2 is installed on one physical NVMe drive.

Windows remains the default UEFI boot operating system because the PN52 continues to serve as a normal Windows computer.

The Windows installation is independent of the Proxmox installation because the operating systems reside on separate physical NVMe drives.

---

## Proxmox Environment

Proxmox VE 9.2-1 is installed on the second physical NVMe drive.

The Proxmox host uses private management addressing.

The actual values are intentionally excluded from this public documentation:

```text
Hostname: <LAB-HOSTNAME>
Management address: <LAB-MANAGEMENT-IP>
Gateway: <LAB-GATEWAY-IP>
DNS: <LAB-DNS-SERVER>
Search domain: <LAB-DOMAIN>
```

The Proxmox management interface was successfully accessed through TCP port `8006`.

This verified that the Proxmox management service was operational.

Actual home-network addressing, DNS information, local hostnames, and other private network identifiers are intentionally omitted from this repository.

---

## Proxmox Network Architecture

The Proxmox installation created a Linux bridge named `vmbr0`.

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

`nic0` represents the physical network interface.

`vmbr0` is a software-defined Linux bridge that provides Layer 2 connectivity between the physical interface, the Proxmox host, and future virtual machines.

Only the Realtek 2.5 GbE interface is currently assigned to the Proxmox bridge.

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

At the current stage, `vmbr0` provides basic Layer 2 connectivity.

Additional network functionality will be introduced deliberately rather than assumed.

---

## Current Proxmox Network Interfaces

The currently identified Proxmox network interfaces are:

| Interface | Type | Current Role |
|---|---|---|
| `nic0` | Realtek 2.5 GbE | Physical interface used by `vmbr0` |
| `nic1` | USB Ethernet | Available physical interface |
| `nic2` | MediaTek Wi-Fi hardware | Available hardware interface |
| `wlp3s0` | Wi-Fi interface | Not used for Proxmox management |
| `vmbr0` | Linux bridge | Proxmox management network |

The additional interfaces have not been assigned a lab networking role.

---

## Proxmox Management Network

The Proxmox management network currently uses a private IPv4 subnet.

The Proxmox host has a static management address within that network.

The configured gateway is the corresponding private network gateway.

These values were verified during the Proxmox configuration process but are intentionally not published in this repository.

This demonstrates the difference between documenting a technical configuration and publishing the actual addressing used by a private home network.

---

## Temporary Direct Ethernet Test

Before the physical MikroTik connection was available, the PN52 was directly connected to another laptop using Ethernet.

The laptop was temporarily configured within the same private management network as the Proxmox host.

The laptop successfully reached the Proxmox management interface through TCP port `8006`.

The test path was:

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

This demonstrated that Proxmox management networking was functional independently of the MikroTik and Internet connectivity.

The temporary private addresses used during this test are intentionally omitted.

---

## Why the Direct Ethernet Test Was Useful

The direct Ethernet test isolated the Proxmox host from the rest of the home network.

This allowed the following question to be answered:

> Can Proxmox provide a functioning management interface over Ethernet without relying on the MikroTik or Internet connectivity?

The successful connectivity and web interface connection demonstrated that it could.

This is an example of methodical troubleshooting through isolation.

Rather than troubleshooting multiple systems simultaneously, the test reduced the number of variables.

---

## MikroTik Network

The MikroTik hEX-S is part of the existing physical networking environment and is intended to become an important component of the lab's future networking architecture.

Previously verified characteristics include:

- WAN-facing interface
- Existing LAN bridge
- DHCP service
- DNS configuration
- Bridge-based LAN architecture
- No currently implemented VLAN configuration

The MikroTik should not be treated as having a completed lab configuration.

The following remain future work:

- Lab VLANs
- Lab-specific firewall rules
- Additional lab routes
- Additional lab subnets
- Lab-specific DHCP scopes
- Network segmentation

Actual home-network IP addresses and DNS values are intentionally excluded from this repository.

---

## Physical Connection to the MikroTik

The PN52 is not currently connected to the MikroTik through the intended lab path.

The ScreenBeam ECB6250 MoCA adapters are physically available.

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

The MoCA path has not yet been implemented and verified end-to-end.

Before connecting the adapters, the existing coaxial infrastructure should be assessed to determine:

- Which coax outlets are interconnected
- The splitter arrangement
- The cable path between the intended locations
- Whether additional splitters or unused branches are present
- Whether the coax path is suitable for MoCA

---

## Ruckus Wireless Access Point

The environment includes a Ruckus R730 wireless access point running Ruckus Unleashed.

The R730 is currently part of the existing home network and provides Wi-Fi connectivity for client devices.

The access point is wired rather than operating through mesh.

Verified operational characteristics include:

- Ruckus Unleashed is operational
- Wireless service is functioning
- The AP is wired
- Mesh is disabled
- Internal Gateway is disabled
- Configuration backup has been created and confirmed
- Password recovery has been configured and confirmed

The R730 is not currently integrated into the lab's logical network architecture.

Potential future uses include:

- Wireless client testing
- SSID-to-VLAN mapping
- Segmented wireless networks
- Wireless-to-wired connectivity testing
- Network troubleshooting

These are future possibilities and should not be represented as implemented.

---

## MoCA Infrastructure

The lab includes a pair of ScreenBeam ECB6250 MoCA 2.5 adapters.

The adapters provide a potential Ethernet transport path over the existing coax infrastructure.

The adapters are physically available, but the end-to-end path has not yet been verified.

MoCA provides physical network transport.

It does not provide:

- VLANs
- Routing
- Firewalling
- Network segmentation
- IP addressing

Those functions belong to the logical networking architecture.

---

## Current Logical Network Model

The current known relationship is:

```text
                    Existing Home Network
                            │
                      Ruckus R730
                   Ruckus Unleashed
                            │
                      Home Clients


                    MikroTik hEX-S
                            │
                    Existing LAN
                            │
                    Future Lab Path
                            │
                       MoCA Transport
                            │
                     ASUS PN52
                            │
                     Realtek 2.5GbE
                            │
                           nic0
                            │
                          vmbr0
                            │
                    Proxmox Management
```

The diagram deliberately does not show VLANs, firewall zones, additional subnets, or virtual machines because those have not yet been implemented.

The Ruckus AP remains part of the existing home network.

The MoCA path is physically available but has not yet been connected and verified end-to-end.

Actual home-network addressing is intentionally omitted.

---

## Virtual Machine Architecture

No lab virtual machines have yet been deployed.

The next virtualization phase is to create the first VM.

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

These may include:

- Virtual CPU
- Virtual memory
- Virtual disk
- Virtual network interface

The exact allocation will be determined when the first VM is selected and deployed.

---

## Network Isolation

The lab does not currently have a completed network isolation architecture.

A virtual network alone does not automatically provide complete security isolation.

Future isolation may require coordination between:

- Layer 2 topology
- VLANs
- IP subnets
- Routing
- Firewall rules
- VM network configuration
- Gateway placement

These capabilities will be introduced incrementally.

---

## Current Architecture Boundaries

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

The Ruckus AP continues to serve the existing home network and has not been integrated into the lab.

### Lab Network Boundary

The MikroTik and MoCA infrastructure are physically available for future lab integration, but the intended MikroTik-to-PN52 path has not yet been established and verified.

---

## Discovery and Decision Process

The architecture was developed by answering individual infrastructure questions rather than implementing a complete design immediately.

### Physical Storage

The two identical NVMe drives were identified using system information before Proxmox installation.

The drive containing Windows was preserved.

The second physical drive was selected for Proxmox.

### Proxmox Management Networking

A direct Ethernet connection between the PN52 and another laptop was used to test Proxmox networking independently of the MikroTik.

The laptop successfully reached the Proxmox management interface.

This verified the Proxmox network path before introducing additional infrastructure.

### Primary Network Interface

The PN52's network interfaces were identified.

The Realtek 2.5 GbE interface was selected as the primary wired Proxmox interface.

Wi-Fi was not selected as the primary infrastructure interface.

### Future Physical Transport

The ScreenBeam ECB6250 adapters and existing coax infrastructure were identified as the intended physical transport between the MikroTik and PN52.

The coax path still requires verification before the MoCA connection is considered implemented.

---

## Current State

### Implemented

- Windows 11 25H2
- Proxmox VE 9.2-1
- Separate physical storage for Windows and Proxmox
- Private IPv4 management addressing
- Linux bridge `vmbr0`
- Realtek 2.5 GbE connected to `vmbr0`
- Proxmox web interface
- Direct Ethernet management test
- Windows as default UEFI boot OS

### Verified Physical Infrastructure

- ASUS ExpertCenter PN52
- MikroTik hEX-S
- Ruckus R730
- ScreenBeam ECB6250 MoCA adapters
- Existing coax infrastructure
- Existing home network infrastructure
- MikroTik bridge-based LAN architecture
- Current absence of VLAN configuration
- Ruckus Unleashed operation
- Ruckus wired operation
- MoCA adapters available for planned physical integration

### Not Yet Implemented

- First VM
- VM operating-system deployment
- VM networking beyond the existing Proxmox bridge
- Physical MikroTik-to-PN52 integration
- End-to-end MoCA connectivity
- VLANs
- Routing experiments
- Lab-specific firewall policies
- Network segmentation
- Wireless lab integration
- Network isolation testing
- Advanced connectivity and failure testing
- Infrastructure automation

---

## Architectural Principles

### Verify Before Configuring

Configuration decisions should be based on observed infrastructure rather than assumptions.

### Minimize Variables During Troubleshooting

When testing a system, unnecessary components should be removed from the test path where possible.

### Separate Physical and Logical Concerns

Physical connectivity and logical network configuration should be documented separately.

### Introduce Complexity Incrementally

New technologies should be added only after the underlying layer has been demonstrated.

### Preserve Existing Functionality

The existing Windows environment and home network should remain functional while the lab is developed.

### Document Decisions, Not Just Commands

Documentation should explain why a configuration was selected and how the result was verified.

---

## Next Step

The next implementation milestone is to verify the existing coax path before connecting the ScreenBeam ECB6250 adapters.

The intended sequence is:

```text
Verify Coax Path
       ↓
Connect MoCA
       ↓
Verify Ethernet Link
       ↓
Verify Proxmox Connectivity
       ↓
Document Actual Result
       ↓
Deploy First VM
       ↓
Develop Virtual Networking
```

No VLAN configuration, wireless segmentation, or other future network architecture should be represented as implemented until it has actually been configured and verified.

After the physical path is verified, the first VM can be deployed using the verified Proxmox environment.
```