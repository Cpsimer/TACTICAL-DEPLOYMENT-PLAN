# Infrastructure Deployment Plan

## Executive Summary

This document provides a comprehensive, step-by-step deployment plan for the AI-accelerated computing infrastructure spanning:

- **Ubuntu Desktop** (Ryzen 9 9900X + RTX 5070 Ti): Primary GPU compute node
- **Dell XPS 15 7590**: CPU services node
- **Dell XPS 13**: Portable client
- **NVIDIA Jetson Orin Nano Super**: Edge AI node
- **WD NAS (2TB)**: TrueNAS-based storage + HashiCorp Vault security gateway

**Orchestration Strategy**: MicroK8s for container orchestration with Slurm overlay for GPU job scheduling and cross-node federation.

**Network Posture**: Leave existing UniFi 2.5G topology untouched; iterate infrastructure post-deployment.

---

## Phase 0: Pre-Deployment Checklist & System Validation

### 0.1 Validate Existing Services

**Objective**: Confirm baseline before changes to prevent cascading failures.

```bash
# Desktop (schlimers-server)
systemctl --failed                    # Identify 2 failed units from status dump
systemctl status docker --user        # Verify rootless Docker
microk8s status --wait-ready          # Confirm MicroK8s degraded state
nvidia-smi                            # Validate RTX 5070 Ti detection (Driver 580.95.05, CUDA 13.0.2)
/opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --version  # Confirm AI TOP 4.1.1
mount | grep nas                      # Check if NAS already mounted (expect none initially)
```

**Action Items**:

- [ ] Document failed systemd units: `systemctl list-units --failed > /tmp/failed-units-baseline.txt`
- [ ] Clear MicroK8s degraded state:

```bash
microk8s kubectl get pods --all-namespaces -o wide  # Identify stuck pods
microk8s reset  # Nuclear option if no critical workloads exist
# OR selectively repair:
microk8s kubectl delete pod <stuck-pod> -n <namespace> --force --grace-period=0
```

- [ ] Verify `pass` password store exists: `pass ls` (if missing, initialize: `pass init <gpg-key-id>`)

### 0.2 Hardware Inventory Confirmation

**Desktop Components** (cross-reference Exact Device Hardware Specs.md):

- CPU: AMD Ryzen 9 9900X (12C/24T, 120W TDP, Radeon iGPU)
- GPU: Gigabyte RTX 5070 Ti (16GB GDDR7, Blackwell arch)
- RAM: 128GB DDR5-6400 (4x32GB TeamGroup T-CREATE)
- Storage: Samsung 9100 PRO 2TB NVMe (PCIe Gen5 x4)
- Cooling: HYTE THICC Q60 240mm AIO
- Mobo: Gigabyte X870 AORUS ELITE WIFI7

**XPS 15 7590**:

- CPU: Intel Core (Model 158, ~2.4GHz)
- RAM: 64GB
- Storage: 1TB NVMe
- OS Target: Ubuntu Server 24.04 LTS (currently Windows 11)

**XPS 13**:

- CPU: Intel Core Ultra 7 258V (8C, 4.8GHz)
- iGPU: Intel Arc (~18GB shared VRAM)
- RAM: 32GB LPDDR5X-8533
- Storage: 512GB NVMe
- OS: Windows 11 Home (Copilot+ PC, keep for portable use)

**Jetson Orin Nano Super**:

- AI Performance: 67 TOPS
- GPU: NVIDIA Ampere (1024 CUDA, 32 Tensor cores)
- CPU: 6-core Arm Cortex-A78AE
- RAM: 8GB LPDDR5 (102GB/s)
- Storage: 32GB SD card + 1TB Samsung 990 EVO Plus NVMe

**WD NAS**:

- Capacity: 2TB
- Connection: USG Gateway (1GbE, ~280MB/s baseline)
- Target OS: TrueNAS Scale (latest stable)

---

## Phase 1: NAS Hardening with TrueNAS + HashiCorp Vault

### 1.1 Factory Reset & TrueNAS Installation

**Duration**: 2-3 hours  
**Prerequisites**: USB stick (8GB+) for TrueNAS installer, backup of any existing NAS data

#### Step 1.1.1: Download TrueNAS Scale

```bash
# On desktop or any workstation
wget https://download.truenas.com/TrueNAS-SCALE-Dragonfish/TrueNAS-SCALE-24.10.0.2/TrueNAS-SCALE-24.10.0.2.iso
# Verify checksum from https://www.truenas.com/download-truenas-scale/
sha256sum TrueNAS-SCALE-24.10.0.2.iso
```

#### Step 1.1.2: Create Bootable USB

```bash
# Linux method (replace /dev/sdX with actual USB device)
sudo dd if=TrueNAS-SCALE-24.10.0.2.iso of=/dev/sdX bs=4M status=progress conv=fsync
# OR use Balena Etcher for GUI approach
```

#### Step 1.1.3: Install TrueNAS on WD NAS

1. **Physical Setup**:
   - Power down WD NAS
   - Insert TrueNAS USB installer
   - Connect keyboard/monitor (if headless, use IPMI/iLO if available)
   - Boot from USB (F11/F12 boot menu)

2. **Installation Wizard**:
   - Select target disk (internal HDD in NAS)
   - Set root password (store in `pass insert truenas/root`)
   - Configure network: Static IP on UniFi LAN (e.g., `192.168.1.10/24`, gateway `192.168.1.1`)
   - Complete installation, reboot

3. **Initial Configuration**:
   - Access TrueNAS UI: `https://192.168.1.10`
   - Enable SSH: `System > Services > SSH` (disable password auth, use keys)
   - Create ZFS pool: `Storage > Create Pool` (name: `vault-pool`, RAIDZ1 or mirror depending on drive count)

### 1.2 Configure NFS/SMB Shares for Cluster Access

#### Step 1.2.1: Create Datasets

