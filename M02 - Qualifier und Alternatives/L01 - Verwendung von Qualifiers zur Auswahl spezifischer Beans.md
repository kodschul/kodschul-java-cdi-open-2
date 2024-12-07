
# Java CDI Schulung: Verwendung von Qualifiers zur Auswahl spezifischer Beans

In dieser Schulung wird die Verwendung von Qualifiers in CDI (Contexts and Dependency Injection) behandelt, um spezifische Beans auszuwählen und den Dependency Injection-Prozess gezielt zu steuern.

## Einführung in CDI Qualifiers

Qualifiers sind benutzerdefinierte Annotationen in CDI, mit denen Sie zwischen mehreren Implementierungen derselben Schnittstelle unterscheiden können. Sie erweitern die Standard-Dependency-Injection-Funktionalität, indem sie präzisieren, welche Bean in einer bestimmten Situation injiziert werden soll.

### Warum Qualifiers verwenden?

- **Mehrere Implementierungen**: Wenn mehrere Beans dieselbe Schnittstelle implementieren, können Sie mit Qualifiers die gewünschte Bean auswählen.
- **Klarheit und Wartbarkeit**: Mit Qualifiers wird der Code klarer, da explizit definiert wird, welche Implementierung verwendet wird.

## Erstellen und Verwenden von Qualifiers

### 1. Definition eines Qualifiers

Ein Qualifier ist eine benutzerdefinierte Annotation, die mit `@Qualifier` und `@Retention(RUNTIME)` sowie `@Target` annotiert ist.

#### Beispiel:
```java
import jakarta.inject.Qualifier;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

@Qualifier
@Retention(RUNTIME)
@Target({FIELD, TYPE, METHOD})
public @interface PaymentMethod {
    PaymentType value();

    enum PaymentType {
        CREDIT_CARD, PAYPAL
    }
}
```

In diesem Beispiel wird ein Qualifier namens `@PaymentMethod` definiert, der mit einem `PaymentType`-Wert (z. B. `CREDIT_CARD` oder `PAYPAL`) verwendet werden kann.

---

### 2. Annotieren von Beans mit einem Qualifier

Sobald der Qualifier definiert ist, können Sie ihn verwenden, um spezifische Beans zu kennzeichnen.

#### Beispiel:
```java
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
@PaymentMethod(PaymentMethod.PaymentType.CREDIT_CARD)
public class CreditCardPaymentProcessor implements PaymentProcessor {
    @Override
    public void processPayment(double amount) {
        System.out.println("Zahlung mit Kreditkarte: " + amount);
    }
}

@ApplicationScoped
@PaymentMethod(PaymentMethod.PaymentType.PAYPAL)
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void processPayment(double amount) {
        System.out.println("Zahlung mit PayPal: " + amount);
    }
}
```

Hier haben wir zwei Implementierungen der Schnittstelle `PaymentProcessor`, jeweils mit einem spezifischen Qualifier versehen.

---

### 3. Verwenden eines Qualifiers bei der Injection

Um eine bestimmte Bean zu injizieren, verwenden Sie den entsprechenden Qualifier.

#### Beispiel:
```java
import jakarta.inject.Inject;

public class PaymentService {

    @Inject
    @PaymentMethod(PaymentMethod.PaymentType.CREDIT_CARD)
    private PaymentProcessor creditCardProcessor;

    @Inject
    @PaymentMethod(PaymentMethod.PaymentType.PAYPAL)
    private PaymentProcessor paypalProcessor;

    public void processPayment(String method, double amount) {
        if ("credit".equalsIgnoreCase(method)) {
            creditCardProcessor.processPayment(amount);
        } else if ("paypal".equalsIgnoreCase(method)) {
            paypalProcessor.processPayment(amount);
        } else {
            System.out.println("Ungültige Zahlungsmethode");
        }
    }
}
```

In diesem Beispiel werden die entsprechenden Implementierungen von `PaymentProcessor` basierend auf dem Qualifier injiziert und dynamisch genutzt.

---

### 4. Dynamische Auswahl von Beans mit `Instance`

Manchmal ist es notwendig, Beans zur Laufzeit basierend auf einem dynamischen Parameter auszuwählen. In solchen Fällen können Sie `Instance` verwenden.

#### Beispiel:
```java
import jakarta.enterprise.inject.Instance;
import jakarta.inject.Inject;

public class DynamicPaymentService {

    @Inject
    @PaymentMethod(PaymentMethod.PaymentType.CREDIT_CARD)
    private Instance<PaymentProcessor> creditCardProcessorInstance;

    @Inject
    @PaymentMethod(PaymentMethod.PaymentType.PAYPAL)
    private Instance<PaymentProcessor> paypalProcessorInstance;

    public void processPayment(String method, double amount) {
        if ("credit".equalsIgnoreCase(method) && !creditCardProcessorInstance.isUnsatisfied()) {
            creditCardProcessorInstance.get().processPayment(amount);
        } else if ("paypal".equalsIgnoreCase(method) && !paypalProcessorInstance.isUnsatisfied()) {
            paypalProcessorInstance.get().processPayment(amount);
        } else {
            System.out.println("Ungültige Zahlungsmethode");
        }
    }
}
```

Mit `Instance` können Sie überprüfen, ob eine Bean verfügbar ist, und sie bei Bedarf zur Laufzeit abrufen.

---

## Best Practices

1. **Qualifiers sinnvoll benennen**: Wählen Sie aussagekräftige Namen für Ihre Qualifiers, um die Lesbarkeit und Verständlichkeit des Codes zu erhöhen.
2. **Vermeidung von Überspezifikation**: Verwenden Sie Qualifiers nur, wenn mehrere Implementierungen existieren.
3. **Kombination mit Stereotypen**: Verwenden Sie Qualifiers zusammen mit Stereotypen, um die Beans besser zu organisieren.

---

## Fazit

CDI-Qualifiers bieten eine flexible Möglichkeit, spezifische Beans in Anwendungen mit mehreren Implementierungen zu selektieren. Durch die Verwendung von Qualifiers wird der Dependency-Injection-Prozess präziser und wartbarer. Sie sind ein unverzichtbares Werkzeug für moderne Java-Enterprise-Anwendungen.
