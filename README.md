# Home Lab

A hands-on infrastructure lab for developing practical experience with virtualization, networking, routing, security, troubleshooting, and infrastructure operations.

The environment is being built incrementally. Documentation focuses not only on the resulting configuration, but also on the **discovery, reasoning, implementation, verification, troubleshooting, and lessons learned** behind each stage.

The objective is to build and operate infrastructure in a controlled environment while developing repeatable technical practices that can scale from a small lab to larger infrastructure environments.

## Project Goals

The lab is being used to develop practical experience with:

- Physical hardware assessment
- Virtualization and Proxmox VE
- Virtual machines
- Virtual networking
- Linux networking concepts
- MikroTik RouterOS
- Routing and DHCP
- VLANs and network segmentation
- Firewalling and network security
- Connectivity testing
- Systematic troubleshooting
- Installation-media verification
- Infrastructure documentation
- Git and GitHub
- Safe infrastructure changes
- Infrastructure automation and infrastructure-as-code concepts

## Engineering Approach

Significant infrastructure changes follow a repeatable process:

1. Define the objective
2. Establish the known starting state
3. Gather evidence
4. Analyze the findings
5. Identify risks and constraints
6. Make an informed decision
7. Implement the change
8. Verify the result
9. Document unexpected behavior and troubleshooting
10. Record lessons learned
11. Identify the next step

The documentation intentionally distinguishes between:

**Verified**

A fact that has been observed, measured, or tested.

**Decision**

A deliberate choice made based on available evidence, requirements, or constraints.

**Planned**

A future objective that has not yet been implemented or verified.

This distinction helps ensure that the repository represents the actual state of the infrastructure rather than an assumed target configuration.

## Current Infrastructure

The primary lab system is an ASUS ExpertCenter PN52 containing:

- AMD Ryzen 5 5600H
- 6 physical CPU cores / 12 logical processors
- Approximately 32 GB RAM
- Two 1 TB Crucial P3 Plus NVMe SSDs
- Realtek 2.5 GbE networking

The two physical NVMe drives have separate purposes.

One drive contains Windows 11 and is preserved for normal computer use.

The other drive is dedicated to Proxmox VE and the lab environment.

### Operating Systems

**Windows 11**

Windows 11 remains the default UEFI boot operating system.

**Proxmox VE**

Proxmox VE 9.2-1 is installed on the dedicated lab NVMe drive.

The current architecture is:

```text
ASUS PN52
│
├── NVMe SSD
│   └── Windows 11
│       └── Default UEFI boot OS
│
└── NVMe SSD
    └── Proxmox VE
        └── Lab Environment
```

## Proxmox Networking

The initial Proxmox management network uses a Linux bridge.

```text
Physical Ethernet NIC
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

The Proxmox host uses a private IPv4 management address within the lab management network.

The Linux bridge provides the connection between the physical network interface and the virtual networking used by Proxmox and future virtual machines.

This initial configuration does not imply VLANs, network segmentation, or firewall isolation. Those capabilities will be introduced and documented only when they are actually implemented.

## Physical Network

The environment includes existing home-network infrastructure that provides connectivity for normal household devices, as well as infrastructure intended for future lab integration.

### Existing Home Network Infrastructure

**MikroTik hEX-S**

The MikroTik provides routing and DHCP services within the existing network. It is also intended to become an important component of the lab's future networking environment.

**Ruckus Access Point**

A Ruckus access point running Ruckus Unleashed is currently part of the existing home network and provides Wi-Fi connectivity for client devices.

The access point is **not currently integrated into the lab environment**.

It is documented here because it is part of the physical network environment in which the lab operates, but its configuration and role in the lab will not be assumed until that integration is deliberately designed and implemented.

### Planned Lab Integration

The intended initial physical path for the Proxmox host is:

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

The required MoCA adapters are not currently available, so this physical integration has not yet been implemented.

The Ruckus access point may become relevant to future networking and segmentation work, particularly when VLANs, wireless networks, and network isolation are introduced. No such integration is currently implemented or assumed.

## Major Milestones

### Completed

- Physical hardware assessment
- Identification of the two NVMe drives
- Verification of the Windows installation
- UEFI and Secure Boot verification
- Proxmox installation-media verification
- Proxmox VE 9.2-1 installation
- Verification of the correct Proxmox installation disk
- Proxmox management networking
- Linux bridge verification
- Direct Ethernet connectivity testing
- Proxmox web interface verification
- Proxmox system and network checks
- Windows/Proxmox dual-boot configuration
- Windows restored as the default UEFI boot OS

### Planned

- First virtual machine
- VM operating-system installation
- Virtual disk and resource allocation
- Virtual networking
- Physical MikroTik integration
- Routing
- VLANs and network segmentation
- Ruckus wireless integration
- Firewalling
- Network isolation
- Connectivity and failure testing
- Troubleshooting exercises
- Additional physical systems
- Infrastructure automation
- More advanced infrastructure projects

## Documentation Structure

Technical documentation will evolve with the infrastructure.

Planned areas include:

```text
architecture/
networking/
virtualization/
projects/
troubleshooting/
notes/
```

These directories will be created as they become useful rather than being populated in advance.

Individual technical documentation will focus on the progression from:

```text
Discovery
    ↓
Evidence
    ↓
Analysis
    ↓
Decision
    ↓
Implementation
    ↓
Verification
    ↓
Troubleshooting
    ↓
Lessons Learned
```

## Current Status

**Active — under development**

The physical host and Proxmox foundation are operational.

The existing home network includes the MikroTik hEX-S and a Ruckus access point running Ruckus Unleashed. These components provide existing home-network connectivity but have not yet been fully integrated into the lab environment.

The next major infrastructure phase is deployment of the first virtual machine, followed by development of the virtual networking environment and eventual integration with the physical MikroTik network.
