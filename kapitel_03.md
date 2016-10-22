# 3. Die Sprache DINGO—II zur Spezifikation von Technologien

In den folgenden Abschnitten wird die Sprache DINGO-II anhand der
Konstrukte vorgestellt, die sie für eine Technologiespezifikation zur Verfügung
stellt. Für eine detailiertere Beschreibung der Syntax und Semantik der Sprache
siehe [DINGO].

DINGO—II ist eine descriptive Sprache. Im Zentrum einer
Technologiespezifikation steht die Beschreibung erlaubter Objektstrukturen (device—
Prototypen) in einem Layout. Eine vollständige Technologiespezifikation besteht aus
der Beschreibung aller Objekte, die bei der Erstellung eines Layouts verwendet
werden können. Dies beinhaltet sowohl einfache Strukturen wie Leiterbahnen auf

DINGO—II 15



einzelnen Layern als auch komplexe Strukturen. wie Transistoren oder Kontakte.
Darüber hinaus können elektrische und geometrische Randbedingungen formuliert
werden, die direkt den einzelnen Technologieobjekten zugeordnet sind. Der
Abstraktionsgrad der Objektbeschreibungen liegt auf der Ebene der Gebiete im
geometrischen Datenmodell von IMAGE (vgl.Abb. 1.4). Zur Einstimmung sei hier eine
vereinfachte Technologiebeschreibung in DINGO—li wiedergegeben:

declaration
process CMOS
layer Metal 1, Poly 2, Contact_Cut 3,
n_Diff 4, p_Diff 5, n_Well 6

declaration end

module
Gate (diffusion)
using
p_wire of layer Poly
diffusion 933 O_f (layer n_Diff‚ layer p_Diff)

description
shape of Gate i_s iso_Polygon

p_wire cross diffus ion

design rules
internal

width of diffusion:
_i_f diffusion & layer n_Diff then >= 15 else >= 10
module end

device
n_Channe1_Enhancement_Transistor alias n_CE_Trans
using
well of layer n_Well
gt s_et of module Gate (p_Diff) cardinality range [1,5]

description
a_ll gt: gt is gt

a_ll gt: gt m well

design rules
internal
a_ll gt: indistance of well form gt: >= 20
external
distance of n_CE_Trans from n_CE_Trans: >= 30
device end

3.1 Syntaktische Konventionen

Der Aufbau von DINGO—II'Sprachkonstrukten wird in einer BNP—ähnlichen
Notation in Form von Produktionsregeln angegeben. Diese ist besonders bei der
Beschreibung rekursiver Struktur‘en prägnanter als die entsprechende NEMO—
Eingabe. Es wird dabei die Notation aus dem DINGO—II Handuch übernommen, welche
folgende Konventionen benutzt:

DINGO-II . 16



— Metazeichen sind :. i. [. l. l. l. {+, +]
- Das Metazeichen : trennt die linke von der rechten Seite einer Produktion.
— Das Metazeichen : trennt alternative rechte Seiten von Produktionen.

— Die Metazeichen [ und ] schließen optionale Anteile auf rechten Seiten von
Produktionen ein.

— Die Metazeichen l und ] schließen Anteile auf rechten Seiten ein, die beliebig
oft (auch kein mal) auftreten dürfen.

— Die Metazeichen (+ und +} schließen Anteile auf rechten Seiten ein, die
beliebig oft (mindestens aber ein mal) auftreten dürfen.

— Nichtterminalsymbole werden als Textstrings dargestellt. Sie bestehen aus
Kleinbuchstaben, Ziffern und den Trennsymbolen — oder _.

- Terminalsymbole. die lexikalische Literalkonstanten repräsentieren, werden in "
eingeschlossen.

— Übrige Terminalsymbole, die lexikalische Konstanten repräsentieren. werden als
Strings bestehend aus Kleinbuchstaben, Ziffern und den Trennsymbolen — und
_ in unterstrichenem Fettdruck dargestellt.

