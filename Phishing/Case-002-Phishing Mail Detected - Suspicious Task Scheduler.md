# SOC Investigation Report — Phishing Mail Detected: Malicious Attachment

---

**Information:**

| Field | Detail |
|---|---|
| Event Time | Mar 21, 2021, 12:26 PM |
| Severity | High |
| Rule | Phishing Mail Detected — Inbound Email with Attachment |

---

## Executive Summary

A phishing detection rule flagged an inbound email containing a malicious attachment sent from an external address to an internal employee. VirusTotal analysis confirmed the attachment is almost certainly malware, with nearly half of all security vendors flagging the file. However, the email was blocked before delivery and never reached the recipient's mailbox. No compromise occurred. The alert is confirmed as a true positive, and no escalation is required.

---

## Alert Details

| Field | Detail |
|---|---|
| Rule Triggered | Phishing Mail Detected |
| Log Source | Email gateway / SIEM |
| Source Email | aaronluo@cmail.carleton.ca (External) |
| Destination Email | mark@letsdefend.io (Internal) |
| SMTP Address | 189.162.189.159 |
| Attachment | Yes — Malicious (flagged by ~50% of VirusTotal vendors) |
| Email Delivered | No — Blocked by email gateway |
| Status | Closed — True Positive, No Escalation |

---

## Investigation Timeline

| Timestamp (UTC) | Action |
|---|---|
| 2021-03-21 12:26 | Alert triggered — Phishing Mail Detected with attachment |
| 2021-03-21 12:28 | Initial triage — reviewed alert details; noted external sender, internal recipient, attachment present, and suspicious mail content |
| 2021-03-21 12:30 | Extracted attachment hash and submitted to VirusTotal — nearly half of all security vendors flagged the file as malicious (malware/virus) |
| 2021-03-21 12:33 | Checked SIEM alert for delivery status — confirmed the email was blocked by the email gateway and never delivered to the recipient |
| 2021-03-21 12:35 | Verified the recipient's mailbox (mark@letsdefend.io) — no trace of the email, no signs of compromise |
| 2021-03-21 12:37 | Investigated the source address (aaronluo@cmail.carleton.ca) and SMTP relay (189.162.189.159) — external origin, likely a compromised or spoofed account |
| 2021-03-21 12:39 | Assessed all findings — confirmed true positive; email was blocked, no impact to internal systems |
| 2021-03-21 12:40 | Closed alert — no escalation required |

---

## Findings

An external actor using the email address aaronluo@cmail.carleton.ca attempted to deliver a malicious attachment to an internal employee (mark@letsdefend.io) via SMTP relay 189.162.189.159. VirusTotal analysis of the attachment confirmed it is malicious, with approximately half of all security vendors flagging the file. The email gateway successfully blocked the message before delivery, preventing any impact to the recipient's system. No indicators of compromise were found on the destination endpoint.

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Phishing: Spearphishing Attachment | T1566.001 | Attacker attempted to deliver malware via a malicious email attachment targeting an internal user |

---

## Verdict

**True Positive** — Confirmed phishing attempt with a malicious attachment. Email was successfully blocked by the email gateway before delivery. No compromise occurred.

---

## Response Actions Taken

- Confirmed email was blocked and never delivered to the recipient
- Verified no indicators of compromise on the recipient's endpoint
- No escalation required — threat was neutralized by existing email security controls

---

## Recommendations

- **Block sender and SMTP relay** — Add aaronluo@cmail.carleton.ca and 189.162.189.159 to email deny lists and threat intelligence blocklists
- **Notify source domain** — Consider notifying Carleton University (cmail.carleton.ca) that the account may be compromised and is being used to distribute malware
- **Hash blocklisting** — Add the malicious attachment hash to endpoint protection and email gateway blocklists to prevent future delivery attempts
- **User awareness** — Inform mark@letsdefend.io of the attempted attack as a teaching moment, even though the email was blocked
- **Monitor for follow-up attempts** — Watch for additional emails from the same sender, SMTP relay, or containing the same attachment hash targeting other internal users

---

## References

- VirusTotal analysis — Malicious attachment (~50% vendor detection rate)
- Email gateway logs — Blocked delivery confirmation
- SIEM alert — Phishing Mail Detected
- MITRE ATT&CK: [T1566.001 — Phishing: Spearphishing Attachment](https://attack.mitre.org/techniques/T1566/001/)
