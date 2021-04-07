# Structured Query Language (SQL)

## Database

Un modello stabilisce le convenzioni per esprimere i diversi aspetti della realtà e costituisce un supporto alla sua rappresentazione. La rappresentazione generata seguendo le regole del modello viene definita **schema**.

Il processo di progettazione di un database si articola in tre (n.3) fasi:

1. **Progettazione concettuale**
L'obiettivo è la rappresentazione completa ed efficace della realtà in maniera indipendente da ogni Database Management System (DBMS). La rappresentazione è lo schema concettuale, è astratta, vicina alla logica umana.
I modelli utilizzati in questa fase sono detti **modelli semantici**.
Il più noto è **Entità-Relazione**, consente di rappresentare i tre tipi di astrazione (classificazione = entità, aggregazione = relazione e generalizzazione).
2. **Progettazione logica**
Ha lo scopo di tradurre lo schema concettuale in una rappresentazione mediante un modello logico dei dati.
Il modello logico è la tecnica di organizzazione e accesso ai dati utilizzata da specifiche categorie di DBMS.
I DBMS vengono distinti, sulla base del modello logico dei dati, in **gerarchici**, **reticolari** e **relazionali**.
3. **Progettazione fisica**
Stabilisce come le strutture definite a livello logico devono essere organizzate negli archivi e nelle strutture del file system.

Vengono distinti tre (n.3) tipi di linguaggio:

1. **Data Description Language (DDL)** per la definizione dello schema logico del database;
2. **Data Manipulation Language (DML)** per le operazioni di inserimento, modifica, eliminazione dati;
3. **Data Control Language (DCL)** per le operazioni di controllo dati, gestione utenti, assegnazione diritti di accesso, ottimizzazione del funzionamento.

## SQL

SQL 92 (standard ISO 9075) è un linguaggio unificato che consente l'esecuzione di tutte le operazioni necessarie alla gestione e l'utilizzo di un database.

Non è un linguaggio di programmazione ma può essere ospitato all'interno di un linguaggio di programmazione (**embedded**).
Può essere utilizzato direttamente dall'utente (**interattivo**).

SQL è un linguaggio **dichiarativo**: permette di lavorare su insiemi di entità.
Dal 1990 è stato implementato con estensioni procedurali (es. trigger, stored procedure e strutture come IF - THEN - ELSE).
Per permettere la compatibilità fra queste due modalità sono stati introdotti i **cursori**.

Lo standard SQL non rispecchia appieno il modello relazionale e non consente di sfruttarne tutta la potenza. E' incompleto, sprovvisto di alcune funzionalità necessarie e presenta estensioni e varianti specifiche ad ogni implementazione (dialetti).

### Query

Le interrogazioni (query) vengono effettuate utilizzando il comando di selezione:

```sql
SELECT [ALL | DISTINCT] <lista_di_selezione>
<espressione_tabella>
[<clausole_di_ordinamento>]

-- dove <espressione_tabella> può contenere:
FROM
WHERE
GROUP BY
HAVING
```

è obbligatoria solo la clausola FROM.

Si può impostare un alias per una colonna o una tabella utilizzando la sintassi:

```sql
nome_colonna_originale 'nome_alias'

-- oppure

nome_colonna_originale As nome_alias
```

### Join

Se due tabelle contengono colonne che memorizzano dati in comune è possibile correlare le tabelle tramite Join.
E' una caratteristica dei sistemi relazionali: si possono collegare i dati dinamicamente al momento dell'esecuzione delle query senza dover utilizzare puntatori diretti.

Tipi di join:

- **equi join** restituisce una tabella le cui righe sono tutte e sole quelle ottenute dalle due tabelle collegate in cui i valori della colonna comune sono uguali.
Si ottiene utilizzando SELECT e WHERE con la condizione di uguaglianza.
- **inner join** è simile al precedente ma con la condizione che può essere diversa dall'uguaglianza.
Si può ottenere utilizzando SELECT e WHERE oppure con la nuova sintassi:

```sql
<riferimento_a_tabella> [NATURAL] INNER JOIN <riferimento_a_tabella>
[ON <condizione_di_join> | USING <lista_colonne_di_join>]
```

Si usa USING solo per il caso particolare di inner join definito equi join.
NATURAL indica l'equi join in base a tutte le colonne che hanno lo stesso nome.

