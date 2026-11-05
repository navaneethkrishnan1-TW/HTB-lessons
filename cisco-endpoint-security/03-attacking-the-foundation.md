# Cisco Endpoint Security — Module 3: Attacking the Foundation

## 1. Network Layer (Layer 3) Protocol Architecture: IPv4 vs. IPv6

The Internet Protocol (IP) operates as a **connectionless, best-effort** protocol responsible for end-to-end packet delivery. Because IP does not validate packet authenticity, threat actors routinely forge source IP addresses and manipulate header control fields.

```
IPv4 Header (Minimum 20 Bytes):
+-------------------------------+-------------------------------+
| Version (4b) | IHL (4b)       | DSCP (6b) | ECN (2b)          | Total Length (16b)
+-------------------------------+-------------------------------+
| Identification (16b)          | Flags (3b) | Fragment Offset (13b)
+-------------------------------+-------------------------------+
| Time-To-Live (8b)             | Protocol (8b)                 | Header Checksum (16b)
+-------------------------------+-------------------------------+
| Source IPv4 Address (32b)                                     |
+-------------------------------+-------------------------------+
| Destination IPv4 Address (32b)                                |
+-------------------------------+-------------------------------+

IPv6 Header (Fixed 40 Bytes):
+-------------------------------+-------------------------------+
| Version (4b) | Traffic Class (8b)            | Flow Label (20b)
+-------------------------------+-------------------------------+
| Payload Length (16b)          | Next Header (8b)              | Hop Limit (8b)
+-------------------------------+-------------------------------+
| Source IPv6 Address (128b)                                    |
+-------------------------------+-------------------------------+
| Destination IPv6 Address (128b)                               |
+-------------------------------+-------------------------------+
```

### Key Differences & Security Impacts
- **TTL vs. Hop Limit:** IPv4 decrements `TTL` at each hop to prevent routing loops; IPv6 replaces this with `Hop Limit`. When it reaches 0, an ICMP/ICMPv6 Time Exceeded packet is returned.
- **Fragmentation:** IPv4 allows intermediate routers to fragment packets exceeding MTU. In IPv6, routers **never** fragment packets; path MTU discovery or host-level fragmentation using **Extension Headers (EHs)** is required.
- **Header Checksum:** IPv4 includes a 16-bit `Header Checksum` recalculated at every router. IPv6 completely eliminates the header checksum to accelerate forwarding, delegating integrity checks to L2 and L4.
- **Extension Headers:** Optional L3 information in IPv6 is chained between the main header and payload using the `Next Header` pointer.

---

## 2. Layer 3 Attack Vectors

### ICMP Abuse & Reconnaissance
ICMP carries operational errors and diagnostic data (`ping`). Adversaries exploit specific message types:

| ICMP Type | Legitimate Purpose | Attack Vector / Exploitation |
| :--- | :--- | :--- |
| **Echo Request/Reply** | Host reachability verification | Reconnaissance scanning; ICMP Echo DoS flood |
| **Unreachable** | Route or port failure reporting | Port and network scanning analysis |
| **Mask Reply** | Internal subnet mask query | Network mapping and internal addressing disclosure |
| **Redirects** | Routing optimization notification | Traffic luring into rogue gateways (Man-in-the-Middle) |
| **Router Discovery** | Default gateway discovery | Injecting bogus gateway routes into host routing tables |

### Spoofing Attacks
- **Blind Spoofing:** Attacker cannot inspect return traffic; predominantly used in DoS/DDoS attacks where responses are misdirected to exhaust victim or third-party resources.
- **Non-Blind Spoofing:** Attacker sits on the transmission path (or subnet) and inspects return traffic; enables sequence-number prediction and session hijacking.
- **MAC Address Spoofing (CAM Table Poisoning):** An attacker on the local LAN transmits frames with a spoofed MAC matching a target server. The switch updates its Content Addressable Memory (CAM) table to associate the server's MAC with the attacker's switch port, rerouting internal traffic to the adversary.

### Amplification & Reflection (Smurf & NTP/DNS Amplification)
1. **Amplification:** The attacker transmits small request packets (e.g., ICMP Echo Requests, open DNS recursors, NTP `monlist`) with the source IP forged as the victim's IP.
2. **Reflection:** Multiple intermediate hosts/servers simultaneously transmit high-volume responses directed at the spoofed victim IP, overwhelming network throughput.

---

## 3. Transport Layer (Layer 4): TCP vs. UDP

```
TCP Segment (Min 20 Bytes):                     UDP Datagram (Fixed 8 Bytes):
+-------------------------------+                +-------------------------------+
| Source Port (16b)             |                | Source Port (16b)             |
+-------------------------------+                +-------------------------------+
| Destination Port (16b)        |                | Destination Port (16b)        |
+-------------------------------+                +-------------------------------+
| Sequence Number (32b)         |                | Length (16b)                  |
+-------------------------------+                +-------------------------------+
| Acknowledgment Number (32b)   |                | Checksum (16b, optional)      |
+-------------------------------+                +-------------------------------+
| HLen | Rsrvd | Control Bits   |                | Payload Data...               |
+-------------------------------+                +-------------------------------+
| Window (16b) | Checksum (16b) |
+-------------------------------+
| Urgent Pointer (16b)          |
+-------------------------------+
```

### Control Bits (Flags)
- **SYN (Synchronize):** Initiates connection state and establishes initial sequence numbers.
- **ACK (Acknowledgment):** Acknowledges receipt of transmitted bytes.
- **RST (Reset):** Abruptly tears down an active connection.
- **FIN (Finished):** Gracefully closes sending half of a full-duplex connection.
- **PSH (Push):** Forces receiver to deliver buffer directly to application layer without waiting for full segments.
- **URG (Urgent):** Indicates urgent pointer field contains urgent priority data.

---

## 4. Layer 4 Attack Vectors

### TCP SYN Flood
- **Vulnerability:** Exploits the stateful nature of the TCP three-way handshake (`SYN` $\rightarrow$ `SYN-ACK` $\rightarrow$ `ACK`).
- **Mechanism:** The adversary floods target listening ports with spoofed `SYN` requests. The server allocates buffer memory (Transmission Control Blocks) and replies with `SYN-ACK`, waiting in a `SYN-RECEIVED` half-open state. Because the client `ACK` never arrives, connection tables become exhausted, rejecting legitimate inbound requests.
- **Mitigations:** SYN cookies, reduced half-open timeout thresholds, and TCP stateful firewalls/load balancers.

### TCP Reset Attack
- **Mechanism:** Threat actor transmits a forged packet with the `RST` bit set to either or both communicating endpoints. Upon reception, endpoints immediately drop connection state and terminate data transfer without a graceful 4-way teardown.

### TCP Session Hijacking
- **Mechanism:** Attacker intercepts communications, spoofs host IP, predicts the sequence and acknowledgment numbering progression, and injects malicious payloads directly into the established session while forcing the victim out.

### UDP Flood Attacks
- **Mechanism:** Threat actors deploy automated flood utilities (e.g., Low Orbit Ion Cannon - LOIC, UDP Unicorn) that transmit high-frequency UDP packets to random high-numbered ports on a target server. For each closed port, the host operating system generates an ICMP Port Unreachable message. The sheer volume of processing and ICMP response generation saturates inbound and outbound bandwidth.
