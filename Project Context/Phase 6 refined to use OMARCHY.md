# **PHASE 6 REFINED: XPS 13 - Omarchy Primary + Windows 11 USB Dual-Boot**

## **STRATEGIC ANALYSIS**

### **✅ ADVANTAGES of Omarchy-Primary Architecture**

|Factor|Omarchy Host|Windows WSL2 (Original)|
|---|---|---|
|**Native Performance**|✅ Direct hardware access|⚠️ WSL2 VM overhead|
|**Docker/Containers**|✅ Native runtime|⚠️ Docker Desktop translation|
|**NFS Performance**|✅ Kernel-level NFS client|⚠️ WSL2 → Windows → NFS|
|**GPU Access**|✅ Intel Arc native drivers|❌ Limited in WSL2|
|**Cluster Integration**|✅ Native Slurm/kubectl|⚠️ WSL2 networking quirks|
|**Privacy/Security**|✅ Omarchy's focus|⚠️ Windows telemetry|
|**Battery Efficiency**|✅ Better power management|⚠️ WSL2 background drain|
|**Development Workflow**|✅ Native Linux tools|⚠️ Path translation issues|

**Decision**: Omarchy as primary OS **significantly enhances** cluster integration and aligns with privacy-first architecture.

### **⚠️ CHALLENGES & MITIGATIONS**

#### **Challenge 1: Windows 11 USB Performance**

- **Issue**: USB 3.2 Gen2 (~1.2 GB/s) vs NVMe (~7 GB/s) = **6x slower**
- **Impact**: Windows boot time ~2-3 min vs 20 sec
- **Mitigation**:
    - Use **high-quality USB 3.2 Gen2 drive** (Samsung T7 Shield, SanDisk Extreme Pro)
    - Enable ReadyBoost cache on USB
    - Keep Windows usage minimal (Office, gaming, Windows-only apps)

#### **Challenge 2: USB Drive Endurance**

- **Issue**: TLC/QLC NAND limited write cycles (~1000-3000)
- **Mitigation**:
    - Disable Windows hibernation (`powercfg /h off`)
    - Disable pagefile or move to RAM (`wmic pagefileset delete`)
    - Disable Windows Search indexing
    - Use write-cache optimization

#### **Challenge 3: Windows 11 System Requirements**

- **Issue**: Requires TPM 2.0, Secure Boot, UEFI
- **Solution**: XPS 13 (Copilot+ PC) **natively supports** all requirements ✅

#### **Challenge 4: UEFI Boot Management**

- **Issue**: Dual boot configuration
- **Solution**: Use systemd-boot or rEFInd boot manager (covered below)

---

## **REVISED PHASE 6: XPS 13 DUAL-BOOT SETUP**

**Duration**: 90 minutes | **Primary OS**: Omarchy | **Secondary OS**: Windows 11 on USB

---

### **STEP 6.0: HARDWARE PREPARATION**

#### **Required Hardware**:

1. **USB Drive**:
    - **Recommended**: Samsung T7 Shield 256GB (USB 3.2 Gen2, hardware encryption, shock-resistant)
    - **Minimum**: 128GB USB 3.2 Gen2 drive with sustained write >200 MB/s
    - ⚠️ **Do NOT use**: Cheap USB sticks, USB 2.0 drives
2. **USB-C to 2.5GbE Adapter** (already owned)
3. **Temporary USB for Installation Media** (8GB+)

#### **Pre-Installation Checklist**:

bash

```bash
# On Desktop (preparation workstation)

# 1. Download Omarchy ISO
wget https://omarchy.org/download/omarchy-latest.iso
# Verify checksum from website

# 2. Download Windows 11 ISO
# Visit: https://www.microsoft.com/software-download/windows11
# Download Windows 11 64-bit ISO

# 3. Download Rufus (for Windows USB creation)
wget https://github.com/pbatard/rufus/releases/download/v4.6/rufus-4.6.exe

# 4. Download Ventoy (alternative bootloader)
wget https://github.com/ventoy/Ventoy/releases/download/v1.0.99/ventoy-1.0.99-linux.tar.gz
```

