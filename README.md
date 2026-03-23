# 💻 Dual Boot Lab Setup — Windows 11 + Ubuntu 24.04 LTS

> **Real-world dual boot configuration on a 1TB HDD desktop tower — solved MBR 4-partition limit, converted to GPT, installed both OSes with GRUB bootloader using Ventoy for multi-ISO USB management.**

---

## 📋 Project Overview

This project documents a complete dual boot setup of Windows 11 and Ubuntu 24.04 LTS on a single HDD-based desktop PC. The setup involved solving real hardware constraints — a legacy MBR partition scheme that blocked Ubuntu installation — requiring disk conversion, partition management, and bootloader configuration before a clean dual boot was achievable.

This is the kind of problem encountered regularly when migrating legacy enterprise systems.

---

## 🖥️ System Specifications

| Component | Details |
|---|---|
| Machine | Desktop Tower |
| RAM | 16 GB DDR4 (2× 8 GB) |
| Storage | 1 TB HDD (Seagate ST1000DM010-2EP102) |
| Motherboard | MSI |
| Boot Mode | UEFI |
| Original OS | Windows 10 (MBR disk) |
| Target | Windows 11 + Ubuntu 24.04 LTS dual boot |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Ventoy 1.1.10 | Multi-ISO bootable USB — both ISOs on one USB, no reflashing |
| GParted | Partition management from Ubuntu live session |
| MBR2GPT | Microsoft built-in MBR to GPT conversion tool (attempted) |
| Windows Disk Management | Initial partition shrink attempt |
| Ubuntu 24.04.4 LTS ISO | Linux OS installer |
| Windows 11 ISO | Windows OS installer |
| 16 GB SanDisk Cruzer Blade | Bootable USB drive |

---

## ⚠️ Problem: MBR 4-Partition Limit

**Root Cause:** The original disk used MBR (Master Boot Record) partition scheme. MBR has a hard limit of **4 primary partitions**. The disk already had all 4 used by Windows — leaving no room for Ubuntu.

The Ubuntu installer did not show the "Install alongside Windows" option because there was no unallocated space in an available partition slot.

### Attempted Fix: MBR2GPT Conversion
Ran Microsoft's `MBR2GPT` tool to convert the disk from MBR to GPT without data loss. This **failed** — the C: drive (49 GB total, only 5.89 GB free) did not have enough contiguous space for MBR2GPT to create the required EFI System Partition.

### Final Solution
1. Deleted the D: drive partition (892 GB — confirmed empty, no data loss)
2. Reduced partition count from 4 to 3 — unblocking the installer
3. Performed a **clean Windows 11 install** which automatically initialized the disk as **GPT**
4. Installed Ubuntu 24.04 on the remaining unallocated space
5. GRUB bootloader configured automatically

GPT eliminates the 4-partition limit entirely — the correct long-term solution for any modern system.

---

## 📦 Ventoy Multi-ISO USB

Instead of using Rufus (which overwrites the USB for every single OS), Ventoy was used to store **both ISOs on a single USB** simultaneously.

```
16GB SanDisk USB (Ventoy)
├── Windows 11 ISO
└── Ubuntu 24.04.4 LTS ISO

→ Boot USB once → select OS from menu → install both without reflashing
```

Ventoy is significantly more efficient for lab environments where multiple OS installations are common.

---

## 🗂️ Installation Sequence

### Step 1 — Prepare USB
- Flashed Ventoy 1.1.10 onto USB with GPT partition style
- Copied both ISOs into Ventoy storage partition

### Step 2 — Windows 11 Installation
- Booted from Ventoy USB → selected Windows 11 ISO
- Deleted all existing partitions → left 931.5 GB unallocated
- Created 50 GB partition for Windows — installer auto-created EFI (200 MB) and MSR (16 MB) partitions
- Installed Windows 11 with offline account (no Microsoft account)
- 881 GB unallocated space remaining for Ubuntu

### Step 3 — Ubuntu 24.04 Installation
- Rebooted → selected Ubuntu ISO from Ventoy menu
- Ubuntu installer detected Windows Boot Manager and unallocated space correctly
- Selected "Install Ubuntu alongside Windows Boot Manager"
- Configured user account, timezone (Asia/Kolkata)
- GRUB installed to EFI partition (sda1) — automatic

---

## 💾 Final Disk Layout

| Partition | Size | Filesystem | Purpose |
|---|---|---|---|
| sda1 | 200 MB | FAT32 | EFI System Partition (shared) |
| sda2 | 16 MB | — | Microsoft Reserved |
| sda3 | 50 GB | NTFS | Windows 11 |
| sda4 | 16 MB | — | Windows Recovery |
| sda5 | 881 GB | ext4 | Ubuntu 24.04 LTS |

---

## 🥾 Boot Configuration (GRUB Menu)

```
┌─────────────────────────────────────┐
│  Ubuntu (default — boots after 10s) │
│  Advanced options for Ubuntu        │
│  Windows Boot Manager               │
│  Memory Test (Memtest86+)           │
│  UEFI Firmware Settings             │
└─────────────────────────────────────┘
```

GRUB is installed on the shared EFI partition (sda1). Both operating systems boot independently with no interference.

---

## ✅ Skills Demonstrated

- **Disk partitioning concepts:** MBR vs GPT, primary vs logical, EFI requirements
- **Real troubleshooting:** Identified MBR 4-partition limit as the root blocker, not a software issue
- **Bootable USB creation:** Ventoy with multiple ISOs — practical efficiency over single-ISO tools
- **Dual boot configuration:** GRUB bootloader setup with multiple OS entries
- **Windows 11 installation:** Clean install with offline account, automatic GPT initialization
- **Ubuntu 24.04 installation:** Alongside existing Windows, timezone and locale setup
- **UEFI/BIOS navigation:** Boot order, UEFI firmware settings access via GRUB

---

## 📁 Project Files

```
dual-boot-lab-setup/
├── docs/
│   └── DualBoot_Lab_Setup.docx
└── README.md
```

---

## 📚 Concepts Demonstrated

`MBR vs GPT` `UEFI vs BIOS` `Partition Schemes` `GRUB Bootloader` `Ventoy` `GParted` `Windows 11 Installation` `Ubuntu 24.04 LTS` `Dual Boot` `EFI System Partition` `Legacy System Migration`

---

## 👨‍💻 Author

**Vasanth Kumar**
BCA — Vivekananda Institute of Management, Bengaluru, Karnataka
📧 vasanthkumarvk2855@gmail.com
🔗 [LinkedIn](https://linkedin.com/in/vasanth-kumar-vk55) | [GitHub](https://github.com/vasanth-kumar-vk)

---

*This project is part of a hands-on IT infrastructure portfolio built to demonstrate entry-level System Administrator and IT Support skills.*
