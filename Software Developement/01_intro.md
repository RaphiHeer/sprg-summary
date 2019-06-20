# SPRG 1 - Intro

Was bedeutet sicheres Programmieren?

> Die Kunst sicherzustellen, dass die Entwickelte Software all das tut, was erwartet wird und nichts anderes.

Wieso sicheres Programmieren?

- Software ist inzwischen in den meisten Teilbereichen unseres Lebens angekommen
  - Daten über von unserem und über unser Leben werden elektronisch gespeichert
  - Elektronische Geräte sind in unserem Wohnraum (Alexa, Siri, Smart TV usw.)
  - All diese Dingen werden verarbeitet und gesteuert durch Software
- Wie entwickeln wir also sicher?
  - Security Requirements
  - Sicheres Design, Policies und Governance
- Das klingt alles Toll. Allerdings muss das auch jemand entwickeln!
  - Im besten Fall wird Security von Anfang an mit einbezogen
  - Und selbst wenn das Design und die Policy stimmen, so können immer noch Fehler bei der Implementation auftreten



## Interessante Beispiele aus der Praxis

Im Folgenden werden ein paar interessante Beispiele aus der Praxis gezeigt. Ich nehme an, dass ist kein Prüfungsstoff. Aber interessant ist es auf jeden Fall! :-)

### Apple goto fail

!["apple fail"](C:\Users\rapha\switchdrive\Studium\Module\Semester 6\SPRG\Zusammenfassung\Software Developement\img\apple_fail.png)

- Auf der Abbildung oben sind 2 goto Statements nacheinander zu sehen. Bei der Programmiersprache C können bei if-Statements die geschweiften Klammern {} weggelassen werden, sofern nur 1 Statement ausgewertet wird.
- Hier sind allerdings 2 goto Statements nacheinander. Das bedeutet, dass erste wird nur ausgeführt, wenn das if-Statement als wahr ausgewertet wird. Das zweite wird allerdings **immer** ausgeführt.

### Cloudbleed

- Was ist Cloudflare?

  - Cloudfare ist ein CDN (Content Delivery Network) und SECaaS (Security as a Service)
  - Beispiele:
    - DDoS protection
    - Web Application Firewall
    - Automatic SSL Management
  - Cloudflare bietet einen revers proxy an
    - Der ist Zuständig für Load balacing
    - Caching
    - HTML Parsen
      - HTTPS everywhere
      - Obfuscation (z.B.: E-Mail Adresse)
      - Analytic Tags hinzufügen

- HTML Code checken ob er am Ende des Buffers ist:

  ```c
  if (++p == pe) // if pointer p is at the end of the buffer
      goto _test_eof;
  ```

- Was passiert, wenn `++p > pe` ?

  - -> Der Pointer verlässt den Buffer und liest Daten aus dem Arbeitsspeicher, welche in den Output geschrieben werden
  - Eine Verwundbare Webseite gab somit Informationen einer anderen, nicht damit verbundenen Webseite Preis
    - HTTP headers, POST data (**Passwörter!!**), JSON für API calls, URI parameters, cookies und authentifizierungsdaten (API keys und OAuth tokens)
    - Obwohl der Fehler schnell gefixxt wurde, so waren trotzdem für eine gewisse Zeit solche Daten im cache von Suche Maschinen zu finden

### Heartbleed

- Kurze Erklärung: Was ist ein Heartbeat?
  - Bei einem Heartbeat sendet der Client zwischendurch eine Nachricht an den Server mit randomisierten Daten
  - Zusätzlich zu den Daten wird die Länge der zurückzusendenen Daten mitgeschickt.Also z.B. wird "Hello World" geschickt und die bitte, doch die erste 5 Buchtstaben dieses Strings zurück zu senden
    - -> somit wird der Server nur "Hello" zurückschicken
  - Damit kann z.B. über openSSL sicher gestellt werden, dass der Server noch erreichbar ist
- Was lief schief?
  - Im Code wurde nicht geprüft, ob die Länge der zurückzuschickenden Daten grösser ist, als die Länge der Daten selbst
  - 
- Der Bug Heartbleed war in openSSL 1.0.1 aufzufinden
- 

