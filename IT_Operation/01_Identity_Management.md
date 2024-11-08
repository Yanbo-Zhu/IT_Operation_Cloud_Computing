

# 1 Ebenen

![](image/Pasted%20image%2020241108212141.png)


![](image/Pasted%20image%2020241108212151.png)

E1: Personendaten
E2: Ressourcen
E3: Autorisierung
E4: Authentisierung

Ebene 1: Personendaten → Wer arbeitet mit dem System?
Verschiedene Datenquellen
- Interner Fokus: Personalabteilung, Immatrikulationsamt, …
- Externer Fokus: Vertrieb (Kunde), Einkaufsabteilung (Lieferant)

Ebene 2: Ressourcen → Mit welchen Systemen wird gearbeitet?
- Systeme und Daten, für die Berechtigungen verwaltet werden
- Individuelle Mechanismen der Zugriffssteuerung durch Systeme vorgegeben


Ebene 3: Autorisierung → Kopplung Ebene 1 und Ebene 2
- Benutzer repräsentiert durch Account → Elegante Systeme zum Management der
- Zugriffsrechte notwendig
- Fokus auf Berechtigungen und deren Verwaltung
- Manuelle Rechteverwaltung für jeden Benutzer ineffektiv
    - Einführung von Gruppen in UNIX, rrste Rollenkonzepte in 1970ern
    - Heute Einsatz von RBAC ( Role Based Access Control)

Ebene 4: Authentisierung → Technische Ebene, stetige Technologieanpassung
- Legitimation des Benutzers als Basis für Rechtevergabe
- Nur der Benutzer selbst darf seine Rechte ausüben
- Erkennung des Benutzers durch charakteristisches Merkmal (Passwort, Biometrie, Chipkarte, …)
- Einsatzgebiet definiert Anforderungen an Authentisierung


## 1.1 Abbildung des Modells


E1: Personendaten
E2: Ressourcen
E3: Autorisierung (Fokus der Ebene 3: Vergabe von Berechtigungen)
E4: Authentisierung


可以用如下方法在每层使用, 去构建那一层 中间应该有啥

Prozessgruppen
- Operative Prozesse
    - Alltägliche Tätigkeiten, um Benutzer mit Rechten auszustatten
- Administrative Prozesse
    - Erweiterung der IdM Funktionen
    - Notwendig bei fehlenden oder unflexiblen operativen Prozessen
- Gestaltende Prozesse
    - Anpassung der Art der Ergebniserzielung an Anforderungen, z.T. auch Anpassung der Ergebnisse an sich → Projekte
- Gruppen im Fokus unterschiedlicher Unternehmensbereiche


Operative Prozess 
![](image/Pasted%20image%2020241108212259.png)




# 2 Ebene 2: Ressourcen (Daten)

Ressourcen 其实就是数据 



Prozess in Ebene 2: Berechtigungssysteme in Anwendungen
Häufig: Access Control Lists (ACL)
- Welcher Benutzer darf auf welche Datei zugreifen
- Granularität: Zugriff kann pro Datei gesteuert werden
- Vergabe von Rechten dezentral möglich
- Überprüfung der Nutzerrechte erfordert Überprüfung aller ACLs


# 3 Ebene 3: Autorisierung (Rollen und Rechte)


![](image/Pasted%20image%2020241108214118.png)


## 3.1 Role

### 3.1.1 Role Based Access Control


Elemente
- Benutzer
- Berechtigung
- Operation
- Objekt
- Rolle: Sammlung von Berechtigungen
    - Benutzer kann mehrere Rollen haben
    - Rolle kann mehrere Benutzer betreffen
    - Rolle soll alle Berechtigungen beinhalten, die zur Ausführung einer bestimmten Tätigkeit benötigt werden
    - Rollen können sich gegenseitig ausschließen → Sicherheit
    - Definition von Min/Max an Rolleninhabern


Hierarchie in RBAC
Rolle 也有三六九等 
- Vererbung von Berechtigungen
    - Übergeordnete Rolle erbt alle Rechte der untergeordneten Rolle
    - Auslagerung allgemeiner Funktionen in Basisrollen
    - An Standortrollen könnten Zugangsberechtigungen gekoppelt sein
