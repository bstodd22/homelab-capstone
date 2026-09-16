# Microsoft Sentinel Setup — Hybrid Cloud Log Collection

Status: Initial integration complete; further analysis and detection work planned

## Overview

Extended an on-premises cybersecurity home lab into Azure to collect
Windows Security Events in Microsoft Sentinel.

The completed work focuses on hybrid connectivity, log collection,
and integration with the Microsoft Defender portal—not completed
security investigations or detection engineering.

## Environment

- Proxmox virtualization environment
- pfSense firewall and VLAN segmentation
- Windows Server and Linux virtual machines forwarding logs to Wazuh
- Windows endpoints configured to forward security events to Sentinel
- Azure Arc for hybrid machine integration
- Microsoft Sentinel integrated with the Microsoft Defender portal

## Log Collection Architecture

Windows endpoints
  → Azure Monitor Agent
  → Log Analytics workspace
  → Microsoft Sentinel

Azure Arc provides the hybrid machine management connection.
The Microsoft Defender portal provides access to the integrated
Sentinel workspace.

Wazuh separately collects logs from Windows Server and Linux systems.

## Completed Work

### Hybrid Cloud Integration
- Extended the on-premises environment to Azure using Azure Arc.

### Microsoft Sentinel
- Configured Microsoft Sentinel for Windows security-event collection.
- Configured Windows endpoints to forward Windows Security Events.
- Established log flow into Sentinel.

### Microsoft Defender Portal
- Integrated Microsoft Sentinel with the Microsoft Defender portal.

This portal integration does not imply Defender for Endpoint
deployment or completed Defender XDR investigations.

## Current Validation Scope

Windows Security Events are flowing into Microsoft Sentinel.

KQL-based validation and analysis have not yet been performed.
This document therefore does not include query output, event counts,
or claims that individual attack techniques have been detected.

Log collection is a foundation for future detection work; it does not
by itself establish tested detection coverage.

## Evidence to Add

- Sanitized screenshots showing Azure Arc connection status
- Data collection configuration
- Screenshots showing incoming Windows Security Events
- Per-endpoint confirmation of log collection
- Sentinel integration within the Microsoft Defender portal

Remove sensitive information before publishing screenshots or logs.

## Planned Work — Not Yet Completed

- Learn and run KQL queries against collected events.
- Document per-endpoint ingestion validation.
- Create and test Sentinel analytics rules.
- Perform controlled authentication-failure simulations.
- Investigate resulting telemetry and alerts.
- Evaluate Sysmon for additional endpoint telemetry.
- Explore Sentinel workbooks.
- Evaluate Defender for Endpoint integration separately.

## Cost Management

Review actual usage, applicable trial terms, and current Azure pricing
before expanding collection. No fixed monthly cost or ongoing free
Sentinel allowance is claimed in this document.
