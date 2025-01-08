
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

> RAID = Redundant Array of Independent Disks

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


# 2 Block storage 

**Block storage** is a storage architecture where ==data is stored and managed in fixed-sized blocks.== Each block acts as an individual storage unit and is identified by a unique address. Block storage is commonly used for high-performance workloads, such as databases, virtual machines, and transactional systems.

Key Features of Block Storage
1. **Block-Level Storage**:
    - Data is split into fixed-size blocks (e.g., 512 bytes or 4 KB).
    - Each block is treated as an independent unit, allowing precise data access.
2. **Abstraction**:
    - Blocks are presented as raw storage to the operating system.
    - File systems or databases organize the raw blocks into structured formats.
3. **High Performance**:
    - Designed for low-latency, high-throughput workloads.
    - Suitable for random read/write operations.
4. **Flexibility**:
    - Blocks can be allocated and managed independently.
    - They can be easily resized or reallocated across storage devices


Advantages of Block Storage
1. **High Performance**:
    - Direct, low-latency access to storage blocks.
    - Ideal for IOPS (Input/Output Operations Per Second)-intensive workloads.
2. **Scalability**:
    - Can scale easily by adding more storage volumes or increasing their size.
3. **Versatility**:
    - Supports multiple workloads, from simple file systems to complex databases.
4. **Fine-Grained Control**:
    - Precise management of storage blocks for efficient resource utilization.


### 2.1.1 **How Block Storage Works**

1. **Data Splitting**:
    - Files or data streams are split into smaller, fixed-size blocks.
    - Each block is assigned a unique address.
2. **Raw Storage Interface**:
    - The operating system interacts with the storage device as if it were a raw, contiguous storage medium.
    - Applications or file systems determine how data is organized and accessed.
3. **Protocols**:
    - Block storage is commonly accessed via protocols such as:
        - **SCSI (Small Computer System Interface)**.
        - **iSCSI (SCSI over IP)**.
        - **Fibre Channel**.
        - **NVMe over Fabrics (NVMe-oF)** for high-performance SSD storage.


Block Storage in Modern Environments
1. **On-Premises Solutions**:
    - **SAN (Storage Area Network)**: A high-speed network providing access to block storage arrays.
    - Enterprise storage solutions from vendors like Dell EMC, NetApp, and HPE.
2. **Cloud Solutions**:
    - **AWS Elastic Block Store (EBS)**: Persistent block storage for EC2 instances.
    - **Azure Managed Disks**: Scalable block storage for virtual machines.
    - **Google Persistent Disks**: Block storage for Google Cloud VMs.
3. **NVMe-Based Storage**:
    - High-performance NVMe drives offer block storage with minimal latency, especially in high-performance computing (HPC) and AI/ML workloads.

## 2.2 Comparison: Block vs. File vs. Object Storage

| **Feature**         | **Block Storage**             | **File Storage**               | **Object Storage**            |
| ------------------- | ----------------------------- | ------------------------------ | ----------------------------- |
| **Data Access**     | Block-level                   | File-level                     | Object-level                  |
| **Performance**     | High                          | Moderate                       | Moderate                      |
| **Use Case**        | Databases, VMs, backups       | File sharing, home directories | Archiving, analytics, backups |
| **Scalability**     | Scalable but with limitations | Limited by file systems        | Highly scalable               |
| **Example Systems** | SAN, EBS, Azure Disks         | NAS, SMB, NFS                  | AWS S3, Azure Blob Storage    |


## 2.3 **Block Storage vs. File Storage: Key Differences**

Block storage and file storage are two fundamental storage architectures, each with distinct features and use cases. Here's how they differ:

