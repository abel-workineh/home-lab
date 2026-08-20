# Proxmox VE Installation

## Purpose

This document records the installation of Proxmox VE on the ASUS ExpertCenter PN52.

The purpose is not simply to record that Proxmox was installed.

It documents the discovery, verification, decision-making, implementation, and validation performed during the installation.

This is important because the PN52 contains two identical NVMe SSDs.

One drive contains Windows and must be preserved.

The other drive was intentionally repurposed for Proxmox.

The installation therefore required careful identification of the correct physical disk before any destructive operation was performed.

---

## Installation Objective

The objective was to establish Proxmox VE as the virtualization platform for the home lab while preserving the existing Windows installation.

The intended architecture was:

    ASUS PN52
    │
    ├── NVMe Disk 1
    │   └── Windows 11
    │       └── Preserve
    │
    └── NVMe Disk 0
        └── Proxmox VE
            └── Home Lab

The two operating environments were intentionally placed on separate physical drives.

---

## Proxmox Version

Installed version:

    Proxmox VE 9.2-1

The installation media was downloaded from the official Proxmox source.

The ISO was:

    proxmox-ve_9.2-1.iso

---

## Installation Media Verification

Before creating the installation USB, the downloaded ISO was verified using its SHA-256 checksum.

The verification established that the downloaded installation image matched the expected software artifact.

The exact checksum value is not necessary for the public documentation and has therefore been omitted.

The verification process was:

    Downloaded ISO
          │
          ▼
    SHA-256 Calculation
          │
          ▼
    Calculated Hash
          │
          ├── Matches official hash
          │
          ▼
    Installation media verified

If the calculated hash does not match the expected hash, the file should not be treated as verified installation media.

This is a useful operational practice beyond this home lab because installation media is part of the trusted software supply chain.

---

## Why Checksum Verification Matters

A checksum provides a way to verify that a downloaded file matches a known reference value.

The important concept is not the specific hash value, but the verification process.

    Official Software Artifact
              │
              ▼
        Official SHA-256
              │
              │
        Downloaded ISO
              │
              ▼
      Calculated SHA-256
              │
              ▼
            Compare
              │
         ┌────┴────┐
         │         │
       Match    Mismatch
         │         │
         ▼         ▼
      Verified  Investigate

Checksum verification provides confidence that the installation image has not been corrupted or unexpectedly modified during download.

---

## Installation USB

A PNY 32 GB USB flash drive was used as the Proxmox installation media.

The USB device was independently identified before writing the Proxmox image.

This was important because writing an installation image to the wrong disk can destroy data.

Device-specific identifiers such as the USB serial number are intentionally excluded from the public documentation.

---

## Rufus Configuration

Rufus was used to create the bootable Proxmox USB.

Rufus identified the Proxmox image as an isohybrid image and required DD Image mode.

The DD Image mode prompt was accepted.

The selected configuration included:

    Partition Scheme: GPT
    Target System: UEFI (non CSM)

The resulting USB successfully booted the PN52.

---

## Disk Identification Challenge

The PN52 contains two identical Crucial P3 Plus 1 TB NVMe SSDs.

Both drives have the same:

- Manufacturer
- Model
- Approximate capacity
- SSD type

This created a potential installation risk.

The drives therefore could not safely be identified based only on appearance or model.

The disks were identified using multiple pieces of information:

- Disk number
- Model
- Capacity
- Partition layout
- Unique device identifier
- Existing operating-system contents

The unique device identifiers were used during the actual installation process to distinguish the two identical physical drives.

The specific identifiers are intentionally omitted from this public documentation.

---

## Windows Disk

One of the NVMe drives contained the existing Windows installation.

The disk was identified through its:

- Physical disk number
- Model
- Capacity
- Unique device identifier
- Existing partition layout
- Windows operating-system contents

Its partitions included:

    System
    ~100 MB

    Reserved
    ~16 MB

    C:
    ~999 GB
    Windows

    Recovery
    ~836 MB

This disk was designated as:

    PRESERVE

It was not selected as the Proxmox installation target.

The exact device serial number is intentionally omitted from the public repository.

---

## Proxmox Disk

The second NVMe drive was identified as the drive that could be repurposed for the lab.

The disk matched the same Crucial P3 Plus model and approximately 1 TB capacity as the Windows disk.

It was distinguished from the Windows disk using its unique device identifier and physical disk mapping.

The drive originally contained the Windows `D:` volume.

The user explicitly authorized this disk to be completely repurposed for the lab.

This disk was therefore selected as the Proxmox installation target.

The exact device serial number is intentionally omitted from the public repository.

---

## Proxmox Installer Disk Names

During the Proxmox installation, the two NVMe drives appeared as:

    /dev/nvme0n1
    /dev/nvme1n1

The graphical installer did not display the complete serial numbers.

