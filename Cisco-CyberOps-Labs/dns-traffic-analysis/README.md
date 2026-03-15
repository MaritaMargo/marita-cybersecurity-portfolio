# DNS Traffic Analysis with Wireshark

This lab demonstrates how DNS traffic can be captured and analyzed using Wireshark.  
The goal was to observe how domain names resolve to IP addresses and examine the structure of DNS packets.

Lab source: Cisco Networking Academy – Exploring DNS Traffic

---

## Tools Used

- Wireshark
- Windows Command Prompt
- nslookup

---

## Wireshark Capture Interface

Wireshark was started and the active network interface was selected to capture traffic.

![Wireshark Interface](screenshots/01-wireshark-interface.png)

---

## Generating DNS Traffic

Before capturing traffic in Wireshark, DNS queries were generated using the Windows Command Prompt.

The DNS cache was cleared to force the system to perform a new DNS lookup.

![DNS Query Command](screenshots/08-dns-nslookup-command.png)


## Capturing DNS Traffic
This ensured that a fresh DNS request would appear in the packet capture.


These commands forced the system to perform a new DNS lookup.

![DNS Capture](screenshots/02-dns-capture.png)

---

## Filtering DNS Packets

Initially the following Wireshark display filter was used:

This filter shows DNS traffic transported over **UDP**, which is the most common transport protocol for DNS queries.

However, Wireshark also provides a more general filter:

The `dns` filter displays all DNS traffic regardless of the transport protocol.  
This includes DNS over **UDP** as well as DNS over **TCP** (which can occur during zone transfers or large DNS responses).

For this lab, `udp.port == 53` was sufficient because the captured traffic consisted of standard DNS queries and responses.

This filter displays only DNS packets.

![DNS Filter](screenshots/03-dns-filter-port53.png)

### Wireshark DNS Filters

| Filter | What it shows | Use case |
|------|------|------|
| dns | All DNS traffic (UDP + TCP) | Recommended general DNS analysis filter |
| udp.port == 53 | DNS over UDP only | Standard DNS queries |
| dns.flags.response == 0 | DNS queries only | Investigating client requests |
| dns.flags.rcode != 0 | DNS errors | Troubleshooting NXDOMAIN or failures |

---

## DNS Query Packet Structure

The DNS query packet contains multiple protocol layers:

- Ethernet
- Internet Protocol (IPv6)
- User Datagram Protocol (UDP)
- Domain Name System (DNS)

### DNS Query Packet Fields

| Field | Value | Description |
|------|------|-------------|
| Transaction ID | 0x0002 | Unique identifier for the DNS query |
| Query Name | www.cisco.com | Requested domain |
| Query Type | AAAA (28) | Request for IPv6 address |
| Flags | 0x0100 | Standard query, recursion desired |
| Questions | 1 | One DNS question |


![DNS Query Analysis](screenshots/04-dns-query-ethernet.png)

---

## IP Layer Analysis

The Internet Protocol layer shows the source and destination IP addresses involved in the DNS communication.

![IP Analysis](screenshots/05-dns-ip-analysis.png)

### IPv4 vs IPv6 Observation

During the packet inspection, both IPv4 and IPv6 addresses were observed in the DNS responses.

The DNS response included IPv6 records (**AAAA records**) as well as IPv4 addresses.

IPv6 is increasingly used by modern web services because it provides a much larger address space and improved routing efficiency compared to IPv4.

In this capture, the client requested an IPv6 address for `www.cisco.com`, which is typical for modern operating systems typically send both A and AAAA queries
(DNS Happy Eyeballs behavior). If a valid AAAA response is received,
IPv6 connectivity may be used; otherwise IPv4 is used as fallback.

---

## UDP Transport Layer

DNS queries are typically transmitted using **UDP port 53**.

![UDP Layer](screenshots/06-dns-udp-port53.png)

---

## DNS Response Records

The DNS response reveals a chain of **CNAME records** before resolving to the final address.

