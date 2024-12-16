
- Informationen sind wertvoll
    - Allerdings: Wert hängt vom Aufwand des Auffindens ab
        - Aufwand kann Zeitaufwand sein, aber auch Geld oder Tätigkeit
    - Daher: Strukturierung von Datensammlungen
        - Hierarchie von Datensätzen oder sonstwie geordnete Form
        - Ziel: Aufwand für Auffinden von Informationen gering halten
- Strukturierte Datensammlungen bilden Verzeichnisse
    - Beispiele aus dem Alltag: Telefonbuch, Katalog
    - Beispiele aus der IT: Domain Name System (DNS), Datenbanken, LDAP


# 1 Einführung in Verzeichnisdienste


Verzeichnisse: Speichern von Informationen

Verzeichnisdienste:
- Eintragen und Sammeln der Information
- Organisation und Aufbereitung der Information
- Bereitstellung von Informationsdiensten für Abruf
- Zugriff über einfache und standardisierte Wege
Welche Ansätze gibt es? DNS, NIS, LDAP, Active Directory, …


- Zentrale Anlaufstelle für Anfragen im Unternehmen
- Weiterleitung der Anfragen an jeweiliges Quellsystem
- Transparent für den Client
    - Client stellt Anfrage an virtuelles Verzeichnis
    - Virtuelles Verzeichnis leitet Anfrage weiter und antwortet dem Client
    - Einbindung anderer Technologien möglich: Relationale Datenbanken oder Web Services als Datenquelle
- Quell Verzeichnisse können autonom verwaltet bleiben
    - Dezentrale Verwaltung ist politisch oft gewollt
- Nachteil: Geschwindigkeit
    - Jede Anfrage muss weitergeleitet werden


## 1.1 Aktualität und Redundanz
- Viele verschiedene Dienste im Netzwerk vorhanden
- Wie kann Aktualität der Daten sichergestellt werden?
    - Neuer Mitarbeiter muss in vielen Systemen bekannt sein: Mailserver, Webserver, Fileserver, Telefonanlage, …
    - Datenstamm kann sich über die Zeit ändern, z.B. Namensänderung aufgrund einer Hochzeit
- Wo ist Grenze zwischen flexibler Datenhaltung und inkonsistentem Datenstamm?
- Redundanzen: Jeder Dienst hält seine eigene Benutzerdatenbank
- Ansatz: ==Zentrales Verzeichnis, dass Daten für alle Dienste hält==

## 1.2 Vorteil und Nachteil 

- Vorteil von Verzeichnissen: Such Operation ist schnell
    - Häufigste Operation in IdM Systemen
- Nachteil von Verzeichnissen: Schreiben, Ändern der Struktur
    - Schreiben ist langsamer als bei relationalen Datenbanken
    - Änderungen an Struktur sind aufwändig und können Auswirkungen auf andere Daten haben
    - Exakte Planung notwendig, wie eine Organisationsstruktur im Rahmen eines Directory Information Trees (DIT) abgebildet wird



## 1.3 Datenbank oder Verzeichnis

- Hängt von Anforderungen ab
- Transaktionsorientiert vs. operationsorientiert
    - Datenbank: Änderung erst gültig, wenn alle Änderungen der Transaktion erfolgreich getätigt wurden
- Verzeichnis: Nur einzelne Datenänderung ist atomar, Änderungen werden jedoch sequentiell abgearbeitet
- Zugriff auf Daten
    - Datenbank: Optimiert für Lese und Schreib Operationen
- Verzeichnis: ==Daten sind statisch, Lese Operation überwiegt== (1:1000)
- Ist Schreib Operation häufiger, empfiehlt sich die Nutzung von Datenbanken





# 2 LDAP

- Problem Anfang der 1980er Jahre
    - Heterogene Systemlandschaft, verteilte Datenhaltung
    - Problem: Austausch von Daten zwischen Systemen
        - Bei n Systemen: Implementation von n versch. Interfaces
        - Quadratischer Aufwand
- Idee: Einführung einer Austauschschicht
    - Lingua Franca: Bereitstellung nur einer Schnittstelle nötig


## 2.1 Standards

- X.500 Series: Beschreibung, wie Verzeichnisdaten bereitgestellt und abgerufen werden können, ebenfalls Verschlüsselung etc.

