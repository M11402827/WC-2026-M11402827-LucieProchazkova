# Wireless Communications Lab0
###### tags: `Wireless Communications`

## :notebook_with_decorative_cover: Lab 1: Analyzing UE–gNB Connectivity in an OAI 5G SA Network(Student)

## 1. Install the OAI-5G Wireshark Profile

## 2. Open and Verify the Capture



- The Protocol column shows NR RRC. And the Info column contains messages:
- RRC Setup Request
- RRC Setup
- RRC Setup Complete
- Security Mode Command
- RRC Reconfiguration

These are the 5G NR Radio Resource Control messages.

Checkpoint 1: Wireshark Setup

![Select profiles](images/profiles.png)

![Opened capture](images/open.png)

![NR RRC messages](images/nrrrc.png)

## 3. Identify the Basic 5G SA Architecture

Applied these filters individually: ngap, gtp, icmp

Checkpoint 2: Basic Architecture
Correctly identify the five components and their IP addresses.
Correctly explain N1, N2, and N3.

## Basic 5G SA Architecture

| Component | IP address | Evidence from the capture |
|---|---|---|
| UE PDU address | `10.0.0.2` | Source of the inner ICMP Echo Request in packet 490 |
| gNB | `192.168.70.129` | NGAP endpoint toward the AMF and outer GTP-U tunnel endpoint |
| AMF | `192.168.70.132` | NGAP endpoint communicating with the gNB |
| UPF | `192.168.70.134` | GTP-U tunnel endpoint and forwards user-plane traffic toward the Data Network |
| Data Network | `192.168.70.135` | Destination of the ICMP Echo Request from the UE |


### 5G Interfaces

## 5G Interfaces

| Interface | Connected components | Main protocol | Purpose |
|---|---|---|---|
| N1 | UE ↔ AMF | NAS-5GS | Carries NAS signaling logically between the UE and AMF. The signaling passes through the gNB. |
| N2 | gNB ↔ AMF | NGAP | Carries control-plane signaling between the gNB and AMF. |
| N3 | gNB ↔ UPF | GTP-U | Carries user-plane traffic between the gNB and UPF. |


![NGAP](images/ngap.png)

![GTP](images/gtp.png)

![ICMP](images/icmp.png)




