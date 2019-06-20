# SPRG 7 - Principles

## Minimum Exposure

- Reduziere die Angriffsfläche durch...
  - ... die Härtung des Systems
    - Deploy / installiere nur notwendige Komponenten und Libraries
    - Programmiere relevant: Denk daran, dass beispielsweise im Fall von Java Libraries und Klasse schwachstellen zu unsicherer serialisierung ausgenutzt werden könne, auch wenn diese von der Applikation nicht verwendet wird
  - ... nur notwendige Funktionalitäten bekannt geben
    - Gib keine Abläufe bekannt die nicht verwendet werden
    - Entferne obsolete Funktionen welche nicht mehr verwendet werden
    - Verwende Interfaces welche nur die notwendigsten Funktionen liefern
    - Web Services (REST/SOAP), RMI, aber auch Java Interfaces / Module (nur notwindiges bekannt geben)

## Simplicity

- Die Komplexität des Systems wächst
  - Anzahl Code Zeilen
  - Verwendete Libraries
  - Bekanntgegebene Interfaces
- Dies führt zu einer höheren Anzahl von Schwachstellen und erhöht somit die Angriffsfläche
- Deswegen bedeutet eine geringere Komplexität des Systems auch eine geringere Angriffsfläche
- Oder noch besser: Je simpler ein System ist, desto einfacher ist es seine "korrektness" festzustellen

## Defense in depth

- Es sollten mehrere Kontrollen vorhanden sein um die Bedrohungen zu vermeiden
  - Alleinige Kontrolle kann scheitern oder deaktiviert werden (single point of failure)
  - Alleinige Kontrolle ist einfacher abzugrenzen (single point of security)
- Deshalb braucht es eine Redundanz innerhalb der Abhängigkeiten von Usability und Kosteneffizienz
- Zum Beispiel:
  - SQL Injection: Validiere und codiere input, verwende prepared statements
  - XSS: Validiere und codiere input, escape output , verwende secure content policy
  - XXE: Konfiguriere XML Parser, Preprocess XML with lexical parser
  - CSRF: Verwende Anti-CSRF-Tokens und Same-Site Session Cookies

## Least privilege

- Subjekte sollte die niedrigsten Privilegien haben, welche sie benötigen um ihre Aufgabe zu erledigen
- Ebenso sollen Applikationen nur mit den minimalen Privilegien laufen, welche sie benötigen
- Zum Beispiel:
  - Serverseitige Applikationen als nicht als Root User laufen lassen
  - Applikations Datenbank Benutze sollte nur die Rechte auf die Datenbank und Operationen haben, welche er benötigt (z.B.: kein Zugriff auf metadaten Tabelle)
  - Genau so sollen technische Accounts auch nur die Rechte haben, welche sie auf dem System benötigen

## Compartmentalization

- Englisch für:
  - Abschottung
  - Bereichsbildung
  - Kompartimentierung (mein Liebliengsbegriff aus dieser Liste)
- Segmentiere System und Applikations Komponenten wie folgt:
  - Funktionalität, Subdomain (bounded context)
  - Security properties
- Halte schadhafte Aktivitäten innerhalb bestimmter Grenzen
- Am offensichtlichsten ist das auf der Infrastrukturebene:
  - Netzwerk Domains (z.B.: DMZ)
- Das ist vor allem in Zeiten von verteilten Systemen und Cloud-Native Applikationen wichtig:
  - Jeder Systemkomponent läuft in seiner eigenen, dedizierten VM oder
  - Jeder Microservice Prozess läuft in seinem Container

## Minimum trust und maximum trustworthiness

- Wenn man sich auf System, ausserhalb der Trust Boundary verlassen muss, so sollte man diesen minimales vertrauen schenken
  - Daten nicht einfach vertrauen -> validate, encode, sanitize
  - Vorsichtig sein mit deren Verfügbarkeit -> design for their failure
- Wenn Daten an andere Systeme geliefert werden:
  - Liefere vertrauenswürdige Daten
  - Liefere maximale Verfügbarkeit

## Traceability and (complete) mediation

- Aufrufe auf remote und lokale System ausserhalb der trust boundaries sollen geloggt werden für:
  - Security breaches
  - Attack patterns
  - Anomalies
- Dieser Punkt gehört stark zu
  - "Insufficient Logging and Monitoring" aus der OWASP Top 10
  - Missing Function Level Access Control (Mediation). Wie auch immer, mediation in diesem Fall betrifft nur Authorisierung aber idealerweise auch interventionen wenn ein möglicher Angriff erkannt wird