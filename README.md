# Incident Response Lab — IR-001

A hands-on incident response simulation conducted on a home lab environment. An attacker machine (Kali Linux) compromised an Ubuntu target via SSH brute force, performed post-compromise reconnaissance, created backdoor accounts with sudo privileges, and planted a persistence mechanism. The full PICERL incident response methodology was followed to contain, eradicate, and recover from the incident.

## Environment

| Component | Details |
|---|---|
| SIEM Host | Ubuntu 26.04 ARM64 — Elasticsearch, Kibana, Elastic Agent |
| Attacker | Kali Linux 2023 ARM64 |
| Host Machine | Apple Mac Mini M4, 32GB RAM |
| Network | Bridged (192.168.1.0/24) |

## Attack Summary

| Phase | Technique | MITRE ID |
|---|---|---|
| Reconnaissance | Nmap SYN scan | T1046 |
| Credential Access | SSH brute force via Hydra | T1110 |
| Discovery | whoami, id, /etc/passwd, ps aux | T1033, T1087, T1057 |
| Persistence | Backdoor accounts created with sudo access | T1136 |
| Persistence | Malicious cron job planted | T1053 |
| Privilege Escalation | Backdoor accounts added to sudo group | T1078 |

## Detection

Three detection rules fired during the incident:

| Rule | Type | Severity |
|---|---|---|
| SSH Brute Force Detection | Custom threshold rule | Medium |
| Potential Internal Linux SSH Brute Force Detected | Elastic prebuilt | Medium |
| Suspicious Account Creation or Modification | Custom threshold rule | High |

851 failed authentication attempts captured. All attack phases logged and alerted on in real time through the ELK stack SIEM.

## Kibana Alert Timeline

![Kibana Alerts](alerts%202.png)

## IR Response (PICERL)

- **Preparation** — System baseline captured before incident
- **Identification** — Kibana alerts triggered investigation. Active attacker session confirmed via last and who commands
- **Containment** — Attacker IP blocked at firewall. Backdoor accounts locked
- **Eradication** — Backdoor accounts deleted. Malicious cron job removed. Compromised password reset
- **Recovery** — Firewall hardened with default deny policy. SSH restricted to admin IP only. Services verified operational
- **Lessons Learned** — Root cause identified as weak password + no host firewall. 7 hardening recommendations documented

## Response Time

~20 minutes from identification to recovery

## Full Report

See [ir_report.docx](ir_report.docx) for the complete incident response report including full timeline, forensic evidence, MITRE ATT&CK mapping, and recommendations.

## Tools Used

- Elastic Stack 8.19.14 (Elasticsearch, Kibana, Elastic Agent)
- Kali Linux — Nmap, Hydra
- UFW (host-based firewall)
- Ubuntu 26.04 ARM64