Example resolution chain:
www.cisco.com
→ CNAME → www.cisco.com.akadns.net
→ CNAME → wwwds.cisco.com.edgekey.net
→ CNAME → wwwds.cisco.com.edgekey.net.globalredir.akadns.net
→ CNAME → e2867.dsca.akamaiedge.net
→ AAAA → 2a02:26f0:e2:784::b33
→ AAAA → 2a02:26f0:e2:788::b33
→ A → 23.32.26.4

This multi-level CNAME chain is typical for Akamai CDN infrastructure,
which dynamically distributes traffic across edge servers.

This shows that Cisco uses **Akamai CDN infrastructure**.

![DNS Response](screenshots/07-dns-response-records.png)

---
## Detailed DNS Packet Analysis

To better understand the DNS protocol, one DNS query and one DNS response packet were examined in detail using Wireshark.

### DNS Query Packet

The DNS query packet contained the following key values:

- **Query type:** AAAA (IPv6 address request)
- **Questions:** 1
- **Answers:** 0
- **Flags:** Standard query

The client requested the IPv6 address for:


This query was sent from the client to the DNS server using **UDP port 53**.

---

### DNS Response Packet

The DNS response packet returned multiple records.

Key fields observed:

- **Flags:** Standard query response (No error)
- **Questions:** 1
- **Answer count:** 6 resource records
- **Authority RRs:** 0
- **Additional RRs:** 0

The response contained a chain of **CNAME records** before resolving to the final IPv6 address.

Example chain observed:


This behavior is typical for large web services that use **Content Delivery Networks (CDN)** such as Akamai.

The DNS records also included **TTL values**, which define how long the response can be cached by the client.

## Security Perspective

During the packet capture only legitimate and commonly used domains were observed, including:

- google.com
- cisco.com
- brave.com

No suspicious domain patterns were identified (such as long random subdomains or unusual query structures), which could otherwise indicate techniques like **DNS tunneling** or data exfiltration.

The DNS traffic observed in this capture appears to represent normal client behavior generated by standard web services and software updates.

From a defensive security perspective, Wireshark can be used to detect abnormal DNS activity by analyzing query frequency and domain patterns. Tools such as **Wireshark Statistics → DNS** or **Expert Information** can help identify:

- unusually high DNS query rates
- repeated failed lookups
- domains with randomized subdomain structures

These patterns may indicate **malware communication, DNS tunneling, or command-and-control infrastructure**.

It is also important to note that the captured DNS traffic was **unencrypted traditional DNS over UDP (port 53)**.  
Modern systems increasingly use encrypted DNS protocols such as **DNS-over-HTTPS (DoH)** or **DNS-over-TLS (DoT)** to protect DNS queries from interception and monitoring.

However, from a network security monitoring perspective, encrypted DNS can reduce visibility for defenders, making traffic analysis more challenging.

In this lab, traditional unencrypted DNS over UDP (port 53) was observed.
However, modern systems increasingly use encrypted DNS protocols such as
DNS-over-HTTPS (DoH, port 443) or DNS-over-TLS (DoT, port 853).
Monitoring DNS activity is an important part of network security and threat detection.

---

## Conclusion

This lab demonstrated how DNS queries and responses can be captured and analyzed using Wireshark.

By inspecting packet structure and DNS record chains, it becomes possible to understand how domain names are resolved and how large services use CDN infrastructure.

This exercise also highlights why **DNS monitoring is an important component of security monitoring systems** such as SIEM platforms (Splunk, ELK, Sentinel).

DNS logs often provide early indicators of malicious activity, including suspicious domains, command-and-control communication, or data exfiltration attempts.

This exercise demonstrates why DNS monitoring is an important component
of SOC and SIEM environments (Splunk, ELK, Sentinel), where DNS logs
can reveal early indicators of command-and-control traffic, DNS tunneling
or domain generation algorithms (DGA).

