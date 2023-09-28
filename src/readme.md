# Una dolça introducció al Rust

![Rust](PPrustS.png)

[thanks to David Marino](http://leftoversalad.com/c/015_programmingpeople/)

## Perquè aprendre un nou llenguatge de programació?

L'objectiu d'aquest tutorial és portar-vos a un punt on pugueu llegir i escriure 
suficient Rust per apreciar plenament els excel·lents recursos d'aprenentatge 
disponibles en línia, en particular l'anomenat [The Book](https://doc.rust-lang.org/stable/book/).
És una oportunitat per _provar abans de comprar_, i obtenir suficients sensacions
sobre la força del llenguatge amb el que es vol aprofundir més.

Com podria haver dit Einstein, "Tan dolç com sigua possible, però no massa dolç". Hi ha un un 
munt de coses noves per aprendre ací, i és prou diferent com per requerir alguna reordenació dels
nostres mobles mentals. Per 'dolça' vull dir que les característiques són presentades pràcticament 
amb exemples; a mesura que anem trobant dificultats, espere mostrar com Rust resol aquests problemes. 
És important entendre els problemes abans que les solucions tinguen sentit. Per dir-ho en amb una mica
més de floritura, anem d'excursió a un país muntanyós i assenyalarem algunes formacions rocoses interessants
pel camí, sabent sols unes poques lliçons sobre geologia. Hi haurà alguna pujada però la vista serà inspiradora; 
La comunitat és inusualment agradable i està molt contenta podent ajudar. Existeixen també els [Forums d'usuaris Rust](https://users.rust-lang.org/) 
i una activa vida a [Reddit](https://www.reddit.com/r/rust/) que està inusualment ben moderada.
Les xarxes socials són també un bon recurs si vos trobeu amb una pregunta
específica: 
    - [Twitter](https://twitter.com/rustlang)
    - [YouTube](https://www.youtube.com/channel/UCaYhcUwRBNscFNUKTjgPFiA)
    - [Discord](https://discord.com/invite/rust-lang)
    - [GitHub](https://github.com/rust-lang)

En primer lloc, per què aprendre un nou llenguatge de programació? És una inversió 
de temps i energia i això necessita alguna justificació. Fins i tot si no vos toca 
immediatament en un treball genial utilitzant aquest llenguatge, això ens fa estirar els 
músculs mentals i ens fa un millor programador. Això sembla un tipus pobre de retorn de la
inversió, però si no esteu aprenent alguna cosa _genuïnament_ nova tot el temps, llavors vos 
estancareu i sereu com la persona que té deu anys d'experiència en fer la mateixa cosa una 
vegada i una altra.

## On brilla Rust

Rust és un llenguatge de programació de sistemes estàtic i fortament tipat. _Estàtic_ 
significa que tots els tipus els coneixen en temps de compilació, _fortament_ significa
que aquests tipus estan dissenyats per dificultar l'escriptura de programes incorrectes,
donant una garantia de correcció molt millor que amb un  llenguatge "cowboy" com C.
_Sistemes_ significa generar el millor codi màquina possible amb total control d'ús de 
la memòria. Per tant, els usos que pot tindre el llenguatge són molt durs: sistemes operatius, 
controladors de dispositius i sistemes encastats que potser ni tan sols tenen sistema 
operatiu. No obstant això, és en realitat, un llenguatge molt agradable per escriure codi 
d'aplicacins normals també.

La gran diferència amb C i C++ és que Rust és _segur per defecte_; Tots els accessos a 
memòria es comproven. No és possible corrompre la memòria per accident.

Els principis unificadors darrere de Rust són:

  - Fer complir estrictament la [_recollida de memòria brossa_](https://ca.wikipedia.org/wiki/Recollida_de_mem%C3%B2ria_brossa) de dades.
  - Funcions, mètodes i tancaments per operar sobre dades.
  - Tuples, estructures i enumeracionss per agregar dades.
  - Coincidència de patrons per seleccionar i desestructurar dades.
  - Trets per definir _comportaments_ a les dades.

Hi ha un ecosistema de ràpid creixement de llibreries disponibles a través de Cargo però
aquí ens concentrarem en els principis bàsics del llenguatge aprenent a utilitzar la 
llibreria estàndard. El meu consell és escriure _lots de petits programes_, per tant, 
aprendre a utilitzar ```rustc``` directament és una habilitat bàsica. A l'hora de fer els 
exemples d'aquest tutorial he definit un petit script anomenat ```rrun``` que fa una 
compilació i executa el resultat:

```
rustc $1.rs && ./$1
```

## Creant

Aquest tutorial assumeix que tens Rust instal·lat localment. Afortunadament això és 
[molt senzill](https://www.rust-lang.org/en-US/downloads.html) si no és el cas.

```
$ curl https://sh.rustup.rs -sSf | sh
$ rustup component add rust-docs
```
Recomanaria obtenir la versió estable per defecte; És fàcil de descarregar versions 
inestables després i intercanviar-nos entre elles.

Això ens fa obtenir el compilador, el paquet Cargo, la documentació de l'API i el Rust Book. 
El viatge de més de mil kilòmetres comença amb un pas, i aquest primer pas és indolor.

`rustup` és la comanda que heu d'usar per controlar la vostra instal·lació de Rust. Quan una 
nova versió estable apareix, només hem d'escriure 'rustup update' per actualitzar-la. S'obrirà
'Rustup Doc' la documentació sense connexió al nostre navegador.

Probablement ja tindreu un editor que vos agrade, i [suport bàsic per Rust](https://areweideyet.com/)
això és bo. Vos suggerisc que comenceu amb el ressaltat de sintaxi bàsica al principi, i que
treballeu a mesura que els vostres programes es facin més grans.

Personalment jo sóc un fan de [VSCodium]([https://vscodium.com/) que és un dels editors que 
ofereixen millor suport per Rust; és particularment senzill d'instal·lar a Linux a través del
gestor de paquets, i també funciona en altres plataformes.

La principal cosa a saber és com editar, compilar i executar programes Rust.
Aprendreu a programar amb els vostres _dits_; escribint el codi per vosaltres mateixa, i aprenent
i aprenent a reorganitzar les coses de manera més eficient amb el vostre editor.

Zed Shaw's [avisa](https://learnpythonthehardway.org/book/intro.html) sobre l'aprenentage
de progrmar en Python és bo, siga quina siga la llengua. Ell comenta que aprendre a programar
és com aprendre a tocar un instrument musical - el secret és la pràctica i la persitència.
També tenim bons consells del mestre Yoga i de les arts marcials com el tai-txi; sentiu la tensió,
però no us esforceu massa. Aquí no estarem exercitant un múscul tonto.

M'agradaria també agrair a Steve Donovan per la seva feina de resum i concreció en aquest bon manual
d'iniciació al Rust.

Steve Donovan © 2017-2018 MIT license version 0.4.0