```bash
# Via TrueNAS UI: Storage > Pools > vault-pool > Add Dataset
# Datasets:
# - obsidian-vault (for Obsidian sync, NFS + SMB)
# - model-weights (NIM/NeMo models, NFS, read-only for clients)
# - redis-persistence (Redis snapshots, NFS)
# - mlflow-artifacts (MLflow storage, NFS)
# - slurm-shared (job scripts/logs, NFS)
```

#### Step 1.2.2: NFS Export Configuration

```bash
# TrueNAS UI: Shares > Unix (NFS) Shares > Add
# For each dataset, configure:
# - Path: /mnt/vault-pool/<dataset>
# - Authorized Networks: 192.168.1.0/24 (entire UniFi LAN)
# - Maproot User/Group: root (for now; refine later)
# - Enable: Yes
```

**Security Note**: For production, create dedicated NFS users and apply `mapall` instead of `maproot`. Document in README.md under "NAS Access".

#### Step 1.2.3: SSL/TLS for NFS (Optional but Recommended)

```bash
# Generate self-signed cert on TrueNAS
openssl req -x509 -nodes -days 365 -newkey rsa:4096 \
  -keyout /mnt/vault-pool/ssl/nfs-server.key \
  -out /mnt/vault-pool/ssl/nfs-server.crt \
  -subj "/CN=nas.local"

# Configure NFS to use Kerberos or stunnel (advanced, defer to Phase 2 if time-constrained)
```

### 1.3 Deploy HashiCorp Vault for Secrets Management

#### Step 1.3.1: Install Vault via Docker on TrueNAS

TrueNAS Scale supports Docker via its Apps ecosystem (based on Kubernetes). Alternative: run Vault in a jail or VM.

**Preferred Method**: Deploy Vault as TrueNAS app

```bash
# SSH into TrueNAS as root
ssh root@192.168.1.10

# Install Docker CLI (if not pre-installed)
apt update && apt install -y docker.io

# Pull Vault image (from NGC if available, else official)
docker pull hashicorp/vault:latest

# Create Vault config directory
mkdir -p /mnt/vault-pool/vault/{config,data,logs}

# Write Vault config
cat <<EOF > /mnt/vault-pool/vault/config/vault.hcl
storage "file" {
  path = "/vault/data"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  tls_disable = 0
  tls_cert_file = "/vault/ssl/vault.crt"
  tls_key_file  = "/vault/ssl/vault.key"
}

api_addr = "https://192.168.1.10:8200"
ui = true
EOF

# Generate Vault SSL cert
openssl req -x509 -nodes -days 730 -newkey rsa:4096 \
  -keyout /mnt/vault-pool/vault/ssl/vault.key \
  -out /mnt/vault-pool/vault/ssl/vault.crt \
  -subj "/CN=vault.local"

# Run Vault container
docker run -d \
  --name vault \
  --cap-add=IPC_LOCK \
  -p 8200:8200 \
  -v /mnt/vault-pool/vault/config:/vault/config:ro \
  -v /mnt/vault-pool/vault/data:/vault/data \
  -v /mnt/vault-pool/vault/logs:/vault/logs \
  -v /mnt/vault-pool/vault/ssl:/vault/ssl:ro \
  hashicorp/vault:latest server

# Initialize Vault (store unseal keys in `pass`)
docker exec -it vault vault operator init -key-shares=5 -key-threshold=3
# Output:
# Unseal Key 1: <key1>
# ...
# Initial Root Token: <token>

# Store in pass
pass insert -m vault/unseal-keys  # Paste all 5 keys
pass insert vault/root-token      # Paste root token

# Unseal Vault (repeat 3 times with different keys)
docker exec -it vault vault operator unseal <key1>
docker exec -it vault vault operator unseal <key2>
docker exec -it vault vault operator unseal <key3>
```

#### Step 1.3.2: Configure Vault for NGC API Keys & Cluster Secrets

```bash
# Export Vault address and token
export VAULT_ADDR='https://192.168.1.10:8200'
export VAULT_TOKEN=$(pass show vault/root-token)
export VAULT_SKIP_VERIFY=1  # Self-signed cert; fix with proper CA later

# Enable KV secrets engine v2
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault secrets enable -path=secret kv-v2

# Store NGC API key (from User special status.md memberships)
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault kv put secret/ngc api_key="<YOUR_NGC_API_KEY>"

# Store Docker registry credentials
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault kv put secret/docker/registry \
  username="<NGC_USERNAME>" \
  password="<NGC_API_KEY>"

# Store database credentials (for postgres_mlops)
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault kv put secret/postgres \
  username="mlops" \
  password="$(openssl rand -base64 32)"

# Store n8n webhook tokens
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault kv put secret/n8n \
  webhook_token="$(openssl rand -hex 32)"

# Create AppRole for Slurm jobs to fetch secrets
docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault auth enable approle

docker exec -e VAULT_TOKEN=$VAULT_TOKEN vault \
  vault write auth/approle/role/slurm-jobs \
  token_ttl=1h \
  token_max_ttl=4h \
  policies="slurm-secrets"

# Create policy for Slurm
cat <<EOF | docker exec -i -e VAULT_TOKEN=$VAULT_TOKEN vault vault policy write slurm-secrets -
path "secret/data/ngc" {
  capabilities = ["read"]
}
path "secret/data/docker/*" {
  capabilities = ["read"]
}
path "secret/data/postgres" {
  capabilities = ["read"]
}
EOF
```

#### Step 1.3.3: Automate Vault Unsealing (systemd on TrueNAS)

```bash
# Create systemd service for auto-unseal on boot
cat <<EOF > /etc/systemd/system/vault-unseal.service
[Unit]
Description=Unseal HashiCorp Vault
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
ExecStart=/usr/bin/bash -c 'for key in \$(pass show vault/unseal-keys | head -3); do docker exec vault vault operator unseal \$key; done'
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable vault-unseal.service
```

### 1.4 Validation & Backup

