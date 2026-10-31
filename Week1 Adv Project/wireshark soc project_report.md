# Networkwalks SOC Project Module — Wireshark Traffic Analysis (Labs 3 - 8)

**Analyst:** Yash Kumar Kumawat  
**Project Track:** Week 1 SOC Analyst Project Module (Wireshark Extra)  
**Date of Completion:** 31 October 2026  
**Final Score:** 600 / 600 (100% Across All 6 Labs)  
**Status:** Completed & Verified  

---

## 1. Executive Summary

This project encompasses a series of six simulated Security Operations Center (SOC) investigative labs hosted on Networkwalks. As a tier-1/tier-2 SOC analyst, network packet captures (`.pcap`) representing varied real-world attack vectors and operational protocols were analyzed using Wireshark. 

The primary objectives were:
1. Detecting adversarial reconnaissance sweeps and distinguishing open vs. closed ports.
2. Rebuilding disrupted session streams across fragmented TCP conversations.
3. Exposing sensitive data transmission and exfiltration hidden in arbitrary streams.
4. Extracting cleartext credential submissions across unencrypted web interfaces.
5. Detecting automated authentication brute-force attacks via HTTP response status code patterns.
6. Replaying and inspecting voice-over-IP (VoIP) traffic to uncover fraud, call spoofing, and covert data transmission over RTP streams.

All 60 discrete analytical tasks across Labs 3 through 8 were solved with 100% accuracy.

---

## 2. Lab Investigations & Technical Breakdown

```
+-------------------------------------------------------------------------+
|                    LAB SCORE SUMMARY (100% OVERALL)                     |
+-------+-------------------------------+---------+--------+--------------+
| Lab # | Lab Title                     | Score   | Tasks  | Status       |
+-------+-------------------------------+---------+--------+--------------+
| Lab 3 | Identify the Attacker's IP    | 100/100 | 10/10  | Outstanding! |
| Lab 4 | Follow TCP Stream, Part 1     | 100/100 | 10/10  | Outstanding! |
| Lab 5 | Follow TCP Stream, Part 2     | 100/100 | 10/10  | Outstanding! |
| Lab 6 | HTTP Plain Text Credentials   | 100/100 | 10/10  | Outstanding! |
| Lab 7 | Catch the Brute Force Attacker| 100/100 | 10/10  | Outstanding! |
| Lab 8 | VoIP Call Replay (Scam Call)  | 100/100 | 10/10  | Outstanding! |
+-------+-------------------------------+---------+--------+--------------+
```

---

### Lab 3: Identify the Attacker's IP (Port Scanning & Reconnaissance)

* **Objective:** Dissect an inbound TCP port scan, determine the malicious IP address, map targeted ports, and differentiate target responses to establish which services were exposed.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **Traffic Capture:** Analyzed raw traffic containing thousands of rapid sequential connection attempts.
2. **SYN Flag Isolation:** Applied flag filters to isolate TCP handshake initiation packets.
3. **Attacker vs. Target Identification:** Pinpointed the single remote IP emitting high-frequency SYN packets against consecutive destination ports of the protected internal server.
4. **Port Probe Mapping:** Identified the specific source port multiplexing and total destination ports surveyed.
5. **Port State Classification:** 
   * **Open Ports:** Answered with `[SYN, ACK]` flags.
   * **Closed Ports:** Answered with `[RST, ACK]` or silent drops (filtered).
6. **Attack Signature:** Classified the activity as a TCP SYN Half-Open Stealth Scan.

#### Core Wireshark Filters Used:
```wireshark
# Identify TCP handshake initiation packets (SYN only, no ACK)
tcp.flags.syn == 1 && tcp.flags.ack == 0

# Track responses indicating open ports
tcp.flags.syn == 1 && tcp.flags.ack == 1

# Detect reset packets from the victim indicating closed ports
tcp.flags.reset == 1 && tcp.flags.ack == 1
```

---

### Lab 4: Follow TCP Stream, Part 1 (Session Reassembly & Internal Comms)

* **Objective:** Reconstruct fragmented TCP communication streams to intercept an internal chat conversation, extract sensitive operational disclosures, and retrieve temporary credentials.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **Chat Port Enumeration:** Located a non-standard cleartext messaging service operating across an unencrypted internal socket.
2. **Conversation Initiator:** Traced packet timestamps and initial sequence synchronization to identify who initiated the session.
3. **Stream Reconstruction:** Leveraged Wireshark's TCP Stream Reassembly engine (`tcp.stream eq X`) to read the full conversation without manual segment ordering.
4. **Data Extraction:**
   * Recovered total chat buffer volume and raw character byte size.
   * Identified internal employee assistance requests regarding degraded network shares and failing storage drives.
   * Uncovered plain text temporary access passwords and guest Wi-Fi pre-shared keys transmitted in the clear.