- **outer join** può mantenere i valori per i quali non esiste una corrispondenza.
Può essere:
  - sinistra, vengono visualizzati tutti i valori della tabella utilizzata come primo operando
  - destra, vengono visualizzati tutti i valori della tabella utilizzata come secondo operando
  - completa,vengono visualizzati tutti i casi di mancata corrispondenza
- **cross join** è il prodotto algebrico delle due tabelle.
Si ottiene mediante SELECT oppure INNER JOIN senza NATURAL, ON o USING
- **self join** correla una tabella con sè stessa.
Utilizza due alias della stessa tabella.

### Operazioni su insiemi

Solo l'operatore UNION viene implementato da tutti i DBMS.

- **UNION** restituisce una tabella a partire dai risultati di due SELECT.
La tabella restituita contiene tutte le righe delle due tabelle di partenza.

```sql
<comando_select> UNION [ALL] <comando_select>
```

I due operandi devono possedere la stessa struttura.
UNION rimuove i valori duplicati se non si utilizza ALL.

- **EXCEPT** restituisce una tabella che contiene tutte le righe del primo operando che non trovano corrispondenza nel secondo
- **INTERSECT** restituisce una tabella che contiene le righe che compaiono sia nel primo che nel secondo operando

### Sub-query

E' un comando SELECT racchiuso fra parentesi tonde ed inserito all'interno di un comando SQL.
Può essere:

- *scalare* se restituisce un singolo valore e può essere utilizzata ovunque serva un valore singolo
- *di colonna* se restituisce una sola colonna
- *di tabella* se restituisce una tabella

Possono essere utilizzate nelle espressioni di confronto, nelle espressioni IN, EXISTS, nel calcolo di espressioni.

### Read, Update, Delete

- **INSERT** per l'inserimento di dati in una tabella.

```sql
INSERT INTO <nome_tabella>
[<lista_colonne>]<origine>

-- <origine> può essere:
VALUES <lista_valori>
-- lo standard non prevede questa istruzione

-- un'altra tabella che possiede la stessa struttura
SELECT * 
FROM <nome_tabella>
```

- **UPDATE** per la modifica dei dati.

```sql
UPDATE <nome_tabella>
SET <nome_colonna> = <espressione>
[, ...]
[WHERE <condizione>]
```

- **DELETE** per l'eliminazione.

```sql
DELETE
FROM <nome_tabella>
[WHERE <condizione>]
```

### Definizione del database

#### Creazione dello schema

Lo **schema** contiene la definizione di tutte le tabella del database.
Il proprietario dello schema è l'amministratore del database e possiede tutti i privilegi possibili sugli elementi che verranno definiti all'interno dello schema.
Il **catalogo** è l'insieme delle definizioni di tutti gli schemi che possiedono condizioni comuni. E' definito come meta-database e viene associato ad uno schema speciale (INFORMATION_SCHEMA) che contiene le definizioni dei dati del catalogo.

```sql
CREATE SCHEMA <clausola_di_autorizzazione>
[DEFAULT CHARACTER SET <set_di_caratteri>]
[<elemento_dello_schema> ...]

-- dove <clausola_di_autorizzazione> ha la sintassi
[<nome_catalogo>.]<nome_schema> |
AUTHORIZATION <nome_proprietario> |
<nome_schema> AUTHORIZATION <nome_proprietario>

-- <elemento_dello_schema> comprende la definizione di tabelle, viste, altro
```

#### Creazione delle tabelle

Ne sono presenti di due tipi: permanenti e temporanee (non sono supportate da molti DBMS).

```sql
CREATE [{GLOBAL | LOCAL} TEMPORARY] TABLE <nome_tabella>
(<definizione_elemento_tabella>
[, <definizione_elemento_tabella> ...])
[ON COMMIT {PRESERVE | DELETE} ROWS]

-- dove <definizione_elemento_tabella> è
<definizione_colonna> | <definizione_vincolo_di_tabella>

-- le opzioni [GLOBAL ...] e [ON COMMIT ...] sono disponibili per le sole tabelle temporanee
```

#### Creazione delle colonne

```sql
<nome_colonna> <tipo_dati>
[<clausola_default>]
[<definizione_vincolo_di_colonna>]
```

### Tipi di dati

1. numerici
   1. intero (tinyint, smallint, integer)
   2. decimale
   3. in virgola mobile (decimale seguito da un esponente)
