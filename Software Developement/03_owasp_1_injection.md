# SPRG 3 - OWASP 1 Injections

- Injections sind bei der OWASP Top 10 permanent auf Rang 1!

- Sogenannte "command interfaces" sind anfällig auf Injections
  - Interfaces, welche eine Mischung aus Befehlen und Parametern aus Daten heraus interpretieren:
    - SQL query
    - OS command
    - LDAP query
    - XML oder XML based DSL
    - (X)HTML / javascript (XSS ist auch eine Form von Injections)
- Häufig entstehen dies Lücken dadurch, dass ein Komponent zu locker mit User Inputs umgeht
  - missing validation
  - missing sanitization
  - missing encoding



## Verschieden Typen von Injections

- Übersicht:
  - In-Band Injection
  - Out-of-Band Injection
  - Blind Injection
  - 2nd order Injection

### In-Band Injection

- Der Angreiffer bekommt das Resultat der Injection auf dem selben Kanal zurück wie auf dem, auf welchem er den schadhaften Payload

### Out-of-Band Injection

- Der Angreiffer bekommt das Resultat der Injection auf einem anderen Kanal mit
  - z.B.: per E-Mail



### Blind Injection

- Der Angreiffer bekommt keine direkte Rückmeldung von der Injection. Allerdings kann er das System beobachten und daraus Rückschlüsse ziehen
  - z.B.: Kann in ein SQL Statement eine "sleep" Funktion eingebaut werden, welche nur ausgeführt wird wenn die gesetzten Bedingungen stimmen

### 2nd order Injection

- Der Angreiffer sendet den schadhaften Payload nicht direkt zum Zielsystem, sondern in ein Zwischensystem
- Der Payload kommt dann über Umwege irgendwann im Zielsystem an und wird dort ausgeführt
- Z.B.:
  - Angreifer sendet schadhaftes JavaScript an einen Server. Dieser speichert die Nachricht aus in die Logs (beispielsweise wird der JavaScript Code anstelle eines Usernamens gesendet. Das System schreibt also den "Usernamen" in die Logs, welcher bei der Authentifizierung gescheitert ist)
  - Als nächstes kommt ein Sysadmin mit einem Tool, über welches Log-Files im Browser dargestellt werden können
  - Er öffnet das Logfile in welches der schadhafte Payload geschrieben wurde
  - Da dort für den Usernamen JavaScript Code eingetragen ist, wird dieser im Browser des Opfers ausgeführt

## SQL Injections in der Wildnis



### SQLi attack vector components



### Basic SQL Query Syntax



### LDAP query injection





### OS command injection





### XML/XPATH injection



## Conclusions

### Injection defences



### Parameterizable API







