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
- Ruckus wireless access point running Unleashed
- Existing home network
- MoCA infrastructure intended for future physical lab connectivity

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
- Secure Boot enabled
- Hardware virtualization enabled

The PN52 serves two purposes:

1. Normal Windows computer
2. Proxmox virtualization host for the lab

These roles are separated using two physical NVMe drives.

---

## Physical Storage

The PN52 contains two identical Crucial P3 Plus 1 TB NVMe SSDs.

Model:

`CT1000P3PSSD8`

The two drives are physically separate devices even though they have the same model and capacity.

This distinction became important during the Proxmox installation because one drive contained Windows and the other was authorized to be repurposed for the lab.

---

## Operating Systems

The PN52 contains two operating-system environments.

### Windows 11

Windows 11 is installed on one physical NVMe SSD.

Windows is the default UEFI boot operating system.

It remains the primary operating environment for normal daily computer use.

### Proxmox VE

Proxmox VE 9.2-1 is installed on the second physical NVMe SSD.

The Proxmox installation is dedicated to the home lab.

The two operating systems therefore have separate physical storage rather than sharing a single disk through partitioning.

---

## Physical Network Interfaces

The PN52 contains multiple network interfaces.

### Realtek 2.5 GbE

The Realtek Gaming 2.5GbE Family Controller is the intended primary physical Ethernet interface for Proxmox.

This interface provides the preferred physical connection for future integration with the lab network.

### USB Ethernet

The system also has a USB Ethernet interface.

It is not currently the intended primary Proxmox network interface.

### MediaTek Wi-Fi

The PN52 contains a MediaTek Wi-Fi 6E adapter.

Wi-Fi is not currently being used as the primary Proxmox network connection.

A wired Ethernet connection is preferred for the virtualization host because it provides a more predictable and appropriate foundation for infrastructure networking.

---

## MikroTik Router

The lab includes a MikroTik hEX-S router.

The MikroTik is intended to provide the networking foundation for future lab work involving:

- Routing
- DHCP
- VLANs
- Firewalling
- Network segmentation
- Connectivity testing
- Network troubleshooting

The MikroTik is physically separate from the PN52.

The PN52 is not currently connected to the MikroTik because the required MoCA adapters are not yet available.

The MikroTik configuration will therefore be documented separately and only as configuration is verified.

---

## Ruckus Wireless Access Point

The home network also includes a Ruckus wireless access point running Ruckus Unleashed.

The access point currently provides Wi-Fi connectivity for devices on the existing home network.

It is part of the physical network environment but is not currently integrated into the lab networking architecture.

This distinction is important.

The access point exists physically and provides an active networking service, but it should not be represented as part of the lab's implemented topology until it is actually integrated.

Potential future uses may include:

- Wireless client testing
- VLAN testing
- SSID-to-VLAN mapping
- Segmented wireless networks
- Testing interaction between wireless and wired infrastructure

These capabilities are future possibilities rather than current implementation.

---

## MoCA Infrastructure

MoCA is intended to provide physical Ethernet transport between locations where a direct Ethernet cable is not currently available.

The planned physical path is:

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

The MoCA adapters are not currently available.

Therefore, this connection has not yet been implemented or tested.

MoCA is considered physical network transport.

It should not be confused with logical networking technologies such as:

- VLANs
- Routing
- Linux bridges
- Firewall rules

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

A virtual machine does not have its own physical CPU or RAM.

Instead, Proxmox allocates portions of the host's physical resources to the VM.

---

## Current Physical Topology

The currently understood physical topology is:

```text
                    Existing Home Network
                            │
                            │
                    Ruckus AP
                  Ruckus Unleashed
                            │
                     Wi-Fi Clients


                 MikroTik hEX-S
                       │
                 Future MoCA Path
                       │
                       │
                  [Not Connected]
                       │
                       │
                  ASUS PN52
                       │
              Realtek 2.5 GbE
                       │
                    Proxmox
```

This diagram intentionally shows the future MoCA path as not yet connected.

The physical topology will be updated as components are integrated and verified.

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

### Wired Networking for Proxmox

The Realtek 2.5 GbE interface is the preferred physical network interface for Proxmox.

Wi-Fi is not being used as the primary infrastructure interface.

### Existing Home Network vs. Lab Network

The Ruckus wireless infrastructure currently serves the existing home network.

It is not considered part of the implemented lab topology until it is intentionally integrated.

### Physical Transport vs. Logical Networking

MoCA provides physical network transport.

Proxmox bridges, VLANs, IP addressing, routing, and firewall rules operate at different layers of the networking architecture.

The lab will document these separately as they are implemented.

---

## Current Status

### Completed

- Physical host identified
- CPU identified
- Memory identified
- Storage devices identified
- Network interfaces identified
- MikroTik identified
- Ruckus wireless infrastructure identified
- Physical separation between Windows and Proxmox established
- Proxmox installed on the authorized NVMe drive
- Windows preserved on its original NVMe drive
- Intended physical network path identified

### Not Yet Implemented

- PN52 to MikroTik physical connection
- MoCA integration
- Internet connectivity from Proxmox through the MikroTik
- VLANs
- Routing lab
- Firewall policies
- Network segmentation
- Wireless VLAN integration
- VM workloads

These items will be documented as they are actually implemented and verified.

---

## Lessons Learned

### Infrastructure Should Be Documented From Evidence

Hardware, interfaces, storage devices, and network components should be identified from actual system information rather than assumptions.

### Physical and Logical Networks Are Different

A physical connection such as Ethernet, MoCA, or coax provides transport.

Logical networking determines how traffic is organized, addressed, routed, and filtered.

### Not Every Available Device Needs to Be Integrated Immediately

The Ruckus AP and additional physical machines may eventually become valuable parts of the lab.

However, adding equipment simply because it is available can increase complexity before the underlying concepts are understood.

The lab will therefore expand incrementally.

### Architecture Should Evolve From Verified Requirements

The final network architecture should emerge from actual lab requirements and experiments rather than being created entirely in advance.

This allows each implementation step to have a clear purpose and provides opportunities to test and troubleshoot individual components.

---

## Next Step

The next documentation milestone is to document the current logical architecture and the separation between:

- Windows
- Proxmox
- Physical networking
- Virtual networking
- Existing home networking
- Future lab networking

This will be documented in:

`architecture/02-current-architecture.md`
