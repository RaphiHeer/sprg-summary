# SPRG 6 - Handling untrusted Input

## Validation

- "Validiere deinen inputs" ist ein so nützlicher Tipp wie "Wenn du Auto fährst, verhindere es einen Unfall zu bauen!"
- Was macht "valide Daten" aus?
  - Stammt von einem legitimen System / User
  - Hat eine angemessene Grösse
  - Ist lexikalisch Korrekt (-> enthält nur die unterstützten Zeichen / Tokens)
  - Hat eine Korrekte Syntax (ist im korrekten Format, stimmt mit einem gegebenen Schema überein)
  - Semantisch korrekt (sagt in der gegebenen Umgebung etwas aus, z.B.: es gibt den gegebenen Account für eine gegebene Transaktion)
- Definition von validen Daten ist Teil des Domain Models eines Systems
  - z.B.: Forma der Account Nummer, Unterstützte Währung, Account Typen
  - Einige davon können auch im Typsystem abgebildet werden, sofern eine angemessene Programmiersprache verwendet wird
    - -> Damit sind Datentypen gemeint
- Wen Validierung implementiert wird, tu das günstigste zuerst
  - **Ursprung prüfen**
  - **Grösse prüfen**
  - **Lexical prüfen**
  - **Syntax prüfen**
  - **Semantik prüfen**



## Encoding und Escaping

- **Encoding**

  - Logische Representation von Text -> Sequenz von binären Ziffern mit einem gut definierten Set
  - Normalerweise wird input codiert
  - Beispiele dafür sind:
    - Character encoding: UTF-8, UTF-16
    - Base64 um binäre Daten in Form von ASCII Text zu repräsentieren
    - HTML encoding, bei dem HTML-Control Characters umgewandelt werde
      - -> z.B.: < und > zu &amp;gt; und &amp;lt;

- **Escaping** ist ein Subset von Encoding

  - Es werden nur gewisse Zeichen oder Prefixes escaped
  - Typischerweise vor allem control characters, so dass diese als Daten interpretiert werden
  - Normalerweise wird auch output encodiert (oder Daten für den Parser)
  - Z.B.:
    - SQL: WHERE='O\\'Connor'
    - OS command: cd Folder\\ With\\ Space

- Beispiel für validierung:

  ```java
  private void validateAccountNo(String accountNo) throws ValidationException {
  	// Prüfen ob accountNo überhaupt einen Wert hat
  	if(accountNo == null || accountNo.length() == 0) {
  		throw new ValidationException("Account is required");
  	}
      
  	// Es sollten genau 11 Zeichen sein
  	else if(accountNo.length() != 11) {
  		throw new ValidationException("Account number should be 11 characters long");
  	}
  	
  	// Pattern der Nummer prüfen
  	else if(!accountNo.matches(ACCOUNT_NO_PATTERN)) {
  		throw new ValidationException("Account number is in invalid format");
  	}
      
      // Account sollte existieren
      else if(accountService.getAccountDetails(accountNo) == null) {
          throw new ValidationException("Account does not exists");
      }
      
      // Alles in Ordnung :-) 
  }
  ```

## Sanitization

- Entfernen von potentiell gefährlichen Zeichensequenzen vom Input
  - sanitize ist Englisch für desinfizieren -> also etwas "entfernen"
- Z.B.: von HTML um XSS zu vermeiden
  - Entfernen:
    - Script tags
    - Event Handler Attribute von tags
    - Attribute deren Wert mit "`javascript:`" beginnt
  - Trotz diesen Massnahmen finden Angreifer immer wieder alternativen für XSS
- Verwende regelmässig getestete sanitizer liberaries

## Validation vs. Encoding vs. Sanitizaion

- Strikte Validierungsregeln definieren normalerweise Encodings und Formate, die schädlichen Input ausschliessen (Allerdings nicht immer...)
- Sauberes Encoding und Escaping reduziert die Chancen auf eine Injiektion signifikant, vorallem bei Daten welche von Parsern verarbeitet werden
  - Codiere (encode) deine Daten bevor du sie persistierst
  - Escape deinen output bevor du ihn in einen parser gibst
    - -> im Grunde genommen ist der **Browser** auch ein **HTML Parser**
- Verwende sanitizaion sorgfälltig und verlass dich nur darauf, wenn es absolut notwendig ist
  - -> Beispielsweise enthält richt text input HTML, welches geparst werden sollte)