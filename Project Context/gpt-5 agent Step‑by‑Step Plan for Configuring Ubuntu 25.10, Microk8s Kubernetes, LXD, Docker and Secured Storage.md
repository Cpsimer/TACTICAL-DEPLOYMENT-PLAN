

## 1 Overview

Your goal is to build a **privacy‑controlled, AI‑accelerated home lab** that supports research and development without any public cloud dependencies. The infrastructure will run **Ubuntu 25.10** across a heterogeneous cluster (AMD desktop, Dell XPS laptops, Jetson Orin Nano Super) and orchestrate workloads with **microk8s** (lightweight Kubernetes) and **rootless Docker**. The plan respects the existing network topology (UniFi USG gateway, Flex Mini 2.5 GbE switch, wired NAS) and focuses on securing the NAS connection, configuring the cluster, providing local API storage and integrating the **Gigabyte AI Top Utility** and **NVIDIA Jetson Orin**. It aligns with the MoSCoW priorities (microk8s, Docker, NeMo, NIM, TensorRT, etc.) and leverages your hardware specialities while enforcing robust security practices.

## 2 Preparation & Baseline Setup

### 2.1 Inventory and network check

|Device|Key Hardware|Connection|Notes|
|---|---|---|---|
|**AI Desktop**|AMD Ryzen 9 9900X, Gigabyte X870 Aorus motherboard, 128 GB DDR5 RAM, RTX 5070 Ti, Samsung 9100 PRO 2 TB NVMe|Wired 2.5 GbE via Flex Mini|Main controller node with GPU acceleration.|
|**Portable XPS 13**|Intel Core Ultra 7 258V, integrated Intel Arc GPU, 32 GB RAM|USB‑C to 2.5 GbE adapter|Used for portable development.|
|**Stationary XPS 15**|Intel Core (~2.4 GHz), 64 GB RAM, 1 TB NVMe|USB‑C to 2.5 GbE|Will dual‑boot/replace Windows with Ubuntu Server.|
|**Jetson Orin Nano Super**|67 TOPS AI, 6‑core ARM CPU, 8 GB LPDDR5, 32 GB micro‑SD + NVMe|1 GbE|Edge device for inference and data ingestion.|
|**NAS (WD 2 TB)**|2 TB HDD|1 GbE to USG gateway|Shared storage and container registry.|

- **Do not modify network topology.** The USG gateway, Flex Mini switch, APs and VLANs remain as they are. All hosts will obtain static or DHCP leases from the existing router.
    
- Confirm firmware/BIOS updates for each device. Update the Gigabyte motherboard BIOS only if necessary; avoid disabling security features (e.g., Secure Boot) unless a kernel module requires it.
    

### 2.2 Create installation media

1. **Download Ubuntu 25.10 ISO** (desktop for workstations, server for headless XPS 15 and Jetson host). Use verified sources and checksums.
    
2. Use `Rufus` or `balenaEtcher` to create a bootable USB drive (16 GB or larger). For the Jetson Orin, install **NVIDIA SDK Manager** on a separate host to flash **JetPack 7** (Beta) onto the micro‑SD or NVMe.
    

### 2.3 Back up existing data

Backup any personal data on the XPS laptops and the desktop. Confirm that the NAS holds all needed repositories; copy `.ssh` keys and important configuration files.

## 3 OS Installation and Hardening

### 3.1 AI Desktop (Ryzen 9)

1. Boot from the Ubuntu 25.10 USB. Choose **Install Ubuntu** and select normal installation.
    
2. At partitioning:
    
    - Use **LVM with encryption** to protect the system drive. Allocate 512 GB for `/` and the rest for `/home` and swap. Enable `LUKS` encryption.
        
    - Leave unallocated space if you expect to dual‑boot.
        
3. Create the user `cam` with a strong passphrase (use password manager). Enable **Secure Boot** and **TPM** if supported. Use encryption keys backed up to `pass.md` (local only).
    
4. After first boot, run system updates:
    
    `sudo apt update && sudo apt full-upgrade -y sudo apt install -y build-essential git curl gnupg lsb-release`
    
5. Configure the hostname (e.g., `ai-desktop`) and static IP if desired via `netplan`; otherwise rely on DHCP from the USG.
    
6. Set the timezone to America/New_York with `sudo timedatectl set-timezone America/New_York`.
    

### 3.2 XPS 13 (portable) & XPS 15 (workhorse)

