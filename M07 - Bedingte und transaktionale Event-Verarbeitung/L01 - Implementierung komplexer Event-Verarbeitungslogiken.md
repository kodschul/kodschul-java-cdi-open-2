
# Java CDI Schulung: Implementierung komplexer Event-Verarbeitungslogiken

In dieser Schulung wird gezeigt, wie komplexe Event-Verarbeitungslogiken in Java mithilfe von Contexts and Dependency Injection (CDI) implementiert werden können. CDI ist eine leistungsstarke Java-API, die lose gekoppelte, ereignisbasierte Architekturen ermöglicht.

## Einführung in die Event-Verarbeitung mit CDI

### Was ist CDI?
Contexts and Dependency Injection (CDI) ist ein Standard für die Abhängigkeitsinjektion in Java EE und Jakarta EE. CDI bietet die Möglichkeit, Komponenten zu verwalten und Events zur Kommunikation zwischen Komponenten zu nutzen.

### Vorteile der Event-Verarbeitung mit CDI
- **Lose Kopplung**: Komponenten kommunizieren über Events, ohne direkt voneinander abhängig zu sein.
- **Erweiterbarkeit**: Neue Event-Typen und Listener können leicht hinzugefügt werden.
- **Asynchrone Verarbeitung**: Events können synchron oder asynchron verarbeitet werden.

## Event-Mechanismus in CDI

### Grundlagen
CDI ermöglicht es, Events mit der `javax.enterprise.event.Event`-Schnittstelle auszulösen und zu empfangen.

### Event-Typen
Ein Event ist ein einfacher Java-Typ. Jeder Event-Typ kann zusätzliche Daten enthalten, die für die Verarbeitung benötigt werden.

#### Beispiel: Definition eines Events
```java
public class OrderEvent {
    private String orderId;

    public OrderEvent(String orderId) {
        this.orderId = orderId;
    }

    public String getOrderId() {
        return orderId;
    }
}
```

## Auslösen und Empfangen von Events

### Auslösen eines Events
Events werden mit der `Event`-Schnittstelle ausgelöst. CDI sorgt dafür, dass alle Listener benachrichtigt werden.

#### Beispiel: Event auslösen
```java
import javax.enterprise.event.Event;
import javax.inject.Inject;

public class OrderService {
    @Inject
    private Event<OrderEvent> orderEvent;

    public void placeOrder(String orderId) {
        System.out.println("Bestellung aufgegeben: " + orderId);
        orderEvent.fire(new OrderEvent(orderId));
    }
}
```

### Empfangen eines Events
Listener für Events werden mit der Annotation `@Observes` registriert.

#### Beispiel: Event empfangen
```java
import javax.enterprise.event.Observes;

public class OrderListener {
    public void onOrderPlaced(@Observes OrderEvent event) {
        System.out.println("Event empfangen: Bestellung " + event.getOrderId() + " wurde aufgegeben.");
    }
}
```

## Komplexe Event-Verarbeitung

### Qualifizierte Events
Mit CDI können Qualifier verwendet werden, um Events zu unterscheiden. Dies ist nützlich, wenn unterschiedliche Listener für verschiedene Event-Typen zuständig sind.

#### Beispiel: Qualifier definieren
```java
import javax.inject.Qualifier;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
public @interface HighPriority {}
```

#### Beispiel: Qualifizierte Events
```java
public class NotificationService {
    @Inject
    @HighPriority
    private Event<OrderEvent> highPriorityOrderEvent;

    public void notifyHighPriorityOrder(String orderId) {
        highPriorityOrderEvent.fire(new OrderEvent(orderId));
    }
}
```

### Asynchrone Events
Asynchrone Events werden mit `Event.fireAsync()` ausgelöst, was nützlich ist, um lang laufende Aufgaben auszulagern.

#### Beispiel: Asynchrones Event
```java
public class AsyncOrderService {
    @Inject
    private Event<OrderEvent> orderEvent;

    public void processOrderAsync(String orderId) {
        orderEvent.fireAsync(new OrderEvent(orderId))
                  .thenAccept(event -> System.out.println("Asynchrones Event verarbeitet: " + event.getOrderId()));
    }
}
```

### Event-Verkettung
Komplexe Logiken können durch Verkettung von Events realisiert werden. Ein Listener kann ein neues Event auslösen, das von einem anderen Listener verarbeitet wird.

#### Beispiel: Event-Verkettung
```java
public class PaymentListener {
    public void onOrderPlaced(@Observes OrderEvent event) {
        System.out.println("Zahlung verarbeiten für Bestellung: " + event.getOrderId());
        // Neues Event auslösen
    }
}
```

## Fazit

CDI ist ein mächtiges Werkzeug zur Implementierung ereignisbasierter Architekturen. Mit seiner Unterstützung für lose Kopplung, qualifizierte und asynchrone Events können Entwickler komplexe Event-Verarbeitungslogiken erstellen, die flexibel und erweiterbar sind.
