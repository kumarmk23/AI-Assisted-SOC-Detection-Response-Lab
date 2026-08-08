# Personal Learning Notes

## What I learned from the completed SOC lab

### 1. Detection starts with telemetry
A SIEM cannot detect what the endpoint does not send. Sysmon supplied detailed Windows process telemetry, while the Wazuh agent transported that telemetry to the Wazuh manager/indexer/dashboard pipeline.

### 2. A detection is not the same thing as a compromise
An alert proves that a rule matched observed behavior. It does not automatically prove that the attack objective succeeded. The lab deliberately separated:
- observed suspicious behavior;
- successful attack simulation;
- Defender prevention/blocking;
- missing telemetry;
- analyst verdict.

### 3. Built-in rules should be reused when they are already effective
The encoded PowerShell simulation matched Wazuh rule `92057`. Creating a custom rule that duplicated the same behavior would add little value. Custom detection engineering is more useful when it demonstrates an environment-specific behavior that is not already covered well.

### 4. Context matters for LOLBins and dual-use tools
`rundll32.exe`, PowerShell, `reg.exe`, `cmd.exe`, and similar Windows binaries can be legitimate. The analyst should inspect command line, parent process, user, timestamp, host, and surrounding activity before deciding whether an alert is malicious.

### 5. Defender prevention is a successful security outcome
Some Atomic Red Team simulations were prevented by Microsoft Defender. Those tests were recorded as defensive-prevention results rather than forcing execution by weakening endpoint protection.

### 6. Custom rule engineering
The completed custom rule `100011` detects `systeminfo.exe` and maps it to MITRE ATT&CK `T1082` (System Information Discovery). The rule demonstrates the complete lifecycle: define behavior -> write rule -> deploy -> generate telemetry -> validate alert -> map to ATT&CK.

### 7. Threat intelligence is enrichment, not proof
VirusTotal can add reputation context to an IOC or file event, but an external reputation result should be correlated with endpoint evidence before assigning a final verdict.

### 8. AI belongs after deterministic detection
The Gemini integration receives selected Wazuh alert data after deterministic Wazuh detection. The AI provides summary, risk context, suspicious indicators, MITRE context, recommended investigation steps, and a preliminary verdict. The analyst remains responsible for the final decision.

### 9. Linux service-account permissions matter
A Python virtual environment under a user's home directory was not usable by the `wazuh` service account. Moving the environment to `/opt/wazuh-ai-venv` with appropriate permissions solved automated execution without weakening Ubuntu's system protections.

### 10. PEP 668 should not be bypassed casually
Ubuntu's `externally-managed-environment` error prevented installing `google-genai` into the system Python environment. The correct solution was an isolated virtual environment rather than `--break-system-packages`.

### 11. Active Response must be tuned conservatively
The lab used a non-destructive Active Response demonstration because `systeminfo.exe` is dual-use. Automatically killing a process simply because it matched a discovery rule would create an avoidable false-positive risk.

## Analyst mental model

`Telemetry -> Detection -> Context -> Enrichment -> Investigation -> Verdict -> Response -> Evidence`

AI and threat intelligence improve investigation quality, but they do not replace deterministic telemetry or analyst judgment.

## Reusable interview explanation

I built a Windows-based SOC lab using Sysmon and Wazuh, validated detections with controlled Atomic Red Team activity, wrote a custom MITRE-mapped detection, enriched events with VirusTotal, integrated Gemini for analyst-assisted investigation, and demonstrated a controlled Active Response workflow. The key design decision was to keep Wazuh responsible for deterministic detection and response while using AI only as an investigation assistant.
