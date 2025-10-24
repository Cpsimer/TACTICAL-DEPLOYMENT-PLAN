# Ultimate Deployment Architecture

## Executive Summary

This document provides the **hypercritical expert-level deployment strategy** for a privacy-focused, cloud-like AI infrastructure that leverages ALL available hardware—primary compute nodes, recycled equipment, and the ultra-secure 2.5G intranet—to deliver enterprise-grade AI capabilities exclusively for internal use.

**Architecture Philosophy**: Local-first, zero-trust, maximum hardware utilization, defense-in-depth security, with cloud-like abstractions (Kubernetes + Slurm) for scalable AI workloads.

---

## Hardware Inventory & Role Assignment

### Primary Compute Tier

| Device | Role | Justification |
|--------|------|---------------|
| **Desktop (Ryzen 9 9900X + RTX 5070 Ti)** | Slurm Controller + GPU Compute | Primary AI inference/training node. RTX 5070 Ti (16GB GDDR7, Blackwell arch) handles NVIDIA NIM, NeMo, Triton workloads. 128GB RAM enables large model hosting. Gigabyte AI TOP 4.1.1 provides thermal automation via Slurm prolog/epilog hooks. |
| **XPS 15 7590 (i9, 64GB RAM)** | CPU Services + Monitoring Hub | Runs n8n automation, Prometheus/Grafana/Loki stack, MLflow, PostgreSQL. 64GB RAM ideal for data preprocessing with RAPIDS/cuDF. Ubuntu Server 24.04 LTS for stability. |
| **Jetson Orin Nano Super (67 TOPS)** | Edge AI + ARM64 Partition | Distilled model inference, video decode specialization (5x 1080p60 H.265), federated Slurm node. JetPack 7.0 (when available) with Docker container runtime. |
| **XPS 13 (Intel Core Ultra 7)** | Portable Client + Orchestration | Remote Slurm job submission, Obsidian client, MicroK8s agent node. Windows 11 preserved for portability; WSL2 for Linux tooling. |

### Recycled Hardware Integration (Maximum Performance Boost)

| Device | New Role | Impact |
|--------|----------|--------|
| **Lenovo P93G001 (i7-8565U, 8GB, 256GB SSD)** | Slurm Login Node + SSH Bastion | **Security**: Single entry point for cluster access, isolates workloads from direct SSH. **Scalability**: Offloads authentication/job submission from Desktop. Install `pass` for credential management, fail2ban for brute-force protection. |
| **UniFi Flex Mini 2.5G (5-port)** | Core 2.5G Wired Switch | **Performance**: 5 ports at 2.5GbE for Desktop/XPS15/NAS/Jetson high-speed interconnect. **Centralization**: Managed via UniFi Network on Desktop, unified with Express 7 for consistent policy. **Simplicity**: Auto-adoption, zero CLI config, PoE passthrough for flexibility. |
| **Samsung HD204UI (2TB) + WD WD10EADS (1TB)** | Cold Storage Tier (TrueNAS) | **Backup Strategy**: 3-2-1 rule implementation. Daily NAS → cold storage (ZFS snapshots), weekly NAS → USB drives. Frees primary NAS for hot data (model weights, Obsidian vault). |
| **MicroCenter USB-C/B Drives (128GB each)** | Encrypted Data Shuttles | **Air-Gap Transfer**: LUKS-encrypted for moving models/data between networks. Emergency recovery images (TrueNAS config, Slurm state). |
| **Apple TV A1427 (3rd Gen)** | 24/7 Dashboard Display | **Operations Visibility**: Kiosk-mode browser displays Grafana dashboards (GPU utilization, Slurm queue, storage health). Reduces context-switching for operators. |

---

## Network Topology: Ultra-Secure 2.5G Intranet

### UniFi-Centralized Architecture

```
Internet (ISP) → Alcatel ONT → Edge Router X SFP (WAN/Routing)
                                    ↓ (Transit /30: 192.168.254.1/30)
                              UniFi Express 7 (Console Mode)
                                    ↓ (Trusted Island: VLANs 10/20/30)
                         ┌──────────────────────┐
                         │  Flex Mini 2.5G      │ (VLAN Trunk)
                         │  5-Port Switch       │
                         └──────────────────────┘
                          │      │      │      │
                       VLAN10 VLAN20 VLAN30 VLAN40
                          │      │      │      │
                       Desktop  NAS  Lenovo Jetson
                       (Compute)(Storage)(Mgmt)(Edge)

EdgeRouter L2 Side (Guest/IoT/Honeypot - VLANs 40/50/60/99):
                         UC AP PRO (Multi-SSID)
                         USG-A (Honeypot VLAN60)
                         USG-B (Management VLAN99)
```

#### VLAN Definitions

**Trusted Island (Express 7 + Flex Mini 2.5G)**:

| VLAN ID | Name | Purpose | Allowed Devices | Subnet |
|---------|------|---------|-----------------|--------|
| **10** | Core/Compute | AI workloads, Slurm, Desktop | Desktop, Lenovo login node | 10.0.10.0/24 |
| **20** | NAS-Management | Storage, backup targets | WD NAS, cold storage HDDs | 10.0.20.0/24 |
| **30** | Personal-WiFi | Trusted personal devices | XPS 13, phones (via Express WiFi) | 10.0.30.0/24 |
| **40** | Edge-Devices | Jetson, future edge nodes | Jetson Orin Nano | 10.0.40.0/24 |

**Edge Router Side (Guest/IoT/Security)**:

| VLAN ID | Name | Purpose | Allowed Devices | Subnet |
|---------|------|---------|-----------------|--------|
| **40** | IoT | Smart devices, sensors | IoT devices via UC AP PRO | 192.168.40.0/24 |
| **50** | Guest | Visitor WiFi | Guest devices via UC AP PRO | 192.168.50.0/24 |
| **60** | Honeypot | Intrusion detection | USG-A (honeypot mode) | 192.168.60.0/24 |
| **99** | Management | EdgeRouter/UniFi device mgmt | USG-B, UC AP PRO | 192.168.99.0/24 |

#### Security Hardening

**UniFi Express 7 (Trusted Island)**:
- **Default Security Posture**: Block-All with explicit allow rules
- **Port AI**: Auto-detects device types, applies appropriate firewall policies
- **Channel AI**: Optimizes WiFi channels for minimal interference
- **Network Isolation**: Core (VLAN10) can access NAS (VLAN20), Edge (VLAN40) isolated unless explicitly routed via Slurm
- **Double NAT**: Express NATs to EdgeRouter transit IP, EdgeRouter NATs to ISP (additional security layer)

