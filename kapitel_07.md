# 7. Die Tabellen

Das Compiler-Backend erzeugt aus einer Technologiebeschreibung in der
DROOLY—Zwischenspraehe zwei Tabellen, die Objekttabelle und die
Wertausdruckstabelie. Diese Tabellen sind zusammen mit der vom Compiler-Frontend
erzeugten Symboltabelle Bestandteil der Technologie—Datenhaltung des IMAGE-
Systems. Über eine Funktionsbibliothek erlauben sie Applikationen den Zugriff auf
alle Technologieinformationen, die zur technologieinvarianten Bearbeitung der
ebenfalls gespeicherten Geomtrie— und Semantikdaten erforderlich sind. Die
Aufspaltung in drei Tabellen begründet sich in der unterschiedlichen Struktur der
gespeicherten Daten und in deren unterschiedlichen Einsatzbereichen innerhalb der
Layoutapplikationen. Manche Applikationen, wie etwa Bauelementextraktoren,
werden in der Hauptsache die Informationen der Objekttabelle verwenden, um
semantische Einheiten im Layout zu erkennen. Erst bei der Extraktion operationaler
Parameter muß auch auf Informationen der Wertausdruckstabelle zugegriffen werden,

da diese Parameter im wesentlichen durch quantitative Abmessungen bestimmt
werden.

Ein Layoutkompaktor wird dagegen zwar aufgrund der objektorientierten
Speicherung der Technologiedaten auf die Hierarchie der deklarierten Unter-
strukturen Bezug nehmen (in der Objekttabelle abgelegt), sich aber auf
quantitative PROPERTIES und RELATIONS konzentrieren, um seine Aufgabe zu
erfüllen. Die zur Verfügung gestellten Zugriffsoperationen erlauben eine solche
Auswahl relevanter Daten aus den Tabellen.

Wesentliches Prinzip beim Aufbau der Tabellen ist die Lokalität. Daten, die
sich ausschließlich auf ein Objekt beziehen, werden auch lokal bei diesem Objekt
und dort wiederum möglichst tief in der Komponentenhierarchie gespeichert. In
DINGO-II ist es durchaus möglich, innerhalb einer Objektdefinition
Topologieanweisungen oder Design Rules anzugeben, deren Operanden
Unterstrukturen ein und derselben Unterstruktur des Objektes sind. Beim Aufbau
der Tabellen wird eine solche Regel dem Objekt zugeordnet, dessen Bezeichner sich
als längster gemeinsamer Pfadpräfix der beiden Operanden ergibt. Dieses Prinzip
erlaubt einerseits die Trennung relevanter von nicht relevanten Daten für eine
bestimmte Objektdefinition. Sie erlaubt andererseits den gezielten Zugriff auf alle
ein Objekt betreffenden Daten, ohne andere Objektdefinitionen nach verstreuten
weiteren Informationen durchsuchen zu müssen.

# 7.1 Objekttabelle

Die Objekttabelle ist als Vektor von Objektstrukturen organisiert. Jedem
Objekt ist in eindeutiger Index in diesem Vektor zugeordnet. Jede Objektstruktur
bietet Platz für eine Anzahl unterschiedlicher Objekttypen, die im Folgenden

Die Tabellen 48



einzeln mit ihren Datenfeldern vorgestellt werden. Zunächst gibt es in einer
Objektstruktur einige Felder, die bei allen Objekttypen gleich genutzt werden:

prefix (ö): Verweis auf das Aggregat, von dem das aktuelle Objekt eine
Unterstruktur darstellt

suffix: Symboltabellenindex dieser Unterstruktur

Diese beiden Felder dienen zur Speicherung des qualifizerten Bezeichners eines
Objektes. Es wird also nicht bei jedem Objekt der vollständige Pfad abgelegt,
sondern nur der Symboltabellenindex der letzten Pfadkomponente und ein Verweis
auf das den statischen Kontext bildende Objekt. Der vollständige Pfad kann durch
Verfolgung der prefix—Verweise zurückgewonnen werden. Die Objekttabelle
übernimmt durch die Aufnahme dieser beiden Felder eine Funktion, die in Compilern
für konventionelle Sprachen eigentlich der Symboltabelle zukommt, nämlich die
Verwaltung des statischen Kontextes eines Bezeichners. Die Informationen zur
Hierarchie der Objekte ist jedoch ein wesentlicher Aspekt einer
Technologiebeschreibung in DINGO-II. Durch die Verwaltung der Objekthierarchie
innerhalb der Objekttabelle wird die Bearbeitung der Vererbungsmechanismen ohne
umfangreiche Annahmen über die Struktur der Symboltabelle möglich.

