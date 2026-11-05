# Cisco Endpoint Security — Module 2: Securing Networks

## 1. Executive Summary & Core Definitions

In modern network defense, security directly governs **business continuity**. A compromise in network infrastructure threatens e-commerce, compromises data integrity, violates privacy regulations, and inflicts severe financial and reputational losses.

```
       +-----------------------------------------------------------+
       |                        Risk Management                    |
       |  Balances operational cost against value of assets saved |
       +-----------------------------------------------------------+
                     /             |             \           \
                    v              v              v           v
             [Acceptance]     [Avoidance]    [Reduction]  [Transfer]
             Tolerate low     Eliminate risk  Mitigate &   Insurance &
             impact risks     by discontinuing patch gaps  SLA / Third-party
```

---

## 2. Threat Actor Taxonomy & Hackers

| Classification | Primary Motivation | Methodology & Ethics |
| :--- | :--- | :--- |
| **White Hat** | Defense, research, compliance | Ethical hackers operating under formal permission and legal contracts; conduct penetration tests to identify weaknesses before adversaries do. |
| **Gray Hat** | Notoriety, personal ethics, curiosity | Commit borderline or technically illegal actions without malicious intent or personal financial gain (e.g., unauthorized scanning followed by public disclosure). |
| **Black Hat** | Financial gain, destruction, espionage | Malicious threat actors violating network security for personal enrichment or targeted destruction. |
| **Cybercriminals** | Financial gain via extortion & theft | Self-employed or organized crime syndicates deploying ransomware, data theft, and extortion networks causing billions in global damages. |
| **State-Sponsored** | Geopolitical advantage, sabotage | Highly funded, persistent advanced threat groups (APTs) targeting critical infrastructure, defense contractors, and foreign governments. |
| **Hacktivists** | Ideological, political, or social causes | Deface websites, conduct DDoS campaigns, or leak confidential data to protest against corporations or political entities. |

---

## 3. Threat Intelligence: IOC vs. IOA

Understanding attacker footprints requires distinguishing between **retrospective evidence** and **proactive intent**.

```
+------------------------------------+------------------------------------+
|  Indicator of Compromise (IOC)     |    Indicator of Attack (IOA)       |
+------------------------------------+------------------------------------+
| • Forensic, reactive evidence      | • Proactive, behavioral intent     |
| • Focuses on WHAT happened         | • Focuses on WHY & HOW (TTPs)      |
| • Specific file hashes (SHA256)    | • Exploitation attempts & code injection |
| • Known malicious C2 IP addresses  | • Lateral movement & privilege escalation|
| • Suspicious registry entries      | • Motivation and execution vectors |
+------------------------------------+------------------------------------+
```

- **Automated Indicator Sharing (AIS):** A DHS/CISA capability that enables real-time, bidirectional exchange of machine-readable cyber threat indicators (STIX/TAXII format) across private and public sectors.

---

## 4. Attack Vectors & Data Loss Prevention (DLP)

An **attack vector** is the specific pathway or mechanism through which a threat actor gains unauthorized access to a network, host, or server.

### Why Internal Threats Pose Higher Risk
Internal threats often inflict significantly greater damage than external adversaries because:
1. **Network Topology & Resource Knowledge:** Internal users know where crown-jewel assets, database servers, and sensitive credentials reside.
2. **Direct Physical & Infrastructure Access:** Internal staff possess physical access to facilities, wiring closets, switches, and internal endpoints, bypassing perimeter firewalls.

### Primary Data Loss Vectors
- **Cloud Storage Devices:** Weak configurations, open cloud storage buckets, or unauthorized synchronizations.
- **Removable Media:** Unrestricted USB transfer or lost unencrypted external drives.
- **Unencrypted Laptops & Endpoints:** Physical theft yielding plaintext corporate data.
- **Improper Access Control:** Weak, shared, or compromised credentials granting unauthorized lateral access.
- **Email & Messaging Platforms:** Accidental dissemination or interception of unencrypted confidential communications.

---

## 5. Risk Management Strategies

Risk is the probability that a threat will exploit a vulnerability resulting in asset loss:

$$\text{Risk} = \text{Threat} \times \text{Vulnerability} \times \text{Asset Value}$$

1. **Risk Reduction (Mitigation):** Implementing controls (e.g., multifactor authentication, network segmentation, firewalls, and patching).
2. **Risk Transfer:** Offloading financial and operational exposure to a third party (e.g., cyber insurance policies, managed cloud providers with stringent SLAs).
3. **Risk Avoidance:** Completely terminating the activity or service creating the exposure (e.g., disabling legacy protocols or shutting down unmaintained internet-facing services).
4. **Risk Acceptance:** Consciously accepting residual risk when the cost of mitigation outweighs potential loss.