- Beispiele
    - X.500 Overview of concepts , models , services
    - X.501 Models
    - X.509 Public Key and Attribute Certificate frameworks
- Zwei Hauptprotokolle
    - DSP (Directory System Protocol): Kommunikation der Server
    - DAP (Directory Access Protocol): Zugriff von Benutzer
- LDAP (Lightweight DAP): Teilmenge von DAP, Aufsetzen auf TCP


## 2.2 Aufbau des Directory Information Tree (DIT)

- DIT kann unterschiedliche Objekttypen beinhalten
- Einträge und Attribute unterliegen Standardisierung
    - Einträge gehören zu Objektklassen
    - Objektklassen schreiben Attribute vor
    - Attribute sind teilweise standardisiert
        - O = Organization
        - OU = Organizational Unit
        - L = Location
        - DESC = Lesbare Beschreibung
        - CN = Common Name

![](image/Pasted%20image%2020241215214044.png)


----

Attribute
- Attribut besteht aus
    - Name: Eindeutige Identifizierung/ Referenzierung innerhalb Objekts
    - Keinem Wert, genau einem Wert oder einer Liste von Werten
- Ziel: Wiederverwendung von Attributen: Definition eines Attributs, Nutzung in mehreren Objekttypen
- Definition von Attribut Typen ( attribute type)
    - Name und/oder Object IDentifier ( OID)
    - Optional: für Menschen lesbare Beschreibung
    - Optional: Definition von Regeln, die für Gleichheit (EQUALITY), Treffer von Teilzeichenketten (SUBSTR) und lexikalische Anordnung (ORDERING) gelten sollen
    - Syntaxbeschreibung: Worum handelt es sich? (Zahl, Zeichenkette, binäres Objekt, ...)
    - Qualifier : Handelt es sich bei Attribut um einfachen Wert (SINGLE VALUE) oder Liste von Werten (COLLECTIVE)


Object Identifier (OID)
- OID = Weltweit eindeutige ID eines Objekts
    - Verteilte Vergabe von IDs ohne Kollision
- Hierarchisches Namensschema (ähnlich DNS)
    - Zentrale Verwaltung der oberen Hierarchieebenen
    - Delegation der Verwaltung von Teilbäumen
    - Registrare können Teilbäume an Organisation zuteilen
    - Beispiel: iso.org.dod.internet.private.enterprise.tu berlin: 1 . 3 . 6 . 1 . 4 . 1 . 10238
- Verwendung von OID ist nicht auf LDAP beschränkt
    - Verwendung in ASN.1 basierten Protokollen wie CORBA, SMNP, ...

## 2.3 Partitionierung und Naming Context

Verzeichness 跟不在多个多个 Server

- X.500 als verteilte Infrastruktur
    - Verzeichnis kann über mehrere Server verteilt sein, Unterteilung in Partitionen nötig
- Master
    - Ein Eintrag im DIT wird von genau einem Server administriert
    - Server wird als Administrative Authority oder Master bezeichnet
- Naming Context
    - Maximaler Teilbaum des DIT, die alle gleichen Master haben
    - Naming Context startet bei einem einzigen Eintrag und spannt sich über alle Elemente, die sich unterhalb dieses Elements im DIT befinden

DIT besteht aus disjunkten Naming Contexts

---
Referral und Continuation
Server 之间怎么相互交流 

推荐（Referral）
Continuation 连续，持续

- Durch Naming Context kann DIT über mehrere Server verteilt werden
- Problem: Wie gelangt man von einem Server zum anderen?
    - Option 1: Server regeln dies intern und transparent
        - Problem: Zusätzliche Last für die Server
        - Problem: Wie sollen Server untereinander kommunizieren?
    - Option 2: Abwälzen auf den Client (Standard seit LDAPv3)
        - Server antwortet Client nicht mit gewünschtem Objekt, sondern mit Referral
- Referral verweist den Client an einen anderen Server
- Continuation Objekt zeigt auf Server, an dem eine Suche fortgesetzt werden soll

## 2.4 Alias

- DIT ist kein echter Baum
    - Baum ist beschränkt auf Verbindung der untergeordneten bene auf ein Element der übergeordneten Ebene
- Alias
    - Regulärer Eintrag im Baum
    - Verweist lediglich auf anderen Eintrag
    - Analogie: Soft Link im Dateisystem
- LDAP verbietet Deadlocks
    - Alias kann nicht angelegt werden, wenn er im Kreis wieder auf sich selbst verweist