### Linux Kernel Backdoor



## Anforderungen um sicher zu Programmieren

- Wie meistert man das sichere Programmieren?
  - Verstehe die Funktionsweise von Software Vulnerabilites
    - -> Häufige Design- und Implementationsfehler
  - Unterschiedliche Fehlerklassen kennenlernen
  - Lerne Mitigationsprinzipien und Techiken zu schätzen
  - Verstehe den Softwareentwicklungsprozess und moderne SDLC (Software Developement Life Cycle) Praktiken


$$
Risk = Impact * Probability
\\
IT Risk = Impact * Thread Level * Vulnerability Level
$$
Threat Level = Möglichkeiten eines bösswilligen Angreiffers, z.B.: Skills, Resourcen

**Vulnerability Level = Anwesenheit von Fehlern und einfach diese auszunutzen**



### Microsoft Vulnerability Mitigation Strategie



<table>
    <tr>
    	<td>Strategie</td>
    	<td colspan="2">Mach es schwierig und kostenaufwendig um vulnerabilities und exploits dafür zu finden</td>
    </tr>
    <tr>
        <td rowspan="4">Taktiken</td>
        <td>Eliminieren Schwachstellen</td>
        <td>SAST und DAST</td>
    </tr>
		<td>Breche exploitation Techniken</td>
		<td>Stack Canary, ASLRT, Same Origin Policy, Sichere HttpOnly Cookies</td>
	</tr>
	<tr>
        <td>Halte den Schaden im zaum und verhindere Persistenz</td>
        <td>App, Containers und Virtualisierung</td>
	</tr>
	<tr>
        <td>Limitiere das Zeitfenster für einen Exploit</td>
        <td>Mature detection and response capabilities</td>
	</tr>
</table

- Eliminate
  - -> Software vulnerabilities
- Break exploitation techniques
  - -> Class specific exploitation primitive
  - -> Generic exploitation primitive
- Contain
  - -> Payload

### Security im Softwareentwicklungsprozess

- Requirements / *Anforderungen*
  - Was soll geschützt werden? -> Threat Model
- Specification and Design
  - Wie wird es gemacht und verwendet?
- Implementation
  - **Fehler im Programmcode sind die Hauptgründe für Schwachstellen**
- Testing
  - Viele verschiedene Tests
  - Penetration Testing :-)
- Maintenance
  - Regelmässige Reviews



### Vom Design zur Implementation

- Wenn wir Software implementieren...
  - ... konkretisieren wir inputs und outputs
    - Felder, Format, Encoding, Protokolle
  - ... Annahmen zum Kontext treffen
    - Libraries, APIs, consumed services
    - Umgebung einberechnen, resourcen
    - Concurrency Model
  - **Angriffsfläche wächst**
  - **Schwachstellen hervor bringen**
  - **Thread Model verbessern**

### Attack surfaces

- Die Angriffsfläche einer Softwareumgebung ist die Summe aller unterschiedlichen Angriffspunkte (bzw. Angriffsvektoren), bei dem es einem unautorisierten User (der Angreifer) möglich ist Daten einzugeben oder Daten aus der Umgebung auszulesen
  - Quelle. Wikipedia
- Durch eine Angriffsfläche kann ein Angreifer theoretisch...
  - ... Daten oder ein System **Verändern / Bearbeiten**
  - ... ein System **Überwachen**
- Angriffsflächen
  - User interface
  - Network connections
  - System interfaces / IPC interfaces
  - Database
  - Storage
  - Log

### Nochmals, vom Design zur Implementierung

- Alle diese Aspekten wurden vom Design absichtlich weggelassen
  - Um ein komplexes System zusammen zu bauen und zu managen benötigt man Komponenten Komponenten auf einem höher abstrahierten Ebene
  - Ansonst würden wir immernoch Web Apps in Assembler schreiben
- Dies führt zu Problemen, denn...
  - ... diese Abstraktionen sind nur auf der konzeptionellen Ebene vorhanden...
  - ... Angreifer respektieren solche Grenzen der Abstraktion nicht
- **Für ein gutes Thread Model muss man dies unbedingt beachten!!!**



### One time pad mit XOR CMOS Gate





