# SOC Investigation Report — SOC282: Phishing Alert — Deceptive Mail Detected

---

**Information:**

| Field | Detail |
|---|---|
| Event Time | May 13, 2024, 09:22 AM |
| Severity | Medium |
| Rule | SOC282 — Phishing Alert: Deceptive Mail Detected |

---

## Executive Summary

A phishing detection rule flagged a deceptive email sent to an internal user containing a malicious URL and a suspicious ZIP attachment. Investigation confirmed the recipient opened the email, clicked the malicious URL — downloading malware onto their system — and the endpoint subsequently communicated with a command-and-control server. The malicious email was deleted, the endpoint was contained via EDR, and a network-wide sweep confirmed no additional systems were affected. The incident was escalated to Tier 2 for full remediation.

---

## Alert Details

| Field | Detail |
|---|---|
| Rule Triggered | SOC282 — Phishing Alert: Deceptive Mail Detected |
| Log Source | Email Security |
| Source Address | free@coffeeshooop.com |
| Destination Address | Felix@letsdefend.io |
| SMTP Address | 103.80.134.63 |
| Attachment | free-coffee.zip |
| Email Delivered | Yes — reached recipient's inbox |
| Status | Closed — Contained and Escalated to Tier 2 |

---

## Investigation Timeline

| Timestamp (UTC) | Action |
|---|---|
| 2026-03-15 05:59 | Alert triggered — SOC282: Phishing Alert - Deceptive Mail Detected on Felix@letsdefend.io's inbox |
| 2026-03-15 06:01 | Submitted the embedded URL to VirusTotal — flagged heavily by multiple security vendors as malicious |
| 2026-03-15 06:02 | Confirmed the phishing email was successfully delivered to the recipient's inbox |
| 2026-03-15 06:03 | Deleted the malicious email from the recipient's inbox to prevent further interaction |
| 2026-03-15 06:07 | Inspected the recipient's endpoint via EDR to identify the operating system for proper sandbox/VM analysis of the attachment and extraction of the C2 address |
| 2026-03-15 06:10 | Discovered the recipient had visited the malicious URL embedded in the email — malware likely downloaded onto the system |
| 2026-03-15 06:20 | Identified outbound communication from the recipient's endpoint to a C2 address — confirmed the system is compromised |
| 2026-03-15 06:21 | Contained the recipient's endpoint via EDR — full network isolation applied |
| 2026-03-15 06:25 | Performed a network-wide sweep filtering for connections to the C2 address — no additional systems communicated with the C2; infection limited to a single endpoint |

---

## Findings

An external actor sent a phishing email from `free@coffeeshooop.com` to an internal employee (Felix@letsdefend.io) containing a malicious URL and a ZIP attachment (`free-coffee.zip`). The email bypassed initial filtering and was delivered to the recipient's inbox. The recipient opened the email and clicked the malicious URL, which resulted in malware being downloaded onto their system. EDR telemetry confirmed the compromised endpoint subsequently established outbound communication with a command-and-control server. A network-wide sweep confirmed no other internal systems contacted the C2 address — the compromise was limited to a single endpoint. The affected system was contained and the incident escalated to Tier 2 for full remediation.

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Phishing: Spearphishing Attachment | T1566.001 | Malicious ZIP file (`free-coffee.zip`) delivered via phishing email |
| User Execution: Malicious File | T1204.002 | Recipient clicked the malicious URL and likely executed the downloaded payload |
| Ingress Tool Transfer | T1105 | Malware downloaded onto the endpoint via the malicious URL |
| Application Layer Protocol: Web Protocols | T1071.001 | Compromised endpoint established outbound HTTP/S communication to a C2 server |

---

## Verdict

**True Positive** — Confirmed phishing attack resulting in endpoint compromise. Recipient clicked a malicious URL, malware was downloaded, and C2 communication was established. Endpoint contained; no lateral spread detected.

---

## Response Actions Taken

- Deleted the malicious phishing email from the recipient's inbox
- Contained the affected endpoint via EDR — full network isolation applied
- Performed a network-wide sweep for C2 indicators — no additional compromised hosts identified
- Escalated to Tier 2 / IR team for full forensic analysis and remediation

---

## Recommendations

- **Block C2 address** — The C2 IP is not affiliated with any major telecommunications or cloud provider and has a documented history of abuse; add it to firewall deny lists, DNS sinkholes, and threat intelligence blocklists
- **Blacklist malware hash** — Add the hash of the downloaded payload to endpoint protection process blocklists across the environment
- **Block SMTP relay** — Add 103.80.134.63 to email gateway deny lists to prevent future delivery from this relay
- **Block sender domain** — Add `coffeeshooop.com` to email filtering blocklists — the misspelled domain is a clear indicator of phishing infrastructure
- **User awareness training** — Flag Felix for targeted phishing awareness training as a follow-up to this incident
- **Attachment policy review** — Evaluate email gateway policies for handling inbound ZIP attachments from unknown external senders

---

## References

- VirusTotal analysis — Embedded URL and sender domain
- EDR endpoint telemetry — Felix's workstation
- Firewall logs — Outbound C2 connection
- MITRE ATT&CK: [T1566.001 — Phishing: Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/)
- MITRE ATT&CK: [T1204.002 — User Execution: Malicious File](https://attack.mitre.org/techniques/T1204/002/)
- MITRE ATT&CK: [T1105 — Ingress Tool Transfer](https://attack.mitre.org/techniques/T1105/)
- MITRE ATT&CK: [T1071.001 — Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)