| **Aspect**              | **Block Storage**                                                                             | **File Storage**                                                               |
| ----------------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Data Storage Format** | Stores data in fixed-sized blocks, each with a unique address.                                | Stores data in a hierarchical file-and-folder structure.                       |
| **Access Method**       | Block-level access: Raw storage units accessed directly by applications or operating systems. | File-level access: Files are accessed through protocols like SMB, NFS, or FTP. |
| **Performance**         | High performance with low latency, ideal for IOPS-intensive workloads.                        | Moderate performance, suitable for general-purpose file sharing.               |
| **Management**          | Requires additional software (e.g., file systems or databases) to organize and manage data.   | Native file system handles data organization, making it easier to manage.      |
| **Scalability**         | Scalable, but often requires specialized tools for managing large-scale deployments.          | Limited by the scalability of the underlying file system.                      |
| **Redundancy**          | Offers advanced redundancy features like RAID, snapshots, and replication.                    | Typically uses simpler redundancy features at the file level.                  |
| **Use Cases**           | Databases, virtual machines, email servers, and transactional systems.                        | Shared file storage, collaboration tools, and user home directories.           |
| **Protocols**           | SCSI, iSCSI, Fibre Channel, NVMe, etc.                                                        | SMB (CIFS), NFS, FTP, HTTP, etc.                                               |
| **Cost**                | Higher cost due to infrastructure complexity and performance optimization.                    | More cost-effective for general-purpose storage.                               |
| **Examples**            | AWS EBS, Azure Managed Disks, Google Persistent Disks.                                        | AWS Elastic File System (EFS), Azure Files, Google Filestore.                  |

### 2.3.1 **Detailed Comparison**

1. **Data Organization**:
    - **Block Storage**: Provides raw storage blocks without any inherent structure. Applications or file systems determine the organization of data.
    - **File Storage**: Organizes data in a hierarchical directory structure, making it user-friendly for storing and retrieving files.
2. **Performance**:
    - **Block Storage**: Optimized for speed and can handle high-performance workloads such as databases or virtual machine storage.
    - **File Storage**: Best for workloads where ease of use and compatibility matter more than raw performance, such as shared drives.
3. **Access**:
    - **Block Storage**: Accessed via operating systems as a virtual disk or logical volume.
    - **File Storage**: Accessed by users or applications using standard file protocols.
4. **Ease of Use**:
    - **Block Storage**: Requires expertise to set up and manage, as raw storage needs to be formatted and configured.
    - **File Storage**: Easier to use, as it is plug-and-play for most users.


When to Use Block Storage vs. File Storage

| **Use Case**                       | **Preferred Storage Type** |
| ---------------------------------- | -------------------------- |
| **High-performance databases**     | Block Storage              |
| **Virtual machines (VMs)**         | Block Storage              |
| **Transactional systems**          | Block Storage              |
| **File sharing and collaboration** | File Storage               |
| **Backup and archiving**           | File Storage               |
| **Web hosting**                    | File Storage               |


# 3 Speichervirtualisierung

- Storage = Grundlegender Dienst für die Infrastruktur
    - Speicher für alle Dienste / Server
    - Zuverlässiger Weg für Austausch von Daten zwischen Diensten
    - Ausfallsicherheit durch Redundanz
- ==Virtualisierung von Speicher==
    - Trennung der logischen Gruppierung von Blöcken von dem konkreten Speicherort
    - Dynamische Zuweisung von Blöcken an Platten (je nach Kapazität, Geschwindigkeit, Priorität, Ort, …)
    - Einheitliches Management aller Speicherkapazitäten


Speichernetze
- Grundelemente
    - Schnelle Verbindungsnetze, niedrige Latenz, hoher Durchsatz
    - Virtualisierungsserver : Entscheidung, wo welche Daten tatsächlich gespeichert werden
- Multipath für die Ausfallsicherheit
    - Mehrere unabhängige Pfade zwischen Servern und Festplatten
    - Server aufgerüstet für SAN Zugriff
    - Kostentreiber

![](image/Pasted%20image%2020241216141708.png)


Policies
Transparente Zuordnung von
- Servern zu Platten
- Platten zu Größen
- Platten zu Redundanzen,