---

### **STEP 6.1: INSTALL OMARCHY ON INTERNAL NVME**

#### **6.1.1: Create Omarchy Installation USB**

**On Desktop**:

bash

````bash
# Create bootable Omarchy USB
sudo dd if=omarchy-latest.iso of=/dev/sdX bs=4M status=progress conv=fsync
# Replace /dev/sdX with your USB device (check with lsblk)

# Or use Ventoy (allows multiple ISOs on one USB)
tar -xzf ventoy-1.0.99-linux.tar.gz
cd ventoy-1.0.99
sudo ./Ventoy2Disk.sh -i /dev/sdX
# Then copy ISO to USB: cp omarchy-latest.iso /media/user/Ventoy/
```

#### **6.1.2: Boot XPS 13 from USB**

1. Insert Omarchy USB into XPS 13
2. Power on, press **F12** for boot menu
3. Select USB device
4. Boot into Omarchy live environment

#### **6.1.3: Partition Strategy for Dual-Boot**

**Recommended Partition Layout** (512GB internal NVMe):
```
/dev/nvme0n1p1: 512 MB    EFI System Partition (ESP) - SHARED
/dev/nvme0n1p2: 480 GB    Omarchy Root (ext4)
/dev/nvme0n1p3: 30 GB     Omarchy Home (ext4) - separate for easier backup
/dev/nvme0n1p4: Remaining Swap (8-16 GB)
````

**Why no Windows on internal?**

- Windows 11 on USB keeps internal NVMe 100% Linux
- Eliminates partition conflicts
- Allows full disk encryption for Omarchy
- Windows USB can be used on other machines

#### **6.1.4: Execute Omarchy Installation**

**In Omarchy Live Environment**:

bash

```bash
# Launch installer (command depends on Omarchy's installer)
# Assuming standard Debian/Ubuntu-based installer

# Select installation options:
# - Disk: /dev/nvme0n1 (entire internal NVMe)
# - Partitioning: Manual (use layout above)
# - Encryption: Enable LUKS full-disk encryption
# - Bootloader: systemd-boot (preferred) or GRUB
# - Hostname: xps13-omarchy
# - Username: aiops
# - Password: [secure password]

# Important: During partitioning, ensure EFI partition is:
# - Size: 512 MB
# - Type: EFI System Partition
# - Filesystem: FAT32
# - Mount: /boot/efi
# - Flags: boot, esp

# Complete installation
# Remove USB when prompted
# Reboot into Omarchy
```

#### **6.1.5: Post-Installation Omarchy Configuration**

**After first boot into Omarchy**:

bash

```bash
# Update system
sudo apt update && sudo apt full-upgrade -y
# Or equivalent for Omarchy's package manager

# Install essential tools
sudo apt install -y \
    git curl wget vim tmux htop \
    build-essential python3-pip python3-venv \
    docker.io docker-compose \
    nfs-common \
    network-manager

# If Omarchy uses different package manager, adapt accordingly

# Configure static IP on USB-C 2.5GbE adapter
# Identify adapter name
ip link show
# Look for USB ethernet adapter (e.g., enx...)

# Create NetworkManager connection
sudo nmcli connection add \
    type ethernet \
    con-name cluster-ethernet \
    ifname enx* \
    ip4 192.168.1.30/24 \
    gw4 192.168.1.1

sudo nmcli connection modify cluster-ethernet \
    ipv4.dns "192.168.1.1 8.8.8.8"

sudo nmcli connection up cluster-ethernet

# Verify connectivity
ping -c 3 192.168.1.10  # Desktop
ping -c 3 8.8.8.8       # Internet
```

---

### **STEP 6.2: CREATE WINDOWS 11 USB WITH WINDOWS TO GO**

#### **6.2.1: Prepare USB Drive**

**⚠️ WARNING: This will ERASE the USB drive!**

**Option A: Using Rufus (Recommended - Windows-based)**

If you have temporary access to a Windows machine:

