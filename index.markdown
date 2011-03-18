% title Informatik Q11 2010/2011
% author Tim Baumann

Verbesserungen bitte an [tim@timbaumann.info](mailto:tim@timbaumann.info).

<!--<p id="leading">Dies ist mein öffentliches Schulheft im Fach Informatik. Ich hoffe sie sind nützlich für meine Mitschüler und sonst für jeden, der das hier liest. Wie immer gilt: Wer einen Fehler findet, der kann ihn behalten. Lieber wär es mir jedoch, wenn ihr mich einfach in der Schule darauf anspricht. Danken möchte ich noch Frau Huber, meiner Informatiklehrerin für die Erlaubnis, im Unterricht unsere Notebooks benützen zu dürfen.</p>-->

<p class="date">27.9.2010</p>

# Listen

## Warteschlangen mit Feld

### Eigenschaften

* Länge begrenzt
* verwendet Feld (Array)
* [FIFO](http://de.wikipedia.org/wiki/First_In_%E2%80%93_First_Out) (first in – first out)
* Zwei Klassen, eine für die Inhalte, eine für Koordination notwendig

### Nachteile

* Länge des Feldes ist begrenzt, das bedeutet
  * entweder gibt es zu wenig Platz
  * oder Speicherplatz wird verschwendet.
* Beim Entfernen müssen alle Elemente aufgerückt werden ⇒ schlechte Performance

## Einfach verkettete Listen

Beispiel: [Polonaise](http://www.youtube.com/watch?v=-Mf3Jc13BpE) im Klassenzimmer. Alex, der letzer Mann ist, soll den ersten, Frank, bitten, langsamer zu gehen. Lösung: Alex wendet sich an seinen Vordermann und bittet diesen langsamer zu gehen, wenn dieser der erste ist, oder die ganze Nachricht wiederum an seinen Vordermann weiterzugeben.

Pseudo-Code (mit [Python](http://python.org/)-Syntax):

~~~
methode langsamer():
  wenn selbst.vordermann:
    selbst.vordermann.langsamer()
  ansonsten:
    selbst.geschwindigkeit--
~~~

Objektdiagramm (sich bitte runde Ecken denken!):

~~~
#yuml dir:lr
[stativ{bg:lightblue}]-erster>[knoten1]
[stativ{bg:lightblue}]-letzter>[knoten3]
[knoten1{bg:green}]-enthält>[element1]
[knoten2{bg:green}]-enthält>[element2]
[knoten3{bg:green}]-enthält>[element3]
[knoten1{bg:green}]-nächster>[knoten2{bg:green}]
[knoten2{bg:green}]-nächster>[knoten3{bg:green}]
~~~

Klassendiagramm:

~~~
#yuml dir:lr
[STATIV{bg:lightblue}]<>hat als erster-1>[KNOTEN{bg:green}]
[KNOTEN{bg:green}]<>nächster-0..1>[KNOTEN{bg:green}]
[KNOTEN{bg:green}]<>enthält->[ELEMENT]
~~~

~~~
#yuml dir:lr
[KNOTEN|KNOTEN naechster;ELEMENT inhalt]
[STATIV|KNOTEN erster;int anzahlKnoten|void hintenAnfuegen()]
~~~

<p class="date">28.9.2010</p>

Und das ganze in Java:

~~~
#highlight java
public void hintenEinfuegen(ELEMENT e) {
  if (naechster == null) {
    naechster = new KNOTEN(e);
    // Anzahl der Knoten erhoehen
  } else {
    nachster.hintenEinfuegen(e);
  }
}
~~~

<p class="date">4.10.2010</p>

Klasse STATIV:

~~~
#highlight java
public void vorneEinfuegen(ELEMENT e) {
  if (erster == null) {
    erster = new KNOTEN(e);
  } else {
    KNOTEN knotenNeu = new KNOTEN(e);
    knotenNeu.naechsterSetzen(knotenNeu);
    erster = knotenNeu;
  }
}
~~~

~~~
#highlight java
public KNOTEN vorneEntfernen() {
  if (erster == null) {
    return null;
  } else {
    KNOTEN weg = erster;
    weg.setzeNaechsten(null);
    erster = erster.gibNaechsten();
    return weg;
  }
}
~~~

~~~
#highlight java
public KNOTEN knotenGeben(int position) {
  KNOTEN aktuell = erster;
  for(int i = 0; i < position; i++) {
    aktuell = aktuell.gibNaechsten();
  }
  return aktuell;
}
~~~

~~~
#highlight java
public KNOTEN knotenLoeschen(int position) {
  if (position == 0) {
    return vorneEntfernen();
  } else {
    KNOTEN weg = knotenGeben(position);
    knotenGeben(position - 1).setzeNaechsten(weg.gibNaechsten);
    weg.setzeNaechsten(null);
    return weg;
  }
}
~~~

<p class="date">5.10.2010</p>

<div class="exercise">
Buch S. 21/6

Eckiges Objektdiagramm:

~~~
#yuml dir:lr
[hotelier0{bg:lightblue}]-erster>[knoten0{bg:green}]
[knoten0{bg:green}]-enthält>[person0]
[knoten0{bg:green}]-nächster>[knoten1{bg:green}]
[knoten1{bg:green}]-enthält>[person1]
[knoten1{bg:green}]-nächster>[knoten2{bg:green}]
[knoten2{bg:green}]-enthält>[person2]
[knoten2{bg:green}]-nächster>[knoten3{bg:green}]
[knoten3{bg:green}]-enthält>[person3]
~~~

~~~
#yuml dir:lr
[PERSON|int gewicht;String name|int gewichtGeben();String nameGeben()]
[KNOTEN|KNOTEN naechster;PERSON inhalt|int inhaltGewichtGeben();void inhaltSetzen(PERSON p);KNOTEN naechsterGeben()]
[HOTELIER|KNOTEN erster|int gewichtGeben();void ersterSetzen(KNOTEN k)]
~~~

Sequenzdiagramme:

~~~
#websequencediagram
(Benutzer)->stativ0: datenAusgeben()
activate stativ0
stativ0->knoten1: datenAusgeben()
activate knoten1
knoten1->element1: datenGeben()
activate element1
element1-->knoten1: "\"Apfel gruen\""
deactivate element1
knoten1-->stativ0: "\"Apfel gruen\""
deactivate knoten1
stativ0->stativ0: Ausgabe mit System.out.println
stativ0->knoten1: naechsterGeben()
activate knoten1
knoten1-->stativ0: knoten2
deactivate knoten1
stativ0->knoten2: datenAusgeben()
activate stativ0
activate knoten2
knoten2->element2: datenGeben()
activate element2
element2-->knoten2: "\"Banane gelb\""
deactivate element2
knoten2-->stativ0: "\"Banane gelb\""
deactivate knoten2
stativ0->stativ0: Ausgabe mit System.out.println
stativ0->knoten2: naechsterGeben()
activate knoten2
knoten2-->stativ0: null
deactivate knoten1
deactivate stativ0
stativ0-->(Benutzer): ""
~~~

Und in Java:

~~~
#highlight java
public void datenAusgeben() {
  KNOTEN k = this.erster;
  while(k != null) {
    System.out.println(k.datenAusgeben());
    k = k.naechsterGeben();
  }
}
~~~

</div>


## Rekursive Funktionen

### Beispiel: Die [Fibonacci-Folge](http://de.wikipedia.org/wiki/Fibonacci-Folge)

A(0) = 0  
A(1) = 1  
A(2) = 1 = 0 + 1  
A(3) = 2 = 1 + 1  
A(4) = 3 = 1 + 2  
A(5) = 5 = 2 + 3  
A(6) = 8 = 3 + 5  
fib(n) = fib(n-2) + fib(n-1)

In Worten: Die n-te Fibonacci-Zahl ist die Summe ihrer beiden Vorgänger. Die 0-te Fibonacci-Zahl ist 0, die 1-te 1.

### Definition

**Rekursive Funktionen** enthalten in ihrem Funktionsterm mindestens einmal den eigenen Funktionsbezeichner. Ein Funktionsaufruf endet nur dann nach einer endlichen Anzahl von Berechnungsschritten, wenn die Abbruchbedingung erfüllt ist.

~~~
fib(5) = fib(4)          + fib(3)
       = fib(3) + fib(2) + fib(2) + fib(1)
       …
       = 5
~~~

~~~
#highlight java
public int fib(int n) {
  if (n == 0 || n == 1) {
    return n;
  } else {
    return fib(n-1) + fib(n-2);
  }
}
~~~

<div class="exercise">
S. 24/3 a)

A(0) = 300  
A(1) = 300 + 300 * 0,2 = 360  
A(2) = 360 + 360 * 0,2 = 432

Allgemein:  
A(t) = A(t-1) + A(t-1) * 0,2 = A(t-1) * 1,2

Abbruchbedingung:  
A(0) = 300
</div>

<p class="date">18.10.2010</p>

<div class="exercise">
Buch S. 25/5 a)

A(3) = 9  
A(2) = 5  
A(1) = 1  

Allgemein:  
A(n) = A(n-1) + 4, n > 1  
Abbruchbedingung:  
A(1) = 1

oder: A(n) = 1 + 4(n-1)

b)

~~~
A(3) = A(2) + 4  
     = A(1) + 4 + 4
     = 1 + 4 + 4
     = 9
~~~

</div>

## Rekursive Methoden einer Liste

Rekursive Funktionen: im Funktionsterm steht mindestens ein Mal der eigene Funktionsbezeichner  
Rekursive Methoden: Innerhalb der Definition (Methodenrumpf); Aufruf der gleichnamigen Methode eines referenzierten Objekts  
Bereits bekannt: `hintenEinfuegen(ELEMENT e)`  
Rekursiver Aufbau in der Klasse KNOTEN:

~~~
#highlight java
public void hintenEinfuegen(ELEMENT e) {
  if (this.naechster != null) {
    this.naechster.hintenEinfuegen(e);
  } else {
    this.naechster = new KNOTEN(e);
  }
}
~~~

Aufgabe: Implementiere `anzahlKnotenGeben()`!

### Ohne Rekursion

STATIV:

~~~
#highlight java
public int anzahlKnotenGeben() {
  KNOTEN aktuell = this.naechster;
  int zaehler = 0;
  while (aktuell != null) {
    zaehler++; // Um eins hochzaehlen
    aktuell = aktuell.naechsterGeben();
  }
  return zaehler;
}
~~~

~~~
#websequencediagram
(Benutzer)->stativ0: anzahlKnotenGeben()
activate stativ0
stativ0->stativ0: Zähler erhöhen
stativ0->knoten1: naechsterGeben()
activate knoten1
knoten1-->stativ0: knoten2
deactivate knoten1
stativ0->stativ0: Zähler erhöhen
stativ0->knoten2: naechsterGeben()
activate knoten2
knoten2-->stativ0: knoten3
deactivate knoten2
stativ0->stativ0: Zähler erhöhen
stativ0->knoten3: naechsterGeben()
activate knoten3
knoten3-->stativ0: null
deactivate knoten3
stativ0-->(Benutzer): 3
deactivate stativ0
~~~

### Mit Rekursion

STATIV:

~~~
#highlight java
public int anzahlKnotenGeben() {
  if (this.erster == null) {
    return 0;
  }
  return this.erster.anzahlGeben();
}
~~~

KNOTEN:

~~~
#highlight java
public int anzahlGeben() {
  if (this.naechster == null) { // Abbruchbedingung
    return 1;
  }
  return this.naechster.anzahlGeben() + 1;
}
~~~

~~~
#websequencediagram
(Benutzer)->stativ0: anzahlKnotenGeben()
activate stativ0
stativ0->knoten1: anzahlGeben()
activate knoten1
knoten1->knoten2: anzahlGeben()
activate knoten2
knoten2->knoten3: anzahlGeben()
activate knoten3
knoten3-->knoten2: 1
deactivate knoten3
knoten2-->knoten1: 2
deactivate knoten2
knoten1-->stativ0: 3
deactivate knoten1
stativ0-->(Benutzer): 3
deactivate stativ0
~~~

Das bedeutet:

* Jeder Knoten muss überprüfen, ob er der letzte ist  
* Alle Knoten verhalten sich gleich **außer** der letzte

&rArr; Verwende eine kleine Kugel, die keinen Inhalt haben kann und hänge sie an den letzten Korb (Knoten).

Objektdiagramm:

~~~
#yuml
[stativ0]-erster>[knoten1]
[knoten1]-inhalt>[element1]
[knoten1]-naechster>[knoten2]
[knoten2]-inhalt>[element2]
[knoten2]-naechster>[knoten3]
[knoten3]-inhalt>[element3]
[knoten3]-naechster>[abschluss0]
~~~

**Vorteil**: Bei Methoden der Klasse `KNOTEN` keine Fallunterscheidung mehr notwendig.  
**Nachteil**: Zwei Klassen (`DATENKNOTEN` und `ABSCHLUSS`)

STATIV:

~~~
#highlight java
public int anzahlKnoten() {
  return this.erster.anzahlGeben();
}
~~~

DATENKNOTEN:

~~~
#highlight java
public int anzahlGeben() {
  return this.naechster.anzahlGeben() + 1;
}
~~~

ABSCHLUSS:

~~~
#highlight java
public int anzahlGeben() {
  return 0;
}
~~~

Klassendiagramm:

~~~
#yuml
[note: LISTENELEMENT + ABSCHLUSS + DATENKNOTEN: Kompositum{bg:cornsilk}]
[LISTE]<>erster-1>[LISTENELEMENT]
[LISTENELEMENT]^-[ABSCHLUSS]
[LISTENELEMENT]^-[DATENKNOTEN]
[DATENKNOTEN]<>naechster-1>[LISTENELEMENT]
[DATENKNOTEN]<>inhalt-1>[DATENELEMENT]
~~~

<p class="date">19.10.2010</p>

~~~
#highlight java
public abstract class LISTENELEMENT {
  public abstract int anzahlKnoten();
}
public class DATENKNOTEN extends LISTENELEMENT {
  ...
}
~~~

<p class="date">25.10.2010</p>

LISTE:

~~~
#highlight java
public DATENELEMENT inhaltLetzterGeben() {
  return this.erster.inhaltLetzterGeben(null);
}
~~~

DATENKNOTEN:

~~~
#highlight java
public DATENELEMENT inhaltLetzterGeben(DATENELEMENT e) {
  return this.naechster.inhaltLetzterGeben(this.inhalt);
}
~~~

ABSCHLUSS:

~~~
#highlight java
public DATENELEMENT inhaltLetzterGeben(DATENELEMENT e) {
  return e;
}
~~~

<p class="date">26.10.2010</p>

LISTE:

~~~
#highlight java
public void listendatenAusgeben() {
  System.out.println("In der Liste ist gespeichert: ");
  erster.listendatenAusgeben();
}
~~~

DATENKNOTEN:

~~~
#highlight java
public void listendatenAusgeben() {
  System.out.println(this.inhalt.datenGeben());
  this.naechster.listendatenAusgeben();
}
~~~

ABSCHLUSS:

~~~
#highlight java
public void listendatenAusgeben() {
  // Nichts zu tun ...
}
~~~

Weitere Methoden:

* `vorneEinfuegen()`
* `vorneEntfernen()`
* `hintenEntfernen()`
* `datenknotenGeben(String datenwert)`

<p class="date">8.11.2010</p>

### Die Methode `hintenEntnehmen` der einfach verketteten Liste:

* Inhalt des letzten Knoten wird für entnehmen benötigt
* Suche nach inhaltLetzter mit inhaltLetzterGeben
* Datenwert von inhaltLetzter kann der Methode entnehmen übergeben werden
* Rückgabewert von entnehmen(inhaltLetzter) liefert Inhalt des ehemals letzten Knoten

~~~
#highlight java
class Liste {
  // ...
  public Datenelement hintenEntnehmen() {
    Datenelement inhaltLetzter = erster.inhaltLetzterGeben(erster.inhaltGeben());
    return entnehmen(inhaltLetzter.datenwertGeben());
  }
  
  public Datenelement entnehmen(String datenwert) {
    // alten Knoteninhalt festhalten
    Datenelement alterKnoteninhalt = datenknotenGeben(datenwert).inhaltGeben();
    erster.entfernen(datenwert);
    return alterKnoteninhalt; // Knoteninhalt zurueckgeben
  }
  
  public Datenknoten datenknotenGeben(String datenwert) {
    return this.erster.datenknotenGeben(datenwert);
  }
  // ...
}
~~~

~~~
#highlight java
abstract class Listenelement {
  // ...
  public abstract Listenelement entfernen(String suchwert);
  public abstract Datenelement inhaltLetzterGeben(Datenelement aktInhalt);
  public abstract Datenknoten datenknotenGeben(String suchwert);
  // ...
}
~~~

~~~
#highlight java
public Datenknoten extends Listenelement {
  // ...
  public Listenelement entfernen(String suchwert) {
    // wenn datenwert von inhalt mit suchwert ueberein-
    // stimmt, soll der Knoten entfernt werden
    if (this.inhalt.datenwertGeben() == suchwert) {
      return this.naechster;
    } else {
      this.naechster = this.naechster.entfernen(suchwert);
      return this;
    }
  }
  
  public Datenelement inhaltLetzterGeben(Datenelement aktInhalt) {
    return this.naechster.inhaltLetzterGeben(this.inhalt);
  }
  
  public Datenknoten datenknotenGeben(String suchwert) {
    if (this.inhalt.datenwertGeben() == suchwert) {
      return this;
    } else {
      return this.naechster.datenwertGeben(suchwert);
    }
  }
  // ...
}
~~~

~~~
#highlight java
public Abschluss extends Listenelement {
  // ...
  public Listenelement entfernen(String suchwert) {
    return this;
  }
  
  public Datenelement inhaltLetzterGeben(Datenelement aktInhalt) {
    return aktInhalt;
  }
  
  public Datenknoten datenknotenGeben(String suchwert) {
    return null;
  }
  // ...
}
~~~

<p class="date">16.11.2010</p>

## Dynamische lineare Datenstrukturen

* Stapel (LIFO)
  * `vorneEinfuegen`
  * `vorneEntnehmen`
  * z.B. Kofferraum, Aktenstapel
* Warteschlange (FIFO)
  * `hintenEinfuegen`
  * `vorneEntnehmen`
  * z.B. Taxistand, Restaurantküche, Kasse

<div class="exercise">
S. 36 Nr. 6

Klassen:

* BETRIEBSFEST
* LKW
* KISTE
* STAPEL

~~~
#yuml
[BETRIEBSFEST]<>-hat 4>[STAPEL]
[BETRIEBSFEST]<>-hat 1>[LKW]
[STAPEL]<>-enthält 0..n>[KISTEN]
[LKW]<>-enthält 0..n>[KISTEN]
~~~

</div>

<p class="date">29.11.2010</p>

<div class="exercise">
S. 39 Nr. 1 a)

