# Informatik Q11 2010/2011

<!--<p id="leading">Dies ist mein öffentliches Schulheft im Fach Informatik. Ich hoffe sie sind nützlich für meine Mitschüler und sonst für jeden, der das hier liest. Wie immer gilt: Wer einen Fehler findet, der kann ihn behalten. Lieber wär es mir jedoch, wenn ihr mich einfach in der Schule darauf anspricht. Danken möchte ich noch Frau Huber, meiner Informatiklehrerin für die Erlaubnis, im Unterricht unsere Notebooks benützen zu dürfen.</p>-->

<p class="date">27.9.2010</p>

## Listen

### Einfach verkettete Listen

#### Warteschlange

##### Eigenschaften

* Länge begrenzt
* verwendet Feld (Array)
* [FIFO](http://de.wikipedia.org/wiki/First_In_%E2%80%93_First_Out) (first in – first out)
* Zwei Klassen, eine für die Inhalte, eine für Koordination notwendig

##### Nachteile

* Länge des Feldes ist begrenzt, das bedeutet
  * entweder gibt es zu wenig Platz
  * oder Speicherplatz wird verschwendet.
* Beim Entfernen müssen alle Elemente aufgerückt werden ⇒ schlechte Performance

#### [Polonaise](http://www.youtube.com/watch?v=-Mf3Jc13BpE)

Beispiel: Polonese im Klassenzimmer. Alex, der letzer Mann ist, soll den ersten, Frank, bitten, langsamer zu gehen. Lösung: Alex wendet sich an seinen Vordermann und bittet diesen langsamer zu gehen, wenn dieser der erste ist, oder die ganze Nachricht wiederum an seinen Vordermann weiterzugeben.

Pseudo-Code (mit [Python](http://python.org/)-Syntax):

	methode langsamer():
	    wenn selbst.vordermann:
	        selbst.vordermann.langsamer()
	    ansonsten:
	        selbst.geschwindigkeit--

Objektdiagramm (sich bitte runde Ecken denken!):

	#YUML dir:lr
	[stativ{bg:lightblue}]-erster>[knoten1],
	[stativ{bg:lightblue}]-letzter>[knoten3],
	[knoten1{bg:green}]-enthält>[element1],
	[knoten2{bg:green}]-enthält>[element2],
	[knoten3{bg:green}]-enthält>[element3],
	[knoten1{bg:green}]-nächster>[knoten2{bg:green}],
	[knoten2{bg:green}]-nächster>[knoten3{bg:green}]

Klassendiagramm:

	#yuml dir:lr
	[STATIV{bg:lightblue}]<>hat als erster-1>[KNOTEN{bg:green}],
	[KNOTEN{bg:green}]<>nächster-0..1>[KNOTEN{bg:green}],
	[KNOTEN{bg:green}]<>enthält->[ELEMENT],
^
	#yuml dir:lr
	[KNOTEN|KNOTEN naechster;ELEMENT inhalt],
	[STATIV|KNOTEN erster;int anzahlKnoten|void hintenAnfuegen()],

<p class="date">28.9.2010</p>

Und das ganze in Java:

	#highlight java
	public void hintenEinfuegen(ELEMENT e) {
		if (naechster == null) {
			naechster = new KNOTEN(e);
			// Anzahl der Knoten erhoehen
		} else {
			nachster.hintenEinfuegen(e);
		}
	}

<p class="date">4.10.2010</p>

Klasse STATIV:

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
^
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
^
	#highlight java
	public KNOTEN knotenGeben(int position) {
		KNOTEN aktuell = erster;
		for(int i = 0; i < position; i++) {
			aktuell = aktuell.gibNaechsten();
		}
		return aktuell;
	}
^
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

<p class="date">5.10.2010</p>

<div class="exercise">
Buch S. 21/6

Eckiges Objektdiagramm:

	#yuml dir:lr
	[hotelier0{bg:lightblue}]-erster>[knoten0{bg:green}],
	[knoten0{bg:green}]-enthält>[person0],
	[knoten0{bg:green}]-nächster>[knoten1{bg:green}],
	[knoten1{bg:green}]-enthält>[person1],
	[knoten1{bg:green}]-nächster>[knoten2{bg:green}],
	[knoten2{bg:green}]-enthält>[person2],
	[knoten2{bg:green}]-nächster>[knoten3{bg:green}],
	[knoten3{bg:green}]-enthält>[person3]
^
	#yuml dir:lr
	[PERSON|int gewicht;String name|int gewichtGeben();String nameGeben()],
	[KNOTEN|KNOTEN naechster;PERSON inhalt|int inhaltGewichtGeben();void inhaltSetzen(PERSON p);KNOTEN naechsterGeben()],
	[HOTELIER|KNOTEN erster|int gewichtGeben();void ersterSetzen(KNOTEN k)]

<p class="date">11.10.2010</p>

Sequenzdiagramme:

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

Und in Java:

	#highlight java
	public void datenAusgeben() {
		KNOTEN k = this.erster;
		while(k != null) {
			System.out.println(k.datenAusgeben());
			k = k.naechsterGeben();
		}
	}

</div>


#### Rekursive Funktionen

##### Die [Fibonacci-Folge](http://de.wikipedia.org/wiki/Fibonacci-Folge)

A(0) = 0  
A(1) = 1  
A(2) = 1 = 0 + 1  
A(3) = 2 = 1 + 1  
A(4) = 3 = 1 + 2  
A(5) = 5 = 2 + 3  
A(6) = 8 = 3 + 5  
fib(n) = fib(n-2) + fib(n-1)

In Worten: Die n-te Fibonacci-Zahl ist die Summe ihrer beiden Vorgänger. Die 0-te Fibonacci-Zahl ist 0, die 1-te 0.

##### Rekursive Funktionen

Definition: **Rekursive Funktionen** enthalten in ihrem Funktionsterm mindestens einmal den eigenen Funktionsbezeichner. Ein Funktionsaufruf endet nur dann nach einer endlichen Anzahl von Berechnungsschritten, wenn die Abbruchbedingung erfüllt ist.

	fib(5) = fib(4)          + fib(3)
	       = fib(3) + fib(2) + fib(2) + fib(1)
	       …
	       = 5
^
	#highlight java
	public int fib(int n) {
		if (n == 0 || n == 1) {
			return n;
		} else {
			return fib(n-1) + fib(n-2);
		}
	}

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

<p class="date">11.10.2010</p>

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

	A(3) = A(2) + 4  
	     = A(1) + 4 + 4
	     = 1 + 4 + 4
	     = 9
</div>

### Rekursive Methoden einer Liste (soll Nummer 3 sein)

Rekursive Funktionen: im Funktionsterm steht mindestens ein Mal der eigene Funktionsbezeichner
Rekursive Methoden: Innerhalb der Definition (Methodenrumpf); Aufruf der gleichnamigen Methode eines referenzierten Objekts
Bereits bekannt: `hintenEinfuegen(ELEMENT e)`
Rekursiver Aufbau in der Klasse KNOTEN:

	#highlight java
	public void hintenEinfuegen(ELEMENT e) {
		if (this.naechster != null) {
			this.naechster.hintenEinfuegen(e);
		} else {
			this.naechster = new KNOTEN(e);
		}
	}

anzahlKnotenGeben()
1. ohne Rekursion
   STATIV:
	public int anzahlKnotenGeben() {
		KNOTEN aktuell = this.naechster;
		int zaehler = 0;
		while (aktuell != null) {
			zaehler++; // Um eins hochzaehlen
			aktuell = aktuell.naechsterGeben();
		}
		return zaehler;
	}
2. mit Rekursion
   STATIV:
	public int anzahlKnotenGeben() {
		if (this.erster == null) {
			return 0;
		}
		return this.erster.anzahlGeben();
	}
   KNOTEN:
	public int anzahlGeben() {
		if (this.naechster == null) { // Abbruchbedingung
			return 1;
		}
		return this.naechster.anzahlGeben() + 1;
	}

Nicht rekursiv:

	#websequencediagram
	(Benutzer)->stativ0: anzahlKnotenGeben()
	activate stativ0
	stativ0->knoten1: naechsterGeben()
	activate knoten1
	knoten1-->stativ0: knoten2
	deactivate knoten1
	stativ0->stativ0: Zähler erhöhen
	deactivate stativ0
	stativ0-->(Benutzer): 3

Rekursiv:

	#websequencediagram
	(Benutzer)->stativ0: anzahlKnotenGeben()
	activate stativ0
	stativ0->knoten1: naechsterGeben()
	activate knoten1
	knoten1-->stativ0: knoten2
	deactivate knoten1
	deactivate stativ0
	stativ0-->(Benutzer): 3

&rarr; Jeder Knoten muss überprüfen, ob er der letzte ist  
&rarr; Alle Knoten verhalten sich gleich **außer** der letzte

&rArr; Verwende eine kleine Kugel, die keinen Inhalt haben kann und hänge sie an den letzten Korb (Knoten).

Sequenzdiagramm:

	#yuml
	[stativ0]-erster>knoten1
	knoten1-inhalt>element1
	knoten1-naechster>knoten2
	…
	knoten2-naechster>abschluss0

Vorteil: Bei Methoden der Klasse KNOTEN keine Fallunterscheidung mehr notwendig.  
Nachteil: Zwei Klassen (DATENKNOTEN und ABSCHLUSS)

STATIV:

	#highlight java
	public int anzahlKnoten() {
		return this.erster.anzahlGeben();
	}

DATENKNOTEN:

	#highlight java
	public int anzahlGeben() {
		return this.naechster.anzahlGeben() + 1;
	}

ABSCHLUSS:

	#highlight java
	public int anzahlGeben() {
		return 0;
	}

Klassendiagramm:

LISTE-erster>
LISTENELEMENT
ABSCHLUSS und DATENKNOTEN vererben von LISTENELEMENT (Beziehung: "ist ein")
ABSCHLUSS
DATENKNOTEN<>inhalt-1>DATENELEMENT
DATENKNOTEN<>naechster-1>LISTENELEMENT
DATENELEMENT

LISTENEL, ABSCHL, und DATENKNOTEN gruppieren, Bezeichnung KOMPOSITUM

<p class="date">19.10.2010</p>

	#highlight java
	public abstract class LISTENELEMENT {
		public abstract int anzahlKnoten();
	}
	public class DATENKNOTEN extends LISTENELEMENT {
		...
	}