![](image/Pasted%20image%2020241216141951.png)

## 3.1 SAN (Storage Area Network) (Block-level access)

> A Storage Area Network (SAN) is a high-speed, dedicated network that connects servers to shared pools of storage devices. It provides block-level data access and is primarily used in enterprise environments for centralized data storage and efficient management of large amounts of data.


Key Features of SAN:
1. **Centralized Storage**:
    - ==SAN allows multiple servers to access shared storage resources.==
    - Ideal for applications requiring high availability and redundancy.
2. **High Performance**:
    - Offers low-latency, high-speed data transfer using technologies like Fibre Channel or iSCSI (Internet Small Computer Systems Interface).
3. **Scalability**:
    - Storage capacity can be increased without affecting server performance or downtime.
4. **Redundancy and Reliability**:
    - Built with fault-tolerant features, such as RAID configurations and multiple data paths.

Benefits of SAN
- **Efficient Storage Utilization**: Centralized storage pools reduce wasted space.
- **High Availability**: Redundant configurations ensure continuous operation.
- **Improved Data Management**: Centralized backups and disaster recovery solutions.

### 3.1.1 **SAN vs. NAS (Network-Attached Storage)**:

| **Aspect**       | **SAN**                                 | **NAS**                                  |
| ---------------- | --------------------------------------- | ---------------------------------------- |
| **Access Level** | Block-level access                      | File-level access                        |
| **Performance**  | High (optimized for databases, VMs)     | Moderate (file sharing, general storage) |
| **Protocols**    | Fibre Channel, iSCSI, FCoE              | SMB, NFS, FTP                            |
| **Use Case**     | Enterprise applications, databases, VMs | File storage and sharing                 |


### 3.1.2 Aufbau eines SANs

![](image/Pasted%20image%2020241216142024.png)

SAN Components
1. **Storage Devices**:
    - Arrays of hard drives, SSDs, or hybrid solutions configured to store data.
2. **Host Bus Adapters (HBAs)**:
    - Installed on servers, enabling communication with SAN devices.
3. **Switches**:
    - Fibre Channel or Ethernet switches facilitate the interconnection between servers and storage.
4. **Protocols**:
    - Common SAN protocols include:
        - **Fibre Channel**: High-speed connectivity (up to 128 Gbps).
        - **iSCSI**: SAN over existing IP networks.
        - **Fibre Channel over Ethernet (FCoE)**: Combines Ethernet and Fibre Channel.


SAN Visual Representation
A typical SAN setup might include:
1. Servers () connected to switches via Fibre Channel or iSCSI.
2. Switches connecting to shared storage arrays.
3. Optional management interfaces for monitoring and configuration.


### 3.1.3 Plattenvirtualisierung

- Erste Stufe : schnelles Netzwerk für Verbindung , aber Server wissen genau , wo die Daten liegen -> Dienstunterbrechung bei Arbeiten am SAN
- Virtualisierung der Festplatten , so dass Remapping im laufenden Betrieb möglich ist Aufgabe des Volume Controllers
- Server nehmen lediglich virtuelle Festplatten wahr , die vom SAN Controller verwaltet / erstellt werden

![](image/Pasted%20image%2020241216142932.png)

![](image/Pasted%20image%2020241216143017.png)

![](image/Pasted%20image%2020241216143104.png)


## 3.2 Virtualisierung auf Dateiebene

### 3.2.1 File system virtualisation 

- Virtuelles Dateisystem abstrahiert das tatsächlich verwendete Dateisystem für die Anwendung
- Kein Unterschied zwischen lokalen und entfernten Zugriffen
- Virtuelles Dateisystem zerlegt die Dateien in Chunks und speichert diese so, dass maximale Effizienz erreicht wird

![](image/Pasted%20image%2020241216143246.png)




### 3.2.2 Block device Virtualisation

Block device virtualization abstracts physical storage devices into virtualized block devices. This allows operating systems and applications to interact with storage as if it were a continuous sequence of blocks, regardless of the underlying physical structure. It is widely used in modern storage systems to enhance flexibility, scalability, and efficiency.

