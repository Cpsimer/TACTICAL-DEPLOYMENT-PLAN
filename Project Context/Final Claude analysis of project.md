# 🎯 **TACTICAL DEPLOYMENT PLAN - Based on Actual System Status**

## **[[CRITICAL FINDINGS & IMMEDIATE PRIORITIES]]** 

### **🚨 BLOCKING ISSUE: Network Subnet Mismatch**

```
Desktop Current IP: 10.0.10.138/24
Expected Cluster Subnet: 192.168.1.0/24
Result: ZERO connectivity to planned node IPs
```

**This must be fixed FIRST before any other work.**

---

## **PHASE 0: EMERGENCY NETWORK RECONFIGURATION** ⚠️

**Duration**: 15 minutes | **CRITICAL PATH**

### **Issue Analysis**

Your desktop is connected to UniFi network but assigned to wrong subnet. The UniFi gateway (192.168.1.1) is visible in nmap, but desktop got DHCP lease from 10.0.10.0/24 pool.

### **Solution: Assign Static IP on Correct Subnet**

```bash
# Step 1: Identify the correct network interface
ip addr show enp6s0
# Current: 10.0.10.138/24

# Step 2: Create static IP configuration
sudo nano /etc/netplan/01-netcfg.yaml
```
- [ ] 
**Paste this configuration**:
- [ ] 

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp6s0:
      dhcp4: no
      addresses:
        - 192.168.1.10/24  # Desktop will be .10
      routes:
        - to: default
          via: 192.168.1.1  # UniFi gateway
      nameservers:
        addresses: [192.168.1.1, 1.1.1.1, 8.8.8.8]
```

```bash
# Step 3: Apply configuration
sudo netplan apply

# Step 4: Verify new IP
ip addr show enp6s0 | grep "inet "
# Should show: 192.168.1.10/24

# Step 5: Test connectivity to UniFi gateway
ping -c 3 192.168.1.1
# Should succeed

# Step 6: Test internet connectivity
ping -c 3 8.8.8.8
# Should succeed
```
- [ ] 
**Expected Result**: Desktop now at **192.168.1.10/24**, ready for cluster formation.
- [ ] 
---

## **PHASE 1: DESKTOP NODE - COMPLETE SLURM SETUP**

**Duration**: 45 minutes | **Dependencies**: Phase 0 complete

### **1.1: Install Slurm + Dependencies**

```bash
# Update package lists
sudo apt update

# Install Slurm packages
sudo apt install -y slurm-wlm slurm-wlm-basic-plugins slurmd slurmctld munge

# Install RyzenAdj for CPU tuning
cd /tmp
git clone https://github.com/FlyGoat/RyzenAdj.git
cd RyzenAdj
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
sudo make install

# Verify installations
which slurmctld
which slurmd
which ryzenadj
```
- [ ] 
### **1.2: Configure Munge Authentication**

```bash
# Fix the failed munge service
sudo systemctl stop munge
sudo rm -f /etc/munge/munge.key

# Create new munge key
sudo /usr/sbin/create-munge-key -f

# Set correct permissions
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key

# Start munge
sudo systemctl enable munge
sudo systemctl start munge

# Verify
systemctl status munge
# Should show: active (running)

# Test munge
munge -n | unmunge
# Should show: STATUS: Success
```
- [ ] 
### **1.3: Configure Slurm for Desktop-Only (Expandable)**

```bash
# Create Slurm user if doesn't exist
sudo useradd -r -s /bin/false slurm 2>/dev/null || true

# Create required directories
sudo mkdir -p /var/spool/slurm/slurmctld
sudo mkdir -p /var/spool/slurm/slurmd
sudo mkdir -p /var/log/slurm
sudo chown -R slurm:slurm /var/spool/slurm
sudo chown -R slurm:slurm /var/log/slurm

# Create Slurm configuration
sudo tee /etc/slurm/slurm.conf > /dev/null <<'EOF'
# Slurm Configuration - AI Cluster
ClusterName=ai-cluster
SlurmctldHost=desktop-gpu(192.168.1.10)

# Authentication
AuthType=auth/munge
CryptoType=crypto/munge