- Variante: Zuteilen von Berechtigungen nur an Blätter

![](image/Pasted%20image%2020241108215111.png)



### 3.1.2 Entwicklung eines Rollenmodells

Top Down: Ausgehend vom definierten „Soll“ Zustand  (从自己的理想状态出发)
Bottom Up: Ausgehend vom „Ist“ Stand  (从实际状态出发)


Bottom Up (Role Mining) : Benutzer können bereits mit Anwendungen arbeiten
- Benutzer verfügen also bereits über Berechtigungen
- Diese sind festzustellen und in Tabelle festzuhalten

Top Down: Ableitung von Rollen aus Geschäftsprozessen
- Hoher Aufwand durch exakte Analyse aller Prozesse


### 3.1.3 Beispiel: Rollenbildung ( Gliederung anhand des Stanford University Modells )

Ableitung von Rollen aus den Aufgaben der Mitarbeiter

![](image/Pasted%20image%2020241108220004.png)



![](image/Pasted%20image%2020241108220151.png)





## 3.2 Rechte 

Fokus der Ebene 3: Vergabe von Berechtigungen




# 4 Ebene 4: Authentisierung



Account = Träger der Berechtigungen
Nach Einloggen in Account besitzt der Benutzer Zugriff auf Funktionen und Daten



## 4.1 Single Sign On

用本公司的 account 去访问其他公司的Service 

Single Sign On: Benutzer meldet sich einmal an und kann alle Anwendungen nutzen
- Großer (sichtbarer!) Vorteil des IdM für den Benutzer
- Nur ein Passwort notwendig, das damit länger sein darf: Unterschiedliche Passwörter je nach Sicherheitsstufe

Funktion
- ==Benutzer wird im „Single Sign On“ System zentral gehalten==
- System meldet den Benutzer bei der gewünschten Anwendung an

Probleme
- Passwort erhält eine höhere Bedeutung
- Auswirkungen eines Missbrauchs werden höher
- Neuer „Single Point Of Failure “ innerhalb der Infrastruktur



# 5 Federation

Lösungsansatz: zentraler Authentisierungsservice

将用户信息分散在各部 

Ansatz: Verteilung der Informationen
- ==Statt alle Informationen bei einem Anbieter zu speichern, werden Informationen über mehrere Anbieter verteilt==
- Kein Anbieter hat alle Informationen komplett
- Kunde nutzt jeweils einen Anbieter für Authentisierung


## 5.1 Idee einer Federation

Federation 的好处 

Standardisierter Austausch von Daten
- Gegenstand: Benutzerdaten und Berechtigungen
- Sicherstellung, dass Benutzer Zugriffsrechte hat

Anonymer Zugriff in vielen Fällen ausreichend
- Dürfen alle Mitarbeiter eines Kunden auf Produktdatenbank zugreifen, so muss der/die Mitarbeiter*in nicht identifiziert, sondern nur die Zugehörigkeit zum Kunden verifiziert werden
- Verlässt ein Mitarbeiter den Kunden, so wird das Zugriffsrecht automatisch entzogen
- Effektiverer Schutz als tägliche Synchronisation auf Ebene 1

Personalisierter Zugriff: Ablage von Daten im Zielsystem
- Federation führt zu effizienter und sicherer Übermittlung


## 5.2 Rollen in der Federation


Identity Provider ( IdP ): Erstellt digitale Identität und hält diese vor
- Kreditkartenfirma: KK Nummer als Identität im KK System
- Staat: Identität im Bereich „Digital Governance

Service Provider (SP): Stellt einen Service zur Verfügung, der Identitäten benötigt

Attribute Authority (AA): Kontrolliert bestimmte Attribute der Benutzer

Subjects (S): Oberbegriff für alle Subjekte, für die es digitale Identitäten gibt: Personen, Firmen, Organisationen, …
Nur Subjects können Gegenstand von Anfragen (Claims) sein


