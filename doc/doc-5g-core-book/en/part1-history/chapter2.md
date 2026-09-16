# Chapter 2: 5G Core Network Overview from 3GPP Perspective

In Chapter 1, we started by discussing “why a core network is needed” and “the evolution and limitations of the 4G EPC.”
From this chapter onward, we will adopt a **3GPP-centric perspective** to examine the design of the entire 5G System (5GS), covering:
how standards are defined, the overall system architecture, and the meaning of commonly referenced 5G concepts such as SBA and interfaces (N1/N2/N3…).

> [!Important]
> The goal of this chapter is not to make you memorize all the specification numbers, but to help you first build a **sense of the overall structure**:
>
> “what the overall 5G system looks like” and “how the control and user planes are separated.” Detailed discussions of key concepts and the concrete roles of each Network Function (NF) will be covered later.

## 2.1 Overview of 3GPP and 5G System Standardization

To understand the 5G core network, the first step is to clarify two questions: **Who defines the specifications, and what do those specifications look like?**

### 2.1.1 What is 3GPP?

3GPP (3rd Generation Partnership Project) is an international standardization organization responsible for defining technical standards for 3G, 4G, and 5G systems.
It is not a single company, but a “collaborative partnership platform” whose members include network equipment vendors, mobile operators, chipset manufacturers, research institutions and so on.

Within 3GPP, the work is broadly divided into three major domains:

- **SA (Service & System Aspects)**

    Responsible for the design of the “overall architecture” and “service requirements.”
    This includes topics such as the overall architecture of the 5G System (5GS), how network functions are decomposed, what interfaces exist between them, and what types of services are supported.

- **RAN (Radio Access Network)**

    Focuses on “radio access technologies,” specifically the NR (New Radio) protocol stack, signaling design, and air-interface specifications.
    The commonly referenced 38.xxx series (e.g., TS 38.300 and TS 38.331) are the specifications that define the RAN.

- **CT (Core Network & Terminals)**

    Responsible for “core network and terminal signaling protocols.”
    This includes how the UE interacts with the core network, the definition of NAS / NGAP signaling, and the detailed procedures for call and session handling.

> [!Tip]
> A simple way to remember this:
>
> - SA: defines what 5G looks like (architecture & services)
> - RAN: defines how radio transmission works
> - CT: defines how signaling flows and how procedures are executed

### 2.1.2 The Roles of 23.501 / 23.502

Among the specifications related to the 5G core network, the following two Technical Specifications (TS) are referenced most frequently:

- **TS 23.501 - System Architecture**

    This document defines “the overall architecture of the 5G system,” including:

    - The network functions (NFs), such as AMF, SMF, UPF, and others
    - The interfaces between them (N1, N2, N3, N4, N6, Nn, etc.)
    - How different service scenarios (eMBB / URLLC / mMTC) are supported architecturally

    23.501 can be thought of as “the architecture blueprints of 5G.”

- **TS 23.502 - Procedures**

    It describes “how the system operates when events occur”, such as:

    - UE registration procedures
    - Establishment and release of PDU Sessions
    - High-level handover procedures

> [!Tip]
> A simple way to remember this:
>
> - 23.501 is “the architectural blueprint of 5G SA”
> - 23.502 is “the operational handbook describing how the system behaves in different scenarios”

### 2.1.3 The 38.x Series and RAN Signaling

The specifications discussed above are primarily core-network-oriented. On the radio access (RAN) side of 5G, you will frequently encounter documents starting with **38.xxx**, for example:

- **TS 38.300**: Describes the overall architecture of NR and NG-RAN
- **TS 38.331**: Defines the RRC (Radio Resource Control) protocol, i.e., the control signaling between the UE and the gNB
- Additional 38.3xx / 38.4xx specifications cover details of the physical layer, MAC, RLC, PDCP, and related protocols

These documents primarily address “UE ↔ gNB (base station)” interactions. Since this book focuses on the **5G Core (5GC)**, RAN signaling will only be briefly referenced when necessary, mainly to help establish end-to-end context.

### 2.1.4 5GC + NR = 5GS

According to 3GPP definitions, the “5G System (5GS)” consists of two main parts:

- **5GC (5G Core)**: the primary focus of this book
- **NR (New Radio) / NG-RAN**: the new radio access network (including gNB)

Together, these form a complete 5G system.

Therefore, the 5G System (5GS) can be understood as **“the end-to-end network formed by the 5G Core (5GC) and NR.”**

