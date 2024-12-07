
# Java CDI Schulung: Einführung in Beans und deren Lebenszyklen

In dieser Schulung werden die Grundlagen von Contexts and Dependency Injection (CDI) in Java behandelt. Ein besonderer Schwerpunkt liegt auf Beans, ihren Typen und Lebenszyklen, sowie ihrer Rolle in der Java-Entwicklung.

## Einführung in Beans

### Was ist CDI?
Contexts and Dependency Injection (CDI) ist ein Standard in der Java-Welt, der die Entwicklung von robusten und wartbaren Anwendungen erleichtert. CDI bietet:
- **Dependency Injection (DI)**: Ermöglicht die Injektion von Abhängigkeiten in Java-Komponenten.
- **Lebenszyklus-Management**: Verwaltet automatisch die Lebenszyklen von Beans.
- **Kontextbezogene Instanzen**: Bindet Beans an bestimmte Lebenszyklen (Scopes).

### Was ist eine Bean?
Eine Bean ist eine verwaltete Komponente in CDI, die von einem Container verwaltet wird. Beans können verschiedene Aufgaben übernehmen, wie:
- Geschäftsfunktionen ausführen
- Daten speichern
- Andere Komponenten bereitstellen

#### Beispiel einer einfachen Bean:
```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class GreetingService {
    public String greet(String name) {
        return "Hallo, " + name + "!";
    }
}
```

### Vorteile von CDI-Beans
- **Lose Kopplung**: Durch Dependency Injection werden Klassen weniger stark voneinander abhängig.
- **Wiederverwendbarkeit**: Beans können in verschiedenen Teilen der Anwendung verwendet werden.
- **Erweiterbarkeit**: Durch CDI-Events und Interceptor können Beans leicht erweitert werden.

## Lebenszyklen von Beans

CDI bietet verschiedene Scopes, die den Lebenszyklus einer Bean bestimmen. Ein Scope definiert, wie lange eine Bean-Instanz verfügbar bleibt.

### Wichtige Scopes

#### 1. **`@ApplicationScoped`**
- **Beschreibung**: Die Bean wird einmal pro Anwendung erstellt und bleibt während der gesamten Laufzeit der Anwendung erhalten.
- **Verwendung**: Ideal für Singleton-Beans oder globale Dienste.
- **Beispiel**:
  ```java
  @ApplicationScoped
  public class GlobalService {
      // Globale Methoden und Daten
  }
  ```

#### 2. **`@SessionScoped`**
- **Beschreibung**: Die Bean ist für die Dauer einer Benutzer-Session verfügbar.
- **Verwendung**: Nützlich für benutzerspezifische Daten, wie Warenkörbe.
- **Beispiel**:
  ```java
  import jakarta.enterprise.context.SessionScoped;
  import java.io.Serializable;

  @SessionScoped
  public class UserSession implements Serializable {
      private String username;

      public String getUsername() {
          return username;
      }

      public void setUsername(String username) {
          this.username = username;
      }
  }
  ```

#### 3. **`@RequestScoped`**
- **Beschreibung**: Die Bean existiert nur während einer HTTP-Anfrage.
- **Verwendung**: Geeignet für kurzlebige Beans, wie Validierungs- oder Logging-Komponenten.
- **Beispiel**:
  ```java
  import jakarta.enterprise.context.RequestScoped;

  @RequestScoped
  public class RequestLogger {
      public void log(String message) {
          System.out.println("Request log: " + message);
      }
  }
  ```

#### 4. **`@Dependent`**
- **Beschreibung**: Die Bean hat keinen eigenen Lebenszyklus und wird immer dann neu erstellt, wenn sie injiziert wird.
- **Verwendung**: Für temporäre oder nicht wiederverwendbare Beans.
- **Beispiel**:
  ```java
  import jakarta.enterprise.context.Dependent;

  @Dependent
  public class TempService {
      // Kurzlebige Logik
  }
  ```

#### 5. **`@ConversationScoped`**
- **Beschreibung**: Die Bean bleibt während einer Konversation erhalten, die mehrere HTTP-Anfragen umfassen kann.
- **Verwendung**: Für mehrstufige Workflows, z. B. in einem Formularprozess.
- **Beispiel**:
  ```java
  import jakarta.enterprise.context.ConversationScoped;
  import java.io.Serializable;

  @ConversationScoped
  public class CheckoutProcess implements Serializable {
      // Logik für mehrstufige Checkout-Vorgänge
  }
  ```

## Praktische Übung

### Aufgabe: Implementieren Sie eine `@RequestScoped` Bean
Erstellen Sie eine Bean, die während einer HTTP-Anfrage die aktuelle Zeit protokolliert.

#### Lösung:
1. Definieren Sie die Bean:
   ```java
   import jakarta.enterprise.context.RequestScoped;
   import java.time.LocalDateTime;

   @RequestScoped
   public class TimeLogger {
       public void logTime() {
           System.out.println("Aktuelle Zeit: " + LocalDateTime.now());
       }
   }
   ```
2. Verwenden Sie die Bean in einer Servlet-Klasse:
   ```java
   import jakarta.inject.Inject;
   import java.io.IOException;
   import jakarta.servlet.http.HttpServlet;
   import jakarta.servlet.http.HttpServletRequest;
   import jakarta.servlet.http.HttpServletResponse;

   public class LogTimeServlet extends HttpServlet {
       @Inject
       private TimeLogger timeLogger;

       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
           timeLogger.logTime();
           resp.getWriter().write("Zeit wurde protokolliert!");
       }
   }
   ```

## Fazit

CDI-Beans sind ein wesentlicher Bestandteil der modernen Java-Entwicklung. Durch das Verständnis der verschiedenen Lebenszyklen und Scopes können Entwickler effizientere und skalierbare Anwendungen erstellen. CDI erleichtert die Verwaltung von Abhängigkeiten und fördert die Wiederverwendbarkeit von Komponenten.