- Blockvirtualisierung : analoge Vorgehensweise zu virtuellem Speicher 
    - Logischer Speicher für den Benutzer
    - Virtualisierungssoftware übersetzt angefragten Block in tatsächliche / aktuelle Position im Speicher
- Komponenten 
    - LUNs ( logical unit identifier) 
    - Offset LBA ( logical block address ) innerhalb der LUN

![](image/Pasted%20image%2020241216143423.png)


---

Key Concepts of Block Device Virtualization
1. **Abstraction Layer**:    
    - Block device virtualization hides the complexity of physical storage hardware (e.g., HDDs, SSDs, or storage arrays) and presents logical volumes to the operating system.
    - These logical volumes can be dynamically allocated, resized, or moved without affecting the applications.
2. **Logical Volumes**:
    - Virtual block devices are created using logical volume managers (e.g., Linux LVM) or storage virtualization solutions.
    - Logical volumes can span multiple physical disks or be subdivided from a single disk.
3. **Flexibility**:
    - Virtual block devices can be used for applications such as databases, virtual machines, or file systems.

---

How Block Device Virtualization Works
1. **Virtual Layer**:
    - A virtualization layer sits between the physical storage hardware and the OS.
    - This layer maps logical block addresses (LBAs) used by the OS to physical block addresses on the storage hardware.
2. **Storage Pooling**:
    - Physical disks are aggregated into a pool of storage.
    - Logical volumes are carved out of this pool based on application needs.
3. **Dynamic Allocation**:
    - Storage capacity can be allocated or expanded on demand.
    - Thin provisioning ensures efficient use of physical storage by allocating only the space actually used.


---

Implementations of Block Device Virtualization

1. **Logical Volume Managers (LVMs)**:    
    - Tools like Linux LVM manage logical volumes on top of physical storage devices.
    - Features include resizing, snapshots, and striping across multiple disks.
2. **Virtual Machine Storage**:
    - Virtualized environments (e.g., VMware, Hyper-V, KVM) use virtual block devices for VM disk files.
    - These devices abstract physical storage into VM-accessible virtual disks.
3. **Storage Area Networks (SANs)**:
    - SAN solutions provide block-level virtualization across networked storage.
4. **Cloud Storage**:
    - Cloud providers (e.g., AWS EBS, Azure Disks) offer virtual block devices for cloud VMs.


## 3.3 Wo kann man virtualisieren

- Host based
    - Implementierung als Software auf den Anwendungsservern
- Network based
    - Aktive Netzwerkelemente übernehmen die Virtualisierung
- Storage based
    - Realisierung auf dem Speichersystem direkt

![](image/Pasted%20image%2020241216150830.png)


### 3.3.1 Host based Virtualisierung

- 适合  File-based storage system
- Systemsoftware auf dem Anwendungsserver verwaltet die Metadaten und stellt die Daten transparent für die Anwendung zur Verfügung
    - Dateilevel: ermitteln der Position der Datei auf den Platten und laden der Daten
    - Blocklevel: Ermittlung der Blöcke und deren Position, Zusammenführung der Blöcke zur Datei und transparente Bereitstellung
- Gute Integration mit Software RAIDs
- Beispiel: NFS (File-based storage system)

![](image/Pasted%20image%2020241216151329.png)

### 3.3.2 Network based Virtualisierung

- ==Für Blocklevel Virtualisierung gut geeignet, Dateilevel sehr selten==
- Große Infrastruktur erforderlich
    - Anmeldedienst
    - Nameserver
    - Updatebenachrichtigung
    - Network address management
    - Zoning
- Fabric switches Appliances für
    - Konnektivität für alle Speicheraktionen
    - Interoperabilität zwischen heterogenen Elementen
