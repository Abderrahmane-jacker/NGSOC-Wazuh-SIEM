# Scenario: Linux — Reconnaissance & Denial of Service

**Target:** Linux server in DMZ (`10.0.20.10`) and internal host (`10.0.10.50`)
**Attacker origin:** Kali Linux

## Step 1 — Port scan (Nmap SYN stealth)

```bash
sudo nmap -sS -p 1-1000 10.0.10.50
```

**Detection:** Suricata rule `SOC LAB - Scan Nmap SYN Detecte` fires (34 hits observed in testing), correlated into Wazuh as `rule.id 1:1000011`.

## Step 2 — Denial of Service (UDP flood)

Target the DNS port (53) with a volumetric flood:

```bash
sudo hping3 --udp --flood --destport 53 10.0.20.10
```

**Detection:** Suricata rule `SOC LAB - Attaque par Deni de Service (UDP Flood)` fires (`rule.id 1:100024`, severity level 8), centralized in the Wazuh SIEM alongside the earlier recon-phase alerts (Nikto scan, SQLi attempt, suspicious `.exe` download) for the same source.

## What this validates

- Suricata + Wazuh correlation across the DMZ/LAN boundary
- Detection holds up across a full attack chain (recon → exploitation attempt → DoS), not just isolated single events
- The SOAR playbook (see [`../soar-playbooks/`](../soar-playbooks/)) triggers automated blocking on the Nmap alert threshold before the DoS phase completes

## TODO (you need to do this)
- Add the exact screenshots (Figures 18–21 from the report) into `docs/screenshots/` and reference them here inline.
- Confirm and document the exact Wazuh alert level threshold that triggers the SOAR playbook for this scenario.