— Terminalsymbole. die lexikalische Variablen repräsentieren, werden als Strings
bestehend aus Großbuchstaben und Ziffern in Normaldruck dargestellt (z.B.
IDENTIFIER. NUMBER).

3.2 Aufbau einer Technologiespezifikation

Eine Technologiespezifikation in DINGO—II gliedert sich in drei Abschnitte:

dingo_2_description :
declarations object_declarations standard_declarations

— Im Deklarationsteil werden wichtige globale Parameter der Technologie
definiert. Neben dem Prozeßbezeichner werden hier die Layer festgelegt,
symbolische Konstanten definiert. erlaubte Geometrieklassen bestimmt und
weitere Festlegungen wie Skalierung und verwendete Maßeinheiten getroffen.

-— Der Objektdeklarationsteil enthält die Definitionen der Topologie aller
erlaubten Technologieobjekte zusammen mit ihren assoziierten Design Rules.
Darunter fallen sog. Module, Bauteile und Zellen.

—- Der Standardobjekt—Deklarationsteil enthält die Definition von spezialisierten
Technologieobjekten. Im Sinne der objektorientierten Programmierung stellen sie
Unterklassen von Objekten des Objektdeklarationsteils dar. Durch Angabe einer
Oberklasse werden deren Topologiebeschreibungen und Design Rules geerbt. Das

Standardobjekt kann aber einzelne Teile davon durch eigene Spezifikationen
verschärfen.

DINGO-II 17



Da der Aufbau des Objekt— und des Standardobjekt—Deklarationsteils fast
identisch ist, werden sie in der folgenden Beschreibung nicht gesondert betrachtet.
Die Syntax einer Standardobjektdeklaration berücksichtigt deren Auffassung als
Spezialisierung eines Technologieobjektes, in dem bestimmte Freiheitsgrade der
Layoutausprägungen durch feste Wertzuweisungen eingeschränkt sind.

## 3.2. 1 Deklarationsteil

declarations : dedaraüon
process IDENTIFIER
[ declaration ] {+ layer_declaration +}
declaration end
declaration : constant—declaration
! atom—declaration
] geometry-class—declaration
l default—declaration
constant-declaration :
const
IDENTIFIER arith—expr [ unit ]
[ "," IDENTIFIER arith—expr [ unit ] [
atom-declaration :
atom
IDENTIFIER ig func—specs
{ "," IDENTIFIER ig func—specs l
func-specs : func—expr [ gg func—expr ]
geometry—class-declaration:
geometr;y class
IDENTIFIER ig atom—specs
{'H" IDENTIFIER_E atom—specs ]

atom—specs : atom—spec { 93 atom—spec ]
default—declaration :

defaults default ( "," default ]
default : function variable IDENTIFIER

] geometpy class IDENTIFIER
] smde [ arith-expr ] scale—unit
| ﬁgure wküh [ arith-expr ] scale-unit

scale—unit : mils [ micron l gl i mp ] lambda
layer-declaration :
layer

IDENTIFIER layer—attrib
{ "," IDENTIFIER layer-attrib ]
layer—attrib : CONSTANT ] STRING ] bool—expr
unit : ! l ml l k! l A ! mA ! uA ] OEM ! kOHM
l MOHM ] n ! pE ! {E l E ] mE'i n!_l um ] n



Obligatorisch wird im Deklarationsteil als erstes ein Bezeichner für die
definierte Technologie festgelegt. Es folgt optional eine Reihe von Deklarationen.
Wie in vielen konventionellen Programmiersprachen können in DINGO-II symbolische
Konstante definiert werden. Dadurch wird der gesamte Beschreibungstext einfacher
lesbar und Änderungen lassen sich einfacher durchführen.

Weiter ist die individuelle Definition von Geometrieklassen möglich. Die hier
benutzerdefinerten sowie einige bereits dem System bekannte Bezeichner für
Geometrieklassen» können in den Objektdeklarationen zur Einschränkung der
erlaubten Geometrieklassen benutzt werden. Die Definition von Geometrieklassen
fußt auf dem fünfstufigen Geometriemodell in IMAGE. Eine Geometrieklasse ist

