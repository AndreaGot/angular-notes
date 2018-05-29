# Corso ES6/Angular

<!-- TOC -->

- [Corso ES6/Angular](#corso-es6angular)
    - [Aspetti tecnici](#aspetti-tecnici)
        - [Let](#let)
        - [Backtick](#backtick)
        - [Costanti](#costanti)
        - [Spread Operator](#spread-operator)
        - [Destructuring](#destructuring)
        - [Merge di oggetti](#merge-di-oggetti)
        - [Ciclo for](#ciclo-for)
        - [Arrow function](#arrow-function)
    - [Angular](#angular)
        - [Creazione progetto](#creazione-progetto)
        - [Direttive](#direttive)
        - [Inference](#inference)
        - [Scrivere json di oggetto](#scrivere-json-di-oggetto)
        - [Chiamata di servizi HTTP](#chiamata-di-servizi-http)
        - [Router](#router)
        - [Propagazione di eventi](#propagazione-di-eventi)
        - [Custom pipe](#custom-pipe)
        - [Componenti parametrizzati](#componenti-parametrizzati)
            - [Input](#input)
            - [Output](#output)
        - [Moduli](#moduli)
        - [Refactoring](#refactoring)
        - [Lazy loading](#lazy-loading)
        - [Services](#services)
        - [Build](#build)
        - [Best Practices](#best-practices)
    - [Documentazione](#documentazione)
        - [Compodoc](#compodoc)
    - [Demo](#demo)

<!-- /TOC -->

## Aspetti tecnici

### Let

Con `let` è possibile dichiarare una variabile che è limitata al blocco in cui si trova. Con `var` sarebbe tutto globale.

    let b = 2;
    {
        let b = 20;
    }
    console.log ( b); // output: 2 (by using 'var' it would be 20)

### Backtick

`ALT + 096` `

Permette di creare pattern dinamici

    const url = `$firstPart/$secondPart`

Si può andare a capo, può anche servire per il template inline

### Costanti

    const a = 1;
Non è modificabile.

    const list = [1,2,3];
É modificabile nel contenuto.

### Spread Operator

Lo spread operator, identificato come `...` (tre puntini), è un operatore che permette, data una lista o una collezione, di passare come parametro il contenuto della lista anzichè l'oggetto lista stesso.

In un certo senso

        foo(...list)

equivale a

        foo(listItem1, listItem2...)

### Destructuring

    const user = {
        name: 'Andrea',
        surname: 'Gottardi',
    };
    const { name, surname } = user ;
    console.log (name, surname);

Il destructuring serve per semplificare la sintassi, evitando di fare riferimenti continui alla stessa variabile

    const { name: n, surname: s, role: r = 'guest' } = user ;
    console.log (n, s, r); // Andrea Gottardi guest

É possibile specificare dei valori specifici, o di default, nel caso il parametro specificato non sia disponibile/valorizzato.

### Merge di oggetti

    const defaults = { draggable: false, resizable: false };
    const config = { draggable: true, resizable: true };

    Object.assign(defaults, config);
    console.log (defaults) // { draggable: true, resizable: true };

`Object.assign()` permette di effettuare il merge tra due oggetti, sovrascrivendo eventuali conflitti ma mantenendo parametri presenti in uno e nell'altro, ma non in entrambi.

**Non funziona:** Se c'è un oggetto incluso in un altro oggetto. In quel caso viene sovrascritto l'intero oggetto senza curarsi delle differenze.
La soluzione può essere l'estrazione dell'oggetto interno e l'aggiunta in coda.

### Ciclo for

    const list = [
        {label: 'Andrea'},
        {label: 'Lisa'},
    ];

In ES5

    for (const key in list) {
        console.log( key, list[key].label ); // 0 Andrea, 1 Lisa
    }

In ES6

    for (const user of list) {
        console.log( user.label );   // Andrea, Lisa
    }

In template Angular

    template: `
        <li *ngFor="let user of list">
            {{user.label}}
        </li> `

### Arrow function

In ES5

    function getCircle(radius) {
        return 2 * Math.PI * radius;
    }
    console.log (getCircle(1)); // 6.283185307179586

In ES6 Arrow function

    const getCircle = (radius) => {
        return 2 * Math.PI * radius;
    }

In ES6, ristretto

    const getCircle = (radius) => 2 * Math.PI * radius;

## Angular

### Creazione progetto

    ng new delta2-project --prefix fb --inline-template --inline-style --skip-tests

`ng new <nome>`: Crea un nuovo progetto <nome>

`--prefix fb`: setta fb come prefisso dei componenti

`--inline-template`: Usa un template inline per i componenti (non esterno). Comodo all'inizio, poi estrarre il contenuto è sempre veloce

`--inline-style`: inserisce lo stile in un array all'interno del componente

`--skip-tests`: Salta i test.

### Direttive

Le **direttive** sono componenti base nativi di Angular:

`*ngIf`: Serve per verificare una proprietà dell'oggetto a cui è associato (es. `*ngIf="visible")

Si può associare a un componente del DOM che deve essere visualizzato o meno, magari associato a un pulsante toggle..

    <button (click)="toggle()> Toggle </button>

E, all'interno del component

    toggle() {
        this.visible = !this.visible;
    }

`*ngFor`: Cicla sul contenuto di una lista (Vedi la sezione dedicata ai cicli for sopra).

### Inference

Angular **inferisce** in automatico i tipi. Se viene inserito un oggetto in una collezione specificando dati errati, il linter interviene sottolineando l'errore

### Scrivere json di oggetto

    <pre> {{ devices | json }} </pre>

Molto utile per visualizzare i dati raw e vedere come vengono visualizzati

### Chiamata di servizi HTTP

    http.get('localhost:3000/devices').subscribe()

In Angular 2+ è necessario **sottoscrivere** il metodo, altrimenti nessuno lo chiama e quindi non viene eseguito

Per utilizzare il JSON ritornato:

    http.get<Device[]>('localhost:3000/devices')
            .subscribe((result: Device[]) => {
                    this.devices = result
                });

Esempio di cancellazione di dati verso un url, con rimozione logica dalla lista solo in caso di operazione completata

    this.http.delete('url').subscribe(() => {
        const index = this.devices.findIndex((d) => d.id === device.id);
        this.device.splice(device.id);
    })

### Router

Il **router** è un componente che permette di impostare diversi path per tutte le pagine dell'applicazione, in modo che cambi l'url, il contenuto della pagina, ma non venga fatto alcun refresh in fase di caricamento dei contenuti.

Nel template

    <button routerLink="login">Login</button>

Nel file app.module

    RouterModule.forRoot([
        {path:'contacts', component: ContactComponent},
        {path:'catalog', component: CatalogComponent},
        {path:'login', component: LoginComponent},
        {path:'**', redirectTo: login}
    ])

Per ogni entry va specificato il nome che verrà riportato nell'URL e il componente ad esso associato. per riportare nel componente principale il risultato di questo router va usato il tag `<router-outlet></router-outlet>`

Si può impostare una formattazione particolare in base al router attivo con la direttiva `routerLinkActive="<property>"`

### Propagazione di eventi

Può capitare che alcuni eventi (ad esempio il click su un bottone presente su un elemento che ha a sua volta un evento di click registrato) partano insieme quando in realtà non è un effetto voluto. Ad esempio, se ho una riga di una tabella selezionata, e voglio cancellarne un'altra, l'esecuzione della cancellazione standard prevede che la riga appena cancellata sia quella "attiva", quando in realtà non dovrebbe cambiare da quella già esistente. Per risolvere, è necessario usare il metodo

    event.stopPropagation()

 che blocca la catena di trigger ed esegue gli eventi solo fino a quando lo si considera opportuno.

`event` è un tipo di evento come ad esempio `MouseEvent`

    toggle(event: MouseEvent) {
        event.stopPropagation();
        this.opened = !this.opened;
    }

### Custom pipe

Le *custom pipe* sono delle shortcut che servono per visualizzare i dati in un modo predefinito, formattandoli sulla base di diversi criteri.

Ad esempio, dato il valore di memoria di un telefono in MB, si può ottenereil valore in GB con

    <span *ngIf="device.memory"> {{device.memory/1000}}Gb </span>

si può creare una **pipe** che è un componente creato come segue

    @Pipe({
        name: 'memory';
    })

    export class MemoryPipe implements PiprTransform {
        transform(value: string) {
            return value ? value / 1000 + 'Gb' : '';
        }
    }

In questo modo è possibile modificare l'esempio sopra scrivendo:

     {{device.memory | memory}}

Per avere lo stesso risultato.

Come sempre, la **pipe** è un component che va registrato correttamente in `app.module` come un qualsiasi componene

### Componenti parametrizzati

#### Input

É possibile creare dei componenti generici (ad esempio un componente Card) a cui vengono passati dei valori in maniera più o meno dinamica separatamente. Per far ciò va impostato un parametro nel tag:

    <ag-toggable title="1+1+1">
    <ag-toggable [title]="1+1+1">

La **differenza** nell'uso delle parentesi quadre sta nel fatto che nel primo caso il valore viene preso come **stringa** (scrivendo quindi `1+1+1`), mentre nel secondo caso viene valutata **l'espressione** associata (quindi restituendo `3`).

C'è differenza anche con le parentesi: se sono **quadre** si riferiscono a un parametro di **solo input**, altrimenti se si aggiungono le **parentesi tonde** è anche un parametro di **output**, o che comunque modifica il proprio valore anche nel senso opposto.

All'interno del componente è necessario impostare il parametro come input

    @Input() title = 'Widget'

dove `Widget` è il valore di default nel caso non fosse specificato.

Ovviamente, per utilizzare il valore è necessario inserirlo nel componente con `{{title}}`

#### Output

Similarmente a `@Input()`, esiste l'omologo per l'output che ha come direttiva `@Output()`.

    @Output() tabClick = new EventEmitter();

nel metodo

        this.tabClick.emit(item);

Diversi component, anche nidificati tra loro, usano una combinazione di emit() al loro livello per lanciare allo strato superiore i dati di output.

Affichè il tutto funzioni nel migliore dei modi, secondo la filosofia che sta sotto alla gestione dei componenti, è necessario che ogni componente sia *stateless*, cioè che abbia lo stato gestito dall'esterno (un componente parent, ad esempio).

Per stato si intende qualsiasi cosa dinamica, anche solo l'oggetto attivo in un dato momento o una parte visualizzabile secondo qualche condizione. L'idea è che un componente non debba sapere come gestire degli eventi, ma che lo faccia solo in risposta a parametri passati dal parent.

Ovviamente, non è possibile che *tutti* i componenti siano stateless, ma più lo stato è gestito "in alto" nella catena, più i componenti sottostanti possono essere riutilizzati.

### Moduli

Un modulo è un insieme ristretto di componenti, in maniera simile ad `app.module`. In ogni modulo si possono inserire diversi componenti, sia standard che custom, e decidere contemporaneamente cosa rendere pubblico all'esterno. Ad esempio, potrebbe essere plausibile voler rendere disponibile solo il componente `GridComponent`, e non i componenti `RowComponent` o `ColumnComponent`, che sono utilizzati solo all'interno della griglia.

Ogni modulo è indipendente dagli altri: va importato in ogni modulo tutto quello che viene utilizzato dai componenti al suo interno (ad esempio `FormsModule`, che deve essere importato, se utilizzato, sia all'interno di `app.module` che nel modulo custom)

L'esportazione, al contrario, non è sempre richiesta: lo è quando il singolo oggetto viene istanziato a mano, mentre se lo si fa con il Router non è necessario.

Nella pratica, se ci si riferisce al componente con il suo tag anzichè con `<router-outlet>`, allora è necessario fare l'export.

### Refactoring

Tutto può essere spostato nel suo file singolo, per mantenere l'organizzazione dell'applicazione il più snella possibile. Il metodo è quello usuale (riferimenti via URL al file, oppure export del singolo componente) con alcune eccezioni, come nel caso del `Router`, che se viene isolato ed esportato va fatto con riferimento all'intero modulo, in modo che sia completamente utilizzabile dall'app principale che utilizza `<router-outlet></router outlet>`

### Lazy loading

É possibile impostare il sistema in modo che carichi i moduli non appena vengono richiesti, e non tutti all'inizio.

nelle route, va specificato:

    path:'catalog', loadChildren(<link_to_module>)

nel modulo linkato, invece, va scritto

    RouterModule.forChild([
        {path:'', component: CatalogComponent}
    ])

In quel modo si specifica all'applicazione che il modulo specificato va caricato solo su richiesta, e con l'ordine specificato nel `RouterModule` (altrimenti non sa cosa caricare)

Può essere utile considerare che può essere necessario includere `CommonModule`

### Services

Un servizio è un componente che fornisce la logica della gestione dati.

### Build

    npm run build --prod

Questo comando, per la pubblicazione in produzione, crea una cartella `dist` che contiene tutte le librerie importate. I moduli lazy-loaded sono caricati come hash, il che è un vantaggio perchè così non viene mai caricato un file già in cache (perchè il nome è diverso).

La build funziona con diversi server, tra cui IIS e Tomcat, perchè sono file statici che possono essere caricati ovunque.

### Best Practices

- Import
  - Prima quelli di sistema, poi quelli utente
  - In ordine alfabetico
- asd

## Documentazione

### Compodoc

Compodoc è una libreria che permette la creazione automatica di documentazione per il codice angular scritto.

## Demo

[Playground ES6 Fabio Biondi](http://demo.fabiobiondi.io/es6playground/)