5. **Stream Formatting:** Inspected color-coded directional flows (client outbound vs. server inbound).

#### Core Wireshark Filters Used:
```wireshark
# Locate active TCP streams on arbitrary ports
tcp.port == [chat_port]

# Follow specific conversation stream index
tcp.stream eq [stream_id]

# Inspect string parameters inside reassembled payloads
frame contains "password" || frame contains "drive"
```

---

### Lab 5: Follow TCP Stream, Part 2 (Hidden Data & Exfiltration Extraction)

* **Objective:** Trace outbound web requests across alternate TCP sessions, locate obfuscated parameters, determine download sources, and identify leaked analytical reports.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **HTTP/TCP Conversation Tracking:** Followed session streams across multi-stage outbound browsing requests.
2. **Hidden Parameter Hunting:** Dissected URL query strings, custom HTTP headers, and hidden form fields to uncover encoded tokens.
3. **Data Exfiltration & File Transfer:**
   * Located communication directing to an external secondary backup server.
   * Identified directional flow indicating an outbound file pull/push.
   * Captured HTTP response status codes (`200 OK`) and traced the specific document transferred across the wire.
4. **Forensic Attribution:** Extracted document metadata to identify the exact author/operator behind the leaked analytical report.

#### Core Wireshark Filters Used:
```wireshark
# Filter for HTTP GET/POST queries
http.request

# Follow specific secondary stream
tcp.stream eq [stream_index]

# Inspect transferred files and mime-types
http.response.code == 200 && http.content_type contains "application"
```

---

### Lab 6: HTTP Plain Text Credentials (Unencrypted Authentication Triage)

* **Objective:** Detect insecure web logins, reconstruct submitted forms, identify target login endpoints, and recover cleartext usernames and passwords.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **Protocol Vulnerability:** Confirmed the presence of unencrypted HTTP (Port 80) carrying critical authentication exchanges.
2. **Form Submission Method:** Isolated `POST` requests targeted at administrative portals (`/login`, `/auth`, etc.).
3. **Endpoint & Host Attribution:** Mapped the victim client IP address against the target web application host header.
4. **Credential Extraction:**
   * Examined `application/x-www-form-urlencoded` and JSON payload sections in the packet details pane.
   * Uncovered the cleartext username and password submitted by the user.
5. **Remediation Identification:** Formulated technical requirements for implementing TLS 1.3 encryption (HTTPS) and HSTS enforcement.

#### Core Wireshark Filters Used:
```wireshark
# Isolate HTTP form submissions
http.request.method == "POST"

# Filter for credential parameters in URL or body
http contains "password" || http contains "uname" || http contains "user"

# Inspect packet payload structure
urlencoded-form
```

---

### Lab 7: Catch the Brute Force Attacker (Authentication Attack Analysis)

* **Objective:** Identify an automated credential guessing attack, isolate the threat actor's IP, determine authentication failure rates, and extract the single successful password.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **Anomaly Detection:** Observed a rapid surge of HTTP `POST` requests originating from a single IP directed at a single authentication endpoint.
2. **Target & Protocol Mapping:** Identified the specific target server IP, host, and application service.
3. **Brute Force Mechanics:**
   * Noticed repeated login attempts utilizing a static username with varying password payloads (Dictionary Attack / Password Spraying).
   * Categorized failed login response patterns via HTTP response status codes (`401 Unauthorized` / custom `200 OK` error responses).
4. **Breach Determination:**
   * Filtered for unique response sizes and status code changes indicating a successful login (`302 Found` redirect or `200 OK` dashboard load).
   * Extracted the precise password string that granted the adversary access.

#### Core Wireshark Filters Used:
```wireshark
# Filter rapid POST requests from suspect host
ip.src == [attacker_ip] && http.request.method == "POST"

# Correlate response failure patterns
http.response.code == 401 || http.response.code == 403

# Locate the single successful authentication response
http.response.code == 302 || (http.response.code == 200 && http.content_length > [normal_error_length])
```

---

### Lab 8: Catch the Call Scammer (VoIP Traffic & Audio Stream Replay)

