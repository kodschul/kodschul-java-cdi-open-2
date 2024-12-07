
# Java CDI Schulung: Nutzung von Events und Observern zur Entkopplung von Systemkomponenten

In dieser Schulung werden die grundlegenden Konzepte und Mechanismen von **Java Contexts and Dependency Injection (CDI)** behandelt. Der Fokus liegt auf der Nutzung von Events und Observern, um Systemkomponenten effizient zu entkoppeln.

## Einführung in Events und Observer in CDI

### Was sind Events und Observer in CDI?
- **Events**: Ereignisse, die innerhalb des Systems ausgelöst (gefeuert) werden, um andere Komponenten zu benachrichtigen.
- **Observer**: Komponenten, die auf bestimmte Ereignisse reagieren und spezifische Aktionen ausführen.

Durch die Verwendung von Events und Observern können Systemkomponenten lose gekoppelt werden, was die Wartbarkeit und Testbarkeit verbessert.

### Vorteile der Entkopplung durch Events und Observer:
- **Flexibilität**: Änderungen an einer Komponente erfordern keine Anpassungen an anderen Komponenten.
- **Wartbarkeit**: Der Code ist modularer und leichter zu verstehen.
- **Erweiterbarkeit**: Neue Funktionalitäten können hinzugefügt werden, ohne bestehende Logik zu beeinflussen.

## Grundlagen der Event-Nutzung in CDI

### Ein Event auslösen
Ein Event wird durch den `Event`-Mechanismus von CDI ausgelöst. Dazu wird ein `Event`-Objekt injiziert.

Beispiel:
```java
import jakarta.enterprise.event.Event;
import jakarta.inject.Inject;

public class OrderService {

    @Inject
    private Event<OrderEvent> orderEvent;

    public void placeOrder(Order order) {
        // Bestellung verarbeiten
        System.out.println("Bestellung wird bearbeitet: " + order);

        // Event auslösen
        orderEvent.fire(new OrderEvent(order));
    }
}

public class OrderEvent {
    private final Order order;

    public OrderEvent(Order order) {
        this.order = order;
    }

    public Order getOrder() {
        return order;
    }
}
```

### Einen Observer erstellen
Ein Observer ist eine Methode, die mit `@Observes` annotiert ist und automatisch aufgerufen wird, wenn ein entsprechendes Event ausgelöst wird.

Beispiel:
```java
import jakarta.enterprise.event.Observes;

public class NotificationService {

    public void onOrderPlaced(@Observes OrderEvent orderEvent) {
        // Benachrichtigung senden
        System.out.println("Benachrichtigung gesendet für Bestellung: " + orderEvent.getOrder());
    }
}
```

## Praktische Anwendung: Nutzung von Qualifizierern

### Warum Qualifizierer verwenden?
Qualifizierer erlauben es, spezifische Events voneinander zu unterscheiden. Dies ist nützlich, wenn verschiedene Event-Typen ähnliche Datenstrukturen verwenden.

### Ein Qualifizierer erstellen
Qualifizierer werden mit der Annotation `@Qualifier` definiert.

Beispiel:
```java
import jakarta.inject.Qualifier;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.METHOD})
public @interface HighPriority {}
```

### Qualifizierer anwenden
- Beim Auslösen eines Events:
```java
@Inject
@HighPriority
private Event<OrderEvent> highPriorityOrderEvent;

public void placeHighPriorityOrder(Order order) {
    highPriorityOrderEvent.fire(new OrderEvent(order));
}
```

- Beim Beobachten eines Events:
```java
public void onHighPriorityOrder(@Observes @HighPriority OrderEvent orderEvent) {
    System.out.println("Hochpriorisierte Bestellung verarbeitet: " + orderEvent.getOrder());
}
```

## Best Practices für Events und Observer

1. **Vermeiden von Geschäftslogik in Observers**:
   - Observers sollten nur koordinierende Aufgaben übernehmen und keine komplexe Geschäftslogik enthalten.

2. **Asynchrone Events für lange laufende Aufgaben**:
   - Verwenden Sie asynchrone Events (`Event.fireAsync`), um lange laufende Aufgaben auszuführen, ohne den Hauptprozess zu blockieren.

   Beispiel:
   ```java
   orderEvent.fireAsync(new OrderEvent(order))
       .thenAccept(event -> System.out.println("Asynchrone Verarbeitung abgeschlossen."));
   ```

3. **Modularisierung durch Events**:
   - Nutzen Sie Events, um die Kommunikation zwischen Modulen zu erleichtern und Abhängigkeiten zu reduzieren.

## Fazit

Die Nutzung von Events und Observern in CDI ist ein leistungsstarkes Mittel, um Systemkomponenten zu entkoppeln. Dies fördert die Flexibilität, Wartbarkeit und Erweiterbarkeit des Systems. Durch den gezielten Einsatz von Qualifizierern und Best Practices können Sie robuste und skalierbare Anwendungen entwickeln.