* LISTE: Container
* abstract LISTENELEMENT: Bauteil
* DATENKNOTEN: Schubladenbox
* ABSCHLUSS: Rollgestell
* abstract DATENELEMENT: Werkzeug
* DATENELEMENTUNTERKLASSEN: Hammer, Rohrzange, Bohrer

</div>

## Heterogene Listen

Die Grundidee von heterogenen Listen ist, das man ja manchmal Listen mit verschiedenen Objekten haben möchte. Darum definiert man `DATENELEMENT` abstrakt und erstellt dann Unterklassen davon.

<p class="date">6.12.2010</p>

## Sortierte Listen

Sortieren mögl. nach Zahl, Text, Datum

Wir wollen `sortiertEinfuegen`, `sortiertSuchen` (?), `sortiertEntfernen` (alle rekursiv)

In DATENKNOTEN:

~~~
#highlight java
public DATENKNOTEN sortiertEinfuegen(DATENELEMENT inhaltNeu) {
  if (inhalt.compare(inhaltNeu)) {
    DATENKNOTEN k = new DATENKNOTEN(inhaltNeu);
    k.naechsterSetzen(this);
    return k;
  } else {
    naechster = naechster.sortiertEinfuegen(inhaltNeu);
    return this;
  }
}
~~~

In ABSCHLUSS:

