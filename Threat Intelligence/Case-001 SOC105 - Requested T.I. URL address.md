# SOC Investigation Report — SOC105: Phishing URL / Malicious File Detected

---

**Information:**

| Field | Detail |
|---|---|
| Case ID | Case-001 |
| Event Time | Apr 07, 2026, 09:15 AM |
| Severity | High |
| Rule | SOC105 — Requested Threat Intel URL Address |

---

## Executive Summary

A threat intelligence rule flagged a suspicious URL requested by an internal system. Investigation confirmed the URL is a known phishing site, flagged by 13 vendors on VirusTotal. Network log analysis revealed an internal endpoint contacted the malicious site twice. EDR inspection of the endpoint uncovered a malicious Excel file actively running on the system — the same process responsible for the outbound network requests to the phishing URL. The endpoint was contained via EDR to prevent further communication with the suspected C2 infrastructure.

---

## Alert Details

| Field | Detail |
|---|---|
| Rule Triggered | SOC105 — Requested Threat Intel URL Address |
| Log Source | Network logs, EDR telemetry |
| Flagged URL | \[Redacted\] — Phishing site (13/91 vendors on VirusTotal) |
| Affected Endpoint | \[Internal host — redacted\] |
| Observation | Internal endpoint contacted known-malicious URL twice; malicious Excel file identified as the originating process |
| Status | Closed — Contained |

---

## Investigation Timeline

| Timestamp (UTC) | Action |
|---|---|
| 2026-04-07 09:15 | Alert triggered — SOC105: Requested Threat Intel URL Address |
| 2026-04-07 09:16 | Initial triage — retrieved the flagged URL from the alert for threat intelligence lookup |
| 2026-04-07 09:18 | Queried URL on VirusTotal — flagged by 13/91 vendors as a phishing site |
| 2026-04-07 09:19 | Analyzed network logs to identify any internal systems that interacted with the malicious URL |
| 2026-04-07 09:20 | Confirmed one internal endpoint accessed the phishing site twice |
| 2026-04-07 09:22 | Pivoted to EDR to inspect the affected endpoint |
| 2026-04-07 09:23 | EDR analysis identified a malicious Excel file running on the endpoint — network logs confirmed this process initiated the outbound requests to the phishing URL |
| 2026-04-07 09:24 | Assessed the situation: malicious Excel file acting as the initial payload, communicating with a suspected C2/phishing server |
| 2026-04-07 09:25 | Contained the endpoint using EDR to isolate it from the network |

---

## Findings

An internal endpoint was found communicating with a known phishing URL flagged by 13 threat intelligence vendors on VirusTotal. EDR inspection revealed a malicious Excel file actively running on the system, which was the source process behind the outbound requests. The malicious file appears to have been delivered to the endpoint through an unknown vector (likely phishing email) and was reaching out to the flagged URL — potentially functioning as a command-and-control (C2) channel. The endpoint was immediately contained via EDR.

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Phishing | T1566 | Likely initial access vector — malicious Excel file delivered to the endpoint |
| User Execution: Malicious File | T1204.002 | User executed a malicious Excel file which initiated outbound C2 communication |
| Command and Scripting Interpreter | T1059 | Excel file likely leveraged macros or embedded scripts to execute malicious code |
| Application Layer Protocol: Web Protocols | T1071.001 | Malware communicated with an external phishing/C2 server over HTTP(S) |

---

## Verdict

**True Positive** — Confirmed malicious activity. A malicious Excel file on an internal endpoint was actively communicating with a known phishing/C2 server. Endpoint contained.

---

## Response Actions Taken

- Contained the affected endpoint via EDR — network isolation applied
- Identified and documented the malicious Excel file and associated process
- Confirmed outbound communication to the phishing URL was limited to two requests

---

## Recommendations

- **Forensic analysis** — Perform a full forensic examination of the contained endpoint to determine the delivery vector and assess data exfiltration risk
- **Block the URL** — Add the flagged phishing URL to proxy deny lists, DNS sinkholes, and threat intelligence blocklists
- **Email investigation** — Search email logs for any messages containing the malicious Excel file or links to the phishing URL to identify additional affected users
- **Endpoint sweep** — Hunt across the environment for the same file hash to determine if other systems are compromised
- **User awareness** — Notify and re-train the affected user on phishing identification
- **Macro policy review** — Evaluate and restrict Office macro execution policies to prevent unauthorized macro-based payloads

---

## References

- VirusTotal analysis — Flagged URL (13/91 vendors)
- Network logs — Internal endpoint activity
- EDR telemetry — Process and containment records
- MITRE ATT&CK: [T1566 — Phishing](https://attack.mitre.org/techniques/T1566/)
- MITRE ATT&CK: [T1204.002 — User Execution: Malicious File](https://attack.mitre.org/techniques/T1204/002/)
- MITRE ATT&CK: [T1059 — Command and Scripting Interpreter](https://attack.mitre.org/techniques/T1059/)
- MITRE ATT&CK: [T1071.001 — Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)
