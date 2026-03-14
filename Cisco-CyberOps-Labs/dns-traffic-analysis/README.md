# DNS Traffic Analysis with Wireshark

This lab explores DNS query and response traffic using Wireshark.

The objective was to capture DNS traffic, analyze packet structure, and understand how domain names resolve to IP addresses.

Tool used:
- Wireshark

Lab source:
Cisco Networking Academy – Exploring DNS Traffic

## Lab Objectives

- Capture DNS traffic using Wireshark
- Filter DNS packets using port 53
- Analyze packet structure (Ethernet, IP, UDP, DNS)
- Identify DNS queries and responses
- Understand CNAME and A record resolution

---

## Tools

- Wireshark
- Windows Command Prompt
- nslookup

---

## DNS Analysis

During the analysis, DNS traffic was captured and filtered using:

udp.port == 53

The DNS query for **www.cisco.com** revealed multiple CNAME records before resolving to the final IP address.

This indicates that Cisco uses a **Content Delivery Network (CDN)** provided by Akamai.

Example resolution chain:

www.cisco.com  
→ www.cisco.com.akadns.net  
→ wwwds.cisco.com.edgekey.net  
→ e2867.dsca.akamaiedge.net  
→ final IPv6 address

---

## Security Insight

DNS traffic analysis can reveal:

- external services used by hosts
- CDN infrastructure
- suspicious domains
- possible malware communication

Attackers may use packet capture tools such as Wireshark to perform reconnaissance and identify network behavior.
