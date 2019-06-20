# SPRG 4 - XSS CSRF

## Einleitung zu Webapplikationen

### Wie funktioniert eine Webapplikation?

- Der Client sendet eine Http-request  an einen Server bzw. eine Adresse (Server + Port)
- Der Server verarbetiet diese Anfrage und sendet ein HTML Dokument als Antwort
- Diese HTML-Dokument enthält:
  - HTML: Datenstruktur der Webseite
  - CSS: Beschreibt, wie die Daten dargestellt werden sollen
  - JavaScript: Code, welcher beim Client ausgeführt wird
    - JavaScript kann den HTML und CSS Teil der Webseite lesen, bearbeiten, löschen usw.
    - JavaScript kann Anfragen mit Daten an einen Server senden
    - JavaScript kann mit dem User interagieren
    - Und JavaScript kann noch vieles vieles mehr!

### Anatomy einer Http-Request

- Adresse:
  - IP Adresse (4.20.9.11) oder Domain Name (reddit.com)
  - Port (80 = http, 443 = https)
- Methode:
  - GET, POST, DELETE, HEAD, OPTIONS
- Headers
  - cookies
  - agent
  - content-type
  - security tokens
- Request Body
  - Inhalt der Anfrage
  - GET, HEAD und DELETE Anfragen haben keinen body

### Anfragen verarbeiten auf dem Server

- Normalerweise wird ein MVC (Model-View-Controller) Framework verwendet
- Damit teilt sich die Verarbeitung in 3 Schritte auf:
  - **Model erstellen** -> basierend auf den Parametern der Anfrage. Mit "Model" ist eigentlich eine Struktur von Daten gemeint welche verarbeitet werden kann.
  - **Controllers aufrufen** -> basierend auf der Business Logik werden die basierend auf dem "Model" Entscheidungen treffen, Daten verarbeiten
  - **View rendern** -> transformiert das Model in eine eine View. Das kann HTML, JSON oder XML sein
- Normalerweise geschieht dasselbe auch bei web-services, auch wenn diese kein HTML generieren

## CSRF

- **CSRF** = **C**ross **S**ite **R**equest **F**orgery
  - Missbraucht das vertrauen einer Webseite in das Vertrauen des Users
  - Dieses vertrauen erlaubt es einer "bösen" Webseite vom Browser des Opfers aus eine Anfrage an die Zielwebseite zu schicken 
    - wenn der User eingeloggt ist, schickt der Browser gleich noch die Cookies mit um den User zu authentifizieren und für diese Aktion zu autorisieren
  - Technisch gesehen entsteht die Sicherheitslücke durch das state management von Http und das Designs des Browsers
- Ablauf von CSRF
  - Begriffserklärung:
    - User = Opfer
    - Zielseite = youtube.com
    - Bösartige Seite = kinox.to
  - Der User loggt sich irgendwann mal auf einer Webseite mit seinen Logindaten ein (z.B. youtube.com)->  der Browser speichert ein Cookie mit welchem der User sich anschliessend wieder authentifizieren kann
  - Irgendwann geht der User auf eine bösartige Seite (z.B. kinox.to)
    - Wie immer erhält der User auf seine Anfrage bei kinox.to eine Antwort welche HTML, CSS und natürlich JavaScript enthält
  - Nun schafft es die böse Seite das Opfer irgendwie dazu zu bringen eine Anfrage auf die Zielseite abzusetzen. Dies kann beispielsweise durch folgende Szenarien passieren:
    - Auf der Seite ist ein Button, auf welchen das Opfer drauf klickt. Allerdings sendet dieser Knopf die Anfrage nicht an die bösartige Webseite (wie es sein sollte), sondern an die Zielseite
    - Durch modernste Webtechnologien ist es dem Browser möglich JavaScript Code, welchen er von der bösen Webseite erhält, auszuführen. Dies ist normalerweise auch legitim und wird für gutes verwendet. In diesem Fall allerdings sendet der Code eine Anfrage an die Zielseite
    - Es gibt bestimmt noch weitere Techniken. Aber egal welche eingesetzt wird, es wird immer dasselbe Ziel verfolgt:
      - Der Browser des Opfers soll eine Anfrage an die Zielseite absetzen, durch welche auf dem Server eine Aktion ausgeführt wird. Da der Browser bei dieser Anfrage gleich noch das Session-Cookie zur Authentifizierung mitschickt, passiert dann die gewünschte Aktion im Namen des Benutzers
  - Man muss sich dabei immer im Kopf behalten, dass **alle** Aktionen auf einem Server durch **Anfragen des Browsers** ausgeführt werden!!!
    - Wenn ich z.B.: unter das neue Video von Justin Bieber schreiben möchte, wie toll ich seine Musik finde, so sendet mein Browser eine Anfrage an youtube.com, welche dem Server befiehlt meinen Kommentar unter das VIdeo zu setzen
  - Um wieder zu unserem Beispiel zurück zu kommen:
    - Die bösartige Seite bastelt (oder eben "forgery") eine Anfrage zusammen, welche das Opfer dann abschicken soll
    - Diese Anfrage kann z.B.: ein Kommentar mit unchristlichen Schimpfwörtern unter das neue Justin Bieber Video sein. Oder die Anfrage, doch bitte meinen Youtube Account zu löschen