umso—n ' 18



definiert durch die Menge unter ihr erlaubter Atome. Atome sind die einzelnen
Begrenzungskanten von Gebieten im Layout. Sie werden durch eine eindimensionale
Funktionsvorschrift definiert. Dies ist die einzige Stelle in DINGO—II, an der ein
Sprachkonstrukt unterhalb der Abstraktionsebene eines Gebietes eingesetzt wird.

Im Default-Deklarationsteil werden Voreinstellungen getroffen, die bestimmte
implizite Annahmen des Systems überschreiben. So kann die abhängige Variable in
Funktionsdefinitionen für Atome (voreingestellt: "X") ebenso geändert werden wie
die Default—Geometrieklasse. Es kann eine globale Skalierung für dimensionslose
Konstanten mit optionaler Angaben- einer Einheit festgelegt werden und die
angenommene Mindestbreite von nicht anderweitig festgelegten Strukturen kann
bestimmt werden.

Obligatorisch ist im Deklarationsteil die Deklaration von Masken-Layern. Jeder
Layer erhält einen eindeutigen Bezeichner und alternativ ein Attribut zugeordnet.
Mit diesem Attribut läßt sich z.B. die Farbe und/oder Schraffur eines Layers für
einen Layouteditor festlegen. Eine weitere Anwendung ist die Zuordnung zwischen
DINGO—II Layern und Layern einer externen Maskendatei z.B. im GIF—Format. Eine
dritte Möglichkeit ist die Angabe eines booleschen Ausdruckes aus Layern und den
Operatoren n_ot, a_np, andnot, p_g, _e_qy und exor erlaubt. Das Attribut wird in
DINGO—II nicht weiter ausgewertet, wird aber in der Symboltabelle gespeichert und
kann von einer Applikation zur Identifikation der DINGO—II Layer mit den
Maskenebenen benutzt werden.

3.2.2 Objektdeklarationsteil

object—decl—part :
[+ object—decl +]
object—decl : module—declaration
] device—declaration
l cell—declaration
device-declaration :
device
object—head
l local—module }
topology
[ design—rules ]
device end

Innerhalb des Objektdeklarationsteils gibt es die drei Objekttypen Modul,
Bauelement und Zelle. DINGO—II nimmt dabei eine Sichtweise im Sinne
objektorientierter Konzepte ein. Die definierten Objekte sind keine eigenständigen
Layoutstrukturen. Vielmehr stellen sie erlaubte Ausprägungen solcher
Layoutstrukturen dar und definieren damit gleichsam Objektklassen. Konkrete
geometrische Strukturen im Layout können in diesem Sinn als Instanzen dieser
Klassen angesehen werden.

Unter einem Bauelement wird eine semantisch unabhängige Struktur wie etwa
ein Transistor oder ein Kontakt verstanden. Ein Modul ist eine semantische Einheit
innerhalb eines Bauelementes, die jedoch nicht unabhängig im Layout vorkommen
kann. Eine Zelle schließlich ist, ein Konglomerat von Bauelementen zu einer
funktionalen Einheit wie etwa einem Logikgatter. Ein Bauelement darf Module und
Layer als Unterstrukturen haben, eine Zelle darf Bauelemente, Module und Layer
als Unterstrukturen haben. Die Unterscheidung ist darüber hinaus jedoch
hauptsächlich konzeptionell und hat nur wenige Auswirkungen auf die Syntax der

DINGO.-II ' 1 9



einzelnen Objektdeklarationen. Daher wird im folgenden nur auf die Struktur einer
Bauelement—Deklaration eingegangen.

Kopf eines Objektes

object—head : IDENTIFIER [ formal—params ]

alias IDENTIFIER

EÄEE

using—decl { "," using—decl ]
formal—params : "(" IDENTIFIER [ "," IDENTIFIER ] ")"
using-decl : IDENTIFIER { "," IDENTIFIER ] type—spec
type-spec : af. BM

