# Wireless Communications Lab0
###### tags: `Wireless Communications`

## :notebook_with_decorative_cover: Lab 1: Analyzing UE–gNB Connectivity in an OAI 5G SA Network(Student)

## 1. Lab Overview

After completing this lab, you should be able to:

Identify the UE, gNB, AMF, UPF, and Data Network.
Explain the purpose of RRCSetupRequest, RRCSetup, and RRCSetupComplete.
Explain how a NAS message is transported from the UE to the AMF through the gNB.
Identify the main 5G Registration messages.
Verify that the UE receives an IP address and exchanges user-plane traffic.

## 2. Required Files

OAI-5G-Wireshark-Profile.zip
oai-5g-combined.pcapng

## 3. Install the OAI-5G Wireshark Profile

![Install profiles](images/profiles.png)

## 4. Open and Verify the Capture

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

## 5. Identify the Basic 5G SA Architecture

Applied these filters individually: ngap, gtp, icmp

Checkpoint 2: Basic Architecture
Correctly identify the five components and their IP addresses.
Correctly explain N1, N2, and N3.

### Basic 5G SA Architecture

| Component | IP address | Evidence from the capture |
|---|---|---|
| UE PDU address | `10.0.0.2` | Source of the inside ICMP Echo Request in the Packet 490 |
| gNB | `192.168.70.129` | NGAP endpoint towards the AMF and outer GTP-U tunnel endpoint   |
| AMF | `192.168.70.132` | NGAP endgpoint communicates with the gNB|
| UPF | `192.168.70.134` | GTP-U tunnel engdpoint and forwards user plane traffics toward the data network|
| Data Network | `192.168.70.135` | Destinatiton of the ICMP Echo Requests from the UE|


### 5G Interfaces


| Interface | Connected components | Main protocol | Purpose |
|---|---|---|---|
| N1 | UE and AMF |NAS-5GS| Carries NAS signal logically between the UE and AMF. The signal goes through the gNB|
| N2 | gNB and AMF |NGAP| Carries controls plane signaling between the gNB and AMF. |
| N3 | gNB and UPF |GTP-U| Cariess user plane traffic between the gNB and UPF. |


![NGAP](images/ngap.png)

![GTP](images/gtp.png)

![ICMP](images/icmp.png)

## 6. Analyze the RRC Connection Establishment

## RRC Connection Establishment

| Message | Direction | Logical channel / SRB | Main purpose | Packet number |
|---|---|---|---|---|
| RRCSetupRequest | UE to gNB | UL-CCCH / SRB0 | The UE request establishsment of RRC conection with gNB. | `104` |
| RRCSetup | gNB to UE | DL-CCCH / SRB0 | The gNB takes the requesta and give RRC configuration that is needed to establissh the connection, with SRB1 config. | `105` |
| RRCSetupComplete | UE to gNB | UL-DCCH / SRB1 | The UE confirmed that RRC setup is compelete and takes the NAS registration request towards the 5G core. | `108` |

![NR-RRC](images/nrrrc2.png)

- What is the establishment cause in RRCSetupRequest? in packet 104: the establishment cause is `mo-Signalling`. This means the UE makes the RRC connection to send signaling traffic toward the network.
- What SRB does RRCSetupRequest use? Why? It uses `SRB0`. SRB0 is used for initial RRC signal because SRB1 is not initailized.
- Which side sends RRCSetup? `UE`. It is used to establish the RRC connection.
- Which signaling radio bearer is used after the RRC connection is established? to `SRB1`.
- Which NAS message is carried inside RRCSetupComplete? `Registration Request`.
- At the end of this procedure, is the UE only connected to the gNB, or is it already registered with the 5G Core? Explain. UE has establsished an RRC connection with gNB, but it is not with the 5G Core. The RRCSetupComplete message takes the NAS registration request. The gNB will take it to AMF and UE will complete tasks: security setup, registration accept and complete, authentication so the 5G registration is finished.

![Establishment cause](images/estabblishment.png)

## 7. Connect RRC Signaling to NGAP and NAS

The UE exchanges NAS signaling with the AMF through the gNB. On the radio side, the NAS message is carried by RRC. The gNB then forwards it to the AMF through NGAP.

![Registration request](images/registration_request.png)

- RRCSetupComplete

`108` contains an `RRCSetupComplete` message sent from the UE to the gNB and inside `RRCSetupComplete` messsage, the `dedicatedNAS-Message` to `Registration Request`.
This show that the UE sends its `NAS Registration Request` to the gNB using RRC signaling.

![NAS-PDU registration request](images/request2.png)


### RRC-to-NGAP/NAS Mapping

| Stage | Protocol message | Sender to receiver | Encapsulated information |
|---|---|---|---|
| Radio side | `RRCSetupComplete` | UE to gNB | NAS `Registration Request` in `dedicatedNAS-Message` |
| Core side | `NGAP InitialUEMessage` | gNB to AMF | NAS `Registration Request` in `NAS-PDU` |

Answer:

- What is the role of the gNB when it transports NAS messages? gNB is between UE and AMF so it recievs the NAS signal from the UE thnaks to RRC message and takes it to AMF. 
- What is the difference between RRC and NAS signaling? RRC singal is in the UE and gNB path to make the connection.
- Is the Registration Request delivered directly from the UE to the AMF? Explain the protocol path. no,, it is not. The UE send the `Registration Request` inside
`RRCSetupComplete` to the gNB and the gNB then sends it to AF. `UE → RRC → gNB → NGAP → AMF`
- Which message confirms that Registration has completed successfully? `Registration Complete` message

## 8. Verify the UE IP Address and User-Plane Traffic

Find the PDU Session Establishment Accept and record the UE address:

- With filter gtp || icmp

| Field | Observed value |
|---|---|
| UE IPv4 address | `10.0.0.2` |

![PDU](images/PDU.png)

- echo Request: packet `490`
- echo Reply: packet `495`
- UE IP address: `10.0.0.2`
- data network IP address: `192.168.70.135`

Packet `490` is between gNB (`192.168.70.129`) and UPF (`192.168.70.134`)

- echo request: 

![Echo request](images/gtp2.png)


- echo reply: 

![Echo reply](images/echo.png)


- What IPv4 address was assigned to the UE?`10.0.0.2`
- How many ICMP Echo Request/Reply pairs are present? `[COUNT]` there are seq from 1 to 10 request and replies
- What does the successful Echo Reply prove about the UE connection? ot proves that the UE has working user-plane connect. The UE can send IP traffic through the gNB and UPF to the Data Network and receive data .

## 9. Final UE Connection Sequence

Create one sequence diagram containing:

UE
gNB
AMF
UPF
Data Network
Include at least:

RRCSetupRequest
RRCSetup
RRCSetupComplete with Registration Request
NGAP InitialUEMessage
Authentication
Security Mode
Registration Accept and Complete
PDU Session establishment
GTP-U ping

![Sequence diagram](images/wireless.png)

Checkpoint 6: Final Sequence Diagram — 5 points
Include the required components and signaling stages. — 3 points
Clearly distinguish control-plane and user-plane traffic. — 2 points





