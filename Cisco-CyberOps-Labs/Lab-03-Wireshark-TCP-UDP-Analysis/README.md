# Wireshark TCP and UDP Traffic Analysis

This lab demonstrates how to capture and analyze TCP and UDP traffic using Wireshark.

The objective was to observe how network communication works at the packet level and identify the behavior of TCP and UDP protocols.

---

# Step 1 — Selecting the Capture Interface

The first step was selecting the correct network interface in Wireshark.

In this case the **eth0 interface** was used to capture network traffic generated from the virtual machine.

![Wireshark interface](screenshots/01-wireshark-capture-interface.png)

---

# Step 2 — Starting Packet Capture

Packet capture was started in Wireshark in order to observe live network traffic.

During this phase Wireshark begins collecting packets transmitted through the network interface.

![Packet capture](screenshots/02_packet_capture_started.png)

---

# Step 3 — TCP SYN Packet Analysis

This packet represents the **first step of the TCP three-way handshake**.

The client initiates communication with a SYN packet sent to the destination server.

![TCP SYN](screenshots/03_tcp_syn_packet_analysis.png)

---

# Step 4 — TCP SYN-ACK Packet

After the client sends the SYN packet, the server responds with a **SYN-ACK packet**.

This packet confirms that the server received the request and is ready to establish a connection.

The SYN-ACK packet contains both the **SYN flag** and the **ACK flag**, acknowledging the client's initial request.

![TCP SYN-ACK](screenshots/04_tcp_syn_ack_packet.png)

---

# Step 5 — TCP ACK Packet

The final step of the **TCP three-way handshake** is the ACK packet sent by the client.

At this stage the connection between the client and server is successfully established.

Once the handshake is complete, application protocols such as **FTP** can begin transmitting data over the TCP connection.

![TCP ACK](screenshots/05_tcp_ack_packet.png)

---

# Step 6 — FTP Protocol Commands

After the TCP connection is established, the FTP protocol begins communication between the client and the server.

FTP uses **TCP port 21** for control communication. In this capture we can observe FTP commands and server responses such as:

- USER
- PASS
- EPSV
- PASV
- PORT

These commands are used to authenticate and establish data transfer channels.

![FTP Protocol](screenshots/06_ftp_protocol_commands.png)

---

# Step 7 — Following TCP Stream

Wireshark allows analysts to reconstruct a full communication session using the **Follow TCP Stream** feature.

This feature is extremely useful during incident response and network investigations because it allows analysts to observe the full conversation between a client and server.

In this case the FTP communication between the client and server can be reconstructed.

![TCP Stream](screenshots/07_follow_tcp_stream.png)

---

# Step 8 — TCP Session Termination

After data transmission is completed, the TCP session is closed using the **FIN flag**.

The FIN packet indicates that one side of the communication wants to terminate the connection.

In Wireshark we can observe the TCP termination process where both sides acknowledge the session closure.

![TCP FIN](screenshots/08_tcp_session_termination.png)

---

# Step 9 — UDP DNS Packet Analysis

Unlike TCP, UDP is a **connectionless protocol**. It does not establish a session before sending data.

In this capture we observe a **DNS query and response** transmitted using UDP on port **53**.

UDP is commonly used for DNS because it provides faster communication without the overhead of connection establishment.

![UDP DNS](screenshots/09_udp_dns_packet_analysis.png)

---

# Security Analysis Summary

This lab demonstrates how Wireshark can be used to analyze network traffic and understand how protocols operate at different layers of the network stack.

Key observations:

- TCP uses a **three-way handshake** to establish reliable connections.
- FTP communication operates over **TCP port 21**.
- TCP sessions are properly terminated using **FIN packets**.
- DNS traffic commonly uses **UDP port 53**.
- Wireshark provides powerful capabilities for analyzing packet headers, protocol behavior, and network communication patterns.

Understanding these patterns is essential for **Security Operations Center (SOC) analysts**, incident responders, and network defenders who investigate suspicious traffic and network anomalies.
