# Homelab Capstone — Cybersecurity Home Lab

This repository documents an ongoing cybersecurity homelab designed to simulate a segmented enterprise environment with on-premises infrastructure, Active Directory, SIEM monitoring, and cloud security integration.

The lab is built to develop practical blue team, SOC analyst, and cloud security skills in a realistic hybrid environment.

---

## Infrastructure

### Hypervisor
- Proxmox VE (bare metal)

### Firewall / Router
- pfSense (virtual machine on Proxmox)

### Networking
- Managed switch with 802.1Q VLAN trunking
- Remote access via Twingate (zero trust network access)

### Servers
- Windows Server 2022 (Active Directory Domain Controller)
- Ubuntu Server (Docker host — Wazuh SIEM)

### Endpoints
- Windows 11 Pro (domain-joined workstation)

### Remote Access
- Microsoft Remote Desktop (Windows administration)
- Twingate connector deployed for secure remote access into homelab

---

## Network Architecture

VLAN segmentation enforced using pfSense with explicit inter-VLAN firewall rules.

| VLAN    | Purpose          | Subnet          |
|---------|------------------|-----------------|
| VLAN 10 | Management / LAN | 10.10.10.0/24   |
| VLAN 30 | Server Network   | 10.30.30.0/24   |

Traffic between VLANs requires explicit firewall rules and is intentionally restricted.

---

## Phase 1 — On-Premises Infrastructure

### Active Directory
- Windows Server 2022 promoted to Domain Controller
- Active Directory Domain Services configured
- DNS integrated with AD for domain resolution
- Windows 11 endpoint successfully joined to domain
- Domain users created: standard users, service accounts, admin accounts
- Authentication testing performed (successful and failed logons)

### Security Monitoring (Wazuh)
- Wazuh SIEM deployed on Ubuntu Server via Docker
- Wazuh agents installed on Windows Server 2022 and Windows 11
- Windows Security Event Logs ingested and normalized
- MITRE ATT&CK mapping configured (T1110 Brute Force, T1078 Valid Accounts)
- Failed authentication events detected and investigated
- Logon type analysis and source workstation identification practiced

### Completed Work
- pfSense deployed and configured on Proxmox
- VLAN 10 and VLAN 30 networks operational
- Inter-VLAN routing configured and validated
- Firewall rules implemented for controlled access
- Active Directory domain deployed and validated
- Windows 11 endpoint domain-joined and authenticated
- Wazuh SIEM deployed with multi-endpoint visibility
- Failed authentication attacks simulated and detected
- Remote access configured via Twingate

### Troubleshooting Highlights
- APIPA (169.x.x.x) addresses caused by incorrect VLAN tagging
- PVID misconfiguration on switch ports
- Trunk vs access port confusion on managed switch
- Firewall rules applied on incorrect interfaces
- DNS dependency issues during Active Directory promotion
- Wazuh agent registration and authentication failures

---

## Phase 2 — Cloud Security Integration

### Azure Hybrid Identity (Entra ID)
- Created Microsoft Azure tenant with full Global Administrator control
- Configured Microsoft Entra ID with users, security groups, and RBAC roles
- Mirrored on-premises Active Directory structure in cloud identity plane
- Assigned Helpdesk Administrator role using Azure RBAC (least privilege)
- Created SOC-Analysts security group for role-based access control

### Microsoft Sentinel (Cloud SIEM)
- Deployed Log Analytics Workspace (`homelab-laws`) in Azure (Central US)
- Enabled Microsoft Sentinel on top of Log Analytics workspace
- Installed Windows Security Events content hub solution (v3.0.12)
- Connected Sentinel workspace to Microsoft Defender XDR unified portal
- All resources deployed in `homelab-rg` resource group

### Azure Arc (Hybrid Machine Management)
- Onboarded Windows Server 2022 (`WIN-N4GSIAT5P7D`) to Azure Arc
- Onboarded Windows 11 Pro (`DESKTOP-AJ9B6KI`) to Azure Arc
- Both on-premises machines registered as Azure-managed resources
- Azure Connected Machine Agent installed and verified on both endpoints

### Data Collection & Detection
- Created Data Collection Rule (`homelab-windows-dcr`) via Azure Monitor Agent
- Windows Security Events streaming from both endpoints into Sentinel
- Validated log ingestion via KQL query in Advanced Hunting (Defender portal)
- Observed Event ID 4624 (successful logon) from domain-joined workstation
- Observed Event ID 4672 (privileged logon) from domain-joined workstation
- Machine FQDN confirmed as `DESKTOP-AJ9B6KI.homelab.local` in cloud SIEM

### Hybrid Architecture
```
On-premises endpoints (Windows Server 2022 + Windows 11)
    → Azure Arc (machine registration + management)
        → Azure Monitor Agent (log collection)
            → Log Analytics Workspace (homelab-laws)
                → Microsoft Sentinel
                    → Microsoft Defender XDR Portal (unified SOC console)
```

---

## Detection Engineering

### KQL Queries (Microsoft Sentinel / Advanced Hunting)

**Security event volume by machine and event ID:**
```kql
SecurityEvent
| where TimeGenerated > ago(1h)
| summarize count() by Computer, EventID
| order by count_ desc
```

**Failed logon detection (brute force pattern):**
```kql
SecurityEvent
| where EventID == 4625
| summarize FailedLogins = count() by Account
| order by FailedLogins desc
```

---

## MITRE ATT&CK Coverage

| Technique | ID    | Description                        | Detection Method         |
|-----------|-------|------------------------------------|--------------------------|
| Brute Force | T1110 | Repeated failed authentication   | Wazuh + Sentinel EID 4625 |
| Valid Accounts | T1078 | Credential abuse simulation   | Wazuh + Sentinel EID 4624 |
| Privileged Logon | T1078.002 | Admin account logon       | Sentinel EID 4672        |

---

## Planned Work

- KQL detection rules and custom analytics in Microsoft Sentinel
- Sentinel scheduled alert rules for brute force and lateral movement
- Sysmon deployment for enhanced endpoint telemetry
- PowerShell script block logging
- Kerberoasting simulation and detection
- BloodHound AD attack path analysis
- Azure Arc extension deployment (Defender for Endpoint)
- AWS CloudGoat for cloud penetration testing scenarios
- Incident response playbooks
- Threat hunting dashboards in Sentinel

---

## Skills Demonstrated

- Enterprise network segmentation (VLANs, pfSense, firewall rules)
- Active Directory deployment and administration
- Windows authentication and Kerberos concepts
- SIEM deployment and log ingestion (Wazuh + Microsoft Sentinel)
- Cloud identity management (Microsoft Entra ID, RBAC)
- Hybrid infrastructure management (Azure Arc)
- KQL query writing for threat detection
- MITRE ATT&CK framework mapping
- Troubleshooting methodology across network, identity, and cloud layers
- Zero trust remote access (Twingate)

---

## Purpose

This lab is designed to develop practical experience for SOC analyst, blue team, and cloud security engineering roles. Every phase is documented to demonstrate real technical ability, troubleshooting methodology, and security engineering thinking — not just tool deployment.

---

## Certifications / Education

- CompTIA A+
- CompTIA Network+
- CompTIA Security+
- CompTIA Project+
- LPI Linux Essentials
- ITIL 4 Foundations
- Western Governors University — B.S. Cybersecurity (in progress)
- Pursuing: CySA+, PenTest+, SSCP, CCSP, Data+