1. For the **XPS 13**, decide whether to dual‑boot Windows 11 (for Copilot + PC features) or run Ubuntu in a VirtualBox/WSL environment. Because rootless Docker and microk8s require Linux, installing **Ubuntu 25.10** as a dual‑boot is recommended. Follow the same encrypted LVM steps as above.
    
2. For the **XPS 15**, install **Ubuntu Server 25.10** (headless). Partition as LVM‑encrypted; assign a static IP if necessary. Add a minimal GNOME desktop only if needed for AI Top GUI; otherwise use command‑line.
    
3. After installation, update the system packages and install basic utilities (`vim`, `htop`, `nfs-common`, `lm-sensors`). Add your `cam` user to the `sudo` group.
    

### 3.3 Jetson Orin Nano Super

1. **Flash JetPack 7 (Beta)** using NVIDIA SDK Manager on another Linux host:
    
    - Connect the Jetson to the host via USB‑C, put it in recovery mode and select **JetPack 7** with **Ubuntu 20.04** root filesystem.
        
    - Choose the NVMe or SD card as the target. When flashing completes, boot the Jetson and complete the on‑device configuration.
        
2. Expand the filesystem (`sudo nvme resize`) if using NVMe. Change the default password and disable unused services.
    
3. Update packages (`sudo apt update && sudo apt upgrade -y`). Install `curl`, `git`, `build-essential`, `python3-venv` and `nfs-common`.
    
4. Set a static IP via NetworkManager or leave DHCP; ensure the Jetson can resolve the cluster control node.
    

### 3.4 System Hardening

Follow general security best practices from CISA/NIST guidelines:

- **Harden the deployment environment** by running ML workloads inside hardened containers or VMs, monitoring the network, and configuring firewalls with allow‑lists[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L48-L53). Apply hardware vendor patches promptly[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L48-L53). Enable disk encryption and secure boot.
    
- **Protect sensitive AI information** by encrypting AI model weights, outputs and logs at rest and storing encryption keys in a secure hardware module or password manager[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L50-L52).
    
- **Use strong authentication and TLS** for all services, with MFA for administrative accounts[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L50-L53).
    

## 4 Container Runtime and Kubernetes Setup

### 4.1 Why microk8s?