**EdgeRouter (Untrusted Side)**:
- **Stateful Firewall**: DROP all inbound except established/related
- **DNAT to Honeypot**: Unsolicited traffic redirected to USG-A (VLAN60) for logging
- **Guest/IoT Isolation**: VLAN50/40 cannot reach Trusted Island (10/20/30/40), internet-only egress
- **Suricata IDS**: Deploy on VLAN60 honeypot; alerts forwarded to Grafana via syslog

**UniFi Network (Desktop Controller)**:
- **CyberSecure**: Threat detection, auto-blocking malicious IPs
- **Client Isolation**: Guest SSID prevents device-to-device communication
- **Bandwidth Limits**: Guest capped at 10Mbps down/5Mbps up
- **fail2ban**: Deployed on Lenovo bastion node (3-strike SSH ban)

---

## Storage Architecture: Tiered + Encrypted

### TrueNAS Scale Configuration

**NAS Hardware**: WD 2TB (primary) + Samsung HD204UI 2TB + WD WD10EADS 1TB

#### ZFS Pool Design

```
Pool: tank (primary)
├── WD 2TB NAS (hot tier, ZFS native encryption)
│   ├── /mnt/tank/obsidian-vault (NFS, 100GB quota)
│   ├── /mnt/tank/model-weights (NFS, 500GB quota, NGC models)
│   ├── /mnt/tank/docker-registry (iSCSI, 200GB, TLS certs)
│   └── /mnt/tank/vault-data (encrypted, HashiCorp Vault backend)
│
Pool: cold-storage (backup tier)
├── Samsung HD204UI 2TB + WD WD10EADS 1TB (ZFS mirror)
│   ├── /mnt/cold/daily-snapshots (automated ZFS send/receive from tank)
│   ├── /mnt/cold/weekly-archives (tar.gz backups)
│   └── /mnt/cold/emergency-recovery (bootable images)
```

#### Backup Strategy (3-2-1 Rule)

- **3 Copies**: Live data (tank), cold storage (mirror), USB drives (weekly rotation)
- **2 Media Types**: NVMe/HDD (tank), HDD (cold), USB flash (portable)
- **1 Off-Site**: USB drives stored in fireproof safe; rotated weekly

**Automation**:

```bash
# TrueNAS Cron Jobs
0 2 * * * zfs snapshot tank@daily-$(date +\%Y\%m\%d) && zfs send tank@daily-* | zfs recv cold-storage/daily
0 3 * * 0 tar -czf /mnt/cold/weekly/backup-$(date +\%Y\%m\%d).tar.gz /mnt/tank/obsidian-vault /mnt/tank/vault-data
```

---

## Orchestration: Hybrid Slurm + MicroK8s

### Why Hybrid?

- **Slurm**: GPU/CPU resource allocation, batch job scheduling, federation across heterogeneous nodes (x86/ARM64)
- **MicroK8s**: Container lifecycle, service discovery, persistent storage (NFS CSI driver)
- **Integration**: Slurm jobs launch MicroK8s pods; MicroK8s provides DNS/load balancing for services

### Slurm Cluster Architecture

```
Controller: Desktop (schlimers-server)
├── slurmd (Desktop): GPUs=1 CPUs=24 RealMemory=128000 (partition: gpu)
├── slurmd (XPS 15): CPUs=16 RealMemory=64000 (partition: cpu)
├── slurmd (Jetson): CPUs=6 RealMemory=8000 Gres=gpu:1 (partition: edge-arm64)
└── slurmctld + slurmdbd: PostgreSQL on XPS 15

Login Node: Lenovo P93G001
├── SSH bastion (port 2222, key-only auth)
├── Slurm client tools (sbatch, squeue, scancel)
└── pass password store (secrets for job submission)
```

#### Partition Strategy

| Partition | Nodes | Purpose | Priority |
|-----------|-------|---------|----------|
| **gpu** | Desktop | NIM inference, NeMo training, TensorRT optimization | High (1000) |
| **cpu** | XPS 15 | RAPIDS data preprocessing, n8n workflows, MLflow | Medium (500) |
| **edge-arm64** | Jetson | Distilled model inference, video decode | Low (100) |
| **all** | Desktop + XPS 15 + Jetson | Embarrassingly parallel jobs (preemptible) | Very Low (10) |

#### Gigabyte AI TOP Integration (Thermal Automation)

**Prolog Script** (`/etc/slurm/prolog.d/ai-top-thermal.sh`):

```bash
#!/bin/bash
# Set thermal profile based on job type
JOB_TYPE=$(scontrol show job $SLURM_JOB_ID | grep -oP 'Comment=\K\w+')

case $JOB_TYPE in
  "training")
    /opt/gigabyte-ai-top-utility/gigabyte-ai-top set-profile --mode performance --pbo enabled
    ;;
  "inference")
    /opt/gigabyte-ai-top-utility/gigabyte-ai-top set-profile --mode balanced --pbo auto
    ;;
  *)
    /opt/gigabyte-ai-top-utility/gigabyte-ai-top set-profile --mode quiet --pbo disabled
    ;;
esac

# Export telemetry to Prometheus pushgateway
/opt/gigabyte-ai-top-utility/gigabyte-ai-top get-telemetry | \
  curl --data-binary @- http://xps15:9091/metrics/job/ai-top/instance/$HOSTNAME
```

**Epilog Script** (reset to quiet mode, log metrics)

---

## Security: Defense-in-Depth

### Layer 1: Network (Zero Trust)

- **Firewall**: Edge Router X SFP with strict inbound rules (DROP all except established/related)
- **VLANs**: Micro-segmentation prevents lateral movement
- **Honeypot**: VLAN50 with fake SSH/SMB shares; logs attackers via Suricata
- **WireGuard VPN**: Optional remote access (disabled by default); XPS 13 clients only

### Layer 2: Host (Hardened OS)

- **Ubuntu 24.04 LTS**: All nodes (except XPS 13 Windows); minimal install
- **Full-Disk Encryption**: LUKS on all SSDs/NVMe drives; passphrase stored in TPM 2.0
- **Secure Boot**: Enabled on Desktop/XPS 15; signed kernels only
- **AppArmor**: Enforcing mode; custom profiles for Docker/Slurm daemons
- **fail2ban**: 3-strike SSH ban; VLAN10 management IPs whitelisted

### Layer 3: Application (Secrets Management)

#### HashiCorp Vault (NAS Deployment)

```
Vault Server: TrueNAS jail (Ubuntu 24.04 minimal)
├── Storage Backend: ZFS encrypted dataset (/mnt/tank/vault-data)
├── Auto-Unseal: Transit seal with KMS key stored on Lenovo login node
├── Auth Methods:
│   ├── AppRole (Slurm jobs, n8n workflows)
│   ├── LDAP (future: Active Directory integration)
│   └── Token (emergency break-glass)
├── Secrets Engines:
│   ├── KV v2: NGC API keys, database credentials, SSH private keys
│   ├── PKI: TLS certs for services (Docker registry, Grafana)
│   └── Database: Dynamic PostgreSQL creds (rotation every 24h)
```