left, right: Zum schnellen Zugriff auf Objekte über ihren qualifizierten Bezeichner
wird über diese beiden Felder ein Binärbaum über die Objekttabelle gelegt. Ein
INORDER—Durchlauf diesen Binärbaums liefert alle deklarierten Objekte und
RELATIONS (welche für diesen Zweck ebenfalls einen Symboltabelleneintrag mit
zugeordnetem Index erhalten). sortiert nach Präfix und Suffix ihres
qualifizierten Bezeichners.

user: Dieses Feld hat keine festgelegte Semantik. Es ist als typloser Zeiger
vereinbart und kann durch entsprechende GAST—Operationen auf
benutzerdefinerte Datenstrukturen verweisen. Eine Objektstruktur erhält
dadurch zusätzliche Flexibilität und kann von einer Applikation ihren
Anforderungen gemäß erweitert werden.

spec: Dieses Feld unterscheidet die einzelnen Objekttypen, die in einer
Objektstruktur gespeichert werden können.

Alle weiteren Felder der Objektstruktur können in einer Variante
zusammengefaßt werden. Jedem Objekttyp entspricht dann eine Komponente dieser
Variante, wobei der Objekttyp durch das spec-Feld bestimmt ist. Die möglichen
Objekttypen sind LAYERDEF, CLASSDEF, STDDEF, PART, PARAM, SET, SHAPE,
PROPERTY, C_PROPERTY, RELATION und C_RELATION. Wie man aber an der
folgenden Beschreibung der für diese Objekttypen spezifischen Felder der
Objektstruktur erkennt, besteht auch zwischen den einzelnen Objekttypen oft eine
große Ähnlichkeit bei der Verwendung der einzelnen Felder. In der Implementierung
wird daher die Objektstruktur auch für die typspezifischen Felder nicht als
Variante realisiert, sondern alle Felder bilden eine große Verbundstruktur. In der
folgenden Beschreibung der Besonderheiten der einzelnen Objekttypen und ihrer



(6) in den folgenden Kapiteln wird auf die verschiedenen Aspekte der lmplementierung eingegangen. Dabei werden bestimmte Fonts für folgende textobjekte benutzt:

Schlüsselworte —- fett, unterstrichen
Variablen— und Feldbezeichner — fett
Funktionsbezeichner — kursiv
Nonterminalsymbole - kursiv

Nachrichten-Selektoren — normal

Die Tabellen 49



spezifischen Felder wird allerdings nur auf die jeweils tatsächlich benutzten Felder
(in den Graphiken jeweils unterlegt dargestellt) eingegangen. Alle unbenutzten
Felder behalten ihren Initialisierungswert (NIL für Verweise in die Objekt— und
Wertausdruckstabelie. NULL für Zeiger, 0 für Zähler, UNUSED für Operatoren).

U'.l'"

com next
next nme
next oulorrnoel
c 1

cn:
clnu cm
‘ nperme
rmorgee
chi

next rel2
um!

| ::
outermoet



Abb. 7.1: Objektstruktur bei LAYERDEF

Obj ekttyp LAYERDEF

keine speziellen Felder

user-
f‘lpeC- '
- ' next
next um.

- next outermoel. .
€

cn!
eperme

cleee_cnt

merge.
ent cm

next | next rel2
um:

|
outermoet



Abb. 7.2: Objektstruktur bei CLASSDEF

Die Tabellen 50



Objekttyp CLASSDEF

complid: Liste der Komponenten. Dieses Feld enthält einen Verweis auf die erste
Komponente. Alle weiteren Komponenten sind über ihr next_comp-Feld zu einer
linearen Liste verkettet. Mit Hilfe dieser beiden Felder wird die
Komponentenhierarchie auf einen Binärbaum abgebildet. Die "linker—Sohn"-
Beziehung bindet die Unterstrukturen an ihre Aggregat, die "rechter—Sohn"-
Beziehung verbindet Unterstrukturen auf gleichem Level miteinander. '

degree: Anzahl der deklarierten Unterstrukturen.

fparm_ix: Liste der parametertypdeklarierten Unterstrukturen. Die Reihenfolge der
Unterstrukturen in dieser Liste entspricht ihrer Reihenfolge in der Liste
formaler Parameter des Objektes.

next_outermost: Dieses Feld verweist auf den Kopf einer Liste von RELATIONS.
Diese RELATIONs sind allesamt Komponenten dieses Objektes und bilden jeweils

die Wurzeln des Raums einer zusammengesetzten oder quantifizierten
RELATION.

properties: Vektor von Verweisen auf die möglichen PROPERTIES. Jedem PROPERTY-
Typ entspricht ein Vektorelement (SHAPE, WIDTH, AREA, DIMENSION, RADIUS‚
LENGTH, IN_ANGLE)

c_properties: Vektor von Verweisen auf die möglichen C_PROPERTIES. Auch hier
entspricht jedem C_PROPERTY—Typ ein Vektor—Element (SHAPE, ..., IN_ANGLE,
EDGE_LENGTH. VOLTAGE, CURRENT, POWER, RESISTANCE, CAPACITANCE)

ueer
' ep.c:
next
next nme

ent
Ipﬂﬂl'l.

7 .cleee_cnt ; '

rmergee
cut rmelqe cn!

next tel!

0 !
oulewmoel



Abb. 7.3: Objektstruktur bei STDDEF

Die Tabellen 51



Objekttyp STDDEF

classes: Dieses Feld enthält einen Zeiger auf eine spezielle Struktur zur
Speicherung der Klasse und evtl. aktueller Parameter. Die einzelnen Felder,
dieser Struktur sind:

class: Verweis auf die Klasse
aparm_cnt: Anzahl der aktuellen Parameter
aparms: Vektor der aktuellen Parameter. Dieser Vektor speichert die im

DROOLY-Format gegebenen Indizes für aktuelle Parameter.

class_cnt: Anzahl der Klassen (immer gleich 1 bei STDDEF)
compHd: (wie bei CLASSDEF)

degree: Anzahl der deklarierten Unterstrukturen.
next_outermostz (wie bei CLASSDEF)

properties: (wie bei CLASSDEF)

c_properties: (wie bei CLASSDEF)

‘ Ill.!
. .
' next
"OKI nme

next outermoet
c

cm
wenn

cleee_citt

morgen
am . ent

next | next r002
uent

1 o
outermoet



Abb. 7.4: Objektstruktur bei PART

Objekttyp PART

classes: (wie bei STDDEF)
class_cnt: Anzahl der Klassen (immer gleich 1 bei PART)

complid: Liste der Komponenten. Dieses Feld enthält einen Verweis auf die erste
Komponente. Alle weiteren Komponenten sind über ihr next_comp—Feld zu einer
linearen Liste verkettet. Ein PART kann auf zwei Arten zu Komponenten
kommen. Bei der ersten Möglichkeit erbt es sie von seiner Klasse. Der
Unterstrukturbaum der Klasse wird dazu kopiert (zum Verfahren der Vererbung
siehe Abschnitt 8.6) und durch Austausch der Wurzel an das PART angehängt.
Da auch RELATIONS und PROPERTIES als Komponenten eines Objektes aufgefaßt
werden, werden auch FORWARDS oder RELATIONS mit; nur einer Unterstruktur
als Operand dieser Unterstruktur als neue Komponenten hinzugefügt. Diese
Vorgehensweise entspricht dem Prinzip der Lokalität.

Die Tabellen 52



next_compz Dieses Feld dient der genannten Verkettung der Unterstrukturen eines
Objektes zu einer linearen Liste.

degree: Anzahl der aus der Klasse geerbten deklarierten Unterstrukturen

properties: (wie bei CLASSDEF)
c _properties: (wie bei CLASSDEF)

lmerges, merges: Diese Felder enthalten Listen auf alle linksseitigen bzw.
rechtsseitigen MERGES dieses PARTS. Diese entstehen aus DINGO—II
Identifikationsanweisungen. Die Reihenfolge der Verkettung enspricht der
Reihenfolge in der DINGO-II Identifikationsanweisung. Das PART muß allerdings
nicht explizit in einer Identifikationsanweisung auftreten. Durch Identifikation
zweier Objekte werden automatisch alle ihre Unterstrukturen identifiziert. Ist
eine solche Identifikation aufgrund unterschiedlicher Struktur nicht möglich,
wird die Identifikation zurückgewiesen (zum Verfahren der Identifikation siehe
Abschnitt 8.8).

