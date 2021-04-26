## Evernet-Software-Defined-Wan-for-Multiple-Mesh-Uplinks
>
>Motivation
>I have been told from different persons that there is no proper software to combine multiple uplinks to the internet.
>So I decided to tackle this problem on my own as an open source project.
>Usecases of this software could be:
>-Livestreams; need a stable internet connection
>-Computer centres; need a stable and fast internet connection
>-Organizations like "Freifunk"; need multiple internet connections for their mesh-networks
>
>#Goal
>Development of a software wich combines multiple uplinks into one.
>This software has two main purposes. The first one is to do a loadbalancing between the uplinks to enable an even utilization.
>The second purpose is an intuitive changeover in case one uplinks disconnects.
>
>Work schedule
>-Get a general understanding of how multi gateway mesh network work
>-Get a general understanding of Wireguard and mwan3
>-Learn hoe to combine those technical tunnel and load scheduling and sharing technics
>-Develop technical concept for multi gateway multiplexing
>  -What are the Wireguard and Routing challenges to tackle?
>  -How will handover switches in cases of failure speed up?
>  -Which kind of traffic flows can be easily loadbalanced hence muliuplexed across multiple gayteways?
>-Create a usable Freinfunk web-based user interface for administration.