```bash
# Test NFS mounts from desktop
sudo mount -t nfs 192.168.1.10:/mnt/vault-pool/obsidian-vault /mnt/nas/obsidian
ls /mnt/nas/obsidian  # Should succeed

# Test Vault secret retrieval
curl --cacert /mnt/vault-pool/vault/ssl/vault.crt \
  -H "X-Vault-Token: $VAULT_TOKEN" \
  https://192.168.1.10:8200/v1/secret/data/ngc

# Backup Vault data (schedule weekly cron)
tar -czf /mnt/vault-pool/backups/vault-backup-$(date +%F).tar.gz \
  /mnt/vault-pool/vault/data
```

**Acceptance Criteria**:

- [ ] TrueNAS UI accessible at `https://192.168.1.10`
- [ ] NFS shares mountable from desktop: `mount | grep 192.168.1.10`
- [ ] Vault UI accessible at `https://192.168.1.10:8200`
- [ ] Secrets retrievable via Vault CLI/API
- [ ] Auto-unseal works after NAS reboot

---

## Phase 2: Ubuntu Desktop Cluster Configuration

### 2.1 Fix MicroK8s Degraded State

```bash
# Desktop (schlimers-server)
microk8s inspect  # Generate diagnostic report
microk8s kubectl get nodes  # Expect 1 node (self)

# If degraded, reset and reinstall
microk8s reset --destroy-storage
sudo snap refresh microk8s --channel=1.31/stable  # Latest stable

# Re-enable addons
microk8s enable dns storage ingress metrics-server

# Validate
microk8s status --wait-ready
microk8s kubectl get pods -A  # All pods should be Running
```

### 2.2 Install Slurm for GPU Job Scheduling

**Objective**: Layer Slurm atop MicroK8s to manage GPU workloads across desktop/XPS/Jetson.

#### Step 2.2.1: Install Slurm Packages

```bash
# Desktop
sudo apt update
sudo apt install -y slurm-wlm slurm-client munge

# Configure Munge (auth daemon for Slurm)
sudo systemctl enable munge
sudo create-munge-key  # Or: sudo dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl start munge
```

#### Step 2.2.2: Configure Slurm Controller (Desktop as slurmctld)

```bash
# Generate slurm.conf
sudo nano /etc/slurm/slurm.conf
```

**Sample `slurm.conf`** (adapt to your hostnames/IPs):

```ini
ClusterName=ai-cluster
SlurmctldHost=schlimers-server(192.168.1.20)  # Desktop IP
MpiDefault=none
ProctrackType=proctrack/cgroup
ReturnToService=2
SlurmctldPidFile=/var/run/slurmctld.pid
SlurmctldPort=6817
SlurmdPidFile=/var/run/slurmd.pid
SlurmdPort=6818
SlurmdSpoolDir=/var/spool/slurmd
SlurmUser=slurm
StateSaveLocation=/var/spool/slurmctld
SwitchType=switch/none
TaskPlugin=task/affinity,task/cgroup

# TIMERS
InactiveLimit=0
KillWait=30
MinJobAge=300
SlurmctldTimeout=120
SlurmdTimeout=300
Waittime=0

# SCHEDULING
SchedulerType=sched/backfill
SelectType=select/cons_tres
SelectTypeParameters=CR_Core_Memory

# LOGGING
SlurmctldDebug=info
SlurmctldLogFile=/var/log/slurm/slurmctld.log
SlurmdDebug=info
SlurmdLogFile=/var/log/slurm/slurmd.log

# ACCOUNTING
JobAcctGatherType=jobacct_gather/linux
JobAcctGatherFrequency=30
AccountingStorageType=accounting_storage/slurmdbd
AccountingStorageHost=schlimers-server
AccountingStorageTRES=gres/gpu

# COMPUTE NODES
NodeName=desktop-gpu Gres=gpu:rtx5070ti:1 CPUs=24 RealMemory=122880 State=UNKNOWN
NodeName=xps15-cpu CPUs=8 RealMemory=61440 State=UNKNOWN
NodeName=jetson-edge Gres=gpu:orin:1 CPUs=6 RealMemory=7680 State=UNKNOWN

# PARTITIONS
PartitionName=gpu Nodes=desktop-gpu Default=YES MaxTime=INFINITE State=UP
PartitionName=cpu Nodes=xps15-cpu MaxTime=INFINITE State=UP
PartitionName=edge Nodes=jetson-edge MaxTime=INFINITE State=UP

# GRES (GPU resources)
GresTypes=gpu
```

#### Step 2.2.3: Configure GRES (GPU Resource Spec)

```bash
sudo nano /etc/slurm/gres.conf
```

```ini
# Desktop RTX 5070 Ti
NodeName=desktop-gpu Name=gpu Type=rtx5070ti File=/dev/nvidia0 CPUs=0-23

# Jetson Orin (will configure in Phase 5)
NodeName=jetson-edge Name=gpu Type=orin File=/dev/nvidia0 CPUs=0-5
```

#### Step 2.2.4: Start Slurm Services

```bash
# Create slurm user if missing
sudo useradd -r -s /bin/false slurm

# Create log/spool directories
sudo mkdir -p /var/log/slurm /var/spool/slurmctld /var/spool/slurmd
sudo chown slurm:slurm /var/log/slurm /var/spool/slurmctld /var/spool/slurmd

# Start controller
sudo systemctl enable slurmctld
sudo systemctl start slurmctld
sudo systemctl status slurmctld

# Start compute daemon (desktop acts as compute node too)
sudo systemctl enable slurmd
sudo systemctl start slurmd
sudo systemctl status slurmd
```

#### Step 2.2.5: Validate Slurm

```bash
sinfo  # Should show partitions (gpu, cpu, edge) with nodes in IDLE state
scontrol show node desktop-gpu  # Verify Gres=gpu:rtx5070ti:1
```

### 2.3 Integrate Gigabyte AI TOP Utility with Slurm

**Objective**: Expose telemetry and automate thermal profiles via Slurm prolog/epilog hooks.

#### Step 2.3.1: Script AI TOP Profile Switching

```bash
# Create Slurm prolog script (runs before job starts)
sudo nano /etc/slurm/prolog.d/01-ai-top-performance.sh
```

