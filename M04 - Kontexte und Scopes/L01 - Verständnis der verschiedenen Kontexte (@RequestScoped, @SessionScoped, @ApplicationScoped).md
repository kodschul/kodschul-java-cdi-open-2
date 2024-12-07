
# Java CDI Schulung: Verständnis der verschiedenen Kontexte

In dieser Schulung lernen Sie die verschiedenen Kontexte in Java Contexts and Dependency Injection (CDI) kennen, wie `@RequestScoped`, `@SessionScoped` und `@ApplicationScoped`. Diese Annotationen bestimmen den Lebenszyklus und den Geltungsbereich von Beans und sind essenziell für die effiziente Entwicklung von Java-Anwendungen.

## Was ist ein Kontext in CDI?

Ein Kontext in CDI definiert den Lebenszyklus und den Geltungsbereich eines Beans. Dies bedeutet:
- **Lebenszyklus**: Wann wird der Bean erstellt und zerstört?
- **Geltungsbereich**: Wo und wie lange ist der Bean verfügbar?

Die wichtigsten Scopes in CDI sind:
- `@RequestScoped`
- `@SessionScoped`
- `@ApplicationScoped`

### Allgemeine Syntax für Beans
```java
import javax.enterprise.context.RequestScoped;

@RequestScoped
public class MyBean {
    // Bean-Logik
}
```

## @RequestScoped: Kontext für eine Anfrage

Ein Bean mit dem Scope `@RequestScoped` ist für die Dauer einer HTTP-Anfrage aktiv. Sobald die Anfrage abgeschlossen ist, wird der Bean zerstört.

### Anwendungsfälle
- Beans, die spezifisch für eine einzelne HTTP-Anfrage benötigt werden.
- Beispiele: Formulardatenverarbeitung, Validierung.

### Beispiel:
```java
import javax.enterprise.context.RequestScoped;

@RequestScoped
public class RequestBean {
    public String getMessage() {
        return "Dies ist eine Anfrage-scoped Bean.";
    }
}
```
- **Lebenszyklus**: Der Bean wird bei einer neuen HTTP-Anfrage erstellt und nach Abschluss zerstört.

## @SessionScoped: Kontext für eine Benutzersitzung

Ein Bean mit dem Scope `@SessionScoped` bleibt für die Dauer einer Benutzersitzung erhalten. Eine Sitzung beginnt in der Regel mit der ersten HTTP-Anfrage eines Benutzers und endet mit dem Ablauf der Sitzung oder durch explizite Abmeldung.

### Anwendungsfälle
- Beans, die Benutzerdaten über mehrere Anfragen hinweg speichern.
- Beispiele: Benutzerprofil, Warenkorb in einem Onlineshop.

### Beispiel:
```java
import javax.enterprise.context.SessionScoped;
import java.io.Serializable;

@SessionScoped
public class SessionBean implements Serializable {
    private String username;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}
```
- **Lebenszyklus**: Der Bean bleibt für die gesamte Sitzung des Benutzers bestehen.

## @ApplicationScoped: Kontext für die gesamte Anwendung

Ein Bean mit dem Scope `@ApplicationScoped` bleibt während der gesamten Laufzeit der Anwendung bestehen. Es wird beim Starten der Anwendung erstellt und erst beim Beenden zerstört.

### Anwendungsfälle
- Beans, die globale Ressourcen oder Konfigurationen verwalten.
- Beispiele: Cache-Management, globale Dienste.

### Beispiel:
```java
import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class ApplicationBean {
    public String getAppInfo() {
        return "Dies ist eine application-scoped Bean.";
    }
}
```
- **Lebenszyklus**: Der Bean wird bei Start der Anwendung erstellt und beim Beenden zerstört.

## Vergleich der Scopes

| Scope            | Lebensdauer                           | Anwendungsfall                                   |
|-------------------|---------------------------------------|-------------------------------------------------|
| `@RequestScoped`  | Während einer einzelnen HTTP-Anfrage | Verarbeitung von Formulardaten                 |
| `@SessionScoped`  | Während einer Benutzersitzung        | Speicherung benutzerspezifischer Daten         |
| `@ApplicationScoped` | Während der gesamten Anwendungslaufzeit | Verwaltung globaler Konfigurationen oder Dienste |

## Fazit

Das Verständnis der verschiedenen Scopes in CDI ist entscheidend für die effektive Verwaltung von Beans in Java-Anwendungen. Durch die korrekte Verwendung von `@RequestScoped`, `@SessionScoped` und `@ApplicationScoped` können Sie die Ressourcen Ihrer Anwendung effizient nutzen und den Code besser strukturieren.
