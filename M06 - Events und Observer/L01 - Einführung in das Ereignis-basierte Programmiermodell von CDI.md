
# Java CDI Schulung: Einführung in das Ereignis-basierte Programmiermodell von CDI

In dieser Schulung wird das ereignisbasierte Programmiermodell von CDI (Contexts and Dependency Injection) behandelt. CDI bietet eine leistungsstarke Möglichkeit, Komponenten in Java-Anwendungen zu entkoppeln und Interaktionen über Ereignisse zu organisieren.

## Was ist CDI?

Contexts and Dependency Injection (CDI) ist eine Spezifikation in der Java EE (Jakarta EE) Plattform, die für die Abhängigkeitsinjektion und die Verwaltung von Komponenten in einem kontextuellen Lebenszyklus verwendet wird.

CDI erleichtert:
- Die Implementierung lose gekoppelter Anwendungen.
- Die Verwaltung von Komponenten mit verschiedenen Lebenszyklen.
- Die Nutzung eines ereignisbasierten Ansatzes für die Interaktion zwischen Komponenten.

## Ereignis-basiertes Programmiermodell

Das Ereignis-basierte Modell von CDI ermöglicht es Komponenten, miteinander zu kommunizieren, ohne dass eine direkte Abhängigkeit zwischen ihnen besteht. Dies wird durch das Erstellen und Abhören von Ereignissen erreicht.

### 1. Senden von Ereignissen

Mit dem CDI-Mechanismus können Sie Ereignisse über die API `javax.enterprise.event.Event` auslösen.

#### Beispiel:
```java
import javax.enterprise.context.ApplicationScoped;
import javax.enterprise.event.Event;
import javax.inject.Inject;

@ApplicationScoped
public class EventProducer {

    @Inject
    private Event<String> event;

    public void fireEvent(String message) {
        event.fire(message);
        System.out.println("Ereignis gesendet: " + message);
    }
}
```

### 2. Abhören von Ereignissen

Empfängerkomponenten können Ereignisse abonnieren, indem sie die Methode mit der Annotation `@Observes` versehen.

#### Beispiel:
```java
import javax.enterprise.context.ApplicationScoped;
import javax.enterprise.event.Observes;

@ApplicationScoped
public class EventConsumer {

    public void handleEvent(@Observes String message) {
        System.out.println("Ereignis empfangen: " + message);
    }
}
```

### 3. Ablauf des Ereignisflusses

1. Der **Producer** sendet ein Ereignis mit der Methode `fire()`.
2. Der **Consumer** reagiert auf das Ereignis mit einer Methode, die die Annotation `@Observes` trägt.

#### Beispiel: Zusammenspiel von Producer und Consumer
```java
public class Main {

    public static void main(String[] args) {
        EventProducer producer = CDI.current().select(EventProducer.class).get();
        producer.fireEvent("Hallo CDI Ereignis!");
    }
}
```

Ausgabe:
```
Ereignis gesendet: Hallo CDI Ereignis!
Ereignis empfangen: Hallo CDI Ereignis!
```

## Vorteile des ereignis-basierten Modells

1. **Lose Kopplung**: Komponenten wissen nichts voneinander, was die Modularität und Testbarkeit verbessert.
2. **Flexibilität**: Einfache Möglichkeit, mehrere Listener für dasselbe Ereignis hinzuzufügen.
3. **Erweiterbarkeit**: Neue Ereignisse oder Listener können hinzugefügt werden, ohne bestehende Komponenten zu verändern.

## Praktische Übung

### Aufgabe:
1. Erstellen Sie eine CDI-Anwendung mit einem Ereignisproduzenten und zwei Verbrauchern.
2. Der erste Verbraucher protokolliert das Ereignis in die Konsole.
3. Der zweite Verbraucher speichert das Ereignis in einer Liste.

#### Beispiel:
Producer:
```java
@ApplicationScoped
public class MyEventProducer {

    @Inject
    private Event<String> event;

    public void sendEvent(String message) {
        event.fire(message);
    }
}
```

Consumer 1:
```java
@ApplicationScoped
public class ConsoleLogger {

    public void logEvent(@Observes String message) {
        System.out.println("ConsoleLogger: " + message);
    }
}
```

Consumer 2:
```java
@ApplicationScoped
public class EventStorage {

    private List<String> events = new ArrayList<>();

    public void storeEvent(@Observes String message) {
        events.add(message);
        System.out.println("Event gespeichert: " + message);
    }

    public List<String> getEvents() {
        return events;
    }
}
```

Hauptprogramm:
```java
public class Main {

    public static void main(String[] args) {
        MyEventProducer producer = CDI.current().select(MyEventProducer.class).get();
        producer.sendEvent("Ereignis 1");
        producer.sendEvent("Ereignis 2");
    }
}
```

## Fazit

Das ereignisbasierte Modell von CDI ist ein leistungsstarkes Werkzeug, um lose gekoppelte und modulare Anwendungen zu erstellen. Durch das Verständnis von Ereignisproduktion und -konsumierung können Entwickler flexible und skalierbare Anwendungen entwickeln.