```bash
#!/bin/bash
# Activate performance mode for GPU jobs
if [[ "$SLURM_JOB_PARTITION" == "gpu" ]]; then
  /opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --set-profile performance
  echo "$(date): Activated AI TOP performance mode for job $SLURM_JOB_ID" >> /var/log/slurm/ai-top.log
fi
```

```bash
# Create Slurm epilog script (runs after job ends)
sudo nano /etc/slurm/epilog.d/01-ai-top-normal.sh
```

```bash
#!/bin/bash
# Revert to balanced mode after GPU job
if [[ "$SLURM_JOB_PARTITION" == "gpu" ]]; then
  /opt/gigabyte-ai-top-utility/gigabyte-ai-top-utility --set-profile balanced
  echo "$(date): Reverted AI TOP to balanced mode after job $SLURM_JOB_ID" >> /var/log/slurm/ai-top.log
fi
```

```bash
# Make executable
sudo chmod +x /etc/slurm/{prolog,epilog}.d/*.sh

# Update slurm.conf to use hooks
sudo nano /etc/slurm/slurm.conf
# Add:
# Prolog=/etc/slurm/prolog.d/*
# Epilog=/etc/slurm/epilog.d/*

# Reload Slurm
sudo scontrol reconfigure
```

#### Step 2.3.2: Integrate ryzenadj for CPU Tuning

```bash
# Install ryzenadj (AMD CPU tuning tool)
git clone https://github.com/FlyGoat/RyzenAdj.git /opt/ryzenadj
cd /opt/ryzenadj
mkdir build && cd build
cmake ..
make
sudo make install

# Test
sudo ryzenadj --info

# Add to prolog for CPU-intensive jobs
sudo nano /etc/slurm/prolog.d/02-ryzenadj-tune.sh
```

```bash
#!/bin/bash
# Apply PBO undervolt for efficiency
if [[ "$SLURM_JOB_PARTITION" == "cpu" ]]; then
  sudo /usr/local/bin/ryzenadj --tctl-temp=85 --stapm-limit=95000 --fast-limit=105000
  echo "$(date): Applied ryzenadj tuning for job $SLURM_JOB_ID" >> /var/log/slurm/ryzenadj.log
fi
```

```bash
sudo chmod +x /etc/slurm/prolog.d/02-ryzenadj-tune.sh
```

### 2.4 Mount NAS Shares Cluster-Wide

```bash
# Desktop (add to /etc/fstab)
echo "192.168.1.10:/mnt/vault-pool/obsidian-vault /mnt/nas/obsidian nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "192.168.1.10:/mnt/vault-pool/model-weights /mnt/nas/models nfs ro,defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "192.168.1.10:/mnt/vault-pool/slurm-shared /mnt/nas/slurm nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab

# Create mount points
sudo mkdir -p /mnt/nas/{obsidian,models,slurm}

# Mount all
sudo mount -a
mount | grep 192.168.1.10  # Verify 3 NFS mounts
```

### 2.5 Configure Docker for NGC Container Registry

```bash
# Login to NGC (use Vault-stored credentials)
export NGC_API_KEY=$(docker exec vault vault kv get -field=api_key secret/ngc)
echo $NGC_API_KEY | docker login nvcr.io --username='$oauthtoken' --password-stdin

# Pull base images
docker pull nvcr.io/nvidia/pytorch:24.10-py3
docker pull nvcr.io/nvidia/tritonserver:24.10-py3
docker pull nvcr.io/nvidia/nemo:24.09

# Tag and push to local registry (if setting up harbor on NAS later)
# For now, cache locally
```

### 2.6 Validation

```bash
# Test Slurm job submission
cat <<EOF > /tmp/test-gpu.sh
#!/bin/bash
#SBATCH --job-name=test-gpu
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --time=00:05:00
#SBATCH --output=/mnt/nas/slurm/logs/%j-test-gpu.out

nvidia-smi
python3 -c "import torch; print(torch.cuda.is_available())"
EOF

sbatch /tmp/test-gpu.sh
squeue  # Check job status
cat /mnt/nas/slurm/logs/<jobid>-test-gpu.out  # Verify GPU detected
```

**Acceptance Criteria**:

- [ ] MicroK8s healthy: `microk8s status`
- [ ] Slurm operational: `sinfo` shows all partitions
- [ ] NAS mounts present: `df -h | grep 192.168.1.10`
- [ ] Docker logged into NGC: `docker info | grep nvcr`
- [ ] Test GPU job completes: `sacct -j <jobid>` shows COMPLETED

---

## Phase 3: XPS 15 7590 - CPU Services Node

### 3.1 Install Ubuntu Server 24.04 LTS

**Prerequisite**: Backup any Windows data, create Ubuntu USB installer.

1. Boot from USB installer
2. Select "Install Ubuntu Server"
3. Network config: Use USB-C to 2.5GbE adapter, static IP `192.168.1.21`
4. Partition: Use entire 1TB disk, ext4 filesystem
5. Create user: `mlops` (password in `pass insert xps15/mlops-password`)
6. Install OpenSSH server
7. Complete installation, reboot

### 3.2 Post-Install Configuration

```bash
# SSH from desktop to XPS 15
ssh mlops@192.168.1.21

# Update system
sudo apt update && sudo apt full-upgrade -y

# Install essential packages
sudo apt install -y build-essential git curl wget htop tmux vim net-tools

# Configure NTP (chrony already installed)
sudo systemctl enable chronyd
sudo systemctl start chronyd
```

### 3.3 Join Slurm Cluster as Compute Node

```bash
# Install Slurm client + munge
sudo apt install -y slurm-wlm-basic-plugins slurm-client munge

# Copy munge key from desktop
scp root@192.168.1.20:/etc/munge/munge.key /tmp/
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl restart munge

# Copy slurm.conf from desktop
scp root@192.168.1.20:/etc/slurm/slurm.conf /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/

# Update hostname in slurm.conf
sudo sed -i 's/NodeName=xps15-cpu/NodeName=xps15-cpu NodeAddr=192.168.1.21/' /etc/slurm/slurm.conf

# Start slurmd
sudo systemctl enable slurmd
sudo systemctl start slurmd

# Verify from desktop
# Desktop: sinfo
# Should show xps15-cpu in IDLE state
```