: g; gpy layer

] gi: gpy module

] 93 some-objects

l parameter—type

] set-type
some—objects : kmalnmduh IDENTIFIER [ actual—params ]

[ some—global—objects
some—global—objects :

layer IDENTIFIER
] moüﬂe IDENTIFIER [ actual—params ]

actual-parms : "(" IDENTIFIER [ actual-params ]

[ "," IDENTIFIER [ actual—params ] } ")"
parameter-type : one of"(" some—global-objects

[ "," some—global—objects ] “)"
set-type : settﬁ some—global-objects

[ cardinality quantification ]
quantification : exactly arith—expr

! one of
"(" arith-expr [ "," arith—expr l ")“
] range "[" bound "," bound "]"
bound : arith—expr ] "?"

Kopf und Rumpf eines Bauelementes sind in gewissen Maße mit Kopf und Rumpf
einer Prozedur in prozeduralen Sprachen verghﬁchbar. Der Baulementkopf defhüert
einen obligatorischen Bezeichner und optional eine Liste formaler Parameter für das
Bauelement. Es folgt die Deklaration aller benutzter Unterstrukturen mitsamt ihrem
Objekttyp. In DINGO—II besteht Deklarationspflicht. Jede Deklaration einer
Unterstruktur vereinbart einer1 Bezeichner für diese Unterstruktur, der innerhalb
der Bauelementdeklaration bekannt ist. In jedem Fall muß ein Typ angegeben
werden. DINGO-II ist daher im Gegensatz zu z.B. Smalltalk eine typisierte Sprache.
Die Typhüerung istjedoch nicht streng. Viehnehr kann der Typ einer Unterstruktur
auf verschiedene Art festgelegt werden:

— Unspezifische Typdeklaration mit einem der Schlüsselworte nolayer, @y layer
oder gpy module (Bauelemente dürfen keine Bauelemente oder sogar Zellen als
Unterstrukturen haben. Int Kopf einer Zelha sind jedoch auch diese
Mögüchkeüen eﬂaubtl

DINGO—II 20



- Festtypdeklaration mit oder ohne Angabe aktueller Parameter. Der definierten
Unterstruktur wird ein festes Objekt als Typ zugeordnet. Als Objekt kommen
im Kopf von Bauelementen nur entweder Layer, zuvor definierte globale Module
oder innerhalb des Bauelementes definierte lokale Module in Frage. Im Kopf
einer Zelle dürfen dagegen auch Unterstrukturen eines Bauelementtyps oder
eines Zelltyps deklariert werden. Durch die Angabe einer Liste aktueller
Parameter werden Objekte mit formalen Parametern eindeutig bestimmt (siehe
"Parametertypdeklaration").

— Mengentypdeklaration. Einer Unterstruktur wird eine Menge von Objekten eines
Festtyps (mit oder ohne Parameter) zugeordnet. Durch diese Deklarationsforrn
wird ausgedrückt, daß ein Bauelement mehrere Exemplare einer Unterstruktur
verwendet, wobei die (genaue) Kardinalität dieser Menge optional entweder in
Form einer exakten Zahl, als Liste von Alternativen oder als Intervall (u.U.
mit einer unbestimmten Grenze) angegeben sein kann. -

— Parametertypdeklaration. Unterstrukturen können mit einer Liste alternativer
Typen deklariert werden. Jede solcherart deklarierte Unterstruktur wird in der
Liste formaler Parameter des Bauelementes aufgeführt. Die Reihenfolge in der
Liste formaler Parameter entspricht der Reihenfolge der
Parametertypdeklarationen im using—Teil. Wird ein solcherart definiertes
Bauelement (gleiches gilt für Module oder Zellen) als Typ einer Unterstruktur
eines zweiten Objektes benutzt, mt_1ß diese Typangabe eine Liste aktueller
Parameter enthalten. Die Anzahl aktueller Parameter muß mit der Anzahl
formaler Parameter übereinstimmen. Jeder aktuelle Parameter wählt einen
alternativen Typ aus der one of—Liste der Parametertypdeklaration aus. Eine
Bauelementdeklaration mit formalen Parametern läßt sich als abkürzende
Schreibweise für die Deklaration einer Menge "ähnlicher" Objektdeklarationen
ohne formale Parameter auffassen. Jeder erlaubten individuellen
Belegungskombination der formalen Parameter durch aktuelle Parameter
entspricht dann ein eigenes parameterloses Objekt.

Beispiel:

device A (sl, S3)

using
sl one of (module cll, module c12)
52 module C2
53 one of (module c31, module c32)

äquivalente Menge von Objekten ohne Parameter:

device A@cll@c3l device A@c12@c31
using using
sl module c11 sl module c12
52 module c2 52 module GZ
53 module C3]. 33 module c31
device A@cll@c32 device A@cl2@c32
using using
sl module c11 sl module c12
52 module c2 52 module C2