**Slurm + Vault Integration**:

```bash
# Job script fetches secrets at runtime
#!/bin/bash
#SBATCH --comment=inference

# Authenticate with Vault using AppRole
export VAULT_ADDR="https://10.0.20.10:8200"
VAULT_TOKEN=$(vault write -field=token auth/approle/login \
  role_id=$SLURM_JOB_ID secret_id=$(cat /etc/slurm/vault-secret-id))

# Fetch NGC API key
NGC_API_KEY=$(vault kv get -field=api_key secret/ngc)

# Run NIM container
docker run --gpus all -e NGC_API_KEY=$NGC_API_KEY nvcr.io/nvidia/nim:latest
```

---

## AI Software Stack Deployment

### Phase 1: Foundation (Must-Have)

#### 1.1 Docker Registry (NAS)

**Why**: Air-gapped NGC container mirror; avoid rate limits; local caching

```bash
# TrueNAS jail: registry2
docker run -d -p 5000:5000 --restart=always \
  --name registry \
  -v /mnt/tank/docker-registry:/var/lib/registry \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_PROXY_REMOTEURL=https://nvcr.io \
  -e REGISTRY_PROXY_USERNAME=$NGC_API_KEY \
  -e REGISTRY_PROXY_PASSWORD=$NGC_API_SECRET \
  registry:2
```

**All nodes configured**:

```json
// /etc/docker/daemon.json
{
  "insecure-registries": [],
  "registry-mirrors": ["https://10.0.20.10:5000"]
}
```

#### 1.2 NVIDIA NIM (Desktop GPU)

**Deployment**: Slurm job template

```bash
#!/bin/bash
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --mem=32G
#SBATCH --comment=inference

srun docker run --gpus all --shm-size=16g \
  -e NGC_API_KEY=$(vault kv get -field=api_key secret/ngc) \
  -p 8000:8000 \
  nvcr.io/nvidia/nim-llm:llama-3.1-8b-instruct-v1.0
```

**Performance Target**: >220 tok/s (Llama 3.1 8B)

#### 1.3 NeMo Framework (Desktop GPU)

**Use Case**: Fine-tune models on custom datasets (Obsidian vault text)

```python
# Slurm job: Fine-tune LoRA adapter for domain-specific QA
from nemo.collections.nlp.models import MegatronGPTModel

model = MegatronGPTModel.restore_from("/mnt/tank/model-weights/llama-3.1-8b.nemo")
model.add_adapter(lora_rank=16)
model.train(
    train_data="/mnt/tank/obsidian-vault/exported-notes.jsonl",
    val_data="/mnt/tank/obsidian-vault/validation.jsonl",
    max_steps=1000
)
model.save_adapter("/mnt/tank/model-weights/lora-obsidian-qa.adapter")
```

#### 1.4 Triton Inference Server (Desktop GPU)

**Multi-Model Serving**: Host multiple models with dynamic batching

```bash
# Model repository structure
/mnt/tank/model-weights/triton-models/
├── llama-3.1-8b/
│   ├── config.pbtxt
│   └── 1/model.plan (TensorRT engine)
├── embedding-model/
│   ├── config.pbtxt
│   └── 1/model.onnx
└── classification/
    ├── config.pbtxt
    └── 1/model.savedmodel

# Slurm job
srun docker run --gpus all -p 8001:8001 -p 8002:8002 \
  -v /mnt/tank/model-weights/triton-models:/models \
  nvcr.io/nvidia/tritonserver:24.10-py3 \
  tritonserver --model-repository=/models --strict-model-config=false
```

#### 1.5 PyTorch + TensorRT (Desktop)

**Container**: NGC PyTorch image with TensorRT pre-integrated

```Dockerfile
FROM nvcr.io/nvidia/pytorch:24.10-py3
RUN pip install tensorrt onnx onnx-graphsurgeon
COPY requirements.txt .
RUN pip install -r requirements.txt
```

#### 1.6 n8n Automation (XPS 15)

**Workflows**:

1. **Git → Obsidian**: Commit hooks push code snippets to vault
2. **MLflow → Obsidian**: Experiment results auto-generate notes
3. **Slurm → Grafana**: Job completion triggers dashboard updates

```yaml
# Docker Compose (XPS 15)
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=$(vault kv get -field=password secret/n8n)
    volumes:
      - /opt/n8n-data:/home/node/.n8n
```

#### 1.7 CUDA-X Libraries (All Compute Nodes)

**Installation**: Via NGC containers

```bash
# cuDF (RAPIDS) for data preprocessing (XPS 15)
docker pull nvcr.io/nvidia/rapidsai/rapidsai-core:24.10-cuda12.5-py3.11

# cuDNN for deep learning (Desktop)
docker pull nvcr.io/nvidia/cuda:12.5.1-cudnn9-devel-ubuntu22.04
```

---

### Phase 2: Performance Boosters (Should-Have)

#### 2.1 RAPIDS (XPS 15 CPU + Desktop GPU)

**Impact**: 5-10x speedup for data preprocessing (pandas → cuDF)

**Example**: Preprocess 10GB CSV for training

```python
# Traditional (pandas on XPS 15 CPU): ~45 minutes
import pandas as pd
df = pd.read_csv("/mnt/tank/datasets/large.csv")
df_clean = df.dropna().groupby("category").mean()

# RAPIDS (cuDF on Desktop GPU): ~4 minutes
import cudf
df = cudf.read_csv("/mnt/tank/datasets/large.csv")
df_clean = df.dropna().groupby("category").mean()
```

**Deployment**: Slurm job requests GPU partition

#### 2.2 PyTorch Geometric (PyG)

**Use Case**: Graph ML for Obsidian knowledge graph (note relationships)

```python
import torch_geometric as pyg

# Build graph from Obsidian backlinks
graph = pyg.data.Data(
    x=note_embeddings,  # Node features (sentence embeddings)
    edge_index=backlink_adjacency,  # [[source, target], ...]
    y=note_categories  # Labels for classification
)

# Train GNN for note recommendation
model = pyg.nn.GCN(in_channels=768, hidden_channels=256, out_channels=10)
```

#### 2.3 Magnum IO + Aerial SDK

**Rationale**: User has NVIDIA 6G Developer access

**Use Case**: High-throughput I/O for streaming inference (future: wireless edge)

---

## Obsidian Integration: AI-Powered Knowledge Management

### Vault Architecture

**Location**: `/mnt/tank/obsidian-vault` (NFS share, all nodes access)

**Plugins** (Core Only, No Community):

- **Dataview**: SQL-like queries for notes
- **Canvas**: Visual knowledge graphs
- **Daily Notes**: Automated journal entries
- **Templates**: Note templates with AI-generated outlines
- **Sync**: Obsidian Sync (premium) + local NAS backup

