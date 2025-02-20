# Conceptes bàsics

## Hello, World!

El propòsit original del "hello world!", des que la primera versió de C fou escrita,
era la de provar el compilador i executar un programa real.

```rust
// hello.rs
fn main() {
    println!("Hello, World!");
}
```

```
$ rustc hello.rs
$ ./hello
Hello, World!
```

Rust és un llenguatge de claudators amb punt i coma, comentaris a l'estil C++ i 
una funció `main` - tan i tan familiar. El signe d'exclamació indica que es tracta
d'una crida a una _macro_. Pels programadors de C++, això pot no agradar-los, ja 
que estan acostumats a macros de C seriosament estúpides, però vos puc assegurar que
aquestes macros són més capaces i sanes.

Per a qualsevol altra persona, probablement siga "Genial, ara he de recordar quan dir 
[bang!](https://en.wikipedia.org/wiki/Exclamation_mark)". No obstant això, el compilador 
és inusualment útil; Si deixeu de banda aquesta exclamació, obtindreu:

```
error[E0425]: unresolved name `println`
 --> hello2.rs:2:5
  |
2 |     println("Hello, World!");
  |     ^^^^^^^ did you mean the macro `println!`?

```

Aprendre un nou llenguatge és sentir-se còmode amb els seus errors. Proveu de veure el 
compilador com un ajudant estricte però simpàtic, en lloc d'un ordinador _cridant-te_, 
perquè aneu a veure molta tinta vermella a la pantalla al principi. És molt millor pel
compilador per atrapar-vos, que no pas que el vostre programa explote davant cap persona.

El següent pas és introduïs una _variable_:

```rust
// let1.rs
fn main() {
    let answer = 42;
    println!("Hello {}", answer);
}

```

Els errors ortogràfics són errors de _compilació_, no errors en temps d'execució com 
passa amb els llenguatges dinàmics com Python o JavaScript. Això vos estalviarà molt 
d'estrès més endavant. I si escriviu 'answr' en comptes de 'answer', el compilador és
en realitat _amable_ amb això:

```
4 |     println!("Hello {}", answr);
  |                         ^^^^^ did you mean `answer`?

```

La macro `println!` agafa un [format de cadena] (https://doc.rust-lang.org/std/fmt/index.html)
i alguns valors; és molt similar al format utilitzat per Python 3.

Una altra macro molt útil és `assert_eq!`. Aquest és el cavall de batalla de
les proves en Rust; El vostre _assert_ mira que dues coses siguen iguals, i 
si no, _panic_.

```rust
// let2.rs
fn main() {
    let answer = 42;
    assert_eq!(answer,42);
}
```

Cosa que no produirà cap sortida. Però canvia 42 a 40:

```
thread 'main' panicked at
'assertion failed: `(left == right)` (left: `42`, right: `40`)',
let2.rs:4
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```
I aquest ha sigut el nostre primer _runtime error_ en Rust.

## Buclejant i condicionant

Qualsevol cosa interessant es pot fer més d'una vegada:

```rust
// for1.rs
fn main() {
    for i in 0..5 {
        println!("Hello {}", i);
    }
}
```

El _bucle_ no es inclusiu, així que `i` va des de 0 fins a 4. Això és convenient en un 
llenguatge de programació que _indexa_ coses com _arrais_ de 0.

I coses ben interessants han estat fetes amb _condicionants_:

```rust
// for2.rs
fn main() {
    for i in 0..5 {
        if i % 2 == 0 {
            println!("parell {}", i);
        } else {
            println!("imparell {}", i);
        }
    }
}
```

```
parell 0
imparell 1
parell 2
imparell 3
parell 4
```

`i % 2` és zero si 2 pot dividir-se entre `i` de manera neta; Rust usa l'estil d'operadors de C.
Hi ha _no_ claudàtors al voltant de la condició, igual que en Go, però s'han d'utilitzar 
claus al voltant del bloc.

Aquest codi fa el mateix, però escrit d'una manera més interessant:

```rust
// for3.rs
fn main() {
    for i in 0..5 {
        let parell_senar = if i % 2 == 0 {"parell"} else {"imparell"};
        println!("{} {}", parell_senar, i);
    }
}
```

Tradicionalment, els llenguatges de programació tenen _sentències_ 
(com `if`) i _expressions_ (com ara `1+i`). En Rust, gairebé tot té
un valor i pot ser una expressió. El molt lleig 'operador ternari' 
de C 'i % 2 == 0? "parell" : "imparell"' no és necessàri.

Tingueu en compte que no hi ha punt i coma en aquests blocs!

## Afegint cosetes

Els ordinadors són molt bons en aritmètica. Ací teniu un primer intent 
d'afegir tots els números del 0 al 4:

```rust
// add1.rs
fn main() {
    let sum = 0;
    for i in 0..5 {
        sum += i;
    }
    println!("sum is {}", sum);
}
```

Però falla en compilar-lo:

```
error[E0384]: re-assignment of immutable variable `sum`
 --> add1.rs:5:9
3 |     let sum = 0;
  |         --- first assignment to `sum`
4 |     for i in 0..5 {
5 |         sum += i;
  |         ^^^^^^^^ re-assignment of immutable variable

```

'Immutable'? Una variable no pot _variar_?  Les variables `let` per defecte
només poden tindre un valor quan són declarades. Afegint la màgica paraula de
`mut` (_per favor_ fes aquesta variable mutable), aconseguim el truc que necessitàvem:

```rust
// add2.rs
fn main() {
    let mut sum = 0;
    for i in 0..5 {
        sum += i;
    }
    println!("sum is {}", sum);
}
```

Això pot semblar desconcertant quan es prové d'altres llengües, on les variables 
poden ser reescrites per defecte. El que fa que una cosa sigui una "variable" és 
que se li assigna un valor calculat en temps d'execució - no és una _constant_. És 
també com s'utilitza la paraula en matemàtiques, com quan diem 'que n sigui el nombre
més gran en el conjunt S'.

Hi ha una raó per declarar que les variables siguen _read-only_ per defecte. En un
programa més llarg, es fa difícil fer un seguiment d'on tenen lloc les escriptures.
Així que Rust fa coses com la mutabilitat ('capacitat d'escriptura') explícita. Hi 
ha molta intel·ligència en el llenguatge, però intenta no amagar res.

Rust és alhora estàtic i fortament tipat: sovint es confonen, però penseu en C 
(estàtic) i Python (dinàmic). En els tipus estàtics el tipus es coneixen en temps
de compilació, i els dinàmics només es coneixen els tipus en temps d'execució.

De moment, però, sembla que Rust està _amagant_ aquests tipus. Què és exactament el
tipus de `i`? El compilador pot treballar, començant per 0, amb _type inference_, i
apareixer amb `i32` ([el tipus sencer signat amb quatre bytes](https://runebook.dev/es/docs/rust/std/primitive.i32)).

Anem a fer exactament un canvi - canvia el `0` per `0.0`. Llavors obtindrem errors:

```
error[E0277]: the trait bound `{float}: std::ops::AddAssign<{integer}>` is not satisfied
 --> add3.rs:5:9
  |
5 |         sum += i;
  |         ^^^^^^^^ the trait `std::ops::AddAssign<{integer}>` is not implemented for `{float}`
  |

```

D'acord, així que la lluna de mel s'ha acabat: què vol dir això? Cada operador (com `+=`) 
correspon a un _trait_, que és com una interfície abstracta que s'ha d'implementar per a 
cada tipus concret. Més endavant tractarem detalladament els _traits_, però aquí tot el que 
heu de saber és que `AddAssign` és el nom del tret que implementa l'operador `+=`, i 
l'error diu que els nombres en coma flotant no implementen aquest operador per a nombres sencers.
(El llistat complet d'operadors _trait_ el podeu trobar [ací](https://doc.rust-lang.org/std/ops/index.html).)

Una vegada més, a Rust li agrada ser explícit: no convertirà silenciosament aquest sencer en un
número real per a nosaltres.

Tindrem que fer-li _cast_ del valor a número sencer (_float_) explícitament.

```rust
// add3.rs
fn main() {
    let mut sum = 0.0;
    for i in 0..5 {
        sum += i as f64;
    }
    println!("sum is {}", sum);
}
```

## Els tipus de les funcions són explícits

_Functions_ són un lloc on el compilador no treballarà els tipus per nosaltres.
I això, de fet, va ser una decisió deliberada, ja que llenguatges com el Haskell 
amb una inferència de tipus tan potent, amb prou feines hi ha noms de tipus explícits.
És realment el bon estil Haskell de posar explícitament el tipus de signatures per 
les funcions el que ha fet inspirar a Rust per usar-lo sempre.

Acñi trobem una simple funció definida per l'usuari:

```rust
// fun1.rs

fn sqr(x: f64) -> f64 {
    return x * x;
}

fn main() {
    let res = sqr(2.0);
    println!("square is {}", res);
}
```

Rust torna a un estil més antic de declaració d'arguments, on el tipus segueix 
el nom. Això és com es feia en llenguatges derivats de l'Algol o com el Pascal.

De nou, no hi ha conversions de sencer a real: si substituim `2.0` per `2`, 
llavors obtenim un error clar:

```
8 |     let res = sqr(2);
  |                   ^ expected f64, found integral variable
  |
```

De fet, poques vegades veurem funcions escrites mitjançant una instrucció 
`return`. Més Sovint, s'assemblarà a la següent:

```rust
fn sqr(x: f64) -> f64 {
    x * x
}
```

Això es deu al fet que el cos de la funció (dins de `{}`) té el valor de la
seva última expressió, igual que amb if-as-an-expression.

Atès que el punt i coma s'insereix semi-automàticament pels dits humans, podeu
afegir-lo aquí i obtindreu el següent error:

```
  |
3 | fn sqr(x: f64) -> f64 {
  |                       ^ expected f64, found ()
  |
  = note: expected type `f64`
  = note:    found type `()`
help: consider removing this semicolon:
 --> fun2.rs:4:8
  |
4 |     x * x;
  |       ^

```

Els `()`són el tipus buit, el no res, `void, zilch, nothing. Tot en Rust té un valor,
però de vegades el valor és res. El compilador sap que això és un error comú, i en 
realitat _ajuda_. (Qualsevol persona que haja passat temps amb el compilador C++ sabrà
com és de _inusual_ això.)

Uns pocs més exemples de l'estil d'expressió de no-retorn:

```rust
// valor absolut del número real
fn abs(x: f64) -> f64 {
    if x > 0.0 {
        x
    } else {
        -x
    }
}

// assegurar-se que el número sempre cau en el rang donat
fn clamp(x: f64, x1: f64, x2: f64) -> f64 {
    if x < x1 {
        x1
    } else if x > x2 {
        x2
    } else {
        x
    }
}
```

No està malament utilitzar `return`, però el codi és més net sense ell. Encara
usareu `return` per a _returning early_ des d'una funció.

Algunes operacions poden ser elegantment expressades amb _recursivitat_:

```rust
fn factorial(n: u64) -> u64 {
    if n == 0 {
        1
    } else {
        n * factorial(n-1)
    }
}
```
Això pot ser una mica estrany al principi, i el millor és utilitzar llapis i paper i 
elabor alguns exemples. No sol ser la manera més _eficient_ de fer-ho operació...

Els valors també es poden passar per _referència_. Una referència és creada amb `&` 
i _dereferenciada_ amb `*`.

```rust
fn by_ref(x: &i32) -> i32{
    *x + 1
}

fn main() {
    let i = 10;
    let res1 = by_ref(&i);
    let res2 = by_ref(&41);
    println!("{} {}", res1,res2);
}
// 11 42
```
I si volem que una funció modifique un dels seus arguments?  Doncs usem _referències mutables_:

```rust
// fun4.rs

fn modifies(x: &mut f64) {
    *x = 1.0;
}

fn main() {
    let mut res = 0.0;
    modifies(&mut res);
    println!("res is {}", res);
}
```
Així és més com ho faria C que C++. Cal que passem explícitament la referència 
(amb `&`) i explícitament _dereferenciem_ amb `*`. I llavors llançar `mut` perquè 
no és per defecte. (Sempre he sentit que les referències C++ són massa fàcil de
perdre en comparació amb C.)

Bàsicament, Rust està introduint una mica de _fricció_ ací, i no tan subtilment 
empenyent-nos cap al retorn directe de valors de funcions. Afortunadament, Rust té
poderoses maneres d'expressar coses com "operation succeeded and here's the result".
Per tant, `&mut` no és necessari tan sovint. Passar per referència és important quan
tenim un objecte gran i no voleu copiar-lo.

L'estil variable-rere-tipus també s'aplica a `let`, quan realment voleu precisar el 
tipus de variable:

```rust
let bigint: i64 = 0;
```

## Aprenent on trobar les cordes

És hora de començar a usar la documentació. Aquesta s'haurà instal·lat a la nostra 
màquina, i la podrem usar amb la comanda de terminal `rustup doc --std` que ens
obrirà automàticament una finestra del navegador.

Fixeu-se en el camp _search_ de la part superior, ja que aquest serà el nostre amic; 
Funciona completament fora de línia.

Suposem que volem veure on són les funcions matemàtiques, així que cerqueu 'cos'. Les
dos primeres coincidènices mostren que està definida per a tots dos nombres reals i de 
doble precisió. Està definida sobre el _value itself_ com a mètode, així:

```rust
let pi: f64 = 3.1416;
let x = pi/2.0;
let cosine = x.cos();
```

I el resultat serà una espècie de zero; Òbviament, necessitarem una font més autoritzada.

(Perquè necessitarem un tipus explícit `f64`? Doncs perque sense ell, la constant podria ser
de tipus `f32`o `f64`, i aquestes tipus són ben diferents.)

Permeteu-me citar l'exemple donat per `cos`, però escrit com un programa complet (`assert!` 
és un cosí de `assert_eq!`; l'expressió ha de ser certa):

```rust
fn main() {
    let x = 2.0 * std::f64::consts::PI;

    let abs_difference = (x.cos() - 1.0).abs();

    assert!(abs_difference < 1e-10);
}
```
`std::f64::consts::PI` és un embarbussament! `::` significa el mateix que en C++,
(sovint s'escriu usant '.' en altres llenguatges) - és un nom _fully qualified_. Així
aconseguim aquest nom complet per la segona pista en la recerca de la constant `PI`.

Fins ara, els nostres petits programes de Rust han estat lliures de tots aquests 
ìmport` i ìnclude`, ítems que tendeixen a alentir la representació dels programes 'Hello World'.
Anem a fer aquest programa més llegible amb la sentència ùse`:

```rust
use std::f64::consts;

fn main() {
    let x = 2.0 * consts::PI;

    let abs_difference = (x.cos() - 1.0).abs();

    assert!(abs_difference < 1e-10);
}
```
Per què no ho hem necessitat fins ara? Això es perquè Rust fa visible moltes 
funcionalitats bàsiques sense explicitar la sentència `use` a través del Rust _prelude_.

## Arrais i Porcions

Tot llenguatge tipat estàtic té _arrays_, els quals són valors empaquetats de dalt a abaix
en memporia. Els arrais estàn _indexats_ des de zero:

```rust
// array1.rs
fn main() {
    let arr = [10, 20, 30, 40];
    let first = arr[0];
    println!("first {}", first);

    for i in 0..4 {
        println!("[{}] = {}", i,arr[i]);
    }
    println!("length {}", arr.len());
}
```

I el output és:

```
first 10
[0] = 10
[1] = 20
[2] = 30
[3] = 40
length 4
```

En aquest cas, Rust coneix _exactament_ com de gran és l'arrrai, i si podem
provar d'accedir a `arr[4]` obtindrem un _error de compilació_.

Aprendre un nou llenguatge sovint implica _desaprendre_ hàbits adquirits d'altres
llenguatges ja sabuts; si forem una Pitonisa, llavors aquests parentesis dirien `List`.
Veurem l'equivalent en Rust de `List`ben aviat, però els arrais no són androids que anem
cercant; aquests són de _grandària prefixada_. També poden ser _mutables_ (si els ho
demanem amablement), però no podrem afegir-hi nou elements.

Els arrais no s'usen molt sovint en Rust, perquè aquest tipus de dada inclou definir la
grandària. El tipus d'arrai en l'exemple és `[i32; 4]`; el tipus de `[10, 20]` podria ser
`[i32; 2]` i així successivament: aquests tenen _diferents tipus_. Per tant són uns
punyeters per passa-los com a arguments de funcions.

El _que s'usa_ més freqüentment són els _slices_ (porcions). Podem veure aquestes com a
_views_ en una gamma subjacent de valors. Altrament, es comporten molt com un arrai, i 
_saben la seva grandària_, a diferència d'aquells perillosos animals de C anomenats punters.

Hem de tindre en compte dues coses importants ací: com escriure el tipus d'una porció, i que
hem d'utilitzar `&` per passar-ho a la funció.

```rust
// array2.rs
// read as: slice of i32
fn sum(values: &[i32]) -> i32 {
    let mut res = 0;
    for i in 0..values.len() {
        res += values[i]
    }
    res
}

fn main() {
    let arr = [10,20,30,40];
    // look at that &
    let res = sum(&arr);
    println!("sum {}", res);
}
```

Ignorem el codi `sum`per un temps, i mirem el `&[i32]`. La relació entre els arrais i
els slices en Rust són similars a les que hi ha entre els arrais en C i els punters,
excepte per dos importants diferències - les _slices_ mantenen la traçabilitat de la
seva grandària (i entraran en pànic si intentem accedir fora dels seus límits) i tenim
que dir explícitament que volem passar un arrai com a un _slice_ usant l'operador `&`.

Un programador de C té en ment `&` com una 'adreça de'; però un programador de Rust ho 
veu com un 'prèstec'. Aquesta serà la paraula clau aprenent Rust. Prestar és el nom donat
a un patró comú en programació; com cada vegada que passem alguna cosa per referència (com 
passa gairebé sempre en els llenguatges dinàmics) o passem un punter en C. Qualsevol cosa
prestada continua pertanyent al propietari original.

## Fent porcions i tallant a daus

No podem imprimir un arrai de la manera usual amb `{}`, però podem fer un _debug_
imprimint amb `{:?}`.

```rust
// array3.rs
fn main() {
    let ints = [1, 2, 3];
    let floats = [1.1, 2.1, 3.1];
    let strings = ["hello", "world"];
    let ints_ints = [[1, 2], [10, 20]];
    println!("ints {:?}", ints);
    println!("floats {:?}", floats);
    println!("strings {:?}", strings);
    println!("ints_ints {:?}", ints_ints);
}
```

El qual ens dona:

```
ints [1, 2, 3]
floats [1.1, 2.1, 3.1]
strings ["hello", "world"]
ints_ints [[1, 2], [10, 20]]
```
Per tant, els arrais d'arrais no són un problema; però la cosa més important és que un
arrai conté valors de _només un tipus_. Els valors en un arrai estàn un al costat de 
l'altre en memòria, per tant és _molt_ eficient el seu accés.

Si teniu curiositat pels tipus actuals d'aquestes variables, ací trobareu un truquet
molt útil. Tant sols declara una variable amb un tipus explícit que sàpigues que és 
incorrecte:

```rust
let var: () = [1.1, 1.2];
```
Ací està l'error ben ecplicadet:

```
3 |     let var: () = [1.1, 1.2];
  |                   ^^^^^^^^^^ expected (), found array of 2 elements
  |
  = note: expected type `()`
  = note:    found type `[{float}; 2]`
```
(`{float}` means 'some floating-point type which is not fully specified yet')

_Slices_ ens dona diferents _views_ del _mateix_ arrai:

```rust
// slice1.rs
fn main() {
    let ints = [1, 2, 3, 4, 5];
    let slice1 = &ints[0..2];
    let slice2 = &ints[1..];  // rang obert!

    println!("ints {:?}", ints);
    println!("slice1 {:?}", slice1);
    println!("slice2 {:?}", slice2);
}
```

```
ints [1, 2, 3, 4, 5]
slice1 [1, 2]
slice2 [2, 3, 4, 5]
```

Aquesta és una notació ordenada que s'asembla a lade Python però amb grans diferències:
no es fa mai una còpia de les dades. Tots aquests _slices_ presten les seves dades des
del seu arrai. Ells tenen una molt intima relació amb l'arrai, i Rust utilitza molt 
d'esforç en assegurar que la relació no es trenque.

## Valors opcionals

Els _slices_, com els arrais, poden _indexar-se_. Rust coneix la grandària de l'arrai en
temps de compilació, però la grandària d'un _slice_ és només coneguda en temps d'execució.
Per tant `s[i]` pot causar un _out of bounds error_ quan s'executa. Açò realment no volem
que passe - esta pot ser la diferència entre un abortament de llançament segur i escampar
peces d'un satèl·lit molt car per tota Florida. I _no hi ha exepcions_.

Deixa que s'acomode la idea, perquè és bastant xocant. No podem envoltar un codi
_dodgy-may-panic_ en un bloc tru-catch i atrapar l'error - com a mínim no de la manera en
la que volem usar-ho a diari. Llavors, com podem estar segurs en Rust?

Aquest és un mètode _slice_ que no espanta. Però què retornar?

```rust
// slice2.rs
fn main() {
    let ints = [1, 2, 3, 4, 5];
    let slice = &ints;
    let first = slice.get(0);
    let last = slice.get(5);

    println!("first {:?}", first);
    println!("last {:?}", last);
}
// first Some(1)
// last None
```
`last`falla (hem oblidat la indexació basada en zero), però retorna una cosa anomenada `None`.
`first`funciona, però apareix com a un valor embolicat en `Some`. Benvinguts als `tipus d'opcions`!
Aquestes poden ser `Some`o `None`.

Els tipus d'`opcions` tenen alguns mètodes ben útils:

```rust
    println!("first {} {}", first.is_some(), first.is_none());
    println!("last {} {}", last.is_some(), last.is_none());
    println!("first value {}", first.unwrap());

// first true false
// last false true
// first value 1
```
Si heguerem _desembolicat_ `last, haguerem obtés un panic error. Però com a mínim podem cridar
`is_some` primer per assegurar-nos - per exemple, si teníem un valor diferent per defecte:`

```rust
    let maybe_last = slice.get(5);
    let last = if maybe_last.is_some() {
        *maybe_last.unwrap()
    } else {
        -1
    };
```
Cal tindre en compte el símbol `*` - el tipus precís de `Some` és `&i32`, que és una referècia.
Necessitem desreferenciar-ho per obtenir un valor en `i32.




Which is long-winded, so there's a shortcut - `unwrap_or` will return the value it
is given if the `Option` was `None`. The types must match up - `get` returns
a reference. so you have to make up a `&i32` with `&-1`. Finally, again use `*`
to get the value as `i32`.

```rust
    let last = *slice.get(5).unwrap_or(&-1);
```

It's easy to miss the `&`, but the compiler has your back here. If it was `-1`,
`rustc` says 'expected &{integer}, found integral variable' and then
'help: try with `&-1`'.

You can think of `Option` as a box which may contain a value, or nothing (`None`).
(It is called `Maybe` in Haskell). It may contain _any_ kind of value, which is
its _type parameter_. In this case, the full type is `Option<&i32>`, using
C++-style notation for _generics_.  Unwrapping this box may cause an explosion,
but unlike Schroedinger's Cat, we can know in advance if it contains a value.

It is very common for Rust functions/methods to return such maybe-boxes, so learn
how to [use them](https://doc.rust-lang.org/std/option/enum.Option.html) comfortably.

## Vectors

We'll return to slice methods again, but first: vectors. These are _re-sizeable_
arrays and behave much like Python `List` and C++ `std::vector`. The Rust type
`Vec` (pronounced 'vector') behaves very much like a slice in fact; the
difference is that you can append extra values to a vector - note that it must
be declared as mutable.

```rust
// vec1.rs
fn main() {
    let mut v = Vec::new();
    v.push(10);
    v.push(20);
    v.push(30);

    let first = v[0];  // will panic if out-of-range
    let maybe_first = v.get(0);

    println!("v is {:?}", v);
    println!("first is {}", first);
    println!("maybe_first is {:?}", maybe_first);
}
// v is [10, 20, 30]
// first is 10
// maybe_first is Some(10)
```
A common beginner mistake is to forget the `mut`; you will get a helpful error
message:

```
3 |     let v = Vec::new();
  |         - use `mut v` here to make mutable
4 |     v.push(10);
  |     ^ cannot borrow mutably
```

There is a very intimate relation between vectors and slices:

```rust
// vec2.rs
fn dump(arr: &[i32]) {
    println!("arr is {:?}", arr);
}

fn main() {
    let mut v = Vec::new();
    v.push(10);
    v.push(20);
    v.push(30);

    dump(&v);

    let slice = &v[1..];
    println!("slice is {:?}", slice);
}
```
That little, so-important borrow operator `&` is _coercing_ the vector into a
slice. And it makes complete sense, because the vector is also looking after an array of
values, with the difference that the array is allocated _dynamically_.

If you come from a dynamic language, now is time for that little talk. In systems
languages, program memory comes in two kinds: the stack and the heap. It is very fast
to allocate data on the stack, but the stack is limited; typically of the order of
megabytes. The heap can be gigabytes, but allocating is relatively expensive, and
such memory must be _freed_ later. In so-called 'managed' languages (like Java, Go
and the so-called 'scripting' languages) these details are hidden from you by that
convenient municipal utility called the _garbage collector_. Once the system is sure
that data is no longer referenced by other data, it goes back into the pool
of available memory.

Generally, this is a price worth paying. Playing with the stack is terribly unsafe,
because if you make one mistake you can override the return address of the current
function, and you die an ignominious death or (worse) got pwned by some guy living
in his Mom's basement in Minsk.

The first C program I wrote (on a DOS PC)
took out the whole computer. Unix systems always behaved better, and only the process died
with a _segfault_. Why is this worse than a Rust (or Go) program panicking?
Because a panic happens when the original problem happens, not when the program
has become hopelessly confused and eaten all your homework. Panics are _memory safe_
because they happen before any illegal access to memory. This is a common cause of
security problems in C, because all memory accesses are unsafe and a cunning attacker
can exploit this weakness.

Panicking sounds desperate and unplanned, but Rust panics are structured - the stack is _unwound_
just as with exceptions. All allocated objects are dropped, and a backtrace is generated.

The downsides of garbage collection? The first is that it is wasteful of memory, which
matters in those small embedded microchips which increasingly rule our world. The
second is that it will decide, at the worst possible time, that a clean up must happen
_now_. (The Mom analogy is that she wants to clean your room when you are at a
delicate stage with a new lover). Those embedded systems need to respond to things
_when they happen_ ('real-time') and can't tolerate unscheduled outbreaks of
cleaning. Roberto Ierusalimschy, the chief designer of Lua (one of the most elegant
dynamic languages ever) said that he would not like to fly on an airplane that
relied on garbage-collected software.

Back to vectors: when a vector is modified or created, it allocates from the heap and becomes
 the _owner_ of that memory. The slice _borrows_ the memory from the vector.
When the vector dies or _drops_, it lets the memory go.

## Iterators

We have got so far without mentioning a key part of the Rust puzzle - iterators.
The for-loop over a range was using an iterator (`0..n` is actually similar to the
Python 3 `range` function).

An iterator is easy to define informally. It is an 'object' with a `next` method
which returns an `Option`. As long as that value is not `None`, we keep calling
`next`:

```rust
// iter1.rs
fn main() {
    let mut iter = 0..3;
    assert_eq!(iter.next(), Some(0));
    assert_eq!(iter.next(), Some(1));
    assert_eq!(iter.next(), Some(2));
    assert_eq!(iter.next(), None);
}
```
And that is exactly what `for var in iter {}` does.

This may seem an inefficient way to define a for-loop, but `rustc` does crazy-ass
optimizations in release mode and it will be just as fast as a `while` loop.

Here is the first attempt to iterate over an array:

```rust
// iter2.rs
fn main() {
    let arr = [10, 20, 30];
    for i in arr {
        println!("{}", i);
    }
}
```
which fails, but helpfully:
```
4 |     for i in arr {
  |     ^ the trait `std::iter::Iterator` is not implemented for `[{integer}; 3]`
  |
  = note: `[{integer}; 3]` is not an iterator; maybe try calling
   `.iter()` or a similar method
  = note: required by `std::iter::IntoIterator::into_iter`
```

Following `rustc`'s advice, the following program works as expected.

```rust
// iter3.rs
fn main() {
    let arr = [10, 20, 30];
    for i in arr.iter() {
        println!("{}", i);
    }

    // slices will be converted implicitly to iterators...
    let slice = &arr;
    for i in slice {
        println!("{}", i);
    }
}
```
In fact, it is more efficient to iterate over an array or slice this way
than to use `for i in 0..slice.len() {}` because Rust does not have to obsessively
check every index operation.

We had an example of summing up a range of integers earlier. It involved a `mut`
variable and a loop. Here's the _idiomatic_, pro-level way of doing the sum:

```rust
// sum1.rs
fn main() {
    let sum: i32  = (0..5).sum();
    println!("sum was {}", sum);

    let sum: i64 = [10, 20, 30].iter().sum();
    println!("sum was {}", sum);
}
```

Note that this is one of those cases where you need to be explicit about
the _type_ of the variable, since otherwise Rust doesn't have enough information.
Here we do sums with two different integer sizes, no problem. (It is also no
problem to create a new variable of the same name if you run out of names to
give things.)

With this background, some more of the [slice methods](https://doc.rust-lang.org/std/primitive.slice.html)
will make more sense.
(Another documentation tip; on the right-hand side of every doc page there's a '[-]' which you can
click to collapse the method list. You can then expand the details of anything
that looks interesting. Anything that looks too weird, just ignore for now.)

The `windows` method gives you an iterator of slices - overlapping windows of
values!

```rust
// slice4.rs
fn main() {
    let ints = [1, 2, 3, 4, 5];
    let slice = &ints;

    for s in slice.windows(2) {
        println!("window {:?}", s);
    }
}
// window [1, 2]
// window [2, 3]
// window [3, 4]
// window [4, 5]
```
Or `chunks`:

```rust
    for s in slice.chunks(2) {
        println!("chunks {:?}", s);
    }
// chunks [1, 2]
// chunks [3, 4]
// chunks [5]
```

## More about vectors...

There is a useful little macro `vec!` for initializing a vector. Note that you
can _remove_ values from the end of a vector using `pop`, and _extend_ a vector
using any compatible iterator.

```rust
// vec3.rs
fn main() {
    let mut v1 = vec![10, 20, 30, 40];
    v1.pop();

    let mut v2 = Vec::new();
    v2.push(10);
    v2.push(20);
    v2.push(30);

    assert_eq!(v1, v2);

    v2.extend(0..2);
    assert_eq!(v2, &[10, 20, 30, 0, 1]);
}
```
Vectors compare with each other and with slices by value.

You can insert values into a vector at arbitrary positions with `insert`,
and remove with `remove`. This is not as efficient as pushing and popping since
the values will have to be moved to make room, so watch out for these operations on big
vectors.

Vectors have a size and a _capacity_. If you `clear` a vector, its size becomes zero,
but it still retains its old capacity. So refilling it with `push`, etc only requires
reallocation when the size gets larger than that capacity.

Vectors can be sorted, and then duplicates can be removed - these operate in-place
on the vector. (If you want to make a copy first, use `clone`.)

```rust
// vec4.rs
fn main() {
    let mut v1 = vec![1, 10, 5, 1, 2, 11, 2, 40];
    v1.sort();
    v1.dedup();
    assert_eq!(v1, &[1, 2, 5, 10, 11, 40]);
}
```

## Strings

Strings in Rust are a little more involved than in other languages; the `String` type,
like `Vec`, allocates dynamically and is resizeable. (So it's like C++'s `std::string`
but not like the immutable strings of Java and Python.) But a program may contain a lot
of _string literals_ (like "hello") and a system language should be able to store
these statically in the executable itself. In embedded micros, that could mean putting
them in cheap ROM rather than expensive RAM (for low-power devices, RAM is
also expensive in terms of power consumption.) A _system_ language has to have
two kinds of string, allocated or static.

So "hello" is not of type `String`. It is of type `&str` (pronounced 'string slice').
It's like the distinction between `const char*` and `std::string` in C++, except
`&str` is much more intelligent.  In fact, `&str` and `String` have a very
similar relationship to each other as do `&[T]` to `Vec<T>`.

```rust
// string1.rs
fn dump(s: &str) {
    println!("str '{}'", s);
}

fn main() {
    let text = "hello dolly";  // the string slice
    let s = text.to_string();  // it's now an allocated string

    dump(text);
    dump(&s);
}
```
Again, the borrow operator can coerce `String` into `&str`, just as `Vec<T>` could
be coerced into `&[T]`.

Under the hood, `String` is basically a `Vec<u8>` and `&str` is `&[u8]`, but
those bytes _must_ represent valid UTF-8 text.

Like a vector, you can `push` a character and `pop` one off the end of `String`:

```rust
// string5.rs
fn main() {
    let mut s = String::new();
    // initially empty!
    s.push('H');
    s.push_str("ello");
    s.push(' ');
    s += "World!"; // short for `push_str`
    // remove the last char
    s.pop();

    assert_eq!(s, "Hello World");
}
```
You can convert many types to strings using `to_string`
(if you can display them with '{}' then they can be converted).
The `format!` macro is a very useful way to build
up more complicated strings using the same format strings as `println!`.

```rust
// string6.rs
fn array_to_str(arr: &[i32]) -> String {
    let mut res = '['.to_string();
    for v in arr {
        res += &v.to_string();
        res.push(',');
    }
    res.pop();
    res.push(']');
    res
}

fn main() {
    let arr = array_to_str(&[10, 20, 30]);
    let res = format!("hello {}", arr);

    assert_eq!(res, "hello [10,20,30]");
}
```
Note the `&` in front of `v.to_string()` - the `+=` operator is defined on a string
slice, not a `String` itself, so it needs a little persuasion to match.

The notation used for slices works with strings as well:

```rust
// string2.rs
fn main() {
    let text = "static";
    let string = "dynamic".to_string();

    let text_s = &text[1..];
    let string_s = &string[2..4];

    println!("slices {:?} {:?}", text_s, string_s);
}
// slices "tatic" "na"
```

But, you cannot index strings!  This is because they use the One True Encoding,
UTF-8, where a 'character' may be a number of bytes.

```rust
// string3.rs
fn main() {
    let multilingual = "Hi! ¡Hola! привет!";
    for ch in multilingual.chars() {
        print!("'{}' ", ch);
    }
    println!("");
    println!("len {}", multilingual.len());
    println!("count {}", multilingual.chars().count());

    let maybe = multilingual.find('п');
    if maybe.is_some() {
        let hi = &multilingual[maybe.unwrap()..];
        println!("Russian hi {}", hi);
    }
}
// 'H' 'i' '!' ' ' '¡' 'H' 'o' 'l' 'a' '!' ' ' 'п' 'р' 'и' 'в' 'е' 'т' '!'
// len 25
// count 18
// Russian hi привет!
```

Now, let that sink in - there are 25 bytes, but only 18 characters! However, if
you use a method like `find`, you will get a valid index (if found) and then
any slice will be fine.

(The Rust `char` type is a 4-byte Unicode code point. Strings are _not_ arrays
of chars!)

String slicing may explode like vector indexing, because it uses byte offsets. In this case,
the string consists of two bytes, so trying to pull out the first byte is a Unicode error. So be
careful to only slice strings using valid offsets that come from string methods.

```rust
    let s = "¡";
    println!("{}", &s[0..1]); <-- bad, first byte of a multibyte character
```

Breaking up strings is a popular and useful pastime. The string `split_whitespace`
method returns an _iterator_, and we then choose what to do with it. A common need
is to create a vector of the split substrings.

`collect` is very general and so needs some clues about _what_ it is collecting - hence
the explicit type.

```rust
    let text = "the red fox and the lazy dog";
    let words: Vec<&str> = text.split_whitespace().collect();
    // ["the", "red", "fox", "and", "the", "lazy", "dog"]
```
You could also say it like this, passing the iterator into the `extend` method:

```rust
    let mut words = Vec::new();
    words.extend(text.split_whitespace());
```
In most languages, we would have to make these _separately allocated strings_,
whereas here each slice in the vector is borrowing from the original string.
All we allocate is the space to keep the slices.

Have a look at this cute two-liner; we get an iterator over the chars,
and only take those characters which are not space. Again, `collect` needs
a clue (we may have wanted a vector of chars, say):

```rust
    let stripped: String = text.chars()
        .filter(|ch| ! ch.is_whitespace()).collect();
    // theredfoxandthelazydog
```
The `filter` method takes a _closure_, which is Rust-speak for
lambdas or anonymous functions.  Here the argument type is clear from the
context, so the explicit rule is relaxed.

Yes, you can do this as an explicit loop over chars, pushing the returned slices
into a mutable vector, but this is shorter, reads well (_when_ you are used to it,
of course) and just as fast. It is not a _sin_ to use a loop, however, and I encourage
you to write that version as well.

## Interlude: Getting Command Line Arguments

Up to now our programs have lived in blissful ignorance of the outside world; now
it's time to feed them data.

`std::env::args` is how you access command-line arguments; it returns an iterator
over the arguments as strings, including the program name.

```rust
// args0.rs
fn main() {
    for arg in std::env::args() {
        println!("'{}'", arg);
    }
}
```
```
src$ rustc args0.rs
src$ ./args0 42 'hello dolly' frodo
'./args0'
'42'
'hello dolly'
'frodo'
```
Would it have been better to return a `Vec`? It's easy enough to use `collect` to
make that vector, using the iterator `skip` method to move past the program
name.

```rust
    let args: Vec<String> = std::env::args().skip(1).collect();
    if args.len() > 0 { // we have args!
        ...
    }
```
Which is fine; it's pretty much how you would do it in most languages.

A more Rust-y approach to reading a single argument (together with parsing an
integer value):

```rust
// args1.rs
use std::env;

fn main() {
    let first = env::args().nth(1).expect("please supply an argument");
    let n: i32 = first.parse().expect("not an integer!");
    // do your magic
}
```
`nth(1)` gives you the second value of the iterator, and `expect`
is like an `unwrap` with a readable message.

Converting a string into a number is straightforward, but you do need to specify
the type of the value - how else could `parse` know?

This program can panic, which is fine for dinky test programs. But don't get too
comfortable with this convenient habit.

## Matching

The code in `string3.rs` where we extract the Russian greeting is not how it would
be usually written. Enter _match_:

```rust
    match multilingual.find('п') {
        Some(idx) => {
            let hi = &multilingual[idx..];
            println!("Russian hi {}", hi);
        },
        None => println!("couldn't find the greeting, Товарищ")
    };
```
`match` consists of several _patterns_ with a matching value following the fat arrow,
separated by commas.  It has conveniently unwrapped the value from the `Option` and
bound it to `idx`.  You _must_ specify all the possibilities, so we have to handle
`None`.

Once you are used to it (and by that I mean, typed it out in full a few times) it
feels more natural than the explicit `is_some` check which needed an extra
variable to store the `Option`.

But if you're not interested in failure here, then `if let` is your friend:

```rust
    if let Some(idx) = multilingual.find('п') {
        println!("Russian hi {}", &multilingual[idx..]);
    }
```
This is convenient if you want to do a match and are _only_ interested in one possible
result.

`match` can also operate like a C `switch` statement, and like other Rust constructs
can return a value:

```rust
    let text = match n {
        0 => "zero",
        1 => "one",
        2 => "two",
        _ => "many",
    };
```
The `_` is like C `default` - it's a fall-back case. If you don't provide one then
`rustc` will consider it an error. (In C++ the best you can expect is a warning, which
says a lot about the respective languages).

Rust `match` statements can also match on ranges. Note that these ranges have
_three_ dots and are inclusive ranges, so that the first condition would match 3.

```rust
    let text = match n {
        0...3 => "small",
        4...6 => "medium",
        _ => "large",
     };
```
## Reading from Files

The next step to exposing our programs to the world is to _reading files_.

Recall that `expect` is like `unwrap` but gives a custom error message. We are
going to throw away a few errors here:

```rust
// file1.rs
use std::env;
use std::fs::File;
use std::io::Read;

fn main() {
    let first = env::args().nth(1).expect("please supply a filename");

    let mut file = File::open(&first).expect("can't open the file");

    let mut text = String::new();
    file.read_to_string(&mut text).expect("can't read the file");

    println!("file had {} bytes", text.len());

}
```
```
src$ file1 file1.rs
file had 366 bytes
src$ ./file1 frodo.txt
thread 'main' panicked at 'can't open the file: Error { repr: Os { code: 2, message: "No such file or directory" } }', ../src/libcore/result.rs:837
note: Run with `RUST_BACKTRACE=1` for a backtrace.
src$ file1 file1
thread 'main' panicked at 'can't read the file: Error { repr: Custom(Custom { kind: InvalidData, error: StringError("stream did not contain valid UTF-8") }) }', ../src/libcore/result.rs:837
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```
So `open` can fail because the file doesn't exist or we aren't allowed to read it,
and `read_to_string` can fail because the file doesn't contain valid UTF-8. (Which is
fair enough, you can use `read_to_end` and put the contents into a vector of bytes
instead.) For files that aren't too big, reading them in one gulp is useful and
straightforward.

If you know anything about file handling in other languages, you may wonder when
the file is _closed_. If we were writing to this file, then not closing it could
result in loss of data.
But the file here is closed when the function ends and the `file` variable is _dropped_.

This 'throwing away errors' thing is getting too much of a habit. You do not
want to put this code into a function, knowing that it could so easily crash
the whole program.  So now we have to talk about exactly what `File::open` returns.
If `Option` is a value that may contain something or nothing, then `Result` is a value
that may contain something or an error. They both understand `unwrap` (and its cousin
`expect`) but they are quite different. `Result` is defined by _two_ type parameters,
for the `Ok` value and the `Err` value.
The `Result` 'box' has two compartments, one labelled `Ok` and the other `Err`.

```rust
fn good_or_bad(good: bool) -> Result<i32,String> {
    if good {
        Ok(42)
    } else {
        Err("bad".to_string())
    }
}

fn main() {
    println!("{:?}",good_or_bad(true));
    //Ok(42)
    println!("{:?}",good_or_bad(false));
    //Err("bad")

    match good_or_bad(true) {
        Ok(n) => println!("Cool, I got {}",n),
        Err(e) => println!("Huh, I just got {}",e)
    }
    // Cool, I got 42

}
```
(The actual 'error' type is arbitrary - a lot of people use strings until
they are comfortable with Rust error types.) It's a convenient way to _either_
return one value _or_ another.

This version of the file reading function does not crash. It returns a `Result` and
it is the _caller_ who must decide how to handle the error.

```rust
// file2.rs
use std::env;
use std::fs::File;
use std::io::Read;
use std::io;

fn read_to_string(filename: &str) -> Result<String,io::Error> {
    let mut file = match File::open(&filename) {
        Ok(f) => f,
        Err(e) => return Err(e),
    };
    let mut text = String::new();
    match file.read_to_string(&mut text) {
        Ok(_) => Ok(text),
        Err(e) => Err(e),
    }
}

fn main() {
    let file = env::args().nth(1).expect("please supply a filename");

    let text = read_to_string(&file).expect("bad file man!");

    println!("file had {} bytes", text.len());
}
```

The first match safely extracts the value from `Ok`, which
becomes the value of the match. If it's `Err` it returns the error,
rewrapped as an `Err`.

If successful, the second match returns the number of bytes which were
read and appended to `text`, wrapped up as an `Ok`, otherwise (again)
the error. The actual value in the `Ok` is unimportant, so we ignore
it with `_`.

This is not so pretty; when most of a function is error handling, then
the 'happy path' gets lost. Go tends to have this problem, with lots of
explicit early returns, or just _ignoring errors_.  (That is, by the way,
the closest thing to evil in the Rust universe.)

Fortunately, there is a shortcut.

The `std::io` module defines a type alias `io::Result<T>` which is exactly
the same as `Result<T,io::Error>` and easier to type.

```rust
fn read_to_string(filename: &str) -> io::Result<String> {
    let mut file = File::open(&filename)?;
    let mut text = String::new();
    file.read_to_string(&mut text)?;
    Ok(text)
}
```
That `?` operator does almost exactly what the match on `File::open` does;
if the result was an error, then it will immediately return that error.
Otherwise, it returns the `Ok` result.
At the end, we still need to wrap up the string as a result.

2017 was a good year for Rust, and `?` was one of the cool things that
became stable. You will still see the macro `try!` used in older code:

```rust
fn read_to_string(filename: &str) -> io::Result<String> {
    let mut file = try!(File::open(&filename));
    let mut text = String::new();
    try!(file.read_to_string(&mut text));
    Ok(text)
}
```

In summary, it's possible to write perfectly safe Rust that isn't ugly, without
needing exceptions.


