# Exact Device Hardware Specs

## Exact AI Desktop Specs

AMD Ryzen 9 9900X Granite Ridge AM5 4.40GHz 12-Core Boxed Processor

- Mfr Part#: 100-100000662WO

Gigabyte X870 AORUS ELITE WIFI7 AMD AM5 ATX Motherboard

- Mfr Part #:  X870AORELITEWF7

TeamGroup T-Create EXPERT 64GB (2 x 32GB) DDR5-6400 PC5-51200 CL34 Dual Channel Desktop

- Mfr Part #: CTCED564G6400HC34BDC01
- 4x32 gb sticks have been installed in this desktop

Gigabyte GeForce RTX™ 5070 Ti WINDFORCE OC SFF 16G

- Mfr Part#: GV-N507TWF3OC-16GD

Samsung 9100 PRO 2TB Samsung V NAND TLC NAND (V8) PCIe Gen 5x4 and PCIe Gen 5x4 NVMe M.2

- Mfr Part#: MZ-VAP2T0B/AM

HYTE THICC Q60 240mm All in One Liquid CPU Cooling Kit

- Mfr Part#: FAN-HYTE-Q60-BW

[[Maximizing specific hardware specialties]]

## References

- [AMD Ryzen 9 9900X Support](https://www.amd.com/en/support/downloads/drivers.html/processors/ryzen/ryzen-9000-series/amd-ryzen-9-9900x.html#amd_support_product_spec)
- [Gigabyte X870 AORUS Motherboard](https://www.gigabyte.com/Motherboard/X870-AORUS-ELITE-WIFI7#kf)
- [Gigabyte RTX 5070 Ti Support](https://www.gigabyte.com/Graphics-Card/GV-N507TWF3OC-16GD/support#support-dl)
- [HYTE Q60 Cooling Kit](https://hyte.com/store/q60/fan-hyte-q60-bw?srsltid=AfmBOopfSGSLCrKkYGZ3CtT7CSCj0lKPFzO8aowoRBjI7zmXOJXKimV9)
- [Samsung 9100 PRO](https://www.samsung.com/us/memory-storage/nvme-ssd/9100-pro-nvme-ssd-sku-mz-vap2t0b-am/)
- [TeamGroup T-CREATE EXPERT RAM](https://www.teamgroupinc.com/en/product-detail/memory/T-CREATE/expert-u-dimm-ddr5-black/expert-u-dimm-ddr5-black-CTCED564G6400HC34BDC01/)

## Exact Jetson Orin Nano Super Specs

NVIDIA Jetson Orin Nano Super Developer Kit

Jetson Orin Nano 8GB Module

AI Performance 67 TOPS

GPU NVIDIA Ampere architecture with 1024 CUDA cores and 32 tensor cores

CPU 6-core Arm® Cortex®-A78AE v8.2 64-bit CPU 1.5MB L2 + 4MB L3

Memory 8GB 128-bit LPDDR5 102GB/s

Storage 32gb SD card

NVME M2: Samsung 990 EVO Plus 1TB Samsung V NAND TLC NAND PCIe Gen 4x4 and PCIe Gen 5x2

Video Encode 1080p30 supported by 1-2 CPU cores

Video Decode 1x 4K60 (H.265), 2x 4K30 (H.265), 5x 1080p60 (H.265), 11x 1080p30 (H.265)

Power 7W–25W

Camera 2x MIPI CSI-2 22-pin camera connectors

PCIe M.2 Key M slot with x4 PCIe Gen3, M.2 Key M slot with x2 PCIe Gen3, M.2 Key E slot

USB USB Type-A connector: 4x USB 3.2 Gen2, USB Type-C connector for UFP

Networking 1xGbE connector

Display 1x DP 1.2 (+MST) connector

Other I/O 40-pin expansion header (UART, SPI, I2S, I2C, GPIO), 12-pin button header, 4-pin fan header, DC power jack

Available cables

USB Type-C to USB Type-C

- Super High Speed usb3.2 CABLE E118077-W AWM 21572 vw-1 ji-haw

USB Type-C to USB Type-CA

- Super High Speed usb3.1 gen2 10g CABLE E118077-W AWM 21572 vw-1 ji-haw

## Portable Workstation (XPS 13)

- **CPU**: Intel Core Ultra 7 258V (8 cores, up to 4.8GHz)
- **GPU**: Intel Arc (integrated, ~18GB shared VRAM via system RAM)
- **RAM**: 32GB LPDDR5X-8533
- **Storage**: 512GB M.2 PCIe NVMe SSD
- **Display**: 13.4" Touch, 3K OLED
- **OS**: Windows 11 Home, Copilot+ PC
- **Network**: USB-C to 2.5GbE adapter

## Stationary Workhorse (XPS 15 7590)

- **CPU**: Intel Core (Model 158, ~2.4GHz base)
- **RAM**: 64GB
- **Storage**: 1TB NVMe (assumed)
- **OS**: Windows 11 (recommend Ubuntu Server 24.04 LTS)
- **Network**: USB-C to 2.5GbE adapter

## Storage

- **WD NAS**: 2TB, connected via USG Gateway, ~280MB/s over 1GbE

## WD NAS Details

- **Purpose**: GitHub repository hub, archive, storage, potentially used for api secret hashicorp vault
- **Connection**: Via USG Gateway

## Recycled Hardware Inventory

### Lenovo Laptop (Dell P93G001)

- **CPU**: 8th Gen Intel Core i7-8565U processor (4 cores, 8 threads, up to 4.6GHz)
- **RAM**: 8GB
- **Storage**: 256GB SSD (14-inch screen)
- **Status**: Factory reset, ready for deployment
- **Proposed Role**: Slurm login node / SSH bastion host

### Samsung HD204UI

- **Capacity**: 2TB SATA HDD
- **Speed**: 5400 RPM
- **Status**: Factory reset
- **Proposed Role**: Cold storage tier for NAS backups/archives

### Western Digital WD10EADS

- **Capacity**: 1TB SATA HDD
- **Speed**: 7200 RPM
- **Status**: Factory reset
- **Proposed Role**: Cold storage tier for NAS backups/archives

### MicroCenter USB Storage Devices

- **Type**: 128GB USB Type-B and USB Type-C drives
- **Status**: Ready for use
- **Proposed Role**: Encrypted portable data shuttles for air-gapped transfers

### Apple TV A1427 (3rd Generation)

- **Model**: Apple TV 3rd Gen
- **Status**: Factory reset
- **Proposed Role**: Dashboard/monitoring display kiosk for Grafana