### n8n Automation Workflows

#### Workflow 1: Git Commit → Obsidian Note

```
Trigger: GitHub Webhook (new commit)
  ↓
Extract: Commit message, diff, author
  ↓
LLM (NIM): Generate summary + tags
  ↓
Create Note: [[Projects/Repo-Name/Commit-HASH]].md
  ↓
Update Index: Add to daily note
```

#### Workflow 2: MLflow Experiment → Obsidian Note

```
Trigger: MLflow REST API (new run)
  ↓
Fetch: Metrics (loss, accuracy), params, artifacts
  ↓
Generate: Markdown table, charts (matplotlib → PNG)
  ↓
Create Note: [[Experiments/Run-ID]].md
  ↓
Link: To parent project note
```

#### Workflow 3: Slurm Job Completion → Dashboard

```
Trigger: Slurm epilog script (HTTP POST to n8n)
  ↓
Parse: Job ID, status, runtime, GPU utilization
  ↓
Update: Grafana annotation (mark job on timeline)
  ↓
Create Note: [[Jobs/Job-ID]].md (if failed, include logs)
```

---

## Deployment Sequence: Exact Step-by-Step Process

### Phase 0: Pre-Deployment (Days 1-2)

**Impact**: Prevent cascading failures, establish baseline

#### Step 0.1: Fix Degraded MicroK8s

```bash
# Desktop (schlimers-server)
systemctl --failed  # Identify 2 failed units
microk8s kubectl get pods --all-namespaces | grep -v Running

# Option A: Selective repair
microk8s kubectl delete pod <stuck-pod> --force --grace-period=0

# Option B: Clean slate (if no critical workloads)
microk8s reset
microk8s start
microk8s enable dns storage
```

**Validation**: `microk8s status` shows "microk8s is running"

#### Step 0.2: Initialize `pass` Password Store

```bash
# Generate GPG key if needed
gpg --full-generate-key  # RSA 4096, no expiry

# Initialize pass
pass init <gpg-key-id>

# Store critical secrets
pass insert slurm/db-password
pass insert vault/root-token
pass insert ngc/api-key
```

**Backup**: `pass git init && pass git remote add origin <private-repo>`

#### Step 0.3: Backup Existing NAS Data

```bash
# If WD NAS has data, rsync to external drive
rsync -avP /mnt/nas/ /mnt/usb-backup/nas-$(date +%Y%m%d)/
```

---

### Phase 1: Network Foundation (Days 3-5)

**Impact**: UniFi-centralized 2.5G backbone, double-NAT security, seamless device adoption

#### Step 1.1: Configure Edge Router X SFP (Transit + EdgeRouter Side VLANs)

**Access**: SSH to Edge Router

```bash
configure

# WAN Interface (ISP)
set interfaces ethernet eth0 address dhcp
set interfaces ethernet eth0 description "ISP WAN"

# Transit Network to Express 7 (eth1.254)
set interfaces ethernet eth1 vif 254 address 192.168.254.1/30
set interfaces ethernet eth1 vif 254 description "Transit to Express 7"

# Guest/IoT/Honeypot VLANs (EdgeRouter switch fabric)
set interfaces switch switch0 address 192.168.99.1/24
set interfaces switch switch0 switch-port interface eth2 vlan vid 99  # Management
set interfaces switch switch0 switch-port interface eth3 vlan vid 40  # IoT
set interfaces switch switch0 switch-port interface eth3 vlan vid 50  # Guest (trunk for UC AP)
set interfaces switch switch0 switch-port interface eth3 vlan vid 60  # Honeypot
set interfaces switch switch0 switch-port interface eth4 vlan vid 60  # USG-A

# VLAN Interfaces
set interfaces ethernet eth1 vif 40 address 192.168.40.1/24
set interfaces ethernet eth1 vif 40 description "IoT"
set interfaces ethernet eth1 vif 50 address 192.168.50.1/24
set interfaces ethernet eth1 vif 50 description "Guest"
set interfaces ethernet eth1 vif 60 address 192.168.60.1/24
set interfaces ethernet eth1 vif 60 description "Honeypot"

commit
save
```

#### Step 1.2: Configure DHCP + UniFi Controller Pointer (EdgeRouter)

```bash
configure

# DHCP for EdgeRouter-managed VLANs
set service dhcp-server shared-network-name MGMT subnet 192.168.99.0/24 start 192.168.99.10 stop 192.168.99.100
set service dhcp-server shared-network-name MGMT subnet 192.168.99.0/24 default-router 192.168.99.1
set service dhcp-server shared-network-name MGMT subnet 192.168.99.0/24 dns-server 1.1.1.1
set service dhcp-server shared-network-name MGMT subnet 192.168.99.0/24 unifi-controller 10.0.10.2  # Desktop UniFi Network

set service dhcp-server shared-network-name IOT subnet 192.168.40.0/24 start 192.168.40.10 stop 192.168.40.200
set service dhcp-server shared-network-name IOT subnet 192.168.40.0/24 default-router 192.168.40.1
set service dhcp-server shared-network-name IOT subnet 192.168.40.0/24 unifi-controller 10.0.10.2

set service dhcp-server shared-network-name GUEST subnet 192.168.50.0/24 start 192.168.50.10 stop 192.168.50.200
set service dhcp-server shared-network-name GUEST subnet 192.168.50.0/24 default-router 192.168.50.1
set service dhcp-server shared-network-name GUEST subnet 192.168.50.0/24 unifi-controller 10.0.10.2

# NAT Masquerade
set service nat rule 5000 outbound-interface eth0
set service nat rule 5000 type masquerade
set service nat rule 5000 description "Masquerade to ISP"

# Honeypot DNAT (redirect unsolicited traffic)
set service nat rule 100 description "Redirect to Honeypot"
set service nat rule 100 inbound-interface eth0
set service nat rule 100 inside-address address 192.168.60.10  # USG-A IP
set service nat rule 100 protocol tcp_udp
set service nat rule 100 type destination

# Firewall: Block Trusted Island from EdgeRouter side
set firewall name BLOCK-ISLAND rule 10 action drop
set firewall name BLOCK-ISLAND rule 10 destination address 10.0.0.0/8
set firewall name BLOCK-ISLAND rule 10 description "Block access to Trusted Island"
set interfaces ethernet eth1 vif 40 firewall in name BLOCK-ISLAND
set interfaces ethernet eth1 vif 50 firewall in name BLOCK-ISLAND

commit
save
```

**Validation**: IoT/Guest devices get DHCP, cannot ping 10.0.x.x (Trusted Island blocked)

#### Step 1.3: Configure UniFi Express 7 (Console Mode - Trusted Island)

**Web UI Setup** (connect to Express 7 default IP or via UniFi app):