lmerge_cnt, merge_cntz Diese Felder zählen die rechtseitigen bzw. linkseitigen
merges.

relationlid: Dieses Feld enthält eine Liste von RELATIONS mit diesem PART als
Operanden. Diese RELATIONS können, müssen aber nicht Komponenten des
PARTS sein.






U“? »

next
fl.“ eeme




"OKI outermoet



‘ >. „ep-me






. = metaee
Cl"








next rel2



uent



!




outermoet

Abb. 7.5: Objektstruktur bei PARAM

Die Tabellen 53



Objekttyp PARAM

classes: Dieses Feld enthält einen Vektor von speziellen Strukturen zur
Speicherung der alternativen Klassen und evtl. aktueller Parameter. Eine
einzelne Elementstruktur dieses Vektors hat den bei STDDEF beschriebenen
Aufbau.

class_cnt: Anzahl der alternativen Klassen. Bei parameterdeklarierten
Unterstrukturen ist die Klasse nicht eindeutig bestimmt, sondern wird durch
eine Liste alternativer Klassen spezifiziert. Die einzelnen Alternativen werden
im classes—Vektor gespeichert; class_cnt enthält die Anzahl der Elemente in
diesem Vektor.

complld: Da bei einem PARAM die Klasse nicht eindeutig festliegt, kann der
Unterstrukturbaum nicht von einer Klasse an das PARAM vererbt werden. Bei
den Unterstrukturen eines PARAMS handelt es sich daher ausschließlich um
PROPERTIES und RELATIONS mit nur diesem PARAM als Operand.

next_comp: (wie bei PART)

degree: Anzahl der deklarierten Unterstrukturen (immer gleich 0 bei PARAM)

properties: (wie bei CLASSDEF)
c _properties: (wie bei CLASSDEF)

fparm_ixz Verkettung der parametertypdeklarierten Unterstrukturen eines Objektes.
Die Position des PARAMS in dieser Liste entspricht seiner Position in der Liste
formaler Parameter des Objektes.

relationlid: Dieses Feld enthält eine Liste mit RELATIONS mit diesem PARAM als
Operanden. Diese RELATIONS können, müssen aber nicht Komponenten des
PARAMS sein.

ueer
epec
com next
fl.“ Ilm.

next outeunoet
c 1

ent
epurme

cleu_cnt

rmergee
ent ent

next 1 next vel2
unnt

1 o 2
°Ul.fm°ll



Abb. 7.6: Objektstruktur bei SET

Die Tabellen 54



Objekttyp SET

classes: (wie bei STDDEF)

class_cnt: Anzahl der Klassen (immer gleich 1 bei SET)
compHd, next_compz (wie bei PART)

degree: (wie bei PART)

properties: (wie bei CLASSDEF)

c _properties: (wie bei CLASSDEF)

lmerges, merges: (wie bei PART)

lmerge_cnt, merge_cntz (wie bei PART)

expr: Dieses Feld enthält einen Index der Wertausdruckstabelle. In dem
zugehörigen Wertausdruck wird die Vorgabe der Kardinalität gespeichert. Wurde
keine solche Vorgabe gemacht, enthält dieses Feld den Index NIL.

relationild: Dieses Feld enthält eine Liste von RELATIONS mit diesem SET als
Operanden. Diese RELATIONS können, müssen aber nicht Komponenten des SETs
sein.

ueer
CP.C
com ﬂ.“
"OX! nme

next outermoet

c !

cleee_cnt

nnergee
ent

next rot:

0 \
outermoet



Abb. 7. 7a: Objektstruktur bei GEOCLASS

Objekttyp GEOCLASS

Der spezielle Objekttyp GEOCLASS benötigt keine zusätzlichen Felder. Er wurde
auch nur zur Vereinheitlichung der Strukturen in der Wertausdruckstabelle
eingeführt. SHAPES verweisen nämlich mit ihrem expr-Feld in die
Wertausdruckstabelle. Dort wiirde außer für diesen Spezialfall kein Feld vom Typ
"Symboltabellen-Index" benötigt. Ein Eintrag in der Objekttabelle kann jedoch mit
seinem suffix-Feld auf die Symboltabelle verweisen. Da Geometrieklassen in jedem
Fall global definiert werden, ist das prefix—Feld einer GEOCLASS immer NIL.

