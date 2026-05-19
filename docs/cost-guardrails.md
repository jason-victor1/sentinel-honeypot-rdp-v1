# Cost Guardrails

## Purpose

This lab intentionally exposes a cloud VM to the internet and sends security telemetry into Log Analytics. Cost controls are part of the security design.

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

This makes cleanup simple and reduces the chance of orphaned resources.

2. Tags

Use tags to support cleanup and accountability:

Project=SentinelHoneypotV1
Owner=<your-name>
ExpiresOn=<yyyy-mm-dd>
Purpose=CloudSecurityLab
3. Budget alerts

Create a budget with alert thresholds such as:

50%
80%
100%

Evidence screenshot:

evidence/screenshots/05_budget_alerts.png
4. VM auto-shutdown

Enable VM auto-shutdown for the same day as the lab.

This is a backup control, not a substitute for deleting the resource group.

5. Minimal event collection

Use the Minimal Windows Security Events set where appropriate so the lab captures relevant logon events without collecting unnecessary high-volume telemetry.

6. Short run window

Recommended run window:

1 to 4 hours

Extend only if you need more telemetry and are actively monitoring cost and VM status.

7. Mandatory cleanup

At the end of the run, delete the resource group.

Evidence screenshot:

evidence/screenshots/06_resource_group_deleted.png
Cost incident response

If cost rises unexpectedly:

Stop the VM.
Disable workbook auto-refresh if enabled.
Delete the resource group.
Confirm no public IPs, disks, NICs, or workspaces remain.
Review Azure Cost Management.
Document what happened in the project notes.
Interview framing

This project treats cost as part of security because uncontrolled spend can indicate:

runaway logging;
exposed infrastructure;
abuse after compromise;
poor cloud lifecycle discipline.
References
Azure Monitor Logs cost guidance: https://learn.microsoft.com/en-us/azure/azure-monitor/logs/cost-logs
Azure Cost Management alerts: https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending
