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
- **inner join** è simile al precedente ma con la condizione che può essere diversa dall'ugualianza.
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