Die Tabellen 55



next outermoet

: 1

Cl"

cleee_ent
epume

lmergee
ent
!
next 1 next rel2

uent
‘ 2
outermoet



Abb. 7. 7b: Objektstruktur bei SHAPE

Objekttyp SHAPE

expr: Dieses Feld enthält einen Index der Wertausdruckstabelle. Der dort
gespeicherte Wertausdruck besteht im einfachsten Fall aus einem Verweis
zurück in die Objekttabelle auf den Eintrag für die Geometrieklasse. Es ist
aber auch ein durch @ verknüpfter boolescher Ausdruck mit alternativen
Geometrieklassen möglich. In der Wertausdruckstabelle können solche
booleschen Ausdrücke gebildet werden.

next_comp: (wie bei PART)

UIQ!

com next
next nme

next outermoet

: t

cleee_cnt

rmerqee
ent rmerge ent

ft.“
um

0
° utermoet



Abb. 7.8: Objektstruktur bei PROPERTY

Die Tabellen 56



Ob] ekttyp PROPERTY

expr: Dieses Feld enthält einen Index der Wertausdruckstabelle. Dort werden die
quantitativen Angaben zu einem PROPERTY gespeichert.

next_comp: (wie bei PART)

next_same: Mit diesem Feld werden C_PROPERTY- und C_RELATION—Objekte an das
zugehörige PROPERTY— bzw. RELATION-Objekt angehängt.

Objekttyp C_PROPERTY

expr: Dieses Feld enthält einen Index der Wertausdruckstabelle. Dort werden die
quantitativen Angaben zu einem C_PROPERTY gespeichert.

next_same: (wie bei PROPERTY)

Ein C_PROPERTY ist das Übersetzungsprodukt einer Bedingung in DROOLY, die
eine PROPERTY eines Objektes abfragt. C_PROPERTIES werden nicht in den

Komponentenbaum eines Objektes eingehängt, sondern sind nur über ihren
Wertausdruck zu erreichen.

II"!“
“CX!

lx
/ next eutermoet -__;.

c !

cteee_cnt

rmerge

next rel2.

uent

‘.
OUKOI'I’I'IO'C



Abb. 7.9: Objektstruktur bei RELATION

Die Tabellen 57

Objekttyp RELATION

quant: Dieses Feld speichert den Operator der RELATION. Als solche sind die

Quantoren a_ll. some, any und one zugelassen ebenso wie die DROOLY—
Operatoren indist, cross, overlap, touch und dist.

opl, op2: Diese Felder enthalten Verweise auf die beiden Operanden der
RELATION. Die entsprechenden Objekte können vom Typ PART, PARAM, SET
oder RELATION sein. In externen Design Rules können auch Objekte als
Operanden auftreten, die nicht als Unterstruktur des aktuellen statischen
Kontextes deklariert sind. In diesem Fall werden bei der Übersetzung in die
Tabellen neue Unterstrukturen erzeugt und diese als Operanden der
betreffenden RELATION eingetragen. Auf diese Weise lassen sich auch Klassen

mit aktuellen Parametern in den normalen Rahmen von Unterstrukturen
einfügen.

expr: Dieses Feld enthält den Verweis auf einen Wertausdruck für den
quantitativen Anteil einer RELATION.

outermost: RELATIONS werden in der Objekttabelle immer als Tripel aus Operator
und zwei Operanden abgelegt. Jedes solche Tripel erhält einen eigenen,
automatisch generierten Bezeichner und wird in die Komponentenstruktur des
lokalsten Objektes eingehängt (Lokalitätsprinzip). Um komplexe Ausdrücke
speichern zu können, ist als Operand ein Objekt vom Typ RELATION erlaubt.
Durch die so erzeugte Verweisstruktur wird in der Objekttabelle der
Syntaxbaum des komplexen Topologieausdruckes aufgebaut. Von jedem Knoten
vom Typ RELATION führt nun über das outermost-Feld ein Verweis zur Wurzel
des Syntaxbaumes dieses komplexen Topologieausdruckes.

next_outermostz Dieses Feld verkettet die zur Komponentenstruktur eines Objektes
gehörenden Wurzeln der Syntaxbäume komplexer Topologieausdrücke.

