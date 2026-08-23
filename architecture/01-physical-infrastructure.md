# Physical Infrastructure

## Purpose

This document establishes the physical infrastructure that forms the foundation of the home lab.

The goal is to distinguish physical infrastructure from the virtual infrastructure that will be built on top of it.

The lab is being developed incrementally. Hardware and systems are documented as they are discovered and verified rather than assuming a final architecture in advance.

---

## Physical Infrastructure Overview

The primary lab environment is centered around an ASUS ExpertCenter PN52.

The current physical infrastructure includes:

- ASUS ExpertCenter PN52
- Two internal NVMe SSDs
- Realtek 2.5 GbE Ethernet
- MediaTek Wi-Fi 6E
- USB Ethernet connectivity
- MikroTik hEX-S router
- Ruckus R730 wireless access point running Ruckus Unleashed
- ScreenBeam ECB6250 MoCA adapters
- Existing home network infrastructure
- Existing coax infrastructure

Not all physical components are currently integrated into the lab environment.

The distinction between equipment that physically exists and equipment that has been integrated into the lab is intentional.

---

## Primary Compute Host

### ASUS ExpertCenter PN52

The ASUS PN52 is the primary physical compute host for the lab.

Verified hardware includes:

- AMD Ryzen 5 5600H with Radeon Graphics
- 6 physical CPU cores
- 12 logical processors
- Approximately 32 GB RAM
- Two 1 TB Crucial P3 Plus NVMe SSDs
- UEFI firmware
- Hardware virtualization enabled

The PN52 serves two purposes:

1. Normal Windows computer
2. Proxmox virtualization host for the lab

These roles are separated using two physical NVMe drives.

---

## Physical Storage

The PN52 contains two identical Crucial P3 Plus 1 TB NVMe SSDs.

Model:

```text
CT1000P3PSSD8
```

The two drives are physically separate devices even though they have the same model and capacity.

This distinction became important during the Proxmox installation because one drive contained Windows and the other was authorized to be repurposed for the lab.

### Storage Roles

The Windows installation remains on its original physical NVMe drive.

The second physical NVMe drive is dedicated to Proxmox VE and the lab environment.

The physical disk assignment was verified before the Proxmox installation to avoid modifying the Windows installation.

---

## Operating Systems

The PN52 contains two operating-system environments.

### Windows 11

Windows 11 is installed on one physical NVMe SSD.

Windows remains the default UEFI boot operating system and the primary operating environment for normal daily computer use.

### Proxmox VE

Proxmox VE 9.2-1 is installed on the second physical NVMe SSD.

The Proxmox installation is dedicated to the home lab.

The two operating systems therefore use separate physical storage rather than sharing a single physical disk through partitioning.

---

## Virtualization Hardware Support

The PN52 hardware was verified to support virtualization.

Verified capabilities include:

- VM Monitor Mode Extensions
- Hardware virtualization enabled in firmware
- Second Level Address Translation (SLAT)
- Data Execution Prevention (DEP)

These capabilities provide the hardware foundation required for the Proxmox virtualization environment.

---

## Physical Network Interfaces

The PN52 contains multiple network interfaces.

### Realtek 2.5 GbE

The Realtek Gaming 2.5GbE Family Controller is the intended primary physical Ethernet interface for Proxmox.

This interface provides the preferred physical connection for infrastructure networking because wired Ethernet provides a more predictable and stable foundation for a virtualization host than Wi-Fi.

The PN52's 2.5 GbE capability may be limited by the network equipment or physical link used to connect it.

### USB Ethernet

The system also has a USB Ethernet interface.

It is not currently the intended primary Proxmox network interface.

### MediaTek Wi-Fi

The PN52 contains a MediaTek Wi-Fi 6E adapter.

Wi-Fi is not currently being used as the primary Proxmox network connection.

---

## MikroTik Router

The lab includes a MikroTik hEX-S (RB760iGS).

The verified RouterOS version is:

```text
RouterOS 6.49.17
```

The MikroTik provides the current routing and DHCP foundation for the existing network and is intended to become an important component of the lab's future networking environment.

Potential lab functions include:

- Routing
- DHCP
- VLANs
- Firewalling
- Network segmentation
- Connectivity testing
- Network troubleshooting

### Current Physical Interface Role

The current MikroTik interface arrangement has been verified.

The physical interface roles are:

```text
WAN
 │
 └── MikroTik ether1

LAN bridge
 │
 ├── ether2
 ├── ether3
 ├── ether4
 ├── ether5
 └── sfp1
```

The existing LAN uses a bridge-based design.

The current configuration does not have an implemented VLAN configuration.

