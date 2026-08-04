# Detection Engineering

## Goal

Move beyond consuming default Wazuh alerts by creating and validating environment-specific detection logic.

## Built-in vs custom detection

An encoded PowerShell simulation generated a valid Sysmon event but continued matching Wazuh built-in rule `92057`. This was useful evidence that a custom rule should not be created merely to obtain a custom rule ID. If an existing rule already detects the behavior accurately, duplicating it increases maintenance without improving coverage.

The lab therefore selected Windows system-information discovery for a clean custom use case.

## Custom detection

Rule `100011` detects `systeminfo.exe` in Sysmon Process Create telemetry and maps the behavior to MITRE ATT&CK T1082.

```xml
<rule id="100011" level="7">
  <if_group>sysmon_event1</if_group>
  <field name="win.eventdata.image" type="pcre2">(?i)\\systeminfo\.exe$</field>
  <description>Custom Detection: System information discovery executed</description>
  <mitre><id>T1082</id></mitre>
</rule>
```

The rule was deployed through Wazuh's custom-rule management interface and validated by executing `systeminfo` on the Windows endpoint.

## Validation criteria

A detection was considered successful only when the Wazuh event showed the intended custom rule ID, expected severity, endpoint identity, process information, and MITRE mapping.

## Tuning principle

`systeminfo.exe` is dual-use. Detection does not equal compromise. This distinction later informed the decision to use a non-destructive Active Response action and keep the analyst responsible for the final verdict.
