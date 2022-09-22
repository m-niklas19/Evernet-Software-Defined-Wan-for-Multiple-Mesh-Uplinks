## Softwarebasiertes WAN für mehrere Uplinks in drahtlosen Mesh-Netzwerken 

1. [Einleitung](#Einleitung)
    - [Motivation](#Motivation)
    - [Problemstellung](#Problemstellung)
    - [Hintergrund](#Hintergrund)

2. [Implementierung](#Implementierung)
    - [Varianten](#Varianten)
        - [Lastverteilung](#Lastverteilung)
            - [Paketbasiert](#Paketbasiert)
            - [Hostbasiert](#Hostbasiert)
			- [Flowbasiert](#Flowbasiert)
    - [Failover](#Failover)
    - [Alternativen](#Alternativen)
        - [Viprinet](#Viprinet)
        - [iTel](#iTel)
        - [andere](#andere)
    - [Auswahl im Projekt](#Auswahl-im-Projekt)
        - [Wireguard und mwan3](#Wireguard-und-mwan3)
        - [Konfiguration](#Konfiguration)

3. [Validierung und Evaluation](#Validierung-und-Evaluation)
    - [Untersuchte Szenarien](#Untersuchte-Szenarien)
    - [Traffic Setup](#Traffic-Setup)
    - [Ergebnis](#Ergebnis)

3. [Zusammenfassung & Ausblick](#Zusammenfassung--Ausblick)

4. Anhang
	- [network Konfiguration](/Quellen/mwan3%20config.md)
	- [mwan3 Konfiguration](/Quellen/network%20config.md)

********************

## Einleitung

### Motivation
Durch meine Interessen im Bereich Kommunikations- und Veranstaltungstechnik habe ich oft die Herausforderung, dass für bestimmte Vorhaben eine stabile und schnelle Internetanbindung benötigt wird. Solche Anwendungsfälle sind zum Beispiel Livestreams, Server oder selbst verwaltete Mesh-Netzwerke wie von "Freifunk".

### Hintergrund
Um eine solche zuverlässige Internetverbindung zu realisieren ist es notwendig mehrere Anschlüsse ans Internet zu verbinden. Dadurch ergibt sich eine höhere Geschwindigkeit durch Verteilung des Traffic auf alle Uplinks. Des Weiteren sind im Fall des Abbruch einer Verbindung die anderen weiterhin mit dem Internet verbunden.

### Problemstellung
Da bereits bestehende Lösungen von diversen Herstellern meist teuer sind und nicht die gewünschte Funktionsweise bieten, habe ich mich entschlossen eine eigene Lösung zu entwickeln. Das Ziel soll es sein eine Lastverteilung zwischen mehreren Internetverbindungen zu gewährleisten. Das System soll ausschließlich mit Open-Source Software realisiert werden, damit nur geringe kosten für Hardware entstehen und es einfach nachbaubar ist.


## Implementierung

### Varianten 

#### Lastverteilung
Wie schon eingangs erwähnt ist einer Betriebsmodus die Lastverteilung. Diese kann auf mehrere Arten durchgeführt werden, welche verschiedene Vor- und Nachteile bieten.
##### Paketbasiert
Eine paketbasierte Lastverteilung ermöglicht die gleichmäßigste Verteilung auf die beiden Uplinks, bringt aber auch den größten Nachteil mit sich. Es wird zwingend ein Server benötigt, der die einzelnen Pakete wieder zusammenführt und mit seiner Public IP weiterleitet. Das liegt daran, dass jede Internetverbindung ihre eigene Public IP besitzt und einige Dienste dann nicht funktionieren könnten, weil die Pakete einer Sitzung mit IPs von zwei verschiedenen Absendern ankommen. 

##### Hostbasiert
Die hostbasierte Lastverteilung ist die einfachste, aber auch ungleichmäßigste Variante. Dabei wird jedem Host ein Uplink zugeteilt, über den dann sein Traffic läuft. In kleinen Netzwerken mit nur wenigen Hosts kann das zu einer ungleichmäßigen Auslastung der Internetzugänge führen. Es ist daher eher für größere Netzwerke mit vielen Hosts geeignet.

##### Flowbasiert
Die Lastverteilung durch Flows ist eine gute Variante, wenn es nur wenige Hosts gibt und kein Server zur Zusammenführung der IP-Pakete genutzt werden soll. Ein Flow ist ein Datenfluss zwischen zwei Endpunkten mit Quell und Ziel IP und Port in einem Netz. Dadurch hat ein Host bei einem Verbindungsaufbau nur eine Public IP. Dies ermöglicht eine feine Lastverteilung zwischen mehreren Uplinks, ohne das externe Hardware benötigt wird.

#### Failover
Das Ziel eines Failovers ist es, beim Ausfall einer Internetverbindung auf eine zweite zu wechseln. Diese Variante ermöglicht es eine dauerhaft stabile Internetverbindung aufrecht zu erhalten, auch wenn ein Uplink wegbricht. Dadurch ist Failover die sicherste Möglichkeit des Zugangs zum Internet, aber bietet keine Geschwindigkeitsvorteile.


### Alternativen

#### Viprinet
Viprinet hat das Ziel eine hohe Ausfallsicherheit und eine Aggregation von mehreren Internetverbindungen zu gewährleisten. Dafür nutzen sie zwei Geräte. Einen Router mit mehreren WAN-Interfaces, welcher an dem Standort positioniert wird, an dem die Ausfallsichere Internetverbindung benötigt wird. Dieser Router baut dann pro WAN-Interface eine VPN-Verbindung zu dem zweiten Gerät auf; dem VPN-Hub. Dieser Hub ist so positioniert, dass er immer erreichbar ist, also z.B. in einem Rechenzentrum, weil dieser den eigentlichen Internetaustrittspunkt darstellt. Viprinet nutzt vermutlich eine Paketbasierte Lastverteilung, bei der der VPN-Hub die einzelnen Pakete, die durch die verschiedenen WAN-Interfaces auch verschiedene IPs besitzen, wieder zusammenfügt und mit seiner IP an das Ziel weiterleitet.
Zu beachten ist dabei, dass der Kunde zwei Geräte kaufen muss. Der Router, welcher in der Firma positioniert wird und den VPN-Hub, welcher in einem Rechenzentrum untergebracht werden sollte. Das bringt auch hohe Kosten mit sich, da die Kaufpreise der zwei Geräte anfallen und außerdem noch die Miete vom Rechenzentrum.

#### iTel
Das System von iTel funktioniert ähnlich wie bei Viprinet, jedoch mit einem großen Unterschied.
Der Bonding Aggregator, welcher die Datenpakete wieder zusammenführt, muss nicht vom Kunden erworben werden, sondern sitzt im Rechenzentrum von iTel. Das hat den Vorteil, dass hier zwar trotzdem monatliche Kosten anfallen, aber nicht der Kaufpreis und außerdem das Gerät vom Hersteller gewartet und im Fehlerfall ausgetauscht wird. Ein Nachteil ist allerdings die Datensicherheit, denn theoretisch kann iTel die Datenpakete analysieren.

#### Andere
Viele Business Router bieten die Möglichkeit an mehr als einem WAN Interface Internetverbindungen anzuschließen. Diese bieten oft aber nur die Möglichkeit des Failovers, also einer Redundanz, falls ein Uplink ausfällt. In seltenen Fällen gibt es die Möglichkeit einer Lastverteilung, wie z.B. bei Sophos. Hier wird aber nicht näher erläutert, auf welchem Weg das Loadbalancing realisiert wird.

### Auswahl im Projekt
Ich habe mich entschieden eine flowbasierte Lastverteilung zu realisieren, da die anderen Varianten zu kompliziert sind oder nicht meinen Anforderungen entsprechen.
Ein Loadbalancing auf Paketbasis ist für mich zu schwer zu realisieren, weil ich mit einfachen Open-Source Mitteln arbeiten und eine zentrale Lösung erschaffen möchte. Es wäre ein zu großer Aufwand und außerdem nicht einfach nachbaubar, wenn ein zusätzlicher Router an einem anderen Ort mit einer stabileren Internetverbindung installiert werden muss, z.B. in einem Rechenzentrum.
Eine hostbasierte Lastverteilung kommt für mich auch nicht in Frage, da sich gerade bei Privatanwendern meist nicht viele Geräte im Netzwerk befinden und es auch eine hohe Fluktuation im Netzwerk gibt. (Meist werden nicht alle Geräte gleichzeitig genutzt, z.B. wenn der PC für die Arbeit genutzt wird, wird nicht gleichzeitig IP TV gestreamt und das Handy nicht genutzt.) Dies hätte eine ungleichmäßige Verteilung der Last zur Folge.
Bei einem flowbasierten Loadbalancing wird selbst bei nur wenigen Hosts eine ausgewogene Verteilung erzielt ohne, dass externe Hardware benötigt wird.

#### Wireguard und mwan3
Da die meisten Router für Privatnutzer nur einen WAN-Anschluss besitzen ist es nötig einem Router weitere WAN-Anschlüsse hinzuzufügen. Ich nutze dafür einen Router mit dem Linux Betriebssystem openWRT auf dem mwan3 installiert wird. Letzteres erlaubt es unter anderem virtuelle WAN-Anschlüsse hinzuzufügen. Jedes virtuelle WAN Interfacewird dann via Wireguard VPN Tunnel mit einem weiteren Router verbunden, welcher dann das physische WAN-Interface zur Verfügung stellt.
Des Weiteren realisiert mwan3 das Loadbalancing. Es entscheidet welcher Datenfluss über welches WAN-Interface geroutet wird.
Für das Projekt benötigt man drei einfache Router, z.B. von TP Link, NETGEAR, etc., die teilweise schon gebraucht für unter 30€ erworben werden können. Eine Liste der mit openWRT kompatiblen Router findet man auf der Webseite "openwrt.org" unter dem Reiter "Supported devices".
Der Einfachheit halber habe ich mich allerdings entschieden mit drei virtualisierten openWRT Instanzen zu arbeiten. Diese habe ich zur besseren Übersicht benannt. Der Hauptrouter, auf dem mwan3 installiert ist, wird im Folgenden als MutterRouter und die anderen beiden Router als Uplink1 und Uplink2 bezeichnet.

#### Konfiguration
Konfigurationsdateien für Netzwerk unter "network.md" und für mwan3 unter "mwan3.md".

Zuerst habe ich auf allen drei Routern das Paket Wireguard installiert, um vom MutterRouter jeweils einen VPN-Tunnel zu jedem Uplink aufzubauen. Dabei ist es wichtig, dass jede VPN-Verbindung auf dem MutterRouter als separates Interface eingerichtet wird.
Dabei werden in der Netzwerk Konfigurationsdatei (/etc/config/network) zwei Interfaces "wg0" und "wg1" angelegt, mit dem Interfacetyp Wireguard, dem eigenen private Key, einer IP für die VPN-Verbindung und einer Metric. Des Weiteren werden in der gleichen Datei die Wireguard Verbindungen selbst konfiguriert. Diese Konfigurationen enthalten eine Liste mit zulässigen IPs für Verbindungen, IP und der Gegenstelle und dem public Key der Gegenstelle. Gleiches erflogt auf den Uplinks.
Mit einem einfachen Ping kann getestet werden, ob die VPN-Verbindung aufgebaut wurde und die Gegenstelle erreichbar ist. Dabei ist darauf zu achten, dass der Ping auch über das entsprechende Interface ausgeführt wird, da sonst das ausgewählte Standartinterface benutzt wird.
Wenn nun beide VPN-Tunnel stehen, kann mit der Installation und Konfiguration von mwan3 fortgefahren werden. 
Dazu wird das Paket mwan3 nur auf dem MutterRouter installiert. Zur Konfiguration wir die mwan3 Konfigurationsdatei aufgerufen, welche sich im gleichen Ordner wie die network Datei befindet (/etc/config/mwan3). Auch hier müssen zwei Interfaces angelegt werden. Diese werden analog zu den zwei Interfaces aus der Netzwerkkonfiguration benannt, damit mwan3 die Einstelllungen auf die richtigen Interfaces bezieht. Wichtig ist hier der Eintrag "option enabled", welche den Wert "1Q enthalten muss und der Eintrag "list track_ip". Bei letzterem werden IP-Adressen eingetragen, welche dafür benutzt werden, um mittels Ping zu überprüfen, ob das Interface eine Verbindung zum Internet hat. Ebenfalls muss pro Interface ein "Member" angelegt werden, welchen jeweils eins der Interfaces zugewiesen wird. Über die Einträge "metric" und "weight" kann eine Gewichtung der Interfaces beim Loadbalancing festgelegt werden oder auch ein Failover realisier werden. Bei mir sind die beiden Werte bei beiden Membern identisch, um eine 50/50 Lastverteilung zu erzielen. Um mwan3 weiß, welche Interfaces in welcher Situation genutzt werden sollen, müssen noch Regeln definiert werden. In einer neuen policy werden die member genannt, die angesprochen werden sollen. In welchem Fall, welche policy verwendet wird, wird mit einer rule festgelegt. Dabei können für verschiedene IP-Bereiche verschiedene Regeln genutzt werden. In meiner policy stehen beide member, die die beiden Wireguard Interfaces wg0 und wg1 beinhalten und meine rule gilt für den kompletten IPv4 Bereich und nutzt dafür die genannte policy.
Nun sollten nach Eingabe des Befehls "mwan3 interfaces" wg0 und wg1 als "online" sichtbar sein und nach Eingabe von "ip route" wg0 und wg1 als default gekennzeichnet sein.


## Validierung und Evaluation

### Untersuchte Szenarien
Ich habe zwei grundlegende Szenarien untersucht. Dabei handelt es sich zum einen um den Normalbetrieb des Systems, bei dem beide Internetverbindungen aktiv sind und überprüft werden soll, ob das Loadbalancing funktioniert und der Traffic gleichmäßig auf alle Uplinks aufgeteilt wird.
Das zweite Szenario baut auf dem ersten auf und simuliert den Ausfall einer Internetverbindung und soll zeigen, ob in diesem Fall der Traffic auf die andere Internetverbindung umgeleitet wird, also das Failover funktioniert.

### Traffic Setup
Getestet habe ich die Szenarien mithilfe eines iPerf3 Speedtest. Beim ersten Versuch habe ich zwei parallele iPerf3 Streams laufen lassen, um zu sehen, ob der Traffic gleichmäßig verteilt wird.
Beim zweiten Versuch habe ich, während der laufenden iPerf3 Streams, einen Uplink Router ausgeschaltet, um einen Ausfall zu simulieren und das Verhalten von mwan3 in diesem Fall zu überprüfen.

#### Ergebnis
Beim ersten Szenario hat sich gezeigt, dass wie erwartet der Traffic gleichmäßig auf beide Uplinks aufgeteilt wird. Die Funktion als Loadbalancer ist also gegeben.
Beim zweiten Teil des Versuchs konnte ich beobachten, dass der gerade aktiv laufende Flow nicht sofort auf die weiterhin aktive Internetverbindung umgeleitet wird. Sobald ein neuer Flow gestartet wird, wird dieser jedoch, wie gewünscht, über den aktiven Uplink geleitet. Das zeigt, dass die Funktion als Failover nicht vollumfänglich, aber in einem trotzdem praktisch nutzbaren Rahmen gegeben ist.


## Zusammenfassung & Ausblick
Es ist mir gelungen mit mwan3, Wireguard VPN Verbindungen und mehreren openWRT Routern einen funktionierenden Loadbalancer zu erstellen. Ich konnte auch durch Tests nachweisen, dass die angestrebten Funktionen (Loadbalancing und Failover) funktionieren. Da dieses Projekt auf virtualisierten Routern basiert kann jedoch nicht gesagt werden, dass es auch in der Praxis funktioniert und ggf. an einigen Stellen optimiert werden müsste. Dafür wäre es nötig das System mit nicht-virtualisierten Routern aufzubauen und in, in der Einleitung beschriebenen, Szenarien zu testen.

