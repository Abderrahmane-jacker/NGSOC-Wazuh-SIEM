# MTTD / MTTR results

The report states (Section 3.4.4) that automation reduces MTTR "from several tens of minutes to a few seconds,".

| Scenario | Alert timestamp (Wazuh) | Case created (TheHive) | IP blocked (pfSense) | Total automated MTTR |
|---|---|---|---|---|
| Nmap SYN scan | 1s(estimated) | 5s->10s| 2s | 15s->20s |
| UDP flood DoS | 1s(estimated) | 5s->10s | 2s | 15s->20s |
| Mimikatz LSASS access | 2s(estimated) | 5s->10s | 2s | 17s->20s |


