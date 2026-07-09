# pfSense interface configuration

From Rapport_PFA-NGSOC_VF.pdf, Figure 14.

| Interface | Link | IP Address |
|---|---|---|
| WAN | 1000baseT full-duplex | `192.168.106.135` (lab-specific, DHCP-assigned) |
| LAN | 1000baseT full-duplex | `10.0.10.1` |
| DMZ | 1000baseT full-duplex | `10.0.20.1` |

## SOAR active-response blocklist

pfSense hosts a dedicated alias table used by the Shuffle SOAR playbook for active IP blocking:

- **Table name:** `SOAR_Blocklist`
- **Populated by:** Shuffle workflow, via pfSense API call, on high-fidelity Wazuh/Suricata alerts
- **Cleared by:** the "Unblock Gate" workflow, triggered by the one-click email action

TODO (you need to do this):
- Export your actual pfSense `config.xml` firewall alias/rule sections relevant to `SOAR_Blocklist` (Diagnostics > Backup & Restore, select only the Aliases/Firewall Rules sections) and add a sanitized version here — strip any real WAN IP, admin credentials, or VPN details before committing.
- Document the exact firewall rule that references the `SOAR_Blocklist` alias to actually drop traffic (this isn't detailed in the report figures, only the resulting table).