### 3.4 Mount NAS Shares

```bash
# XPS 15
sudo mkdir -p /mnt/nas/{obsidian,models,slurm}
echo "192.168.1.10:/mnt/vault-pool/obsidian-vault /mnt/nas/obsidian nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "192.168.1.10:/mnt/vault-pool/model-weights /mnt/nas/models nfs ro,defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "192.168.1.10:/mnt/vault-pool/slurm-shared /mnt/nas/slurm nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
sudo mount -a
```

### 3.5 Deploy CPU Services (n8n, PostgreSQL, MLflow)

#### Step 3.5.1: Install Docker

```bash
# XPS 15
sudo apt install -y docker.io docker-compose
sudo usermod -aG docker mlops
newgrp docker  # Refresh group membership
```

#### Step 3.5.2: Deploy PostgreSQL (MLOps Database)

```bash
# Fetch credentials from Vault
export VAULT_ADDR='https://192.168.1.10:8200'
export VAULT_TOKEN=$(pass show vault/root-token)
export VAULT_SKIP_VERIFY=1

POSTGRES_PASSWORD=$(curl -H "X-Vault-Token: $VAULT_TOKEN" \
  $VAULT_ADDR/v1/secret/data/postgres | jq -r '.data.data.password')

# Create docker-compose.yml
mkdir -p ~/services/postgres
cat <<EOF > ~/services/postgres/docker-compose.yml
version: '3.8'
services:
  postgres:
    image: postgres:16
    container_name: postgres-mlops
    restart: unless-stopped
    environment:
      POSTGRES_USER: mlops
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: mlflow
    volumes:
      - /mnt/nas/postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - mlops-net

networks:
  mlops-net:
    driver: bridge
EOF

# Start PostgreSQL
cd ~/services/postgres
docker-compose up -d
docker logs postgres-mlops  # Verify startup
```

#### Step 3.5.3: Deploy MLflow Tracking Server

```bash
mkdir -p ~/services/mlflow
cat <<EOF > ~/services/mlflow/docker-compose.yml
version: '3.8'
services:
  mlflow:
    image: python:3.11-slim
    container_name: mlflow-server
    restart: unless-stopped
    command: >
      bash -c "pip install mlflow psycopg2-binary &&
      mlflow server --backend-store-uri postgresql://mlops:${POSTGRES_PASSWORD}@postgres-mlops:5432/mlflow
      --default-artifact-root /artifacts --host 0.0.0.0 --port 5000"
    volumes:
      - /mnt/nas/mlflow-artifacts:/artifacts
    ports:
      - "5001:5000"
    networks:
      - mlops-net
    depends_on:
      - postgres

networks:
  mlops-net:
    external: true
EOF

cd ~/services/mlflow
docker-compose up -d
```

#### Step 3.5.4: Deploy n8n Automation

```bash
mkdir -p ~/services/n8n
N8N_WEBHOOK_TOKEN=$(curl -H "X-Vault-Token: $VAULT_TOKEN" \
  $VAULT_ADDR/v1/secret/data/n8n | jq -r '.data.data.webhook_token')

cat <<EOF > ~/services/n8n/docker-compose.yml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n-automation
    restart: unless-stopped
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=${N8N_WEBHOOK_TOKEN}
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - WEBHOOK_URL=http://192.168.1.21:5678/
    volumes:
      - /mnt/nas/n8n-data:/home/node/.n8n
    ports:
      - "5678:5678"
    networks:
      - mlops-net

networks:
  mlops-net:
    external: true
EOF

cd ~/services/n8n
docker-compose up -d
```

### 3.6 Validation

```bash
# Test PostgreSQL connection
docker exec postgres-mlops psql -U mlops -d mlflow -c "SELECT version();"

# Access MLflow UI from desktop browser
# http://192.168.1.21:5001

# Access n8n UI
# http://192.168.1.21:5678 (user: admin, password from Vault)

# Test CPU job on XPS 15
cat <<EOF > /tmp/test-cpu.sh
#!/bin/bash
#SBATCH --job-name=test-cpu
#SBATCH --partition=cpu
#SBATCH --cpus-per-task=8
#SBATCH --time=00:05:00
#SBATCH --output=/mnt/nas/slurm/logs/%j-test-cpu.out

lscpu
python3 -c "import multiprocessing; print(f'CPUs: {multiprocessing.cpu_count()}')"
EOF

sbatch /tmp/test-cpu.sh  # Submit from desktop
```

**Acceptance Criteria**:

- [ ] XPS 15 shows in `sinfo` as xps15-cpu (IDLE)
- [ ] PostgreSQL accessible: `psql -h 192.168.1.21 -U mlops -d mlflow`
- [ ] MLflow UI loads at `http://192.168.1.21:5001`
- [ ] n8n UI loads at `http://192.168.1.21:5678`

---

## Phase 4: Desktop GPU Services Deployment

### 4.1 Deploy NIM Inference Server (LLAMA 3.1 8B)

```bash
# Desktop
mkdir -p ~/containers/nim-llama
cd ~/containers/nim-llama

# Fetch NGC API key from Vault
export NGC_API_KEY=$(docker exec vault vault kv get -field=api_key secret/ngc)

# Create Slurm job script
cat <<EOF > nim-llama-deploy.sh
#!/bin/bash
#SBATCH --job-name=nim-llama
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --time=INFINITE
#SBATCH --output=/mnt/nas/slurm/logs/%j-nim-llama.out

docker run -d --name nim-llama-8b \
  --gpus all \
  -p 8000:8000 \
  -v /mnt/nas/models/llama-3.1-8b:/models \
  -e NGC_API_KEY=${NGC_API_KEY} \
  nvcr.io/nvidia/nim:llama-3.1-8b-instruct

# Keep job alive
tail -f /var/log/slurm/slurmd.log
EOF

# Submit as long-running service job
sbatch nim-llama-deploy.sh
```