![](image/Pasted%20image%2020241215215549.png)


## 2.5 Elemente eines Verzeichnisses 

Verzeichnis Objekt
- Datenelement mit einer bestimmten Menge von Attributen
- Datenattribute können optional oder Pflicht sein
- Repräsentiert ein Objekt im Netzwerk (Benutzer, Rechner, …)

Verzeichnis Schema: Legt generelles Aussehen des Verzeichnisses fest
- Welche Objekte mit welchen Attributen können erfasst werden?
- Welche Struktur hat das Verzeichnis?

Schema legt Verzeichnis Namespace fest
- Fest umgrenzter Bereich mit Objekten, die den gleichen Namenskonventionen folgen

Verzeichnis Baum
- Logische Struktur eines Verzeichnisses lässt sich als Baum darstellen
- Objekte sind in gemeinsamem Namespace
- Objekte sind zusammenhängend
- Baum repräsentiert eine logische Hierarchie zwischen den Elementen

Partitionen
- Aufteilen der Datenbasis auf mehrere verteilten Systeme (z.B. wg. Skalierung)
- Replikation der Datenbasis zwischen verschiedenen Server Systemen
- Kann Dienste von Datenbankservern hierfür nutzen

![](image/Pasted%20image%2020241215220814.png)


## 2.6 Praktische Anwendung: LDAP und Resolver

- Zentrale Aufgabe des Resolvers
    - Beispiel DNS: Auflösung von Hostnamen in IP Adressen
    - Hier: Auflösen von Usernamen/Gruppen in User IDs/Group IDs
        - ![](image/Pasted%20image%2020241215221016.png)
- Authentisierung des Benutzers
    - Erfolgt aus leicht nachvollziehbaren Gründen nicht über Resolver
    - Authentisierung über Plugable Authentication Module (PAM)
        - SSHD verbindet sich über PAM mit LDAP
        - Unterstützung auch durch Apache: libapache auth ldap

--- 

Konfiguration des Resolvers
![](image/Pasted%20image%2020241215221236.png)


## 2.7 Operationen von LDAP (1/2)

- Bind
    - Initiierung einer Client/Server Verbindung
    - Authentisierung
- Search
    - Start einer Suche durch den Client
- Modify
    - Änderung eines Objekts im Directory durch den Client
- Add/Delete
    - Löschen bzw. Hinzufügen von Einträgen durch den Client
- Modify RDN
    - Änderung des letzten Teils des Namens eines Eintrags durch den Client
- Compare
    - Vergleich eines Wertes gegen das Directory
- Abandon
    - Bricht eine Operation des Directories ab, die auf Grund einer Anfrage des Clients durchgeführt wurde
- Unbind
    - Abbau der Verbindung zwischen Client und Server


# 3 Active Directory (AD)

- Verzeichnisdienst von Microsoft
    - Erste Version im Jahr 2000
    - Standard seit Exchange 2000
- Vier Hauptkomponenten
    - LDAP Server
    - DNS Server
    - Kerberos
    - Common Internet File System (CIFS) Protokoll: Erweiterung des Server Message Block (SMB) Protokolls um Datei --/Druckerfreigabe, Windows RPC, NT Domänendienst
- Eigenschaften von AD ergeben sich wesentlich aus den Eigenschaften der vier Hauptkomponenten


## 3.1 Entwicklungsstufe

Entwicklungsstufe 1: Host-Systeme
- Zentraler leistungsstarker Server
    - Direkter Anschluss von Datenbanken und teurer Hardware
    - Zugriff von leistungsschwachen Terminals, im Wesentlichen nur Geräte zur Anzeige
- Mainframe als zentrale Instanz der Infrastruktur
    - Terminals haben selbst keine Intelligenz
    - Zentraler Server übernimmt Authentisierung der Benutzer

![](image/Pasted%20image%2020241215222547.png)

---

2 Entwicklungsstufe 2: Arbeitsgruppen
Arbeitsgruppe: Gruppe von Rechnern unter einem Namen
- Start 1992 mit „Windows 3.1.1 for Workgroups“
- Grundsatz: Alle Rechner dürfen alle Ressourcen gleichberechtigt nutzen
    - Keine weitere Abstufung von Rechten vorgesehen
    - Keine Zugriffsdatenbank

