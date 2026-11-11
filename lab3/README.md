# Wireless Communications Lab2
###### tags: `Wireless Communications`

## :notebook_with_decorative_cover: Lab 2: gNB TDD Traffic Analysis

## 1. Lab Overview

In the previous labs, you analyzed 5G signaling messages and network performance using provided captures and measurements.

In this lab, you will deploy your own 5G Standalone (SA) network using OpenAirInterface (OAI), verify the complete connection, investigate RFsimulator communication, perform active network measurements, and troubleshoot a controlled connection failure.

Unlike the previous labs, you will collect measurements directly from your own running 5G network.

After completing this lab, you should be able to:

1. Deploy an OAI gNB, nrUE, and 5G Core Network.
2. Identify the main stages of 5G SA connection establishment.
3. Verify communication between gNB and nrUE through RFsimulator.
4. Measure user-plane connectivity under different packet sizes.
5. Diagnose connection failures using OAI and 5GC logs.
6. Explain the limitations of a software-simulated 5G network.

## 2. Experimental Environment

Use the OAI v2.4.0 Build Guide to prepare the environment before class. The experiment uses one Ubuntu machine without physical RF hardware.

## 3. Task 1 — Deploy and Verify the Complete 5G SA Network

![Scenario A](images/gNB.png)<br>

*Figure: gNB successfully connected*

![Scenario A](images/ue.png)<br>

*Figure: UE successfully connected*

## Task 1 — Deploy and Verify the Complete 5G SA Network

| Stage | Successful? | Evidence from logs |
|---|---|---|
| CN5G initialization | yes | show `healthy`. |
| gNB NG Setup | yes | `Received NGSetupResponse from AMF` |
| UE synchronization | yes |  `UE synchronized!` |
| RRC connection establishment | yes | `State = NR_RRC_CONNECTED` |
| UE Registration | yes | The UE received `RegistrationComplete` |
| PDU Session Establishment | yes | `PDU Session Establishment Accept, UE IPv4: 10.0.0.2`. |
| UE tunnel creation | yes |  `Interface oaitun_ue1 successfully configured, IPv4 10.0.0.2`. |

### Questions

1. Does a successful NG Setup guarantee that a UE can register? Explain.

   No, it does not. Because the UE still has to complete synchronization, RRC connection establishment, NAS registration, security, autentification and the gNB could be connected to the AMF also when a UE registration attempt failed.

2. Why can the UE establish an RRC connection but still fail Registration?

   RRC connection establishment is being done between the UE and gNB at the radio access network level. The registration has NAS with the 5G Core. The UE is synchronize and `NR_RRC_CONNECTED`,but it does not have authentication or registration with the AMF. This happened to me when the AMF could not obtain the authentication response from AUSF in time. The AUSF sent response 1 ms later than the AMF ressponse timer was.

3. What additional procedure is required after Registration before user data can be exchanged?

   A PDU Session has to be done after successful registration. This procedure creates the user plane connectivity and cpnnects the UE an IP address.


## 4. Task 2 — Investigate RFsimulator Communication

