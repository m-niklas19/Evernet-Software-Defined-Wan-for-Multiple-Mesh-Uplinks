Für die Installation werden min. 3 OpenWRT Router benöigt.
Einer davon dient als Main Router, auf dem später auch mwan3 läuft. Die anderen Router fungieren als physische WAN Interfaces.

1. Alle Router updaten (alle Router)

"opkg update"

2. Wireguard installieren (alle Router)

"opkg install wireguard"

3. Wireguard einrichten

3.1 