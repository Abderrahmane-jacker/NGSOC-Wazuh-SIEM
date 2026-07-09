# Architecture

## Network topology

Defense-in-depth via strict network segmentation using pfSense as the central filtering node.

| Zone | Subnet | Gateway | Purpose |
|---|---|---|---|
| WAN | (external, DHCP) | — | Public internet / Kali Linux attack origin |
| DMZ | `10.0.20.0/24` | `10.0.20.1` | Exposed web server (`10.0.20.10`) — isolated to prevent pivoting into LAN |
| LAN | `10.0.10.0/24` | `10.0.10.1` | Domain controller, monitored endpoints, SOC stack |

### LAN host assignments

| Host | IP | Role |
|---|---|---|
| Windows Server | `10.0.10.200` | Domain controller / monitored endpoint |
| Linux Mint | `10.0.10.50` | Monitored Linux endpoint |
| Wazuh Manager | `10.0.10.100` | SIEM/EDR core |
| SOAR stack (Shuffle + TheHive) | `10.0.10.150` | Orchestration + case management |

<!-- TODO: replace with your actual pfSense WAN IP from your lab -->

## Component roles

- **pfSense**: routing, stateful firewall, NAT, hosts Suricata as NIDS/NIPS, executes SOAR-driven active blocks via the `SOAR_Blocklist` alias table.
- **Suricata**: multi-threaded deep packet inspection on the pfSense gateway; custom rules tuned for the specific attacker TTPs simulated in this lab (see [`detection-rules/suricata/custom.rules`](detection-rules/suricata/custom.rules)).
- **Wazuh**: agent-based telemetry (FIM, vulnerability detection, log analysis) from Windows + Linux hosts, correlated centrally and mapped to MITRE ATT&CK.
- **Shuffle**: receives Wazuh webhooks, runs enrichment + remediation playbooks, calls the pfSense API and TheHive API.
- **TheHive**: converts high-fidelity alerts into structured investigation cases with attached observables (IPs, hashes).

## Data flow (detection → response)

```
Attacker (Kali, WAN)
   │
   ▼
pfSense + Suricata  ──(alert)──▶  Wazuh Manager (correlation, MITRE mapping)
                                        │
                                        ▼ (webhook, JSON)
                                   Shuffle (SOAR)
                                        │
                    ┌───────────────────┼────────────────────┐
                    ▼                   ▼                    ▼
              TheHive (case)     pfSense API (block IP)   Discord + Email (notify)
                                                                  │
                                                          (false positive?)
                                                                  ▼
                                                        one-click Unblock webhook
```

## Design rationale

Full comparative analysis (Wazuh vs. Splunk/ELK, Shuffle vs. Cortex XSOAR/Zapier, TheHive vs. Jira/GLPI, pfSense+Suricata vs. commercial NGFWs) is in the full report, Section 3.4 (Benchmarking).

<!-- TODO: paste your Figure 12 network diagram into docs/screenshots/ and reference it here -->
