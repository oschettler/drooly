10. Beispiel für eine technologieinvariante Applikation:
Ein Bauelement—Extraktor

In diesem Abschnitt soll anhand eines Beispiels die Anwendung der
tabellarischen Technologiebeschreibung verdeutlicht werden. Bei der gewählten
Applikation handelt es sich um einen technologieinvarianten Bauelementextraktor
ähnlich dem Extraktor MONDRIAN, der im Rahmen einer Projektgruppe an der
Universität Dortmund entwickelt wurde [MONDRIAN).

MONDRIAN erhält seine Technologieinformationen aus einer Datei in der
Sprache DINGO—I. Die zu extrahierenden Objekte werden in einer speziell für diesen
Anwendungszweck entwickelten Abfragesprache SA OMON beschrieben. Das
SALOMON-Programm wird in einen Extraktionsbaum 1'bersetzt. Die Abarbeitung
dieses Baums initiiert die einzelnen Extraktionsschrit„ die zur Extraktion aller
Objekte einer Anfrage erforderlich sind. Die Techno . ‚beschreibung wird ebenfalls
in eine interne Darstellung. das Extraktions—Sché'm ‚’bersetzt, mit welchen für
jedes Technologieobjekt die Aufrufreihenfolge der Ex _.3: ionsalgorithmen festgelegt
wird. Die Abarbeitung eines Extraktions—Schemas rind -_:‚ er Aufruf der Algorithmen
ist Aufgabe des Technologieprozessors in MONDRIAN, des og. DINGO—Managers.








)

Eine Beispielapplikation 120



Zentrale Steuerungskomponente MONDRIANS ist jedoch der SALOMON—Manager.
Dieser veranlaßt zunächst das Einlesen des Layouts aus einer Datei hn GIF-Format
in die MONDRIAN—Datenstruktur. Anschließend wird der DINGO—Manager aufgerufen,
um legale Technologieobjekte in dieser Datenstruktur zu finden und zu markieren.
Der SALOMON-Mamager extrahiert dann daraus unter Abarbeitung des
Extraktionsbaums die geforderten Daten und liefert sie an den Ausgabeprozessor
zur Fonnaüerung und Ausgabe

Es soll plausibel gemacht werden, wie die Objekttabelle und die
Wertausdruckstabehe in die beschüeben Abläufe einer Apphkathn1 wie LHJNDRIAN
zu integrieren sind. In MONDRIAN ergäbe sich für die Extraktion eines

Bauelementes unter Verwendung der in dieser Arbeit entwickelten TabeUen
folgender Ablauﬁ

EINGABE: 1. Die Technologiespezifikation in Form von Syme,
Objekth und ExprTb
2. Ein Layout und die Möglichkeit, neue Pseudolayer
einzuführen
AUSGABE: Extrahierte Bauelemente in einem Pseudolayer

"hole Zeiger auf Objektbeschreibung in Objecth durch Zugriff
über den Symboltabellindex mithilfe von hmkup"
"setze im usw—Feld des Objekteintrages und aller seiner
Unterstrukturen (Generator m_PREFIRST, m_PRENEXT) eine
Markierung, daß diese Struktur noch nicht extrahiert ist"
"hole erste deklarierte Komponente mithilfe der
Generatoroperation m_GFIRSTCOMP"
whüe "Komponente nicht leer" gg
begin

if "Komponente ist PARAM"

then "FEHLER: bei Bauelementen müssen alle Komponentenklassen

eindeutig bestimmt sein"





(D
.—

se
e

|

U“
...

n
y’"Komponente noch nicht extrahiert (Markierung im usa—
Feld)"
then "extrahiere diese Komponente durch rekursiven Aufruf
dieses Algorithmus"
whﬂe "es gibt noch nicht extrahierte RELATIONS (über
m_NEXT_RELATION und die umn—Markierung)" gg
"extrahiere die äußerste zu dieser RELATION gehörige
RELATION (m_GOUTERMOST) und markiere alle davon
abhängigen RELATIONS als extrahiert"
gnd ( PART oder SET )
"hole mithilfe von m_NEXTCOMP nächste deklarierte
Komponente"
ggg ( alle deklarierten Komponenten ) .
"überprüfe alle PROPERTIES"
"überprüfe alle Design Rules, die sich au
beziehen"





keine Unterstruktur

Eine Beispielapplikation ' 121



In diesem Verfahren werden folgende Möglichkeiten beim Zugriff auf die
Tabellen besonders genutzt:

— Generatoren für

- alle deklarierten Komponenten

- alle RELATIONS auf einer gegebenen Komponente
- alle objektglobalen PROPERTIES

- alle Unterstrukturen

— Möglichkeit, an jedes Objekt eine benutzerdefinierte Informationen anzuhängen,
diese abzufragen und zu ändern (2.8. "ist bereits extrahiert", "Design Rule
verletzt")

All diese Zugriffsarten werden in den Tabellenmodulen durch entsprechende
Zugriffsoperationen ermöglicht. Besonders die Möglichkeit, beliebige eigene
Strukturen über das user—Feld in die Objekttabelle einzuhängen, vereinfacht die
Zuordnung applikationsspezifischer Informationen zu einzelnen Objekteinträgen.
