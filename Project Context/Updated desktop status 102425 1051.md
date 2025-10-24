# Desktop Node Status Report
Generated: $(date)
Hostname: $(hostname)

---

## System Information
```
Linux schlimers-server 6.17.0-5-generic #5-Ubuntu SMP PREEMPT_DYNAMIC Mon Sep 22 10:00:33 UTC 2025 x86_64 GNU/Linux
Distributor ID:	Ubuntu
Description:	Ubuntu 25.10
Release:	25.10
Codename:	questing
```

## CPU Information
```
Model name:                              AMD Ryzen 9 9900X 12-Core Processor
Thread(s) per core:                      2
Core(s) per socket:                      12
Socket(s):                               1
CPU(s) scaling MHz:                      89%
CPU max MHz:                             5662.0161
CPU min MHz:                             613.9540
model name	: AMD Ryzen 9 9900X 12-Core Processor
```

## Memory Information
```
               total        used        free      shared  buff/cache   available
Mem:           115Gi       4.8Gi       106Gi        93Mi       5.2Gi       110Gi
Swap:           16Gi          0B        16Gi

```

## GPU Information
```
Fri Oct 24 10:47:58 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.95.05              Driver Version: 580.95.05      CUDA Version: 13.0     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 5070 Ti     Off |   00000000:01:00.0 Off |                  N/A |
|  0%   34C    P8             20W /  300W |      40MiB /  16303MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            2901      G   /usr/bin/gnome-shell                      3MiB |
|    0   N/A  N/A           41656      G   ...ess --variations-seed-version         16MiB |
+-----------------------------------------------------------------------------------------+

name, driver_version, memory.total [MiB], compute_cap
NVIDIA GeForce RTX 5070 Ti, 580.95.05, 16303 MiB, 12.0
```