Because the drives were identical, additional verification was performed using the Proxmox advanced/debug environment.

The serial-number mapping was compared against the previously identified physical disks.

The resulting installation decision was:

    /dev/nvme0n1
        │
        └── Verified as the authorized Proxmox target

    /dev/nvme1n1
        │
        └── Verified as the Windows disk

The actual serial-number values used to establish this mapping are intentionally not included in the public repository.

---

## Why Serial-Number Verification Was Important

The most significant installation risk was selecting the wrong NVMe drive.

Because both SSDs had the same manufacturer, model, and capacity, identifying a disk by model alone was insufficient.

The verification process therefore used multiple independent attributes.

    Physical Disk
         │
         ├── Disk Number
         ├── Model
         ├── Capacity
         ├── Partition Layout
         ├── Unique Identifier
         └── Existing Contents
                 │
                 ▼
           Confirm Identity
                 │
                 ▼
          Approve Installation

This provided a stronger basis for a destructive installation decision than relying on the order in which disks appeared in the installer.

---

## Installation Decision

The critical installation decision was:

    /dev/nvme0n1

was selected for Proxmox.

The Windows disk was not selected.

The decision was based on the verified physical-disk mapping rather than assuming that the first disk listed by the installer was safe to overwrite.

This was an intentional safety control.

---

## Installation

Proxmox VE 9.2-1 was installed to:

    /dev/nvme0n1

Filesystem:

    ext4

The installation completed successfully.

The Windows installation remained intact on its separate NVMe drive.

---

## UEFI Boot Configuration

Before Proxmox installation, the UEFI firmware contained:

    Windows Boot Manager

After Proxmox installation, the boot entries included:

    Proxmox
    Windows Boot Manager
    UEFI OS

The boot order was intentionally changed so that Windows remained the default operating system.

Final boot order:

    Boot Option #1
    Windows Boot Manager

    Boot Option #2
    Proxmox

    Boot Option #3
    UEFI OS

Windows subsequently booted normally.

---

## Why Windows Was Kept as the Default

The PN52 serves two purposes:

1. Normal Windows computer
2. Home lab virtualization host

The lab is therefore not intended to replace the normal Windows environment.

Keeping Windows as the default boot option allows the machine to continue functioning normally while Proxmox remains available as a separate lab environment.

This was a deliberate operational decision rather than an installer default.

---

## Proxmox Host Configuration

After installation, the Proxmox host was configured with private network values.

For public documentation, these values are represented generically:

    Hostname:
    <PROXMOX-HOSTNAME>

    FQDN:
    <PROXMOX-FQDN>

    Management IP:
    <PROXMOX-MANAGEMENT-IP>/<PREFIX-LENGTH>

    Gateway:
    <LAB-GATEWAY-IP>

    DNS:
    <LAB-DNS-SERVER>

    Search Domain:
    <LAB-DOMAIN>

The actual values remain part of the local infrastructure configuration.

They are intentionally not published in this repository because they identify the private home network.

---

## Initial Network Configuration

The Proxmox installer created the following basic network architecture:

    Realtek 2.5 GbE
          │
         nic0
          │
          ▼
        vmbr0
          │
          └── Proxmox Management

`nic0` represents the physical network interface.

`vmbr0` is a Linux bridge.

The bridge provides the virtual Layer 2 connection that future VMs can use to access the physical network.

---

## Direct Ethernet Verification

Because the PN52 was not yet physically connected to the lab router, a temporary direct Ethernet connection was used for testing.

The PN52 was connected directly to another laptop.

The laptop's Ethernet interface was temporarily configured with an address in the same management subnet as the Proxmox host.

Conceptually:

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

The laptop successfully reached the Proxmox management address.

The Proxmox web interface was successfully accessed.

The actual private IP addresses and management URL are intentionally omitted from this public documentation.

This verified:

- Physical Ethernet connectivity
- Proxmox network configuration
- `nic0`
- `vmbr0`
- Proxmox management networking
- Proxmox web service

---

## Why the Direct Connection Was Used

The direct connection allowed the Proxmox installation and management network to be tested before integrating the host with the larger physical network.

This reduced the number of variables involved in the test.

Instead of troubleshooting:

    PN52
      +
    Proxmox
      +
    MikroTik
      +
    MoCA
      +
    Home Network
      +
    Internet

the test focused on:

    Laptop
      +
    Ethernet
      +
    PN52
      +
    Proxmox

This is an example of isolating a system during troubleshooting.

By reducing the test path, the result provided stronger evidence about which part of the system was functioning.

---

## Initial System Verification

After installation, several Proxmox system areas were reviewed.

### Network

The expected `vmbr0` bridge and `nic0` relationship were verified.

### DNS

The configured DNS settings were reviewed.

Private DNS server addresses and the local search domain are intentionally omitted from the public repository.

