# Perchè TypeScript
TypeScript si pone due principali traguardi:
* Fornire *un sistema di tipizzazione opzionale* per JavaScript.
* Fornire funzionalità pianificate dalle future edizioni JavaScript ai motori JavaScript attuali

Il desiderio di questi traguardi è motivato qui sotto:

## La tipizzazione di TypeScript

Forse ti starai chiedendo "**Perchè aggiungere tipi al JavaScript?**"

E' dimostrato che i tipi migliorano la qualità e la leggibilità del codice. Grandi team (Google, Microsoft, Facebook) sono
arrivati continuamente alla stessa conclusione. Nello specifico:

* I tipi incrementano la tua agilità durante il refactoring. *E' meglio che sia il compilatore a rilevare gli errori che averli in runtime*
* I tipi sono una delle migliori forme di documentazione che puoi avere. *La firma di una funzione è un teorema ed il suo corpo è la dimostrazione*.

Ad ogni modo, i tipi sono soliti essere un po troppo cerimoniosi. TypeScript è molto particolare nel mantenere una barriera a
questo. Ecco come:

### Il tuo JavaScript è TypeScript
TypeScript è in grado di compilare in sicurezza il tuo codice JavaScript. Non ci sono sorprese dato il suo nome. La cosa bella è che i tipi sono completamente opzionali. Il tuo file JavaScript `.js` può essere modificato a `.ts` e TypeScript ti darà comunque il tuo file `.js` equivalente al tuo file originale. TypeScript è *intenzionalmente* e strettamente un supervisore di JavaScript con un controllo opzionale dei tipi.

### I tipi possono essere impliciti
TypeScript proverà ad interferire come può a seconda delle informazioni dei tipi con un minimo costo di produttività durante lo sviluppo di codice. Per esempio, nell' esempio che segue TypeScript saprà che foo ' di tipo `number` e darà un errore sulla seconda linea come mostrato:

```ts
var foo = 123;
foo = '456'; // Error: cannot assign `string` to `number`

// Foo è un numero o una stringa?
```
Questa inferenza di tipi è ben motivata. Facendo cose come nell' esempio, nel resto del codice, non potrai essere certo che `foo` sia `number` o `string`. Errori simili spesso capitano in grandi progetti con molti file. Discuteremo a fondo l'inferenza di tipi più avanti.

### I tipi possono essere espliciti
Come abbiamo detto, TypeScript proverà ad interferire il più possibile, puoi comunque usare delle annotazioni per:
1. Aiutare il compilatore, e cosa più importante fornire documentazione per chi dovrà leggere il tuo codice (potresti essere tu dal futuro!).
1. Fare in modo che ciò che il compilatore vedrà, sarà cio che tu vuoi vedrà. Questa sarà la tua compresione del codice che corrisponde ad un analisi algoritmica del codice (fatta dal compilatore).

