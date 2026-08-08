# Troubleshooting Record

This document preserves failures and defensive observations that materially changed the implementation. They are part of the project because they demonstrate how the final design was reached.

| Problem / observation | What it meant | Resolution / lesson |
|---|---|---|
| Wazuh/indexer installation repeatedly stalled | The original VM was resource constrained and the deployment was sensitive to available resources | Stabilized VM resources/services before adding more lab components |
| VM package downloads were extremely slow under the initial network arrangement | Network mode was contributing to installation delays | Switched to NAT and used port forwarding where host-to-VM access was required |
| Encoded PowerShell kept firing rule `92057` | Wazuh already had an effective built-in detection | Avoided duplicating existing coverage; selected a cleaner custom rule use case |
| Regsvr32 Atomic test was prevented | Microsoft Defender blocked/quarantined the test artifact | Kept Defender enabled and recorded prevention as a defensive success |
| Ingress Tool Transfer test returned access denied and Defender identified the WebClient behavior | The intended download simulation was prevented before completion | Recorded prevention rather than treating it as a Wazuh failure |
| Custom registry persistence test did not show separate Sysmon Event ID 13 telemetry | Process telemetry was available but registry-value-set visibility was incomplete | Documented the telemetry gap and did not claim registry Event ID 13 evidence that was not observed |
| Custom rule did not initially appear as intended | Rule deployment/testing workflow was creating friction | Used Wazuh server-management custom rules and validated the final rule in the dashboard |
| VirusTotal search initially returned nothing | Integration/FIM configuration was not yet producing the expected enrichment event | Corrected configuration/reload flow and regenerated the monitored event |
| Raw terminal alert output was difficult to read | JSON was valid telemetry but poor presentation | Parsed/extracted structured fields for investigation and evidence |
| `pip install --user -U google-genai` returned `externally-managed-environment` | Ubuntu protects the system Python environment under PEP 668 | Created a virtual environment instead of using `--break-system-packages` |
| Wazuh could not execute Python from `/home/.../ai-soc-venv` | The `wazuh` service account lacked traversal/execution permission through the user's home path | Created the dedicated `/opt/wazuh-ai-venv` environment with service-appropriate permissions |
| `gemini-ai.log` initially did not exist | Automated Integrator execution had not succeeded yet | Checked integration executable permissions and Python accessibility, then retriggered the alert |
| Active Response log looked scrambled | Full alert JSON was written as a single line | Confirmed response fields, then separated evidence formatting from response execution; sanitized example uses `printf` |
| Wazuh admin password was rotated | Indexer internal credentials changed and dependent components could need reload/restart | Used the official password-management utility, then restarted/verified indexer, manager, Filebeat and dashboard as required |

## Incident-testing principle

The project deliberately distinguished between a simulation being executed, a detection firing, an attack objective succeeding, and an endpoint defense preventing execution. A blocked Atomic test is not a failed SOC exercise; it is evidence that a preventive control worked.

## Troubleshooting principle

The project stopped troubleshooting when remaining work would only reproduce an existing capability without adding meaningful learning or coverage. The encoded-PowerShell/custom-rule collision is the clearest example: built-in rule `92057` already detected the behavior, so the better engineering decision was to move forward rather than force a custom ID for its own sake.

## Security principle

Do not disable Microsoft Defender, bypass OS package protections, expose credentials, or weaken service permissions simply to make a demonstration work. The final lab should show how the system behaves under realistic defensive constraints.