The MikroTik's current configuration is treated as an existing home-network baseline. Lab-specific changes will be introduced deliberately and documented only after implementation and verification.

### Resource Baseline

The hEX-S currently has:

- 256 MB RAM
- 4 CPU cores
- 880 MHz CPU frequency
- Approximately 213 MB free memory at the time of discovery
- 0% CPU utilization at the time of the captured baseline
- No reported bad blocks

This establishes that the router was lightly loaded at the time of discovery.

---

## Ruckus Wireless Access Point

The environment includes a Ruckus R730 wireless access point running Ruckus Unleashed.

The R730 is currently part of the existing home network and provides Wi-Fi connectivity for client devices.

The access point is wired rather than operating through mesh.

The following operational state has been verified:

- Ruckus Unleashed is operational
- Existing wireless service is functioning
- Internal Gateway is disabled
- Mesh is disabled
- Configuration backup has been created and confirmed
- Password recovery has been configured and confirmed
- The access point is using a high-power PoE configuration appropriate for its current power requirements

The R730 is not yet integrated into the lab's logical network architecture.

This distinction is important.

The access point exists physically and provides an active networking service, but it should not be represented as part of the implemented lab topology until that integration is deliberately designed and implemented.

Potential future uses may include:

- Wireless client testing
- VLAN testing
- SSID-to-VLAN mapping
- Segmented wireless networks
- Testing interaction between wireless and wired infrastructure

These capabilities are future possibilities rather than current implementation.

---

## MoCA Infrastructure

The lab includes a pair of ScreenBeam ECB6250 MoCA 2.5 adapters.

The adapters are intended to provide Ethernet transport over the existing coax infrastructure between locations where a direct Ethernet cable is not currently practical.

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
   Proxmox
```

The adapters are physically available.

The MoCA path has not yet been fully implemented and verified.

Before connecting the adapters, the coaxial infrastructure should be assessed to verify:

- Which coax outlets are interconnected
- Splitter arrangement
- Cable path between the intended locations
- Additional splitters or unused branches
- Compatibility of the existing coax path with the MoCA adapters

### MoCA vs. Logical Networking

MoCA provides physical network transport.

It should not be confused with logical networking technologies such as:

- VLANs
- Routing
- Linux bridges
- Firewall rules
- IP addressing

MoCA can provide the physical Ethernet transport while the MikroTik and Proxmox environment determine how that traffic is logically organized.

---

## Physical vs. Virtual Infrastructure

An important design concept in this lab is the distinction between physical and virtual infrastructure.

### Physical Infrastructure

Physical infrastructure consists of actual hardware and physical communication paths.

Examples include:

- CPU
- RAM
- NVMe SSDs
- Ethernet NICs
- Ethernet cables
- Router
- Wireless access point
- MoCA adapters
- Coaxial cable

### Virtual Infrastructure

Virtual infrastructure is created by software using the physical resources.

Examples include:

- Proxmox
- Virtual machines
- Virtual NICs
- Linux bridges
- VLAN configuration
- Virtual disks
- Virtual CPU allocation
- Virtual memory allocation

The relationship can be represented as:

```text
Physical Infrastructure
        │
        ▼
     Proxmox
        │
        ├── Virtual CPU
        ├── Virtual RAM
        ├── Virtual Storage
        └── Virtual Networking
                 │
                 ▼
             Virtual Machines
```

A virtual machine does not have its own physical CPU, RAM, or Ethernet adapter.

Instead, Proxmox provides virtualized resources backed by the physical resources of the PN52.

---

## Virtual Networking and Physical Transport

The lab will use physical and virtual networking together.

For example:

```text
PN52
 │
 ├── Physical Ethernet NIC
 │
 ▼
Physical Network
 │
 ▼
Proxmox
 │
 └── Virtual Networking
      │
      ├── Virtual NIC
      ├── Linux Bridge
      └── Future VLAN Connectivity
            │
            ▼
        Virtual Machines
```

A single physical Ethernet connection can support multiple logical networks when VLAN tagging and appropriate virtual networking are implemented.

The logical networks remain separate even though they share the same physical transport.

This capability is planned for the lab but has not yet been implemented.

---

## Current Physical Topology

The currently verified physical environment is:

```text
                    Existing Home Network
                             │
                             │
                       Ruckus R730
                      Ruckus Unleashed
                             │
                        Wi-Fi Clients


                      MikroTik hEX-S
                             │
                  Existing LAN infrastructure
                             │
                    Future lab integration
                             │
                      MoCA transport
                             │
                      ScreenBeam ECB6250
                             │
                           Coax
                             │
                      ScreenBeam ECB6250
                             │
                         Ethernet
                             │
                         ASUS PN52
                             │
                     Realtek 2.5 GbE
                             │
                          Proxmox
