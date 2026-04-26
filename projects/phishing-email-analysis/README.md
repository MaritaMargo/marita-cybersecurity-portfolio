# Phishing Email Analysis — SOC L1 Triage Workflow

## Project Overview

End-to-end analysis of a real phishing email sample received in a personal inbox.
This project simulates a SOC L1 analyst triage workflow — from initial detection through header analysis, authentication checks, IP reputation lookup, and final verdict.

**Analyst:** Marita Troksa  
**Date:** April 2026  
**Difficulty:** Beginner–Intermediate  
**Tools used:** MXToolbox · AbuseIPDB · VirusTotal · URLScan.io

---

## Sample Details

| Field | Value |
|---|---|
| Subject | LABAS ZIŅAS |
| Display Name | Mrs. Tina Lee |
| From | g36989279@gmail.com |
| Reply-To | www.citibank08@gmail.com |
| Date | Fri, 27 Mar 2026 |
| To | undisclosed-recipients |
| Claims to be | SVF (IMF) Representative — Tobias Andrian |

---

## Red Flags — First Impressions

Before any technical analysis, the following were immediately suspicious:

- Sender claims to represent the International Monetary Fund (IMF / SVF)
- Real IMF domain is @imf.org — sender uses a personal Gmail account
- Email written in Latvian — targeted at Latvian-speaking victims
- Requests sensitive personal data: bank account number, SWIFT code, passport copy
- Promises $1,500,000 USD transfer — classic advance fee fraud
- Contact address www.citibank08@gmail.com impersonates Citibank using Gmail
- To: undisclosed-recipients — mass distribution campaign

---

## Step 1 — Sender Identity Analysis

| Check | Expected | Observed | Result |
|---|---|---|---|
| From domain | @imf.org | @gmail.com | ❌ FAIL |
| Return-Path | matches From | g36989279@gmail.com | ✅ No spoofing |
| Reply-To | matches From | www.citibank08@gmail.com | ❌ MISMATCH |
| Display name | consistent | "Mrs. Tina Lee" | ⚠️ Unverifiable |

**Key finding:** The Reply-To field differs from From — any reply would be redirected to www.citibank08@gmail.com, a Gmail address impersonating Citibank. This is a classic social engineering technique to redirect victim responses to an attacker-controlled inbox.

---

## Step 2 — Email Header Analysis (MXToolbox)

**Tool:** mxtoolbox.com/EmailHeaders

![MXToolbox Header Analysis](./screenshots/mxtoolbox-results.png)

### Authentication Results

| Check | Result | Notes |
|---|---|---|
| DMARC Compliant | ❌ FAIL | Policy not enforced |
| SPF Alignment | ✅ PASS | |
| SPF Authenticated | ❌ FAIL | Sender server not authorised |
| DKIM Alignment | ✅ PASS | |
| DKIM Authenticated | ✅ PASS | Signed by gmail.com |

**DMARC Policy: p=none** — Gmail's DMARC policy is set to "none", meaning failed emails are not rejected. This allows spam campaigns to pass through Gmail infrastructure.

### Hop Chain Analysis

| Hop | From | By | Protocol | Time (UTC) |
|---|---|---|---|---|
| 1 | — | mail-oo1-f67.google.com | SMTP | 3/27/2026 1:22:14 PM |
| 2 | mail-oo1-f67.google.com [209.85.161.67] | timmy4-main.inbox.lv | ESMTPS | 3/27/2026 1:22:14 PM |
| 3 | kas7.inbox.lv 10.0.1.97 | ham4.inbox.lv | ESMTP | 3/27/2026 1:22:14 PM |
| 4 | ham4.inbox.lv 10.0.1.104 | timmy5-clean.inbox.lv | ESMTP | 3/27/2026 1:22:17 PM |
| 5 | timmy5-clean.inbox.lv 10.0.1.75 | brat27.inbox.lv | LMTPA | 3/27/2026 1:22:17 PM |

**Total delivery time:** 3 seconds  
**Origin:** Gmail infrastructure → inbox.lv mail servers

---

## Step 3 — IP Reputation Analysis (AbuseIPDB)

**Tool:** abuseipdb.com  
**IP analysed:** 209.85.161.67

![AbuseIPDB IP Lookup](./screenshots/abuseipdb-reports.png)

