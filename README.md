## Softwarebasiertes WAN für mehrere Uplinks in drahtlosen Mesh-Netzwerken 

1. Einleitung
    - Motivation
    - Ziel
2. Hauptteil
    - Varianten der Nutzung mehrerer Uplinks
        - Wireguard und mwan3 -> passt nicht unbedingt an diese Stelle
        - Lastverteilung
            - Paketbasiert
            - Flowbasiert
            - Hostbasiert
        - Failover
    - Ähnliche Systeme
        - Viprinet
        - iTel
        - andere
    - Meine Lösung
        - Die beste Variante für mich
        - Predecure
        - Ergebnis
        - Unterschiede zu bestehenden Lösungen
3. Zusammenfassung
4. Anhang
    - Installationsanleitung

********************


### Motivation
Ich habe durch Recherchen herausgefunden, dass es keine gute Lösung für Privatkunden gibt, mit der sich mehrere Internetverbindungen zu einer verbinden lassen. Also habe ich beschlossen, dieses Problem selbstständig als Open-Source-Projekt anzugehen.
Folgende Anwendungsfälle könnte es für diese Software geben:
- Livestreams, welch eine dauerhaft stabile Internetverbindung benötigen
- Selbst gehostete Server, die immer erreichbar sein sollen
- Initiativen wie Freifunk", die mehrere Internetverbindungen für ihre Mesh-Netzwerke brauchen

### Ziel
Entwicklung einer Software, die mehrere Internetverbindungen zu einer kombiniert.
Diese Software soll 2 grundlegende Betriebsmodi haben. Zum Einen ist es möglich eine Lastverteilung zwschen den beiden Internetverbindungen durchzuführen. Zum Anderen ist es möglich von einer Verbindung zur anderen zu wechseln, falls ein Uplink wegbricht.


### Varianten der Nutzung mehrerer Uplinks
#### Wireguard and mwan3
Da die meisten Router für Privatnutzer nur einen WAN Anschluss besitzen ist es nötig einen Router mit mehreren WAN Anschlüssen zu verwenden. Ich nutze dafür einen Router mit dem Linux Beriebssystem openWRT auf dem mwan3 installiert wird. Letzteres erlaubt es virtuelle WAN Anschlüsse hinzuzufügen. Jedes virtuelle WAN Interfacewird dann via Wireguard VPN Tunnel mit einem weiteren Router verbunden welcher dann das physische WAN Interface zur Verfügung stellt.

#### Lastverteilung
Wie schon eingangs erwähnt ist einer Betriebsmodus die Lastverteilung. Diese kann auf mehrere Arten durchgeführt werden, welche veschiedene Vor- und Nachteile bieten.
##### Paketbasiert
Eine paketbasierte Lastverteilung ermöglicht die gleichmäßigste Verteilung auf die beiden Uplinks, bringt aber auch den größen Nachteil mit sich. Es wird zwingend ein Server benötigt, der die einzelnen Pakete wieder zusammenführt und mit seiner Public IP weiterleitet. Das liegt daran, dass jede Internetverbindung ihre eigene Public IP besitzt und einige Dienste dann nicht funktionieren könnten, weil die Pakete einer Sitzung mit IPs von zwei verschiedenen Absendern ankommen. 

##### Flowbasiert

##### Hostbasiert
Die hostbasierte Lastverteilung is die einfachste, aber auch ungleichmäßigste Variante. Dabei wird jedem Host ein Uplink zugeteilt, über den dann sein Traffic läuft. In kleinen Netzwerken mit nur wenigen Hosts kann das zu einer ungleichmäßigen Auslastung der Internetzugänge führen. Es ist daher eher für größere Netzwerke mit vielen Hosts geeignet.

#### Failover
Das Ziel eines Failovers ist es, beim Ausfall einer Internetverbindung auf eine zweite zu wechseln. Diese Variante ermöglicht es eine dauerhaft stabile Internetverbing aufrecht zu erhalten, auch wenn ein Uplink wegbricht. Dadurch ist der Failover die sicherste Möglichekeit des Zugangs zum Internet, aber bietet keine Geschwindigkeitsvorteile.


### Ähnliche Systeme
Das Ziel von fast allen SD-WAN Lösungen ist es Zweigsetllen mit der Hauptniederlassung oder dem Rechenzentrum zu verbinden. Deswegen benötigt man bei diesen Varianten zwei Geräte.

### Meine Lösung

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