~~~
#highlight java
public DATENKNOTEN sortiertEinfuegen(DATENELEMENT inhaltNeu) {
  DATENKNOTEN k = new DATENKNOTEN(inhaltNeu);
  k.naechsterSetzen(this);
  return k;
}
~~~

In DATENELEMENT:

~~~
#highlight java
public abstract boolean istKleiner(DATENELEMENT e);
~~~

Neue Klasse ANZUG:

~~~
#highlight java
class ANZUG extends DATENELEMENT {
  public boolean istKleiner(DATENELEMENT e) {
    return groesse < ((ANZUG) e).groesse;
  }
}
~~~

<p class="date">7.12.2010</p>

ZEITSCHRIFT:

~~~
#highlight java
private String name;

public boolean istKleiner(DATENELEMENT vg) {
  return name.compareTo(((ZEITSCHRIFT) vg).name) == -1;
}
~~~

<p class="date">14.12.2010</p>

# Bäume

Bäume zählen wie die Listen zu den sehr häufig verwendeten Datenstrukturen in der Informatik. Wie Listen bestehen Sie aus Knoten. Allerdings hat ein Knoten nicht nur einen, sondern viele Nachfolger. So ergibt sich eine Struktur wie z.B. in Stammbäumen.

## Binäre (Weihnachts-) Bäume