- Vorteil: effizient, keine Änderung an Servern erforderlich, Multi Path
- Nachteil: hochgradige Abhängigkeit vom Hersteller
- Beispiel: SVC (Storage Volume Controller ) von IBM (in tubIT)

---


In-band vs. Out-band Virtualisierung

Wo werden die Storage Volume Controller (SVC, Virtualisierungsserver) mit den Metadaten positioniert?

![](image/Pasted%20image%2020241216151851.png)

---

In-band Virtualizierung
- Symmetrische Virtualisierung
    - Virtualisierungsserver zwischen Server und Speicherracks führt die gesamte Kommunikation durch
- Virtualisierungsserver
    - Kontrolliert Zugang zum Speicher (wer darf worauf zugreifen?) 
    - Bestimmt die Speicherallokation
    - Steuert die Speicherracks
- Vorteil
    - Einfach Implementierbar
- Nachteil
    - Möglicher Flaschenhals
    - Skalierungsproblem

![](image/Pasted%20image%2020241216152009.png)


--- 


Out of band Virtualisierung
- Asymmetrische Virtualisierung
    - Trennung der Metadaten ( Virtualisierungsserver ) von Datenübertragung (direkt zwischen Server und Speicherarrays) → zusätzliche Software notwendig
    - Virtualisierungsserver  ( Storage Volume Controller ) 
        - kontrolliert Zugang zum Speicher
        - Speicher sendet Infos über alle Daten an VS
        - Server fragen beim VS nach Daten
        - VS beauftragt das direkte versenden der Daten
    - Vorteile
        - Bessere Skalierbarkeit, hoher Durchsatz
    - Nachteil
        - Aufwendige Implementierung

![](image/Pasted%20image%2020241216154056.png)


### 3.3.3 Storage based Virtualisierung


Storage-based virtualization is a technique that abstracts and consolidates multiple physical storage resources into a unified, virtualized storage pool. This approach simplifies management, improves resource utilization, and enhances scalability in modern IT environments.

Virtualisierung eingebettet im Speicherkontroller

Vorteile
Effiziente Implementierung

Nachteil
Homogene Speicherumgebung erforderlich


![](image/Pasted%20image%2020241216152819.png)


Eigenschaft von SAN Struktur

Verfügbarkeit
- Vollständig Redundant (kein Single Point of Failure ), alle Komponenten in sich redundant
    - Alle Geräte auf mind. 2 verschiedenen Wegen angebunden
    - Alle Daten gespiegelt in zweitem Standort
    - Ausfallsicherheit durch RAID Verbünde und Global Hot Spare
- Flexibilität
    - dynamische Zuteilung der Ressourcen durch Virtualisierung
    - Erweiterbar mit Disksystemen verschiedener Hersteller
- Sicherheit
    - Abschottung der Einzelsysteme durch SAN Zoning
    - Dedizierte Zuordnung VDisks zu Hosts
    - Beschränkter Zugang zu Management und SAN System



# 4 Replikation

Drei wichtigste Verfahrensklassen für die Aktualisierung und Synchronisation auf replizierten Datenbanken
- Write All Ansatz: Synchrone Aktualisierung aller Kopien
- Primary Copy Verfahren: Sofortige Aktualisierung einer Masterkopie, die Veränderungen werden mit etwas Verspätung weitergegeben
- Voting Verfahren: Jede der Repliken bekommt eine oder mehrere Stimmen zugeordnet. Bei jedem Schreib oder Lesezugriff muss die Mehrheit dieser Stimmen durch Sperren der entsprechenden Objekte gewonnen werden


![](image/Pasted%20image%2020241216154138.png)




## 4.1 Write All Read Any (ROWA) Strategy

Varianten des Write All Ansatzes

