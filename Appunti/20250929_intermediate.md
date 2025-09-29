CLR fa garbage collector, e si occupa dell'allocazione di memoria.

Creazione di oggetti: essendo reference type, posso creare più variabili che puntano allo stesso punto in memoria.

Struct: simili alle classi, ma sono value type e vengono allocate sullo stack.

int, char, float e double sono degli **alias per delle struct**.



La copia per le reference type è una copia del puntatore, mentre per le value type è una copia del valore.

L'assegnazione e una copia di un reference type è molto più economico.



#### Mutable vs immutable object

Quando instanziamo un oggetto gli assegnamo uno stato, che può cambiare durante l'esecuzione del programma.

Il poter cambiare lo stato della classe (il valore di tutto quello che la classe contiene) può essere più o meno corretto.

L'oggetto è immutable se le properties non possono cambiare durante l'esecuzione del programma.

Le properties dell'oggetto immutabile non devono avere il set, possono essere assegnate solo dal costruttore della classe.



Per gli ogetti mutabili ha senso creare il costruttore per la **validazione dei parametri**, ma il costruttore può non essere esplicitamente dichiarato.



La mutabilità può portare ad errori.

L'immutabilità facilita il debugging.





#### Tuple

Value types che permettono di memorizzare più valori, è un tipo mutabile.

Tipicamente utilizzati per il ritorno multiplo nei metodi.

Istanziabile come:

* (T1, T2, ...) pippo = (val1, val2, ...)
* (T1 nome1, T2 nome2, ...) pippo = (val1, val2, ...)

Posso poi accedere ai valori della tupla come:

* pippo.Item1, pippo.Item2, ... nel primo caso
* pippo.nome1, pippo.nome2, ... nel secondo caso



#### Records

Un tipo di data structure che il compilatore sintetizza in altro modo.

Posso designare tipi di dati immutabili.

Setter privato con delle properties specificate nei parametri.

Non garantisce l'immutabilità!



Record sono trattati come **value types ma sono reference type**.

Le proprietà che ha un record sono pubbliche in get e private in set, e sono quelle definite come parametri.

Garantiscono la comparazione per value equality, ovvero compara che i valori interni siano uguali.



Supportano l'immutabilità ma **possono essere mutati**, ho quindi qualcosa che è value type ma può essere trattato come reference type.

Posso fare mutation non destructive.

Built-in formatting for display.



Non è garantita l'immutabilità perché i reference type al suo interno sono puntatori costanti a oggetti non costanti.



Posso copiarli cambiando delle properties con la keyword **with {}**.



#### Const

Devono essere immutabili per tutta l'esecuzione del programma, la dichiarazione e l'inizializzazione devo essere fatte **one time** e **one shot** a compile time.

Solo i tipi value type possono essere definiti come const (eccetto le stringhe).

Le reference type non possono essere definite const.

Il trucco che il CLR usa è creare del linguaggio macchina che viene riutilizzato at run time.



#### Oggetti anonimi

Possono essere crati con var \_ = new {}

Contengono una collezione di campi o proprietà definite all'inizializzazione.

Il confronto tra oggetti anonimi è per valore.



#### Readonly

Può essere inizializzato una sola volta a runtime e non può più essere cambiato.

Viene inizializzato all'avvio se il campo o la classe è statica.





#### Attributes

Dichiarativo per decorare una classe, un parametro o assembly (le dll generate dalla compilazione).

Sintassi usa le parentesi quadre \[].

Possono avere uno o più parametri, e possono esserne utilizzati anche in più di uno.

Si può creare un attributo custom ereditando da system attribute.



Ad esempio si possono utilizzare per compilare o meno un metodo se si è in release piuttosto che in debug.



Per modificare una route si può utilizzare un attribute.



