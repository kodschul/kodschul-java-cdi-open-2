
# Java CDI Schulung: Direkte Interaktion mit dem Bean Manager

In dieser Schulung wird der Bean Manager, ein zentrales Element der Contexts and Dependency Injection (CDI) in Java, untersucht. Der Fokus liegt auf der direkten Interaktion mit dem Bean Manager und wie er für fortgeschrittene Dependency Injection (DI) Szenarien verwendet werden kann.

## Was ist der Bean Manager?

Der **Bean Manager** ist ein zentraler Bestandteil des CDI-Frameworks in Java. Er bietet eine API für die direkte Interaktion mit CDI-Beans und ermöglicht:
- Dynamische Auflösung und Instanziierung von Beans zur Laufzeit.
- Zugriff auf CDI-Metadaten, wie Qualifier und Scopes.
- Verwaltung von benutzerdefinierten Beans.

Der Bean Manager wird in der Regel verwendet, wenn statische Dependency Injection (z. B. mit `@Inject`) nicht ausreicht.

## Zugriff auf den Bean Manager

Der Zugriff auf den Bean Manager erfolgt über die CDI-API. Hier ein typisches Beispiel:
```java
import jakarta.enterprise.inject.spi.BeanManager;
import jakarta.naming.InitialContext;

public class BeanManagerExample {
    public static void main(String[] args) {
        try {
            BeanManager beanManager = (BeanManager) new InitialContext().lookup("java:comp/BeanManager");
            System.out.println("Bean Manager erfolgreich abgerufen: " + beanManager);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### Alternativ: Zugriff über `CDI` Utility
Das `jakarta.enterprise.inject.spi.CDI` Utility kann ebenfalls verwendet werden, um den Bean Manager zu erhalten:
```java
import jakarta.enterprise.inject.spi.CDI;

public class CDIExample {
    public static void main(String[] args) {
        BeanManager beanManager = CDI.current().getBeanManager();
        System.out.println("Bean Manager erfolgreich abgerufen: " + beanManager);
    }
}
```

## Dynamisches Auflösen von Beans

Der Bean Manager ermöglicht die dynamische Auflösung und Instanziierung von Beans zur Laufzeit.

### Beispiel: Dynamisches Auflösen einer Bean
```java
import jakarta.enterprise.inject.spi.Bean;
import jakarta.enterprise.inject.spi.BeanManager;
import jakarta.enterprise.util.AnnotationLiteral;
import jakarta.naming.InitialContext;

import java.util.Set;

public class DynamicBeanResolution {
    public static void main(String[] args) {
        try {
            BeanManager beanManager = (BeanManager) new InitialContext().lookup("java:comp/BeanManager");

            // Finden einer Bean basierend auf ihrem Typ
            Set<Bean<?>> beans = beanManager.getBeans(MyService.class);
            Bean<?> bean = beanManager.resolve(beans);

            // Erstellen einer Instanz der Bean
            MyService service = (MyService) beanManager.getReference(bean, MyService.class, beanManager.createCreationalContext(bean));
            service.performTask();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

class MyService {
    public void performTask() {
        System.out.println("Task ausgeführt!");
    }
}
```

### Erklärung der Schritte
1. **Auflösen von Beans**: `beanManager.getBeans(MyService.class)` sucht alle Beans des Typs `MyService`.
2. **Instanzierung der Bean**: Mit `beanManager.getReference` wird eine Instanz der Bean erstellt, die zur Laufzeit verwendet werden kann.

## Erstellung benutzerdefinierter Beans

Der Bean Manager ermöglicht die Registrierung und Verwaltung von benutzerdefinierten Beans. Diese Beans können dynamisch zur Laufzeit erstellt und registriert werden.

### Beispiel: Registrierung einer benutzerdefinierten Bean
```java
import jakarta.enterprise.inject.spi.BeanManager;
import jakarta.enterprise.inject.spi.CreationalContext;
import jakarta.enterprise.inject.spi.InjectionTarget;
import jakarta.naming.InitialContext;

public class CustomBeanExample {
    public static void main(String[] args) {
        try {
            BeanManager beanManager = (BeanManager) new InitialContext().lookup("java:comp/BeanManager");

            // Dynamische Bean-Definition
            InjectionTarget<MyCustomBean> injectionTarget = beanManager.createInjectionTarget(
                beanManager.createAnnotatedType(MyCustomBean.class)
            );

            CreationalContext<MyCustomBean> context = beanManager.createCreationalContext(null);
            MyCustomBean beanInstance = injectionTarget.produce(context);
            injectionTarget.inject(beanInstance, context);
            injectionTarget.postConstruct(beanInstance);

            beanInstance.customTask();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

class MyCustomBean {
    public void customTask() {
        System.out.println("Benutzerdefinierte Aufgabe ausgeführt!");
    }
}
```

## Anwendungsfälle für den Bean Manager

1. **Dynamisches Hinzufügen von Beans**:
   - Nützlich in Szenarien, in denen Beans erst zur Laufzeit bekannt sind.
2. **Programmgesteuerte Kontrolle**:
   - Ermöglicht die vollständige Kontrolle über den Lebenszyklus von Beans.
3. **Fortgeschrittene Dependency Injection**:
   - Ermöglicht die Implementierung komplexer DI-Logiken, die über die Standard-Annotationen hinausgehen.

## Fazit

Der Bean Manager ist ein leistungsstarkes Werkzeug innerhalb von CDI, das Entwicklern erweiterte Kontrollmöglichkeiten über Beans und deren Lebenszyklus bietet. Obwohl die direkte Interaktion mit dem Bean Manager in der Regel nicht erforderlich ist, ist sie in fortgeschrittenen Anwendungsfällen unverzichtbar. Durch das Verständnis der Bean Manager-API können Sie flexiblere und dynamischere Java-Anwendungen entwickeln.