## Storage Information
```
NAME          SIZE TYPE MOUNTPOINT                   FSTYPE
loop0       183.6M loop /snap/chromium/3282          squashfs
loop1        63.8M loop /snap/core20/2669            squashfs
loop2        73.9M loop /snap/core22/2133            squashfs
loop3           4K loop /snap/bare/5                 squashfs
loop4        66.8M loop /snap/core24/1196            squashfs
loop5       516.2M loop /snap/gnome-42-2204/226      squashfs
loop6        73.9M loop /snap/core22/2139            squashfs
loop7        13.5M loop /snap/kubectl/3677           squashfs
loop8        47.6M loop /snap/cups/1112              squashfs
loop9        24.7M loop /snap/data-science-stack/49  squashfs
loop11       91.7M loop /snap/gtk-common-themes/1535 squashfs
loop12       12.6M loop /snap/nmap/4210              squashfs
loop13      175.9M loop /snap/microk8s/8492          squashfs
loop14      113.7M loop /snap/lxd/36340              squashfs
loop15      128.9M loop /snap/obsidian/50            squashfs
loop16       10.8M loop /snap/snap-store/1270        squashfs
loop17       50.8M loop /snap/snapd/25202            squashfs
loop18       17.5M loop /snap/snap-store/1300        squashfs
loop19       50.9M loop /snap/snapd/25577            squashfs
loop20      229.6M loop /snap/vault/2454             squashfs
loop21       66.8M loop /snap/core24/1225            squashfs
loop22      113.7M loop /snap/lxd/36347              squashfs
nvme0n1       1.8T disk                              
├─nvme0n1p1   512M part /boot/efi                    vfat
├─nvme0n1p2   1.8T part /                            ext4
└─nvme0n1p3  16.8G part [SWAP]                       swap

Filesystem      Size  Used Avail Use% Mounted on
tmpfs            24G  2.8M   24G   1% /run
/dev/nvme0n1p2  1.8T   61G  1.7T   4% /
tmpfs            58G   57M   58G   1% /dev/shm
efivarfs        128K   26K   98K  22% /sys/firmware/efi/efivars
tmpfs           5.0M   16K  5.0M   1% /run/lock
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
tmpfs           1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
tmpfs            58G   13M   58G   1% /tmp
/dev/nvme0n1p1  511M  5.4M  506M   2% /boot/efi
tmpfs            12G  148K   12G   1% /run/user/1000
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/4396b8c173064b4a93c4dd07e967401bb40695ce264dedbd53e6c3b9ee715c55/shm
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/23b8f5c10b6399313366a199a24d219162231620d7d0252f0c4c29fb287075cc/shm
shm              64M     0   64M   0% /var/snap/microk8s/common/run/containerd/io.containerd.grpc.v1.cri/sandboxes/f2a3db136833980160ce04769fd60aebec712ed6433e0cf64dc48b6996216432/shm

Node                  Generic               SN                   Model                                    Namespace  Usage                      Format           FW Rev  
--------------------- --------------------- -------------------- ---------------------------------------- ---------- -------------------------- ---------------- --------
/dev/nvme0n1          /dev/ng0n1            S7YCNJ0Y309094L      Samsung SSD 9100 PRO 2TB                 0x1         87.37  GB /   2.00  TB    512   B +  0 B   0B2QNXH7
Smart Log for NVME device:nvme0 namespace-id:ffffffff
critical_warning			: 0
temperature				: 40 °C (313 K)
available_spare				: 100%
available_spare_threshold		: 10%
percentage_used				: 0%
endurance group critical warning summary: 0
Data Units Read				: 472880 (242.11 GB)
Data Units Written			: 5527148 (2.83 TB)
host_read_commands			: 5760563
host_write_commands			: 26609742
controller_busy_time			: 159
power_cycles				: 163
power_on_hours				: 46
unsafe_shutdowns			: 110
media_errors				: 0
num_err_log_entries			: 0
Warning Temperature Time		: 0
Critical Composite Temperature Time	: 0
Temperature Sensor 1			: 42 °C (315 K)
Temperature Sensor 2			: 40 °C (313 K)
Thermal Management T1 Trans Count	: 0
Thermal Management T2 Trans Count	: 0
Thermal Management T1 Total Time	: 0
Thermal Management T2 Total Time	: 0
```

## Network Configuration
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp6s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 10:ff:e0:b4:b7:c4 brd ff:ff:ff:ff:ff:ff
    altname enx10ffe0b4b7c4
    inet 10.0.10.138/24 brd 10.0.10.255 scope global dynamic noprefixroute enp6s0
       valid_lft 83769sec preferred_lft 83769sec
    inet6 fe80::e2a6:66fb:566:fe1e/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: wlp7s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether ac:36:1b:ea:89:6b brd ff:ff:ff:ff:ff:ff
    altname wlxac361bea896b
4: cali5964e41fcbd@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netns cni-12f76c7f-a9e4-ed46-7435-9709512836bb
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
5: cali4233be410bf@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netns cni-079af9a9-3cd0-d59c-e72c-776ce2c44b9b
    inet6 fe80::ecee:eeff:feee:eeee/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
6: vxlan.calico: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 66:2f:57:00:86:ee brd ff:ff:ff:ff:ff:ff
    inet 10.1.66.192/32 scope global vxlan.calico
       valid_lft forever preferred_lft forever
    inet6 fe80::642f:57ff:fe00:86ee/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever

default via 10.0.10.1 dev enp6s0 proto dhcp src 10.0.10.138 metric 100 
10.0.10.0/24 dev enp6s0 proto kernel scope link src 10.0.10.138 metric 100 
blackhole 10.1.66.192/26 proto 80 
10.1.66.199 dev cali5964e41fcbd scope link 
10.1.66.200 dev cali4233be410bf scope link 