2. alfanumerici, stringa di 0 o più caratteri racchiusi fra virgolette semplici. (max 255 escluse le virgolette)
3. temporali
   1. data, composta da 'anno-mese-giorno'
   2. ora, composta da 'ore:minuti:secondi'
   3. timestamp, combina data e ora
   4. intervallo, rappresenta un periodo di tempo.
   Si definisce con INTERVAL 'valore' DAY oppure INTERVAL 'valore' MINUTE, oppure YEAR TO MONTH oppure HOUR TO SECOND

### Vincoli di integrità

```sql
-- di colonna
[CONSTRAINT <nome_vincolo>]
{<vincolo_NOT NULL> |
<vincolo_di_univocità> |
<vincolo_di_chiave_esterna> |
<vincolo_di_controllo>}
[<attributi_del_vincolo>]

-- di tabella
[CONSTRAINT <nome_vincolo>]
{<vincolo_di_univocità> |
<vincolo_di_chiave_esterna> |
<vincolo_di_controllo>}
[<attributi_del_vincolo>]

-- dove <attributi_del_vincolo> è
[NOT] DEFERRABLE
[INITIALLY IMMEDIATE | INITIALLY DEFERRED] |
[INITIALLY IMMEDIATE | INITIALLY DEFERRED] |
...
[NOT] DEFERRABLE
```

Il vincolo NOT NULL stabilisce che i valori della colonna sono obbligatori.

Il vincolo di univocità può essere UNIQUE o PRIMARY KEY.

Il vincolo di chiave esterna, per l'integrità referenziale, fa sì che ad ogni valore non nullo della tabella che contiene la chiave esterna del join deve corrispondere uno ed un sol valore nella tabella che contiene la chiave primaria.

```sql
-- di colonna
REFERENCES <tabella_riferita>
[<colonna_riferita> [, <colonna_riferita> ...]]
[MATCH {FULL | PARTIAL}]
[<azione_innescata>]

-- di tabella
FOREIGN KEY (<colonne_referenti>)
REFERENCES <tabella_riferita>
[<colonna_riferita> [, <colonna_riferita> ...]]
[MATCH {FULL | PARTIAL}]
[<azione_innescata>]
```

Le colonne referenti sono quelle che fungono da chiave esterna, quelle riferite sono chiavi primarie.

La clausola <azione_innescata> ha la sintassi:

```sql
ON UPDATE [<azione>][ON DELETE [<azione>]] |
ON DELETE [<azione>][ON UPDATE [<azione>]]

-- dove <azione> è
CASCADE | SET NULL | SET DEFAULT | NO ACTION
```

I vincoli di controllo sono utilizzati per verificare generiche condizioni sui valori di una colonna.

```sql
CHECK (<condizione>)
```

Non possono contenere funzioni dipendenti dal tempo.

#### Asserzione

Corrisponde ad un vincolo di integrità referenziale definito separatamente dalle tabelle cui è riferito.

```sql
CREATE ASSERTION <nome_asserzione>
CHECK (<condizione>)
[<attributi_del_vincolo>]
```

#### Dominio

E' un tipo di dati, al quale possono essere associati vincoli e valori di default, che può essere utilizzato nella definizione di tabelle.
Garantisce che, in caso di modifica delle caratteristiche del tipo di dati, le definizioni di tutte le colonne che lo utilizzano risultino automaticamente aggiornate.

```sql
CREATE DOMAIN <nome_dominio> [AS] <tipo_dati>
[<clausola_default>]
[<vincolo_di_dominio>]

-- <vincolo_di_dominio> è
[CONSTRAINT <nome_vincolo>]
<vincolo_di_controllo>
[<attributi_del_vincolo>]
```

La definizione dei domini non devono contenere cicli.
Per indicare le colonne che fanno uso del dominio si utilizza la parola chiave VALUE.

Non è supportato da molti DBMS.

#### Eliminazione elementi

```sql
-- Lo schema non sarà accessibile a nessun utente e l'azione non è reversibile.
DROP SCHEMA <nome_schema> {RESTRICT | CASCADE}  -- con RESTRICT lo schema verrà eliminato solo se vuoto

-- dominio
DROP DOMAIN <nome_dominio> {RESTRICT | CASCADE}

-- tabella
DROP TABLE <nome_tabella> {RESTRICT | CASCADE}

-- asserzione
DROP ASSERTION <nome_asserzione>
```

#### Modifica

