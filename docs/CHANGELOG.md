# Homelab Changelog

---

## 2026-06-22 — Azure Arc + Microsoft Sentinel + Hybrid SIEM Integration

### Cloud Infrastructure
- Created Microsoft Azure tenant (Azure for Students — $100 credit)
- Configured Microsoft Entra ID with users, security groups, and RBAC roles
- Created `homelab-rg` resource group in Central US
- Deployed Log Analytics Workspace (`homelab-laws`) in Central US
- Deployed Microsoft Sentinel on top of Log Analytics workspace
- Installed Windows Security Events content hub solution (v3.0.12)
- Connected Sentinel workspace to Microsoft Defender XDR unified portal

### Azure Arc Onboarding
- Onboarded Windows Server 2022 (`WIN-N4GSIAT5P7D`) to Azure Arc
- Onboarded Windows 11 Pro (`DESKTOP-AJ9B6KI`) to Azure Arc
- Both on-premises machines registered as Azure-managed resources
- Azure Connected Machine Agent installed and verified on both endpoints

### Data Collection and Detection
- Created Data Collection Rule (`homelab-windows-dcr`) via Azure Monitor Agent
- Configured collection of All Security Events from both endpoints
- Validated log ingestion via KQL query in Microsoft Defender Advanced Hunting
- Confirmed Event ID 4624 (successful logon) streaming from `DESKTOP-AJ9B6KI.homelab.local`
- Confirmed Event ID 4672 (privileged logon) streaming from `DESKTOP-AJ9B6KI.homelab.local`

### ISP Migration and Troubleshooting
- Migrated from AT&T Internet Air to T-Mobile 5G Home Internet
- Diagnosed pfSense VM stopped state in Proxmox after ISP swap
- Updated Proxmox management IP from 192.168.1.50 to 192.168.12.x subnet
- Confirmed pfSense WAN auto-pulled new IP (192.168.12.121/24) from T-Mobile gateway
- Confirmed VLAN 10 (10.10.10.1) and VLAN 30 (10.30.30.1) remained intact after recovery
- Twingate connector re-established outbound tunnel automatically after WAN recovery

---

## 2026-03-31 — Wazuh + RDP + Inter-VLAN Routing

- Deployed Wazuh SIEM on Ubuntu server via Docker
- Onboarded Windows Server 2022 as monitored Wazuh agent
- Configured inter-VLAN routing between VLAN 10 and VLAN 30
- Implemented firewall rules for controlled RDP access
- Validated traffic flow and troubleshooting methodology
- Simulated failed authentication attempts and investigated events in Wazuh
- Mapped detections to MITRE ATT&CK (T1110 Brute Force, T1078 Valid Accounts)

---

## 2026-03-03 — Initial Infrastructure Deployment

### Infrastructure
- Proxmox hypervisor deployed (bare metal)
- pfSense firewall VM deployed on Proxmox
- Managed switch configured with 802.1Q VLAN trunking
- Ubuntu server deployed running Docker
- Node.js helpdesk portal deployed as Docker container

### Networking
- VLAN 10 — Internal LAN (10.10.10.0/24)
- VLAN 30 — Server network (10.30.30.0/24)
- Inter-VLAN routing configured via pfSense
- Outbound NAT verified

### Troubleshooting
- APIPA addresses (169.x.x.x) caused by incorrect VLAN tagging
- Trunk port misconfiguration on managed switch
- PVID alignment errors resolved
- Missing pfSense firewall rules identified and corrected