# This is /run/systemd/resolve/stub-resolv.conf managed by man:systemd-resolved(8).
# Do not edit.
#
# This file might be symlinked as /etc/resolv.conf. If you're looking at
# /etc/resolv.conf and seeing this text, you have followed the symlink.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "resolvectl status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs should typically not access this file directly, but only
# through the symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a
# different way, replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
options edns0 trust-ad
search .
```

## Installed Container Runtimes
```
=== Docker ===
Docker version 28.5.1, build e180ab8
Client: Docker Engine - Community
 Version:    28.5.1
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.29.1
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.40.2
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 1
 Server Version: 28.5.1
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: true
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

=== MicroK8s ===
name:      microk8s
summary:   Kubernetes for workstations and appliances
publisher: Canonical**
store-url: https://snapcraft.io/microk8s
contact:   https://github.com/canonical/microk8s
license:   Apache-2.0
description: |
  MicroK8s is a small, fast, secure, certified Kubernetes distribution that
  installs on just about any Linux box. It provides the functionality of core
  Kubernetes components, in a small footprint, scalable from a single node to
  a high-availability production multi-node cluster. Use it for offline
  developments, prototyping, testing, CI/CD. It's also great for appliances -
  develop your IoT apps for K8s and deploy them to MicroK8s on your boxes.
commands:
  - microk8s.add-node
  - microk8s.addons
  - microk8s.config
  - microk8s.ctr
  - microk8s.dashboard-proxy
  - microk8s.dbctl
  - microk8s.disable
  - microk8s.enable
  - microk8s.helm
  - microk8s.helm3
  - microk8s.images
  - microk8s.inspect
  - microk8s.istioctl
  - microk8s.join
  - microk8s.kubectl
  - microk8s.leave
  - microk8s.linkerd
  - microk8s
  - microk8s.refresh-certs
  - microk8s.remove-node
  - microk8s.reset
  - microk8s.start
  - microk8s.status
  - microk8s.stop
  - microk8s.version
services:
  microk8s.daemon-apiserver-kicker: simple, enabled, active
  microk8s.daemon-apiserver-proxy:  simple, enabled, inactive
  microk8s.daemon-cluster-agent:    simple, enabled, active
  microk8s.daemon-containerd:       notify, enabled, active
  microk8s.daemon-etcd:             simple, enabled, inactive
  microk8s.daemon-flanneld:         simple, enabled, inactive
  microk8s.daemon-k8s-dqlite:       simple, enabled, active
  microk8s.daemon-kubelite:         simple, enabled, active
