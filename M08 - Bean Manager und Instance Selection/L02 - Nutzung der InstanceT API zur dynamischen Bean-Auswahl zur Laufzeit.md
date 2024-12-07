
# Java CDI Schulung: Nutzung der Instance<T> API zur dynamischen Bean-Auswahl zur Laufzeit

In dieser Schulung lernen Sie, wie die `Instance<T>` API von Contexts and Dependency Injection (CDI) verwendet wird, um dynamisch Beans zur Laufzeit auszuwählen und zu verwalten. Dieses Feature bietet Flexibilität und erweiterte Kontrollmöglichkeiten in Java-Anwendungen.

## Was ist die Instance<T> API?

Die `Instance<T>` API ist ein CDI-Mechanismus, der es Entwicklern ermöglicht:
- Beans dynamisch zu instanziieren und zu verwalten.
- Zur Laufzeit zwischen verschiedenen Implementierungen einer Bean zu wählen.
- Beans mit Qualifiern dynamisch zu filtern.

### Vorteile der `Instance<T>` API
- **Flexibilität**: Beans können basierend auf Laufzeitbedingungen ausgewählt werden.
- **Vermeidung von Boilerplate-Code**: Ersetzt oft aufwändige manuelle Konfigurationen.
- **Einfache Integration**: Funktioniert nahtlos mit anderen CDI-Funktionen wie Qualifiern.

## Verwendung der Instance<T> API

### 1. Einfache Verwendung von Instance<T>
Mit der `@Inject` Annotation kann die `Instance<T>` API in eine Klasse eingebunden werden.

Beispiel:
```java
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;

public class BeanConsumer {
    @Inject
    private Instance<MyService> myServiceInstance;

    public void execute() {
        MyService myService = myServiceInstance.get();
        myService.performAction();
    }
}
```

In diesem Beispiel:
- Die `Instance<MyService>` API wird verwendet, um die Bean vom Typ `MyService` zu laden.
- Mit `myServiceInstance.get()` wird eine Instanz der Bean zur Laufzeit abgerufen.

### 2. Auswahl spezifischer Beans mit Qualifiern
Die `Instance<T>` API unterstützt die Verwendung von Qualifiern, um zwischen mehreren Implementierungen zu unterscheiden.

Beispiel:
```java
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;

public class QualifierExample {
    @Inject
    @CustomQualifier
    private Instance<MyService> qualifiedServiceInstance;

    public void execute() {
        MyService myService = qualifiedServiceInstance.get();
        myService.performAction();
    }
}
```

Hier:
- Der Qualifier `@CustomQualifier` wird verwendet, um eine spezifische Implementierung auszuwählen.

### 3. Iteration über verfügbare Beans
Die `Instance<T>` API ermöglicht es, über alle verfügbaren Beans eines Typs zu iterieren.

Beispiel:
```java
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;

public class BeanIterator {
    @Inject
    private Instance<MyService> myServiceInstances;

    public void executeAll() {
        for (MyService service : myServiceInstances) {
            service.performAction();
        }
    }
}
```

### 4. Dynamische Auswahl basierend auf Bedingungen
Mit der `Instance<T>` API können Sie dynamisch entscheiden, welche Bean zur Laufzeit verwendet werden soll.

Beispiel:
```java
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;

public class DynamicSelection {
    @Inject
    private Instance<MyService> myServiceInstances;

    public void execute(String condition) {
        for (MyService service : myServiceInstances) {
            if (service.supportsCondition(condition)) {
                service.performAction();
                break;
            }
        }
    }
}
```

## Praktische Übung

### Aufgabe: Dynamische Bean-Auswahl implementieren
1. Definieren Sie eine Schnittstelle `MyService` mit einer Methode `performAction()`.
2. Implementieren Sie zwei verschiedene Beans für `MyService`, z. B. `ServiceA` und `ServiceB`, mit spezifischen Qualifiern.
3. Verwenden Sie die `Instance<MyService>` API, um basierend auf einer Laufzeitbedingung die passende Implementierung auszuwählen und auszuführen.

Beispiel:
```java
@CustomQualifierA
public class ServiceA implements MyService {
    @Override
    public void performAction() {
        System.out.println("ServiceA wird ausgeführt.");
    }
}

@CustomQualifierB
public class ServiceB implements MyService {
    @Override
    public void performAction() {
        System.out.println("ServiceB wird ausgeführt.");
    }
}
```

In der Verbrauchsklasse:
```java
public class DynamicConsumer {
    @Inject
    private Instance<MyService> services;

    public void execute(String serviceType) {
        for (MyService service : services) {
            if (service.supportsServiceType(serviceType)) {
                service.performAction();
                return;
            }
        }
        System.out.println("Kein passender Service gefunden.");
    }
}
```

## Fazit

Die `Instance<T>` API ist ein leistungsstarkes Werkzeug in CDI, das Flexibilität und dynamische Kontrolle über Beans zur Laufzeit ermöglicht. Mit dieser API können komplexe Abhängigkeiten und Laufzeitentscheidungen elegant gelöst werden, wodurch Ihre Anwendungen modularer und wartungsfreundlicher werden.
