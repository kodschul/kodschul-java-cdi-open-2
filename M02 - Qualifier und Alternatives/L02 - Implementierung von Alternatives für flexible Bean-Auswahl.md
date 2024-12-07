
# Java CDI Schulung: Implementierung von Alternatives für flexible Bean-Auswahl

In dieser Schulung lernen Sie, wie Sie mit der CDI (Contexts and Dependency Injection) Funktionalität `@Alternative` flexible Bean-Auswahlen in Java implementieren können. Diese Funktionalität ermöglicht es, verschiedene Implementierungen derselben Schnittstelle zu verwenden, je nach Kontext oder Konfigurationsbedarf.

## Einführung in Alternatives

### Was sind Alternatives in CDI?
`@Alternative` ist eine Annotation in Java CDI, die es ermöglicht, alternative Implementierungen einer Bean bereitzustellen. Nur eine der als `@Alternative` markierten Beans wird im finalen Deployment aktiv sein, basierend auf der CDI-Konfiguration.

### Anwendungsfälle für Alternatives
- **Test- und Produktionsumgebungen**: Bereitstellung unterschiedlicher Implementierungen für Tests und die Produktion.
- **Feature-Toggles**: Aktivieren oder Deaktivieren von Features durch den Wechsel zwischen verschiedenen Implementierungen.
- **Flexible Konfiguration**: Verwendung einer spezifischen Implementierung basierend auf Konfigurationsparametern oder Deployment-Profilen.

## Implementierung von Alternatives

### Schritt 1: Definition einer Schnittstelle
Erstellen Sie eine gemeinsame Schnittstelle, die von allen Implementierungen verwendet wird.

```java
public interface PaymentService {
    void processPayment(double amount);
}
```

### Schritt 2: Implementierung der Beans
Erstellen Sie verschiedene Implementierungen der Schnittstelle und markieren Sie sie mit `@Alternative`.

**Produktionsimplementierung:**
```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Named;

@ApplicationScoped
@Named
public class ProductionPaymentService implements PaymentService {
    @Override
    public void processPayment(double amount) {
        System.out.println("Zahlung in der Produktion verarbeitet: " + amount);
    }
}
```

**Testimplementierung:**
```java
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.inject.Alternative;

@Alternative
@ApplicationScoped
public class TestPaymentService implements PaymentService {
    @Override
    public void processPayment(double amount) {
        System.out.println("Testzahlung verarbeitet: " + amount);
    }
}
```

### Schritt 3: Aktivieren einer Alternative
Um eine `@Alternative` zu aktivieren, müssen Sie sie in der Datei `beans.xml` deklarieren. Dies gibt Ihnen die Kontrolle darüber, welche Implementierung verwendet wird.

**Beispiel für beans.xml:**
```xml
<beans xmlns="https://jakarta.ee/xml/ns/jakartaee"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/beans_4_0.xsd"
       version="4.0">
    <alternatives>
        <class>com.example.TestPaymentService</class>
    </alternatives>
</beans>
```

### Schritt 4: Nutzung der Bean
Verwenden Sie `@Inject`, um die gewünschte Implementierung in Ihre Klassen einzufügen. Die aktivierte Alternative wird automatisch bereitgestellt.

```java
import jakarta.inject.Inject;

public class PaymentProcessor {
    @Inject
    private PaymentService paymentService;

    public void process(double amount) {
        paymentService.processPayment(amount);
    }
}
```

## Best Practices

- **Konsistenz in Test- und Produktionscode**: Verwenden Sie `@Alternative`, um sicherzustellen, dass die Codebasis konsistent bleibt, unabhängig von der Umgebung.
- **Modularität fördern**: Gruppieren Sie alternative Implementierungen in separate Module oder Pakete, um die Wartbarkeit zu verbessern.
- **Dokumentation der beans.xml**: Halten Sie die Konfigurationsdatei übersichtlich, besonders wenn mehrere `@Alternative` Beans vorhanden sind.

## Fazit

Die Verwendung von `@Alternative` in Java CDI ist eine leistungsstarke Methode, um flexible Bean-Auswahlen zu implementieren. Sie ermöglicht es Entwicklern, unterschiedliche Implementierungen basierend auf Kontexten oder Konfigurationsanforderungen zu aktivieren und zu verwenden. Dies fördert nicht nur die Wiederverwendbarkeit, sondern auch die Wartbarkeit und Modularität des Codes.