![](image/Pasted%20image%2020241215222646.png)


---

Entwicklungsstufe 3: Arbeitsgruppen NT

Arbeitsgruppen mit steuerbaren Freigaben
- Start 1993 mit „Windows NT 3.1“
- Jeder Rechner führt eigene Benutzerdatenbank
    - Benutzer melden sich an Arbeitsgruppe an
    - Password wird über das Netz zum Zielsystem übertragen
    - Zielsystem prüft Gültigkeit und erlaubt Zugriff
- Kein Domain-Controller!

![](image/Pasted%20image%2020241215222759.png)

---

Entwicklungsstufe 4: Domänen
Idee: Verwaltung durch zentrale Komponente Active Directory
- Administrativer Stab wacht über Einstellungen
- Zentrale Verwaltung aller Benutzer, Rechner, Dienste, …
- Ganzheitliches Management mit nur einem Tool

![](image/Pasted%20image%2020241215223201.png)

## 3.2 Begriff: Domäne

- Schon aus anderen Bereichen bekannt:
    - DNS: Teil des Namensraums, unter einheitlicher Verwaltung
    - LDAP: Namensraum mit einheitlichen Konventionen
- Andere Bedeutung im Kontext von Microsoft
    - ==Domäne = logische Gruppierung von Ressourcen, die die zentrale Verzeichnisdatenbank nutzen==
    - ==Verzeichnisdienst ist nur ein Teil der Domäne: Genutzt zur Speicherung von Informationen==
- Domäne nicht lokal begrenzt
    - Verbindung der Ressourcen einer Domäne über beliebige Netze
    - Verschlüsselung von Datenverbindungen

Vor und Nachteile von Domänen
- Vorteile
    - Einheitliche und nachvollziehbare Verwaltung und damit Umgehung von Inkonsistenzen einer lokalen Konfiguration
    - Realisierung eines netzwerkweiten „Single Sign On“
    - Erhöhter Sicherheitslevel in einer Domäne
        - Einheitliche Vertrauenseinstellungen
        - Zentrale Koordination und Überwachung der Systeme
- Nachteile
    - Deutliche Erhöhung der Komplexität im Netzwerk
        - Anstieg des Administrationsaufwands
        - Intensive Schulung des administrativen Personals notwendig
    - Eingeschränkte Interoperabilität mit Linux, MacOS X, …


## 3.3 Ebenen des Active Directory

Objekt
- Kleinste Organisationseinheit
    - Ein Mitarbeiter, ein Computer, ein Drucker, …
    - Keine weitere Aufteilung des Objekts möglich
- Objekte verfügen über Eigenschaften
    - Eigenschaften werden als Attribute definiert: Name, Telefonnummer, Berechtigungen, …
    - Spezifiziert durch den Objekt Typ des Objekts
- Kategorien
    - Ressource: Drucker, Server, Computer, …
    - Dienst: Email, …
    - Konto: Benutzerkonto, Gruppenkonto, Computerkonto, …


Organisationseinheit (OU)
- Erlaubt die hierarchische Strukturierung
    - OU ist sowohl Container für Objekte als auch für andere Ous: Strukturierung in
    - Ober OUs und Unter OUs
    - Container vererbt seine Eigenschaften
- Möglichkeiten der Strukturierung
    - Logisch: Nach Struktur der Organisation
    - Physikalisch: Nach Netzen, Systemen, Orten, …
- Einheitliche Zuweisung von Gruppen Richtlinien
    - Alle Objekte einer OU fallen unter die gleichen Regelsätze
- Üblich: OU als kleinste Ebene der Administration
    - Beispiel: IT Verantwortlicher für Abteilung

Domäne und Bäume/Strukturen
- Nächste Ebene in der Strukturierung: Domäne
    - Name der Domäne folgt den Regeln des DNS
    - Domänen umfassen größere Teile der Organisation
- Domänen im Active Directory
    - Ein Active Directory umfasst beliebig viele Domänen
    - Domänencontroller regelt Zugriff auf Ressourcen in Domäne
- Strukturen/Bäume ( Trees ) umfassen Domänen
    - Beliebig viele Domänen je Tree
    - Domänen stehen in transitivem Vertrauensverhältnis

Gesamtstruktur/Wald
- Höchste Gliederungsebene: Gesamtstruktur/Wald (Forest)
    - Umfasst sämtliche Elemente eines Active Directories
    - Ein Wald verwaltet einen oder mehrere Bäume: Transitives Vertrauensverhältnis zwischen den Bäumen