Bekanntestes Variante: Write All Read Any oder auch Read Once Write All (ROWA) Strategie genannt
- Write All Read Any oder auch Read Once Write All (ROWA) 是同一个 Strategie 
- Synchrone Änderung aller Replikate vor Abschluss der Änderungstransaktion
- Jedes Replikat ist auf dem aktuellen Stand und kann für Lesezugriffe verwendet werden
- Auswahl beim Lesen erfolgt basierend auf minimaler Kommunikation oder Auslastung der einzelnen Knoten
- Einzelne Knotenausfälle können beim Lesen problemlos kompensiert werden

The Write All Read Any (ROWA) strategy is a consistency mechanism often used in distributed systems to ensure data consistency across multiple replicas. It is also referred to as Read Once Write All.

How ROWA Works
1. **Write Operation**:
    - Any update or write operation is propagated to **all replicas** in the distributed system.
    - Every replica receives and stores the same data, ensuring all replicas are synchronized.
2. **Read Operation**:
    - A read operation can be performed on **any one replica**, as all replicas are assumed to have the same data.
    - This allows for low-latency reads and load balancing since multiple replicas are available to handle read requests.

Key Features
- **Strong Consistency**:
    - Ensures that all replicas have the latest version of the data after a write operation.
    - No stale or outdated data is returned during reads, as all writes are completed before reads.
- **High Read Availability**:
    - Reads can be served by any replica, making the system highly available for read-heavy workloads.
- **Synchronous Writes**:
    - Write operations are typically synchronous to ensure all replicas are updated before the write is acknowledged.


Nachteile der ROWA Strategie
- Änderung der Sperrprotokolle erforderlich
    - Vor dem Zugriff muss eine Schreibsperre auf allen replizierten Datensätzen auf unterschiedlichen Knoten erworben werden
    - Alle Knoten müssen an dem Commit Protokoll beteiligt werden
- Nachteil: Ausfallsicherheit schlechter als bei nicht redundanten Datenbanken
    - Fällt nur ein der replizierten Knoten aus, so ist die gesamte Datenbank nicht mehr funktionsfähig, da alle Knoten mit Replikaten berücksichtigt werden müssen
- Abgeschwächte Forderung Write All Available Read Any
    - Die replizierten Datensätze werden nur auf den verfügbaren Rechnern aktualisiert
    - Bei ausgefallenen Rechnern werden alle Aktualisierungen protokolliert und beim nächsten Start nachgeholt
    - Probleme, wenn sich die Knoten in unterschiedlichen Netzwerkpartitionen befinden


## 4.2 Primary Copy Verfahren


- Effiziente Bearbeitung von Änderungen, indem nur ein Replikat so genannte Primärkopie sofort aktualisiert wird
- Die anderen Kopien werden asynchron vom Primary Copy Rechner aus und sobald möglich ( as soon as possible ) aktualisiert
- Effizienzvorteil
    - Aktualisierungsnachrichten werden gebündelt und zusammen zum Zielknoten übertragen
    - Primärkopien auf unterschiedlichen Knoten zur Vermeidung von Engpässen
- Nachteil: Verzögerte Anpassung der Replikate
- Realisierung des Primary Copy Protokolls
    - Anforderung von Schreibsperren für alle Replikate (wie bei ROWA) 还是先一口气将所有 Replikate 锁住, allerdings wird nur die primäre Kopie synchron aktualisiert
    - Die Sperre geht in Besitz des Primary Copy Rechners über, der dann nach und nach die Replikate aktualisiert und erst nach Abschluss alle Sperren wieder frei gibt


Alternativen zum Primary Copy Protokoll
- Schreibsperren werden nur noch zentral auf der Primärkopie angefordert ->  Anzahl von Sperrkonflikte wird reduziert
- Behandlung der Lesezugriffe wegen eventuell veralteter, inkonsistenter Daten erforderlich
    - Lesezugriff auf Primärkopie: Alle Lesetransaktion referenzieren nur die primäre Kopie keine Lokalität/Parallelität, lediglich Ausfallsicherheit
    - Sperranforderung beim Primary Copy Rechner, Lesezugriff auf lokale Kopien: Beim Sperren wird überprüft, ob das Datum aktualisiert werden muss und ggf. die Aktualisierung bevorzugt behandelt → Entlastung des Primary Copy Rechners
    - Lokale Abwicklung von Lesezugriffen: Inkonsistenzen in praktischen Anwendungen von wenigen Sekunden werden in Kauf genommen und auf die Daten ohne vorherige Überprüfung der Primärkopie zugegriffen

