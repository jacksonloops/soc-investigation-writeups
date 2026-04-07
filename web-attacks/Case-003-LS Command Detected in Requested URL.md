# SOC Investigation Report — Incident: LS Command Detected in Requested URL

---

**Information:**

| Field | Detail |
|---|---|
| Event Time | (See Investigation Timeline) |
| Severity | Low |
| Rule | URL Contains LS Command |

---

## Executive Summary

A detection rule flagged an outbound HTTPS request from an internal endpoint after identifying what appeared to be an "ls" command embedded in the requested URL. Investigation revealed the flagged URL was a legitimate search query on the company's own blog (`letsdefend.io/blog/?s=skills`). Both the source and destination IPs were verified — the source belongs to a company endpoint and the destination resolves to a Cloudflare server hosting the company's web infrastructure. Analysis of surrounding traffic from the same endpoint showed no malicious activity. The alert was determined to be a **false positive**, and no escalation or response actions were required.

---

## Alert Details

| Field | Detail |
|---|---|
| Rule Triggered | URL Contains LS Command |
| Log Source | Network / IDS |
| Source IP | 172.16.17.46 |
| Source Port | 46843 |
| Destination IP | 188.114.96.15 |
| Destination Port | 443 |
| Protocol | HTTPS |
| Flagged URL | `https://letsdefend.io/blog/?s=skills` |
| Status | Closed — False Positive |

---

## Investigation Timeline

| Step | Action |
|---|---|
| 1 | Alert triggered — URL flagged for containing a string matching an "ls" command pattern |
| 2 | Reviewed the alert trigger reason — rule fired because the URL contained the string "ls" |
| 3 | Investigated source IP (172.16.17.46) — confirmed via SIEM as an internal company endpoint |
| 4 | Investigated destination IP (188.114.96.15) — SIEM search returned the same company domain; ARIN WHOIS/RDAP confirmed the IP belongs to Cloudflare infrastructure |
| 5 | Analyzed the full URL (`https://letsdefend.io/blog/?s=skills`) — confirmed it is a standard search query on the company blog, not an injected OS command |
| 6 | Reviewed additional traffic from the source endpoint — no indicators of malicious activity observed |

---

## Findings

An internal endpoint (172.16.17.46) made an HTTPS request to the company's blog hosted behind Cloudflare (188.114.96.15). The detection rule flagged the URL because the search query string contained characters matching an "ls" command pattern. Upon analysis, the full URL (`https://letsdefend.io/blog/?s=skills`) is a benign search request — a user on the company network was simply searching for content on the blog. The destination IP was verified as Cloudflare infrastructure serving the company's own domain. A review of additional traffic from the source endpoint revealed no suspicious or malicious behavior.

---

## MITRE ATT&CK Mapping

Not applicable — no malicious activity identified.

---

## Verdict

**False Positive** — The detection rule matched a benign search query string in a legitimate URL. No command injection, exploitation, or malicious activity occurred. The request was normal user browsing behavior on an internal company resource.

---

## Response Actions Taken

- No escalation required
- No containment or remediation actions necessary
- Alert closed as false positive

---

## Recommendations

- **Tune detection rule** — Consider refining the "URL Contains LS" rule to reduce false positives by excluding matches within URL query parameters of known-safe internal domains or by requiring additional context (e.g., pattern matching for full command syntax rather than simple substring matches)

---

## References

- SIEM endpoint lookup — Source IP 172.16.17.46
- ARIN WHOIS/RDAP — Destination IP 188.114.96.15 (Cloudflare)
- Network traffic logs — Source endpoint activity review
