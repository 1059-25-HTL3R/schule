# **Transaktionen**

# Inhanlt
- [Plan](#plan)
- [Dantenbank aufsetzen](#datenbank-aufsetzen)
- [Dirty read](#dirty-read)
- [Nonrepeaatable read](#nonrepeatable-read)
- [Phantom read](#phantom-read)


## Plan

Wir wollen anhand der verschiedenen Isolation Levels

Die Datenbank ist [**"MySQL"**](https://www.mysql.com/de/) welche auf einer Ubuntu-VM realisiert wird.

unter [Datenbank aufsetzen](#datenbank-aufsetzen) ist beschrieben wie die Ausgangslage nachzumachen ist.

**Dummy Table mit dummy Daten:**
```
CREATE DATABASE isolation_demo;
USE isolation_demo;

CREATE TABLE konto (
  id INT PRIMARY KEY,
  kontostand DECIMAL(10,2)
);

INSERT INTO konto VALUES (1, 100.00);
```
**mit diesen Table und Daten wird gearbeitet!**
- ## Dirty read
    - ### READ UNCOMMITTED  - isolation level
        Session A:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
        START TRANSACTION;

        UPDATE konto SET kontostand = 200.00 WHERE id = 1;
        -- Noch kein COMMIT!
        ```
        Session B:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
        START TRANSACTION;

        SELECT * FROM konto WHERE id = 1;
        -- → Gibt 200.00 zurück, obwohl A noch nicht committet hat!
        ```
    ---
    - ### READ COMMITTED    - isolation level
        Session A:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
        START TRANSACTION;
        UPDATE konto SET kontostand = 300.00 WHERE id = 1;
        -- Kein COMMIT
        ```
        Session B:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
        START TRANSACTION;
        SELECT * FROM konto WHERE id = 1;
        -- → Gibt 100.00 zurück (alte, committete Version)
        ```
    ---
    - ### REPEATABLE READ   - isolation level
        Session A:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
        START TRANSACTION;
        UPDATE konto SET kontostand = 400.00 WHERE id = 1;
        -- Kein COMMIT
        ```
        Session B:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
        START TRANSACTION;
        SELECT * FROM konto WHERE id = 1;
        -- → 100.00 (keine uncommitteten Werte sichtbar)
        ```
    ---
    - ### Serializable      - isolation level
        Session A:
        ```
        USE isolation_demo;

        SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
        START TRANSACTION;
        UPDATE konto SET kontostand = 500.00 WHERE id = 1;
        -- Kein COMMIT
        ```
        Session B:
        ```
        SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
        START TRANSACTION;
        SELECT * FROM konto WHERE id = 1;

        -- wartet bis andere laufende transaktionen fertig sind bevor daten gelsensen werden
        ```
- ## Nonrepeatable read

    - ### READ COMMITTED    - isolation level
    - ### REPEATABLE READ   - isolation level
    - ### Serializable      - isolation level
- ## Phantom read 
    - ### READ UNCOMMITTED  - isolation level
    - ### READ COMMITTED    - isolation level
    - ### REPEATABLE READ   - isolation level
    - ### Serializable      - isolation level


## Datenbank aufsetzen