### 4.2 Deploy Triton Inference Server

```bash
mkdir -p ~/containers/triton
cd ~/containers/triton

cat <<EOF > triton-deploy.sh
#!/bin/bash
#SBATCH --job-name=triton-server
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --time=INFINITE
#SBATCH --output=/mnt/nas/slurm/logs/%j-triton.out

docker run -d --name triton-server \
  --gpus all \
  -p 8001:8001 -p 8002:8002 -p 8003:8003 \
  -v /mnt/nas/models/triton-models:/models \
  nvcr.io/nvidia/tritonserver:24.10-py3 \
  tritonserver --model-repository=/models

tail -f /var/log/slurm/slurmd.log
EOF

sbatch triton-deploy.sh
```

### 4.3 Configure Redis Cache

```bash
mkdir -p ~/containers/redis
cd ~/containers/redis

docker run -d --name redis-cache \
  -p 6379:6379 \
  -v /mnt/nas/redis-persistence:/data \
  redis:7-alpine redis-server --appendonly yes
```

### 4.4 Validation

```bash
# Test NIM inference
curl -X POST http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Explain Slurm in one sentence.", "max_tokens": 50}'

# Test Triton health
curl http://localhost:8001/v2/health/ready

# Test Redis
redis-cli ping  # Should return PONG
```

**Acceptance Criteria**:

- [ ] NIM responds to curl requests: `curl localhost:8000/v1/models`
- [ ] Triton ready: `curl localhost:8001/v2/health/ready` returns 200
- [ ] Redis operational: `redis-cli info server`

---

## Phase 5: NVIDIA Jetson Orin Nano Super Setup

### 5.1 Flash JetPack 6.1 (Jetson Linux 38.2.1)

**Prerequisites**: Host PC with Ubuntu (can use desktop), USB-C cable, Jetson in recovery mode.

#### Step 5.1.1: Download SDK Manager (on Desktop)

```bash
# Download from NVIDIA Developer portal (requires login)
wget https://developer.nvidia.com/downloads/sdkmanager-2.3.0-11797_amd64.deb
sudo dpkg -i sdkmanager-2.3.0-11797_amd64.deb
sudo apt install -f  # Fix dependencies
```

#### Step 5.1.2: Flash Jetson

1. Connect Jetson to desktop via USB-C
2. Put Jetson in recovery mode:
   - Power off Jetson
   - Press and hold RECOVERY button
   - Press and release POWER button
   - Release RECOVERY after 2 seconds
3. Launch SDK Manager: `sdkmanager`
4. Select:
   - Product: Jetson Orin Nano Super
   - JetPack version: 6.1 (L4T 38.2.1)
   - Target components: All (Jetson OS, CUDA, cuDNN, TensorRT)
5. Flash to NVMe (select Samsung 990 EVO Plus as target)
6. Set username: `jetson`, password in `pass insert jetson/jetson-password`
7. Complete flashing (~30 minutes)

#### Step 5.1.3: Initial Jetson Configuration

```bash
# SSH into Jetson (get IP from router or serial console)
ssh jetson@192.168.1.22

# Update system
sudo apt update && sudo apt full-upgrade -y

# Install jtop (Jetson monitoring tool)
sudo -H pip3 install jetson-stats
sudo systemctl restart jtop.service
jtop  # Verify installation

# Configure static IP
sudo nano /etc/netplan/01-netcfg.yaml
```

```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.22/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```

```bash
sudo netplan apply
```

### 5.2 Install Docker and NGC Containers

```bash
# Docker pre-installed on JetPack, verify
docker --version
sudo usermod -aG docker jetson
newgrp docker

# Login to NGC
export NGC_API_KEY=$(pass show vault/root-token | xargs docker exec vault vault kv get -field=api_key secret/ngc)
echo $NGC_API_KEY | docker login nvcr.io --username='$oauthtoken' --password-stdin

# Pull Jetson-compatible containers (ARM64)
docker pull nvcr.io/nvidia/l4t-pytorch:r38.2.0-pth2.5-py3
docker pull nvcr.io/nvidia/l4t-tensorrt:r38.2.0-runtime

# Test PyTorch + CUDA
docker run --rm --runtime nvidia nvcr.io/nvidia/l4t-pytorch:r38.2.0-pth2.5-py3 \
  python3 -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}'); print(f'GPU: {torch.cuda.get_device_name(0)}')"
```

### 5.3 Join Slurm Cluster

```bash
# Install Slurm + munge
sudo apt install -y slurm-wlm-basic-plugins slurm-client munge

# Copy munge key from desktop
scp root@192.168.1.20:/etc/munge/munge.key /tmp/
sudo mv /tmp/munge.key /etc/munge/
sudo chown munge:munge /etc/munge/munge.key
sudo chmod 400 /etc/munge/munge.key
sudo systemctl restart munge

# Copy slurm.conf
scp root@192.168.1.20:/etc/slurm/slurm.conf /tmp/
sudo mv /tmp/slurm.conf /etc/slurm/

# Update gres.conf for Jetson GPU
sudo nano /etc/slurm/gres.conf
# Add:
# NodeName=jetson-edge Name=gpu Type=orin File=/dev/nvidia0 CPUs=0-5

# Start slurmd
sudo systemctl enable slurmd
sudo systemctl start slurmd
```

### 5.4 Mount NAS Shares

```bash
sudo apt install -y nfs-common
sudo mkdir -p /mnt/nas/{models,slurm}
echo "192.168.1.10:/mnt/vault-pool/model-weights /mnt/nas/models nfs ro,defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "192.168.1.10:/mnt/vault-pool/slurm-shared /mnt/nas/slurm nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
sudo mount -a
```

### 5.5 Optimize Jetson for AI Workloads