```

The MoCA path is physically available but has not yet been connected and verified end-to-end.

The Ruckus access point remains part of the existing home network and is not yet part of the implemented lab VLAN architecture.

---

## Discovery and Implementation Approach

The physical infrastructure was not documented by assuming the desired final state.

Instead, the lab is being built using a discovery-first approach:

```text
Observe
   ↓
Identify
   ↓
Verify
   ↓
Document
   ↓
Plan
   ↓
Implement
   ↓
Test
   ↓
Update Documentation
```

This approach reduces the risk of building configurations around incorrect assumptions.

It also creates a record of why infrastructure decisions were made.

---

## Design Considerations

Several design decisions have already been established.

### Separate Physical Storage

Windows and Proxmox use separate physical NVMe drives.

This provides a clear boundary between the normal desktop environment and the lab environment.

The Windows drive is preserved and the second NVMe drive is dedicated to the lab.

### Wired Networking for Proxmox

The Realtek 2.5 GbE interface is the preferred physical network interface for Proxmox.

Wi-Fi is not being used as the primary infrastructure interface.

### Existing Home Network vs. Lab Network

The MikroTik and Ruckus currently provide infrastructure for the existing home network.

They may become components of the lab's networking architecture, but their existing functionality should be preserved unless an intentional lab change is being performed.

### Physical Transport vs. Logical Networking

Ethernet and MoCA provide physical network transport.

Proxmox bridges, VLANs, IP addressing, routing, and firewall rules operate at different layers of the networking architecture.

The lab will document these separately as they are implemented.

### Incremental Network Integration

The PN52, MikroTik, Ruckus, and MoCA infrastructure will not all be modified simultaneously.

Changes will be introduced incrementally so that each component can be tested independently and unexpected behavior can be isolated.

---

## Current Status

### Completed

- Physical host identified
- CPU identified
- Memory identified
- Storage devices identified
- Windows installation identified and preserved
- Proxmox installation disk identified
- Proxmox VE 9.2-1 installed
- Proxmox installation media verified
- Hardware virtualization capabilities verified
- PN52 network interfaces identified
- MikroTik hEX-S identified
- MikroTik RouterOS version verified
- MikroTik physical interface arrangement verified
- Current MikroTik bridge-based LAN architecture verified
- Current absence of VLAN configuration verified
- Ruckus R730 identified
- Ruckus Unleashed operation verified
- Ruckus wired operation verified
- Ruckus configuration backup verified
- MoCA adapters identified and confirmed available
- Intended MoCA physical path identified

### Not Yet Implemented

- PN52-to-MikroTik physical lab connection
- MoCA end-to-end connectivity testing
- Proxmox connectivity through the intended physical lab path
- First lab virtual machine
- VLAN implementation
- Routing lab
- Firewall policies
- Network segmentation
- Wireless VLAN integration
- Network isolation testing
- Connectivity and failure testing
- Additional physical systems
- Infrastructure automation

These items will be documented as they are actually implemented and verified.

---

## Lessons Learned

### Infrastructure Should Be Documented From Evidence

Hardware, interfaces, storage devices, and network components should be identified from actual system information rather than assumptions.

### Physical and Logical Networks Are Different

A physical connection such as Ethernet, MoCA, or coax provides transport.

Logical networking determines how traffic is organized, addressed, routed, and filtered.

### Virtualization and VLANs Solve Different Problems

Virtualization allows multiple virtual machines to share the physical resources of a host.

VLANs provide logical Layer-2 segmentation across shared physical network infrastructure.

These technologies can work together, but they should not be treated as the same concept.

### Not Every Available Device Needs to Be Integrated Immediately

The Ruckus AP, MikroTik, MoCA infrastructure, and additional physical systems may eventually become valuable parts of the lab.

However, adding equipment simply because it is available can increase complexity before the underlying concepts are understood.

The lab will therefore expand incrementally.

### Architecture Should Evolve From Verified Requirements

The final network architecture should emerge from actual lab requirements and experiments rather than being created entirely in advance.

This allows each implementation step to have a clear purpose and provides opportunities to test and troubleshoot individual components.

---

## Next Step

The next implementation milestone is to establish the physical connection between the PN52 and the existing network using the available MoCA infrastructure.

Before connecting the MoCA adapters, the coaxial path should be verified.

After the physical path is established, connectivity should be tested before introducing VLANs or other logical network changes.

The next documentation milestone is therefore:

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

No VLAN configuration or wireless segmentation should be represented as implemented until it has actually been configured and verified.