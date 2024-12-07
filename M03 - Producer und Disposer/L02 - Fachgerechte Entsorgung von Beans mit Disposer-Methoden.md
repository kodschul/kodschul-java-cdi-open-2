
# Java CDI Schulung: Fachgerechte Entsorgung von Beans mit Disposer-Methoden

In dieser Schulung lernen Sie, wie Sie Beans in Java Contexts and Dependency Injection (CDI) fachgerecht mit Disposer-Methoden entsorgen. Disposer-Methoden ermöglichen es Entwicklern, Ressourcen freizugeben oder bestimmte Bereinigungsaktionen durchzuführen, wenn eine Bean nicht mehr benötigt wird.

## Was sind Disposer-Methoden?

Disposer-Methoden in CDI sind spezielle Methoden, die definiert werden, um Beans zu zerstören oder aufzuräumen, wenn sie nicht mehr verwendet werden. Sie bieten eine saubere Möglichkeit, Ressourcen zu verwalten, die von einer Bean genutzt werden.

### Einsatzgebiete von Disposer-Methoden
- **Schließen von Datenbankverbindungen**
- **Freigeben von Dateihandles**
- **Bereinigen von Caches oder Buffern**
- **Stoppen von Hintergrund-Threads oder Tasks**

## Aufbau einer Disposer-Methode

Eine Disposer-Methode wird mit der CDI-Annotation `@Disposes` gekennzeichnet. Sie wird automatisch aufgerufen, wenn die zugehörige Bean aus dem CDI-Kontext entfernt wird.

### Syntax einer Disposer-Methode
```java
import javax.enterprise.inject.Disposes;

public class ResourceProducer {

    // Producer-Methode zum Erstellen der Bean
    public Connection createConnection() {
        // Beispiel: Erstellen einer Datenbankverbindung
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/db", "user", "password");
    }

    // Disposer-Methode zur Freigabe der Ressourcen
    public void closeConnection(@Disposes Connection connection) {
        try {
            connection.close();
            System.out.println("Datenbankverbindung geschlossen.");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### Wichtige Punkte:
1. **Parameter mit `@Disposes`**:
   Der Parameter, der entsorgt werden soll, wird mit der Annotation `@Disposes` markiert. Die Disposer-Methode wird automatisch für die Bean aufgerufen, die von der zugehörigen Producer-Methode erstellt wurde.

2. **Ressourcenverwaltung**:
   Alle Aufräumarbeiten, die mit der Bean verbunden sind, sollten innerhalb der Disposer-Methode durchgeführt werden.

## Beispielanwendung: Dateioperationen

### Producer- und Disposer-Methoden für einen `FileWriter`
```java
import javax.enterprise.inject.Disposes;
import javax.enterprise.inject.Produces;
import java.io.FileWriter;
import java.io.IOException;

public class FileWriterProducer {

    // Producer-Methode: Erstellt einen FileWriter
    @Produces
    public FileWriter createFileWriter() throws IOException {
        return new FileWriter("output.txt");
    }

    // Disposer-Methode: Schließt den FileWriter
    public void disposeFileWriter(@Disposes FileWriter writer) {
        try {
            writer.close();
            System.out.println("FileWriter wurde geschlossen.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Verwendung der Bean
```java
import javax.inject.Inject;
import java.io.FileWriter;
import java.io.IOException;

public class FileOperation {

    @Inject
    private FileWriter writer;

    public void writeToFile(String data) {
        try {
            writer.write(data);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Ablauf:
1. Die Bean `FileWriter` wird durch die Producer-Methode erzeugt.
2. Nach der Verwendung wird die Disposer-Methode automatisch aufgerufen, um den `FileWriter` zu schließen.

## Vorteile der Verwendung von Disposer-Methoden

- **Saubere Ressourcennutzung**: Ressourcen wie Datenbankverbindungen, Netzwerk-Sockets oder Dateihandles werden zuverlässig freigegeben.
- **Automatisierung**: Entwickler müssen sich nicht manuell um das Freigeben von Ressourcen kümmern.
- **Vermeidung von Speicherlecks**: Durch die automatische Freigabe der Beans können Speicherlecks vermieden werden.

## Best Practices

1. **Producer- und Disposer-Methoden im gleichen Scope**:
   Producer- und Disposer-Methoden sollten konsistent verwendet werden, um sicherzustellen, dass die zugehörige Bean korrekt verwaltet wird.

2. **Fehlerbehandlung in Disposer-Methoden**:
   Stellen Sie sicher, dass Ausnahmen in Disposer-Methoden protokolliert oder ordnungsgemäß behandelt werden.

3. **Kombination mit anderen CDI-Features**:
   Nutzen Sie Disposer-Methoden zusammen mit anderen CDI-Funktionen wie Scopes (`@RequestScoped`, `@ApplicationScoped`) für eine umfassende Ressourcenverwaltung.

## Fazit

Disposer-Methoden sind ein leistungsstarkes Werkzeug in Java CDI, um Ressourcen sicher und effizient zu verwalten. Durch die Kombination von Producer- und Disposer-Methoden können Entwickler den Lebenszyklus von Beans vollständig kontrollieren und gleichzeitig eine saubere Codebasis gewährleisten.
