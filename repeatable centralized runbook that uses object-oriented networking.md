 reusable groups, profiles, and policies instead of one-off snowflakes. I’ll keep your edgerouter as the routing brain, UniFi as the SDN control plane, and wire everything so adoption “just works.”

# 0) Addressing and roles at a glance

- **Transit /30**: EdgeRouter eth0 = 192.168.254.1/30 → walljack → Express 7 **WAN** = 192.168.254.2/30, gw 192.168.254.1.
    
- **EdgeRouter owns** VLANs: 40=IoT, 50=Guest, 60=Honeypot, 99=Mgmt. It does NAT to ISP, DHCP for those VLANs, and points UniFi devices on them to the **desktop** controller.
    
- **Express 7 (Console mode)** owns “Trusted-Island” only: 10=Core, 20=NAS-Mgmt, 30=Personal-Wi-Fi. It does **its own DHCP + NAT** for these LANs and adopts the Flex Mini.
    
- **UC-AP-Pro** plugs into the **EdgeRouter’s switch fabric** (not the Express) so Guest/IoT are completely outside the island. Guest/IoT are isolated via VLANs and UniFi policy.
    

# 1) Physically cable it right

1. ONT → EdgeRouter WAN.
    
2. EdgeRouter **eth0** → walljack → Express 7 **WAN** (this is the /30 transit).
    
3. Express 7 **LAN** → Flex Mini uplink. All **your** wired devices connect to the Flex Mini/Express LAN.
    
4. UC-AP-Pro connects to the **EdgeRouter’s L2 side** (see step 3 for switch0/trunk) so it never hairpins through the island.
    
5. USG-A connects to EdgeRouter L2 in VLAN 60; USG-B on VLAN 99.
    

# 2) EdgeRouter configuration

Keep EdgeRouter online; you’ve already set **eth0 = 192.168.254.1/30**.

2.1 WAN and NAT

- WAN (DHCP/PPPoE) on your ISP port.
    
- Masquerade NAT out WAN. This remains the **primary** NAT.
    

2.2 Create L2 for UC-AP-Pro and USGs (switch0 / trunks)

- Put EdgeRouter LAN ports that feed UC-AP-Pro/USGs into **switch0**.
    
- On switch0, define tagged VLANs **40/50/99** toward the UC-AP-Pro port (TRUNK), and untag 99 for initial adoption if needed. This gives the router basic L2 so the AP can carry multiple SSIDs securely.
    

2.3 Create **VLAN interfaces** on EdgeRouter (only 40/50/60/99)

- **VLAN 99 (Mgmt)**: DHCP scope; **UniFi Controller** = desktop controller IP
	- so UC-AP-Pro and USGs adopt to the desktop server.
    
- **VLAN 40 (IoT)** and **VLAN 50 (Guest)**: DHCP scopes; **UniFi Controller** = desktop controller IP.
    
- **VLAN 60 (Honeypot)**: static honeypot IP for USG-A, DHCP optional.  
    EdgeOS 3.0’s DHCP server lets you set a **per-scope “UniFi Controller”** address so UniFi gear knows which controller to phone home to.
    

2.4 Edge firewall & honeypot redirect

- Standard stateful WAN policy, drop unsolicited inbound.
    
- DNAT anything “not explicitly allowed” to USG-A’s honeypot IP in VLAN 60. This is your decoy tripwire.
    

# 3)  UniFi controller — “Risky side” brain

- Bring up UniFi Network host and keep it **local-only** with scheduled backups.
    
- Create **VLAN-only Networks** for 40/50/60/99; do **not** set a UniFi gateway for them (EdgeRouter is the gateway).
    
- Adopt **UC-AP-Pro, USG-A, USG-B** here.
    
- Turn on **Default Security Posture = Block-All**, **Port AI** and **Channel AI**, and **CyberSecure** features where supported; Guest gets **Network & Client Isolation** and speed limits.
    

# 4) UC-AP-Pro on the Edge side

- SSID-IoT → VLAN 40, egress-only (DNS/HTTPS), block east-west.
    
- SSID-Guest → VLAN 50, **Client Isolation** + **Network Isolation**, apply bandwidth caps.
    
- Keep firmware at or above the required versions to use Channel AI and Port AI effectively.
    

# 5) Express 7 in **Console mode** — “Trusted-Island” brain

5.1 First-boot wizard

- Choose **Console mode**.
    
- **WAN (Express)**: Static **192.168.254.2/30**, gateway **192.168.254.1** (EdgeRouter).
    
- **LAN**: Create Networks
    
    - VLAN 10 “Core” 10.0.10.0/24
        
    - VLAN 20 “NAS-Mgmt” 10.0.20.0/24
        
    - VLAN 30 “Personal-Wi-Fi” 10.0.30.0/24
        
- Enable **DHCP** for these networks and **NAT** out the Express WAN.  
    This keeps your island’s addressing identical to your earlier plan, but now it’s **hidden** behind Express NAT.
    

5.2 Adopt the Flex Mini into the Express site

- Uplink Flex Mini port profile = **TRUNK-ALL** (tags 10/20/30).
    
- Edge ports for your gear: **ACCESS-Core (10)**, **ACCESS-NAS (20)**.
    
- If you broadcast a personal SSID from the Express, map it to **VLAN 30**.
    
- Set the Express site’s Default Security Posture to **Block-All** too, so nothing leaks by default.
    

# 6) Keep the two controllers from “stealing” devices

- Because the Express is its own console **only for VLANs 10/20/30**, it should also serve DHCP there (so its devices auto-adopt to the Express).
    
- On **EdgeRouter** DHCP scopes for 40/50/60/99, set **UniFi Controller = desktop controller IP** so UC-AP-Pro and USGs join the desktop site. EdgeOS 3.0 supports a per-scope controller field.
    

# 7) Double-NAT specifics and port-forwarding

- **Outbound traffic** from the island is fine through two layers of NAT.
    
- If you need inbound to island hosts (e.g., SSH to AI desktop), mirror forwards:
    
    1. EdgeRouter: WAN → 192.168.254.2 (Express WAN).
        
    2. Express: WAN → target host on VLAN 10/20/30.
        
- Prefer reverse tunnels or WireGuard if you can avoid inbound — simpler and safer.
    

# 10) Why this maximizes your hardware

- EdgeRouter remains the hardened internet edge and policy blade; UniFi Network (two small “brains”) gives you SDN-style object management, Block-All isolation, and analytics on both sides.
    
- The Express + Flex Mini become a **performance island** for your AI desktop/Jetson/NAS with zero RF risk from the UC-AP-Pro side. Endpoints and service placement match exactly what your central plan calls out.