### let

`var` Le variabili in JavaScript sono *function scoped*. Questo è diverso da molti altri linguaggi (C# / Java ecc.) dove le variabili sono *block scoped*. Se si porta una mentalità *block scoped* in JavaScript, ci si aspetterebbe che quanto segue stampi `123`, invece stamperà `456`:

```ts
var foo = 123;
if (true) {
    var foo = 456;
}
console.log(foo); // 456
```
Questo perché `{` non crea un nuovo *variable scope*. La variabile `foo` è la stessa all'interno del *blocco* if come lo è all'esterno del blocco if. Questa è una fonte comune di errori nella programmazione JavaScript. Per questo motivo TypeScript (ed ES6) introduce la parola chiave `let` per consentire di definire variabili con vero *block scope*. Questo se si usa `let` invece di `var` si ottiene un vero elemento unico disconnesso da ciò che si potrebbe aver definito al di fuori dello scope. Lo stesso esempio è dimostrato con `let`:

```ts
let foo = 123;
if (true) {
    let foo = 456;
}
console.log(foo); // 123
```

Un altro posto in cui `let` ti salverebbe dagli errori sono i cicli.

```ts
var index = 0;
var array = [1, 2, 3];
for (let index = 0; index < array.length; index++) {
    console.log(array[index]);
}
console.log(index); // 0
```
In tutta sincerità riteniamo che sia meglio usare `let` ogni volta che è possibile, perché porta a minori sorprese per gli sviluppatori multilingue nuovi ed esistenti.

#### Funzioni che creano un nuovo scope
Visto che ne abbiamo parlato, vorremmo dimostrare che le funzioni creano un nuovo variable scope in JavaScript. Considera quanto segue:
```ts
var foo = 123;
function test() {
    var foo = 456;
}
test();
console.log(foo); // 123
```
Questo si comporta come ci si aspetterebbe. Senza questo sarebbe molto difficile scrivere codice in JavaScript.

#### JS generato
Il JS generato da TypeScript è una semplice ridenominazione della variabile `let` se un nome simile esiste già nello scope circostante. Ad esempio, viene generato quanto segue, così com'è con una semplice sostituzione di `var` con `let`:

```ts
if (true) {
    let foo = 123;
}

// becomes //

if (true) {
    var foo = 123;
}
```
Tuttavia, se il nome della variabile è già preso dallo scope circostante, allora viene generato un nuovo nome di variabile come mostrato (notare `_foo`):

```ts
var foo = '123';
if (true) {
    let foo = 123;
}

// becomes //

var foo = '123';
if (true) {
    var _foo = 123; // Renamed
}
```

#### Switch
Potete racchiudere il contenuto dei `case` in `{}` per riutilizzare i nomi delle variabili in modo affidabile in diversi `case` come mostrato di seguito: 

```ts
switch (name) {
    case 'x': {
        let x = 5;
        // ...
        break;
    }
    case 'y': {
        let x = 10;
        // ...
        break;
    }
}
```

#### let in closures
Una domanda comune nei colloqui di programmazione per uno sviluppatore JavaScript è quale sia il log di questo semplice file:

```ts
var funcs = [];
// create a bunch of functions
for (var i = 0; i < 3; i++) {
    funcs.push(function() {
        console.log(i);
    })
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```
Ci si sarebbe aspettato che fosse "0,1,2". Sorprendentemente sarà `3` per tutte e tre le funzioni. La ragione è che tutte e tre le funzioni utilizzano la variabile `i` dall'outer scope e nel momento in cui le eseguiamo (nel secondo ciclo) il valore di `i` sarà `3` (che è la condizione di terminazione per il primo ciclo).

Una soluzione sarebbe quella di creare una nuova variabile in ogni loop specifica per quell'iterazione del loop. Come abbiamo imparato prima di poter creare un nuovo scope di variabili creando una nuova funzione ed eseguendola immediatamente (cioè il pattern IIFE delle classi `(function() { /* body */ })();`) come mostrato di seguito:

```ts
var funcs = [];
// create a bunch of functions
for (var i = 0; i < 3; i++) {
    (function() {
        var local = i;
        funcs.push(function() {
            console.log(local);
        })
    })();
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```
Qui le funzioni si chiudono sopra (da qui il nome di "closure") la variabile *local* (convenientemente chiamata "local") e usano quella al posto della variabile loop "i".

> Si noti che le closure hanno un impatto sulle prestazioni (devono conservare lo stato circostante).

La parola chiave ES6 `let` in un ciclo avrebbe lo stesso comportamento dell'esempio precedente:

```ts
var funcs = [];
// create a bunch of functions
for (let i = 0; i < 3; i++) { // Note the use of let
    funcs.push(function() {
        console.log(i);
    })
}
// call them
for (var j = 0; j < 3; j++) {
    funcs[j]();
}
```

Utilizzando `let` invece di `var` si crea una variabile `i` unica per ogni iterazione del loop.

#### Riepilogo
`let` è estremamente utile per la maggior parte del codice. Può migliorare notevolmente la leggibilità del codice e diminuire la possibilità di un errore di programmazione.



[](https://github.com/olov/defs/blob/master/loop-closures.md)