## 2.2 5G System Overview (5GS Overview)

Before diving into details, let’s first look at the overall structure of the 5G system from a high-level perspective.

### 2.2.1 UE - RAN - 5GC - DN: The Path from Device to Service

From a user’s point of view, when a smartphone(UE) accesses the network, the data path typically goes through the following layers:

![5gc](../../image/part1/5gc.png)

- **UE (User Equipment)**: smartphones, CPEs, IoT devices, etc.
- **RAN (Radio Access Network, i.e., gNB / NG-RAN)**: converts radio signals into packets and connects them to the core network
- **5GC (5G Core)**: handles registration, identity management, routing, policy control, and charging
- **DN (Data Network)**: such as the Internet, enterprise private networks, public cloud VPCs, or IMS networks

### 2.2.2 Division of Responsibilities Between the RAN and the Core

- **The RAN acts like an “access station”**

    - Managing radio resources: who is allowed to transmit, how much data can be transmitted, and which modulation and coding schemes are used
    - Packaging each UE’s user data and forwarding it toward the core network

- **The Core acts like a “command center and traffic dispatcher”**

    - Determining the UE’s identity, whether it is authorized to access the network, and which path its traffic should follow
    - Deciding which Data Network (DN) the packets should be routed to, and what QoS and policy rules should be applied

This division of responsibilities is conceptually similar to that of the 4G era. However, in 5G, the separation between the control plane and the user plane is made much more explicit, enabling greater optimization and more flexible deployment options.

## 2.3 SBA (Service-Based Architecture)

In the 4G EPC, many network functions communicate with each other using **Diameter** or other traditional telecom protocols.
With the 5G core network, 3GPP introduced a major architectural shift: **the adoption of a Service-Based Architecture (SBA) built on HTTP and JSON**.

### 2.3.1 Why Move from Diameter to HTTP + JSON?

There are several key reasons for this transition:

- Bringing telecom networks closer to the IT and cloud technology stack, making it easier to integrate existing tools and ecosystems
- Making interactions between network functions more “microservices-like”: each network function exposing a clearly defined set of APIs
- Offering advantages over Diameter in terms of multiplexing, latency characteristics, and deployment flexibility

For developers and researchers, this also means that parts of the 5G core network logic can be understood and implemented using familiar web technologies.

### 2.3.2 What Is a Service-Based Interface (SBI)?

In an SBA, each Network Function (NF) exposes its capabilities as services, for example:

- AMF provides services related to UE registration and access management
- SMF provides services related to PDU session management
- PCF provides services related to policy control

These services communicate through a unified conceptual interface known as the **Service-Based Interface (SBI)**. In practice, an SBI is implemented as a set of HTTP + JSON APIs.

> [!Tip]
> You can think of SBA as follows: “each network function in the 5G core acts as a service provider, and they invoke each other using REST-style APIs.”

### 2.3.3 Advantages of SBA

Compared to traditional point-to-point, interface-specific interactions between network functions, SBA offers several clear advantages:

- **Scalability**: When the load on a particular NF increases, additional instances can be deployed. As long as they are registered with the NRF, they can be used dynamically.
- **Replaceability**: Multiple implementations or versions of the same NF can coexist, potentially from different vendors, with compatibility achieved through standardized APIs.
- **Interoperability**: By adhering to common API specifications, different implementations can interoperate more easily, reducing vendor lock-in.
- **Testability**: Developers and testers can validate individual NFs or specific APIs in isolation, without having to deploy the entire system end to end.

## 2.4 5G Interfaces and Protocol Classification (N1 / N2 / N3 / N4 / N6 / SBA)

This section is one of the core parts of the chapter. We organize the commonly used 5G interfaces based on the distinction between the “control plane and the user plane.”

![5gc-n](../../image/part1/5gc-n.png)

### 2.4.1 Control Plane Interfaces

The control plane is primarily responsible for “signaling and coordination,” rather than carrying large volumes of user data. Common control-plane interfaces include the following:

- **N1: UE ↔ 5GC (NAS)**

    - This interface carries high-level control signaling between the UE and the 5GC, such as registration procedures and PDU session establishment requests.
    - The signaling content is referred to as **NAS (Non-Access Stratum)** and is transported via the RAN to the AMF.