![](image/Pasted%20image%2020241215225632.png)



# 4 Domain Name System (DNS und DNSsec ) 


## 4.1 Background 

- Nummern sind schwieriger zu merken als Wörter
    - Vanity Number : „CALL TOM“ besser als „2355 866“
    - Internet: „www.heise.de“ besser als 193.99.144.85
- Unix: Ansprechen eines Rechners über seinen Namen
    - Jedoch: Mapping auf IP Adresse notwendig
- Bis 1985: / etc hosts
    - Zentrale Stelle im Internet pflegte die hosts Datei
    - Datei existiert bis heute, wird aber nur für lokale Knoten genutzt
    - Jedes System zog täglich ein Update per FTP
    - Probleme: Skalierbarkeit, Aktualität


Neuer Ansatz: Der Name Server
- Applikation kontaktiert den Name Server über einen sog. Resolver
    - Läuft auf dem gleichen System
- Resolver fragt Name Server
    - Anfrage nach IP Adresse für den von Applikation übermittelten Host Namen
- Name Server antwortet mit IP Adresse, die der Resolver an die Applikation weiter reicht
    - Auch Reverse Lookup möglich, also Anfrage „IP nach Name“

![](image/Pasted%20image%2020241215233410.png)


## 4.2 DNS Grundlagen

Eigenschaften von DNS
- Hierarchischer Aufbau, genannt Domain Namespace
- Eine Partei kann Kontrolle über einen Teilbaum erhalten
    - Autonomes Einfügen zusätzlicher Schichten innerhalb des Teilbaums
    - Z.B. dos.Tu Berlin.de
- Knotennamen sind von tatsächlichem Ort unabhängig
    - Rechner bkits.dos.tu berlin.de muss nicht in Berlin stehen
- Üblich: DNS Einträge folgen IP Bereichen
    - Alle IP Adressen in 130.149/16 gehören tu berlin.de


Hierarchischer Aufbau
- Aufbau von DNS ist streng hierarchisch
    - Baumstruktur
    - Jeder Knoten ist DNS Name (FQDN: Fully Qualified Domain Name)
    - Durch Knoten aufgespannter Teilbaum heißt Domäne
- Länder Domänen als Kinder an Wurzel, verwaltet durch ICANN
- NICs verwalten Top Level Domains (TLDs)
- Delegation im Teilbaum
![](image/Pasted%20image%2020241215234211.png)

---

Hierarchie von Name Servern
- Auch die DNS Auflösung ist streng hierarchisch gegliedert
- Jeder Server ist verantwortlich für zusammenhängenden DNS Bereich
    - Server ist „autoritative Server“
    - Teilbaum wird „DNS Zone“ genannt
    - Zone ist Teil des Teilbaums
    - Zone ist Einheit der Delegation von Verantwortlichkeiten im DNS
- Autoritative Server beantwortet DNS Anfragen zu Knoten in seiner Zone

![](image/Pasted%20image%2020241215234313.png)

---

Root Nameserver im DNS

Root Nameserver kontrollieren Top Level Domains, neue TDLs müssen von diesen geroutet werden
123 Server, viele davon jedoch geographisch gespiegelt

![](image/Pasted%20image%2020241215234635.png)

## 4.3 DNS Anfrage
### 4.3.1 Hierarchische (rekursive) Namensauflösung

- Applikation nutzt lokalen Resolver für DNS Auflösung
- Resolver wendet sich an den konfigurierten Nameserver, hier TUB Nameserver
- Nameserver prüft: „bin ich der authoritative NS für Anfrage?“
    - Ja: Direkte Beantwortung
    - Nein: Weiterleitung zu anderem NS, anschließend Weiterleitung der erhaltenen Antwort

![](image/Pasted%20image%2020241215234744.png)


### 4.3.2 DNS Anfragetypen

- Resolver nutzt Nameserver
    - Sendet Anfragen
    - Erhält Antworten
- Anfragetyp: hierarchisch (rekursiv)
    - Nameserver leitet Anfrage ggf. an andere Nameserver weiter
- Anfragetyp: nicht hierarchisch (iterativ)
    - Nameserver antwortet Resolver ggf. mit Adresse eines anderen Nameservers
    - Resolver kann sich dann selbst an diesen Nameserver wenden

