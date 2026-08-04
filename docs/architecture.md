# Architecture

## Data flow

```text
Windows 10
  |
  | Sysmon + Wazuh agent
  v
Wazuh Manager / Indexer / Dashboard (Ubuntu)
  |
  +-- Rule engine --> custom detection 100011 --> MITRE T1082
  |
  +-- FIM --> VirusTotal enrichment
  |
  +-- Integrator --> custom-gemini --> Gemini analysis
  |
  +-- Active Response --> soc-response (non-destructive evidence action)
  |
  +-- Dashboard --> severity / MITRE / rules / timeline
```

## Design decisions

- A single Windows endpoint was sufficient to demonstrate endpoint telemetry and SOC workflows while keeping VM RAM usage manageable.
- Sysmon supplied richer process telemetry than standard event collection alone.
- Custom detections were added only where they demonstrated environment-specific detection engineering rather than duplicating effective built-in rules.
- AI was positioned after deterministic detection, not before it.
- VirusTotal and Gemini were enrichment/analysis sources; neither was treated as an unquestionable verdict.
- The Active Response demonstration was intentionally non-destructive because `systeminfo.exe` is dual-use and may be legitimate.

## Trust boundaries

API credentials remain outside Git. The repository contains only sanitized examples. The Wazuh manager is responsible for selecting alerts before they reach the external AI service, limiting unnecessary data exposure and API usage.