Claim Transformers (CT)
- Übersetzt Informationen über Identität, wie sie in Unternehmen A verstanden wird, in eine allgemeine Form, wie sie auch von anderen Unternehmen verstanden werden kann
- Übersetzung muss für andere Systeme vertrauenswürdig sein
    - X.500, Kerberos , SAML

Beispiele
- „Ist Angestellter“ wird übersetzt in „darf
- Geburtsdatum wird übersetzt in „ist volljährig“


## 5.3 Federation Beispiel: Single Sign On


Benutzer S ist Angestellter von Unternehmen A
用本公司的 account 去访问其他公司的Service 
- Unternehmen A ist Identity Provider (IDP)
- Subject S meldet sich bei seiner Firma an
- Subject S kann auf Anwendung (Service Provider, SP) einer anderen Firma zugreifen, ohne sich dort noch einmal anmelden zu müssen

Verknüpfung über Rollen
用户的信息 会交给 SP,   SP 中会生成一个新的 lokalen Account  给这个空户
- Übermittlung von Attributen beim Zugriff auf fremden SP (service provider)
- ==SP ist in der Lage dem fremden Benutzer Rolle zuzuordnen==
    - Benutzer erlangt Zugriffsrechte im fremden System
    - Benutzer ist nicht namentlich im fremden System bekannt

Beispiel: „Benutzer ist Mitarbeiter im DSL Verkauf von 1&1“
- Telekom erlaubt Benutzer Zugriff auf DSL Verfügbarkeitsdaten


Verknüpfung über Account Linking
- Personendaten sind in beiden Systemen vorhanden
- Bei Zugriff wird ID an Zielsystem übertragen
    - Zielsystem kann Benutzer einem lokalen Account zuordnen
    - Benutzer muss sich nicht neu am Zielsystem anmelden
    - ID kann anonym sein ohne Rückschlüsse auf eine Person
        - Unternehmen verwalten die Identität der Benutzer unabhängig voneinander
        - ID204=„Mitarbeiter DSL-Verkauf 1&1“

![](image/Pasted%20image%2020241108222845.png)

## 5.4 Federation Beispiel 2: Anforderung von Attributen

Dynamische Anforderung
- Benutzer (Subjekt) arbeitet mit System bei Unternehmen B
- SP von B fordert zusätzliche Informationen an
    - Benutzer ruft Wetterinformation ab, SP(B) fragt nach Wohnort
    - Request wird an IDP(A) gerichtet
- Claim Transformer (CT) filtert aus Benutzer Datensatz die PLZ aus
    - Nur diese Information wird von IDP(A) an SP(B) übertragen



IDP(A) verwaltet nur eine Teilmenge an Informationen
- SP(B) kann ein Attribut anfordern, was IDP(A) nicht verwaltet
- IDP(A) liefert Adresse der Attribute Authority (AA) an SP(B)
- SP(B) stellt neue Anfrage bei AA(A)
上面的图的解释 


AA  <----  IDP (A)   <------  SP (B)
IDP (A) 通过 CT 将 PLZ 交给 SP(B)
AA 返回 address 给 IDP(A) 再给 SP(B)



Claim Transformers (CT)
- Übersetzt Informationen über Identität, wie sie in Unternehmen A verstanden wird, in eine allgemeine Form, wie sie auch von anderen Unternehmen verstanden werden kann



## 5.5 Protokoll: Security Assertion Markup Language (SAML)

XML und OASIS Standard ( Organization for the Advancement of Structured Information Standards)

Beschreibung und Übertragung von sicherheitsbezogenen Informationen
- Client erfragt Sicherheitsinformationen bei SAML Authority
- SAML Authority antwortet mit entsprechenden Daten

Datei mit drei Komponenten
- Authentifizierung (z.B. Zeitpunkt, Ausführender, Art des Authentifizierungsmechanismus)
- Person (z.B. Abteilungszugehörigkeit innerhalb einer Firma)
- Berechtigungen der Person für die betroffenen Ressourcen



# 6 OpenID and OAuth