TypeScript usa una popolare annotazione in altri linguaggi dai tipi *opzionali* (e.g. ActionScript e F#)

```ts
var foo: number = 123;
```
Quindi se fai qualcosa di sbagliato il compilatore darà questo errore e.g.:

```ts
var foo: number = '123'; // Error: cannot assign a `string` to a `number`
```

Discuteremo tutti i dettagli della sintassi dell' annotazione supportata in TypeScript in un capitolo più avanti.

### I Tipi sono strutturali
In diversi linguaggi (specificatamente quelli tipizzati) la tipizzazione statica risulta cerimoniosa anche perchè *sapendo già* che il codice funzionerà la semantica del linguaggio ti costringe a copiare sempre le stesse cose. Questo è il motivo perchè cose come l'[automapper per C#](http://automapper.org/) è vitale per C#. Siccome vogliamo che TypeScript sia facile per gli sviluppatori JavaScript con un minimo carico cognitivo, i tipi sono *strutturali*. Questo significa che il [duck typing](https://it.wikipedia.org/wiki/Duck_typing) è un costrutto di prim'ordine. Considera il seguente esempio. La funzione `iTakePoint2D` accetterà qualsiasi cosa che comprende tutto ciò che si aspetta (`x` e `y`):

```ts
interface Point2D {
    x: number;
    y: number;
}
interface Point3D {
    x: number;
    y: number;
    z: number;
}
var point2D: Point2D = { x: 0, y: 10 }
var point3D: Point3D = { x: 0, y: 10, z: 20 }
function iTakePoint2D(point: Point2D) { /* fai qualcosa */ }

iTakePoint2D(point2D); // informazioni esatte
iTakePoint2D(point3D); // informazioni aggiuntive ok
iTakePoint2D({ x: 0 }); // errore perchè manca l'informazione l'y
```

### Gli errori di tipo non prevengono gli errori JavaScript
Per rendere facile la migrazione da JavaScript a TypeScript, anche se ci sono errori di compilazioni, di default TypeScript *emmettera validi errori JavaScript* al meglio che può e.g.:

```ts
var foo = 123;
foo = '456'; // Error: cannot assign a `string` to a `number`
```

Emmetterà questo JavaScript:

```ts
var foo = 123;
foo = '456';
```

Quindi puoi man mano passare il tuo codice da JavaScript a TypeScript. E' molto differente da come molti altri compilatori lavorano ed è una ragione in più per passare a TypeScript.

### I tipi possono essere d'ambiente
Un maggior traguardo di TypeScript era poter rendere possibile usare librerie JavaScript già esistenti in maniera sicura e facile. TypeScript lo fa grazie alle *declaration*. TypeScript ti fornisce una scala di quanto impegno vuoi mettere nelle tue delcaration, più effort ci metterai, più le librerie saranno sicure ed intelligenti. Le definizioni per le più popolari librerie JavaScript sono già state scritte per te attraverso la [DefinitelyTyped community](https://github.com/borisyankov/DefinitelyTyped) quindi per molti scopi:

1. I file di definizione già esistono
1. O in ogni caso, hai una vasta lista di dichiarazioni template già disponibili

Un esempio veloce di come puoi scrivere il tuo file di dichiarazione, consideriamo un esempio triviale di [jquery](https://jquery.com/). Normalmente (come ci si aspetta da un buon codice JS) TypeScript si aspetta una dichiarazione (i.e l'uso di `var`) prima di usare una variabile

```ts
$('.awesome').show(); // Error: cannot find name `$`
```
Come veloce rimedo, *puoi dire a TypeScript* che sicuramente esiste qualcosa chiamato `$`
```ts
declare var $: any;
$('.awesome').show(); // Ok!
```
Se lo desideri puoi costruire la tua definizione e fornire ulteriori informazioni per prevenire degli errori:
```ts
declare var $: {
    (selector:string): any;
};
$('.awesome').show(); // Ok!
$(123).show(); // Error: selector needs to be a string
```

Discuteremo i dettagli della creazione di definizioni TypeScript per JavaScript appena saprai di più riguardo TypeScript (e.g. cose come `interface` ed `any`)

## JavaScript del futuro => Adesso
TypeScript fornisce un numero di funzionalità che sono pianificate nell' ES6 per i motori JavaScript attuali (che al momento supportano solo ES5 etc.).
Il Team di TypeScript sta continuando ad aggiungere queste funzionalità e questa lista diventerà più lunga nel tempo e lo vederemo nelle sezioni relative. Come esempio vediamo una classe:

```ts
class Point {
    constructor(public x: number, public y: number) {
    }
    add(point: Point) {
        return new Point(this.x + point.x, this.y + point.y);
    }
}

var p1 = new Point(0, 10);
var p2 = new Point(10, 20);
var p3 = p1.add(p2); // { x: 10, y: 30 }
```

e le amate arrow function:

```ts
var inc = x => x+1;
```

### Sommario
In questa sezione abbiamo fornito le motivazioni ed i traguardi di TypeScript. DOpo questo possiamo scendere nei dettagli di TypeScript.

[](Interfaces are open ended)
[](Type Inferernce rules)
[](Cover all the annotations)
[](Cover all ambients : also that there are no runtime enforcement)
[](.ts vs. .d.ts)
