## Software Defined Wan for Multiple Uplinks in Wireless Mesh Networks

1. Introduction
    - Motivation
    - Goals
2. Main Part
    - Principles of multiple uplink sharing
        - wireguard and mwan3
        - load sharing
            - Per Packet
            - Per Flow
            - Per Host
        - failover
    - Related Work
        - Viprinet
        - iTel
        - others
    - My solution
        - The best principle for me
        - Predecure
        - Result
        - Differences to existing solutions
3. Summary
4. Attachment
    - Installation guide

********************

### Motivation
I have been told from different persons that there is no proper software to combine multiple uplinks to the internet.
So I decided to tackle this problem on my own as an open source project.
Usecases of this software could be:
- Livestreams; need a stable internet connection
- Computer centres; need a stable and fast internet connection
- Organizations like "Freifunk"; need multiple internet connections for their mesh-networks

### Goal
Development of a software wich combines multiple uplinks into one.
This software has two main purposes. The first one is to do a loadbalancing between the uplinks to enable an even utilization.
The second purpose is an intuitive changeover in case one uplinks disconnects.

### Principles of multiple uplink sharing
#### Wireguard and mwan3
Our goal is to combine multiple uplinks, but the basic problem is that most consumer router do not have more than one physical wan connection. So we need more wan interfaces on our router. The solution is mwan3. This software allows to add virtual wan interfaces to the main router. Each of those virtual interfaces is connected via wireguard vpn tunnel to an other router with a physical wan interface. With this procedure we are able to increase the number of uplinks on the main router.

#### Load sharing
One operation mode is the sharing of the load between the connected uplinks. There are several ways this could be done with different advantages and disadvantages.
##### Per Packet
Packetbased load sharing allows the most evenly distributed load. But it also entails the biggest disadvantage, the need of a server to recombine the packets.
Every packet is send over one of the uplinks in e.g. round robin procedure. The problem is that every wan interface has its own IP, so some services could block the connection because of different IPs of the packets.

##### Per Flow

##### Per Host

#### Failover


### Related Work
The goal of almost all SD-Wan solution is to connect branch offices to your servers. So they are using ...

### Work schedule
- Get a general understanding of how multi gateway mesh network work
- Get a general understanding of Wireguard and mwan3
- Learn hoe to combine those technical tunnel and load scheduling and sharing technics
- Develop technical concept for multi gateway multiplexing
  - What are the Wireguard and Routing challenges to tackle?
  - How will handover switches in cases of failure speed up?
  - Which kind of traffic flows can be easily loadbalanced hence muliuplexed across multiple gayteways?
- Create a usable Freinfunk web-based user interface for administration.

## ToDo List:
- [ ] Check & Add Related Work with References
- [ ] Software Design
- [ ] Programmierung / Umsetzung
- [ ] Installationsanleitung
- [ ] Validierung / Limitierung
- [ ] Zusammenfassung & Ausblick
