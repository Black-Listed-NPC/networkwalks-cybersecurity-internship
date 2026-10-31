# Networkwalks Cybersecurity Internship

Documentation, investigation reports, packet analysis artifacts, and scorecards for the Cybersecurity Internship at Networkwalks.

---

## Internship Roadmap

| Track | Module / Focus Area | Documentation & Proofs | Status |
|:---:|:---|:---|:---:|
| **Week 1** | **SOC & Packet Forensics (Wireshark)** | [Week 1 Adv Project](Week1%20Adv%20Project/wireshark%20soc%20project_report.md) | **Completed (100%)** |
| **Week 2** | Threat Detection & Incident Response | *Upcoming* | In Progress |
| **Week 3** | Endpoint Security & Log Analysis | *Upcoming* | Scheduled |
| **Week 4** | SIEM Engineering & Capstone Incident Triage | *Upcoming* | Scheduled |

---

## Week 1: Wireshark SOC Project Module (Score: 600 / 600)

Complete investigation write-up: **[Wireshark SOC Project Report](Week1%20Adv%20Project/wireshark%20soc%20project_report.md)**

| Lab | Investigation Objective | Protocol & Filter Focus | Score | Proof of Completion |
|:---:|:---|:---|:---:|:---:|
| **Lab 3** | Identify the Attacker's IP | Reconnaissance triage, TCP SYN port scanning, host discovery | 100/100 | [View Result](Week1%20Adv%20Project/Lab%203%20Result%20-%20Yash%20Kumar%20Kumawat.png) |
| **Lab 4** | Follow TCP Stream (Part 1) | TCP conversation stream reassembly, sensitive data exposure | 100/100 | [View Result](Week1%20Adv%20Project/Lab%204%20Result%20-%20Yash%20Kumar%20Kumawat.png) |
| **Lab 5** | Follow TCP Stream (Part 2) | Hidden payload discovery, backup server transfer analysis | 100/100 | [View Result](Week1%20Adv%20Project/Lab%205%20Result%20-%20Yash%20Kumar%20Kumawat.png) |
| **Lab 6** | HTTP Plain Text Credentials | Cleartext HTTP POST analysis, authentication credential snooping | 100/100 | [View Result](Week1%20Adv%20Project/Lab%206%20Result%20-%20Yash%20Kumar%20Kumawat.png) |
| **Lab 7** | Catch the Brute Force Attacker | Authentication spray detection, HTTP 401/403/200 status pattern mapping | 100/100 | [View Result](Week1%20Adv%20Project/Lab%207%20Result%20-%20Yash%20Kumar%20Kumawat.png) |
| **Lab 8** | Catch the Call Scammer | SIP call flow analysis, RTP G.711 PCMU voice stream audio replay | 100/100 | [View Result](Week1%20Adv%20Project/Lab%208%20Result%20-%20Yash%20Kumar%20Kumawat.png) |

---

## Repository Structure

```text
networkwalks-cybersecurity-internship/
├── README.md
├── Week1 Adv Project/
│   ├── wireshark soc project_report.md
│   ├── Lab 3 Result - Yash Kumar Kumawat.png
│   ├── Lab 4 Result - Yash Kumar Kumawat.png
│   ├── Lab 5 Result - Yash Kumar Kumawat.png
│   ├── Lab 6 Result - Yash Kumar Kumawat.png
│   ├── Lab 7 Result - Yash Kumar Kumawat.png
│   └── Lab 8 Result - Yash Kumar Kumawat.png
└── week-1-project/
