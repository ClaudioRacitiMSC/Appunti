#### Automapper

Per mappare i dati tra i layers vengono usati gli automapper.



#### Entity framework in breve

Entity framework è un Object-Relationship-Mapper.

Va a creare nell'applicazione un context database.

Crea delle classi per accedere e modificare campi degli oggetti presenti nel database.





#### Torniamo agli automapper

Processo di tradurre una struttura ad oggetto in un altra.

Si può avere un DTO (DataToObject ?) in ogni layer volendo

Cosa fornisce DTO?

* Separazione dei compiti
* Sicurezza
* Flessibilità
* Mantenibilità



I mapper possono essere scritti manualmente e di solito sono bi-direzionali (A->B e B->A).

Come creare un mapping ?

```C#
var config = new MapperConficuration(cfg =>    /\* Creo la configurazione che definisce i mapper \*/
{
    cfg.CreateMap<User, UserDto>(); /\* La configurazione di map è definita tra le due classi specificate\*/
});
var mapper = config.CreateMapper();            /\* Istanzio il mapper dalla configurazione \*/

UserDto dto = mapper.Map<UserDto>(userFromDb); /\* Uso il mapper per convertire un oggetto in un altro \*/



/\* Da notare come CreateMapper è un metodo di MapperConfiguration,

&nbsp;  mentre CreateMap è un metodo diverso utilizzato per specificare

&nbsp;  le classi tra cui è definita la mappa                          \*/
```



#### Programmazione Asincrona

L'esecuzione dell'applicazione non si interrompe quando si avviano processi di lunga durata.

Ci sono attività (task) che non necessariamente girano in parallelo, ma il programma può saltare tra uno e l'altro e anche gestirli contemporaneamente.

Una volta che un task è completato, il programma viene informato, e può accedere ai risultati.



###### Parallelismo vs concorrenza

Parallelismo si ha quando due task vengono eseguiti contemporanemente (programmazione asincrona).

Nella concorrenza i task vengono eseguiti sequenzialmente nell'ordine in cui vengono chiamati (programmazione sincrona).



In programmazione asincrona ogni thread può essere lanciato prima che il precedente completi.

Ogni thread ha un task specifico. 

Il thread è l'entità che esegue il compito e il task è il compito che viene eseguito (ad esempio un metodo).



Quando un thread viene lanciato, ha sempre un callback di ritorno.

Il thread principale lancia tutti quelli secondari, e deve aspettare che si chiudano prima di chiudersi.

Se questo non avviene tutti i thread secondari (o figli), vengono lasciati orfani (o zombie).

Senza un supporto del linguaggio si ha bisogno di gestire callback, eventi di collegamento, etc...

In C# è tutto gestito dalle apposite librerie.



Parole chiave:

* **async** : definisce che il task è asincrono e il metodo ritorna una promessa
* **await** : definisce che il chiamante deve aspettare che la promessa del thread lanciato sia rispettata. Il flusso di controllo del **chiamante si interrompe** finché la promessa non viene rispettata e il processo del thread restituisce il valore promesso.



È buona norma che i task ritornino sempre un valore.



#### Task parallel library

Utilizzare dei cicli per lanciare dei task:

* Parallel.For
* Parallel.ForEach  

Molto semplici e sicuri da utilizzare.

Posso assegnare un livello massimo di parallelismo (max num di task paralleli lanciati).

Posso creare un cancellation token, che segnala ai tasks il momento in cui si devono chiudere necessariamente.



#### Deadlocks

* due processi cercano di accedere contemporaneamente a una risorsa con un lock mutualmente esclusivo
* un processo sta mantenendo un lock su una risorsa e non può rilasciarla
* il primo processo aspetta il secondo per l'accesso a una risorsa, mentre il secondo aspetta il primo per un'altra
* no preemption (non ho capito rip)



Oltre ai locks esistono i semafori, 

I semafori servono a coordinare i thread, definiscono un numero massimo di thread che possono accedere a delle risorse.

I lock si applicano a una risorsa, nessuno può accedervi se la risorsa è lockata.



#### Thread-safe collection

Collection classes che sono sia thread-safe e scalabili.

Sono accessibili da thread multipli e aggiungere o rimuovere dati in modo sicuro.



