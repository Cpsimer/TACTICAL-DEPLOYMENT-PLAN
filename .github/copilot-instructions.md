# Copilot Instructions
## Core Architecture
- Primary compute: AMD Ryzen 9 9900X desktop with RTX 5070 Ti (16GB) orchestrates GPUs while XPS 15 handles CPU services and XPS 13 acts as portable client; see README.md + Exact Device Hardware Specs.md.
- Jetson Orin Nano Super is the edge node (67 TOPS) slated for Slurm federation; align container builds with JetPack 7 once connected.
- Storage flows through the WD 2TB NAS (SSH/NFS) and the Samsung 9100 PRO NVMe scratch; keep NAS mounted for Obsidian vault and shared datasets.
- Network stays on the documented UniFi 2.5G topology (Network Topology.canvas); leave physical layout untouched until an explicit network iteration is scheduled.

## Orchestration & Runtime
- Transition plan centers on Slurm for scheduling across desktop/XPS/Jetson while MicroK8s (snap) remains active; consult System status 10-23-25 12-53am.md to confirm current services before reconfiguring.
- Containers must come from NVIDIA NGC builds (NIM, NeMo, Triton, CUDA-X); prefer rootless Docker (already running) and wrap Slurm prolog/epilog hooks for ryzenadj + telemetry (Maximizing specific hardware specialties.md).
- Gigabyte AI TOP Utility lives at /opt/gigabyte-ai-top-utility; keep it running for PBO/fan curve automation and expose telemetry to scheduling scripts instead of manual BIOS tweaks.
- Preserve security posture: run everything local-first, store secrets with pass, and avoid introducing cloud APIs without explicit approval.

## Daily Ops & Commands
- Validate base services first: `systemctl --user status docker`, `microk8s status --wait-ready`, and `systemctl --failed` (System status 10-23-25 12-53am.md shows two failed units worth clearing before new work).
- Treat Slurm enablement as WIP; keep scripts/jobs under version control and dry-run with `scontrol show config` once packages land.
- Mount checks: ensure NAS paths (see README.md architecture section) are present via `mount | grep nas` before jobs use shared storage.
- When building containers, rely on `docker buildx build --platform=linux/amd64` for desktop/XPS targets and `--platform=linux/arm64` for Jetson; push images to the NAS registry mirror if cloud registries are disallowed.

## Critical Workstreams
- Finalize NAS hardening: ensure SSL-accessible mounts for all nodes, document paths in README.md, and reflect permissions in future automation.
- Cluster configuration: author Slurm configs/job templates that mirror the existing service inventory (nim_inference, triton_server, nemo_training, etc.) from README.md.
- Local API storage: route model weights, Redis, and KV caches to the NAS or NVMe scratch with explicit mount checks; disallow ad-hoc ~/Downloads usage.
- Gigabyte AI TOP integration: script profile swaps via CLI or DBUS so Slurm jobs can request thermal/fan modes automatically.
- Jetson onboarding: prep ARM64 container manifests, validate RAPIDS/PyG optional installs, and describe federated workflow (desktop distills, Jetson runs edge inference).

## Knowledge & Workflow Resources
- PKTFDM/ holds planning, governance, and training material; pull process conventions from matching folders (e.g., 5Software Management/Software Configuration Management.md).
- Expert computing environment advice.md captures the authoritative deployment plan and required software stack order (Must/Should list); treat it as the source of truth for step sequencing.
- MoSCoW prioritization of software.md defines the acceptance bar—Must items must be satisfied before refactoring Should/Could components.
- Obsidian workflows (README.md) rely on the NAS share plus n8n automation; maintain the pipeline that pushes Git commits and MLflow outputs into the vault.
- Document new automations or deviations in README.md and link supporting notes inside PKTFDM so future agents can trace decisions quickly.

## Guardrails & Open Issues
- Network topology (Network Topology.canvas) is considered stable; avoid physical or VLAN changes until the planned “iterate later” phase.
- `pass` is referenced for secrets, but no pass store lives in this repo—verify the host’s password store before assuming availability.
- The system is currently in a degraded MicroK8s state; fix or document outstanding failed units before layering Slurm services to prevent cascading faults.
- Jetson onboarding requires JetPack 7 images and ARM64 builds; track those deliverables explicitly to avoid blocking the federation milestone.