### Hosts

The Proxmox hostname configuration was reviewed.

The actual local FQDN and private address are intentionally omitted from the public repository.

### Time

The configured time zone was:

    America/Los_Angeles

The displayed local time was not synchronized to current Internet time because the Proxmox host did not yet have a physical Internet connection.

This was not treated as a separate failure because network integration had not yet been completed.

---

## Package Database Update Observation

The Proxmox task list showed an error associated with:

    received interrupt (description: update package database)

At the time, the PN52 did not have a physical path to the lab router or Internet.

The Proxmox Summary page did not show a current system error.

The package database issue was therefore not treated as a priority during initial installation validation.

The decision was to avoid troubleshooting an Internet-dependent function before Internet connectivity existed.

This is an example of prioritizing problems based on the current system state rather than treating every logged message as an immediate fault.

---

## Installation Validation

The installation was considered successful because the following conditions were verified:

- Proxmox booted successfully
- The correct physical NVMe drive was used
- Windows remained intact
- Windows remained bootable
- Windows was restored as the default UEFI boot option
- Proxmox received its intended management configuration
- `vmbr0` was operational
- Proxmox management responded to ICMP during the direct Ethernet test
- Proxmox web interface was accessible
- The Proxmox Summary page showed no current critical errors

---

## Security and Privacy Considerations

The public documentation intentionally excludes infrastructure details that are unnecessary for demonstrating the technical work.

Excluded information includes:

- Private IP addresses
- Private DNS server addresses
- Local DNS/search domains
- Local hostnames that are specific to the home network
- Physical device serial numbers
- Credentials
- Passwords
- API keys
- Tokens
- Private keys
- Product or license keys

The repository documents the methodology and architecture rather than exposing unnecessary details about the private home network.

Where an exact value is important to explain a technical process, a sanitized placeholder or generalized description is used instead.

---

## Lessons Learned

### Identical Hardware Requires Stronger Identification

When multiple storage devices have identical models and capacities, device identification should rely on unique identifiers such as serial numbers in addition to model, capacity, and partition information.

### Destructive Operations Require Verification

Installing an operating system is potentially destructive because the selected disk may be reformatted.

The target should therefore be verified before proceeding.

### Installation Media Should Be Verified

Checksum verification provides confidence that the installation image matches the expected software artifact.

### Test One Layer at a Time

The direct Ethernet test verified Proxmox networking independently from the MikroTik and MoCA infrastructure.

This reduced troubleshooting complexity.

### Not Every Error Requires Immediate Action

The package database update issue was observed while the host lacked Internet connectivity.

Rather than immediately changing configuration, the problem was considered in the context of the environment.

### Preserve Existing Functionality

The Windows installation was intentionally preserved and made the default boot environment after Proxmox was installed.

This allowed the lab to coexist with the normal use of the computer.

### Public Documentation Should Separate Methodology From Private Data

A portfolio repository can demonstrate strong infrastructure practices without exposing private network information or unique device identifiers.

The important information is:

- What was discovered
- Why it mattered
- How it was verified
- What decision was made
- How the implementation was tested
- What was learned

---

## Current Status

### Completed

- Proxmox VE 9.2-1 installed
- Installation ISO verified
- Installation USB created
- Correct NVMe drive identified
- Windows NVMe drive preserved
- Proxmox installed to `/dev/nvme0n1`
- Proxmox host configured
- `vmbr0` configured
- Proxmox management connectivity verified
- Proxmox web interface verified
- Windows confirmed bootable
- Windows restored as default UEFI boot OS

### Not Yet Completed

- First VM
- VM operating-system deployment
- VM storage allocation
- VM network configuration
- Physical MikroTik integration
- MoCA integration
- Internet connectivity through the lab router
- VLAN configuration
- Routing experiments
- Firewall configuration
- Network segmentation

---

## Documentation Sanitization

The original installation investigation required exact device identifiers and private network information.

Those values were useful during the actual implementation but are not necessary for the public portfolio repository.

The public documentation preserves the technical reasoning while removing unnecessary private information.

The original workflow was:

    Identify
       ↓
    Verify
       ↓
    Implement
       ↓
    Test
       ↓
    Document

The public documentation additionally applies:

    Technical Evidence
           ↓
    Privacy Review
           ↓
    Sanitized Documentation
           ↓
    Public Repository

This demonstrates that infrastructure documentation must consider both technical accuracy and information security.

---

## Next Step

The next major virtualization milestone is the first virtual machine.

Before creating the VM, the operating-system ISO should be selected and verified.

The VM's intended purpose should also be established before assigning CPU, memory, storage, and networking resources.

The next step is therefore:

    Select VM operating system
            ↓
        Obtain ISO
            ↓
        Verify ISO
            ↓
       Document ISO
            ↓
        Create first VM