```bash
# Set max performance mode (NVP model 0 = maxn)
sudo nvpmodel -m 0
sudo jetson_clocks  # Max clocks

# Automate on boot
echo "@reboot /usr/bin/jetson_clocks" | sudo crontab -

# Verify power mode
sudo nvpmodel -q  # Should show mode 0 (25W)
```

### 5.6 Validation

```bash
# From desktop, check Jetson in cluster
sinfo  # Should show jetson-edge in IDLE state

# Submit test job to Jetson
cat <<EOF > /tmp/test-jetson.sh
#!/bin/bash
#SBATCH --job-name=test-jetson
#SBATCH --partition=edge
#SBATCH --gres=gpu:orin:1
#SBATCH --time=00:05:00
#SBATCH --output=/mnt/nas/slurm/logs/%j-test-jetson.out

jtop --csv > /tmp/jtop-stats.csv &
sleep 10
pkill jtop

docker run --rm --runtime nvidia nvcr.io/nvidia/l4t-pytorch:r38.2.0-pth2.5-py3 \
  python3 -c "import torch; print(torch.rand(1000,1000).cuda().sum())"

cat /tmp/jtop-stats.csv
EOF

sbatch /tmp/test-jetson.sh
```

**Acceptance Criteria**:

- [ ] Jetson shows in `sinfo` as jetson-edge (IDLE)
- [ ] Docker runs GPU workloads: `docker run --rm --runtime nvidia nvcr.io/nvidia/l4t-base:r38.2.0 nvidia-smi`
- [ ] Test job completes on Jetson partition
- [ ] NAS mounts accessible: `df -h | grep 192.168.1.10`

---

## Phase 6: Obsidian Integration & Automation

### 6.1 Configure Obsidian Vault on NAS

```bash
# Desktop: Obsidian already installed (snap package per system status)
# Point vault to NAS mount
# File > Open Vault > /mnt/nas/obsidian

# Enable Obsidian Sync (premium feature from User special status.md)
# Settings > Sync > Sign in with Obsidian account
# Configure sync to use NAS as primary, cloud as backup
```

### 6.2 Setup n8n Workflows for AI Automation

**Objective**: Automate Git commits → Obsidian notes, MLflow experiments → knowledge base.

#### Workflow 1: Git Commit → Obsidian Note

1. Access n8n UI: `http://192.168.1.21:5678`
2. Create new workflow:
   - **Trigger**: Webhook (listen for GitHub push events)
   - **Action 1**: Parse commit message
   - **Action 2**: Call NIM API to generate summary
   - **Action 3**: Create markdown file in `/mnt/nas/obsidian/Automated/Git-<commit-hash>.md`
3. Configure GitHub webhook:
   - Payload URL: `http://192.168.1.21:5678/webhook/<webhook-id>`
   - Content type: application/json
   - Events: Push events

#### Workflow 2: MLflow Run → Obsidian Experiment Log

1. n8n workflow:
   - **Trigger**: Webhook (MLflow experiment complete)
   - **Action 1**: Fetch experiment metrics from MLflow API
   - **Action 2**: Format as markdown table
   - **Action 3**: Append to `/mnt/nas/obsidian/Experiments/<experiment-name>.md`

### 6.3 Validation

```bash
# Test Git webhook
git clone https://github.com/Cpsimer/infrastructure-setup /tmp/test-repo
cd /tmp/test-repo
echo "test" >> README.md
git commit -am "Test automation"
git push

# Check Obsidian vault for new note
ls /mnt/nas/obsidian/Automated/
```

**Acceptance Criteria**:

- [ ] Obsidian vault syncs to NAS: files visible at `/mnt/nas/obsidian`
- [ ] n8n Git workflow triggers on commit: new .md file created
- [ ] MLflow integration logs experiments to Obsidian

---

## Phase 7: Final Validation & Documentation

### 7.1 End-to-End Test: AI Inference Pipeline

```bash
# Submit NeMo training job on desktop GPU
cat <<EOF > /tmp/nemo-train.sh
#!/bin/bash
#SBATCH --job-name=nemo-train
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --time=01:00:00
#SBATCH --output=/mnt/nas/slurm/logs/%j-nemo-train.out

docker run --rm --gpus all \
  -v /mnt/nas/models:/workspace/models \
  nvcr.io/nvidia/nemo:24.09 \
  python3 -c "
import nemo.collections.nlp as nemo_nlp
print('NeMo version:', nemo_nlp.__version__)
# Minimal training loop (placeholder)
"
EOF

sbatch /tmp/nemo-train.sh
squeue  # Monitor job
```

### 7.2 Update Documentation

```bash
# Update README.md with actual NAS IP and service URLs
nano ~/infrastructure-setup/README.md
# Add section:
## Deployed Services
- NAS (TrueNAS): https://192.168.1.10
- Vault: https://192.168.1.10:8200
- MLflow: http://192.168.1.21:5001
- n8n: http://192.168.1.21:5678
- NIM Inference: http://192.168.1.20:8000
- Triton: http://192.168.1.20:8001

# Update .github/copilot-instructions.md with NAS paths
nano ~/infrastructure-setup/.github/copilot-instructions.md
# Update "Storage flows through..." with:
# - Obsidian vault: /mnt/nas/obsidian
# - Model weights: /mnt/nas/models
# - Slurm logs: /mnt/nas/slurm/logs
```

### 7.3 Backup & Recovery Plan

```bash
# Create automated backup script
sudo nano /etc/cron.weekly/backup-cluster.sh
```

```bash
#!/bin/bash
BACKUP_DIR=/mnt/nas/backups/$(date +%F)
mkdir -p $BACKUP_DIR

# Backup Slurm configs
tar -czf $BACKUP_DIR/slurm-configs.tar.gz /etc/slurm

# Backup Vault data (already scripted in Phase 1.4)
tar -czf $BACKUP_DIR/vault-data.tar.gz /mnt/vault-pool/vault/data

# Backup Docker volumes
docker run --rm -v /mnt/nas/postgres-data:/backup-src -v $BACKUP_DIR:/backup-dest alpine tar -czf /backup-dest/postgres.tar.gz -C /backup-src .

echo "Backup completed: $BACKUP_DIR" | tee -a /var/log/cluster-backup.log
```

