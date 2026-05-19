# ADR-0001: Use Windows Security Events via AMA

## Status

Accepted

## Context

This lab is designed to collect failed RDP logon telemetry from a Windows VM and analyze it in Microsoft Sentinel.

The legacy tutorial pattern used a custom PowerShell script, a third-party GeoIP API, a custom text log, and manual field extraction. That approach is useful for understanding the lower-level mechanics of log transformation, but it adds unnecessary moving parts for the V1 goal.

For V1, the goal is to demonstrate a realistic Sentinel onboarding path:

- collect Windows Security Events;
- validate Event ID 4625;
- query the `SecurityEvent` table;
- enrich source IPs in KQL;
- build a workbook visualization.

## Decision

Use the Microsoft Sentinel **Windows Security Events via AMA** connector to collect Windows Security Events from the honeypot VM into the Log Analytics workspace.

The project will use the modern Azure Monitor Agent path instead of legacy collection patterns.

## Rationale

This approach is preferred for V1 because it:

- aligns with a realistic enterprise Sentinel workflow;
- reduces custom scripting complexity;
- avoids embedded API keys;
- avoids brittle custom field extraction;
- keeps telemetry in the Sentinel-oriented `SecurityEvent` table;
- supports focused collection, such as the Minimal event set, to reduce ingestion volume.

## Alternatives considered

### Custom PowerShell + custom log file

Rejected for V1.

This remains useful for a future V2 custom telemetry pipeline, but it is not required to prove Sentinel onboarding and failed RDP logon analysis.

### Third-party GeoIP enrichment

Rejected for V1.

KQL can enrich IP addresses directly with `geo_info_from_ip_address()`, avoiding external API keys and outbound dependency from the honeypot VM.

### Legacy Log Analytics agent

Rejected.

The project should use the current Azure Monitor Agent pattern.

## Consequences

### Positive

- Simpler build.
- Better interview alignment.
- Fewer secrets and fewer dependencies.
- Cleaner KQL workflow.
- Easier evidence capture.

### Tradeoffs

- Less custom pipeline engineering in V1.
- The custom log pipeline must be deferred to a later project version if needed.

## References

- Windows Events collection with Azure Monitor Agent: https://learn.microsoft.com/en-us/azure/azure-monitor/vm/data-collection-windows-events
- Windows Security Event sets for Sentinel: https://learn.microsoft.com/en-us/azure/sentinel/windows-security-event-id-reference
