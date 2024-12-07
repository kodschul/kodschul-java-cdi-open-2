
# Java CDI Schulung: Einführung in CDI-Erweiterungen zur Anpassung des Frameworks

In dieser Schulung wird erläutert, wie CDI-Erweiterungen (Contexts and Dependency Injection) verwendet werden können, um das Java-Framework an spezifische Anforderungen anzupassen.

## Was sind CDI-Erweiterungen?

CDI-Erweiterungen sind ein Mechanismus, mit dem Entwickler das Verhalten des CDI-Frameworks zur Laufzeit erweitern oder modifizieren können. Sie bieten eine Möglichkeit, auf die Ereignisse des Lebenszyklus von CDI-Komponenten zu reagieren und benutzerdefinierte Logik hinzuzufügen.

### Vorteile von CDI-Erweiterungen:
- **Framework-Anpassung**: Passen Sie CDI an spezifische Projektanforderungen an.
- **Erweiterbarkeit**: Integrieren Sie neue Funktionalitäten ohne Änderungen am Framework-Kern.
- **Dynamische Komponentenregistrierung**: Fügen Sie beans dynamisch hinzu oder ändern Sie deren Metadaten.

## Aufbau einer CDI-Erweiterung

Eine CDI-Erweiterung wird durch die Implementierung der Schnittstelle `javax.enterprise.inject.spi.Extension` definiert.

### Schritte zum Erstellen einer CDI-Erweiterung:

1. **Erstellen der Erweiterungsklasse**:
   Erstellen Sie eine Klasse, die die `Extension`-Schnittstelle implementiert. Sie können Methoden definieren, die auf spezifische CDI-Lebenszyklusereignisse reagieren.

   Beispiel:
   ```java
   import javax.enterprise.event.Observes;
   import javax.enterprise.inject.spi.AfterBeanDiscovery;
   import javax.enterprise.inject.spi.Extension;

   public class CustomExtension implements Extension {
       public void afterBeanDiscovery(@Observes AfterBeanDiscovery event) {
           System.out.println("CDI-Erweiterung: Beans wurden entdeckt!");
       }
   }
   ```

2. **Registrieren der Erweiterung**:
   Fügen Sie die Erweiterung in der Datei `META-INF/services/javax.enterprise.inject.spi.Extension` hinzu.
   ```
   com.example.CustomExtension
   ```

3. **Anwendung starten**:
   Die CDI-Erweiterung wird automatisch während des CDI-Bootstraps geladen und ausgeführt.

### Häufig verwendete Ereignisse:
- **`BeforeBeanDiscovery`**: Wird vor der Erkennung von beans ausgelöst.
- **`AfterBeanDiscovery`**: Wird nach der Erkennung von beans ausgelöst, um zusätzliche beans hinzuzufügen.
- **`ProcessAnnotatedType<T>`**: Ermöglicht das Anpassen von Annotationen oder das Überspringen bestimmter beans.

Beispiel für die Anpassung eines beans:
```java
import javax.enterprise.event.Observes;
import javax.enterprise.inject.spi.Extension;
import javax.enterprise.inject.spi.ProcessAnnotatedType;

public class AnnotationModifierExtension implements Extension {
    public <T> void processAnnotatedType(@Observes ProcessAnnotatedType<T> pat) {
        System.out.println("Bearbeite: " + pat.getAnnotatedType().getJavaClass().getName());
        // Entfernen Sie eine spezifische Annotation
        if (pat.getAnnotatedType().isAnnotationPresent(Deprecated.class)) {
            pat.veto();
        }
    }
}
```

## Erweiterte Beispiele

### Dynamisches Hinzufügen von beans
Erstellen und registrieren Sie beans während der Laufzeit:
```java
import javax.enterprise.event.Observes;
import javax.enterprise.inject.spi.AfterBeanDiscovery;
import javax.enterprise.inject.spi.Extension;

public class DynamicBeanExtension implements Extension {
    public void addDynamicBean(@Observes AfterBeanDiscovery event) {
        event.addBean()
            .addTransitiveTypeClosure(String.class)
            .beanClass(String.class)
            .scope(javax.enterprise.context.ApplicationScoped.class)
            .produceWith(() -> "Dynamischer Bean-Wert");
    }
}
```

### Anpassen von Injection Points
Passen Sie Injection Points an, um benutzerdefinierte Logik bereitzustellen:
```java
import javax.enterprise.event.Observes;
import javax.enterprise.inject.spi.Extension;
import javax.enterprise.inject.spi.ProcessInjectionPoint;

public class InjectionPointExtension implements Extension {
    public <T, X> void processInjectionPoint(@Observes ProcessInjectionPoint<T, X> pip) {
        System.out.println("Injection Point erkannt: " + pip.getInjectionPoint().getType());
    }
}
```

## Fazit

CDI-Erweiterungen bieten eine flexible Möglichkeit, das Verhalten des CDI-Frameworks anzupassen. Durch die Implementierung von Erweiterungen können Entwickler beans dynamisch hinzufügen, Injection Points anpassen und das gesamte Lebenszyklusmanagement des Frameworks steuern. Dies ermöglicht es, komplexe Anforderungen auf elegante und modulare Weise zu erfüllen.