```bash
sudo chmod +x /etc/cron.weekly/backup-cluster.sh
```

### 7.4 Final Acceptance Checklist

- [ ] **NAS**: TrueNAS accessible, all shares mounted on all nodes
- [ ] **Vault**: Secrets retrievable, auto-unseal works
- [ ] **Slurm**: All 3 nodes (desktop-gpu, xps15-cpu, jetson-edge) in `sinfo` as IDLE
- [ ] **Desktop Services**: NIM + Triton + Redis running, responding to requests
- [ ] **XPS 15 Services**: PostgreSQL + MLflow + n8n operational
- [ ] **Jetson**: Slurm jobs execute on edge partition, GPU detected
- [ ] **Obsidian**: Vault synced to NAS, n8n automation functional
- [ ] **AI TOP Utility**: Profile switching via Slurm prolog/epilog logged
- [ ] **Documentation**: README.md + copilot-instructions.md updated with deployed IPs

---

## Appendix A: Troubleshooting

### Issue: Slurm Node Shows DOWN State

```bash
# Check slurmd logs on problem node
sudo journalctl -u slurmd -n 50

# Common fixes:
# - Munge clock skew: sync time with `sudo chronyc makestep`
# - Firewall blocking: `sudo ufw allow 6817,6818/tcp`
# - Config mismatch: ensure slurm.conf identical on all nodes
```

### Issue: Docker Fails to Pull NGC Images

```bash
# Re-authenticate
docker logout nvcr.io
export NGC_API_KEY=$(docker exec vault vault kv get -field=api_key secret/ngc)
echo $NGC_API_KEY | docker login nvcr.io --username='$oauthtoken' --password-stdin
```

### Issue: NAS Mount Hangs

```bash
# Check NFS server status on TrueNAS
ssh root@192.168.1.10
systemctl status nfs-server

# Force unmount on client
sudo umount -f /mnt/nas/obsidian
# Remount
sudo mount -a
```

---

## Appendix B: Reference Architecture Diagram

```text
┌─────────────────────────────────────────────────────────────────┐
│                      UniFi 2.5G Network                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ Desktop GPU  │  │  XPS 15 CPU  │  │ Jetson Edge  │         │
│  │ (Slurm+K8s)  │  │ (Services)   │  │ (ARM64 AI)   │         │
│  │ RTX 5070 Ti  │  │ n8n/MLflow   │  │ 67 TOPS      │         │
│  │ 192.168.1.20 │  │ 192.168.1.21 │  │ 192.168.1.22 │         │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘         │
│         │                 │                 │                  │
│         └─────────────────┼─────────────────┘                  │
│                           │                                    │
│                  ┌────────▼────────┐                           │
│                  │   WD NAS (2TB)  │                           │
│                  │   TrueNAS Scale │                           │
│                  │ 192.168.1.10    │                           │
│                  │                 │                           │
│                  │ ┌─────────────┐ │                           │
│                  │ │ HashiCorp   │ │                           │
│                  │ │ Vault :8200 │ │                           │
│                  │ └─────────────┘ │                           │
│                  │                 │                           │
│                  │ NFS Shares:     │                           │
│                  │ /obsidian-vault │                           │
│                  │ /model-weights  │                           │
│                  │ /slurm-shared   │                           │
│                  └─────────────────┘                           │
└─────────────────────────────────────────────────────────────────┘
```

---

## Appendix C: GitHub Repos for Jetson Setup

From `Jetson orin nano super setup.md`, key repositories:

- **JetsonHacks**: <https://github.com/jetsonhacks>
  - `install-docker`: Docker installation scripts
  - `jetson-containers`: Pre-built containers for Jetson
  - `rootOnNVMe`: Boot from NVMe SSD
- **NVIDIA AI-IoT**: <https://github.com/NVIDIA-AI-IOT>
  - `jetson-intro-to-distillation`: Model distillation tutorials
- **dusty-nv**: <https://github.com/dusty-nv>
  - `jetson-containers`: Comprehensive container collection
- **GokuMohandas/Made-With-ML**: <https://github.com/GokuMohandas/Made-With-ML>
- **eriklindernoren/ML-From-Scratch**: <https://github.com/eriklindernoren/ML-From-Scratch>
- **rasbt/LLMs-from-scratch**: <https://github.com/rasbt/LLMs-from-scratch>
- **codecrafters-io/build-your-own-x**: <https://github.com/codecrafters-io/build-your-own-x>

---

## Appendix D: Next Steps (Post-Deployment)

1. **Network Iteration** (per user constraint "leave network as is, iterate later"):
   - Evaluate 10G upgrade for desktop-NAS link
   - Implement VLANs for service isolation (GPU/CPU/Management)
   - Configure UniFi Dream Machine for advanced firewall rules

2. **Jetson Advanced Integration**:
   - Deploy video decode pipeline (H.265, 5x 1080p60)
   - Edge inference with quantized models (INT8)
   - Multi-stream analytics (refer to NVIDIA-AI-IOT repos)

3. **Scaling**:
   - Add 2nd GPU node or XPS 13 as backup controller
   - Migrate to UNAS-Pro 4 (UniFi, Q4 2025 release) when available
   - Implement Harbor registry on NAS for local image caching

4. **Security Hardening**:
   - Replace self-signed certs with Let's Encrypt
   - Enable Vault auto-unseal with cloud KMS (optional, violates local-first if internet-dependent)
   - Implement mTLS for Slurm inter-node communication

5. **Advanced AI Workflows**:
   - Deploy NeMo Megatron for large-scale LLM training
   - Implement RAPIDS for GPU-accelerated ETL pipelines
   - Integrate PyG (PyTorch Geometric) for knowledge graph embeddings in Obsidian

---

**Document Version**: 1.0  
**Last Updated**: October 23, 2025  
**Maintained By**: infrastructure-setup repository  
**License**: GNU GPL v3 (per LICENSE file)
