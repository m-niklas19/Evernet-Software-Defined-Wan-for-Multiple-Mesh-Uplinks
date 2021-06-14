## Software Defined Wan for Multiple Uplinks in Wireless Mesh Networks

1. Introduction
    - Motivation
    - Goals
2. Main Part
    - Principles of multiple uplink sharing
        - Package based
        - Flow based
        - Per Host
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
