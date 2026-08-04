# Incident Response

## Objective

Demonstrate an end-to-end alert-to-response workflow without using a destructive action against a dual-use Windows utility.

## Trigger

Custom Wazuh rule `100011` detects `systeminfo.exe` and maps it to MITRE ATT&CK T1082.

## Response design

A custom Wazuh Active Response command named `soc-response` executes on the Wazuh manager when rule `100011` fires. The script reads the JSON supplied by `wazuh-execd` and records response evidence.

```text
Detection
   -> Wazuh alert
      -> Gemini-assisted investigation
      -> Active Response trigger
         -> evidence log
```

## Why the response is non-destructive

System-information discovery can be malicious reconnaissance, but `systeminfo.exe` is also a legitimate administrative utility. Automatically killing processes, blocking hosts, or disabling accounts from this single signal would be poor response engineering.

The lab therefore proves orchestration while preserving the principle that response severity should match confidence and context.

## Evidence

Successful validation showed an Active Response `add` command associated with rule `100011`, the `Windows10-SOC` agent, MITRE T1082, and the `soc-response` program.

The first log format placed the full JSON on one line. This affected readability, not execution. The sanitized example uses `printf` rather than relying on `echo` when preserving the JSON string.