53 module c32 53 module c32

umso—11 21



Lokale Module

local—module : local module
object—head
{ local—module ]
topology
[ design-rules ]
1%.m_oduleerid

Lokale Module defhüeren Strukturen. die nur innerhalb des staüschen
Kontextes eines anderen Objektes bekannt sind. So können Unterstrukturen mit
eigener Semantik. die aber nur innerhalb eines Objektes auftreten können,
innerhalb dieses CHÜektes nüt einen:rnnemonischen Bezeichner angesprochen werden.
Die Struktur einer Lokalmoduldeklaration enspricht der der Bauelementdeklaration.
Innerhalb eines lokalen Moduls dürfen widerum lokale Module definiert werden,
deren Bezeichner nur innerhalb des statischen Kontextes des 1nnsclüießenden
Lokalmoduls bekannt ist. Eine Besonderheit bei lokalen Modulen ist, daß sie erst
nach ihrer Benutzung im using—Teil definiert werden.

Topologiebeschreibungen

topology : desmüpüon [ quantifiers ] statement
( ";" [ quantifiers ] statement ]
quantifiers : [ quantifier ":" ]
quantifier : quantor qualified—identifier
quantor : äﬂ ] some ] gpy ] ppg
qualified-identifier
- IDENTIFIER [ "." IDENTIFIER } l sg!

statement : topology—description

l geometry—decription

! identification

