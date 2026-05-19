# Runbook — Sentinel Honeypot RDP 4625 World Map V1

## Objective

Build, validate, document, and clean up an Azure Sentinel honeypot lab that collects failed RDP logons from a Windows VM and visualizes source IP geolocation in a Sentinel workbook.

## 0. Pre-flight checklist

Before creating resources:

- Confirm this is a dedicated lab subscription or safe lab environment.
- Decide the run window: 1–4 hours.
- Decide screenshot naming convention.
- Confirm no production network peering will be used.
- Confirm you will delete the resource group after evidence capture.

Abort if you cannot commit to cleanup.

## 1. Create resource group

Create a dedicated resource group.

Suggested name:

```text
rg-sentinel-honeypot-v1
```

Suggested tags:

```text
Project=SentinelHoneypotV1
Owner=<your-name>
ExpiresOn=<yyyy-mm-dd>
Purpose=CloudSecurityLab
```

Evidence: optional screenshot of resource group and tags.

## 2. Create Log Analytics workspace

Create a Log Analytics workspace.

Suggested name:

```text
law-sentinel-honeypot-v1
```

Keep retention short for the lab.

Evidence: optional screenshot of workspace overview.

## 3. Enable Microsoft Sentinel

Enable Microsoft Sentinel on the Log Analytics workspace.

Checkpoint:

- Sentinel opens successfully for the workspace.

## 4. Create network resources

Create:

- VNet
- Subnet
- NSG

NSG inbound rule:

| Direction | Source | Destination | Protocol | Port | Action |
|---|---|---|---|---|---|
| Inbound | Internet | VM | TCP | 3389 | Allow |

Do not create Any/Any inbound rules.

Evidence:

```text
evidence/screenshots/01_nsg_3389_only.png
```

## 5. Create Windows VM

Create a Windows VM with a public IP.

Requirements:

- Strong local admin password.
- Do not use `Administrator` as the username.
- Keep Windows Defender Firewall ON.
- Enable auto-shutdown.

Checkpoint:

- You can RDP to the VM.

Evidence:

- Auto-shutdown screenshot can be saved with cost evidence.

## 6. Configure cost guardrails

Set budget alerts.

Suggested thresholds:

- 50%
- 80%
- 100%

Evidence:

```text
evidence/screenshots/05_budget_alerts.png
```

## 7. Enable Windows Security Events via AMA

In Microsoft Sentinel:

1. Open Data connectors.
2. Select Windows Security Events via AMA.
3. Create or configure the data collection rule.
4. Scope the DCR to only the honeypot VM.
5. Select the Minimal event set where appropriate.

Checkpoint query:

```kusto
SecurityEvent
| where TimeGenerated > ago(60m)
| take 10
```

Evidence:

```text
evidence/screenshots/02_connector_windows_events_via_ama.png
```

## 8. Generate test failed logon

From your local machine:

1. Attempt one failed RDP login with a bogus username/password.
2. Then log in correctly if needed.

Validation query:

```kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| where EventID == 4625
| take 50
```

Evidence:

```text
evidence/screenshots/03_securityevent_4625_query.png
```

## 9. Create workbook map

In Sentinel:

1. Open Workbooks.
2. Create a new workbook.
3. Add a query component.
4. Paste the world map KQL from `kql/02_failed_rdp_world_map.kql`.
5. Set visualization to Map.
6. Configure latitude/longitude fields.
7. Size by `event_count`.

Evidence:

```text
evidence/screenshots/04_workbook_map.png
```

## 10. Monitor during the run window

Monitor for:

- External failed logons.
- Rapid increases in failed attempts.
- Unexpected costs.
- Any signs of VM compromise.

Abort conditions:

- Any/Any inbound rule was accidentally created.
- Windows firewall was turned off.
- VM appears compromised.
- Cost trend is unexpected.

Abort action:

1. Stop VM.
2. Capture brief note.
3. Delete resource group.

## 11. Cleanup

Delete the resource group.

Suggested target:

```text
rg-sentinel-honeypot-v1
```

Verify:

- VM deleted.
- Public IP deleted.
- NIC deleted.
- NSG deleted.
- Workspace/Sentinel resources deleted, unless intentionally preserved.

Evidence:

```text
evidence/screenshots/06_resource_group_deleted.png
```

## 12. After-action notes

Record:

- Time to first external failed logon.
- Total failed logons.
- Top source IPs.
- Top usernames.
- Top countries.
- What worked.
- What failed.
- Cost result.
- Cleanup confirmation.

## Interview narrative

This lab demonstrates that I can:

- Onboard Windows Security telemetry into Sentinel.
- Validate failed RDP logons using Event ID 4625.
- Use KQL to analyze and enrich security events.
- Build a Sentinel workbook visualization.
- Apply cloud security guardrails to an intentionally exposed lab.
- Treat cost, cleanup, and evidence as part of the security workflow.