---

Hierarchische Anfragen (recursive request) 
- Lokaler Resolver stellt eine hierarchische Anfrage an den Name Server
- Name Server kann Anfrage selbst nicht beantworten, startet daher an der Wurzel die Auflösung der Adresse
    - Rekursives vorgehen, bis der „ Authoritative Nameserver“ der angefragten Adresse erreicht ist
- Rückgabe von IP an Resolver

![](image/Pasted%20image%2020241215235133.png)



Iterative Anfragen (non recursive request
- Lokaler Resolver stellt eine iterative Anfrage an den Name Server
- Name Server kann Anfrage nicht selbst beantworten und antwortet mit Adresse des root Name Servers
- Iterative Anfrage aller Name Server, bis Authoritative NS“ für Hostnamen erreicht ist
![](image/Pasted%20image%2020241215235229.png)

### 4.3.3 Caching von DNS Anfragen

- Hierarchische Anfragen sind Standard
    - Server leitet ggf. Anfrage weiter
    - Server antwortet mit IP
- Hohe Anfragelast, speziell am root Knoten
    - Muss Adressen der TLD Name Server bekannt geben
- Caching im Name Server hilft Last zu senken
    - Name Server merkt sich Antworten zu Anfragen
    - Bei neuer Anfrage wird ohne Rückfrage direkt geantwortet
        - ==Antwort ist als „non authoritative “ gekennzeichnet==
    - Timeout für Gültigkeit gecachter Antworten, dann neue Anfrage
    - Problem: Caching verzögert die Propagierung neuer Werte
        - Beispiel: IP Adresse eines Hosts kann sich ändern


Summary of DNS Cache Locations
When a DNS query is made, the resolved DNS record is cached at several levels in the DNS resolution process. This caching improves performance by reducing latency and the load on DNS servers. Here’s where DNS records can be cached:
1. **Client Device**: Browser cache, OS-level cache.
2. **Local DNS Resolver**: Provided by the ISP or custom-configured (e.g., Google or Cloudflare DNS).
3. **Recursive Resolver**: Intermediate resolvers along the query path.
4. **Authoritative DNS Server**: Occasionally caches data internally for performance optimization.

Caching at multiple levels helps reduce DNS resolution time, improves user experience, and decreases load on the overall DNS infrastructure.


## 4.4 DNS Resource Records

- Häufigste Nutzung von DNS:
    - Auflösung von Hostname in IP Adresse
- Aber auch andere Nutzungen
    - Welchen Nameserver muss ich kontaktieren?
    - An welchen Mailserver kann ich per SMTP EMail ausliefern?
- Zahlreiche DNS „ Resource Record “ Typen
    - A = Host Address
    - NS = Authoritative Name Server
    - MX = Mail Exchanger Record
    - RP = Responsible person

## 4.5 Abfrage von DNS

- Üblich: Auflösung von Hostnamen durch Applikation
- Auch manuelle Name Server Anfragen möglich, beispielsweise mit dem Tool nslookup

Beispiel: Ausgabe des MX Records des NDR:
Bemerke: Antwort ist „non authoritative
![](image/Pasted%20image%2020241216000927.png)


Beispiel: Ermittlung des authorative Name Servers des NDR
![](image/Pasted%20image%2020241216001143.png)

Erneute Anfrage nach MX Record : Diese Anfrage wird direkt an Name Server des NDR gerichtet
Bemerke: Antwort ist diesmal authorative
![](image/Pasted%20image%2020241216001506.png)


### 4.5.1 Gewichtete Antworten

每个 Records 有权重, 最小权重的 最先被联系
多个 Record , 如果primärer Mailserver挂了, 有替代的可以顶上 

- Manche Records liefern mehrere gewichtete Antworten zu einer Anfrage
    - Im Beispiel: 3 MX Einträge für die Domäne ndr.de
- Zweck: Ausfallschutz
    - ==Zuerst: Kontaktiere Host mit kleinstem Gewicht==
    - Wenn Host nicht erreichbar: Kontaktiere nächsten Host
    - usw.
- Sinnvoll für MX Records
    - Mail kann zugestellt werden, auch wenn primärer Mailserver der Domäne vorübergehend nicht online ist

![](image/Pasted%20image%2020241216002238.png)


## 4.6 Sicherheitsaspekte

- DNS ist sehr anfällig für Angriffe
    - Kommunikation basiert auf UDP (verbindungslos)
        - Keine Überprüfung des Absenders
        - Absenderadresse der Antwort kann beliebig sein
    - Keinerlei Signierung oder Verschlüsselung
        - Einfach durchführbare Man in the middle Attacken
        - Umleitung des Browsers auf präparierten Server
    - Relaying aller Mails über eigenen Mailserver

Beispiel: Aufruf von www.berliner sparkasse.de
- Falsche DNS Antwort leitet auf falschen Webserver
- Bankkunde bemerkt lediglich Zertifikatsfehler oder fehlende Verschlüsselung der Verbindung
    - Normalnutzer bemerkt/versteht diese Probleme kaum


## 4.7 DNSsec
- Handlungsbedarf wurde schon 1997 erkannt
    - Definition: Domain Name System Security Extensions ( DNSsec )
    - Standardisiert in RFC 4034
    - Problem: Protokoll mit viel Kommunikation, nur anwendbar in kleinen Netzen
- Überarbeitete Version: DNSsec bis
    - Standardisiert: 2005
    - Idee: Signatur der Antwortpakete
        - Anbieter signiert DNS Aussagen zu seiner Zone
    - ==Antwort erhält DNS Information, Signatur und Zertifikat==
        - Übertragung der Informationen als zusätzl . Resource Records
        - ==Empfänger kann Gültigkeit der Antwort überprüfen==
    - Wichtig: Vollständig abwärtskompatibel zu herkömmlichem DNS

Überprüfung der Signatur
- Überprüfung des KEY Records
    - KEY Record des Unterzeichners des SIG Records anfragen
- Überprüfung des SIG Records
    - SIG Record anfragen, der zum KEY Record aus Schritt 1 gehört
- Berechnung des MD5 Hashes aus Schritt 1
- Ermittlung der Signatur aus Schritt 2
- Wenn beide Werte überein stimmen, Antwort akzeptieren
- Wichtig: Es werden keine Teilnehmer authentifiziert, sondern nur die Gültigkeit von Antworten überprüft
- Authentifizierung erfolgt über Chain of Trust

Verschlüsselung/Signierung in DNSsec
- Möglichkeit 1: Symmetrisch ist offensichtlich unpraktikabel
- Möglichkeit 2: Asymmetrisch
    - Aufbau einer Chain of Trust
    - Zentrale Instanz: ICANN
    - Zertifizierung nationaler Stellen ( DeNIC) 
    - Zertifizierung der jeweiligen Domänen Inhaber
- Öffentlicher Key kann in DNS Anfragen mitgeteilt werden
- Privater Schlüssel muss sicher verwahrt werden
    - ICANN: Nur 7 Personen mit Zugang zu Signier Hardware, 5 Personen müssen bei Erzeugung des Keys anwesend sein
    - DeNIC : Geschützter Rechner holt alle 2 Stunden zentrale Hostliste, signiert diese und spielt sie danach zurück

Sicherheit vs. Aufwand
![](image/Pasted%20image%2020241216003558.png)



Teilung der Schlüssel: KSK - ZSK
- Lösung: Nutzung unterschiedlicher Arten Schlüssel
- Typ 1: Key Signing Key (KSK)
    - Langer (und sicherer) Schlüssel mit langer Lebenszeit
- Typ 2: Zone Signing Key (ZSK)
    - Kurze (und potentiell unsichere) Schlüssel mit kurzer Lebenszeit
- Einbettung in die Chain of Trust
    - Zentrale Stelle signiert KSK des Providers → Authentizität des KSK wird geprüft
    - Provider signiert ZSK mit eigenem KSK → Auch die Authentizität des ZSK wird geprüft


Wechsel des Zone Signing Keys
- Üblich: Mehr als ein ZSK pro Provider
    - Überlappende Zeiträume für Gültigkeit: Wichtig für Timeout Problematik gecachter Anfragen bei einem Wechsel des ZSK
    - ![](image/Pasted%20image%2020241216003838.png)
- Doppelte Signatur während Übergangszeit
    - Nameserver cachen Antwort mit neuem und altem ZSK
    - Solang alter ZSK gültig, werden beide Antworten aus Cache akzept
    - Nach Ablauf der Gültigkeit von ZSKn wird Antwort ZSK(n+1) akzeptiert