### Schutz gegen CSRF

- Same-Site Cookies und CSRF-Tokens sind für andere Webseiten nicht verwendbar
- Allerdings kann böser Code, welcher von der Zielseite kommt, auf diese auf diese Tokens zugreifen -> siehe **XSS**

## XSS

- **XSS** = **Cross** **S**ite **S**cripting (Cross ist englisch für Kreuz -> X ist kreuz)
  - Schwachstelle in einer (Web-)Applikation, welche es erlaubt schädlichen Code in Form von JavaScript so zu injezieren, dass er bei anderen Benutzern (bzw. Opfer) beim Aufruf der Seite mitgeliefert wird
- Der Angreifer injiziert JavaScript in die (Web-)Applikation indem er eine Schwachstelle ausnutzt.
  - Server side code (sanitizing, validation, encoding/escaping)
  - Client side code (escaping)
- Für was kan XSS eingesetzt werden?
  - Die hürden, welche für CSRF-Protection aufgesetzt wurden überwinden (JavaScript kann die anti CSRF-Tokens auslesen)
  - Session eines Opfers verwenden indem das Session Cookie mithilfe von JavaScript geklaut wird
  - Tastatureingaben über JavaScript abfangen (Keylogger ahoi!)
  - Ein falsches HTML-Formular darstellen um die Daten des Opfers zu klauen (fake login usw.)
  - Dateien herunterladen (typischerweise Malware)
  - Das Opfer auf eine andere Webseite weiterleiten die unter der Kontrolle des Angreifers liegt

### Verschiedene XSS Typen

- Darauf basierend, von wo der Schadcode stammt
  - **Reflected XSS**:
    - Schädliches JavaScript in der HTTP-Anfrage wird vom Server zurückgegeben
    - Dieser Code ist meistens Teil eines Links / Formulars / Bildes, erstellt durch den bösen Angreifer
  - **Persistent or stored XSS**
    - Schädliches JavaScript wird von einem persistenten Speicher gelesen
    - Damit dies passieren kann, muss der Angreifer es zuerst fertig bringen diesen Code beim Server ein zu schleusen
- Basierend darauf, wo der schädliche Code im angezeigten Dokument plaziert wird
  - **Source-base XSS**: Server plaziert den Schadcode ins HTML vor dem rendering
  - **DOM-based XSS**: Clientseitiges JavaScript (nicht schädlicher Code) platziert den Schadcode in den DOM-Tree des Dokuments (also ins HTML der aufgerufenen Webseite)



- **Reflected XSS (source based)**

  - Eine Schwachstelle in der Webapplikation, wenn sie den Wert der Request-Parameter ins HTML der Antwort steckt

    Folgend ein sehr simples PHP Beispiel:

    ```php
    <html>
        <body>
            <?php 
            	echo $_GET["parameter"];
            ?>
        </body>
    </html>
    ```