1. Insert 128GB USB drive
2. Launch Rufus
3. Settings:
    - Device: [Your 128GB USB]
    - Boot selection: Windows 11 ISO
    - Partition scheme: GPT
    - Target system: UEFI (non CSM)
    - File system: NTFS
    - Cluster size: Default
4. Click **START**
5. When prompted, select:
    - **Windows To Go** option
    - Disable "Remove requirement for TPM 2.0" (XPS 13 has TPM)
6. Wait for creation (~15-30 minutes)

**Option B: Using WinToUSB (Windows-based)**

1. Download WinToUSB Free: [https://www.easyuefi.com/wintousb/](https://www.easyuefi.com/wintousb/)
2. Select Windows 11 ISO
3. Select USB drive
4. Choose installation type: Windows To Go
5. Create

**Option C: Using Linux (Advanced)**

bash

```bash
# On Desktop (requires more manual steps)

# 1. Partition USB drive
sudo fdisk /dev/sdY  # Replace Y with USB drive letter

# Create GPT partition table:
# g (create GPT)
# n (new partition 1: EFI - 512MB)
# t (type: 1 = EFI)
# n (new partition 2: Windows - remaining space)
# t (type: 11 = Microsoft Basic Data)
# w (write and exit)

# 2. Format partitions
sudo mkfs.fat -F32 /dev/sdY1       # EFI partition
sudo mkfs.ntfs -f /dev/sdY2        # Windows partition

# 3. Mount Windows ISO
sudo mkdir -p /mnt/iso /mnt/usb
sudo mount -o loop Windows11.iso /mnt/iso
sudo mount /dev/sdY2 /mnt/usb

# 4. Copy Windows files
sudo rsync -avP /mnt/iso/ /mnt/usb/

# 5. Install Windows bootloader
# This requires WinPE or Windows Recovery Environment
# Recommended: Use Rufus method instead for simplicity
```

#### **6.2.2: First Boot Windows 11 from USB**

1. Insert Windows 11 USB into XPS 13
2. Reboot, press **F12** for boot menu
3. Select USB device (should show "Windows Boot Manager")
4. First boot will be SLOW (~3-5 minutes)
5. Complete Windows 11 OOBE (Out-of-Box Experience):
    - Region: [Your region]
    - Keyboard: US
    - Network: Skip for now (offline setup)
    - Account: Local account (avoid Microsoft account for privacy)
        - Username: `aiops`
        - Password: [secure password]
    - Privacy settings: Disable all telemetry

#### **6.2.3: Optimize Windows 11 for USB Performance**

**In Windows 11 (as Administrator)**:

powershell

```powershell
# Disable hibernation (saves 12GB+ on USB)
powercfg /h off

# Disable pagefile (use RAM only - XPS 13 has 32GB)
wmic pagefileset delete

# Disable Windows Search indexing
sc stop "WSearch"
sc config "WSearch" start=disabled

# Disable system restore
Disable-ComputerRestore -Drive "C:\"

# Optimize USB write cache
# Open Device Manager → Disk Drives → [USB Drive]
# Properties → Policies → Enable "Better performance"

# Disable SuperFetch/SysMain
sc stop "SysMain"
sc config "SysMain" start=disabled

# Disable Windows Update automatic downloads (manual only)
# Settings → Windows Update → Advanced Options
# Set "Download updates over metered connections" to OFF
# Configure Active Hours

# Enable compact OS (saves ~2GB)
Compact.exe /CompactOS:always

# Verify free space
Get-Volume
# Ensure 20-30GB free after optimization
```

---

### **STEP 6.3: CONFIGURE DUAL-BOOT MANAGER**

#### **6.3.1: Install rEFInd Boot Manager (Recommended)**

**Why rEFInd?**

- Automatically detects all bootable OSes
- Beautiful GUI
- No manual configuration needed
- Works with both Linux and Windows

**In Omarchy**:

bash

```bash
# Install rEFInd
sudo apt install refind
# Or if not in repos:
cd /tmp
wget https://sourceforge.net/projects/refind/files/0.14.2/refind-bin-0.14.2.zip
unzip refind-bin-0.14.2.zip
cd refind-bin-0.14.2
sudo ./refind-install

# Configure rEFInd
sudo nano /boot/efi/EFI/refind/refind.conf

# Add timeout and default boot:
# timeout 10
# default_selection "Omarchy"
# (rEFInd auto-detects entries, minimal config needed)

# Reboot to test
sudo reboot
```

**Expected Behavior**:

- Reboot shows rEFInd menu with:
    - Omarchy (internal NVMe)
    - Windows Boot Manager (if USB inserted)
- Arrow keys to select, Enter to boot
- Default boots Omarchy after 10 seconds

#### **6.3.2: Alternative - systemd-boot Configuration**

If Omarchy uses systemd-boot (common in modern distros):

bash

```bash
# Check if systemd-boot is installed
bootctl status

# Add Windows entry (only when USB is inserted)
sudo nano /boot/efi/loader/entries/windows.conf
```

ini

```ini
title   Windows 11 USB
efi     /EFI/Microsoft/Boot/bootmgfw.efi
options root=/dev/sdX2
```

bash

```bash
# Update boot loader
sudo bootctl update

# Set default
sudo nano /boot/efi/loader/loader.conf
```

ini

```ini
default omarchy.conf
timeout 5
console-mode keep
editor no
```

---

### **STEP 6.4: CONFIGURE OMARCHY FOR CLUSTER ACCESS**

#### **6.4.1: Install Cluster Tools**

bash

```bash
# Install Docker
sudo apt install -y docker.io docker-compose
sudo usermod -aG docker aiops
newgrp docker

# Test Docker
docker run hello-world

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install Slurm client + munge
sudo apt install -y slurm-client munge

# Copy munge key from desktop
scp aiops@192.168.1.10:/etc/munge/munge.key /tmp/
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key

# Start munge
sudo systemctl enable munge
sudo systemctl start munge

# Copy Slurm config
scp aiops@192.168.1.10:/etc/slurm/slurm.conf /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/

# Test Slurm
sinfo
squeue
```

#### **6.4.2: Mount NFS Shares**

bash

```bash
# Create mount points
sudo mkdir -p /mnt/nfs/{shared,models,datasets,results,obsidian}

# Add to fstab
sudo tee -a /etc/fstab > /dev/null <<'EOF'

# NFS mounts from desktop cluster
192.168.1.10:/srv/nfs/shared            /mnt/nfs/shared         nfs defaults,_netdev,x-systemd.automount 0 0
192.168.1.10:/srv/nfs/models            /mnt/nfs/models         nfs defaults,_netdev,x-systemd.automount 0 0
192.168.1.10:/srv/nfs/datasets          /mnt/nfs/datasets       nfs defaults,_netdev,x-systemd.automount 0 0
192.168.1.10:/srv/nfs/results           /mnt/nfs/results        nfs defaults,_netdev,x-systemd.automount 0 0
192.168.1.10:/srv/nfs/obsidian-vault    /mnt/nfs/obsidian       nfs defaults,_netdev,x-systemd.automount 0 0
EOF

# Mount
sudo systemctl daemon-reload
sudo mount -a

# Verify
df -h | grep nfs
```

#### **6.4.3: Setup kubectl for MicroK8s**

bash

```bash
# Get kubeconfig from desktop
scp aiops@192.168.1.10:~/.kube/config /tmp/microk8s-config.yaml

# Setup kubectl
mkdir -p ~/.kube
mv /tmp/microk8s-config.yaml ~/.kube/config

# Fix server IP
sed -i 's|127.0.0.1:16443|192.168.1.10:16443|g' ~/.kube/config

# Test
kubectl get nodes
kubectl get pods -A
```

#### **6.4.4: Intel Arc GPU Configuration (Optional)**

Since XPS 13 has Intel Arc iGPU, enable for OpenCL workloads:

bash

```bash
# Install Intel GPU drivers
sudo apt install -y \
    intel-gpu-tools \
    intel-opencl-icd \
    clinfo

# Verify GPU detected
clinfo
# Should show Intel Arc GPU

# Test OpenCL
cat > /tmp/test_opencl.c <<'EOF'
#include <CL/cl.h>
#include <stdio.h>

int main() {
    cl_uint num_platforms;
    clGetPlatformIDs(0, NULL, &num_platforms);
    printf("OpenCL Platforms: %d\n", num_platforms);
    return 0;
}
EOF

gcc /tmp/test_opencl.c -o /tmp/test_opencl -lOpenCL
/tmp/test_opencl
# Should show: OpenCL Platforms: 1
```

---

### **STEP 6.5: CREATE PORTABLE DEVELOPMENT ENVIRONMENT**

#### **6.5.1: Install Development Tools**

bash

```bash
# Programming languages
sudo apt install -y \
    python3 python3-pip python3-venv \
    nodejs npm \
    golang-go \
    rust-all

# Python AI/ML libraries
pip3 install --user \
    numpy pandas matplotlib \
    jupyter jupyterlab \
    torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu

# VS Code (if not in Omarchy by default)
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
sudo apt update
sudo apt install -y code

# Git configuration
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

#### **6.5.2: Setup Obsidian Integration**

bash

```bash
# Install Obsidian (AppImage method)
cd ~/Downloads
wget https://github.com/obsidianmd/obsidian-releases/releases/download/v1.7.7/Obsidian-1.7.7.AppImage
chmod +x Obsidian-1.7.7.AppImage
sudo mv Obsidian-1.7.7.AppImage /opt/obsidian

# Create desktop entry
cat > ~/.local/share/applications/obsidian.desktop <<'EOF'
[Desktop Entry]
Name=Obsidian
Exec=/opt/obsidian %u
Terminal=false
Type=Application
Icon=obsidian
Categories=Office;
MimeType=x-scheme-handler/obsidian;
EOF

# Launch and point to NFS vault
/opt/obsidian &
# File → Open Vault → /mnt/nfs/obsidian
```

---

### **STEP 6.6: WINDOWS 11 CLUSTER ACCESS (OPTIONAL)**

If you need cluster access from Windows 11:

#### **6.6.1: Install WSL2 in Windows 11 USB**

powershell

````powershell
# In Windows 11 on USB (as Administrator)
wsl --install -d Ubuntu-24.04

# After reboot, configure Ubuntu WSL
wsl

# Inside WSL, repeat Step 6.4 commands:
# - Install kubectl, Slurm client
# - Mount NFS shares (via WSL2's mount capabilities)
# - Configure cluster access
```

**Note**: Performance will be degraded (USB + WSL2 overhead), use sparingly.

---

## **FINAL XPS 13 CONFIGURATION SUMMARY**

### **Boot Sequence**:
```
1. Power On XPS 13
   ↓
2. rEFInd Boot Menu (10 sec timeout)
   ├─→ Omarchy (default) - Internal NVMe
   └─→ Windows 11 - USB Drive (if inserted)
   ↓
3. Selected OS boots
````

### **Omarchy Capabilities** ✅:

- Native Docker containers
- Direct NFS mounts (high performance)
- MicroK8s kubectl access
- Slurm job submission
- Intel Arc GPU for OpenCL
- Full development environment
- Obsidian vault integration
- SSH access to all cluster nodes

### **Windows 11 Capabilities** ✅:

- Windows-only applications
- Office 365
- Gaming (limited by USB speed)
- WSL2 for basic Linux tasks (optional)
- Portable between machines

### **Performance Comparison**:

|Task|Omarchy (NVMe)|Windows 11 (USB)|
|---|---|---|
|Boot Time|15-20 sec|2-3 min|
|App Launch|Instant|2-5 sec|
|File I/O|7000 MB/s|1200 MB/s|
|Docker Build|Fast|N/A|
|NFS Access|Native|Via WSL2 (slow)|
|Battery Life|8-10 hours|6-8 hours|

---

## **VALIDATION TESTS**

### **Test 1: Dual Boot**

bash

```bash
# From Omarchy:
sudo reboot

# At rEFInd menu:
# 1. Verify Omarchy entry present ✓
# 2. Insert Windows USB
# 3. Press ESC to rescan
# 4. Verify Windows entry appears ✓
# 5. Boot into Windows ✓
# 6. Reboot back to Omarchy ✓
```

### **Test 2: Cluster Access from Omarchy**

bash

```bash
# Network
ping -c 2 192.168.1.10  # Desktop
ping -c 2 192.168.1.20  # XPS 15
ping -c 2 192.168.1.40  # Jetson

# NFS
ls /mnt/nfs/shared

# Kubernetes
kubectl get nodes

# Slurm
sinfo
sbatch --wrap="hostname && date" 
squeue
```

### **Test 3: Windows 11 USB Performance**

powershell

```powershell
# In Windows 11
# Open Task Manager → Performance
# Monitor disk speed during app launch
# Should see 500-1200 MB/s sustained
```

---

## **USB DRIVE LONGEVITY OPTIMIZATION**

### **Expected USB Lifespan**:

- **Consumer USB (TLC NAND)**: 1-2 years with daily Windows use
- **Pro USB (MLC/3D NAND)**: 3-5 years with optimization
- **Samsung T7 Shield**: ~5 years (has wear leveling, encryption HW)

### **Monitoring Script** (run in Windows monthly):

powershell

```powershell
# Check USB health
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, HealthStatus, OperationalStatus

# Check SMART data (requires third-party tools like CrystalDiskInfo)
# Download: https://crystalmark.info/en/software/crystaldiskinfo/
```

### **Backup Strategy**:

bash

```bash
# Monthly: Clone Windows USB to spare USB (in Omarchy)
sudo dd if=/dev/sdY of=/path/to/backup/windows11-backup-$(date +%Y%m%d).img bs=4M status=progress

# Compress backup
pigz -9 /path/to/backup/windows11-backup-*.img
```

---

## **MIGRATION PATH TO PERMANENT WINDOWS (OPTIONAL)**

If USB performance becomes an issue, migrate Windows to internal NVMe:

bash

```bash
# Future option: Resize Omarchy partitions
# Shrink Omarchy Home from 30GB → 10GB
# Create new 20GB partition for Windows
# Restore Windows image to partition
# Update rEFInd configuration
```

But for now, **USB boot is optimal** given:

- ✅ Windows rarely used (per your requirements)
- ✅ Keeps internal NVMe 100% optimized for Omarchy
- ✅ USB can be physically removed for security
- ✅ Portable between machines

---

## **IMPACT ON PROJECT GOALS**

### **✅ ENHANCED**:

1. **Privacy** - Omarchy's privacy focus + Linux-primary = stronger data sovereignty
2. **Performance** - Native Linux eliminates WSL2 overhead (~15% faster NFS, Docker)
3. **Integration** - Seamless cluster membership (no translation layers)
4. **Flexibility** - Windows on USB = portable, removable, sandboxed
5. **Security** - LUKS encryption on Omarchy + physical USB separation

### **➡️ NEUTRAL**:

1. **Windows Requirement** - Met (USB Windows is fully functional)
2. **Development** - Same tools available (VS Code, Docker, etc.)

### **⚠️ TRADE-OFFS**:

1. **Windows Speed** - 6x slower disk I/O (acceptable for occasional use)
2. **Setup Complexity** - +30 minutes for USB creation
3. **USB Durability** - Needs monitoring/backup strategy

---

## **FINAL RECOMMENDATION**

**✅ PROCEED with Omarchy-primary architecture** because:

1. XPS 13 is **cluster client** (not compute node) - native Linux optimal
2. Windows "rarely needed" per requirements - USB acceptable
3. Aligns with **privacy-first** infrastructure philosophy
4. **Future-proof** - easy to migrate Windows to internal if needed
5. Samsung T7 Shield USB + optimizations = **3-5 year lifespan**

**Next Steps**:

1. Order Samsung T7 Shield 256GB (if not using 128GB)
2. Backup XPS 13 current data (if any)
3. Execute Phase 6 Steps 6.1-6.6
4. Validate with Tests 1-3
5. Proceed to Phase 7 (advanced configurations)

Would you like me to:

1. Generate automation scripts for Omarchy post-install?
2. Create detailed troubleshooting for dual-boot issues?
3. Design the Windows USB optimization checklist?