# Scheduling
SchedulerType=sched/backfill
SelectType=select/cons_tres
SelectTypeParameters=CR_Core_Memory

# Logging
SlurmctldDebug=info
SlurmctldLogFile=/var/log/slurm/slurmctld.log
SlurmdDebug=info
SlurmdLogFile=/var/log/slurm/slurmd.log

# State preservation
StateSaveLocation=/var/spool/slurm/slurmctld
SlurmdSpoolDir=/var/spool/slurm/slurmd

# Process tracking
ProctrackType=proctrack/cgroup
TaskPlugin=task/affinity,task/cgroup

# Accounting
AccountingStorageType=accounting_storage/none
JobAcctGatherType=jobacct_gather/linux
JobAcctGatherFrequency=30

# Timeouts
SlurmctldTimeout=300
SlurmdTimeout=300
InactiveLimit=0
MinJobAge=300
KillWait=30
Waittime=0

# Node Definitions
NodeName=desktop-gpu Gres=gpu:rtx5070ti:1 CPUs=24 Sockets=1 CoresPerSocket=12 ThreadsPerCore=2 RealMemory=118000 State=UNKNOWN

# Partition Definitions
PartitionName=gpu Nodes=desktop-gpu Default=YES MaxTime=INFINITE State=UP Priority=10
PartitionName=all Nodes=desktop-gpu MaxTime=INFINITE State=UP Priority=1

# GPU Resource Scheduling
GresTypes=gpu
EOF

# Create GRES configuration for GPU
sudo tee /etc/slurm/gres.conf > /dev/null <<'EOF'
# GPU Resource Configuration
NodeName=desktop-gpu Name=gpu Type=rtx5070ti File=/dev/nvidia0 CPUs=0-23
EOF

# Create cgroup configuration
sudo tee /etc/slurm/cgroup.conf > /dev/null <<'EOF'
CgroupAutomount=yes
ConstrainCores=yes
ConstrainRAMSpace=yes
ConstrainDevices=yes
EOF
```
- [ ] 
### **1.4: Start Slurm Services**

```bash
# Start controller (this node is both controller and compute)
sudo systemctl enable slurmctld
sudo systemctl start slurmctld

# Start compute daemon
sudo systemctl enable slurmd
sudo systemctl start slurmd

# Verify services
systemctl status slurmctld
systemctl status slurmd

# Check Slurm status
sinfo
# Should show:
# PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
# gpu*         up   infinite      1   idle desktop-gpu

scontrol show node desktop-gpu
# Should show: State=IDLE, Gres=gpu:rtx5070ti:1
```
- [ ] 
### **1.5: Test GPU Job Submission**

```bash
# Create test directory
mkdir -p ~/slurm-tests

# Create GPU test script
cat > ~/slurm-tests/gpu-test.sh <<'EOF'
#!/bin/bash
#SBATCH --job-name=gpu-test
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=4
#SBATCH --mem=8G
#SBATCH --time=00:05:00
#SBATCH --output=%x-%j.out

echo "=== GPU Test Job ==="
echo "Node: $(hostname)"
echo "Date: $(date)"
echo ""

echo "=== NVIDIA GPU Info ==="
nvidia-smi

echo ""
echo "=== CUDA Test ==="
cat > /tmp/cuda_test.cu <<'CUDA'
#include <stdio.h>

__global__ void hello() {
    printf("Hello from GPU thread %d!\n", threadIdx.x);
}

int main() {
    hello<<<1, 10>>>();
    cudaDeviceSynchronize();
    printf("CUDA Test Complete!\n");
    return 0;
}
CUDA

nvcc /tmp/cuda_test.cu -o /tmp/cuda_test
/tmp/cuda_test

echo ""
echo "=== Test Complete ==="
EOF

chmod +x ~/slurm-tests/gpu-test.sh

# Submit test job
cd ~/slurm-tests
sbatch gpu-test.sh

# Check queue
squeue

