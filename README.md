## Softwarebasiertes WAN für mehrere Uplinks in drahtlosen Mesh-Netzwerken 

1. Einleitung
    - Motivation
    - Ziel
2. Hauptteil
    - Varianten der Nutzung mehrerer Uplinks
        - Lastverteilung
            - Paketbasiert
            - Hostbasiert
			- Flowbasiert
        - Failover
    - Ähnliche Systeme
        - Viprinet
        - iTel
        - andere
    - Meine Lösung
		- Wireguard und mwan3
        - Die beste Variante für mich
        - Procedure
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

#### Lastverteilung
Wie schon eingangs erwähnt ist einer Betriebsmodus die Lastverteilung. Diese kann auf mehrere Arten durchgeführt werden, welche veschiedene Vor- und Nachteile bieten.
##### Paketbasiert
Eine paketbasierte Lastverteilung ermöglicht die gleichmäßigste Verteilung auf die beiden Uplinks, bringt aber auch den größen Nachteil mit sich. Es wird zwingend ein Server benötigt, der die einzelnen Pakete wieder zusammenführt und mit seiner Public IP weiterleitet. Das liegt daran, dass jede Internetverbindung ihre eigene Public IP besitzt und einige Dienste dann nicht funktionieren könnten, weil die Pakete einer Sitzung mit IPs von zwei verschiedenen Absendern ankommen. 

##### Hostbasiert
Die hostbasierte Lastverteilung is die einfachste, aber auch ungleichmäßigste Variante. Dabei wird jedem Host ein Uplink zugeteilt, über den dann sein Traffic läuft. In kleinen Netzwerken mit nur wenigen Hosts kann das zu einer ungleichmäßigen Auslastung der Internetzugänge führen. Es ist daher eher für größere Netzwerke mit vielen Hosts geeignet.

##### Flowbasiert
Die Lastverteilung durch Flows ist eine gute Variante, wenn es nur wenige Hosts gibt und kein Server zur Zusammenführung der IP Pakete genutzt werden soll. Ein Flow ist ein Datenfluss zwischen zwei Endpunkten mit Quell und Ziel IP und Port in einem Netz. Dadurch hat ein Host bei einem Verbindungsaufbau nur eine Public IP. Dies ermöglicht eine feine Lastverteilung zwischen mehreren Uplinks, ohne das externe Hardware benötigt wird.

#### Failover
Das Ziel eines Failovers ist es, beim Ausfall einer Internetverbindung auf eine zweite zu wechseln. Diese Variante ermöglicht es eine dauerhaft stabile Internetverbing aufrecht zu erhalten, auch wenn ein Uplink wegbricht. Dadurch ist der Failover die sicherste Möglichekeit des Zugangs zum Internet, aber bietet keine Geschwindigkeitsvorteile.


### Ähnliche Systeme
#### Viprinet
Viprinet hat das Ziel eine hohe Ausfallsicherheit und eine Aggregation von mehreren Internetverbindungen zu gewährleisten. Dafür nutzen sie zwei Geräte. Einen Router mit mehreren WAN Interfaces, welcher an dem Standort positioniert wird, an dem die Ausfallsichere Internetverbindung benötigt wird. Dieser Router baut dann pro WAN Interface eine VPN Verbindung zu dem zweiten Gerät auf; dem VPN Hub. Dieser Hub ist so positioniert, dass er immer erreichbar ist, also z.B. in einem Rechenzentrum, weil dieser den eigentlichen Internetaustrittspunkt darstellt. Viprinet nutzt vermutlich eine Paketbasierte Lastverteilung, bei der der VPN Hub die einzelnen Pakete, die durch die verschiedenen WAN Interfaces auch verschiedene IPs besitzen, wieder zusammenfügt und mit seiner IP an das Ziel weiterleitet.
Zu bachten ist dabei, dass der Kunde zwei Geräte kaufen muss. Der Router, welcher in der Firma positioniert wird und den VPN Hub, welcher in einem Rechenzentrum untergebracht werden sollte. Das bringt natürlich auch hohe Kosten mit sich, da die Kaufprese der zwei Geräte anfallen und außerdem noch die Miete vom Rechenzentrum.

####iTel
Das System vin iTel funktioniert im großen und ganzen Ähnlich wie bei Viprinet, jedoch mit einem großen Unterschied.
Der Bonding Aggregator, welcher die Datenpakete wieder zusammenführt, muss nicht vom Kunden erworben werden, sondern sitzt im Rechenzentrum von iTel. Das hat den Vorteil, dass hier zwar trotzdem monatliche Kosten anfallen, aber nicht der Kaufpreis und außerdem das Gerät vom Hersteller gewartet und im Fehlerfall ausgetauscht wird. Ein Nachteil ist allerdings die Datensicherheit, denn theoretisch kann iTel die Datenpakete analysieren.

####Andere
Viele Business Router bieten die Möglichkeit an mehr als einem WAN Interface Internetverbindungen anzuschließen. Diese bieten oft aber nur die Möglichkeit des Failovers, also einer Redundanz, falls ein Uplink ausfällt. In seltenen Fällen gibt es die Möglichkeit einer Lastverteilung, wie z.B. bei Sophos. Hier wird aber nicht näher erläutert, auf welchem Weg das Loadbalancing realisiert wird.

### Meine Lösung

#### Wireguard and mwan3
Da die meisten Router für Privatnutzer nur einen WAN Anschluss besitzen ist es nötig einen Router mit mehreren WAN Anschlüssen zu verwenden. Ich nutze dafür einen Router mit dem Linux Beriebssystem openWRT auf dem mwan3 installiert wird. Letzteres erlaubt es unter anderem virtuelle WAN Anschlüsse hinzuzufügen. Jedes virtuelle WAN Interfacewird dann via Wireguard VPN Tunnel mit einem weiteren Router verbunden welcher dann das physische WAN Interface zur Verfügung stellt.
Des weiteren realisiert mwan3 das Loadbalancing bzw. Failover. Es entscheidet welcher Datenfluss über welches WAN Interface geroutet wird.

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
- [x] Check & Add Related Work with References
- [x] Software Design
- [x] Programmierung / Umsetzung
- [ ] Schriftkram
- [ ] Installationsanleitung
- [ ] Zusammenfassung & Ausblick