* **Objective:** Investigate intercepted Voice-over-IP (VoIP) traffic, inspect call signaling and voice transmission protocols, extract caller metadata, decode voice codecs, and replay the conversation to capture a phone fraud incident.
* **Score:** 10/10 tasks (100 / 100 marks)

#### Key Observations & Tasks:
1. **Protocol Separation:**
   * **Call Setup (Signaling):** Filtered for Session Initiation Protocol (`SIP`) packets negotiating connection parameters and session state.
   * **Voice Stream:** Filtered for Real-time Transport Protocol (`RTP`) carrying digitized audio data over UDP.
2. **Signaling & Metadata Analysis:**
   * Examined SIP `INVITE` and `200 OK` packets to retrieve caller name, extension number, destination URI, and caller IP address.
   * Identified the negotiated audio codec (e.g., G.711 PCMU/PCMA).
3. **Audio Reassembly & Playback:**
   * Utilized Wireshark's built-in VoIP Analysis tool (`Telephony -> VoIP Calls -> Play Streams`).
   * Reconstructed the forward and reverse RTP channels to play back clear voice audio.
4. **Fraud & Data Extraction:**
   * Listened to the active scam conversation wherein sensitive payment information was requested.
   * Extracted the victim's compromised credit card number, expiration date, and CVV code spoken during the call.
5. **Security Controls:** Identified necessary preventative controls including SIP-TLS for signaling encryption and Secure RTP (SRTP) for media stream protection.

#### Core Wireshark Filters Used:
```wireshark
# Filter for VoIP call setup and teardown
sip

# Isolate media voice packets
rtp

# Trace specific SIP methods (INVITE, BYE)
sip.Method == "INVITE"
```

---

## 3. Incident Timeline & Attack Flow Summary

```
   [Phase 1: Recon]                     [Phase 2: Weaponization & Access]              [Phase 3: Exploitation & Fraud]
+----------------------+              +------------------------------------+          +-------------------------------+
|  Lab 3: Port Scan    |  --------->  | Lab 6: HTTP Cleartext Interception | -------> | Lab 8: Insecure VoIP Wiretap  |
| (Attacker maps open  |              | Lab 7: Web Auth Brute Force        |          | (RTP replayed, credit card &  |
|  ports via SYN scan) |              | (Attacker cracks valid password)   |          |  PII exfiltrated over audio)  |
+----------------------+              +------------------------------------+          +-------------------------------+
                                                        |
                                                        v
                                      +------------------------------------+
                                      | Lab 4 & 5: Stream Forensics        |
                                      | (Internal chat & files intercepted |
                                      |  via unencrypted TCP streams)      |
                                      +------------------------------------+
```

---

## 4. Analytical Techniques & Recommended SOC Mitigations

| Threat Vector / Finding | Wireshark Analytical Indicator | Recommended Enterprise Defense |
|---|---|---|
| **Port Scanning (Lab 3)** | High-frequency `tcp.flags.syn==1` across port ranges with rapid `RST` returns | Deploy stateful firewalls, implement IDS/IPS rate-limiting rules, and disable unnecessary open ports. |
| **Cleartext Chat & Files (Labs 4 & 5)** | Reconstructed plaintext strings inside arbitrary TCP sockets | Mandate end-to-end encrypted messaging; enforce internal network segmentation. |
| **Insecure HTTP Logins (Lab 6)** | Plaintext credentials visible in HTTP `POST` body (`urlencoded-form`) | Enforce HTTPS (TLS 1.3), configure HSTS, and block unencrypted HTTP on web application firewalls. |
| **Web Brute Force (Lab 7)** | Rapid sequence of failed HTTP response codes followed by a status anomaly | Implement Account Lockout policies, multi-factor authentication (MFA), and CAPTCHA rate-limiting. |
| **VoIP Eavesdropping (Lab 8)** | Cleartext SIP signaling and unencrypted RTP media payloads playable via Wireshark | Enforce SIPS (SIP over TLS) on port 5061 and encrypt media streams using SRTP. |

---

## 5. Conclusion

Completing this project module validated end-to-end network traffic investigation workflows. From identifying preliminary reconnaissance scans to decrypting application-layer exchanges, tracking brute-force authentication attempts, and replaying VoIP audio streams, each scenario highlighted how unencrypted or poorly segmented infrastructure leaves critical organizational data exposed.

All lab evidence, screenshots, and task scorecards confirming 100/100 performance across all six labs are archived in the project proofs directory.