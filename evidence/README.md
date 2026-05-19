# Evidence Pack

Use this folder to store screenshots and redaction notes for the lab.

## Required screenshots

| File | Evidence |
|---|---|
| `screenshots/01_nsg_3389_only.png` | NSG inbound rule showing TCP/3389 only |
| `screenshots/02_connector_windows_events_via_ama.png` | Sentinel Windows Security Events via AMA connector |
| `screenshots/03_securityevent_4625_query.png` | KQL results showing Event ID 4625 |
| `screenshots/04_workbook_map.png` | Sentinel workbook map |
| `screenshots/05_budget_alerts.png` | Budget thresholds / cost guardrails |
| `screenshots/06_resource_group_deleted.png` | Cleanup proof |

## Redaction guidance

Do not expose:

- Passwords
- Secrets
- API keys
- RDP files
- Tenant IDs if you prefer to keep them private
- Subscription IDs if you prefer to keep them private
- Personal/home IP addresses if you prefer to keep them private

Document any redactions in:

```text
evidence/redactions/redaction-notes.md
```