```sql
-- dominio
ALTER DOMAIN <nome_dominio> 
SET <clausola_default>
--oppure
ALTER DOMAIN <nome_dominio> 
DROP DEFAULT
-- oppure
ALTER DOMAIN <nome_dominio> 
ADD <vincolo_di_dominio>
-- oppure
ALTER DOMAIN <nome_dominio> 
DROP CONSTRAINT <nome_vincolo>

-- tabella
-- è possibile aggiungere o rimuovere colonne
ALTER TABLE <nome_tabella>
ADD [COLUMN] <definizione_di_colonna>
-- oppure 
ALTER TABLE <nome_tabella>
DROP [COLUMN] {RESTRICT | CASCADE}

-- aggiungere o rimuovere vincoli
-- il vincolo viene verificato anche sui dati già presenti
ALTER TABLE <nome_tabella>
ADD <vincolo_di_tabella>
-- oppure
ALTER TABLE <nome_tabella>
DROP <nome_vincolo> {RESTRICT | CASCADE}

-- assegnare o rimuovere valori di default
ALTER TABLE <nome_tabella>
ALTER [COLUMN] <nome_colonna>
SET <clausola_default>
-- oppure
ALTER TABLE <nome_tabella>
DROP DEFAULT
```

### Indici

Un indice è una struttura dati ausiliaria che il DBMS associa ad una tabella per eseguire una maggiore efficienza nella ricerca dei dati.
Permette di applicare una tecnica di accesso diretto evitando la scansione sequenziale della tabella. 

Se la colonna indicizzata viene utilizzata frequentemente nelle ricerche, i vantaggi in termini di velocità superano gli svantaggi del overhead connesso alla gestione dell'indice nelle operazioni di aggiornamento.

Lo standard non copre tutto l'argomento ma è possibile individuare le seguenti linee comuni:

- il DBMS crea automaticamente un indice per la chiave primaria di ogni tabella e per ogni colonna sulla quale venga definito un vincolo di unicità;
- esistono costrutti, con differente sintassi, che permettono di definire o di eliminare esplicitamente degli indici (CREATE | DROP INDEX);

Non esistono comandi per specificare quali indici utilizzare ma è il *query optimizer* del DBMS che applica delle tecniche specifiche.

SQL utilizza l'accesso *sequenziale* come strategia di base.

L'utilizzo di un indice permette di cambiare la strategia in *ottimizzata*. SQL, infatti, utilizza un componente per scegliere la strategia più efficace per l'elaborazione di un'istruzione.

#### Scelta degli indici

E' consigliabile definire un indice:

- per ogni `candidate key` per garantire il controllo di univocità dei nuovi valori;
- per ogni `foreign key` per velocizzare i join.

Le istruzioni WHERE possono trarre giovamento dalla presenza di un indice sulla colonna interessata dalla condizione.
Se l'istruzione WHERE contiene un AND viene creato un indice sulle combinazioni interessate per garantire un'esecuzione più efficiente.

Anche ORDER BY può migliorare in presenza di un indice.

### Viste

Si utilizzano per:

- semplificare le istruzioni.
Le istruzioni eseguite spesso o molto simili possono essere semplificate tramite viste.
- riorganizzare le tabelle.
Per evitare la modifica di molte istruzioni quando si rende necessario modificare uno o più tabelle.
- elaborare le istruzioni SELECT in più fasi
- semplificare i vincoli con la clausola WITH CHECK OPTION
- superare dei limiti dell'istruzione SELECT
- aumentare la sicurezza dei dati

Sono *tabelle virtuali* poiché non contengono dati propri ma sono ottenute riaggregando dati contenuti nelle tabelle base.

Anche le viste hanno una struttura definita all'interno dello schema ma è dipendente dalle definizioni delle tabelle già presenti.

#### Creazione di viste

```sql
CREATE VIEW <nome_vista>
[(<nome_colonna> {, <nome_colonna>} ...)]
AS <comando_select> [WITH [LOCAL | CASCADED] CHECK OPTION]
```

Se non vengono specificati nomi per le colonne della vista, vengono assunti uguali a quelli originali delle tabelle.

L'opzione WITH CHECK OPTION assicura che le operazioni di inserimento e di modifica dei dati effettuate con la vista soddisfino la clausola WHERE.

Le viste di gruppo contengono almeno una colonna ottenuta tramite una funzione di raggruppamento.

#### Read, Update, Delete di viste

```sql
-- eliminazione
DROP VIEW <nome_view> {RESTRICT | CASCADE}
```

Si può utilizzare una vista ovunque si possa utilizzare una tabella.

Le viste sono soggette a restrizioni riguardo all'aggiornamento dei dati.
Una vista è aggiornabile se verifica tutte le seguenti condizioni:

