# Cost Guardrails

## Purpose

This lab intentionally exposes a cloud VM to the internet and sends security telemetry into Log Analytics. Cost controls are part of the security design.

## Azure for Students Cost Profile

This lab is being executed under an **Azure for Students** account with **$100 in Azure credits** available over 12 months.

### Planning assumptions

| Item | Value |
|---|---:|
| Available student credit | $100 |
| Expected V1 lab cost | $0–$5 |
| Hard stop threshold | $10 |
| Log Analytics daily cap | 0.25 GB/day |
| Sentinel trial awareness | First 31 days after enabling Sentinel should be treated as a limited evaluation window |

### Cost posture

The lab should remain inexpensive because it is timeboxed, uses a small VM, collects a narrow set of Windows Security Events, and is deleted after evidence capture.

The hard stop threshold is **$10**. If spend approaches this amount, stop the VM, delete the resource group, and investigate before continuing.

### Sentinel trial note

Once Microsoft Sentinel is enabled on the workspace, the project should assume the Sentinel evaluation/trial window has started. Any related Sentinel learning loops should be planned intentionally so the trial period is not wasted.

### Log Analytics cap

Set the Log Analytics workspace daily cap to:

```text
0.25 GB/day
```

This cap is intentionally conservative for a short failed-RDP telemetry lab.

## Main cost drivers

| Cost driver | Risk |
|---|---|
| VM runtime | Compute cost if left running |
| Public IP | Cost if left allocated |
| Log Analytics ingestion | Cost increases with event volume |
| Log retention | Cost can continue after the lab |
| Microsoft Sentinel | Cost depends on ingestion and configuration |

## Required controls

### 1. Dedicated resource group

All resources should be placed in one dedicated resource group:

```text
rg-sentinel-honeypot-v1
```

This makes cleanup simple and reduces the chance of orphaned resources.

### 2. Tags

Use tags to support cleanup and accountability:

```text
Project=SentinelHoneypotV1
Owner=<your-name>
ExpiresOn=<yyyy-mm-dd>
Purpose=CloudSecurityLab
```

### 3. Budget alerts

Create a budget with alert thresholds such as:

- 50%
- 80%
- 100%

Evidence screenshot:

```text
evidence/screenshots/05_budget_alerts.png
```

### 4. VM auto-shutdown

Enable VM auto-shutdown for the same day as the lab.

This is a backup control, not a substitute for deleting the resource group.

### 5. Minimal event collection

Use the Minimal Windows Security Events set where appropriate so the lab captures relevant logon events without collecting unnecessary high-volume telemetry.

### 6. Short run window

Recommended run window:

```text
1 to 4 hours
```

Extend only if you need more telemetry and are actively monitoring cost and VM status.

### 7. Mandatory cleanup

At the end of the run, delete the resource group.

Evidence screenshot:

```text
evidence/screenshots/06_resource_group_deleted.png
```

## Cost incident response

If cost rises unexpectedly:

1. Stop the VM.
2. Disable workbook auto-refresh if enabled.
3. Delete the resource group.
4. Confirm no public IPs, disks, NICs, or workspaces remain.
5. Review Azure Cost Management.
6. Document what happened in the project notes.

## Interview framing

This project treats cost as part of security because uncontrolled spend can indicate:

- runaway logging;
- exposed infrastructure;
- abuse after compromise;
- poor cloud lifecycle discipline.

## References

- Azure Monitor Logs cost guidance: https://learn.microsoft.com/en-us/azure/azure-monitor/logs/cost-logs
- Azure Cost Management alerts: https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending
- Microsoft Sentinel billing and trial guidance: https://learn.microsoft.com/en-us/azure/sentinel/billing
