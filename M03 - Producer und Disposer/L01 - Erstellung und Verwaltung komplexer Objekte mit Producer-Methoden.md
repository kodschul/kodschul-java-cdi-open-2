
# Java CDI Schulung: Erstellung und Verwaltung komplexer Objekte mit Producer-Methoden

In dieser Schulung lernen Sie, wie Sie mit CDI (Contexts and Dependency Injection) komplexe Objekte erstellen und verwalten können, indem Sie Producer-Methoden einsetzen. Producer-Methoden sind eine flexible Möglichkeit, um Abhängigkeiten zu erzeugen und zu konfigurieren.

## Einführung in CDI und Producer-Methoden

CDI ist ein Standard für Dependency Injection in Java EE und Jakarta EE. Es ermöglicht die lose Kopplung von Komponenten und verbessert die Testbarkeit und Wartbarkeit von Anwendungen.

### Was sind Producer-Methoden?
Producer-Methoden sind spezielle Methoden, die komplexe Objekte erzeugen und in den CDI-Kontext einfügen. Sie ermöglichen:
- Die Erstellung von Objekten, die nicht direkt von CDI verwaltet werden können (z. B. externe Bibliotheken).
- Eine flexible und dynamische Konfiguration von Objekten.
- Die Anpassung der Objektinstanz basierend auf spezifischen Anforderungen.

### Beispiel:
```java
import javax.enterprise.context.ApplicationScoped;
import javax.enterprise.inject.Produces;
import java.text.SimpleDateFormat;
import java.util.Date;

@ApplicationScoped
public class DateProducer {

    @Produces
    public SimpleDateFormat produceDateFormat() {
        return new SimpleDateFormat("yyyy-MM-dd");
    }

    @Produces
    public Date produceCurrentDate() {
        return new Date();
    }
}
```

In diesem Beispiel:
- `produceDateFormat` erzeugt ein `SimpleDateFormat`-Objekt.
- `produceCurrentDate` erzeugt ein aktuelles Datum.

Diese Objekte können anschließend von anderen CDI-kompatiblen Klassen injiziert werden.

## Erstellung komplexer Objekte mit Producer-Methoden

### Schritt 1: Definieren der Producer-Methode
Eine Producer-Methode wird mit der Annotation `@Produces` versehen. Sie kann in jeder CDI-verwalteten Klasse definiert werden.

Beispiel:
```java
@Produces
public MyService createService() {
    MyService service = new MyService();
    service.configure("Param1", "Param2");
    return service;
}
```

### Schritt 2: Verwenden des erzeugten Objekts
Das von der Producer-Methode erzeugte Objekt kann mit `@Inject` in andere Klassen injiziert werden.
```java
@Inject
private MyService myService;

public void useService() {
    myService.performAction();
}
```

### Anwendungsfall: Erstellung eines konfigurierten Objekts
Wenn ein Objekt mit spezifischen Parametern oder Konfigurationen erstellt werden muss:
```java
@Produces
public DatabaseConnection createDatabaseConnection() {
    DatabaseConnection connection = new DatabaseConnection();
    connection.setUrl("jdbc:mysql://localhost:3306/mydb");
    connection.setUsername("user");
    connection.setPassword("password");
    return connection;
}
```

## Verwaltung von Objekten mit Scopes

CDI bietet verschiedene Scopes, um die Lebensdauer von Objekten zu verwalten:
- **`@ApplicationScoped`**: Ein Objekt wird einmal pro Anwendung erstellt.
- **`@RequestScoped`**: Ein Objekt wird einmal pro HTTP-Request erstellt.
- **`@SessionScoped`**: Ein Objekt wird einmal pro HTTP-Session erstellt.

Beispiel:
```java
@ApplicationScoped
public class ServiceProducer {

    @Produces
    @RequestScoped
    public UserSession produceUserSession() {
        return new UserSession();
    }
}
```

In diesem Beispiel wird `UserSession` für jeden Request neu erstellt.

## Erweiterte Konfiguration mit Qualifiern

Mit Qualifier-Annotationen können verschiedene Instanzen des gleichen Typs erzeugt und injiziert werden.

### Beispiel:
```java
@Qualifier
@Retention(RUNTIME)
@Target({FIELD, METHOD, PARAMETER, TYPE})
public @interface ConfigType {
    String value();
}
```

Produzieren Sie verschiedene Instanzen basierend auf dem Qualifier:
```java
@Produces
@ConfigType("development")
public Config createDevConfig() {
    return new Config("dev-db-url", "dev-user", "dev-pass");
}

@Produces
@ConfigType("production")
public Config createProdConfig() {
    return new Config("prod-db-url", "prod-user", "prod-pass");
}
```

Verwenden Sie die spezifische Instanz:
```java
@Inject
@ConfigType("production")
private Config prodConfig;
```

## Fazit

Producer-Methoden in CDI bieten eine leistungsstarke Möglichkeit, komplexe Objekte zu erstellen und zu verwalten. Durch die Kombination von Producer-Methoden, Scopes und Qualifiern können Entwickler flexible und wartbare Lösungen für die Abhängigkeitsverwaltung implementieren. Diese Techniken tragen dazu bei, die Wiederverwendbarkeit und Anpassbarkeit des Codes zu verbessern.
