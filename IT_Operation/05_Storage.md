
Rolle des Storage
- Daten zentraler Gegenstand
    - Computer erzeugen, verändern und löschen Daten
    - Nur begrenzte Kapazität des Hauptspeichers
    - Keine persistente Datenhaltung im Hauptspeicher
- Auslagerung der Speicherung
    - Nutzung von Storage (Festplatten, …)
    - Effizienz der Anbindung entscheidend
- Zahlreiche Techniken
    - Unterscheiden sich in Kapazität, Geschwindigkeit, Skalierbarkeit, …
    - Wahl der für den jeweiligen Einsatzzweck optimalen Technologie


![](image/Pasted%20image%2020241216012649.png)

# 1 RAIDs, iSCSI und Fiber Channels

## 1.1 Fibre Channel (FC)

- Backbone Netzwerk für die Verbindung von LANs
- Ablösung von Fast Ethernet und Fibre Distrib . Data Interface (FDDI)
- Design Ziele
    - Serielle Übertragung für hohe Geschwindigkeiten
    - Geringe Rate an Übertragungsfehlern
    - Geringe Latenz beim Versand von Daten
    - Implementierung des FC Protokolls in Hardware auf den Hostbus Adapterkarten zur Entlastung der Server CPUs
- Nutzung für SANs durch Seagate (1992)
    - Positionierung gegen IBM Serial Storage Architecture (SAA)
    - Durchbruch durch günstige SAN Infrastrukturen


Fibre Channel Topologien
![](image/Pasted%20image%2020241216013227.png)

Zoning
- FC Fabric ist eine Infrastruktur mit zahlreichen Geräten (Server, Disks, …)
- Herausforderung: Übersichtlichkeit, Sicherheit
- Lösung: Zoning als Zusammenfassung von Geräten, die sich gegenseitig sehen sollen
    - Soft Zoning: Lösung auf Basis des Name Servers (NS)
        - NS gibt nur Auskunft über Geräte in gleicher Zone
        - Vorteil: Einfach und kostengünstig zu implementieren
        - Nachteil: Verhindert nicht den Zugriff auf ein FC Gerät, da die Liste von Endgeräten ggf. lange im Cache
    - Hard Zoning: Filterung durch den FC Switch
        - Änderungen haben sofortige Auswirkung
        - Zugriff auf Geräte wird verhindert, auch wenn Adresse bekannt


## 1.2 iSCSI

- IP Storage: iSCSI
    - iSCSI ist IETF Standard seit 2003
    - Aktueller iSCSI Standard RFC7143 aus 2014
    - iSCSI kapselt SCSI Befehle und Daten in TCP Pakete und überträgt diese über TCP/IP
- Kostengünstiger SAN Aufbau, da die SCSI Kabel durch TCP/IP Verbindungen ersetzt werden

![](image/Pasted%20image%2020241216013624.png)


## 1.3 RAID-Technologie

RAID = Redundant Array of Independent Disks

Ziele
- Erhöhung der Ausfallsicherheit durch Redundanz
- Steigerung von Transferraten
- Aufbau großer logischer Laufwerke
- Austausch von Festplatten und Erhöhung der Speicherkapazität während des Betriebs
- Kostenreduktion durch Einsatz mehrerer günstiger Festplatten

Unterscheidung
- Hardware RAID: Spezieller Kontroller übernimmt Kontrolle → geringe CPU Belastung
- Software RAID: Softwaregesteuerte Datenblockverteilung → günstigere Alternative, integriert in Betriebssystemen

### 1.3.1 RAID0
就是 不同的数据, 会分别存到两个不同的 festplatten 中, 并且相互之间不同步
- RAID 0: Striping
- Blöcke abwechselnd auf Festplatten schreiben
- Benötigt min. 2 Festplatten
- Leserate : x N
- Schreibrate: x N
- Ausfallwahrscheinlichkeit: ~ x N
- Nutzbarer Platz: N
Zusammenfassung: Schnell & Riskant

