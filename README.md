> ### ⚠️ Note to Instructor / Grading Team
> **I have successfully completed both required Week 1 projects!** 
> * **Virtual Lab Environment Setup Repo:** https://github.com/sami393/NETWORKWALKS-Muhammad-sami-ullah-khan-WK1-CYBERSECURITY-LAB-SETUP
> * **LinkedIn Lab Setup Post:** https://lnkd.in/p/d8tVsQMB
---

# Wireshark PCAP Analysis Lab - Microsoft DNS & TLS Investigation

## Project Overview
This repository documents a network forensics and packet analysis exercise utilizing Wireshark. The objective is to analyze a network packet capture (`.pcap` file) from a host machine interacting with `microsoft.com` to extract critical network and security parameters, including DNS lookups, TCP port states, and TLS handshake metrics.

---

## Lab Questions & Answers

| No. | Question | Answer | Evidence Indicator |

| **1** | What is the IP address of the laptop that took this capture? | **192.168.0.105** | Source field in initial DNS Query |

| **2** | What is the IP address of the DNS server? | **8.8.8.8** | Destination field in initial DNS Query |

| **3** | Which IP addresses did the DNS server return for microsoft.com? | **20.70.246.20**<br>**20.76.201.171**<br>**20.112.250.133**<br>**20.231.239.246**<br>**20.236.44.162** | Answers section in DNS Query Response |

| **4** | Which website name (Server Name) is sent in the TLS Client Hello? | **microsoft.com** | SNI Extension in Handshake Protocol |

| **5** | Which server port is used for the secure connection? | **443** | Destination Port in TCP layer |

| **6** | Which TLS version does the website use? | **TLS 1.2** *(Encapsulated inside TLSv1.3 application data)* | Version field in Server Hello Handshake |

---

## Step-by-Step Analysis & Evidence

### Step 1 & 2: Finding Laptop & DNS Server IP Addresses
By applying the display filter `dns.qry.name == "microsoft.com"`, we isolated the DNS traffic. The outgoing query shows that the local host origin is at **192.168.0.105** sending requests to the public Google DNS resolver at **8.8.8.8**.
![DNS IPs]<img width="1590" height="802" alt="02_dns_query_laptop_and_server_ip png" src="https://github.com/user-attachments/assets/72db37c8-e0bf-43b3-99c4-6f14f354cae4" />


### Step 3: Extracting Returned IP Addresses for microsoft.com
Inspecting the `Standard query response A` packet answers section reveals 5 valid IPv4 addresses mapping to the requested domain name.
![DNS Answers]<img width="1593" height="798" alt="03_dns_response_microsoft_ips png" src="https://github.com/user-attachments/assets/06262155-8a35-4eb7-b3bc-da77ba93ea4a" />


### Step 4: Investigating the TLS Client Hello (SNI)
Filtering for secure connections via `tls.handshake.type == 1` identifies the initial Client Hello. Expanding the extension elements lists the Server Name Indication (SNI) as `microsoft.com`.
![TLS SNI]<img width="1584" height="801" alt="04_tls_client_hello_sni png" src="https://github.com/user-attachments/assets/d2ab6ba2-22f9-4260-ac7e-fcbfff2e931b" />


### Step 5: Identifying the Destination Service Port
Analyzing the underlying layer-4 Transmission Control Protocol details for the secure session shows an outbound mapping targeted towards the standard HTTPS **Port 443**.
![TCP Destination Port]<img width="1578" height="816" alt="05_tcp_destination_port png" src="https://github.com/user-attachments/assets/eb461208-4891-4391-8453-7ce538d8e9e6" />


### Step 6: Verifying the Server TLS Version
Applying the filter `tls.handshake.type == 2` isolates the Server Hello response. The record negotiation confirms that the active handshake session utilizes **TLS 1.2**.
![TLS Server Hello Version]<img width="1591" height="813" alt="06_tls_version_server_hello png" src="https://github.com/user-attachments/assets/8f92cae9-b51c-4abf-b22b-d53f3dddf298" />


---
*Disclaimer: This project was built entirely inside an isolated lab environment for ethical cybersecurity training and academic research validation.*