Beispiel zu Primary Copy Verfahren
- Primärkopien der Kontodatensätze liegen auf Filialrechnern (R1 hält Primärkopie von A und R3 von B)
- Sämtliche Lese --/Schreibzugriffe lokal durchgeführt, Kommunikation nur bei Zugriffen der Zentrale (R2), d.h. Sperren und Aktualisieren der Primärkopien
    - Strategie 1 verlangt Sperren und Lesen der Primärkopie  -> Nachrichten für Objektzugriff und Commit
    - Strategie 2 erfordert Nachrichten für Sperranforderung und freigabe
    - Strategie 3 ist z.B. für globale Auswertungen ausreichend
- Was passiert, wenn der Knoten mit der Primärkopie ausfällt?
    - Primärkopie nicht erreichbar ->  keine Transaktion wird ausgeführt, wenn sie diese Daten benötigt
    - Ausweg: Wahlalgorithmen zur Bestimmung einer neuen Primärkopie
    - Voraussetzung: alle Repliken auf dem aktuellen Stand bzw. können aktualisiert werden


## 4.3 Voting-Verfahren

Vor jedem Zugriff auf ein repliziertes Datum müssen ausreichend viele Stimmen (votes ) gesammelt werden

Mehrheits Votum
- Die Transaktion muss die Mehrheit der Repliken des benötigten Objekts sperren (Sperre = Stimme)
- Beim Lesen wird eine Mehrheit der Repliken mit Sperren belegt ->  das Objekt kann nicht von einer anderen Transaktion zwischenzeitlich verändert werden
- Mindestens ein Replikat befindet sich auf dem aktuellen Stand
- Aktualität der Replikate wird durch einen Änderungszähler festgestellt

Vorteil: Objekte auch bei mehreren Knotenausfällen referenzierbar
Nachteil: mehrere Nachrichten pro Zugriff zur Sicherstellung der Mehrheit

Gewichtetes Voting (Quorum 多数派 Consensus)
- Jedem Replikat wird ein bestimmtes Gewicht (Stimmenanzahl) zugeordnet
- Zum Lesen bzw. Schreiben eines Datums wird eine bestimmte Anzahl von Stimmen (Lese Quorum bzw. Schreib Quorum) gesammelt  必须达到一定数量的 stimme 才能开始 lese 或者 schreiben 操作 
    - r = Lese Quorum
    - w = Schreib Quorum
- Garantien für konsistente Datenverarbeitung (bei insgesamt v Stimmen):
    - w>v/2 garantiert: kein Objekt gleichzeitig von mehr als 1 Transaktion geändert
    - r+x >v verhindert: ein Objekt kann nicht gleichzeitig gelesen und geschrieben werden
- Durch die Gewichte können sowohl die Kosten für Schreib/Lese Zugriffe als auch die Verfügbarkeit bestimmt werden
    - Je kleiner r bzw. w , desto schneller sind die Lese bzw. Schreibzugriffe\
    - Erhöhte Verfügbarkeit, da einige Rechner ausfallen dürfen
    - Lesebevorzugung geht auf Kosten der Schreiboperationen und umgekehrt

Beispiel zu Quorum Verfahren
- Objekt A sei an vier Rechnern R1 bis R4 repliziert
- Stimmenverteilung <2,1,1,1>, d.h. R1 hat 2 Stimmen
- Wählt man r = 3 und w = 3, so sind 2 oder 3 Rechner bei jeder Transaktion involviert
    - Durch Bevorzugung von R1 ist ein schnellerer Zugriff auf die Daten von R1 gewährleistet
    - Zugriff ist auch nach Auswahl jedes der Rechner möglich. Wenn R1 intakt 完好无损的 bleibt dürfen auch zwei Rechner ausfallen