![](image/Pasted%20image%2020241216014157.png)


### 1.3.2 RAID1
就是 不同的数据, 会分别存到两个不同的 festplatten 中, 并且相互会同步 
- RAID 1: Mirroring
- Spiegelung d. Blöcke auf N Festplatten
- Benötigt min. 2 Festplatten
- Leserate : x N
- Schreibrate: x 1
- Nutzbarer Platz: x 1
Zusammenfassung: Schnelles lesen, hohe Verlässlichkeit (manchmal trügerisch), geringe Kapazität

![](image/Pasted%20image%2020241216014321.png)


### 1.3.3 RAID01 vs. RAID10
RAID0 zielt auf Geschwindigkeit, RAID1 auf Sicherheit. Man würde aber gern beides haben!
Idee: Kombination von RAID0 und RAID1
![](image/Pasted%20image%2020241216014524.png)


### 1.3.4 RAID5

 RAID 5: Striping + Parität 平等,同等;价值相等
头三个 寸不同的数据,  最后一个同步前三个中的数据, 到最后一个 festplatte 中 

Grundidee
- N-1 Platten für Stripes, letzte Platte für Parität
- Benötigt: min. 3 Festplatten
Eigenschaften
- Leserate : x (N 1)
- Schreibrate: schlecht für kleine Dateien, gut für große Dateien
- Ausfallwahrscheinlichkeit: 1 Ausfall kann kompensiert werden
- Nutzbarer Platz: x (N 1)

Zusammenfassung: sparsam, aber Wiederherstellung dauert (zu) lange


### 1.3.5 Prüfsummenberechnung: XOR

Paritätsblocks 中 如何同步 其他三个 festplatz 中的数据 
- Bilden des Paritätsblocks mit logischem XOR: Ap = A1 XOR A2 XOR A3
- Aktualisierung des Paritätsblocks bei jeder Schreibzugriff
    - Physikalisches Schreiben des Datenblocks
    - Neuberechnung Paritätsblock : Hierzu ist Lesen aller anderen Datenblöcke notwendig!
    - Physikalisches Schreiben des neuen Paritätsblocks
- Optimierungen durch Controller
    - Ausnutzung von XOR Eigenschaft: Ap neu Ap alt XOR A1 alt XOR A1 neu
        - Erfordert lediglich das Auslesen von zwei Datenblöcken
        - Vorteil der Optimierung steigt mit Anzahl der Platten
    - Berechnung der Parität aus Datenstrom bei seq. Schreiben
    - Caching von Paritätsblöcken


### 1.3.6 RAID6
RAID 5: Striping + doppelte Parität

- Grundidee
    - N-2 Platten für Stripes, letzte 2 Platten für Parität
    - Benötigt: min. 4 Festplatten
- Eigenschaften
    - Leserate : x (N 2)
    - Schreibrate: schlecht für kleine Dateien, gut für große Dateien; wird besser bei vielen Festplatten und zeitgleichem Zugriff
    - Ausfallwahrscheinlichkeit: 2 Ausfälle können kompensiert werden
    - Nutzbarer Platz: x (N 2)

![](image/Pasted%20image%2020241216020003.png)


### 1.3.7 Probleme von RAID

- Korrelierte Fehler
    - Festplatten haben gleiches Alter + Nutzung + Serie
    - Gefahr, dass auf einmal alle ausfallen
    - Ausfall aufgrund externer Umstände
- Nichtgeschriebene Daten: Daten im Cache des Betriebssystems oder RAID Controllers können bei Stromausfall verloren gehen
- Defekter und nicht mehr zu beschaffender RAID Controller
- Langsame Geschwindigkeit im Recovery Mode
- Viren, Abstürze und Bedienfehler
- RAID ersetzt kein Backup!

# 2 Speichervirtualisierung


# 3 Replikation


# 4 Backup
