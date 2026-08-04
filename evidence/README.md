# Evidence Manifest

Eight primary PNG captures document the completed AI-assisted SOC lab. Each screenshot was reviewed before repository publication to avoid exposing credential-bearing configuration or secrets.

1. `01-agent-active.png` — `Windows10-SOC` connected and active in Wazuh.
2. `02-sysmon-telemetry.png` — Sysmon Process Create telemetry with relevant event fields.
3. `03-custom-detection-100011.png` — custom detection with rule `100011` and MITRE ATT&CK T1082.
4. `04-custom-rules.png` — Wazuh custom-rules management view.
5. `05-virustotal-enrichment.png` — VirusTotal threat-intelligence enrichment evidence.
6. `06-gemini-ai-investigation.png` — structured Gemini SOC investigation output.
7. `07-active-response.png` — controlled Active Response validation evidence.
8. `08-final-soc-dashboard.png` — final four-panel security-operations dashboard.

## Publication safety

The repository intentionally excludes live API keys, authentication tokens, passwords, secret-bearing shell configuration, the live Wazuh `ossec.conf`, and raw backup directories. Sanitized configuration examples use placeholders instead.