1. **Choose Console Mode** (standalone site, not managed by other controller)
2. **WAN Configuration**:
   - Static IP: `192.168.254.2/30`
   - Gateway: `192.168.254.1` (EdgeRouter eth1.254)
   - DNS: `1.1.1.1, 1.0.0.1`
3. **Create Networks**:
   - VLAN 10 "Core": `10.0.10.0/24` (DHCP enabled, default route)
   - VLAN 20 "NAS-Mgmt": `10.0.20.0/24` (DHCP enabled)
   - VLAN 30 "Personal-WiFi": `10.0.30.0/24` (DHCP enabled, for Express WiFi SSID)
   - VLAN 40 "Edge-Devices": `10.0.40.0/24` (DHCP enabled)
4. **Enable NAT**: Express WAN → NAT to 192.168.254.1 (double-NAT for security)
5. **Security Settings**:
   - Default Security Posture: **Block-All**
   - Enable **Port AI** and **Channel AI**
   - Create firewall rule: Allow VLAN10 → VLAN20 (Core can access NAS)
   - Block VLAN40 → VLAN10/20 (Edge isolated unless routed via Slurm jobs)

**Validation**: Express WAN pings 192.168.254.1, LAN devices get DHCP on 10.0.10.0/24

#### Step 1.4: Adopt Flex Mini 2.5G to Express 7

**Physical Connection**: Flex Mini uplink → Express 7 LAN port

**Express UI**:

1. Navigate to **Devices** → **Adopt** Flex Mini
2. **Uplink Profile**: Create "TRUNK-ALL" (tagged VLANs 10, 20, 30, 40)
3. **Port Profiles**:
   - Port 1 → "ACCESS-Core" (VLAN 10, Desktop)
   - Port 2 → "ACCESS-NAS" (VLAN 20, WD NAS)
   - Port 3 → "ACCESS-Mgmt" (VLAN 10, Lenovo bastion)
   - Port 4 → "ACCESS-Edge" (VLAN 40, Jetson)
   - Port 5 → Reserved/uplink
4. **Enable PoE Passthrough** (if needed for future devices)

**Validation**: 
```bash
# From Desktop (10.0.10.2)
ping 10.0.20.10  # NAS should respond
ping 10.0.40.2   # Jetson should respond (if firewall allows)
iperf3 -c 10.0.20.10 -t 10  # Test 2.5G throughput to NAS
```

#### Step 1.5: Setup UniFi Network Controller (Desktop)

**Desktop Installation** (Ubuntu 24.04):

```bash
# Install UniFi Network
curl -fsSL https://get.glennr.nl/unifi/install/install_latest/unifi-latest.sh | sudo bash

# Access Web UI
# https://10.0.10.2:8443

# Initial Setup:
# - Create admin account
# - Name: "AI-Cluster-Controller"
# - Skip Cloud Access (local-only)
```

**Add EdgeRouter-Side Networks** (VLAN-only, no gateway):

1. Settings → Networks → Create New
   - Name: "IoT-EdgeRouter", VLAN 40, **No gateway** (EdgeRouter manages)
   - Name: "Guest-EdgeRouter", VLAN 50, **No gateway**
   - Name: "Honeypot", VLAN 60, **No gateway**
   - Name: "Management", VLAN 99, **No gateway**

2. **Adopt UC AP PRO**:
   - Should auto-discover via DHCP option (EdgeRouter DHCP scope 99 points here)
   - Assign SSIDs:
     - "AI-IoT" → VLAN 40, **Client Isolation** enabled
     - "AI-Guest" → VLAN 50, **Network & Client Isolation**, 10Mbps cap
   - Enable **CyberSecure**, **Optimize for WiFi6**

3. **Adopt USG-A (Honeypot)** and **USG-B (Management)**:
   - Configure as standalone gateways (no routing, monitoring only)

**Validation**: UC AP PRO shows online, broadcasts 2 SSIDs, devices on IoT/Guest cannot reach 10.0.x.x

---

### Phase 2: Storage Layer (Days 6-8)

**Impact**: Encrypted ZFS pools, automated backups, Vault secrets backend

#### Step 2.1: Install TrueNAS Scale on WD NAS

1. Download ISO: `wget https://download.truenas.com/TrueNAS-SCALE-Dragonfish/TrueNAS-SCALE-24.10.0.2/TrueNAS-SCALE-24.10.0.2.iso`
2. Flash to USB: `dd if=TrueNAS-SCALE-24.10.0.2.iso of=/dev/sdX bs=4M status=progress`
3. Boot NAS from USB, follow installer
4. Set static IP: `192.168.30.10/24` (VLAN30)

**Post-Install**:

```bash
# SSH to TrueNAS
ssh admin@192.168.30.10

# Update system
truenas-scale update
```

#### Step 2.2: Create ZFS Pools

**Web UI** (`https://192.168.30.10`):

1. Storage → Pools → Add
   - Name: `tank`
   - Layout: Single disk (WD 2TB)
   - Encryption: ✅ Enabled (passphrase in `pass`)
2. Add Cold Storage Pool:
   - Name: `cold-storage`
   - Layout: Mirror (Samsung 2TB + WD 1TB)
   - Encryption: ✅ Enabled

**Datasets**:

```bash
zfs create tank/obsidian-vault
zfs set quota=100G tank/obsidian-vault

zfs create tank/model-weights
zfs set quota=500G tank/model-weights

zfs create tank/docker-registry
zfs set quota=200G tank/docker-registry

zfs create tank/vault-data
zfs set quota=50G tank/vault-data
```

#### Step 2.3: Configure NFS Shares

**Web UI**:

- Sharing → Unix (NFS) → Add
  - Path: `/mnt/tank/obsidian-vault`
  - Network: `192.168.20.0/24,192.168.40.0/24,192.168.70.0/24`
  - Options: `rw,sync,no_subtree_check`

**Mount on All Nodes**:

```bash
# Desktop/Jetson/Lenovo (all on Trusted Island)
echo "10.0.20.10:/mnt/tank/obsidian-vault /mnt/obsidian-vault nfs defaults,_netdev 0 0" >> /etc/fstab
echo "10.0.20.10:/mnt/tank/model-weights /mnt/model-weights nfs defaults,_netdev 0 0" >> /etc/fstab
mount -a
```

#### Step 2.4: Deploy HashiCorp Vault

**TrueNAS Jail** (Ubuntu 24.04 minimal):

```bash
# Install Vault
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list
apt update && apt install vault

# Configure Vault
cat > /etc/vault.d/vault.hcl <<EOF
storage "file" {
  path = "/mnt/tank/vault-data"
}
listener "tcp" {
  address = "0.0.0.0:8200"
  tls_cert_file = "/etc/vault.d/tls/vault.crt"
  tls_key_file = "/etc/vault.d/tls/vault.key"
}
ui = true
EOF

# Start Vault
systemctl enable vault
systemctl start vault

# Initialize
vault operator init -key-shares=5 -key-threshold=3 | tee /root/vault-init.txt

# Store unseal keys in pass
pass insert vault/unseal-key-1 < /root/vault-init.txt
```