topology—description
topo—operand ( topo—operator topo—operand l

topo—operand : qualified—identifier
I "(" topology—description ")"
topo-operator : cnms ] oveﬂap | endose

] in i touch ] notouch
geometry-restriction :
Shape o_f qualified-id—list
i_s IDENTIFIER { 93 IDENTIFIER ]
qualified-id—list :
qualified—identifier
] qualified-identifier
{ "," qualified—identifier [
identification : qualified—identifier
[+ 15 qualified-identifier +}

In der Topologiebeschreibung wird die Struktur eines Objektes festgelegt. ohne
jedoch auf quantitative Abmessungen der einzelnen Unterstrukturen einzugehen. Da
jedoch die Struktur eines Layoütobjektes im wesentlichen seine Funktionalität
bestimmt, ist die Topologiebeschreibung der zentrale Teil einer Objektdeklaration.
Zur Bildung einer Topologiebeschreibung stehen drei Anweisungsarten zur
Verfügung, welche in behebiger Anzahl und Reihenfolge auftreten dürfen:

DINGO-II

22



- Topologieanweisungen beschreiben die topologischen Beziehungen zwischen
deklarierten Unterstrukturen eines Objektes mit Hilfe der Operatoren cross.
overlap, enclose, i_n, touch und notouch. Die Semantik dieser Operatoren soll

hier

nur anhand einer Graphik veranschaulicht werden. Zur exakten Definition

der Semantik siehe [DINGO].

Abb. 3.2:

Als







A overlap 8 A notouch B A enclose B

Die Topologieoperatoren in DINGO-II

Operanden eine Topologieanweisung dürfen die folgenden Bezeichner

benutzt werden:

1.

Sind

Bezeichner für Strukturen der Typen layer, module, local, module und
innerhalb von Zelldeklarationen auch Bezeichner für Strukturen der Typen
device. c_el_l, standard device oder standard cell, die im using-Teil der
Objektdeklaration deklariert wurden

Bezeichner für Unterstrukturen der unter 1. genannten Strukturen.
Bezeichner dieser Gruppe werden analog zur Notation für Record—
Komponenten in C oder Pascal mithilfe der Punktnotation gebildet. Die
erlaubte Länge solcher Pfade ist nur durch die Komplexität der
beteiligten Unterstrukturen begrenzt.

Schlüsselwort _se_lf zur Bezeichnung des Objektes, in dessen statischen
Kontext es verwandt wird

einige der Operanden einer Topologieanweisung als Mengentyp deklariert,

kann die Topologieanweisung quantifiziert werden. Folgende Quantoren stehen
zur Verfügung:

a_ll: Die folgende Anweisung gilt für jedes Element der durch den Quantor
gebundenen Struktur.

some: Die folgende Anweisung gilt für mindestens ein Element der durch
den Quantor gebundenen Struktur.

pn_e: Die folgende Anweisung gilt für genau ein Element der durch den
Quantor gebundenen Struktur.

ay: Die folgende Anweisung gilt für ein Element, für einige Elemente
oder für alle Elemente der durch den Quantor gebundenen Struktur.

DINGO-II ' 23



Nicht quantifizierte Topologieanweisungen mit Operanden eines Mengentyps
gelten als implizit gli—quantifiziert.

- Geometriebeschränkungen weisen einem Objekt oder einer Liste von Objekten
eine Geometrieklasse zu. Da jedem Objekt in DINGO-II eine Geometrieklasse
zugeordnet ist, gilt für Objekte ohne explizite Spezifizierung entweder keine
Einschränkung oder die im Deklarationsteil deklarierte Default—
Geometrieklasse. Die Zuordnung einer Geometrieklasse gilt nur im lokalen.
statischen Kontext. Einem Objekt kann daher in verschiedenen statischen
Kontexten verschiedene Geometrieklassen zugeordnet werden.

- Identifizierungsanweisungen setzen verschiedene Unterstrukturen eines
Objektes zueinander in Beziehung (1). Neben der einfachen Verbindung der
einzelnen Operanden wird auch eine Reihenfolge der Anordnung der
Unterstrukturen im Layout festgelegt. Typischerweise werden Unterstrukturen
unterschiedlicher Hierarchiestufen (durch Angabe qualifizierter Bezeichner)
durch eine Identifizierungsanwelsung miteinander verbunden. Werden
strukturierte Objekte miteinander verbunden, ist damit auch eine
Identifizierung korrespondierender Unterstrukturen impliziert. Die Korrespon—
denz zweier Unterstrukturen kann durch Vergleich der durch ihre using—Teile
rekursiv definierten part-of-Bäume und anschliessenden Vergleich ihrer
Topologie festgestellt werden (siehe Abschnitt 7.8: Topologievergleich).



(I) Ein ähnlicher lechanismus (dort unter deal Namen 'merges'l ist in einem Artikel über eine spezielle Erweiterung des Smalltalk-Systems zur Bearbeitung von
Constraints beschrieben [Bor 81].Dss dort beschriebene System weist auch andere interessante Analogien zu den Beschreibungsmöglichlteiten von DillGO-Il sul (LB.
die Relerenzierung von Unterstmltturen mittels Pfad-Bezeichner). Der Autor dieses Artikels warnt insbesondere implementierer von Wstemen mit
ldentiftkationsmechanismen vor deren Tücken !

DINGO—II

Design Rule Beschreibungen

design—rules :
internal—rules :
external—rules
design—rule :
quantifier
quantor

24

internal-rules { external—rules }
inﬂnnali+ design—rule +}

ex“nnali+ design—rule +}

{ quantifier ":" ! dr—head dr—body
quantor qualified—identifier
a_lllan£ianxlw

qualified-identifier

dr—head
dr—property
dr—relation

dr—operand :

all—objects :

!
all—global—objects

|
|
l
l
I
l
|
!

actual-params
dr—body :
simple-dr-body :

simple—dr—term .
simple—dr—elem :

dr—attribute

quantification :

bound
dr-expr
comp—op
dr—value

IDENTIFIER { "." IDENTIFIER } ! säf
dr—property dr—operand ":"
dr-relation dr-operand "," dr—operand ":"

width } length ! area : hl angle

radius : dhnension

overhang ! overlap

in distance } distance : out angle

