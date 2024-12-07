
# Java CDI Schulung: Umgang mit Transaktionen in CDI

In dieser Schulung wird der Umgang mit Transaktionen in Java Contexts and Dependency Injection (CDI) behandelt. Sie lernen, wie Transaktionen in CDI verwaltet werden, um Datenkonsistenz und Zuverlässigkeit sicherzustellen.

## Grundlagen: Was sind Transaktionen?

Eine Transaktion ist eine Abfolge von Operationen, die entweder vollständig abgeschlossen werden (Commit) oder vollständig rückgängig gemacht werden (Rollback). Transaktionen werden verwendet, um sicherzustellen, dass Datenbankoperationen atomar, konsistent, isoliert und dauerhaft (ACID) sind.

Beispiele für Transaktionsanwendungen:
- Banküberweisungen
- Ticketbuchungssysteme
- Bestellabwicklungen in E-Commerce

## Transaktionsmanagement in CDI

CDI bietet die Möglichkeit, Transaktionen deklarativ und programmgesteuert zu verwalten. Durch die Integration mit JTA (Java Transaction API) können CDI-Beans Transaktionen effizient nutzen.

### 1. Deklaratives Transaktionsmanagement

Mit Hilfe der Annotation `@Transactional` können Sie Transaktionen deklarativ verwalten. Diese Methode ist einfach und reduziert den Boilerplate-Code.

#### Beispiel:
```java
import jakarta.transaction.Transactional;
import jakarta.inject.Named;

@Named
public class BankService {

    @Transactional
    public void transferMoney(Long fromAccount, Long toAccount, Double amount) {
        // Abziehen des Betrags vom Quellkonto
        // Hinzufügen des Betrags zum Zielkonto
        // Beide Operationen sind innerhalb einer Transaktion
    }
}
```

In diesem Beispiel sorgt die Annotation `@Transactional` dafür, dass die Methode in einer Transaktion ausgeführt wird. Bei einem Fehler wird die gesamte Transaktion zurückgesetzt.

#### Transaktionsattribute
Mit Transaktionsattributen können Sie das Verhalten der Transaktion steuern. Gängige Werte sind:
- `REQUIRED`: Nutzt die bestehende Transaktion oder erstellt eine neue.
- `REQUIRES_NEW`: Erstellt immer eine neue Transaktion.
- `MANDATORY`: Erfordert eine bestehende Transaktion.
- `NOT_SUPPORTED`: Führt den Code ohne Transaktion aus.
- `NEVER`: Darf nicht in einer Transaktion ausgeführt werden.

Beispiel:
```java
@Transactional(Transactional.TxType.REQUIRES_NEW)
public void createAccount(String accountName) {
    // Logik zum Erstellen eines neuen Kontos
}
```

### 2. Programmgesteuertes Transaktionsmanagement

Für komplexe Anwendungsfälle können Sie Transaktionen programmgesteuert mit der `UserTransaction`-API verwalten.

#### Beispiel:
```java
import jakarta.transaction.UserTransaction;
import jakarta.inject.Inject;

public class AccountService {

    @Inject
    private UserTransaction userTransaction;

    public void performTransaction() {
        try {
            userTransaction.begin();
            // Transaktionale Operationen
            userTransaction.commit();
        } catch (Exception e) {
            try {
                userTransaction.rollback();
            } catch (Exception rollbackException) {
                rollbackException.printStackTrace();
            }
            e.printStackTrace();
        }
    }
}
```

Hier haben Sie die vollständige Kontrolle über den Beginn, das Commit und den Rollback der Transaktion. Dies ist nützlich, wenn Sie mehrere operationale Schritte koordinieren müssen.

## Best Practices für den Umgang mit Transaktionen in CDI

### 1. Transaktionen klein und gezielt halten
Lange laufende Transaktionen können zu Deadlocks und Performance-Problemen führen. Halten Sie den Umfang der Transaktionen auf das Minimum beschränkt.

### 2. Keine UI-Logik in Transaktionen
Vermeiden Sie lang laufende Operationen wie Benutzerinteraktionen innerhalb einer Transaktion.

### 3. Exception Handling
Stellen Sie sicher, dass Transaktionen bei Fehlern ordnungsgemäß zurückgesetzt werden. Verwenden Sie Checked Exceptions sparsam, da sie die automatische Rollback-Logik beeinträchtigen können.

Beispiel:
```java
@Transactional
public void processOrder(Order order) throws OrderProcessingException {
    try {
        // Logik zur Bestellabwicklung
    } catch (Exception e) {
        throw new OrderProcessingException("Fehler bei der Bestellabwicklung", e);
    }
}
```

### 4. Nutzung von Transaktionsattributen
Wählen Sie das richtige Transaktionsattribut basierend auf den Anforderungen. Zum Beispiel:
- Verwenden Sie `REQUIRES_NEW` für isolierte Operationen wie das Protokollieren.
- Nutzen Sie `NOT_SUPPORTED`, um Operationen außerhalb einer Transaktion auszuführen.

## Fazit

Der Umgang mit Transaktionen in CDI ermöglicht es Ihnen, Datenbankoperationen sicher und effizient zu verwalten. Durch die Verwendung deklarativer oder programmgesteuerter Ansätze können Sie Ihre Anwendung flexibler gestalten und die Datenintegrität gewährleisten. Mit Best Practices wie schlanken Transaktionen und ordnungsgemäßem Exception Handling sichern Sie die Stabilität Ihrer Anwendungen.
