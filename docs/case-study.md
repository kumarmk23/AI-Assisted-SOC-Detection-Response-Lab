# Case Study: From Endpoint Telemetry to an AI-Assisted SOC Workflow

## Objective

Build and validate a compact Windows SOC environment that demonstrates endpoint telemetry, SIEM detection, custom detection engineering, threat-intelligence enrichment, phishing investigation methodology, AI-assisted triage, controlled response, and SOC visualization.

## Environment

- Windows 10 endpoint
- Sysmon for detailed Windows telemetry
- Ubuntu Server Wazuh all-in-one deployment
- Wazuh Manager, Indexer, Dashboard, FIM and Integrator
- MITRE ATT&CK for technique mapping
- VirusTotal for reputation enrichment
- Google Gemini for analyst-assisted investigation
- VMware/virtualized lab environment

## Incident and validation record

| Activity | Result | Analyst conclusion |
|---|---|---|
| Account Discovery / T1087.001 | Wazuh observed discovery commands and Sysmon process activity | True positive, authorized simulation |
| Regsvr32 / T1218.010 | Microsoft Defender detected/quarantined the Atomic test artifact; execution was prevented | Prevented by Defender; do not weaken endpoint protection to force execution |
| Encoded PowerShell / T1059.001 | Wazuh rule `92057` detected encoded PowerShell execution at high severity | True positive, authorized simulation; built-in coverage already effective |
| Registry Run Keys / T1547.001 | Wazuh observed `reg.exe` modifying the Run key; separate Sysmon Event ID 13 telemetry was not observed | True positive, authorized simulation; registry-set telemetry visibility gap documented |
| Ingress Tool Transfer / T1105 | Atomic execution was denied and Defender identified the WebClient download behavior | Prevented by Defender; not a Wazuh detection failure |
| Credential Manager / T1003 | Wazuh observed `rundll32.exe keymgr,KRShowKeyMgr` activity | True positive, authorized simulation; detection proves suspicious execution, not credential extraction |
| Custom System Information Discovery / T1082 | Wazuh custom rule `100011` matched `systeminfo.exe` | Validated custom detection engineering use case |

All activity was controlled lab testing. No conclusion claims that an attack objective succeeded unless the evidence demonstrated it.

## Detection engineering outcome

The custom detection was intentionally selected where it added meaningful coverage rather than duplicating a strong built-in rule. Rule `100011` identifies `systeminfo.exe` through Sysmon process telemetry and maps the event to MITRE ATT&CK `T1082`.

The encoded-PowerShell test remained mapped to built-in rule `92057` because the existing detection already covered the behavior effectively.

## Phishing investigation outcome

The phishing workflow follows an analyst sequence:

1. Inspect sender and domain context.
2. Extract URLs, attachments, hashes and other IOCs.
3. Enrich suspicious artifacts with threat intelligence.
4. Correlate relevant endpoint/SIEM evidence.
5. Decide whether the evidence supports malicious, benign, or suspicious-but-unconfirmed classification.
6. Preserve the evidence and reasoning behind the verdict.

## AI-assisted investigation outcome

Gemini was integrated after Wazuh detection. Selected alerts are passed through a dedicated integration and analyzed for:

- concise alert summary;
- risk assessment;
- suspicious indicators;
- MITRE context;
- recommended investigation steps;
- preliminary verdict.

The AI is explicitly non-authoritative. Analyst validation remains mandatory.

## Threat-intelligence outcome

Wazuh FIM events were enriched with VirusTotal information. The project treats reputation as supporting evidence, not as a standalone verdict.

## Response outcome

Wazuh Active Response was validated with a non-destructive response action. This was deliberate: discovery activity involving a legitimate Windows binary can be benign, so destructive response would have created unnecessary operational risk.

## Architecture lesson

The final design is intentionally layered:

`Windows -> Sysmon -> Wazuh -> deterministic detection -> enrichment/AI -> analyst verdict -> controlled response -> dashboard/evidence`

The order matters. AI does not replace detection, and threat intelligence does not replace investigation.

## Final lessons

1. Detection quality depends on endpoint telemetry quality.
2. A blocked attack is still valuable test evidence.
3. False-positive risk must influence response design.
4. Custom rules should add coverage rather than duplicate existing detections.
5. External AI and reputation services must be treated as advisory.
6. Every case should preserve evidence, reasoning, limitations, and the final verdict.
7. Sanitized examples and secret management are part of the engineering work, not an afterthought.

## Portfolio value

The completed lab demonstrates more than a Wazuh installation. It shows a repeatable SOC workflow from endpoint telemetry through detection, enrichment, AI-assisted investigation, response, and visualization, while documenting both successful detections and defensive prevention outcomes.