## 6.1 Digitale Identifikations und Authentifikationssystem OpenID



Dezentrales Authentifizierungsprotokoll für Internet Dienstanbieter
- Benutzer kann Anwendungen ohne erneute Registrierung nutzen
- Sofortige Anmeldung bei allen dem Provider vertrauenden Dienste
- Passwörter für Einzeldienste entfallen
- OpenID als Authentifizierungsmerkmal erzeugt von OpenID Provider


Dezentrale Konzeption von OpenID , freie Verfügbarkeit der Software → Anzahl Provider nicht beschränkt
- Keine Authentifizierungsmethode vorgeschrieben: Benutzername/Passwort, Tokens, SmartCards


Ablauf
- Anwendung erfragt beim OpenID Provider die Zulässigkeit des Nutzers
- Benutzer muss sich gegenüber OpenID Provider authentifizieren
- Provider sendet zur Verifizierung der Identität eine Pseudokennung an Anwendung, mit der nicht auf den Benutzer Account des Nutzers geschlossen werden kann


OpenID是一个去中心化的网上身份认证系统 (Dezentrales Authentifizierungsprotokoll)。对于支持OpenID的网站，用户不需要记住像用户名和密码这样的传统验证标记。取而代之的是，他们只需要预先在一个作为OpenID身份提供者（identity provider, IdP）的网站上注册。
OpenID是去中心化的，==任何网站都可以使用OpenID来作为用户登录的一种方式，任何网站也都可以作为OpenID身份提供者==。OpenID既解决了问题而又不需要依赖于中心性的网站来确认数字身份

不再需要统一的一个identity provider来, 储存 身份信息.   任何网站也都可以作为OpenID identity provider 


### 6.1.1 属于 

**最终用户（End User）**
想要向某个网站表明身份的人。