# Wait ~30 seconds, then check output
ls -lh gpu-test-*.out
cat gpu-test-*.out
```
- [ ] 
**Expected Output**: Job completes, nvidia-smi shows RTX 5070 Ti, CUDA test prints "Hello from GPU thread" messages.
- [ ] 
---

## **PHASE 2: AI TOP UTILITY & RYZENADJ INTEGRATION**

**Duration**: 30 minutes | **Dependencies**: Phase 1 complete

### **2.1: Verify AI TOP Utility**

```bash
# Check installation
ls -lh /opt/gigabyte-ai-top-utility/

# Launch GUI to verify functionality
/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility &

# If it launches successfully, close it (we'll script it later)
```
- [ ] 
### **2.2: Create AI TOP CLI Wrapper**

```bash
# Create wrapper script for automation
sudo tee /usr/local/bin/ai-top-profile <<'EOF'
#!/bin/bash
# AI TOP Profile Switcher
# Usage: ai-top-profile [performance|balanced|quiet]

PROFILE=${1:-balanced}
AI_TOP_PATH="/opt/gigabyte-ai-top-utility"

case $PROFILE in
    performance)
        echo "Activating Performance Mode..."
        # Note: AI TOP Utility may not have CLI - this is placeholder
        # Actual implementation depends on utility's capabilities
        ;;
    balanced)
        echo "Activating Balanced Mode..."
        ;;
    quiet)
        echo "Activating Quiet Mode..."
        ;;
    *)
        echo "Usage: $0 [performance|balanced|quiet]"
        exit 1
        ;;
esac
EOF

sudo chmod +x /usr/local/bin/ai-top-profile
```
- [ ] 
### **2.3: Create RyzenAdj Slurm Integration**

```bash
# Create Slurm prolog script (runs before job starts)
sudo mkdir -p /etc/slurm/prolog.d

sudo tee /etc/slurm/prolog.d/01-ryzenadj-tune.sh > /dev/null <<'EOF'
#!/bin/bash
# Slurm Prolog: Apply CPU tuning based on job type

LOG_FILE="/var/log/slurm/ryzenadj-prolog.log"

# Log job start
echo "[$(date)] Job $SLURM_JOB_ID starting on partition $SLURM_JOB_PARTITION" >> $LOG_FILE

# Apply tuning based on partition
if [[ "$SLURM_JOB_PARTITION" == "gpu" ]]; then
    # GPU jobs: Boost CPU for data feeding
    /usr/local/bin/ryzenadj --stapm-limit=120000 --fast-limit=135000 --slow-limit=120000
    echo "[$(date)] Applied performance profile (120W TDP)" >> $LOG_FILE
else
    # Non-GPU jobs: Balanced profile
    /usr/local/bin/ryzenadj --stapm-limit=95000 --fast-limit=105000 --slow-limit=95000
    echo "[$(date)] Applied balanced profile (95W TDP)" >> $LOG_FILE
fi
EOF

sudo chmod +x /etc/slurm/prolog.d/01-ryzenadj-tune.sh

# Create Slurm epilog script (runs after job ends)
sudo mkdir -p /etc/slurm/epilog.d

sudo tee /etc/slurm/epilog.d/01-ryzenadj-reset.sh > /dev/null <<'EOF'
#!/bin/bash
# Slurm Epilog: Reset CPU to default state

LOG_FILE="/var/log/slurm/ryzenadj-epilog.log"

# Reset to balanced state
/usr/local/bin/ryzenadj --stapm-limit=95000 --fast-limit=105000 --slow-limit=95000

echo "[$(date)] Job $SLURM_JOB_ID completed, reset to balanced profile" >> $LOG_FILE
EOF

sudo chmod +x /etc/slurm/epilog.d/01-ryzenadj-reset.sh

# Update slurm.conf to enable prolog/epilog
sudo tee -a /etc/slurm/slurm.conf > /dev/null <<'EOF'

