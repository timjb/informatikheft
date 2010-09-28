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

Objektdiagramm:

	#YUML dir:lr
	[stativ{bg:lightblue}]-erster>[knoten1],
	[stativ{bg:lightblue}]-letzter>[knoten3],
	[knoten1{bg:green}]-enthält>[element1],
	[knoten2{bg:green}]-enthält>[element2],
	[knoten3{bg:green}]-enthält>[element3],
	[knoten1{bg:green}]-nächster>[knoten2{bg:green}],
	[knoten2{bg:green}]-nächster>[knoten3{bg:green}],

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
		if(naechster == null) {
			naechster = new KNOTEN(e);
			// Anzahl der Knoten erhöhen
		} else {
			nachster.hintenEinfuegen(e);
		}
	}
