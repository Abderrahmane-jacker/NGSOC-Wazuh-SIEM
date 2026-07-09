# MTTD / MTTR results

The report states (Section 3.4.4) that automation reduces MTTR "from several tens of minutes to a few seconds," but doesn't include a measured table of actual timestamps.

## TODO (you need to do this)

If you want this repo to make a *quantified* claim rather than a qualitative one, pull real numbers from your Shuffle execution logs and Wazuh alert timestamps:

| Scenario | Alert timestamp (Wazuh) | Case created (TheHive) | IP blocked (pfSense) | Total automated MTTR |
|---|---|---|---|---|
| Nmap SYN scan | | | | |
| UDP flood DoS | | | | |
| Mimikatz LSASS access | | | | |

Shuffle keeps per-workflow execution logs with timestamps for each node — export those for the runs you already did during testing (Figures 26–30 imply you have these runs available) and fill in the table above.

For comparison, if you want a "manual baseline" number to contrast against, time how long the same detect → investigate → block sequence takes if done manually through the Wazuh dashboard + pfSense web UI, with no SOAR involved. Even one measured run gives you a legitimate before/after comparison for the README.