| Field | Value |
|---|---|
| ISP | Google LLC |
| Usage Type | Data Center / Web Hosting / Transit |
| ASN | AS15169 |
| Hostname | mail-oo1-f67.google.com |
| Country | United States |
| Confidence of Abuse | 0% |
| Times Reported | 33 times from 9 distinct sources |
| Most Recent Report | 22 hours ago |
| Category | Email Spam |

**Key finding:** The IP belongs to legitimate Google infrastructure, however it has been reported 33 times by 9 independent sources for Email Spam activity. AbuseIPDB flagged it as "potentially still actively engaged in abusive activities." This confirms the email is part of an active spam/phishing campaign using Gmail servers.

---

## Step 4 — IOC Lookup (VirusTotal)

**Tool:** virustotal.com  
**IP analysed:** 209.85.161.67

![VirusTotal IP Lookup](./screenshots/virustotal-ip-lookup.png)

**Result:** 0/94 — Clean

> **Note:** VirusTotal confirms this is legitimate Google infrastructure (AS15169, Google LLC). A clean score is expected — the malicious activity originates from abuse of Gmail servers, not a dedicated attack infrastructure. This is consistent with AbuseIPDB findings.

---

## Step 5 — Infrastructure Context (URLScan.io)

**Tool:** urlscan.io  
**Search:** gmail.com as sending infrastructure

![URLScan gmail.com search](./screenshots/urlscan-gmail-search.png)

Search for `gmail.com` confirms active scanning activity across multiple domains using Gmail as sending infrastructure. URLScan is most effective when phishing URLs are present in the email body — in this case it validates gmail.com as a known platform frequently appearing in security scans and phishing campaigns.

---

## Step 6 — IOC Summary

| Type | Value | Source | Notes |
|---|---|---|---|
| Email | g36989279@gmail.com | Email header | Sender — auto-generated Gmail account |
| Email | www.citibank08@gmail.com | MXToolbox | Reply-To — impersonates Citibank |
| IP | 209.85.161.67 | MXToolbox Hop 2 | Gmail server — reported 33x for spam |
| ASN | AS15169 | AbuseIPDB | Google LLC |
| Domain | gmail.com | Header | Sending infrastructure |

---

## Step 7 — Analyst Verdict

**Classification:** ⛔ PHISHING / ADVANCE FEE FRAUD (419 Scam)  
**Confidence:** HIGH  
**Threat Type:** Social engineering · Identity theft · Financial fraud  
**Target:** Latvian-speaking email users  
**Campaign type:** Mass distribution (undisclosed-recipients)

### Summary

This email is a classic advance fee fraud (also known as 419 scam) combined with identity theft elements. The attacker impersonates an IMF representative offering a large financial transfer, then requests banking details, SWIFT codes, and a passport copy from the victim.

Key indicators confirming phishing verdict:

1. Institution impersonation — IMF contacted from personal Gmail
2. Reply-To mismatch redirecting responses to attacker-controlled address
3. SPF Authentication failure
4. DMARC policy not enforced — allows fraudulent email delivery
5. Mass distribution to undisclosed recipients
6. Sending IP reported 33 times for Email Spam within last 30 days
7. Requests highly sensitive personal and financial data
8. Latvian-language targeting suggests geo-targeted campaign

---

## Recommended SOC Actions

| Action | Priority |
|---|---|
| Mark as phishing, move to quarantine | High |
| Block sender domain g36989279@gmail.com | High |
| Add 209.85.161.67 to watchlist | Medium |
| Submit IOCs to threat intel platform | Medium |
| Notify user — do not respond or provide data | High |
| Check if other users received same campaign | Medium |

---

## Skills Demonstrated

Email header analysis · SPF/DKIM/DMARC verification · IP reputation lookup · IOC extraction · Threat triage · Analyst reporting · MXToolbox · AbuseIPDB · VirusTotal · URLScan.io · Social engineering recognition

---

## Tools Used

| Tool | Purpose | URL |
|---|---|---|
| MXToolbox Email Header Analyzer | Header parsing, SPF/DKIM/DMARC | mxtoolbox.com/EmailHeaders |
| AbuseIPDB | IP reputation check | abuseipdb.com |
| VirusTotal | IOC lookup | virustotal.com |
| URLScan.io | Domain/URL analysis | urlscan.io |