![Binärer Weihnachtsbaum](assets/christmas-binary-tree.png)

(XKCD's [Tree](http://xkcd.com/835/) by Randall Munroe; **euch allen übrigens an dieser Stelle frohe Weihnachten und einen guten Rutsch!**)

In Binärbäumen hat jedes Objekt also höchstens zwei Nachfolger.

### Ausgeben eines Binärbaums

Wenn alle Knoten eines Binärsbaums ausgegeben werden sollen, so gibt es drei Möglichkeiten:

Preorder
  ~ Zuerst wird der Datenwert des Knotens ausgegeben, dann rekursiv der linke, dann der rechte Nachfolger.
Inorder
  ~ Zuerst wird rekursiv der linke Nachfolger, dann der Datenwert des Knotens, dann der rechte Nachfolger ausgegeben.
Postorder
  ~ Zuerst wird der linke und rechte Nachfolger, dann der Datenwert des Knotens ausgegeben.

#### Beispiel

~~~
#yuml dir:td
[8]-rechts>[13]
[8]-links>[3]
[3]-rechts>[2000]
[3]-links>[99]
[99]-rechts>[5]
[99]-links>[1]
[2000]-links>[2]
[13]-links>[42]
[42]-rechts>[7]
[42]-links>[4]
~~~

Preorder
  ~ 8, 3, 99, 1, 5, 2000, 2, 13, 42, 4, 7
Inorder
  ~ 1, 99, 5, 3, 2, 2000, 8, 4, 42, 7, 13
Postorder
  ~ 1, 5, 99, 2, 2000, 3, 4, 7, 42, 13, 8

<p class="date">20.12.2010</p>

Klassendiagramm für einen Binärbaum:

~~~
#yuml
[BINAERBAUM]<>wurzel-1>[BAUMELEMENT]
[DATENKNOTEN]->[BAUMELEMENT]
[ABSCHLUSS]->[BAUMELEMENT]
[DATENKNOTEN]<>-2>[BAUMELEMENT]
[DATENKNOTEN]<>inhalt-1>[DATENELEMENT]
~~~

Baumelement und Datenelement sind abstrakt. In unserem Fall (Aufgabe im Buch) erstellen wir folgende Unterklasse von Datenelement:

~~~
#yuml
[TAENZER|Farbe;Geschlecht;Bezeichner]
~~~

<p class="date">10.1.2010</p>

Preorder ausgeben:

Binaerbaum:

~~~
#highlight java
wurzel.datenAusgeben();
~~~

Datenknoten:

~~~
#highlight java
inhalt.datenAusgeben();
links.datenAusgeben();
rechts.datenAusgeben();
~~~

Abschluss: nichts machen

<p class="date">10.1.2010</p>

S. 62 oben

## Geordnete Binärbäume

S. 62 oben

nicht geordnet:

~~~
#yuml dir:td
[1]-links>[2]
[1]-rechts>[3]
[2]-links>[4]
[2]-rechts>[6]
[4]-links>[5]
[4]-rechts>[7]
[6]-links>[10]
[6]-rechts>[12]
[3]-links>[8]
[3]-rechts>[9]
[8]-links>[14]
[8]-rechts>[15]
[9]-links>[13]
[9]-rechts>[11]
~~~

Suche nach 13: maximal 15 Vergleiche.

geordnet:

~~~
#yuml dir:td
[8]-links>[4]
[4]-links>[2]
[2]-links>[1]
[2]-rechts>[3]
[4]-rechts>[6]
[6]-links>[5]
[6]-links>[7]
[8]-rechts>[12]
[12]-links>[10]
[10]-links>[9]
[10]-rechts>[11]
[12]-rechts>[14]
[14]-links>[13]
[14]-rechts>[15]
~~~

Suche: maximal 4 Vergleiche

<p class="date">24.1.2010</p>

~~~
funktion baumErzeugen(liste)
  wenn nicht liste.leer()
    linkeHaelfte, rechteHaelfte = liste.splitten()
    knotenNeu = linkeHaelfte.letzterEntnehmen()
    knotenNeu.naechsterLinks  = baumErzeugen(linkeHaelfte)
    knotenNeu.naechsterRechts = baumErzeugen(rechteHaelfte)
    return knotenNeu
  ende
  return neuer Abschluss
ende
~~~

<p class="date">7.2.2010</p>

## Aufbau und Erweiterung von Suchbäumen

### Beispiel

Ausgangssituation:

~~~
#yuml
[Melone]-links>[Erdbeere]
[Melone]-rechts>[Quitte]
[Erdbeere]-links>[Abschluss1]
[Erdbeere]-rechts>[Abschluss2]
[Quitte]-links>[Abschluss3]
[Quitte]-rechts>[Abschluss4]
~~~

Nun soll eine Pflaume eingefügt werden.

Schritt 1 (nur der Teil mit der Quitte):

~~~
#yuml
[Quitte]-links>[Abschluss3]
[Abschluss3]-.->[Pflaume]
[Pflaume]-links>[Abschluss3]
[Pflaume]-rechts>[Abschluss5]
~~~

Schritt 2:

~~~
#yuml
[Quitte]-links>[Pflaume]
[Pflaume]-links>[Abschluss3]
[Pflaume]-rechts>[Abschluss5]
[Quitte]-rechts>[Abschluss4]
~~~

<p class="date">15.3.2010</p>

Graphen
-------

Graphen können folgende Eigenschaften besitzen:

* gerichtet
* gewichtet
* zusammenhängend

Sie können die folgenden Formen einnehmen:

* Stern
* Ring
* vollständig

## Adjazenzmatrix

              Augsb. Bob. Inningen Königsb. Merching Mering Obo.
------------ ------- ---- -------- -------- -------- ------ ----
   Augsburg   -      -1   7        12       -1       -1     -1  
   Bobingen          -    5        5        -1       -1     6,5 
   Inningen               -        8,7      -1       -1     -1  
Königsbrunn                        -        10,5     9,1    4,5 
   Merching                                 -        3      -1  
     Mering                                          -      -1  
  Oberottm.                                                 -   