Sono solitamente utilizzati assieme alla reflection. (L'API che permette di leggere il codice che è stato scritto)



\[Serializable] indica che una classe è serializzabile

\[Obsolete] indica che un metodo è obsoleto, lancia un warning

\[Route] indica una route



#### Delegates and events

Meccanismo di binding a un comportamento che può essere definito successivamente.

I delegati sono typesafe.

Vengono utilizzati per una sorta di "polimorfismo", ma per scrittura di codice funzionale. 

Sostanziamente definisco un tipo di funzione, i tipi in ingresso e quelli in uscita, ma senza definire la funzione specifica.

Una volta che ho il tipo della funzione, posso definire dove questa dev'essere usata, ma questa può essere implementata in maniera diversa.

```C#

// public static void SortArray<T>(T\[] array, Func<T, T, bool> comparator)

// public static void SortArray(int\[] array)



public static delegate bool Comparator<in T> (T a, T b);



public static void SortArray<T>(T\[] array, Comparator<T> comparator)

{

   int n = array.Length;

   bool swapped;

   for (int i = 0; i < n - 1; i++)

   {

       swapped = false;

       for (int j = 0; j < n - i - 1; j++)

       {

	    // if (array\[j] < array\[j + 1])

           if (comparator(array\[j] , array\[j + 1]))

           {

               T temp = array\[j];

               // int temp = array\[j];

               array\[j] = array\[j + 1];

               array\[j + 1] = temp;

               swapped = true;

           }

       }

       if (!swapped)

       {

            break;

       }

   }

}



int\[] mioArray = { ... };

var lambda = (a,b) => a > b;

SortArray<int>(mioArray, lambda);

```

Posso instanziare un delegato con delle funzioni anonime inline.



I delegati di tipo Action hanno return void, mentre i delegati Func ritornano l'ultimo valore definito.



#### Eventi

Nelle interfacce grafiche sono presenti gli eventi, ci si può registrare, sono un late binding mechanism.

La sottoscrizione ad un evento crea un accoppiamento tra classi.

È un accoppiamento abbastanza lasco, la classe chiamante è interessata solo a determinati eventi.



Gli eventi sono definiti con la keyword **event**. 

Il tipo dell'evento deve essere un **delegato**.

La loro dichiarazione dev'essere un verbo o una frase verbale in tempo presente (present tense) per qualcosa che sta accadendo oppure tempo passato prossimo (past tense) per qualcosa che è accaduto.

Il codice dev'essere tollerante al fatto che non ci siano sottoscrittori (con il punto interrogativo -> **?**).





Una classe espone un evento nel seguente modo:

```C#

// Questa è la classe del pubblicatore

public class Pippo

{

   // qui la classe pippo definisce un evento a cui ci si può sottoscrivere

   public event EventHandler<EventArgs>? NomeDellEvento;



   // qui è il metodo in cui l'evento viene "raised" e quindi vengono chiamati tutti i sottoscritti

   public void Run()

   {

        NomeDellEvento?.Invoke(this, new EventArgs(/\*parametri corretti\*/));

   }



}



// Questa è la classe del sottoscrittore

public class Minnie

{

   // metodo che implementa il delegato, viene usato per sottoscriversi ad un evento

   public void ImplementazioneDelDelegato(object? sender, EventArgs argomenti)

   {

       // corpo del metodo

   }

}



protected static Main()

{

   Pippo pippo = new Pippo();

   Minnie minnie = new Minie();

   

   // minnie si sottoscrive all'evento di pippo

   pippo.NomeDellEvento += Minnie.ImplementazioneDelDelegato;





   // pippo "raises" l'evento, e chiama tutti i metodi sottoscritti (quindi il metodo fornito da Minnie)

   pippo.Run();



   // minnie si disiscrive dall'evento

   pippo.NomeDellEvento -= Minnie.ImplementazioneDellEvento;

}

```

#### CLR

Common intermediate language: crea dell'output intermedio, che supporta vari linguaggi.

Tutti i linguaggi di programmazione compilano in CIL (common intermediate language), per poi essere compilati nel binario.

C# code --> C# compiler --> Intermediate language (.exe oppure .dll) --> JIT compiler --> Codice binario (istruzioni di macchina)



JIT compiler è gestito dal CLR -> prende il codice intermedio riga-per-riga e lo esegue.

Fa da interprete al nostro codice e lo fa diventare codice nativo di macchina.



Oltre al JustInTime esiste l' AheadOfTime (AOT), che produce codice macchina che è auto-contenuto ed è stato creato prima-del-tempo (di esecuzione).

Può essere però utilizzato per una singola architettura (quindi combinazione cpu-gpu-ram-... che sono presenti nella configurazione del pc su cui è invocato).



###### Pro del CLR 

* Gestione della memoria: l'allocazione dinamica della memoria ha bisogno dell'allocazione e della de-allocazione (tramite garbage-collection)
* Security boundaries: in ambienti cloud possono essere runnati processi di vari utenti simultaneamente, ma la sicurezza viene garantita dal CLR. (Non posso accedere a memoria e dati altrui e vice-versa)
* Type safety: conosco i tipi tramite la reflection
* Exception handling: lancia delle eccezioni quando si verificano condizioni anomale
* Piccoli suggerimenti di miglioramento della performance 