snap-id:      EaXqgt1lyCaxKaQCU349mlodBkDCXRcg
tracking:     latest/stable
refresh-date: yesterday at 00:30 EDT
channels:
  1.32/stable:           v1.32.9  2025-10-14 (8511) 172MB classic
  1.32/candidate:        v1.32.9  2025-10-14 (8511) 172MB classic
  1.32/beta:             v1.32.9  2025-10-14 (8511) 172MB classic
  1.32/edge:             v1.32.9  2025-10-13 (8511) 172MB classic
  latest/stable:         v1.34.1  2025-10-14 (8492) 184MB classic
  latest/candidate:      v1.34.1  2025-10-10 (8492) 184MB classic
  latest/beta:           v1.34.1  2025-10-10 (8492) 184MB classic
  latest/edge:           v1.34.1  2025-10-10 (8492) 184MB classic
  1.34-strict/stable:    v1.34.1  2025-10-16 (8447) 184MB -
  1.34-strict/candidate: v1.34.1  2025-09-24 (8447) 184MB -
  1.34-strict/beta:      v1.34.1  2025-09-24 (8447) 184MB -
  1.34-strict/edge:      v1.34.1  2025-09-10 (8447) 184MB -
  1.34/stable:           v1.34.1  2025-10-14 (8491) 184MB classic
  1.34/candidate:        v1.34.1  2025-10-10 (8491) 184MB classic
  1.34/beta:             v1.34.1  2025-10-10 (8491) 184MB classic
  1.34/edge:             v1.34.1  2025-10-10 (8491) 184MB classic
  1.33-strict/stable:    v1.33.5  2025-10-15 (8477) 177MB -
  1.33-strict/candidate: v1.33.5  2025-09-23 (8477) 177MB -
  1.33-strict/beta:      v1.33.5  2025-09-23 (8477) 177MB -
  1.33-strict/edge:      v1.33.5  2025-09-16 (8477) 177MB -
  1.33/stable:           v1.33.5  2025-10-14 (8507) 177MB classic
  1.33/candidate:        v1.33.5  2025-10-14 (8507) 177MB classic
  1.33/beta:             v1.33.5  2025-10-14 (8507) 177MB classic
  1.33/edge:             v1.33.5  2025-10-10 (8507) 177MB classic
  1.32-strict/stable:    v1.32.9  2025-10-14 (8476) 172MB -
  1.32-strict/candidate: v1.32.9  2025-09-18 (8476) 172MB -
  1.32-strict/beta:      v1.32.9  2025-09-18 (8476) 172MB -
  1.32-strict/edge:      v1.32.9  2025-09-16 (8476) 172MB -
  1.31-strict/stable:    v1.31.13 2025-10-07 (8455) 168MB -
  1.31-strict/candidate: v1.31.13 2025-09-16 (8455) 168MB -
  1.31-strict/beta:      v1.31.13 2025-09-16 (8455) 168MB -
  1.31-strict/edge:      v1.31.13 2025-09-10 (8455) 168MB -
  1.31/stable:           v1.31.13 2025-10-06 (8456) 168MB classic
  1.31/candidate:        v1.31.13 2025-09-15 (8456) 168MB classic
  1.31/beta:             v1.31.13 2025-09-15 (8456) 168MB classic
  1.31/edge:             v1.31.13 2025-09-10 (8456) 168MB classic
  1.30-strict/stable:    v1.30.14 2025-09-02 (8240) 169MB -
  1.30-strict/candidate: v1.30.14 2025-08-26 (8240) 169MB -
  1.30-strict/beta:      v1.30.14 2025-08-26 (8240) 169MB -
  1.30-strict/edge:      v1.30.14 2025-06-18 (8240) 169MB -
  1.30/stable:           v1.30.14 2025-09-01 (8248) 169MB classic
  1.30/candidate:        v1.30.14 2025-08-25 (8248) 169MB classic
  1.30/beta:             v1.30.14 2025-08-25 (8248) 169MB classic
  1.30/edge:             v1.30.14 2025-06-18 (8248) 169MB classic
  1.29-strict/stable:    v1.29.15 2025-04-01 (7969) 170MB -
  1.29-strict/candidate: v1.29.15 2025-03-30 (7969) 170MB -
  1.29-strict/beta:      v1.29.15 2025-03-30 (7969) 170MB -
  1.29-strict/edge:      v1.29.15 2025-04-09 (8045) 170MB -
  1.29/stable:           v1.29.15 2025-04-01 (8006) 170MB classic
  1.29/candidate:        v1.29.15 2025-04-01 (8006) 170MB classic
  1.29/beta:             v1.29.15 2025-04-01 (8006) 170MB classic
  1.29/edge:             v1.29.15 2025-04-09 (8050) 170MB classic
  1.28-strict/stable:    v1.28.15 2025-03-31 (7972) 186MB -
  1.28-strict/candidate: v1.28.15 2025-03-30 (7972) 186MB -
  1.28-strict/beta:      v1.28.15 2025-03-30 (7972) 186MB -
  1.28-strict/edge:      v1.28.15 2025-04-09 (8046) 187MB -
  1.28/stable:           v1.28.15 2025-04-02 (7971) 186MB classic
  1.28/candidate:        v1.28.15 2025-04-02 (7971) 186MB classic
  1.28/beta:             v1.28.15 2025-04-02 (7971) 186MB classic
  1.28/edge:             v1.28.15 2025-04-09 (8048) 187MB classic
  1.27-strict/stable:    v1.27.16 2024-11-04 (7275) 179MB -
  1.27-strict/candidate: v1.27.16 2024-11-04 (7275) 179MB -
  1.27-strict/beta:      v1.27.16 2024-11-04 (7275) 179MB -
  1.27-strict/edge:      v1.27.16 2024-10-16 (7275) 179MB -
  1.27/stable:           v1.27.16 2024-10-30 (7280) 179MB classic
  1.27/candidate:        v1.27.16 2024-10-30 (7280) 179MB classic
  1.27/beta:             v1.27.16 2024-10-30 (7280) 179MB classic
  1.27/edge:             v1.27.16 2024-10-16 (7280) 179MB classic
  1.26-strict/stable:    v1.26.15 2024-05-30 (6672) 173MB -
  1.26-strict/candidate: v1.26.15 2024-04-04 (6672) 173MB -
  1.26-strict/beta:      v1.26.15 2024-04-04 (6672) 173MB -
  1.26-strict/edge:      v1.26.15 2024-07-16 (6979) 173MB -
  1.26/stable:           v1.26.15 2024-04-19 (6673) 173MB classic
  1.26/candidate:        v1.26.15 2024-04-03 (6673) 173MB classic
  1.26/beta:             v1.26.15 2024-04-03 (6673) 173MB classic
  1.26/edge:             v1.26.15 2024-07-16 (6980) 173MB classic
  1.25-strict/stable:    v1.25.16 2024-02-21 (6575) 171MB -
  1.25-strict/candidate: v1.25.16 2024-02-21 (6575) 171MB -
  1.25-strict/beta:      v1.25.16 2024-02-21 (6575) 171MB -
  1.25-strict/edge:      v1.25.16 2024-02-20 (6575) 171MB -
  1.25-eksd/stable:      v1.25-18 2023-07-31 (5630) 176MB classic
  1.25-eksd/candidate:   v1.25-18 2023-07-21 (5630) 176MB classic
  1.25-eksd/beta:        v1.25-18 2023-07-21 (5630) 176MB classic
  1.25-eksd/edge:        v1.25-18 2023-07-20 (5630) 176MB classic
  1.25/stable:           v1.25.16 2024-02-21 (6571) 171MB classic
  1.25/candidate:        v1.25.16 2024-02-20 (6571) 171MB classic
  1.25/beta:             v1.25.16 2024-02-20 (6571) 171MB classic
  1.25/edge:             v1.25.16 2024-02-20 (6571) 171MB classic
  1.24-eksd/stable:      v1.24-30 2023-11-18 (6213) 172MB classic
  1.24-eksd/candidate:   v1.24-31 2023-11-28 (6280) 172MB classic
  1.24-eksd/beta:        v1.24-36 2024-02-08 (6479) 172MB classic
  1.24-eksd/edge:        v1.24-36 2024-02-01 (6479) 172MB classic
  1.24/stable:           v1.24.17 2023-09-01 (5872) 225MB classic
  1.24/candidate:        v1.24.17 2023-08-27 (5872) 225MB classic
  1.24/beta:             v1.24.17 2023-08-27 (5872) 225MB classic
  1.24/edge:             v1.24.17 2023-11-01 (6153) 225MB classic
  1.23-eksd/stable:      v1.23-33 2023-10-22 (6081) 168MB classic
  1.23-eksd/candidate:   ^                                
  1.23-eksd/beta:        ^                                
  1.23-eksd/edge:        ^                                
  1.23/stable:           v1.23.17 2023-03-30 (4916) 213MB classic
  1.23/candidate:        ^                                
  1.23/beta:             ^                                
  1.23/edge:             v1.23.17 2023-08-17 (5821) 213MB classic
  1.22-eksd/stable:      v1.22-26 2023-05-18 (5261) 164MB classic
  1.22-eksd/candidate:   ^                                
  1.22-eksd/beta:        ^                                
  1.22-eksd/edge:        ^                                
  1.22/stable:           v1.22.17 2023-04-29 (4915) 187MB classic
  1.22/candidate:        ^                                
  1.22/beta:             ^                                
  1.22/edge:             v1.22.17 2023-03-16 (4915) 187MB classic
  1.21/stable:           v1.21.13 2022-07-20 (3410) 191MB classic
  1.21/candidate:        ^                                
  1.21/beta:             ^                                
  1.21/edge:             ^                                
  1.20/stable:           v1.20.13 2021-12-08 (2760) 221MB classic
  1.20/candidate:        ^                                
  1.20/beta:             ^                                
  1.20/edge:             ^                                
  1.19/stable:           v1.19.15 2021-09-30 (2530) 216MB classic
  1.19/candidate:        ^                                
  1.19/beta:             ^                                
  1.19/edge:             ^                                
  1.18/stable:           v1.18.20 2021-07-12 (2271) 198MB classic
  1.18/candidate:        ^                                
  1.18/beta:             ^                                
  1.18/edge:             ^                                
  1.17/stable:           v1.17.17 2021-01-15 (1916) 177MB classic
  1.17/candidate:        ^                                
  1.17/beta:             ^                                
  1.17/edge:             ^                                
  1.16/stable:           v1.16.15 2020-09-12 (1671) 179MB classic
  1.16/candidate:        ^                                
  1.16/beta:             ^                                
  1.16/edge:             ^                                
  1.15/stable:           v1.15.11 2020-03-27 (1301) 171MB classic
  1.15/candidate:        ^                                
  1.15/beta:             ^                                
  1.15/edge:             ^                                
  1.14/stable:           v1.14.10 2020-01-06 (1120) 217MB classic
  1.14/candidate:        ^                                
  1.14/beta:             ^                                
  1.14/edge:             ^                                
  1.13/stable:           v1.13.6  2019-06-06  (581) 237MB classic
  1.13/candidate:        ^                                
  1.13/beta:             ^                                
  1.13/edge:             ^                                
  1.12/stable:           v1.12.9  2019-06-06  (612) 259MB classic
  1.12/candidate:        ^                                
  1.12/beta:             ^                                
  1.12/edge:             ^                                
  1.11/stable:           v1.11.10 2019-05-10  (557) 258MB classic
  1.11/candidate:        ^                                
  1.11/beta:             ^                                
  1.11/edge:             ^                                
  1.10/stable:           v1.10.13 2019-04-22  (546) 222MB classic
  1.10/candidate:        ^                                
  1.10/beta:             ^                                
  1.10/edge:             ^                                
