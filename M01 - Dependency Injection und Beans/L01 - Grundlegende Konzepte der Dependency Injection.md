
# Java CDI Schulung: Grundlegende Konzepte der Dependency Injection

Diese Schulung behandelt die grundlegenden Konzepte der Dependency Injection (DI) in Java, mit besonderem Fokus auf Contexts and Dependency Injection (CDI), dem Standard für DI in Java EE und Jakarta EE.

## Was ist Dependency Injection?

Dependency Injection ist ein Designprinzip, bei dem Objekte ihre Abhängigkeiten nicht selbst erstellen, sondern von einem externen System bereitgestellt bekommen. Dies verbessert die Modularität und Testbarkeit von Code.

### Vorteile von Dependency Injection
- **Entkopplung**: Komponenten sind weniger voneinander abhängig und können leichter ausgetauscht oder wiederverwendet werden.
- **Testbarkeit**: Durch DI können Abhängigkeiten einfach durch Mock-Objekte ersetzt werden.
- **Klarheit**: Der Code wird übersichtlicher, da die Erstellung von Abhängigkeiten an einem zentralen Ort erfolgt.

### Grundlegendes Beispiel ohne DI
```java
public class Service {
    private Repository repository;

    public Service() {
        this.repository = new Repository(); // Direkte Abhängigkeit
    }

    public void performAction() {
        repository.save();
    }
}
```

### Mit Dependency Injection
```java
public class Service {
    private final Repository repository;

    // Abhängigkeit wird injiziert
    public Service(Repository repository) {
        this.repository = repository;
    }

    public void performAction() {
        repository.save();
    }
}
```

In diesem Beispiel wird die Abhängigkeit `Repository` durch DI bereitgestellt, wodurch die Klasse `Service` unabhängig von der Implementierung des `Repository` wird.

## Einführung in Contexts and Dependency Injection (CDI)

CDI ist der Java-Standard für Dependency Injection und bietet einen einheitlichen Ansatz zur Verwaltung von Abhängigkeiten in Anwendungen. Es wird häufig in Java EE und Jakarta EE verwendet.

### Kernkonzepte von CDI

1. **Managed Beans**:
   - CDI verwaltet die Lebenszyklen von Objekten, die als "Beans" bezeichnet werden.
   - Beans werden mit Standard-Java-Klassen implementiert und durch CDI-Annotations definiert.

   Beispiel:
   ```java
   import jakarta.inject.Named;

   @Named
   public class MyBean {
       public String sayHello() {
           return "Hallo aus CDI!";
       }
   }
   ```

2. **Injection Points**:
   - Injection Points sind Stellen im Code, an denen CDI eine Abhängigkeit bereitstellt.
   - Die Annotation `@Inject` kennzeichnet ein Injection Point.

   Beispiel:
   ```java
   import jakarta.inject.Inject;

   public class Service {
       @Inject
       private Repository repository;

       public void performAction() {
           repository.save();
       }
   }
   ```

3. **Scopes**:
   - CDI bietet verschiedene Scopes, um die Lebensdauer von Beans zu definieren.
   - Beispiele:
     - `@RequestScoped`: Bean existiert während einer HTTP-Anfrage.
     - `@SessionScoped`: Bean existiert während einer Benutzersitzung.
     - `@ApplicationScoped`: Bean existiert während der gesamten Laufzeit der Anwendung.

4. **Producer Methods**:
   - Producer-Methoden bieten eine Möglichkeit, benutzerdefinierte Objekte als CDI-Beans bereitzustellen.

   Beispiel:
   ```java
   import jakarta.enterprise.inject.Produces;

   public class ConfigProducer {
       @Produces
       public Config createConfig() {
           return new Config("example");
       }
   }
   ```

5. **Qualifiers**:
   - Qualifiers werden verwendet, um zwischen mehreren Implementierungen derselben Abhängigkeit zu unterscheiden.

   Beispiel:
   ```java
   import jakarta.inject.Qualifier;

   @Qualifier
   @Retention(RUNTIME)
   @Target({ FIELD, METHOD, PARAMETER, TYPE })
   public @interface Special {}

   @Special
   public class SpecialRepository implements Repository {
       // Spezielle Implementierung
   }
   ```

6. **Interceptors und Decorators**:
   - Interceptors und Decorators erweitern die Funktionalität von Beans, ohne deren Code zu ändern.

## Fazit

CDI bietet ein leistungsstarkes Framework zur Implementierung von Dependency Injection in Java-Anwendungen. Durch die Verwendung von Managed Beans, Injection Points, Scopes und weiteren Features können Entwickler modularen, wartbaren und testbaren Code erstellen. Diese Konzepte bilden die Grundlage für moderne Java EE- und Jakarta EE-Anwendungen.