# Prolog/Epilog Scripts
Prolog=/etc/slurm/prolog.d/*
Epilog=/etc/slurm/epilog.d/*
EOF

# Restart Slurm to apply changes
sudo systemctl restart slurmctld
sudo systemctl restart slurmd

# Test
sbatch ~/slurm-tests/gpu-test.sh
# Check logs
tail -f /var/log/slurm/ryzenadj-prolog.log
```
- [ ] 
---

## **PHASE 3: TEMPORARY NFS STORAGE (DESKTOP-HOSTED)**

**Duration**: 20 minutes | **Rationale**: Bridge solution until UNAS-Pro 4 arrives

### **3.1: Setup NFS Server on Desktop**

```bash
# Install NFS server
sudo apt install -y nfs-kernel-server

# Create shared directories
sudo mkdir -p /srv/nfs/shared
sudo mkdir -p /srv/nfs/models
sudo mkdir -p /srv/nfs/datasets
sudo mkdir -p /srv/nfs/results
sudo mkdir -p /srv/nfs/obsidian-vault

# Set permissions (open for now, will tighten later)
sudo chown -R nobody:nogroup /srv/nfs
sudo chmod -R 777 /srv/nfs

# Configure NFS exports
sudo tee /etc/exports > /dev/null <<'EOF'
# Temporary NFS exports (will migrate to UNAS-Pro 4)
/srv/nfs/shared       192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)
/srv/nfs/models       192.168.1.0/24(ro,sync,no_subtree_check,no_root_squash)
/srv/nfs/datasets     192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)
/srv/nfs/results      192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)
/srv/nfs/obsidian-vault 192.168.1.0/24(rw,sync,no_subtree_check,no_root_squash)
EOF

# Export shares
sudo exportfs -arv

# Restart NFS server
sudo systemctl restart nfs-kernel-server

# Verify exports
showmount -e localhost
```

### **3.2: Mount NFS on Desktop (Self-Mount for Testing)**

```bash
# Create mount points
sudo mkdir -p /mnt/nfs/{shared,models,datasets,results,obsidian}

# Add to fstab for auto-mount
sudo tee -a /etc/fstab > /dev/null <<'EOF'

# Temporary NFS mounts (self-hosted, will migrate to UNAS-Pro 4)
192.168.1.10:/srv/nfs/shared            /mnt/nfs/shared         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/models            /mnt/nfs/models         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/datasets          /mnt/nfs/datasets       nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/results           /mnt/nfs/results        nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/obsidian-vault    /mnt/nfs/obsidian       nfs defaults,_netdev 0 0
EOF

# Mount all
sudo mount -a

# Verify
df -h | grep nfs
mount | grep nfs
```

---

## **PHASE 4: XPS 15 7590 - BARE METAL UBUNTU SERVER SETUP**

**Duration**: 60 minutes | **Node Role**: CPU Workhorse + MLOps Services

### **4.1: Create Ubuntu Server 24.04 LTS USB Installer**

**On Desktop**:

```bash
# Download Ubuntu Server 24.04 LTS
cd ~/Downloads
wget https://releases.ubuntu.com/24.04/ubuntu-24.04.1-live-server-amd64.iso

# Verify checksum
sha256sum ubuntu-24.04.1-live-server-amd64.iso
# Compare with: https://releases.ubuntu.com/24.04/SHA256SUMS

# Create bootable USB (replace /dev/sdX with your USB device)
# WARNING: This will erase the USB drive!
sudo dd if=ubuntu-24.04.1-live-server-amd64.iso of=/dev/sdX bs=4M status=progress conv=fsync
```

### **4.2: Install Ubuntu Server on XPS 15**

**Physical Steps**:

1. Insert USB into XPS 15
2. Boot from USB (F12 boot menu)
3. Select "Install Ubuntu Server"

**Installation Settings**:

- Language: English
- Keyboard: US
- Network: **USB-C to 2.5GbE adapter** - configure static IP:
    - IP: `192.168.1.20/24`
    - Gateway: `192.168.1.1`
    - DNS: `192.168.1.1, 8.8.8.8`
- Storage: **Use entire 1TB disk**
- Hostname: `xps15-mlops`
- Username: `aiops`
- Password: `[secure password - store in password manager]`
- SSH: **Enable OpenSSH server**
- Packages: Select "Docker" if offered, otherwise skip

**Complete installation, remove USB, reboot.**

### **4.3: Initial XPS 15 Configuration**

**SSH from Desktop**:

```bash
ssh aiops@192.168.1.20
```

**On XPS 15**:

```bash
# Update system
sudo apt update && sudo apt full-upgrade -y

# Install essentials
sudo apt install -y \
    build-essential git curl wget htop vim tmux \
    nfs-common python3-pip python3-venv \
    docker.io docker-compose

# Add user to docker group
sudo usermod -aG docker aiops
newgrp docker

# Test docker
docker run hello-world

# Mount NFS shares
sudo mkdir -p /mnt/nfs/{shared,models,datasets,results,obsidian}

sudo tee -a /etc/fstab > /dev/null <<'EOF'

# NFS mounts from desktop (will migrate to UNAS-Pro 4)
192.168.1.10:/srv/nfs/shared            /mnt/nfs/shared         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/models            /mnt/nfs/models         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/datasets          /mnt/nfs/datasets       nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/results           /mnt/nfs/results        nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/obsidian-vault    /mnt/nfs/obsidian       nfs defaults,_netdev 0 0
EOF

sudo mount -a

# Verify
df -h | grep nfs
```

### **4.4: Join XPS 15 to Slurm Cluster**

**On Desktop, copy munge key**:

```bash
scp /etc/munge/munge.key aiops@192.168.1.20:/tmp/
```

**On XPS 15**:

```bash
# Install Slurm client
sudo apt install -y slurm-wlm-basic-plugins slurm-client munge

# Configure munge
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl enable munge
sudo systemctl start munge

# Test munge
munge -n | unmunge

# Copy Slurm config from desktop
scp aiops@192.168.1.10:/etc/slurm/slurm.conf /tmp/
```

**On Desktop, update slurm.conf**:

```bash
sudo nano /etc/slurm/slurm.conf

# Add XPS 15 node definition before PartitionName lines:
NodeName=xps15-mlops CPUs=8 RealMemory=61440 State=UNKNOWN

# Update partition definitions:
PartitionName=cpu Nodes=xps15-mlops MaxTime=INFINITE State=UP Priority=5
PartitionName=all Nodes=desktop-gpu,xps15-mlops MaxTime=INFINITE State=UP Priority=1

# Restart Slurm
sudo systemctl restart slurmctld
```

**Back on XPS 15**:

```bash
# Copy updated config
scp aiops@192.168.1.10:/etc/slurm/slurm.conf /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/

# Create slurm directories
sudo mkdir -p /var/spool/slurm/slurmd
sudo mkdir -p /var/log/slurm
sudo chown -R slurm:slurm /var/spool/slurm
sudo chown -R slurm:slurm /var/log/slurm

# Start slurmd
sudo systemctl enable slurmd
sudo systemctl start slurmd
```

**Verify on Desktop**:

```bash
sinfo
# Should show both nodes:
# PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
# gpu*         up   infinite      1   idle desktop-gpu
# cpu          up   infinite      1   idle xps15-mlops
# all          up   infinite      2   idle desktop-gpu,xps15-mlops
```

---

## **PHASE 5: JETSON ORIN NANO SUPER - BARE METAL JETPACK SETUP**

**Duration**: 90 minutes | **Node Role**: Edge AI Inference

### **5.1: Flash JetPack 6.1 (Using NVIDIA SDK Manager)**

**Prerequisites**:

- XPS 15 will act as the flashing host
- Jetson Orin Nano Super Developer Kit
- USB-C cable (included with Jetson)
- Samsung 990 EVO Plus 1TB NVMe installed in Jetson

**On XPS 15**:

```bash
# Download SDK Manager
wget https://developer.nvidia.com/downloads/sdkmanager-2.3.0-11797_amd64.deb

# Install
sudo dpkg -i sdkmanager-2.3.0-11797_amd64.deb
sudo apt -f install

# Launch SDK Manager
sdkmanager
```

**SDK Manager Workflow**:

1. Login with NVIDIA Developer account
2. Select Product: **Jetson Orin Nano Super**
3. JetPack version: **6.1** (or latest stable)
4. Target Components: **All** (Jetson OS, CUDA, cuDNN, TensorRT)
5. Storage: **NVMe** (Samsung 990 EVO Plus)
6. OEM Configuration: **Runtime** (will configure manually after flash)

**Physical Steps**:

1. Connect Jetson to XPS 15 via USB-C
2. Put Jetson in Recovery Mode:
    - Power off Jetson
    - Press and hold RECOVERY button
    - Press POWER button (keep holding RECOVERY)
    - Release RECOVERY after 2 seconds
3. SDK Manager should detect Jetson
4. Start flashing process (~30-45 minutes)

### **5.2: Initial Jetson Configuration**

**After flash completes**:

1. Connect monitor, keyboard, mouse to Jetson
2. Connect ethernet cable (or use WiFi)
3. Complete Ubuntu setup:
    - Username: `jetson`
    - Password: `[secure password]`
    - Computer name: `jetson-edge`

**Configure static IP**:

```bash
# On Jetson
sudo nano /etc/netplan/01-netcfg.yaml
```

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.40/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [192.168.1.1, 8.8.8.8]
```

```bash
sudo netplan apply

# Verify
ip addr show eth0
ping -c 3 192.168.1.10
```

### **5.3: Configure Jetson for Slurm + Docker**

```bash
# Update system
sudo apt update && sudo apt full-upgrade -y

# Install NFS client
sudo apt install -y nfs-common

# Mount NFS shares
sudo mkdir -p /mnt/nfs/{shared,models,datasets,results}

sudo tee -a /etc/fstab > /dev/null <<'EOF'

# NFS mounts from desktop
192.168.1.10:/srv/nfs/shared            /mnt/nfs/shared         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/models            /mnt/nfs/models         nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/datasets          /mnt/nfs/datasets       nfs defaults,_netdev 0 0
192.168.1.10:/srv/nfs/results           /mnt/nfs/results        nfs defaults,_netdev 0 0
EOF

sudo mount -a

# Install jtop for monitoring
sudo pip3 install jetson-stats
sudo systemctl restart jtop.service

# Set power mode to maximum performance
sudo nvpmodel -m 0  # 25W MAXN mode
sudo jetson_clocks

# Make persistent
echo "@reboot /usr/bin/jetson_clocks" | sudo crontab -

# Verify Docker (pre-installed with JetPack)
docker --version

# Test GPU access
docker run --rm --runtime nvidia nvcr.io/nvidia/l4t-base:r36.4.0 nvidia-smi
```

### **5.4: Join Jetson to Slurm Cluster**

**On Desktop, copy munge key**:

```bash
scp /etc/munge/munge.key jetson@192.168.1.40:/tmp/
```

**On Jetson**:

```bash
# Install Slurm client
sudo apt install -y slurm-wlm-basic-plugins slurm-client munge

# Configure munge
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl enable munge
sudo systemctl start munge

# Copy Slurm config
scp jetson@192.168.1.10:/etc/slurm/slurm.conf /tmp/
```

**On Desktop, update slurm.conf**:

```bash
sudo nano /etc/slurm/slurm.conf

# Add Jetson node definition:
NodeName=jetson-edge Gres=gpu:orin:1 CPUs=6 RealMemory=7680 State=UNKNOWN

# Update gres.conf
sudo nano /etc/slurm/gres.conf
# Add:
NodeName=jetson-edge Name=gpu Type=orin File=/dev/nvidia0 CPUs=0-5

# Update partition definitions:
PartitionName=edge Nodes=jetson-edge MaxTime=INFINITE State=UP Priority=8
PartitionName=all Nodes=desktop-gpu,xps15-mlops,jetson-edge MaxTime=INFINITE State=UP Priority=1

# Restart Slurm
sudo systemctl restart slurmctld
sudo scontrol reconfigure
```

**Back on Jetson**:

```bash
# Copy updated config
scp jetson@192.168.1.10:/etc/slurm/{slurm.conf,gres.conf} /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/
sudo mv /tmp/gres.conf /etc/slurm/

# Create slurm directories
sudo mkdir -p /var/spool/slurm/slurmd /var/log/slurm
sudo chown -R slurm:slurm /var/spool/slurm /var/log/slurm

# Start slurmd
sudo systemctl enable slurmd
sudo systemctl start slurmd
```

**Verify on Desktop**:

```bash
sinfo
# Should show all 3 nodes

scontrol show node jetson-edge | grep Gres
# Should show: Gres=gpu:orin:1
```

---

## **PHASE 6: XPS 13 - WINDOWS 11 + WSL2 DUAL ROLE SETUP**

**Duration**: 45 minutes | **Node Role**: Windows Host + Portable Cluster Client
[[Phase 6 refined to use ARCH]] 
[[Phase 6 refined to use OMARCHY]] 
### **6.1: Windows 11 Configuration** (Already Installed)

**No reinstall needed** - XPS 13 stays on Windows 11 Home.

### **6.2: Install WSL2 with Ubuntu 24.04**

**Open PowerShell as Administrator on XPS 13**:

```powershell
# Enable WSL2
wsl --install

# Reboot when prompted
Restart-Computer
```

**After reboot, in PowerShell**:

```powershell
# Install Ubuntu 24.04
wsl --install -d Ubuntu-24.04

# Set Ubuntu as default
wsl --set-default Ubuntu-24.04

# Launch Ubuntu (creates user account)
wsl
```

**In WSL2 Ubuntu**:

- Username: `aiops`
- Password: `[secure password]`

### **6.3: Configure WSL2 for Cluster Access**

**In WSL2 Ubuntu on XPS 13**:

```bash
# Update system
sudo apt update && sudo apt full-upgrade -y

# Install essentials
sudo apt install -y \
    git curl wget vim tmux \
    nfs-common python3-pip python3-venv

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install Slurm client
sudo apt install -y slurm-client munge

# Copy munge key from desktop (via scp)
# First, get XPS 13's WSL2 IP:
ip addr show eth0 | grep "inet "

# From Desktop:
scp /etc/munge/munge.key aiops@<XPS13_WSL2_IP>:/tmp/
```

**Back in WSL2 on XPS 13**:

```bash
# Configure munge
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl enable munge
sudo systemctl start munge

# Copy Slurm config
scp aiops@192.168.1.10:/etc/slurm/slurm.conf /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/

# Test Slurm access
sinfo -s
squeue
```

### **6.4: Setup MicroK8s kubectl Access**

**On Desktop**:

```bash
# Export kubeconfig
microk8s config > ~/kubeconfig-xps13.yaml
```

**Copy to XPS 13 WSL2**:

```bash
# From Desktop
scp ~/kubeconfig-xps13.yaml aiops@<XPS13_WSL2_IP>:~/
```

**In WSL2 on XPS 13**:

```bash
# Setup kubectl config
mkdir -p ~/.kube
mv ~/kubeconfig-xps13.yaml ~/.kube/config

# Edit config to use correct server IP
sed -i 's|127.0.0.1|192.168.1.10|g' ~/.kube/config

# Test kubectl access
kubectl get nodes
kubectl get pods --all-namespaces
```

---

## **VALIDATION & SMOKE TESTS**

### **Test 1: Full Cluster Connectivity**

**On Desktop**:

```bash
# Verify all nodes reachable
ping -c 2 192.168.1.10  # Desktop (self)
ping -c 2 192.168.1.20  # XPS 15
ping -c 2 192.168.1.40  # Jetson
ping -c 2 192.168.1.30  # XPS 13 (if connected via 2.5GbE adapter)

# Verify Slurm sees all nodes
sinfo
# Expected:
# gpu*         up   infinite      1   idle desktop-gpu
# cpu          up   infinite      1   idle xps15-mlops
# edge         up   infinite      1   idle jetson-edge
# all          up   infinite      3   idle desktop-gpu,xps15-mlops,jetson-edge
```

### **Test 2: Multi-Node Job Submission**

```bash
# Submit GPU job to Desktop
sbatch --partition=gpu ~/slurm-tests/gpu-test.sh

# Submit CPU job to XPS 15
sbatch --partition=cpu --wrap="hostname && lscpu | grep 'Model name'"

# Submit Edge job to Jetson
sbatch --partition=edge --wrap="hostname && jtop --csv > /tmp/jtop.csv && head /tmp/jtop.csv"

# Monitor queue
watch squeue
```

### **Test 3: NFS Access from All Nodes**

```bash
# From Desktop
echo "Desktop: $(date)" > /mnt/nfs/shared/test-desktop.txt

# From XPS 15 (via SSH)
ssh aiops@192.168.1.20 "echo 'XPS15: $(date)' > /mnt/nfs/shared/test-xps15.txt"

# From Jetson (via SSH)
ssh jetson@192.168.1.40 "echo 'Jetson: $(date)' > /mnt/nfs/shared/test-jetson.txt"

# Verify all files visible on all nodes
ls -lh /mnt/nfs/shared/test-*.txt
```

---

## **MIGRATION PLAN FOR UNAS-PRO 4** (In 2 Months)

### **Preparation Checklist**:

```bash
# Create migration script (run when UNAS-Pro 4 arrives)
cat > ~/unas-migration.sh <<'EOF'
#!/bin/bash
# UNAS-Pro 4 Migration Script

UNAS_IP="192.168.1.50"  # Assign this to UNAS-Pro 4

echo "=== UNAS-Pro 4 Migration ==="
echo "1. Configure UNAS-Pro 4 with static IP: $UNAS_IP"
echo "2. Create ZFS pools on UNAS-Pro 4"
echo "3. Copy data from desktop NFS to UNAS:"
echo "   rsync -avP /srv/nfs/ root@$UNAS_IP:/mnt/tank/"
echo "4. Update /etc/fstab on all nodes:"
echo "   sed -i 's|192.168.1.10:/srv/nfs|$UNAS_IP:/mnt/tank|g' /etc/fstab"
echo "5. Remount all nodes: sudo umount /mnt/nfs/* && sudo mount -a"
echo "6. Disable NFS server on desktop: sudo systemctl disable nfs-kernel-server"
EOF

chmod +x ~/unas-migration.sh
```

---

## **FINAL SYSTEM TOPOLOGY**

```
┌─────────────────────────────────────────────────────────────┐
│                 192.168.1.0/24 Network (UniFi)              │
│                                                              │
│  ┌──────────────────┐  ┌──────────────────┐                │
│  │  Desktop GPU     │  │  XPS 15 MLOps    │                │
│  │  192.168.1.10    │  │  192.168.1.20    │                │
│  │  - RTX 5070 Ti   │  │  - i9 CPU        │                │
│  │  - Slurm Ctrl    │  │  - 64GB RAM      │                │
│  │  - MicroK8s      │  │  - Docker Host   │                │
│  │  - NFS Server*   │  │  - Slurm Compute │                │
│  └────────┬─────────┘  └────────┬─────────┘                │
│           │                     │                           │
│  ┌────────┴─────────────────────┴─────┐                    │
│  │                                     │                    │
│  │  ┌──────────────────┐  ┌──────────────────┐            │
│  │  │  Jetson Edge     │  │  XPS 13 Client   │            │
│  │  │  192.168.1.40    │  │  192.168.1.30    │            │
│  │  │  - 67 TOPS       │  │  - Windows 11    │            │
│  │  │  - 8GB RAM       │  │  - WSL2 Ubuntu   │            │
│  │  │  - Slurm Compute │  │  - kubectl       │            │
│  │  └──────────────────┘  └──────────────────┘            │
│                                                              │
│  * Will migrate to UNAS-Pro 4 (192.168.1.50) in 2 months   │
└─────────────────────────────────────────────────────────────┘
```

---

## **NEXT STEPS AFTER BASE DEPLOYMENT**

1. **Deploy NVIDIA NGC Containers** (NIM, NeMo, Triton)
2. **Setup HashiCorp Vault** (on XPS 15 via Docker)
3. **Deploy n8n Automation** (on XPS 15)
4. **Configure Obsidian Vault** (on NFS share)
5. **Implement PyG Knowledge Graph** (Phase 7)
6. **Deploy Monitoring Stack** (Prometheus/Grafana)

---

**Ready to proceed?** Start with **Phase 0** (network fix) and work sequentially through the phases. Each phase builds on the previous, and validation tests ensure stability before moving forward.

Would you like me to:

1. Generate automation scripts for any specific phase?
2. Create detailed troubleshooting guides for common issues?
3. Provide the advanced optimization configurations from my previous response once the base system is operational?