**Auto-Unseal Systemd Service**:

```ini
[Unit]
Description=Vault Auto-Unseal
After=vault.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/vault-unseal.sh

[Install]
WantedBy=multi-user.target
```

```bash
#!/bin/bash
# /usr/local/bin/vault-unseal.sh
export VAULT_ADDR="https://192.168.30.10:8200"
for i in {1..3}; do
  vault operator unseal $(pass show vault/unseal-key-$i)
done
```

---

### Phase 3: Compute Cluster (Days 9-12)

**Impact**: Slurm federation, GPU/CPU partitions, thermal automation

#### Step 3.1: Install Slurm on All Nodes

**All Nodes** (Desktop, XPS 15, Jetson, Lenovo):

```bash
apt install slurm-wlm slurm-client -y
```

**Desktop (Controller)**:

```bash
apt install slurm-wlm-basic-plugins slurmctld slurmdbd -y
```

#### Step 3.2: Configure Slurm

**`/etc/slurm/slurm.conf`** (Desktop):

```ini
ClusterName=ai-cluster
SlurmctldHost=desktop(10.0.10.2)

# Accounting
AccountingStorageType=accounting_storage/slurmdbd
AccountingStorageHost=desktop
AccountingStoragePort=6819

# Scheduling
SchedulerType=sched/backfill
SelectType=select/cons_tres
SelectTypeParameters=CR_Core_Memory

# Nodes (Trusted Island IPs)
NodeName=desktop CPUs=24 RealMemory=128000 Gres=gpu:rtx5070ti:1 State=UNKNOWN NodeAddr=10.0.10.2
NodeName=lenovo CPUs=8 RealMemory=8000 State=UNKNOWN NodeAddr=10.0.10.3
NodeName=jetson CPUs=6 RealMemory=8000 Gres=gpu:orin:1 State=UNKNOWN NodeAddr=10.0.40.2

# Partitions
PartitionName=gpu Nodes=desktop Default=YES MaxTime=INFINITE State=UP Priority=1000
PartitionName=cpu Nodes=lenovo MaxTime=INFINITE State=UP Priority=500
PartitionName=edge-arm64 Nodes=jetson MaxTime=INFINITE State=UP Priority=100
PartitionName=all Nodes=desktop,lenovo,jetson MaxTime=INFINITE State=UP Priority=10 Shared=YES PreemptMode=CANCEL
```

**`/etc/slurm/gres.conf`** (Desktop + Jetson):

```ini
# Desktop
NodeName=desktop Name=gpu Type=rtx5070ti File=/dev/nvidia0

# Jetson
NodeName=jetson Name=gpu Type=orin File=/dev/nvidia0
```

**Start Services**:

```bash
# Desktop
systemctl enable slurmctld slurmdbd slurmd
systemctl start slurmctld slurmdbd slurmd

# XPS 15, Jetson, Lenovo
systemctl enable slurmd
systemctl start slurmd
```

**Validation**:

```bash
sinfo  # All nodes should show "idle"
srun -N1 hostname  # Test job submission
```

#### Step 3.3: Integrate Gigabyte AI TOP with Slurm

**Install AI TOP Utility** (Desktop):

```bash
# Assume binary at /opt/gigabyte-ai-top-utility/gigabyte-ai-top
chmod +x /opt/gigabyte-ai-top-utility/gigabyte-ai-top

# Create wrapper script
cat > /usr/local/bin/ai-top-thermal <<'EOF'
#!/bin/bash
MODE=${1:-balanced}
/opt/gigabyte-ai-top-utility/gigabyte-ai-top set-profile --mode $MODE
EOF
chmod +x /usr/local/bin/ai-top-thermal
```

**Slurm Prolog/Epilog**:

```bash
# /etc/slurm/prolog.d/01-thermal.sh
#!/bin/bash
COMMENT=$(scontrol show job $SLURM_JOB_ID | grep -oP 'Comment=\K\w+')
case $COMMENT in
  training) ai-top-thermal performance ;;
  inference) ai-top-thermal balanced ;;
  *) ai-top-thermal quiet ;;
esac

# /etc/slurm/epilog.d/99-reset-thermal.sh
#!/bin/bash
ai-top-thermal quiet
```

**Enable**:

```bash
chmod +x /etc/slurm/prolog.d/01-thermal.sh /etc/slurm/epilog.d/99-reset-thermal.sh
```

---

### Phase 4: AI Services (Days 13-16)

**Impact**: NIM inference, NeMo training, Triton serving, n8n automation

#### Step 4.1: Deploy Docker Registry (NAS)

**TrueNAS Jail**:

```bash
# Generate TLS certs
openssl req -newkey rsa:4096 -nodes -sha256 -keyout /etc/docker/certs.d/domain.key -x509 -days 365 -out /etc/docker/certs.d/domain.crt -subj "/CN=nas.vlan30.local"

# Run registry
docker run -d -p 5000:5000 --restart=always \
  --name registry \
  -v /mnt/tank/docker-registry:/var/lib/registry \
  -v /etc/docker/certs.d:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2
```

**Configure All Nodes**:

```bash
# Copy CA cert
scp nas:/etc/docker/certs.d/domain.crt /usr/local/share/ca-certificates/nas-registry.crt
update-ca-certificates

# Update Docker daemon
cat > /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://nas.vlan30.local:5000"]
}
EOF
systemctl restart docker
```

#### Step 4.2: Deploy NVIDIA NIM (Desktop GPU)

**Slurm Job Template**:

```bash
cat > /opt/slurm-templates/nim-llama.sh <<'EOF'
#!/bin/bash
#SBATCH --job-name=nim-llama
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --mem=32G
#SBATCH --comment=inference

export VAULT_ADDR="https://10.0.20.10:8200"
NGC_KEY=$(vault kv get -field=api_key secret/ngc)

srun docker run --gpus all --shm-size=16g \
  -e NGC_API_KEY=$NGC_KEY \
  -p 8000:8000 \
  nvcr.io/nvidia/nim-llm:llama-3.1-8b-instruct-v1.0
EOF

sbatch /opt/slurm-templates/nim-llama.sh
```

**Validation**: `curl http://10.0.10.2:8000/v1/chat/completions` returns 200

#### Step 4.3: Deploy n8n (XPS 15)

```bash
# XPS 15
mkdir /opt/n8n-data
docker run -d --name n8n \
  -p 5678:5678 \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=$(vault kv get -field=password secret/n8n) \
  -v /opt/n8n-data:/home/node/.n8n \
  n8nio/n8n
```

**Create Workflows** (via Web UI `http://10.0.10.2:5678`):

