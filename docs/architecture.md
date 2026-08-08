# Architecture

## Data flow

```text
Windows 10 Endpoint
  |
  +-- Sysmon + Wazuh Agent
  |
  v
Wazuh Manager / Indexer / Dashboard (Ubuntu)
  |
  +-- Built-in Rules --> alerting / threat hunting
  |
  +-- Custom Rule 100011 --> MITRE T1082
  |
  +-- FIM --> VirusTotal enrichment
  |
  +-- Integrator --> custom-gemini --> Gemini analysis
  |
  +-- Active Response --> soc-response (non-destructive evidence action)
  |
  v
SOC Dashboard --> severity / MITRE / rules / timeline
```

## Administrative flow

```text
Wazuh administrator
        |
        +--> Dashboard authentication
        |
        +--> Wazuh password-management utility
        |
        +--> Indexer internal users
        |
        +--> dependent service credential/config reload
```

## Design decisions

- A single Windows endpoint was sufficient to demonstrate endpoint telemetry and SOC workflows while keeping VM RAM usage manageable.
- Sysmon supplied richer process telemetry than standard event collection alone.
- Custom detections were added only where they demonstrated environment-specific detection engineering rather than duplicating effective built-in rules.
- AI was positioned after deterministic detection, not before it.
- VirusTotal and Gemini were enrichment/analysis sources; neither was treated as an unquestionable verdict.
- The Active Response demonstration was intentionally non-destructive because `systeminfo.exe` is dual-use and may be legitimate.
- Defender was not disabled to make blocked Atomic tests succeed. Prevention itself was treated as a valid defensive result.
- API credentials remain outside Git. The repository contains sanitized examples only.

## Trust boundaries

Windows endpoint telemetry crosses into the Wazuh server. Only selected Wazuh alerts are forwarded to external enrichment/AI services. This reduces unnecessary data exposure and API usage. External results return to the analyst workflow and do not automatically override Wazuh evidence.

## Security boundary

The lab is a controlled environment. Production deployments would require stronger network segmentation, secret management, access controls, certificate lifecycle management, alert-volume controls, and carefully tested response policies.