- non utilizza DISTINCT
- la clausola FROM della vista utilizza una tabella o una vista aggiornabile
- la clausola SELECT della vista contiene solo riferimenti puri
- la clausola WHERE della vista non contiene sub-query
- la vista non contiene GROUP BY

La maggior parte dei DBMS concedono deroghe a queste restrizioni.

#### Vantaggi delle viste

- facilitano l'accesso ai dati
- forniscono visualizzazioni diverse dei dati
- garantiscono l'indipendenza logica delle applicazioni e delle operazioni rispetto alla struttura

## Trigger e Stored Procedure

Il principale limite delle tecniche di definizion dei vincoli di integrità deriva dalla necessità di definire vincoli con una sintassi non dinamica (consente solo la specifica di alcune clausole predefinite).

La tecnica delle asserzioni permette di definire vincoli basati su SELECT ma ha il suo limite nella necessità di utilizzare meccanismi dichiarativi.

I sistemi commerciali utilizzano sistemi procedurali che permettono di definire moduli software all'interno dei DBMS per controllare eventi di sistema.

> Lo svantaggio dei meccanismi procedurali sta nella mancanza di uno standard per le estensioni di SQL.

### Transact-SQL (Sybase)

I database commerciali supportano, normalmente, i tipi di dati dello standard SQL e ne aggiungono degli altri.
Possono inoltre mettere a disposizione anche istruzioni per il controllo del flusso.

I programmi possono essere utilizzati in maniera interattiva, batch o all'interno di trigger o stored procedure:

Prima di utilizzare un qualsiasi oggetto del database è necessario il comando **USE** per specificare il database da utilizzare.

#### Variabili

I nomi di variabili locali devono essere preceduti dal carattere @

```sql
DECLARE @nome_variabile <tipo_dati>
[, @nome_variabile <tipo_dati> ...]

-- La variabile appena dichiarata ha valore NULL.
-- La si può inizializzare utilizzando
SELECT @nome_variabile = <valore>
```

#### Istruzione IF

```sql
IF <condizione>
    <istruzione>
[ELSE
    <istruzione>]
```

#### Blocchi di codice

```sql
BEGIN
    <istruzione>
    <istruzione>
    ...
    <istruzione>
END
```

#### CASE

```sql
CASE
    WHEN <condizione_1> THEN <risultato_1>
    WHEN <condizione_2> THEN <risultato_2>
    ...
    [ELSE <risultato_alternativo>]
END
```

#### WHILE

```sql
WHILE <condizione>
    <istruzione>

BREAK    -- forza l'uscita
CONTINUE -- forza la ripartenza
RETURN   -- forza l'uscita dal programma
```

Le variabili globali sono predefinite dal sistema e non possono essere aggiornate dall'utente.
Si distinguono poiché i loro nomi sono preceduti da due @.

La variabile `@@error` si utilizza per verificare la corretta esecuzione dell'ultimo programma eseguito in batch. Contiene 0 se ha avuto successo oppure il codice di errore generato.

```sql
-- permette di personalizzare la visualizzazione del risultato, mostrare statistiche e fornire informazioni per la diagnostica e il debug
SET

-- lancia l'esecuzione di un blocco di comandi, una S.P. o una transazione a un certo orario, dopo un intervallo di tempo o a causa di un evento
WAITFOR TIME 'hh:mm'
<istruzione>
-- oppure
WAITFOR DELAY 'hh:mm'
<istruzione>
-- oppure
WAITFOR ERROREXIT
<istruzione>
```

### Trigger

E' costituito da un insieme di istruzioni che vengono eseguite all'interno del DBMS al verificarsi di determinati eventi (non su richiesta dell'utente).

Gli eventi gestibili sono quelli correlati alla modifica dei dati (insert, update, delete).

```sql
CREATE TRIGGER <nome_trigger>
    ON <nome_tabella> FOR <evento>
    AS <programma>

-- dove <evento> può essere:
INSERT | UPDATE | DELETE
-- e programma è rappresentato da codice T-SQL
```

Al momento dell'esecuzione del trigger esistono due viste speciali, visibile solo dal trigger stesso, contenenti le sole righe inserite e cancellate dalla transazione in corso, ad esempio.

Il linguaggio T-SQL è una versione semplificata di un linguaggio di programmazione.

Il vantaggio nell'utilizzo dei trigger è dato dalla capacità di eseguire controlli sui dati cha hanno appena subito modifiche. Ciò permette di annullare la transazione in caso di problemi.