1. Git Webhook → LLM Summary → Obsidian Note
2. MLflow API → Grafana Annotation
3. Slurm Epilog → Dashboard Update

---

### Phase 5: Monitoring & Observability (Days 17-18)

**Impact**: Real-time cluster health, GPU telemetry, job analytics

#### Step 5.1: Deploy Prometheus + Grafana (XPS 15)

**Docker Compose**:

```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=$(vault kv get -field=password secret/grafana)
    volumes:
      - grafana-data:/var/lib/grafana

  loki:
    image: grafana/loki:latest
    ports:
      - "3100:3100"
    volumes:
      - ./loki-config.yml:/etc/loki/local-config.yaml

volumes:
  prometheus-data:
  grafana-data:
```

**Prometheus Config** (`prometheus.yml`):

```yaml
scrape_configs:
  - job_name: 'desktop-gpu'
    static_configs:
      - targets: ['10.0.10.2:9400']  # DCGM exporter

  - job_name: 'node-exporters'
    static_configs:
      - targets:
        - '10.0.10.2:9100'  # Desktop
        - '10.0.10.3:9100'  # Lenovo
        - '10.0.40.2:9100'  # Jetson

  - job_name: 'ai-top-telemetry'
    static_configs:
      - targets: ['10.0.10.2:9091']  # Pushgateway

  - job_name: 'nas-storage'
    static_configs:
      - targets: ['10.0.20.10:9100']  # NAS node exporter
```

**Install Exporters on All Nodes**:

```bash
# Node exporter
docker run -d --net="host" --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host

# DCGM exporter (Desktop only)
docker run -d --gpus all -p 9400:9400 \
  nvcr.io/nvidia/k8s/dcgm-exporter:3.3.5-3.4.1-ubuntu22.04
```

#### Step 5.2: Create Grafana Dashboards

**Import Pre-Built**:

- NVIDIA DCGM Exporter (ID: 12239)
- Node Exporter Full (ID: 1860)

**Custom: Slurm Cluster Health**

- Panels: Job queue depth, GPU utilization over time, partition idle time
- Data Source: Prometheus + Slurm REST API

---

### Phase 6: Jetson Integration (Days 19-20)

**Impact**: Edge AI, ARM64 partition, federated workloads

#### Step 6.1: Flash JetPack 7.0 (When Available)

**Option A: SDK Manager** (Ubuntu host):

```bash
# Install SDK Manager
wget https://developer.nvidia.com/sdk-manager
chmod +x sdkmanager_*.AppImage
./sdkmanager_*.AppImage

# GUI: Select Jetson Orin Nano Super, JetPack 7.0, flash over USB
```

**Option B: Docker Method**:

```bash
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb \
  nvcr.io/nvidia/jetpack-flashing:latest \
  flash.sh jetson-orin-nano-devkit mmcblk0p1
```

#### Step 6.2: Configure Jetson Post-Flash

```bash
# SSH to Jetson
ssh nvidia@10.0.40.2

# Install Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker nvidia

# Install Slurm client
sudo apt install slurm-client -y

# Mount NAS
sudo mkdir /mnt/obsidian-vault /mnt/model-weights
echo "10.0.20.10:/mnt/tank/obsidian-vault /mnt/obsidian-vault nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
echo "10.0.20.10:/mnt/tank/model-weights /mnt/model-weights nfs defaults,_netdev 0 0" | sudo tee -a /etc/fstab
sudo mount -a

# Configure Slurm client to point to Desktop controller
echo "SlurmctldHost=desktop(10.0.10.2)" | sudo tee /etc/slurm/slurm-client.conf

# Join Slurm cluster (already configured in Phase 3)
```

**Test Edge Inference**:

```bash
# Slurm job on Jetson
sbatch --partition=edge-arm64 <<EOF
#!/bin/bash
#SBATCH --gres=gpu:1
srun docker run --runtime nvidia \
  dustynv/l4t-ml:r36.2.0 \
  python3 -c "import torch; print(torch.cuda.is_available())"
EOF
```

---

### Phase 7: Obsidian + Knowledge Graph (Days 21-22)

**Impact**: AI-powered note-taking, automated workflows

#### Step 7.1: Configure Obsidian Vault

**Desktop/XPS 13**:

1. Install Obsidian: <https://obsidian.md/download>
2. Open Vault: `/mnt/obsidian-vault` (NFS mount)
3. Enable Core Plugins:
   - Dataview
   - Canvas
   - Daily Notes
   - Templates

**Vault Structure**:

```
/mnt/obsidian-vault/
├── Daily Notes/
│   └── 2025-10-24.md
├── Projects/
│   ├── infrastructure-setup/
│   │   ├── Deployment Log.md
│   │   └── Commits/
│   └── AI Research/
├── Experiments/
│   └── MLflow-Run-12345.md
└── Templates/
    ├── Project Note.md
    └── Experiment Note.md
```

#### Step 7.2: Create n8n Workflows

**Workflow 1: Git Commit → Obsidian**

1. **Trigger**: Webhook (GitHub post-commit hook)
2. **HTTP Request**: Fetch commit details
3. **AI Chat**: Summarize diff (NIM Llama 3.1)
4. **Obsidian API**: Create note in `Projects/<repo>/Commits/`

**Workflow 2: Slurm Job → Note**

1. **Trigger**: HTTP Request (Slurm epilog POST)
2. **Code**: Parse job status JSON
3. **Condition**: If job failed → create troubleshooting note
4. **Obsidian API**: Link to project note

---

### Phase 8: Validation & Benchmarking (Days 23-24)

**Impact**: Confirm >220 tok/s, 98% GPU util, <50ms latency

#### Step 8.1: Performance Tests

**NIM Inference** (Desktop GPU):

```bash
# Llama 3.1 8B throughput
sbatch <<EOF
#!/bin/bash
#SBATCH --partition=gpu --gres=gpu:1
srun python3 /opt/benchmarks/nim-throughput.py \
  --model llama-3.1-8b --requests 1000 --batch-size 16
EOF

# Target: >220 tokens/second
```

**RAPIDS Speedup** (XPS 15 → Desktop GPU):

```bash
# CPU baseline (pandas)
time python3 /opt/benchmarks/pandas-preprocessing.py  # ~45 min

# GPU (cuDF)
sbatch --partition=gpu <<EOF
#!/bin/bash
srun python3 /opt/benchmarks/cudf-preprocessing.py
EOF
# Target: <5 min (9x speedup)
```

**Network Throughput** (2.5G validation):

```bash
# Desktop → NAS (iperf3 over 2.5G Flex Mini)
iperf3 -c 10.0.20.10 -t 60 -P 4
# Target: >2.3 Gbps aggregate (2.5G wire speed minus overhead)
```

#### Step 8.2: Security Audit

