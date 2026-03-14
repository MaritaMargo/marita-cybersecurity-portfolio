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

## Capturing DNS Traffic

DNS traffic was generated using the following commands:


These commands forced the system to perform a new DNS lookup.

![DNS Capture](screenshots/02-dns-capture.png)

---

## Filtering DNS Packets

To isolate DNS traffic, the following display filter was applied:


This filter displays only DNS packets.

![DNS Filter](screenshots/03-dns-filter-port53.png)

---

## DNS Query Packet Structure

The DNS query packet contains multiple protocol layers:

- Ethernet
- Internet Protocol (IPv6)
- User Datagram Protocol (UDP)
- Domain Name System (DNS)

![DNS Query Analysis](screenshots/04-dns-query-ethernet.png)

---

## IP Layer Analysis

The Internet Protocol layer shows the source and destination IP addresses involved in the DNS communication.

![IP Analysis](screenshots/05-dns-ip-analysis.png)

---

## UDP Transport Layer

DNS queries are typically transmitted using **UDP port 53**.

![UDP Layer](screenshots/06-dns-udp-port53.png)

---

## DNS Response Records

The DNS response reveals a chain of **CNAME records** before resolving to the final address.

Example resolution chain:


This shows that Cisco uses **Akamai CDN infrastructure**.

![DNS Response](screenshots/07-dns-response-records.png)

---

## Security Perspective

DNS traffic analysis helps security analysts:

- detect suspicious domains
- identify command-and-control communication
- investigate external connections
- understand network behavior

Monitoring DNS activity is an important part of network security and threat detection.

---

## Conclusion

This lab demonstrated how DNS queries and responses can be captured and analyzed using Wireshark.  
Understanding DNS traffic is essential for both network troubleshooting and cybersecurity analysis.