**标识（Identifier）**
最终用户用以标识其身份的[URL](https://zh.wikipedia.org/wiki/URL "URL")或[XRI](https://zh.wikipedia.org/wiki/XRI "XRI")。

**身份提供者（Identity Provider, IdP）**
提供OpenID URL或XRI注册和验证服务的服务提供者。

**依赖方（Relying Party, RP）**
想要对最终用户的标识进行验证的网站。


### 6.1.2 登录

一个想要为其访问者提供OpenID登录网站，比如example.com，需要在页面的某个地方放置一个登录表单。与提示用户输入用户名和密码的传统登录表单不同的是，这种表单只有一个输入框：OpenID标识。网站可以选择在这个输入框旁显示一个小的OpenID图标。这个表单与OpenID客户端库的实现相连接。

假设用户Alice在身份提供者openid-provider.org处注册了一个OpenID标识：alice.openid-provider.org。如果Alice想使用这个标识来登录example.com，她只需要到example.com去将alice.openid-provider.org填入OpenID登录表单。


## 6.2 Autorisierungsdienst OAuth

Registrierte Nutzer kontrollieren Art und Umfang der erteilten Freigaben auf eigene Daten, die beim Oauth Provider für Drittanwendungen hinterlegt sind
- Ähnlich zu OpenID , Unterschied durch Autorisierungsfunktion
- Einer Anwendung kann auch im Nachhinein die genehmigte Nutzung bestimmter Daten entzogen werden
- Einmaliger Zugriff der Anwendung auf Nutzerdaten erforderlich → Verweigerung (automatisch und ohne weitere Überwachung durch Nutzer) aller weiteren Abfragen


https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html



### 6.2.1 应用场景

为了理解OAuth的适用场合，让我举一个假设的例子。
有一个"云冲印"的网站，可以将用户储存在Google的照片，冲印出来。用户为了使用该服务，必须让"云冲印"读取自己储存在Google上的照片。
问题是只有得到用户的授权，Google才会同意"云冲印"读取这些照片。那么，"云冲印"怎样获得用户的授权呢？
传统方法是，用户将自己的Google用户名和密码，告诉"云冲印"，后者就可以读取用户的照片了。这样的做法有以下几个严重的缺点

（1）"云冲印"为了后续的服务，会保存用户的密码，这样很不安全。
（2）Google不得不部署密码登录，而我们知道，单纯的密码登录并不安全。
（3）"云冲印"拥有了获取用户储存在Google所有资料的权力，用户没法限制"云冲印"获得授权的范围和有效期。
（4）用户只有修改密码，才能收回赋予"云冲印"的权力。但是这样做，会使得其他所有获得用户授权的第三方应用程序全部失效。
（5）只要有一个第三方应用程序被破解，就会导致用户密码泄漏，以及所有被密码保护的数据泄漏。




### 6.2.2 名词定义

在详细讲解OAuth 2.0之前，需要了解几个专用名词。它们对读懂后面的讲解，尤其是几张图，至关重要。

    （1） Third-party application：第三方应用程序，本文中又称"客户端"（client），即上一节例子中的"云冲印"。

    （2）HTTP service：HTTP服务提供商，本文中简称"服务提供商"，即上一节例子中的Google。

    （3）Resource Owner：资源所有者，本文中又称"用户"（user）。

    （4）User Agent：用户代理，本文中就是指浏览器。

    （5）Authorization server：认证服务器，即服务提供商专门用来处理认证的服务器。

    （6）Resource server：资源服务器，即服务提供商存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。

知道了上面这些名词，就不难理解，OAuth的作用就是让"客户端"安全可控地获取"用户"的授权，与"服务商提供商"进行互动


### 6.2.3 OAuth的思路

OAuth在"客户端"与"服务提供商"之间，设置了一个授权层（authorization layer）。"客户端"不能直接登录"服务提供商"，只能登录授权层，以此将用户与客户端区分开来。=="客户端"登录授权层所用的令牌（token），与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。==

"客户端"登录授权层以后，"服务提供商"根据令牌的权限范围和有效期，向"客户端"开放用户储存的资料。


### 6.2.4 运行流程


![](image/Pasted%20image%2020241108230444.png)


（A）用户打开客户端以后，客户端要求用户给予授权。
（B）用户同意给予客户端授权。
（C）客户端使用上一步获得的授权，向认证服务器申请令牌。
（D）认证服务器对客户端进行认证以后，确认无误，同意发放令牌。
（E）客户端使用令牌，向资源服务器申请获取资源。
（F）资源服务器确认令牌无误，同意向客户端开放资源。

不难看出来，上面六个步骤之中，B是关键，即用户怎样才能给于客户端授权。有了这个授权以后，客户端就可以获取令牌，进而凭令牌获取资源。

下面一一讲解客户端获取授权的四种模式。

客户端必须得到用户的授权（authorization grant），才能获得令牌（access token）。OAuth 2.0定义了四种授权方式。
    授权码模式（authorization code）
    简化模式（implicit）
    密码模式（resource owner password credentials）
    客户端模式（client credentials）

https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html



### 6.2.5 更新令牌

如果用户访问的时候，客户端的"访问令牌"已经过期，则需要使用"更新令牌"申请一个新的访问令牌。

客户端发出更新令牌的HTTP请求，包含以下参数：
- grant_type：表示使用的授权模式，此处的值固定为"refresh_token"，必选项。
- refresh_token：表示早前收到的更新令牌，必选项。
- scope：表示申请的授权范围，不可以超出上一次申请的范围，如果省略该参数，则表示与上一次一致。

下面是一个例子。

```http
 POST /token HTTP/1.1
 Host: server.example.com
 Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
 Content-Type: application/x-www-form-urlencoded

 grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
```


## 6.3 OpenID Connect

OpenID Connect verbindet OpenID und Oauth
- Anmeldung via OpenID
- Danach gezielte und spezifische Freigabe von Daten mittels Oauth
- Benutzer kann sich gleichzeitig authentifizieren ( OpenID ) und Zugriffsrechte auf Daten einräumen durch Nutzung eines einzelnen OpenID Connect Providers

Viele OpenID Provider, deren Dienstleistungen auch von einem durch ihre Nutzer autorisierten Zugriff auf Daten profitieren, nutzen OpenID Connect und sind OpenID Connect Provider