ax laxer } ay_x module } m device

32! ggä_l all—objects
qualified-identifier

kmalnmduh IDENTIFIER [ actual-params ]
all-global—objects

laXM'IDENTIFIER

moüﬂe IDENTIFIER [ actual-params ]
devke IDENTIFIER [ actual—params ]
ggQ IDENTIFIER [ actual—params ]
standard device IDENTIFIER

standard ceH IDENTIFIER

"(" IDENTIFIER [ actual—params ]

{ "," IDENTIFIER [ actual-params ] ] ")"
simple-dr-body

conditional-dr—body

{ simple—dr-term gg ! simple-dr-term
{ simple—dr-elem 229 } simple—dr-elem
[ dr—attribute ] quantification

[ dr-attribute ] dr—expr

asxggnetric

"(" dr—value { "," dr—value } ")"
"(" simple—dr-body ")"

centered ! eccentric ! in current direction

against current direction

exacüy arith—expr

M

"(" arith-expr { "," arith—expr } ")"
range "[" bound "," bound “]"
arith—expr } "?"

camp-op arith-expr

">=" ‘ ">=" { ">" I "<" : ll_=ll
dr—expr

II (”II dr_expr " ) " ll * "

ll ( " dr_expr " ) " "+"

umso—n ' 25



conditional—dr—body :
if condition then dr—body

_ [ else dir—body ]
: „(" conditional—dr—body n)"

condition : { cond—term gg { cond—term

cond-term : { cond—elem ggd } cond-elem

cond—elem : cond-property dr—operand simple-dr-elem
! dr—relation dr—operand "," dr—operand

simple—dr-elem
{ IDENTIFIER 15 some-objects
some—objects : kmalnmduh IDENTIFIER [ actual—params ]
{ some-global—objects
some-global—objects
hggg IDENTIFIER
: moüﬂe IDENTIFIER [ actual—params ]
cond—property : dr—property
voltage ! current } power % resistance
{ capacitance | edge length

Ebenso wie die Topologie eines Layout-Objektes für seine Funktionalität
verantwortlich ist, legen die quantitativen Abmessungen seine elektrischen
Parameter fest und sind damit letztendlich ausschlaggebend für das Funktionieren
einer implementierten Schaltung. Design Rules können in DINGO—II für jedes Objekt
angegeben werden. Es gibt jedoch keine Design Rules, die nicht an irgend ein
Objekt gebunden sind. Jedes Objekt kann optional einen Block mit internen
und/oder einen Block mit externen Design Rules erhalten. interne Design Rules
legen geometrische Restriktionen für die Unterstrukturen eines Objektes fest.
Externe Design Rules beschreiben geometrische Restriktionen für ein Objekt
innerhalb seines Layout-Kontextes. Dies beinhaltet geometrische Beziehungen zu
anderen Objekten, aber auch zu Layoutinstanzen desselben Objektes.

Jede Design Rule besteht aus den zwei Bestandteilen Kopf und Rumpf. Im Kopf
einer Design Rule wird ihr Typ und betroffene Objekte angegeben. Je nach Typ
können Eigenschaften eines Objektes oder Beziehungen zwischen Objekten mit einer
Design Rule quantifiziert werden. Erlaubte Typen zur Quantifizierung von
Eigenschaften sind width, length. @, in angle‚ radius oder dimension. Erlaubte
Typen zur Quantifizierung von Beziehungen sind overhang. overläap, in distance,
distance oder out angle. Auch hier sei die Semantik der Operatoren nur anhand
einer Graphik illustriert. Die exakte Definition der Semantik findet sich in [DINGO].

DINGO-II 26



X

EJ





D;
DE
L:
ru

| | | t l |
. x
wrdlh length lru
4
“// N ,
/ \‘
& ai //
/3
_ 6 in_nnqle
radius dimoncion

Abb. 3.3a: Design Rule Eigenschaften

]

/\\
\\

/' \

L*Li ......r’ ä“ﬂ VII,

®V%

[___ ‚/’ //
overlnp distance \/

fl



\\J

































in_diulunco

Abb. 3.3b: Design Rule Beziehungen

Ist ein Operand ein Objekt vom SET-Typ, kann die Design Rule mit denselben

Quantoren wie in Topologieanweisungen (a_ll‚ some, aux,

quantifiziert werden. Nicht quantifizierte Design Rules mit Objekten vom SET-Typ

gelten auch hier als implizit Lil—quantifiziert.

Die betroffenen Objekte können auf drei Arten spezifiziert werden:

— einzelne geometrische Strukturen werden durch einen möglicherweise
qualifizierten Bezeichner spezifiziert. Dadurch ist auch das Überschreiben von

Design Rules für beliebige Unterstrukturen möglich.

».

DINGO—II 27

alle Instanzen eines Objektes werden durch Angabe der Objektklasse optional
gefolgt von einer Liste aktueller Parameter spezifiziert. Diese Art ist nur in
externen Design Rules erlaubt.

- alle Objekte eines Typs werden durch das Schlüsselwort a_ny gefolgt von der
Angabe des Objekttyps spezifiziert. Die Design Rule bezieht sich in diesem
Fall einfach auf alle Objekte von diesem Typ. Auch diese Art ist nur in
externen Design Rules erlaubt.

Der Rumpf einer Design Rule ist vom Kopf durch einen Doppelpunkt getrennt.
wodurch der fordernde Charakter einer Design Rule unterstrichen wird. Zur
Quantifizierung der geometrischen Restriktion gibt es im Rumpf einer Design Rule
syntaktisch zwei Möglichkeiten. Gilt eine Design Rule im gesamten Objektkontext im
Layout. reicht eine einfache Quantifizierung. Dazu können in einem Design Rule
Wertausdruck Unter- und bei Bedarf auch Obergrenzen eines erlaubten
Wertebereiches angegeben werden. Alternativ ist auch die Angabe von Intervallen
oder die exakte Festlegung eines Zahlenwertes oder einer konsekutiven Liste von
Zahlenwerten möglich. Einfache Quantifizierungen können mit den booleschen
Operatoren al und @ verknüpft werden.

Reicht es nicht aus, daß eine Design Rule innerhalb des gesamten
Objektkontextes gültig ist. kann mit einer Bedingung der lokale Kontext zur
Entscheidung über den gültigen Wertebereich herangezogen werden. Die Syntax
einer bedingten Design Rule ähnelt der von bedingten Ausdrücken z.B. in der
Programmiersprache PASCAL. Als Bedingungen sind boolesche Ausdrücke aus
Eigenschaften von und Beziehungen zwischen Objekten erlaubt. Die Syntax ist
identisch mit der von Design Rules; Kopf und Rumpf von Bedingungen werden

allerdings nicht durch einen ":" getrennt, da es sich nicht um eine Forderung.
sondern lediglich um eine Abfrage handelt. Die Liste der erlaubten Schlüsselworte
für Eigenschaften in Bedingungen wird um die Schlüsselworte voltage, current.

power. resistance, capacitance und edge length erweitert.

Durch die Strukturierungsmöglichkeiten und die Einführung einer
Parametrisierbarkeit von Objektbeschreibungen erweist sich noch eine zusätzliche
Möglichkeit zur Formulierung von Bedingungen als nützlich: In Design Rule
Bedingungen kann die aktuelle Objektklasse eines Operanden abgefragt werden und
zur Entscheidung über den erlaubten Wertebereich der geometrischen Einschränkung
herangezogen werden.