```bash
# Check VLAN isolation (from Desktop on VLAN10)
ping -c 3 192.168.50.10  # Should fail (Guest isolated by firewall)
ping -c 3 10.0.20.10     # Should succeed (NAS accessible)

# Verify Vault sealed on boot
systemctl status vault  # Should show "sealed"
vault status  # Confirm auto-unseal works

# Check SSH hardening
sudo grep PermitRootLogin /etc/ssh/sshd_config  # Should be "no"

# Test UniFi adoption
# From UniFi Network UI (https://10.0.10.2:8443)
# All devices (Flex Mini, UC AP PRO, Express 7 if managed) show "Connected"

# Verify 2.5G link speed on Flex Mini
# UniFi Network → Devices → Flex Mini → check port speeds show 2500 Mbps
```

---

## Operational Runbooks

### Daily Operations

1. **Morning Health Check**:

   ```bash
   sinfo  # Confirm all nodes idle
   microk8s status  # Ensure K8s healthy
   zfs list  # Check pool usage (<80%)
   ```

2. **Submit Inference Job**:

   ```bash
   sbatch /opt/slurm-templates/nim-llama.sh
   squeue -u $USER
   ```

3. **Monitor Dashboards**:
   - Open `http://10.0.10.2:3000` (Grafana)
   - Check GPU utilization panel
   - Review Slurm job queue
   - View UniFi Network health at `https://10.0.10.2:8443`

### Weekly Maintenance

1. **Backup Vault**:

   ```bash
   vault operator raft snapshot save /mnt/tank/vault-backups/vault-$(date +%Y%m%d).snap
   ```

2. **Rotate USB Drives**:

   ```bash
   rsync -avP /mnt/tank/ /mnt/usb-drive-A/
   # Swap USB-A with USB-B (stored off-site)
   ```

3. **Update Containers**:

   ```bash
   docker pull nvcr.io/nvidia/nim-llm:latest
   docker pull nvcr.io/nvidia/tritonserver:latest
   ```

### Disaster Recovery

**Scenario: NAS Failure**

1. **Restore from Cold Storage**:

   ```bash
   zfs send cold-storage/daily@latest | zfs recv tank-new
   mount -t nfs 10.0.20.10:/mnt/tank-new /mnt/obsidian-vault
   ```

2. **Restore Vault**:

   ```bash
   vault operator raft snapshot restore /mnt/usb-drive/vault-backup.snap
   ```

---

## Advanced Optimizations

### CPU: AMD Ryzen 9 9900X Tuning

**PBO Curves via AI TOP**:

```bash
ai-top-thermal performance
# Auto-applies: PBO +200MHz, Curve Optimizer -15 all-core
```

**iGPU Offload** (Radeon Graphics for OpenCL):

```bash
# n8n workflow uses iGPU for image preprocessing
clinfo | grep "Device Name"  # Confirm Radeon detected
```

### GPU: RTX 5070 Ti Optimization

**TensorRT FP8 Quantization**:

```python
# Convert Llama 3.1 to FP8 (2x speedup, minimal accuracy loss)
import tensorrt as trt
builder = trt.Builder()
config = builder.create_builder_config()
config.set_flag(trt.BuilderFlag.FP8)
engine = builder.build_engine(network, config)
```

**VRAM Pooling with NVMe**:

```bash
# AI TOP SSD Mounting: Use Samsung 9100 PRO as VRAM overflow
ai-top storage mount --device /dev/nvme0n1p2 --use vram-pool
```

### Network: 2.5G Tuning

**Jumbo Frames** (UniFi Express 7 + Flex Mini):

1. **Express UI** → Settings → Networks → Edit "Core" (VLAN 10)
   - Advanced → Manual → MTU: 9000
2. Repeat for VLAN 20, 30, 40

**Note**: All Trusted Island devices (Desktop/NAS/Jetson) must support jumbo frames. Verify with:
```bash
ip link show | grep mtu  # Should show mtu 9000
```

**TCP Tuning** (All Nodes):

```bash
cat >> /etc/sysctl.conf <<EOF
net.core.rmem_max = 536870912
net.core.wmem_max = 536870912
net.ipv4.tcp_rmem = 4096 87380 268435456
net.ipv4.tcp_wmem = 4096 65536 268435456
EOF
sysctl -p
```

---

## Critical Success Metrics

| Metric | Target | Validation |
|--------|--------|------------|
| **NIM Throughput** | >220 tok/s | Llama 3.1 8B inference benchmark |
| **GPU Utilization** | >98% | DCGM exporter, sustained training job |
| **Network Bandwidth** | >2.3 Gbps | iperf3 Desktop ↔ NAS |
| **RAPIDS Speedup** | 5-10x | cuDF vs pandas on 10GB dataset |
| **Slurm Queue Time** | <30s | Job submission to execution start |
| **Vault Availability** | 99.9% | Uptime monitoring (Prometheus) |
| **Backup RTO** | <2 hours | NAS failure → cold storage restore |
| **Obsidian Sync Latency** | <5s | Note save → n8n trigger |

---

## Security Compliance Checklist

- [ ] Full-disk encryption (LUKS) on all SSDs
- [ ] Secure Boot enabled (Desktop, XPS 15)
- [ ] `pass` password store initialized with GPG
- [ ] Vault root token stored offline (USB drive in safe)
- [ ] SSH key-only auth (password disabled)
- [ ] VLANs configured with ACLs
- [ ] Honeypot (VLAN50) logs forwarded to Grafana
- [ ] TLS certs for all services (Vault, Registry, Grafana)
- [ ] Weekly USB backup rotation
- [ ] fail2ban active on all nodes (3-strike ban)

---

## Conclusion

This architecture delivers **cloud-like AI infrastructure with zero cloud dependencies**, leveraging:

1. **UniFi-Centralized Architecture**: Express 7 Console + Flex Mini 2.5G for Trusted Island; EdgeRouter + UC AP PRO for Guest/IoT/Honeypot; single Desktop controller for unified management
2. **Double-NAT Security**: Trusted Island (10.0.x.x) hidden behind Express NAT → EdgeRouter NAT → ISP; Guest/IoT completely isolated from compute resources
3. **2.5G Wired Performance**: Full 2.5GbE between Desktop/NAS/Jetson via Flex Mini; jumbo frames enabled; >2.3 Gbps sustained throughput
4. **Recycled Hardware Maximized**: Lenovo (bastion/login node), HDDs (cold storage), Apple TV (dashboards), USB drives (air-gap shuttles)
5. **AI Performance**: NIM (>220 tok/s), RAPIDS (5-10x speedup), TensorRT optimization, Slurm thermal automation via Gigabyte AI TOP
6. **Knowledge Management**: Obsidian + n8n automation, AI-powered note generation, vault on NAS NFS share

**Next Steps**: Execute Phase 0 (fix degraded state), then proceed sequentially through Phases 1-8. Estimated total deployment: **24 days** for production-ready infrastructure.
