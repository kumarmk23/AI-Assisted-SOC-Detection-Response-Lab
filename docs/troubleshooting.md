# Troubleshooting Record

This document preserves the failures that materially changed the implementation. They are part of the project because they demonstrate how the final design was reached.

| Problem | What it meant | Resolution / lesson |
|---|---|---|
| Wazuh indexer installation repeatedly stalled | The original all-in-one VM was resource constrained | Stabilized VM resources/services before adding more lab components |
| Encoded PowerShell kept firing rule `92057` | Wazuh already had an effective built-in detection | Avoided spending more time duplicating existing coverage; selected a cleaner custom rule use case |
| Custom rule did not initially appear as intended | Rule deployment/testing workflow was creating friction | Used Wazuh server-management custom rules and validated the final rule in the dashboard |
| VirusTotal search initially returned nothing | Integration/FIM configuration was not yet producing the expected enrichment event | Corrected configuration/reload flow and regenerated the monitored event |
| Raw terminal alert output was difficult to read | JSON was valid telemetry but poor presentation | Parsed/extracted structured fields for investigation and evidence |
| `pip install --user -U google-genai` returned `externally-managed-environment` | Ubuntu protects the system Python environment under PEP 668 | Created a virtual environment instead of using `--break-system-packages` |
| Wazuh could not execute Python from `/home/.../ai-soc-venv` | The `wazuh` service account lacked traversal/execution permission through the user's home path | Created the dedicated `/opt/wazuh-ai-venv` environment with service-appropriate permissions |
| `gemini-ai.log` initially did not exist | Automated Integrator execution had not succeeded yet | Checked integration executable permissions and Python accessibility, then retriggered the alert |
| Active Response log looked scrambled | Full alert JSON was written as a single line | Confirmed response fields, then separated evidence formatting from response execution; sanitized example uses `printf` |

## Troubleshooting principle

The project deliberately stopped troubleshooting when the remaining work would only reproduce an existing capability without adding meaningful learning or coverage. The encoded-PowerShell/custom-rule collision is the clearest example: built-in rule `92057` already detected the behavior, so the better engineering decision was to move forward rather than force a custom ID for its own sake.
