# Corso ES6/Angular
<!-- TOC -->

- [Corso ES6/Angular](#corso-es6angular)
    - [Aspetti tecnici](#aspetti-tecnici)
        - [Let](#let)
        - [Backtick](#backtick)
        - [Costanti](#costanti)
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
        - [Componenti parametrizzati](#componenti-parametrizzati)
    - [Documentazione](#documentazione)
        - [Demo online](#demo-online)

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
        {label: 'Fabio'},
        {label: 'Lisa'},
    ];

In ES5

    for (const key in list) {
        console.log( key, list[key].label ); // 0 Fabio, 1 Lisa
    }

In ES6

    for (const user of list) {
        console.log( user.label );   // Fabio, Lisa
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

    //...All'interno del component..
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

Il router è un componente che permette di impostare diversi path per tutte le pagine dell'applicazione, in modo che cambi l'url, il contenuto della pagina, ma non venga fatto alcun refresh in fase di caricamento dei contenuti.

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

### Componenti parametrizzati

É possibile creare dei componenti generici (ad esempio un componente Card) a cui vengono passati dei valori in maniera più o meno dinamica separatamente. Per far ciò va impostato un parametro nel tag:

    <ag-toggable title="1+1+1">
    <ag-toggable [title]="1+1+1">

La differenza nell'uso delle parentesi quadre sta nel fatto che nel primo caso il valore viene preso come stringa (scrivendo quindi `1+1+1`), mentre nel secondo caso viene valutata l'espressione associata (quindi restituendo `3`)

All'interno del componente è necessario impostare il parametro come input 

    @Input() title = 'Widget'

dove `Widget` è il valore di default nel caso non fosse specificato. 

Ovviamente, per utilizzare il valore è necessario inserirlo nel componente con `{{title}}`

## Documentazione

### Demo online

[Playground ES6 Fabio Biondi](http://demo.fabiobiondi.io/es6playground/)