- Lesezugriffe werden gegenüber von Schreibzugriffen bevorzugt, wenn z.B. r=2 und w=4 gilt
    - Lesezugriffe können auf R1 lokal ausgeführt werden
    - Bei Schreibzugriffen sind allerdings mindestens 3 Rechner beteiligt. Nach Ausfall von R1 ist das Objekt nicht mehr modifizierbar

Vor und Nachteile des Quorum Verfahrens
- Durch spezielle Parameterwahl lassen sich das Mehrheits und das ROWA Verfahren sowie eines der Primary Copy Protokolle nachbilden
    - Mehrheitsverfahren: Jedes Replikat hat das gleiche Gewicht (1 Stimme)
    - ROWA Verfahren: Jedes Replikat hat eine Stimme. Ferner ist r =1 und w =v=Anzahl Replikate
    - Primary Copy : 
        - Primärkopie bekommt 1 Stimme, alle anderen Replikate bekommen keine Stimme und r = w =1 
        - Lesezugriffe müssen an Primärkopie gerichtet werden, Repliken ohne Stimme werden ohne Konsistenzzusicherung genutzt
- Nachteil: geeignete Wahl der Parameter komplex, insbesondere wenn diese ohne oder mit geringer Beteiligung des Systemverwalters erfolgen sollen


# 5 Backup

- Backup: Datensicherung auf redundante Speichermedien
- Restore: Wiederherstellung verlorener/veränderter Daten anhand der Backup Kopien nach
    - Festplattencrash und anderen Katastrophen
    - Datenverlust nach Fehlbedienung
- Backupmedien sollen vor Gefahren wie Feuer, Wasserschäden, Erdbeben, Raub, usw. geschützt werden


Backup- Arten:
vollständig vs. inkrementell
- Vollständige Sicherung: Alle Daten werden auf Backupmedium kopiert
- Inkrementelle Sicherung
    - Sicherung aller Daten, die sich seit der letzten Sicherung geändert haben: Letzte Änderung kann inkrementell oder vollständig sein
    - Wiederherstellung setzt Verfügbarkeit der letzten vollständigen Sicherung und ALLER inkrementellen Sicherungen voraus: Datenverlust, falls auch nur eine inkrementelle Sicherung fehlt
- Üblich: Wöchentliche vollständige Sicherung (etwa Wochenende) und tägliches inkrementelles Backup
    - Sicherung eines Zyklus (etwa 3 4 Wochen) auf unterschiedlichen Medien
    - Von Zeit zu Zeit  -> Vollständige Sicherung, die nicht überschrieben wird, d.h. ein bestimmter System und Datenzustand wird konserviert

Beispiel Backup Policy
- Backupklassen definieren verschiedene Wertigkeiten von Daten
    - Typ 1: 3 Versionen, 7 Tage, Aufbewahrung nach Löschung: 1 Version, 30 Tage
    - Typ 2: 7 Versionen, 14 Tage, Aufbewahrung: 1 Version, 90 Tage
    - Typ 3: 30 Versionen, 30 Tage, Aufbewahrung: 5 Versionen, 365 Tage
- Bei Typ 1
    - Maximal 3 Versionen innerhalb von 7 Tagen
        - Sind 3 Versionen vorhanden und Dateiänderung innerhalb des 7 Tage Intervalls, so wird die älteste Version durch die neue Kopie im Backup ersetzt
    - Automatischer Abbau von Versionen, wenn keine Veränderungen
        - Nach 8 Tagen sind 2 Versionen vorhanden. Nach weiteren 8 Tagen nur noch eine
        - Solange Datei nicht verändert oder gelöscht, verbleibt eine Version im Backup
        - Gelöscht Datei kann noch 30 Tage zurückgeholt werden, danach endgültige Löschung