- **N2: RAN ↔ 5GC (NGAP)**

    - This is the control interface between the gNB and the 5GC, specifically the AMF.
    - It uses the **NGAP** protocol, which handles functions such as UE context setup, handover control, and PDU Session-related control signaling.

- **N4: SMF ↔ UPF (PFCP)**

    - This interface is used by the SMF to instruct the UPF on how to establish or update forwarding rules, such as which Data Network (DN) packets should be routed to and which QoS parameters should be applied.

- **Nn: 5GC NF ↔ NF (SBA / HTTP/2)**

    - This is a collective term representing service-based interactions between network functions within the 5GC.
    - These interactions are implemented using HTTP/2 and JSON, i.e., the Service-Based Interface (SBI) discussed earlier; typical examples include interactions between the AMF and SMF, or between the SMF and PCF.

### 2.4.2 User Plane Interfaces

The user plane represents the path that “actually carries user data, such as packets for video streaming, web browsing, and data transfer”:

- **N3: RAN ↔ UPF (GTP-U)**

    - This interface provides the user-plane tunnel between the gNB and the UPF.
    - It uses the GTP-U protocol to encapsulate UE IP packets, similar to the S1-U interface in 4G.

- **N6: UPF ↔ DN (Data Network)**

    - This interface connects the UPF to external data networks, such as the public Internet or enterprise private networks.

## 2.5 Overview of Key 5G Core Procedures

This section introduces several of the most important procedures in 5G at a “conceptual level.” Rather than diving into the detailed behavior of each network function, the goal here is to help you first see the overall storyline of how the system operates.

### 2.5.1 Registration Procedure (NAS / NGAP)

Registration can be viewed as the “registration / login procedure” in 5G. At a high level, it consists of the following key steps:

1. **UE initiates a Registration Request**: When the UE powers on or enters a 5G network, it sends a NAS Registration Request to the AMF via the RAN.
2. **UE context establishment**: After receiving the request, the AMF creates a “context” for the device, which includes information such as identity, location, and capabilities.
3. **Security-related procedures**: These include authentication and security mode control, ensuring that subsequent signaling and data transmission are protected and encrypted.
4. **Registration Complete**: After all required exchanges and configurations are completed, the UE responds with Registration Complete to indicate that it has successfully “logged in” to the 5G network.

> [!Tip]
> The **registration** procedure is the first **access** procedure initiated by the UE toward the core network when it powers on or exits airplane mode.

### 2.5.2 PDU Session Procedure

In 4G, we often refer to **Bearers**. In 5G, the corresponding core concept is the **PDU session**, which you can think of as “a **logical connection** between the UE and a specific Data Network (DN).”

At a high level, the procedure works as follows:

1. **UE requests PDU session establishment**: When the UE wants to access the Internet or connect to a specific service, it sends a PDU Session Establishment Request.
2. **SM control-plane decisions**: The SMF in the core network determines which UPF and which DN the PDU session should be associated with, as well as the QoS parameters to be applied.
3. **User-plane GTP-U path establishment**: The user-plane GTP-U path for the PDU session is constructed through N3 (RAN ↔ UPF) and N6 (UPF ↔ DN).

Afterward, the UE’s data packets are forwarded along this PDU session. A single UE may have multiple PDU sessions simultaneously, each corresponding to a different service or network.

> [!Tip]
> Establishing a PDU session is essentially about creating a logical connection from the UE to a DN!

### 2.5.3 Handover Concept

Handover refers to the process by which a UE moves from one base station to another without interrupting the ongoing connection as it travels.

At this level, it is sufficient to understand two key aspects:

- **Control-plane relocation**: The system updates the UE’s location information so that the core network knows which RAN the UE is currently connected through.

- **User-plane path switching**: To ensure that packets continue to reach the UE, the forwarding path is switched from the old gNB to the new gNB. In some cases, the user-plane path at the UPF may also need to be updated.

## 2.6 QoS Flows and 5QI: Differences Between 5G and 4G QoS Models

QoS (Quality of Service) is a fundamental topic in mobile networks. In 4G, QoS is commonly discussed in terms of “bearer.” In 5G, however, the **QoS flow** becomes the more central concept.

### 2.6.1 4G Bearer vs 5G QoS Flow

- In **4G**, an EPS bearer typically corresponds to a single set of QoS parameters. The UE and the core network establish or modify bearers to support specific services.
- In **5G**, a PDU session acts as a “primary tunnel,” and **multiple QoS flows can be associated with a single PDU session**, each with its own QoS characteristics.

