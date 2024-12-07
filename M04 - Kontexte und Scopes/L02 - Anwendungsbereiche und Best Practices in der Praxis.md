
# Java CDI Schulung: Anwendungsbereiche und Best Practices in der Praxis

In dieser Schulung wird der praktische Einsatz von Contexts and Dependency Injection (CDI) in Java erläutert. CDI ist eine leistungsstarke Technologie für die Erstellung von modularen und wartbaren Anwendungen. Hier erfahren Sie mehr über die Anwendungsbereiche und Best Practices für CDI in der Praxis.

## Anwendungsbereiche von CDI

### 1. Dependency Injection (DI)
CDI ermöglicht eine einfache und effektive Verwaltung von Abhängigkeiten zwischen Klassen. Mit CDI können Objekte automatisch instanziiert und bereitgestellt werden, wodurch Boilerplate-Code reduziert wird.

Beispiel:
```java
import javax.inject.Inject;

public class OrderService {

    @Inject
    private PaymentService paymentService;

    public void processOrder() {
        paymentService.pay();
    }
}
```

### 2. Context Management
CDI bietet Unterstützung für verschiedene Lebenszykluskontexte, wie z. B.:
- **Request Scoped**: Für die Dauer einer HTTP-Anfrage.
- **Session Scoped**: Für die Dauer einer Benutzersitzung.
- **Application Scoped**: Über die gesamte Lebensdauer der Anwendung.

Beispiel für eine `@RequestScoped` Bean:
```java
import javax.enterprise.context.RequestScoped;

@RequestScoped
public class UserSession {
    private String username;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}
```

### 3. Interceptors und Dekoratoren
Mit CDI können Interceptors und Dekoratoren verwendet werden, um Funktionen wie Logging, Sicherheitsprüfungen oder Transaktionsmanagement zu implementieren.

Beispiel für einen Interceptor:
```java
import javax.interceptor.AroundInvoke;
import javax.interceptor.Interceptor;
import javax.interceptor.InvocationContext;

@Interceptor
@Logging
public class LoggingInterceptor {

    @AroundInvoke
    public Object logMethodCall(InvocationContext context) throws Exception {
        System.out.println("Methode aufgerufen: " + context.getMethod().getName());
        return context.proceed();
    }
}
```

### 4. Event Handling
CDI unterstützt ein flexibles Event-System, mit dem Klassen Ereignisse auslösen und darauf reagieren können.

Beispiel für das Auslösen eines Events:
```java
import javax.enterprise.event.Event;
import javax.inject.Inject;

public class OrderProcessor {

    @Inject
    private Event<OrderEvent> event;

    public void processOrder(Order order) {
        event.fire(new OrderEvent(order));
    }
}
```

Beispiel für das Abfangen eines Events:
```java
import javax.enterprise.event.Observes;

public class OrderListener {

    public void onOrderProcessed(@Observes OrderEvent event) {
        System.out.println("Order processed: " + event.getOrder());
    }
}
```

## Best Practices in der Praxis

### 1. Minimieren Sie den Umfang von Beans
Verwenden Sie den kleinstmöglichen Kontext für Ihre Beans, um Speicherverbrauch und potenzielle Fehler zu minimieren:
- **`@RequestScoped`** für HTTP-Anfragen.
- **`@ApplicationScoped`** für allgemeine, langlebige Dienste.

### 2. Verwenden Sie Qualifier für spezifische Abhängigkeiten
Nutzen Sie Qualifier, um zwischen verschiedenen Implementierungen einer Schnittstelle zu unterscheiden:
```java
import javax.inject.Qualifier;

@Qualifier
@Retention(RUNTIME)
@Target({ FIELD, TYPE, METHOD })
public @interface Premium {}

@Inject
@Premium
private PaymentService premiumPaymentService;
```

### 3. Modularisieren Sie Ihre Beans
Teilen Sie Ihre Anwendung in Module auf, die jeweils spezifische Aufgaben erfüllen, und kapseln Sie Logik innerhalb von Beans.

### 4. Logging und Fehlerbehandlung
Setzen Sie Interceptors ein, um Logging und Fehlerbehandlung konsistent zu implementieren:
```java
@Interceptor
@ErrorHandler
public class ErrorHandlerInterceptor {

    @AroundInvoke
    public Object handleErrors(InvocationContext context) {
        try {
            return context.proceed();
        } catch (Exception e) {
            System.err.println("Fehler in Methode: " + context.getMethod().getName());
            throw e;
        }
    }
}
```

### 5. Einheitliche Konfiguration
Verwenden Sie CDI-Konfigurationsmechanismen, um Änderungen zentral verwalten zu können.

### 6. Tests und Mocking
Nutzen Sie CDI-fähige Test-Frameworks wie Arquillian oder Weld, um Ihre Beans in einer kontrollierten Umgebung zu testen.

## Fazit

Java CDI ist ein mächtiges Framework, das eine saubere und modulare Architektur unterstützt. Mit seinen Features wie Dependency Injection, Context Management, Interceptors und Event Handling bietet es eine solide Grundlage für die Entwicklung wartbarer und skalierbarer Anwendungen. Die Einhaltung von Best Practices hilft dabei, das Beste aus CDI herauszuholen und die Entwicklungseffizienz zu steigern.
