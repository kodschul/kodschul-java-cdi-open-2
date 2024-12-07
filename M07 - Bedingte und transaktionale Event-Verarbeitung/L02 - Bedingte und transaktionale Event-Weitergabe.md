
# Java CDI Schulung: Bedingte und transaktionale Event-Weitergabe

In dieser Schulung wird das Konzept der bedingten und transaktionalen Event-Weitergabe in Java Contexts and Dependency Injection (CDI) behandelt. Dabei lernen Sie, wie Ereignisse abhängig von Bedingungen oder Transaktionen in CDI gesteuert und ausgelöst werden.

## Bedingte Event-Weitergabe

Die bedingte Event-Weitergabe ermöglicht es, Ereignisse nur unter bestimmten Umständen an die entsprechenden Beobachter weiterzuleiten. CDI stellt dafür ein leistungsstarkes Mechanismus bereit.

### Verwendung von Qualifikatoren für bedingte Events
Mit **Qualifikatoren** können Sie Events kategorisieren und so steuern, welche Beobachter auf bestimmte Events reagieren.

#### Beispiel:
```java
import javax.enterprise.event.Event;
import javax.enterprise.event.Observes;
import javax.inject.Inject;
import javax.inject.Qualifier;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.METHOD})
@interface Critical {}

public class EventExample {

    @Inject
    @Critical
    private Event<String> criticalEvent;

    public void triggerEvent() {
        criticalEvent.fire("Critical event occurred!");
    }

    public void observeCriticalEvent(@Observes @Critical String event) {
        System.out.println("Observed critical event: " + event);
    }
}
```

### Dynamische Filterung von Events
Mit CDI können Sie dynamische Bedingungen für die Event-Weitergabe implementieren.

#### Beispiel mit einer Bedingung:
```java
public void observeFilteredEvent(@Observes String event) {
    if (event.contains("Important")) {
        System.out.println("Processing important event: " + event);
    }
}
```

## Transaktionale Event-Weitergabe

CDI bietet die Möglichkeit, Events an den Ausgang von Transaktionen zu binden. Dies stellt sicher, dass Events nur dann ausgelöst werden, wenn die umgebende Transaktion erfolgreich abgeschlossen wurde.

### Phasen der transaktionalen Events
CDI unterstützt verschiedene Phasen für die Event-Auslösung:
- **BEFORE_COMPLETION**: Vor dem Abschluss der Transaktion.
- **AFTER_COMPLETION**: Nach dem Abschluss der Transaktion.
- **AFTER_SUCCESS**: Nach erfolgreichem Abschluss der Transaktion.
- **AFTER_FAILURE**: Nach einem Transaktionsfehler.

#### Beispiel:
```java
import javax.enterprise.event.Event;
import javax.enterprise.event.Observes;
import javax.enterprise.event.TransactionPhase;
import javax.inject.Inject;

public class TransactionalEventExample {

    @Inject
    private Event<String> transactionalEvent;

    public void triggerEvent() {
        transactionalEvent.fire("Transaction-bound event fired!");
    }

    public void observeEventAfterSuccess(@Observes(during = TransactionPhase.AFTER_SUCCESS) String event) {
        System.out.println("Observed after successful transaction: " + event);
    }

    public void observeEventAfterFailure(@Observes(during = TransactionPhase.AFTER_FAILURE) String event) {
        System.out.println("Observed after transaction failure: " + event);
    }
}
```

### Anwendung von transaktionalen Events
1. **Validierung von Daten**: Nur Events, die auf erfolgreichen Transaktionen basieren, werden verarbeitet.
2. **Auditierung**: Nachverfolgung erfolgreicher oder fehlgeschlagener Transaktionen.
3. **Zustandsänderungen**: Änderungen in abhängigen Komponenten nur nach Transaktionsabschluss vornehmen.

## Best Practices

- **Verwendung von Qualifikatoren**: Gruppieren Sie Events, um sicherzustellen, dass nur relevante Beobachter angesprochen werden.
- **Transaktionen gezielt nutzen**: Verwenden Sie transaktionale Events sparsam, um unnötige Komplexität zu vermeiden.
- **Fehlerbehandlung**: Planen Sie, wie fehlgeschlagene Transaktionen und deren Events verarbeitet werden sollen.

## Fazit

Die bedingte und transaktionale Event-Weitergabe in Java CDI ermöglicht die flexible und sichere Steuerung von Ereignissen in einer Anwendung. Durch den gezielten Einsatz von Qualifikatoren und Transaktionsphasen können Sie Ihre Anwendung modular und robust gestalten.