> [!Note]
> What does this mean in practice?
>
> Within the same PDU session, different types of traffic can be mapped to different QoS flows. For example:
>
> - A single UE may use one PDU session to carry both general Internet traffic and real-time voice traffic.
> - The network can assign higher priority and lower latency to voice traffic, while applying more relaxed QoS settings to best-effort data traffic.

### 2.6.2 Flow-level QoS: QFI、5QI、GBR / Non-GBR、MBR

In 5G, each QoS flow is associated with several key attributes (which will be discussed in more detail later in this book). Here, we first introduce a few essential terms:

- **QFI (QoS Flow Identifier)**: An identifier used to uniquely distinguish a QoS flow.
- **5QI (5G QoS Identifier)**: 5QI defines the QoS characteristics associated with a QoS flow.

    - Latency requirements
    - Priority level
    - Packet loss tolerance

- **GBR / Non-GBR**:

    - GBR (Guaranteed Bit Rate): Ensures that a minimum bit rate is guaranteed for the flow.
    - Non-GBR: Does not guarantee a specific bit rate, and most general data services fall into this category.

- **MBR (Maximum Bit Rate)**:

    - As the name suggests, this defines “the maximum bandwidth,” and it is often used in conjunction with GBR.
    - Put simply: **GBR specifies “at least this much bandwidth,” while MBR specifies “no more than this much bandwidth.”**
    - By enforcing MBR, the system prevents a single flow from consuming excessive resources when capacity is abundant, thereby preventing other users or services from being impacted.

Through this design, 5G can flexibly support diverse service scenarios such as eMBB, URLLC, and mMTC. Even within a single PDU session, different types of traffic can be treated with different QoS levels.

## 2.7 5G Security Introduction

Security is a fundamental aspect of 5G design. However, this chapter does not dive into cryptographic algorithms or mathematical details. Instead, we introduce a few key concepts to help you build a first impression of the 5G security mechanisms.

### 2.7.1 5G AKA (Authentication and Key Agreement)

5G inherits and extends the AKA framework used in previous generations:

- It verifies the identities of both the UE and the network through a challenge-response mechanism.
- At the same time, it derives cryptographic keys that are later used to protect signaling and data.

You can think of this process as: “both sides first confirm that I really am who I claim to be and that you really are who you claim to be, and then jointly decide which keys will be used to lock the door.”

### 2.7.2 SUCI / SUPI: Privacy Protection

To better protect user privacy, 5G introduces the following identifiers:

- **SUPI (Subscription Permanent Identifier)**: Represents the user’s permanent identity, similar to a “real identity” (e.g., corresponding to the IMSI).

- **SUCI (Subscription Concealed Identifier)**: It is an encrypted, privacy-protected identifier transmitted over the air interface, thereby preventing direct exposure of the user’s permanent identity even under radio eavesdropping. As a result, even if an attacker eavesdrops on the radio interface, it is difficult to directly reveal the user’s true identity.

## 2.8 Chapter Summary

In this chapter, we took a bird’s-eye view of the 5G system and core network from a 3GPP perspective:

- Understood the **role of 3GPP** and the division of responsibilities among SA, RAN, and CT in 5G standardization
- Clarified the roles of **23.501 / 23.502** in defining architecture, procedures, and policy
- Examined the overall structure of **5GS = 5GC + NR**, as well as the high-level relationship between UE, RAN, 5GC, and DN.
- Established a foundational understanding of **the separation between the control plane (N1 / N2 / N4 / Nn) and the user plane (N3 / N6)**
- Gained an initial understanding of the **Service-Based Architecture (SBA)** and the role of the Service-Based Interface (SBI)
- Followed the high-level flow of key procedures, including Registration, PDU Session establishment, and Handover
- Compared the **5G QoS Flow / 5QI** model with the 4G bearer-based QoS model
- Formed a first impression of **basic 5G security concepts**, including AKA, SUCI/SUPI, and security mode procedures

In the following chapters, we will build on the 5G architecture by analyzing the open-source project free5GC, and gradually break down the roles and internal procedures of individual network functions (such as the AMF, SMF, and UPF). This will take you from “understanding the overall map” to “understanding what happens along each individual path.”

<div class="chapter-nav">
  <a href="../../part2-free5gc/chapter3/" class="nav-btn nav-next" title="Next: free5GC Overall Architecture and Module Introduction">
    <span class="arrow"></span>
  </a>
</div>
