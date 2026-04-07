# SOC Investigation Report — Incident: Whoami Command Detected in Request Body

---

**Information:**

| Field | Detail |
|---|---|
| Event Time | (See Investigation Timeline) |
| Severity | High |
| Rule | Whoami Command Detected in Request Body |

---

## Executive Summary

A detection rule flagged an inbound HTTPS request to an internal web server after identifying a "whoami" command embedded in the request body. Investigation confirmed the source IP originates from an external Chinese telecom provider (CHINANET Jiangsu Province) with no affiliation to the organization. Log analysis revealed the attacker sent multiple POST requests containing OS commands targeting the web server, and all injection attempts returned HTTP 200 responses. EDR telemetry on the web server confirmed the injected commands were successfully executed on the underlying system. Email logs were reviewed to rule out any scheduled penetration testing — none was authorized. The affected web server was contained and the incident was escalated to Tier 2 for full forensic analysis and remediation.

---

## Alert Details

| Field | Detail |
|---|---|
| Rule Triggered | Whoami Command Detected in Request Body |
| Log Source | Network / IDS |
| Source IP | 61.177.172.87 |
| Source Port | 49821 |
| Destination IP | 172.16.17.16 |
| Destination Port | 443 |
| Protocol | HTTPS |
| Traffic Direction | Internet → Company Network |
| Status | Closed — Contained and Escalated to Tier 2 |

---

## Investigation Timeline

| Step | Action |
|---|---|
| 1 | Alert triggered — "whoami" command string detected in the body of an inbound HTTPS request to 172.16.17.16 |
| 2 | Investigated source IP (61.177.172.87) via ARIN WHOIS/RDAP — identified as belonging to CHINANET Jiangsu Province Network; no affiliation with the organization |
| 3 | Investigated destination IP (172.16.17.16) — confirmed as an internal company web server |
| 4 | Reviewed web server logs — identified multiple POST requests from the source IP containing OS commands injected into request parameters |
| 5 | Analyzed HTTP response codes — all command injection attempts returned HTTP 200, indicating the requests were successfully processed |
| 6 | Inspected the web server endpoint via EDR — confirmed the injected commands were executed on the system; the server is compromised |
| 7 | Reviewed email logs to determine whether a penetration test or security assessment was scheduled — no authorized testing was found |
| 8 | Contained the affected web server — full network isolation applied |
| 9 | Escalated the incident to Tier 2 / IR team for full forensic analysis and remediation |

---

## Findings

An external attacker operating from a CHINANET Jiangsu Province IP address (61.177.172.87) targeted an internal company web server (172.16.17.16) over HTTPS. The attacker exploited a command injection vulnerability by embedding OS commands — including "whoami" — into POST request parameters. Log analysis revealed multiple injection attempts, all of which returned HTTP 200 response codes. EDR inspection of the web server confirmed that the injected commands were successfully executed on the underlying operating system, meaning the server is compromised. The source IP has no connection to the organization, and a review of email logs confirmed no penetration test or authorized security assessment was scheduled. The traffic flow was inbound from the public internet to the company network.

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Attacker exploited a command injection vulnerability in the company web server via crafted POST requests |
| Command and Scripting Interpreter | T1059 | OS commands (including "whoami") were injected and executed on the web server |
| System Owner/User Discovery | T1033 | Execution of "whoami" indicates the attacker was enumerating the user context on the compromised system |

---

## Verdict

**True Positive** — Confirmed command injection attack against an internal web server from an external source. Injected OS commands were successfully executed on the system. No authorized testing was scheduled. The server is compromised.

---

## Response Actions Taken

- Contained the affected web server via EDR — full network isolation applied
- Escalated to Tier 2 / IR team for full forensic analysis and remediation

---

## Recommendations

- **Block source IP** — Add 61.177.172.87 to firewall deny lists and threat intelligence blocklists
- **Patch the web application** — Identify and remediate the command injection vulnerability in the web server application; conduct a code review of input handling and parameter sanitization
- **Web Application Firewall (WAF)** — Deploy or tune WAF rules to detect and block OS command injection patterns in request bodies and parameters
- **Forensic analysis** — Perform a full forensic review of the compromised web server to determine the scope of attacker activity, any data accessed, and whether persistence mechanisms were established
- **Network-wide sweep** — Search logs for additional connections from the source IP or related CHINANET ranges to identify any other targeted systems
- **Harden public-facing assets** — Review all internet-facing services for similar input validation weaknesses

---

## References

- ARIN WHOIS/RDAP — Source IP 61.177.172.87 (CHINANET Jiangsu Province)
- Web server access logs — POST request analysis and HTTP response codes
- EDR endpoint telemetry — Web server 172.16.17.16
- Email logs — Penetration test verification
- MITRE ATT&CK: [T1190 — Exploit Public-Facing Application](https://attack.mitre.org/techniques/T1190/)
- MITRE ATT&CK: [T1059 — Command and Scripting Interpreter](https://attack.mitre.org/techniques/T1059/)
- MITRE ATT&CK: [T1033 — System Owner/User Discovery](https://attack.mitre.org/techniques/T1033/)