- **Persisted XSS (source-based)**

  - Eine Schwachstelle, bei der User-Input mit Schadcode zugelassen und gespeicehrt wird. Anschliessend lädt das Opfer eine Seite, bei der der Schadcode aus der Datenbank ins HTML Dokument der Antwort gesteckt wird

    Folgend ein sehr simples PHP Beispiel (Annahme, DB verbindung usw. durch einfachen Methodenaufruf):

    ```php
    <?php
        // Save data
    	if(isset($_POST["data"]))
        {
            $db->saveDataDirectToDB($_POST["data"]);
        }
    ?>
    <html>
        <body>
        	<?php
        		// Output Data
        		echo $db->getDataFromDB();
        	?>
        </body>
    </html>
    ```

    

- **DOM-based XSS**

  - Clientseitiges JavaScript manipulieren den DOM tree sehr oft

    - manipulieren -> Elemente hinzufügen, löschen und verändern
    - Somit können auch Schadhafte Elemente eingefügt werden

  - Das Laden von Daten und manipulieren vom DOM tree ist gerade bei Single Page Applications völlig normal

  - Es gibt typischerweise 3 Orte, an denen JavaScript Schadcode im DOM hinzufügen kann

    - **Document sink**

      - Updated innerHTML oder outerHTML von einem document.element (also die HTML-Tags) mit Daten des Angreifers

        Beispielcode:

        ```javascript
        var username = searchParams.get('name');
        if(username !=== null) 
        {
            document.getElementById('p1').innerHTML = 'Hello ' + username;
        }
        ```

        

    - **Location sink**

      - Updated document.location mit den Daten des Angreifers

      - Normalerweise mit dem JavaScript-Pseudo-Protocol wie z.B.: javascript:alert(1)

        Beispielcode:

      ```javascript
      var redir = searchParams.get('redir');
      if(redir !=== null)
      {
      	document.location = redir
      }
      ```

      

  - **Execution sink**

    - Führt die `eval`-Funktion oder template



### Schutz vor XSS

- Validate, encode and/or sanitize input
  - Definiere validierungsregeln, welche keinen schadhaften input erlauben
    - Bei streng typisierten Sprachen kann dies mit Klassen von Datentypen geregelt werden (z.B. eine Klasse `Namen`, welche nur Zeichen des Alphabets als input zulässt usw.)
  - Codiere (encode) input in ein harmloses Format
    - z.B.: html encode -> Zeichen wie < und  > durch &amp;gt; und &amp;lt; ersetzen -> so führt interpretiert der Browser die Daten nicht als HTML bzw. JavaScript
  - Bereinige (sanitize) "Rich Text Input" (also z.B. Daten, welche mit HTML-Tags verziert werden)
    - entferne alle <script>-Tags aus dem Input
  - Ausgabe in der view escapen
    - verwende template frameworks auf der Server und Client Seite, welches den output standardmässig escaped
- Definiere, welchem Code der Server vertrauen (und ausführen) soll mit dem Content-Security-Policy header

## CSRF vs XSS

| **CSRF**                                                     | **XSS**                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Missbraucht Vertrauen der Seite                              | Missbraucht vertrauen des Users                              |
| Die Applikation selbst ist nicht unbedingt verwundbar (ihr fehlt nur der CSRF Schutz) | Die Applikation ist verwundbar, denn sie lässt das injizieren von Schadcode zu |
| Es ist mer ein Browser/Client Problem des Cookie-Handling    | Eine Hauptmotivation für XSS ist das überwinden des CSRF schutzes |
| Trotzdem benötigt es einen Schutz für die Applikation        | XSS missbraucht Schwachstellen des Servers und des Clients   |
|                                                              | Es gibt eine Menge verschiedene XSS-Attacken die sich auch stetig verändern |
|                                                              | Somit sollte das System und die Mitigationen dagegen regelmässig überprüft werden |

