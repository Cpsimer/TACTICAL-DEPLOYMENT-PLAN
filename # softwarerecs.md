# softwarerecs.md

Operating system choices.

Compute nodes (Desktop/XPs‑15/Jetson) – A Linux distribution optimized for HPC and container workloads (e.g., Ubuntu LTS, Rocky Linux or Debian) ensures stability and access to GPU drivers. Use the same OS across nodes to simplify management. For rolling updates or cutting‑edge packages, Arch‑based Omarchy works, but pair it with pinned kernels and LTS alternatives for stability. Always enable full‑disk encryption and Secure Boot.

New laptop – Install a lightweight but compatible Linux (e.g., Ubuntu or Debian), especially since it will act as a Slurm/K3s client. A minimal install with docker, nfs-common, Slurm client and Kubernetes agent keeps overhead low. Use full‑disk encryption and set static IPs.

NAS/Vault – Continue with TrueNAS SCALE or a 45Drives Storinix system for ZFS and Vault integration; these appliances provide integrated backup, replication and UI management.

Container and orchestration stack.

Use Docker Engine for container builds and K3s or MicroK8s for orchestration. K3s is well‑suited to resource‑constrained nodes and avoids Snap dependencies. Deploy the NVIDIA GPU Operator for automatic GPU driver management.

Slurm remains the scheduler for batch HPC jobs. Configure the Desktop as controller and run slurmd on each worker.

AI and data science stack.

PyTorch and TensorRT/Triton for training and inference; these align with your current GPU capabilities.

Use MLflow for experiment tracking; n8n for automation; FastAPI for microservices like T‑Rex classifier; Obsidian for note management.

Adopt RAPIDS for accelerated data processing on GPUs; it integrates with Dask for distributed workloads.

MLOps and observability tools.

Implement Prometheus and Grafana for metrics and dashboards; Loki for logs.

Use Git (self‑hosted Gitea) and Git LFS for version control of code and models. Integrate with a CI/CD pipeline (e.g., Jenkins or GitHub Actions) that runs in your intranet.

Consider DVC or Weights & Biases for data versioning and experiment management; both can operate on-premises.

Security stack.

Deploy HashiCorp Vault for secrets management and integrate Vault Agent across nodes (as in Phase 5).

Use WireGuard if remote VPN access is required, but avoid opening inbound ports; keep the intranet isolated.

Maintain an immutable backup system and test disaster recovery procedures
