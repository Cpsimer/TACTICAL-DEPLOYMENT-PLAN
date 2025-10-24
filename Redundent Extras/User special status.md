# User Special Status & Developer Access

Your memberships essentially act as "keys" to the latest toolchains and firmware that the Slurm‑based plan depends on. Here's how each status maps to the components in the MoSCoW list and the new orchestration setup:

- **NVIDIA Developer & AI Aerial programs / NGC Catalog** – These accounts give you access to pre‑release CUDA toolkits, drivers and SDKs. For the Slurm cluster you’ll want to request the CUDA 13.1 toolkit, JetPack 7 beta for the Jetson Orin and RAPIDS 26.02 preview; these programmes routinely distribute them to registered developers. They also provide early versions of NeMo, NIM microservices, Triton and CUDA‑X libraries, so you can build Apptainer/Singularity images with the newest GPU support

- **NVIDIA 6G Developer** – This grants early access to the Aerial SDK and Magnum IO improvements. You can download the latest Aerial 25‑2 release and future 6G‑ready drivers directly from the portal. Even though these components are “should‑have” in your MoSCoW list, having them early lets you experiment with high‑throughput I/O and 6G research on the Jetson once it’s integrated via Slurm.

- **Docker Beta Developer Program** – Since you’re dropping Docker Swarm but keeping Docker Engine, this status is useful for trying upcoming Docker features (for example, improvements in `docker buildx` or experimental GPU‑sharing flags) before they hit general release. You can then wrap those containers inside Slurm jobs.

- **GitHub Student Developer Pack** – Use this to host your Slurm job scripts and container recipes in private repositories without cost. It also includes free credits for Codespaces and GitHub Actions, which can build your Apptainer images remotely and push them back to your cluster.

- **Google AI Pro / Azure Education Hub / Azure AI Foundry** – These cloud programmes grant you preview access to tools like Azure’s MLflow integration or Google’s Vertex AI SDKs. While your platform is local, they’re handy for off‑line testing of TensorFlow or JAX builds compiled against CUDA 13. You can download those binaries and run them locally under Slurm.

- **Meta Developer Models** – Through the Meta developer program you can legally download Llama 4 (Scout, Maverick) and other Llama 3.x models. These weights can then be quantised with TensorRT and served on your desktop via Slurm‑managed jobs, giving you access to state‑of‑the‑art LLMs without waiting for public release.

## Active Memberships

- Nvidia AI Aerial developer program
- Nvidia Developer Program
- Docker Beta Developer Program
- Nvidia 6g Developer Program
- NGC Catelog
- GitHub Student Developer pack
- Google AI Pro
- Azure Education Hub
- Azure AI Foundry
- Meta Developer Models

## Meta Developer Models Provided

- Llama 4 Scout
- Llama 4 Maverick
- Llama 3.3: 70B
- Llama 3.2: 1B & 3B
- Llama 3.2: 11B & 90B
- Llama 3.1: 8B & 405B
- Llama 3
- Llama Code
- Llama 2
- Llama Guard 2

## Additional Services

- Obsidian Sync + Obsidian Publish
- Icloud+ 50gb
- Student at University of Cincinnati

## References

- [[developer program]]