installed:               v1.34.1             (8492) 184MB classic


```

## Slurm Status
```
Slurm NOT installed
```

## AI TOP Utility Status
```
AI TOP Utility installed at: /opt/gigabyte-ai-top-utility/
total 217M
-rw-r--r-- 1 root root 1.1K Sep 23 06:18 LICENSE.electron.txt
-rw-r--r-- 1 root root 8.8M Sep 23 06:18 LICENSES.chromium.html
-rw-rw-r-- 1 root root 228K Sep 23 06:18 THIRD-PARTY-NOTICES.txt
-rwxr-xr-x 1 root root  53K Sep 23 06:18 chrome-sandbox
-rw-r--r-- 1 root root 165K Sep 23 06:18 chrome_100_percent.pak
-rw-r--r-- 1 root root 224K Sep 23 06:18 chrome_200_percent.pak
-rwxr-xr-x 1 root root 1.3M Sep 23 06:18 chrome_crashpad_handler
-rwxr-xr-x 1 root root 169M Sep 23 06:18 gigabyte-ai-top-utility
-rw-r--r-- 1 root root  11M Sep 23 06:18 icudtl.dat
-rwxr-xr-x 1 root root 244K Sep 23 06:18 libEGL.so
-rwxr-xr-x 1 root root 6.3M Sep 23 06:18 libGLESv2.so
-rwxr-xr-x 1 root root 2.8M Sep 23 06:18 libffmpeg.so
-rwxr-xr-x 1 root root 4.1M Sep 23 06:18 libvk_swiftshader.so
-rwxr-xr-x 1 root root 7.2M Sep 23 06:18 libvulkan.so.1
drwxrwxr-x 2 root root 4.0K Oct 22 23:54 locales
drwxrwxr-x 3 root root 4.0K Oct 22 23:54 resources
-rw-r--r-- 1 root root 5.3M Sep 23 06:18 resources.pak
-rw-r--r-- 1 root root 271K Sep 23 06:18 snapshot_blob.bin
-rw-r--r-- 1 root root 628K Sep 23 06:18 v8_context_snapshot.bin
-rw-r--r-- 1 root root  107 Sep 23 06:18 vk_swiftshader_icd.json
```

## RyzenAdj Status
```
RyzenAdj NOT installed
```

## Currently Running Services
```
  snap.microk8s.daemon-apiserver-kicker.service loaded active running Service for snap application microk8s.daemon-apiserver-kicker
  snap.microk8s.daemon-cluster-agent.service    loaded active running Service for snap application microk8s.daemon-cluster-agent
  snap.microk8s.daemon-containerd.service       loaded active running Service for snap application microk8s.daemon-containerd
  snap.microk8s.daemon-k8s-dqlite.service       loaded active running Service for snap application microk8s.daemon-k8s-dqlite
  snap.microk8s.daemon-kubelite.service         loaded active running Service for snap application microk8s.daemon-kubelite
