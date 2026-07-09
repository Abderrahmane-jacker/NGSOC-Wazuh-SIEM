# Scenario: Windows — Post-Exploitation & Credential Dumping (Mimikatz)

**Target:** Windows host (Domain Controller / monitored endpoint), `10.0.10.200`
**Technique:** LSASS memory access / credential dumping (MITRE ATT&CK T1003.001)

## Step 1 — Run Mimikatz on the target host

```
mimikatz 2.2.0 x64 (oe.eo)
mimikatz #
```

## Step 2 — Local logging via Sysmon

Sysmon Event ID 1 (process creation) captures the command line, parent process (`explorer.exe`), and file hash of the executable — this is the raw telemetry that makes the next step possible.

## Step 3 — SIEM detection (Wazuh)

Wazuh correlates `sourceImage` (mimikatz.exe) against `targetImage` (`C:\Windows\system32\lsass.exe`), confirming a credential dumping attempt:

| Field | Value |
|---|---|
| `data.win.eventdata.sourceImage` | `C:\Users\user\Downloads\mimikatz_trunk\x64\mimikatz.exe` |
| `data.win.eventdata.targetImage` | `C:\Windows\system32\lsass.exe` |
| `data.win.eventdata.grantedAccess` | `0x143a` |

## Bonus: privilege escalation tracing

Wazuh's PAM/session monitoring on the Linux side separately catches privilege escalation attempts, e.g.:

```
rule.description: "Successful sudo to ROOT executed"
rule.id: 5402
```

## What this validates

- Wazuh detection isn't limited to network-layer signatures — it extends to OS-level credential theft via Sysmon telemetry correlation
- The `sourceImage`/`targetImage` correlation pattern is a reusable detection technique for any LSASS-access tool, not just Mimikatz specifically

## TODO (you need to do this)
- Add Figures 22–25 from the report into `docs/screenshots/`.
- Document the exact Wazuh rule ID that fires on this LSASS access pattern (the report shows the field correlation but not the specific custom rule ID — check your `local_rules.xml`).
