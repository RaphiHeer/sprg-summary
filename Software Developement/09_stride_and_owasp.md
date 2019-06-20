# SPRG 9 - Stride and OWASP



## STRIDE Analyse mit OWASP Top 10



### Spoofing

- Spoofing -> sich als jemand falsches ausgeben

- Beispiel: Der Angreifer möchte sich als einen anderen User einlogen

- Relevant für die OWASP Top 10 items:

  - **Broken Authentication**

    - Authentication bypass (authentifizierung umgehen):
      - Injection (SQL, LDAP)
        - z.B.: `... WHERE username = '{username}'`
        - wobei `{username}` = `' OR 1=1 --`
      - Geklaute Credentials (XSS)
    - Session Hijack (Session ID klauen z.B.: von den Cookies)
    - Session Fixation (Angreiffer verwendet XSS um dem Opfer eine Session ID zu setzen)

  - **Injection**

    - Wenn authentifizierung verwundbar ist (dasselbe Beispiel wie bei broken authentication)

  - **Cross Site Scripting**

    - Wenn Login Seite auf XSS verwundbar ist:

      `<div><p>${param.message}</p></div>`

### Tampering

- Tampering -> Daten verändern / manipulieren
- Der Angreifer führt eine Transaktion durch, die das Opfer niemals durchführen würde
  - Transaktion erstellen durch CSRF
  - Transaktion erstellen durch XSS
- Angreiffer modifiziert eine existierende Transaktion
  - SQL Injection auf UPDATE, DELETE statements
- Schadhafter insider verändert Daten in der Datenbank direkt
- Relevante OWASP Top 10 items:
  - XSS, CSRF, SQL Injection, Unsichere Serialisierung
  - Broken Access Control führt häufig zu unauthorisierten veränderungen

### Repudiation

- Abstreitbarkeit
- Ein Benutzer erstellt eine Transaktion, von welcher er später behauptet diese so nie durchgeführt zu haben
- Beispiele:
  - Böswilliger Insider modifiziert die Datenbank
- Wichtiges OWASP Top 10 Item:
  - **Insufficient Logging and Monitoring**
- Werden Aktionen geloggt?
  - Auf der Applikationsschicht
    - Login Event, gescheitertes Login (invalide Daten nicht loggen)
    - Transaktion erstellt mit Details
    - Transaktions History darstellen für einen Account mit N Transaktionen
  - Database Access
    - User / Admin logging in der Datenbank
    - Query ausführung
- Werden diese Logs archiviert? Sind sie sicher gegen Tampering?

### Information Disclosure

- Vertrauliche Informationen gelangen in die falschen Hände
- Angreifer stiehlt vertrauenswürdige Daten vom System
  - User credentials, account details, transaction history
- Bösartiger Insider schaut sich die Transaktions History eines Accounts an, welcher nciht zu ihm gehört
- Relevante OWASP Top 10 Items:
  - Sensitive Data Exposure
    - Sensible Daten werden nicht genügend sicher behandelt (z.B.: bei Datenübertragung oder bei speicherung)
    - SQL Injection (UNION ALL und blind)
    - XSS (vom Browser und HTTP/Web Services)
    - Broken Access Control führt häufig zu Information Disclosure

### Denial of Service

- Service Lahmlegen
- Angreifer beeinflusst die Verfügbarkeit einer Applikation
- Login Seite verunstalltet durch XSS
- Schadhafter Payload crasht das System
  - Sehr grosse Eingabeparameter
  - XML Entity Expansion (Billion Lughs attack)
  - Distributed DoS
- System wird nicht neu gestartet oder hat einen fatalen Fehler (ungenügendes Monitoring)
- Relevante OWASP Top 10 Items:
  - Insufficient Logging and **Monitoring**
  - XSS - daface (verunstalten)



### Elevation of Privilege

- Erhöhen der Rechte
- Bösartiger Insider verändert seine Privilegien
  - -> so kann er auf Daten zugreifen, auf die er normalerweise keinen Zugriff hat
- Relevante OWSP Top 10 items
  - Broken Access Control
    - Access Control kann umgangen werden
    - Fehlende Function Level Access Control
    - Unsichere direkte Objekt Referenzen
  - XXE -> kann auf HTTP Service zugreiffen mit fehlender authorisierung hinter der firewall
  - Unsichere deserialisierung -> je nach dem wie die authorisierung durchgeführt wird, kann diese bei schwachstellen durch deserialisierung umgangen werden