Microk8s provides a **full CNCF‑compliant Kubernetes distribution** with low resource usage (~540 MB) and built‑in high‑availability clustering, GPU/FPGA acceleration, networking, service mesh and observability[microk8s.io](https://microk8s.io/compare#:~:text=Feature%20,O). It is **easy to install** with a single command and manages automatic updates and self‑healing control plane[microk8s.io](https://microk8s.io/compare#:~:text=Selecting%20your%20lightweight%20Kubernetes%20distribution,familiarity%20with%20the%20Kubernetes%20internals). Microk8s is therefore ideal for a home lab cluster spanning both x86‑64 and ARM devices.

### 4.2 Install microk8s on each node

1. On **all Ubuntu hosts (desktop, XPS machines, Jetson)** run:
    
    `sudo snap install microk8s --classic --channel=latest/stable sudo usermod -a -G microk8s $(whoami) sudo chown -f -R $(whoami) ~/.kube`
    
    Log out and back in for group changes to take effect.
    
2. Check status:
    
    `microk8s status --wait-ready microk8s kubectl get nodes`
    
3. Enable essential add‑ons:
    
    `microk8s enable dns storage helm3 metrics-server dashboard microk8s enable gpu        # on AI desktop and Jetson for NVIDIA GPU scheduling microk8s enable hostpath-storage registry  # local registry for images`
    
    The built‑in registry is exposed on `NodePort 32000` and uses a 20 Gi persistent volume[microk8s.io](https://microk8s.io/docs/registry-built-in#:~:text=Having%20a%20private%20Docker%20registry,to%20limit%20access%20to%20it). Do **not** expose this port to the internet; restrict it to the VLAN using firewall rules.
    
4. (Optional) To isolate Kubernetes inside a VM, you may install **LXD** and create LXD VMs instead of containers. Microk8s recommends using LXD VMs for Kubernetes experiments because privileged containers can expose the host; VMs provide proper isolation[microk8s.io](https://microk8s.io/docs/install-lxd#:~:text=MicroK8s%20can%20also%20be%20installed,need%20for%20multiple%20physical%20hosts). Install LXD if you need to run additional sandboxed clusters:
    
    `sudo snap install lxd sudo lxd init            # accept defaults or specify ZFS/LVM storage lxc launch ubuntu:25.10 k8s-vm --vm -c limits.cpu=4 -c limits.memory=8GB lxc exec k8s-vm -- sudo snap install microk8s --classic`
    

### 4.3 Join nodes into a cluster

1. Choose the **AI Desktop** as the cluster **control plane**. On the desktop, run:
    
    `microk8s add-node`
    
    The command prints a join token (e.g., `microk8s join 192.168.1.100:25000/abcdef...`). Note the IP address and token.
    
2. On each **worker node** (XPS 13, XPS 15, Jetson): run the join command using the token:
    
    `microk8s join 192.168.1.100:25000/<token>`
    
3. Verify the cluster:
    
    `microk8s kubectl get nodes`
    
    Nodes should show `Ready` with `worker` or `control-plane` roles.
    

### 4.4 Install rootless Docker for development

Microk8s uses containerd internally but you will still need **Docker Engine** for building images and running local containers. Use the rootless installation to avoid daemon privileges:

`curl -fsSL https://get.docker.com/rootless | sh # Add environment variables to ~/.bashrc: export PATH=$HOME/bin:$PATH export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock # Start rootless docker systemctl --user start docker systemctl --user enable docker`

Test with `docker run hello-world`.

Add your user to the `docker` group for convenience:

`sudo usermod -aG docker $(whoami)`

## 5 NAS Integration and Secure Storage

### 5.1 Configure NFS on the NAS

1. On the WD NAS, enable **NFSv4**. Edit `/etc/exports` (or NAS GUI) to export a directory (e.g., `/srv/nas/k8s`) with restricted access:
    

`/srv/nas/k8s  192.168.1.0/24(rw,sync,no_subtree_check,fsid=0)`

Using `rw,sync,no_subtree_check` ensures writes are committed safely and subtree checking is disabled to avoid path traversal issues[help.ubuntu.com](https://help.ubuntu.com/community/NFSv4Howto#:~:text=,0%2F24). Restrict the export to your LAN subnet.

2. Apply the export configuration (`exportfs -ra`) and, if necessary, firewall off NFS from guest networks.
    

### 5.2 Mount the NAS on each host

1. Install NFS client packages:
    
    `sudo apt install -y nfs-common`
    
2. Create mount points (choose directories used by microk8s persistent volumes and your home directory):
    
    `sudo mkdir -p /mnt/nas/share sudo mkdir -p /mnt/nas/registry`
    
3. Add entries to `/etc/fstab` to mount at boot:
    
    `192.168.1.10:/srv/nas/k8s   /mnt/nas/share    nfs4    rw,sync,hard,noatime    0  0 192.168.1.10:/srv/nas/registry /mnt/nas/registry nfs4 rw,sync,hard,noatime    0  0`
    
    Replace `192.168.1.10` with the NAS IP. The `hard` option ensures the NFS client retries indefinitely; `noatime` improves performance.
    
4. Mount all file systems:
    
    `sudo mount -a`
    

### 5.3 Expose NAS storage to microk8s

1. Enable the `hostpath-storage` add‑on (`microk8s enable hostpath-storage`) or install the **NFS CSI driver** via Helm. For NFS:
    
    `helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts helm install nfs-csi csi-driver-nfs/nfs-csi-driver --namespace kube-system --set node.enableController=true`
    
2. Create a `StorageClass` referencing the NFS server and export path, then create `PersistentVolume` objects for data and container registry. This ensures stateful workloads (e.g., databases, NIM models) are stored on the NAS.
    
3. When deploying workloads via Helm or YAML, specify the appropriate `storageClassName` to use the NAS volumes.
    

### 5.4 Local container registry and secret storage

- **Microk8s registry:** already enabled in §4.2; push images with:
    
    `docker tag my-image localhost:32000/my-image docker push localhost:32000/my-image`
    
- **Secret vault:** deploy **HashiCorp Vault** via Helm to store API keys, model weights and encryption keys. Example:
    
    `helm repo add hashicorp https://helm.releases.hashicorp.com helm install vault hashicorp/vault --set server.dev.enabled=true -n vault --create-namespace`
    
    For production, configure persistent storage, TLS certificates and enable unsealing keys. Use `vault kv put` to store secrets. Reference these secrets in microk8s workloads via Kubernetes `Secret` resources.
    
- **Local API storage for security:** run `n8n` or your API micro‑services within the cluster, exposing them only through an internal ingress. Use TLS and authentication. If you expose an API to the LAN (e.g., to serve NIM endpoints), use Kubernetes Ingress with `nginx` and restrict by IP.
    

## 6 Gigabyte AI Top Utility & Hardware Optimization

### 6.1 Install AI Top Utility

1. Download **Gigabyte AI TOP Utility 4.1.1** from the Gigabyte support page for the RTX 5070 Ti. Verify checksums.
    
2. On the AI desktop, install dependencies:
    
    `sudo apt install -y libgtk-3-0 libnss3 libasound2 chmod +x gigabyte-ai-top-utility*.run sudo ./gigabyte-ai-top-utility*.run`
    
3. Run the utility as your user (no sudo). The tool monitors CPU/GPU sensors and provides dynamic PBO and VRAM pooling features. Use it to:
    
    - Configure **workload‑adaptive PBO**: automatically switch between AMD Precision Boost Overdrive profiles depending on Slurm queue jobs. This leverages the Active OC Tuner while retaining BIOS defaults, as suggested in the hardware specialization document.
        
    - Enable **SSD‑VRAM hybrid pooling**: mount the NVMe drive within AI TOP to cache large models and quantized weights; this reduces VRAM pressure and allows running 70B models by offloading to the Samsung 9100 PRO NVMe.
        
    - Script **fan and power curves**: integrate AI TOP telemetry with `lm-sensors` or `nvidia-smi` to ramp HYTE Q60 fans based on GPU load. This ensures stable temperatures under sustained workloads.
        
4. For automation, integrate AI TOP with **Slurm** and `ryzenadj`. Use Slurm job prolog/epilog scripts to adjust PBO undervolt/overclock parameters before and after GPU‑heavy jobs. This approach keeps the system reversible and safe.
    

### 6.2 Integrate AI Top with microk8s

- Expose AI TOP metrics via Prometheus exporter or custom script so that the Kubernetes cluster can schedule workloads based on thermal or power headroom. For example, write a script that publishes CPU/GPU temperatures to a `NodeFeature` resource; the scheduler can then avoid overloading the desktop when it is thermally saturated.
    
- Use the **GPU add‑on** (`microk8s enable gpu`) to automatically configure NVIDIA drivers and device plugin on both the desktop and Jetson. Verify with `microk8s kubectl describe node ai-desktop | grep nvidia.com/gpu`.
    

## 7 Jetson Orin Nano Super Setup

1. After OS installation (§3.3), install **CUDA**, **TensorRT**, **cuDNN** and Jetson‑optimised drivers. JetPack 7 should preinstall these components.
    
2. Install **microk8s** on the Jetson as described in §4.2. Use the `arm64` snap channel if necessary: `sudo snap install microk8s --classic --channel=1.28/stable`.
    
3. Enable the `gpu` add‑on to schedule pods on the Jetson’s Ampere GPU. Validate with `microk8s kubectl describe node jetson | grep nvidia.com/gpu`.
    
4. Join the Jetson to the cluster (token created on the desktop). The Jetson will then be available for edge inference workloads; you can schedule low‑power AI tasks (e.g., video decode pipelines, streaming analytics) to it, offloading heavy training to the desktop.
    
5. To maximize performance:
    
    - Use the **M.2 NVMe** drive for caching models and data; mount it as `/mnt/nvme-cache` and configure AI TOP’s SSD pooling if the utility supports the Jetson (it might not). Otherwise, rely on JetPack’s `jetson_clocks --max` to run at maximum clocks.
        
    - Use **nvidia-docker2** if necessary: `sudo apt install -y nvidia-docker2` and set `"default-runtime": "nvidia"` in `/etc/docker/daemon.json` for GPU access outside microk8s.
        

## 8 Orchestration, Workflows and Scheduling

### 8.1 Slurm for workload scheduling

1. Install Slurm on the desktop and optionally on the Jetson and XPS machines (`sudo apt install slurm-wlm`).
    
2. Configure `slurm.conf` to treat each host as a compute node with appropriate CPU/GPU resources. Use `Gres=gpu:N` to schedule GPUs. Set `ControlMachine=ai-desktop` and list each node under `NodeName` entries.
    
3. Create **job prolog/epilog scripts** that interface with AI TOP and `ryzenadj` to adjust CPU frequencies, enable/disable PBO and manage SSD caching depending on job type. This realises the dynamic multi‑core scaling described in the hardware specialities document.
    
4. Use `srun` or `sbatch` to submit tasks; integrate with microk8s by running `kubectl` commands inside Slurm jobs to launch pods. Alternatively, use the **Slurm operator** for Kubernetes.
    

### 8.2 n8n automation and NIM interface hub

1. Deploy **n8n** (open‑source workflow automation) as a Kubernetes deployment. Use the local NAS volume for persistent data and set environment variables for your secrets (from Vault). Configure n8n to trigger on new documents in Obsidian or NAS, to run inference pipelines, or to call the **NVIDIA NIM** API locally.
    
2. Install **NIM interface hub** containers from NVIDIA NGC using the built‑in registry. Fine‑tune NIM models with your own data stored on the NAS. Always scan models for malicious code and verify their integrity before loading[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L61-L64).
    
3. Integrate n8n with **Obsidian** using the community plugin and API tokens. Automate summarisation of your research notes, call NIM models for question answering, and schedule model training tasks via Slurm.
    

## 9 Security Hardening & Monitoring

### 9.1 Access controls and secrets

- Enforce **role‑based access control** (RBAC) within Kubernetes and ABAC where feasible. Limit who can deploy workloads, access secrets and view logs[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L82-L85).
    
- Use **MFA** for all administrative logins (SSH, Vault UI, n8n, Kubernetes dashboard). Implement **fail2ban** on SSH to block brute‑force attempts.
    
- Store all keys and API tokens in **HashiCorp Vault**; never hard‑code secrets in YAML files. Use Kubernetes `Secret` objects sourced from Vault.
    

### 9.2 Network and API security

- Adopt a **Zero‑Trust** mindset: assume breach and isolate services. Place internal services (registry, Vault, n8n) behind a **reverse proxy** (e.g., Traefik or Nginx Ingress) with TLS certificates. Only expose necessary ports to your LAN. Use firewall rules on the USG to restrict external access.
    
- For any API you expose, implement authentication and authorization, validate inputs and sanitise to reduce injection attacks[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L70-L73).
    

### 9.3 Monitoring and auditing

- Enable **microk8s dashboard** and deploy **Prometheus** and **Grafana** via Helm for metrics collection. Export AI TOP telemetry to Prometheus.
    
- Use **Elasticsearch** or **Loki** for log aggregation. Monitor system logs for anomalies and configure alerts.
    
- Schedule regular security audits and penetration tests to identify vulnerabilities[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L88-L89). Keep software up‑to‑date and apply patches promptly[github.com](https://github.com/Cpsimer/infrastructure-setup/blob/HEAD/PKTFDM/4Data%20Management/Ai%20Data%20Management/Deploying%20AI%20Systems%20Securely.md#L48-L53).
    

## 10 Validation & Testing

1. **Verify cluster health:** run `microk8s kubectl get nodes,pods -A`. Deploy a test pod (e.g., nginx) and confirm it schedules correctly. Test GPU scheduling with a CUDA‑based pod (e.g., `nvidia-smi` container).
    
2. **Test NAS mounts:** create and write files from each node to `/mnt/nas/share` and confirm data consistency and throughput.
    
3. **Push/pull images** to the local registry and deploy them via microk8s. Validate that images stored on the NAS persist after node reboots.
    
4. **Check secret retrieval:** store a dummy secret in Vault and mount it into a test pod via Kubernetes secret; verify it appears in the environment.
    
5. **Run AI workloads:** deploy a small NIM model via n8n; confirm latency and throughput; monitor AI TOP metrics and adjust PBO/fan curves as necessary.
    
6. **Evaluate Jetson integration:** schedule a lightweight inferencing job on the Jetson and measure power draw and performance. Use `jetson_stats` to monitor.
    

## 11 Future Iterations

- **Network enhancements:** later iterations may introduce VLAN segmentation, 10 GbE networking, or WireGuard tunnels. For now, maintain existing topology.
    
- **High‑availability:** add additional control plane nodes for microk8s. Microk8s automatically configures a distributed datastore (dqlite) for high availability[microk8s.io](https://microk8s.io/compare#:~:text=Selecting%20your%20lightweight%20Kubernetes%20distribution,familiarity%20with%20the%20Kubernetes%20internals).
    
- **Sovereign AI platform:** integrate **NVIDIA Dynamo Platform** (Triton + NeMo) and **MAGNUM IO** for multi‑GPU multi‑node training once the base cluster is stable.
    
- **FAIR data management:** adopt the FAIR principles by attaching metadata to datasets, using persistent identifiers and licensing as described in the FAIR UST file. This ensures your data are Findable, Accessible, Interoperable and Reusable.
    

---

Following this plan will give you a secure, self‑contained AI infrastructure that leverages the capabilities of your AMD/NVIDIA hardware, supports both x86‑64 and ARM workloads and preserves complete control over your data. The cluster uses microk8s for orchestration, rootless Docker for development, a secured NAS for storage and a local registry and vault for secrets. With the Gigabyte AI Top Utility and Jetson Orin integration, you can maximize hardware utilization while maintaining privacy, flexibility and future scalability.