```

## Failed Services

```
AI TOP Utility installed at: /opt/gigabyte-ai-top-utility/

  UNIT          LOAD   ACTIVE SUB    DESCRIPTION
● munge.service loaded failed failed MUNGE authentication service

Legend: LOAD   → Reflects whether the unit definition was properly loaded.
        ACTIVE → The high-level unit activation state, i.e. generalization of SUB.
        SUB    → The low-level unit activation state, values depend on unit type.

1 loaded units listed.
```

## NFS Mounts
```

UUID=F3A0-4AD2 /boot/efi vfat defaults 0 1

UUID=f3a4b2a7-0de2-4484-86f4-cc77c00b4234 / ext4 defaults 0 1

UUID=0fbf925b-ffa1-45a3-9b30-0e71cbc6783c none swap sw 0 1 # UNCONFIGURED FSTAB FOR BASE SYSTEM
```

## Installed AI/ML Software
```
=== Python Packages ===
nvidia-ml-py              13.580.82

=== CUDA Toolkit ===
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2025 NVIDIA Corporation
Built on Wed_Aug_20_01:58:59_PM_PDT_2025
Cuda compilation tools, release 13.0, V13.0.88
Build cuda_13.0.r13.0/compiler.36424714_0
```

## System Load & Resources
```
 10:48:05 up 44 min,  1 user,  load average: 0.19, 0.27, 0.25

top - 10:48:05 up 44 min,  1 user,  load average: 0.19, 0.27, 0.25
Tasks: 568 total,   1 running, 562 sleeping,   0 stopped,   5 zombie
%Cpu(s):  2.8 us,  1.2 sy,  0.4 ni, 95.2 id,  0.4 wa,  0.0 hi,  0.0 si,  0.0 st 
MiB Mem : 118111.1 total, 108698.0 free,   5003.3 used,   5526.6 buff/cache     
MiB Swap:  17216.0 total,  17216.0 free,      0.0 used. 113107.8 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   2901 camr      20   0 5129276 389440 183604 S  18.2   0.3   0:39.55 gnome-s+
      1 root      20   0   27488  17268  10524 S   0.0   0.0   0:04.53 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0.0   0.0   0:00.00 pool_wo+
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      5 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      7 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
      8 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
     11 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
     13 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker+
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.01 ksoftir+
     15 root      20   0       0      0      0 I   0.0   0.0   0:00.73 rcu_pre+
```

---
Status collection complete. Share this file for analysis.