next_relationl, next_relation2: Diese Felder verketten alle RELATIONS auf einem
bestimmten PART, PARAM oder SET, und zwar getrennt für rechten und linken
Operanden.

Objekttyp C_RELATION

expr: Dieses Feld enthält einen Index der Wertausdruckstabelle. Dort werden die
quantitativen Angaben zu einer C_RELATION gespeichert.

Eine C_RELATION ist das Übersetzungsprodukt einer Bedingung in DROOLY, die
eine RELATION zweier Objekte abfragt. C_RELATIONS werden wie C_PROPERTIES
nicht in den Komponentenbaum eines Objektes engehängt.

Die Tabellen

58



Zusammenfassend besteht die Objektstruktur aus folgenden Feldern:

prefix Verweis auf Aggregat

suffix Verweis auf. Symboltabelle

left Sortierung nach prefix, suffix

right -"-

user Zeiger auf benutzerdefinerte Struktur

spec Auswahlfeld für Objekttyp

classes Vektor der Klassen mit aktuellen Parametern

class_cnt Anzahl d. Elemente im classes-Vektor

fparm_ix Verkettung der PARAMS eines Objektes

complld Liste der Komponenten (negativ, wenn INORDER—
Fädelung)

next_comp Verweis zur nächsten Komponente (negativ,
wenn INORDER—Fädelung)

next_same Verkettung von PROPERTY bzw. RELATION mit

next_outermost
degree
properties [ 0 . . 5]

c_properties[0 . . ll]

zugehörigem C_-Objekt

Verkettung der Outermost—RELATION5
Anzahl deklarierter Komponenten
Zeiger auf die PROPERTY—Objekte

Zeiger auf die C_PROPERTY—Objekte

lmerges linksseitige Merges

lmerge_cnt Anzahl der Links—Merges

merges rechtsseitige Merges

rmerge_cnt Anzahl der Rechts—Merges

quant Operator bzw. Quantor von RELATIONS
opl linker Operand von RELATIONS

op2 rechter Operand von RELATIONS

expr Verweis in die Wertausdruckstabelle
relationlld Liste der RELATIONS mit diesem Objekt

next_relationl
next_relation2
outermost

Verkettung der RELATIONS für opl
Verkettung der RELATIONS für op2
Zeiger auf die Wurzel eines Relationenbaums

7.2 Tabelle der Wertausdrücke

Wie die Objekttabelle ist auch die Ausdruckstabelle als ein Vektor von

Strukturen definiert. Eine Struktur zur
Wertausdruckes besteht aus acht Feldern:

Speicherung eines "atomaren"

spec: Dieses Feld legt den Typ des Atomarausdruckes fest. Mögliche Typen sind
VALUE, OR, AND. COND_OR, COND_AND, C_PROPERTY und ASYMMETRIC.

cond: Dieses Feld enthält alternativ einen Verweis auf einen Atomarausdruck oder
einen Verweis in die Objekttabelle. Seine Hauptanwendung ist die Speicherung
des Verweises auf die Bedingung in einem bedingten Wertausdruck.

left, right: Boolesche Ausdrücke werden aus den dyadischen Operatoren g und
@ bzw. cond or und cond and aufgebaut. Diese beiden Felder verweisen
jeweils auf den linken bzw. rechten Operanden eines booleschen Ausdruckes.
In bedingten Ausdrücken speichert das left—Feld den Verweis auf den
Ausdruck im then—Zweig, das right—Feld speichert den Verweis auf den
Ausdruck im _el_se—Zweig.

Die Tabellen 59



attrib: Dieses Feld speichert ein Attribut. Erlaubte Attributwerte sind CENTERED,
ECCENTRIC, IN_CURRENT_DIRECTION und AGAINST_CURRENT_DIRECTION.

minval, eqval, maxwal: Diese drei Felder dienen zur Speicherung eines
Ordnungszeichens zusammen mit einem numerischen Wert. Dabei wird
folgendermaßen zugeordnet ("n" ist der numerische Wert):

DROOLY } m1n eq max
________ {___—___________
) n i n
>= n i n n
== n i n
<= n l n n
< n i n
i

Die nicht belegten Felder werden durch eine 0 markiert. Auf diese Weise ist
es allerdings nicht möglich, einen numerischen Wert von 0 darzustellen.
