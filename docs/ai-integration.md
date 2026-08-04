# AI and Threat-Intelligence Integration

## VirusTotal enrichment

Wazuh File Integrity Monitoring was used to generate file events that could be enriched with VirusTotal reputation information. The integration demonstrates how a SOC can add external IOC context to local telemetry.

The real VirusTotal API key is intentionally excluded. `configs/ossec.conf.example` contains only `YOUR_VIRUSTOTAL_API_KEY`.

## Gemini investigation layer

The Gemini phase was implemented after alert generation and custom detection were working reliably. This keeps the AI layer focused on investigation rather than asking a language model to replace deterministic detection logic.

The desired output structure was:

1. Alert Summary
2. Risk Assessment
3. Suspicious Indicators
4. MITRE ATT&CK Context
5. Recommended Investigation Steps
6. Preliminary Verdict

## Python environment

Ubuntu's PEP 668 protections rejected direct `pip --user` installation into the externally managed Python environment. The project therefore used a virtual environment and installed the Google GenAI SDK there.

The first venv lived under the interactive user's home directory. Wazuh executes integrations as its service account, which could not traverse/execute that environment. A dedicated environment at `/opt/wazuh-ai-venv` solved the permission boundary cleanly.

## Automated flow

```text
Wazuh alert matching rule 100011
       |
       v
/var/ossec/integrations/custom-gemini
       |
       v
/opt/wazuh-ai-venv/bin/python
       |
       v
Gemini analysis script/API
       |
       v
/var/ossec/logs/gemini-ai.log
```

## Security model

The API key must be supplied outside source control. This repository does not contain the original key, secret-bearing shell configuration, or the live Wazuh configuration. AI output is advisory and requires analyst validation.
