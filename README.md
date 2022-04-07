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
		- Die beste Variante für mich
		- Wireguard und mwan3
        - Konfiguration
        - Ergebnis
        - Unterschiede zu bestehenden Lösungen
3. Zusammenfassung & Ausblick
4. Anhang
	- network Konfiguration (interfaces.md)
	- mwan3 Konfiguration (mwan3.md)
    - Installationsanleitung (Installation.md)

********************


### Motivation
Ich habe durch Recherchen herausgefunden, dass es keine kostengünstige Lösung für Privatkunden gibt, mit der sich mehrere Internetverbindungen zu einer verbinden lassen. Also habe ich beschlossen, dieses Problem selbstständig als Open-Source Projekt anzugehen.
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

#### Die beste Variante für mich
Ich habe mich entschieden eine flowbasierte Lastverteilug zu realisieren, da die anderen Varianten zu kompliziert sind oder nicht meinen Anforderungen entsprechen.
Ein Loadbalancing auf Paketbasis ist für mich zu schwer zu realisieren, weil ich mit einfachen Open-Source Mitteln arbeiten und eine zentrale Lösung erschaffen möchte. Es wäre ein zu großer Aufwand und außerdem nicht einfach nachbaubar, wenn ein zusätzlicher Router an einem anderen Ort mit einer stabileren Intenetverbindung installiert werden muss, z.B. in einem Rechenzentrum.
Eine hostbasierte Lastverteilung kommt für mich auch nicht in Frage, da sich gerade bei Privatanwendern meist nicht viele Geräte im Netzwerk befinden und es auch eine hohe Fluktuation im Netzwerk gibt. (Meist werden nicht alle Geräte gleichzeitig genutzt, z.B. wenn der PC für die Arbeit genutzt wird, wird nicht gleichzeit IP TV gestreamt und das Handy nicht genutzt.) Dies hätte eine relativ ungleichmäßige Verteilung der Last zur folge.
Bei einem flowbasierten Loadbalancing wird selbst bei nur wenigen Hosts eine ausgewogene Verteilung erzielt ohne, dass externe Hardware benötigt wird.

#### Wireguard and mwan3
Da die meisten Router für Privatnutzer nur einen WAN Anschluss besitzen ist es nötig einem Router weitere WAN Anschlüsse hinzuzufügen. Ich nutze dafür einen Router mit dem Linux Beriebssystem openWRT auf dem mwan3 installiert wird. Letzteres erlaubt es unter anderem virtuelle WAN Anschlüsse hinzuzufügen. Jedes virtuelle WAN Interfacewird dann via Wireguard VPN Tunnel mit einem weiteren Router verbunden welcher dann das physische WAN Interface zur Verfügung stellt.
Des weiteren realisiert mwan3 das Loadbalancing. Es entscheidet welcher Datenfluss über welches WAN Interface geroutet wird.
Für das Projekt benötigt man drei einfache Router, z.B. von TP Link, NETGEAR, etc., die teilweise schon gebraucht für unter 30€ erworben werden können. Eine Liste der mit openWRT kompatiblen Router findet man auf der Webseite "openwrt.org" unter dem Reiter "Supported devices".
Der Einfachkeit halber habe ich mich allerdings entschieden mit drei virtualisierten openWRT Instanzen zu arbeiten. Diese habe ich zur besseren Übersicht benannt. Der Hauptrouter, auf dem mwan3 installiert ist, wird im folgenden als MutterRouter und die anderen beiden Router als Uplink1 und Uplink2 bezeichnet.

####Konfiguration
Genaue Installationsanleitung unter "Installation.md". Konfigurationsdateien für Netzwerk unter "network.md" und für mwan3 unter "mwan3.md".

Zuerst habe ich auf allen drei Routern das Paket Wireguard installiert um vom MutterRouter jeweils einen VPN Tunnel zu jedem Uplink aufzubauen. Dabei ist es wichtig, dass jede VPN Verbindung auf dem MutterRouter als separates Interface eingerichtet wird.
Dabei werden in der Netzwerk Konfigurationsdatei (/etc/config/network) zwei Interfaces "wg0" und "wg1" angelegt, mit dem Interfacetyp Wireguard, dem eigenen private Key, einer IP für die VPN Verbindung und einer Metric. Des weitern werden in der gleichen Datei die Wireguardverbindungen selbst konfiguriert. Diese Konfigurationen enthalten eine Liste mit zulässigen IPs für Verbindungen, IP und  der Gegenstelle und dem public Key der Gegenstelle. Gleiches erflogt auf den Uplinks.
Mit einem einfachen Ping kann getestet werden, ob die VPN Verbindung aufgebaut wurde und die Gegenstelle erreichbar ist. Dabei ist darauf zu achten, dass der Ping auch über das entsprechende Interface ausgeführt wird, da sonst das ausgewählte Standartinterface benutzt wird.
Wenn nun beide VPN Tunel stehen kann mit der Installation und Konfiguration von mwan3 fortgefahren werden. 
Dazu wird das Paket mwan3 nur auf dem MutterRouter installiert. Zur Konfiguration wir die mwan3 Konfigurationsdatei aufgerufen, welche sim im gleichen Ordner wie die network Datei befindet (/etc/config/mwan3). Auch hier müssen zwei Interfaces angelegt werden. Diese weden analog zu den zwei Interfaces aus der Netzwerkkonfiguration benannt, damit mwan3 die Einstelllungen auf die richtigen Interfaces bezieht. Wichtig ist hier der Eintrag "option enabled", welche den Wert "1Q enthalten muss und der Eintrag "list track_ip". Bei letzterem werden IP Adressen eingetragen, welche dafür benutzt werden, um mittels Ping zu überprüfen, ob das Interface eine Verbindung zum Internet hat. Ebenfalls muss pro Interface ein "Member" angelegt werden, welchen jeweil eins der Interfaces zugewiesen wird. Über die Einträge "metric" und "weight" kann eine Gewichtung der Interfaces beim Loadlacancing festgelegt werden oder auch ein Failover realisier werden. Bei mir sind die beiden Werte bei beiden Membern identisch, um eine 50/50 Lastverteilung zu erzielen. Um mwan3 weiß, welche Interfaces in welcher Situation genutzt werden sollen, müssen noch Regeln definiert werden. In einer neuen policy werden die member genannt, die angesprochen werden sollen. In welchem Fall, welche policy verwendet wird, wird mit einer rule festgelegt. Dabei können für verschiedene IP Bereiche verschiedene Regeln genutzt werden. In meiner policy stehen beide member, die  die beiden Wireguard Interfaces wg0 und wg1 beinhalten und meine rule gilt für den kompletten IPv4 Bereich und nutzt dafür die gannate policy.
Nun sollten nach eingabe des Befehls "mwan3 interfaces" wgo und wg1 als "online" sichtbar sein und nach Eingabe von "ip route" wg0 und wg1 als default gekennzeichnet sein.


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
