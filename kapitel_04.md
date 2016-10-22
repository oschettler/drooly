# 4. Die DROOLY Zwischensprache

## 4.1 Grundlagen

Ziel der Definition einer Zwischensprache für den DINGO—II Compiler war es,
eine saubere Schnittstelle zwischen Compiler-Frontend (dessen Entwicklung nicht
Gegenstand dieser Arbeit ist) und Compiler—Backend zu schaffen. Die hier
vorgestellte Zwischensprache DROOLY ("Devices Represented in Object Oriented
Lyrics") muß verschiedenen Anforderungen genügen:

— Vollständige Technologiespezifikation. Die Struktur von in DINGO—II definierten
Technologieobjekten muß sich auch in DROOLY darstellen lassen. Nicht
strukturelle Informationen werden dabei nicht nach DROOLY übersetzt, sondern

gelangen vom Frontend über die Symboltabelle direkt zu den anfordernden
Applikationen.

DROOLY ' 29



- Manuelle Programmerstellung möglich. Während der Fertigstellung dieser Arbeit
stand noch kein voll funktionsfähiges Compiler—Frontend zur Verfügung. Es
war daher notwendig, zu Testzwecken DROOLY—Technologiebeschreibungen von
Hand zu erstellen. Dies begünstigte die Verwendung eines textuellen Formates.
Bei der Integration mit dem Compiler—Frontend sollte jedoch ein Binärformat
erwogen werden, da sich ein solches schneller schreiben und lesen läßt.

- Integrierbarkeit. Als typische Zwischensprache muß DROOLY einen leichten
Übergang von der benutzerfreundlichen, teilweise recht wortreichen
Technologiebeschreibungssprache DINGO—II in eine kryptische, dabei aber
wohlstrukturierte und möglichst einheitliche interne Darstellung der
Technologieinformationen ermöglichen. DINGO—II ist bereits definiert; die
Philosophie der objektorientierten Datenhaltung in IMAGE ist ebenso bereits
durch mehrere Diplomarbeiten zu diesem Thema [Mör 88], [Pre 88], [Wro 871
weitgehend vorgegeben.

## 4.2 Vererbungsmechanismen

DINGO—II kennt als objektorientierte Sprache zwei Vererbungsmechanismen, die
zwar an unterschiedlichen Stellen bei der Technologiespezifikation auftreten, die
jedoch ähnliche Auswirkungen auf die erzeugte Struktur haben.

Objektdeklarationen enthalten in ihrem Kopf die Liste aller Unterstrukturen
des Objektes. Jeder deklarierten Unterstruktur sind ein oder mehrere Typen und
eine oder mehrere Klassen zugeordnet. Ist die Klasse einer Unterstruktur bei einer
Festtypdeklaration oder einer Mengentypdeklaration eindeutig bestimmt, kann im
Topologiebeschreibungsteil oder im Design Rule Teil nicht nur auf die Unterstruktur
selbst, sondern über einen qualifizierten Bezeichner auch auf deren Unterstrukturen
bezug genommen werden. Durch die Verbindung mit einer Klasse erbt die
Unterstruktur alle Komponenten ihrer Klasse. Es sind dies nicht nur die
deklarierten Unterstrukturen. Auch Topologiebeschreibungen und Resign Rules auf
den deklarierten Unterstrukturen werden mitvererbt.

Bei einer Parametertypdeklaration ist keine eindeutige Zuordnung zwischen
Unterstruktur und Klasse gegeben. Qualifizierte Bezeichner mit dieser Unterstruktur
als Präfix (Le. als erste Pfadkomponente) sind daher in dieser Objektdeklaration
nicht zulässig. Wird aber ein zweites Objekt mit einer Komponente deklariert, die
daß erste Objekt als Klasse erhält, so wird über die aktuellen Parameter die Klasse
der Unterstruktur des ersten Objektes eindeutig festgelegt. Die Unterstruktur erbt
in diesem Moment alle Komponenten ihrer nun eindeutigen Klasse. Qualifizierte
Bezeichner werden damit möglich.

module CMOS_TMIM_GAN\
Inu-n: der
Klone

module end ___.
device CMOS_THM \_
ueing gute of module CMOS_Tr-m_Gule

Abb. 5.1: Instanziierung einer Klasse

Der zweite Vererbungsmechanismus in DINGO—II ist offensichtlicher. Durch die
Standardobjekt-Deklaration erhält der Benutzer die Möglichkeit, ein
Technologieobjekt zu spezifizieren, das in wesentlichen Aspekten seiner

DROOLY ' 30



Objektklasse gleicht. Bei der Objektklasse handelt es sich um ein normales
Technologieobjekt (device oder cell). Standardobjektbildungen von Standardobjekten
sind nicht zulässig. Das Standardobjekt erbt alle deklarierten Unterstrukturen
ebenso wie alle topologischen Relationen und Design Rules der Oberklasse auf
ihnen. Das Standardobjekt kann einzelne Topologieanweisungen oder Design Rules
seiner Oberklasse überschreiben. Jedoch ist es nicht möglich, neue Unterstrukuren
zu definieren, da hierdurch die Struktur und damit auch die Funktionalität des
Objektes im Layout verändert würde (2).

device CMOS_Trene—N Unterkleue '

von ?
device end
eienderd device minimum_p_Trene

Abb. 5.2: Un terk]assenbildung

## 4.3 Die Beispieltechnologie in DROOLY

Um die folgende Beschreibung der Syntax von DROOLY zu veranschaulichen,
wird hier zunächst ein Beispiel für eine Technologiespezifikation in DROOLY
gegeben. Das Format entspricht der der Präprozessoreingabe. Der DROOLY—
Präprozessor ist kein eigentlicher Bestandteil des Compiler—Backends. Er erlaubt
jedoch eine mnemonische Schreibweise der Schlüsselworte, das Einfügen von
Kommentaren sowie die Benutzung von Bezeichnern für Objekte und wurde während
der Entwicklung des Compiler—Backends zur Programmierung von Testbeispielen
benutzt. Der Präprozessor erzeugt aus seiner Eingabe das eigentliche DROOLY-
Format sowie die zugehörige Symboltabelle.



(2) Dies ist ein Unterschied LB. zu Smalltalk. wenn man auch sonst die DINGO—il Unterstrukturen als Analogon zu den dortigen [mtanzvariablen betrachten kann.

DROOLY ' 31



; eine CMOS-Technologie

.
’

layerdef Metal
layerdef Poly
layerdef Contact_Cut
layerdef n_Diff

layerdef p_Di f f
layerdef n_Wel 1

(classdef Gate (fpar diffusion )

part p_wire (class Poly )
param diffusion (class n_Diff: ) class p_Diff )

shape iso_Polygon
cross [ ] p_wire diffusion
diffusion width [ & diffusion (classtest n_Diff )
then >= 15
_el_sg >= 10
l
)
(classdef n_Channe1_Enhancement_Transistor

part well (class n_Well )
s_et[ai>=l<=5]gtﬁclassGate2)

a_ll gt (merge gt gt )

a_ll gt indist [ ] gt well

gl_l gt indist [ >= 20 ] gt well
dist [ >= 30 ] &lf &lf

## 4.4 Syntaktische Struktur

Für die Beschreibung der DROOLY—Syntax wird dasselbe Format wie bei der
DINGO—II Beschreibung verwendet. Da DROOLY als Zwischenformat nicht zur direkten
Benutzung durch einen menschlichen Benutzter konzipiert ist, kann die lexikalische
Struktur der Sprache wesentlich einfacher als die von DINGO—II ausfallen. Eine
DROOLY—Technologiespezifikation besteht aus folgenden lexikalischen Primitiven,
die formatfrei gemäß der in den folgenden Abschnitten beschriebenen Syntax
angeordnet werden können:

— Trennzeichen sind Blank, Tabulatorzeichen und Zeilensprung. Die Anzahl und
Art von Trennzeichen ist nicht signifikant. In jedem Fall müssen aber
Bezeichner, Dezimalkonstanten und Token jeweils durch mindestens ein
Trennzeichen voneinander getrennt sein.

— Kommentare sind sowohl in der Präprozessoreingabe als auch im DROOLY-
Format erlaubt. Ein Kommentar wird durch ein ";" in beliebiger Spalte
eingeleitet und reicht bis an das Ende der Zeile. Alle Zeichen zwischen dem
Semikolon und dem Zeilenende werden überlesen.

«'.

DROOLY 32



— Lexeme bestehen aus einem, zwei oder maximal drei Sonderzeichen, Ziffern oder

Kleinbuchstaben, Die verwendeten Sonderzeichen sind &, (. ), <, >, =, ?, [, ], :,

i und '. Zu beachten ist in der Präprozessoreingabe, daß einige Schlüsselworte
als erstes Zeichen eine "(" enthalten. Diese ist fester Bestandteil des
Schlüsselwortes und darf nicht durch ein Trennzeichen vom Rest des
Schlüsselwortes getrennt sein ( z.B.: "(classdef" ). Solche Schlüsselworte leiten
allgemein ein komplexes Konstrukt (2.8. eine Objektdefinition) oder eine Liste
(z.B. die Liste formaler Parameter eines Objektes) ein. Das Konstrukt oder die
Liste werden dann durch eine einfache ")" abgeschlossen. Eine vollständige
Liste der DROOLY—Lexeme zusammen mit ihren Präprozessor—Ensprechungcn
findet sich im Anhang A.

— (qualifizierte) Bezeichner haben folgenden Aufbau, geschrieben als regulärer
Ausdruck:

@[0—9]'(.[0-91+)' (3)

Ein Bezeichner besteht also aus einem "@", ohne Trennzeichen gefolgt vom
Präfix eines Pfades. Dabei handelt es sich um den Symboltabellenindex eines
Objektes. Ist der Bezeichner qualifiziert, folgen jeweils durch einen Punkt,
aber ohne Trennzeichen getrennt weitere Symboltabellenindizes. Normalerweise
verstehen sich (qualifizierte) Bezeichner relativ zum statischen Kontext ihrer
Benutzung. Solche Bezeichner erhalten im Backend automatisch den Bezeichner
des aktuellen Objektes als Präfix. Ist jedoch das erste Zeichen nach dem "@"
ein Punkt, liegt ein absoluter Pfad vor. Ein besonderer Bezeichner ist das
Lexem "%" (das Schlüsselwort &lf in der Präprozessoreingabe). Dieser wird im
Backend durch den Bezeichner des aktuellen Objektes ersetzt.

In der Präprozessoreingabe fehlt das "@"—Zeichen. Die einzelnen Bezeichner für
die Pfadkomponenten werden jeweils durch einen Punkt. aber ohne
Trennzeichen getrennt. Auch hier können absolute Pfade einfach durch einen
Punkt vor dem ersten Bezeichner gebildet werden. Bezeichner werden in der
folgenden Syntaxbeschreibung durch IDENTIFIER gekennzeichnet.

- Dezimalkonstanten werden nach folgendem regulären Ausdruck gebildet
(Notation wie in der Fußnote (S)):

#[0-9]+

Eine Dezimalkonstante besteht einfach aus einem "#", ohne Trennzeichen

gefolgt von einer nichtleeren Ziffernfolge. In der Präprozessoreingabe fehlt das
"#".

Im Hinblick auf eine möglichst einheitliche, durchgängige Struktur der zu
erzeugenden Tabellen werden schon in DROOLY keine Unterschiede zwischen den
einzelnen strukturierten Objekttypen gemacht. Syntaktisch gleichen sich also die
DROOLY-Beschreibungen von Modulen, Bauelementen, Zellen, Standardbauelementen
und Standardzellen völlig. In Hinblick auf objektorientierte Programmiersprachen
definieren alle DROOLY—Objekte Objektklassen. Auch das Konzept der lokalen
Module ist syntaktisch in DROOLY nicht mehr zu erkennen. Um trotzdem ein Mittel
zur Beschreibung ihrer Lokalität zur Verfügung zu stellen, können Objektklassen
mit einem qualifizierten Bezeichner definiert werden. Dieser dient dann auch als

\
.



(3) [0-91 steht für ein Zeichen (eine Dezimalziilerl; runde Klammern ('('‚ ')“) fassen mehrere Zeichen zu einer Kette zusammen; '" markiert das kein-, ein- oder
mehrmalige Auftreten eines einae'zen Zeichens oder einer geklammerten Zeichenkette; “+“ markiert das mindestens einmalige Auftreten eines einzelnen Zeichens oder
einer geklammerten Zeichenkette.

DROOLY 33



impliziter Präfix für alle im statischen Kontext des "lokalen" Moduls benutzten
Bezeichner.

## 4.5 Layerdefinitionen

Alle Informationen aus dem DINGO—II Deklarationsteil werden in der
Symboltabelle gespeichert. Da jedoch Layer als unstrukurierte Objektklassen
aufgefaßt werden können, werden die Layerdeklarationen in den DROOLY—Text
übernommen. Eine Technologiebeschreibung im DROOLY—Zwischenformat besteht somit
aus Objektdefinitionen, wobei zwei Objekttypen möglich sind:

intermediate : i+ layer_definition +!
[+ class_definition +]

Da Layer unstrukturierte Objekte sind, unterscheidet sich die Syntax ihrer
Definition von der Syntax der übrigen Objektdefinitionen:

layer_definition : layerdef IDENTIFIER

Layer werden abgesehen von ihrer Strukturlosigkeit wie normale Objekte
behandelt. Insbesondere dürfen natürlich wie in DINGO—II Unterstrukturen vom Typ
Layer deklariert werden. Es ist jedoch wie in DINGO—II nicht möglich, Design Rules
direkt an einen Layer zu binden. Sollte so etwas notwendig werden, ist eine eigene
Objektklasse mit dem Layer als einziger Unterstruktur zu definieren, innerhalb
welcher Design Rules für die Unterstruktur angegeben werden können.

## 4.6 Objektklassendefinitionen

Bedingt durch die Notwendigkeit, Standardobjekten den Bezeichner ihrer
Oberklasse mitzugeben, gibt es zwei Typen von Objektklassendefinitionen:

class_definition :
(classdef IDENTIFIER
[ f ar [+ IDENTIFIER +l ")" ]

{ declaration } [ description } ")"
{ (MddefIDENTIFIER class_call
{ declaration ! [ description ! ")"
class_call : (ckws IDENTIFIER { INTEGER ! ")"

Der Kopf eines Objektes definiert einen eindeutigen Bezeichner und optional
‚eine Liste formaler Parameter. Es folgt die Deklaration der Unterstrukuren,
wiederum gefolgt von der Beschreibung des Objektes. Standardobjekten wird
zusätzlich der Bezeichner ihrer Oberklasse zugewiesen. Gibt die Definition der
Oberklasse eine Liste formaler Parameter an, kann das Standardobjekt eine
entsprechende Liste aktueller Parameter enthalten. Dadurch können die einzelnen
Parametertypdeklarationen von Unterstrukturen der Oberklasse mit eindeutigen
Klassen versehen werden. Das Standardobjekt erbt dann Unterstrukuren mit einer
eindeutigen Klasse. Werden keine aktuellen Parameter angegeben, werden

parametertypdeklarierte Unterstrukturen geerbt. Eine nur teilweise Angabe von
aktuellen Parametern ist nicht erlaubt.

Anders als in DiNGO—II werden als aktuelle Parameter nicht Bezeichner
übergeben, sondern Indizes in die Alternativliste von Klassen bei einer
parametertypdeklarieﬂzen Unterstruktur. Da die alternativen Klassen wieder aktuelle
Parameter tragen können, welche wiederum aktuelle Parameter tragen können,

DROOLY 34



ergibt sich eine u.U. recht komplizierte Struktur zur genauen Spezifikation einer
Klasse (welche allerdings wohl nur in den seltensten Fällen über eine zweifache
Schachtelung hinausreichen wird). Durch die Angabe eines einfachen
Positionszeigers in die Liste alternativer Klassen wird das sonst nötige Duplizieren
dieser komplizierten Struktur vermieden. Schließlich steht bei einer Zwischensprache
nicht die einfache Lesbarkeit im Vordergrund, sondern ihre Ausdrucksstärke und
einfache Verarbeitbarkeit.

## 4.7 Deklaration von Unterstrukturen
Wie in DINGO-il ist auch in DROOLY die Deklaration von Unterstrukturen auf

verschiedene Art möglich. Die unterschiedlichen Deklarationen werden jeweils durch
ein besonderes Schlüsselwort eingeleitet:

declaration : part } param : set
part : @_r_t_; IDENTIFIER class_call
param : param IDENTIFIER {+ class_call +i
set : s_e_t IDENTIFIER class_call card_range
card_range : "[" [ card_body ] "]"
card_body : eq_spec

! or_card

) g_r1d ">=" INTEGER "<=" INTEGER
or_card : g_r; or_card eq_spec

i % eq_spec eq_spec
eq_spec : "==" INTEGER

Für jede Art von Unterstruktur wird zunächst ein Bezeichner definiert, der
innerhalb des statischen Kontextes eindeutig sein muß. Einem PART (Festtyp—
deklaration) wird dann eine eindeutige Objektklasse (u.U. mit aktuellen Parametern)
zugeordnet. Unspezifische Typdeklarationen werden als Spezialfall der
Festtypdeklarartion behandelt. Dazu werden vom Compiler-Frontend Symboltabellen—
einträge für die speziellen Objekte NOLAYER, ANYLAYER, ANYMODULE, ANYDEVICE
und ANYCELL erzeugt und als Objekttypen für unspezifische Typdeklarationen
verwandt.

Ein PARAM (Parametertypdeklaration) erhält eine Liste alternativer Objekt—
klassen. Ein SET (Mengentypdeklaration) schließlich kann neben der eindeutigen
Objektklasse optional noch eine Kardinalitätsangabe erhalten. Die Syntax dieser
Kardinalitätsangabe ist eine Untermenge eines einfachen Wertausdruckes, wie er in
quantifizierten Geometrieeinschränkungen benutzt wird (su). Erlaubt sind an dieser
Stelle nur über 93 verkettete Alternativen oder die Angabe einer unteren und einer
oberen Intervallgrenze.

## 4.8 Beschreibungsteil

Nach der Deklaration aller Unterstrukturen folgt der Beschreibungsteil:

description : merge ! forward : relation
merge : (merge {+ IDENTIFIER +) ")"
forward : [ [ Quantor_spec ] IDENTIFIER ] property
property : property_type expression

{ (Shape {+ IDENTIFIER +)
property_type : width ! area { dimension ) radius ) length
quantor_spec : Ä } some { a_ny { ae



DROOLY ' 35



Ein Beschreibungs-"Anweisung" besteht in DROOLY alternativ aus einem merge,
einem forward oder einer relation. Ein merge — der Begriff ist aus dem ThingLab—
System entlehnt (siehe Fußnote auf Seite 23 ) - ist die DROOLY—Entsprechung der
Identifikationsanweisung aus DINGO—II. Hinter“ dem Schlüsselwort (merge kann eine
beliebige Liste zumeist qualifizierter Bezeichner angegeben werden. Die
Layoutinstanzen der zugehörigen Objekte werden mitsamt ihren Unterstrukturen
miteinander identifiziert, wobei durch ihre Reihenfolge in der Liste zusätzlich eine
Reihenfolge im Layout induziert wird.

Forwards (4) bzw. die zugrundeliegenden properties sind die
Übersetzungsprodukte von einstelligen DINGO—II Design Rules bzw. von
Geometriebeschränkungen aus dem Topologiebeschreibungsteil. Da die beschriebenen
Eigenschaften dem DINGO—II Design Rule Teil entstammen, enthalten sie einen
Wertausdruck, der die Eigenschaft quantifiziert. Die Struktur von Wertausdrücken
wird in Abschnitt 5.9 behandelt.

Fehlt der Bezeichner, so wird die Eigenschaft der aktuellen Klasse zugeordnet.
Durch Angabe eines beliebigen, qualifizierten Bezeichners ist es möglich,
Eigenschaften von Unterstrukturen und deren Unterstrukturen festzulegen.
Beschreibt ein forward eine Eigenschaft einer mengentypdeklarierten Unterstruktur,
ist die zusätzliche Angabe eines Quantors möglich (nicht quantifizierte forwards
gelten in diesem Fall als implizit a_ll—quantifiziert).

Bei Geometriebeschränkungen muß die in DINGO—II mögliche Angabe einer Liste
von Objekten in mehrere forwards aufgelöst werden. Der Bezeichner gehört zu einer
im DINGO—II Deklarationsteil definierten Geometrieklasse oder zu einer der dem
System direkt bekannten Geometrieklassen, die jedoch ebenfalls eigene
Symboltabelleneinträge haben müssen.





description : merge : forward ) relation
relation : quantor_spec IDENTIFIER relation
{ complex_area

complex_area : relation_type expression area area
area : IDENTIFIER { class_call ! complex_area
quantor_spec : EH ! some ) any { gng
relation_type : huﬁstl (nass { ovedgp {

touch ) dist { outangle
class_call : dass IDENTIFIER ( INTEGER ) ")"

Im Gegensatz zu DINGO-II gibt es in DROOLY keine Unterscheidung zwischen
Topologiebeschreibung und Design Rules. Design Rules werden vielmehr als
quantitative Topologieausdrücke geschrieben. Zur Rechtfertigung dieser
Vorgehensweise ist es notwendig, eine Zuordnung der Operatoren des Topologie—
Teils zu den Operatoren des Design Rule Teils zu finden. Eine solche Zuordnung ist
in der folgenden Tabelle gegeben. Die erste Spalte enthält den Operator in seiner
DINGO-II Topologiebeschreibungs—Form, die zweite Spalte enthält den vergleichbaren
Operator aus den Design Rules und die dritte Spalte nennt den entsprechenden
Operator in DROOLY—Notation:



(4) in Anlehnung an den Forward-Begriff in einem Artikel über die Erweiterung von Smalltalk um PART-OF—Hierarchien {Bla 87)

.!
3

D

3 n.

DROOLY ' 36



Lage— Design Rule ) DROOLY—
schlüssel Typ ! Operator
_______________________ {__-________
in in_distance ) indist
cross overhang ! cross
overlap overlap } overlap
enclose distance ) enclose
touch —-— l touch
notouch distance ! dist

—-— out_angle } outangle

Eine weitere Eigenschaft der Design Rule Typen in DINGC)—II rnacht die
Zusanunenfassung vorr Topologiebeschreibung und Design Rules sinnvolh Laut
DINGO—II Handbuch ist eine Design Rule vom Typ overhang nur definiert, wenn für
beide Operanden die 'Topologiebeziehung cross erfüUt ist. Gleiches gﬂt für die
Paare overlap/overlap, indistance/"in und distance/notouch. Der Design Rule Typ
out_ang1e ist nur bei einer der Topologiebeziehungen cross, overlap oder touch
zwischen den beiden Operanden defhüert. Durch diese Zuordnung wird die
Verwendung eines einzigen Satzes von Operatoren sowotü für die Topologiebeschrei-
bung als auch für die Design Rulesrnögüch. Topologieausdrücke enthalten dann nur
einen leeren W@rtausdruck. Als Operanden für eine redaüon konunen verschiedene
Objekttypen in Frage:

- innerhalb der statischen Umgebung des aktuellen Objektes deklarierte
Unterstrukuren oder derer: Unterstrukturen. Sind denr Objekt in einer
Unterstrukturdeklaration bereits quantifizierte RELATIONS oder PROPERTIES
zugeordnet werden die neu lünzukommenden Quantüiüerungen nüt den ahen
Quantüiüerungen gnd—verknüpft

— alle Objekte eines Typs. Dieser Fall wird als Sonderfall des ersten CHﬁekttyps
unter Verwendung der speziellen "Pseudoobjekte" ANYLAYER, ANYMODULE,
ANYDEVICE. ANYCELL behandelt

— eine Objektklasse, u.U. nüt Angabe von aktueﬂen Parametern. Operanden
dieses 'Typs dürfen in DINGCT—H nur in. zweisteHigen. externen Design Rules
verwandt werden. Ist eine Parameterüste speziﬁzierß so ist die Design Rule
nur auf die Teihnenge von CHÜektinstanzen arnvendbar, die die
Parameterbelegung erfüﬂen, ansonsten gﬂt sie für CHÜektinstanzen nüt
behebiger Parameterbelegung.

— Operand einer RELATION kann wieder eine RELATJON sein. RELATIONS
repräsentieren ja selbst Strukturen in: Layout (nüt Flächerunaß 0 bei dist und
touch) und lassen sich auf natürüche Weise als Operanden weiterer RELATTONS
benutzen, was Topologieausdrücke von behebiger Schachtelungsüefe'ermöghcht.

Werden innerhalb einer RELATION Operanden eines Mengentyps benutzt können
RELATIONS von einem oder mehreren Quantoren beherrscht werden. Nicht
quantiﬁzierte Operanden eines Mengentyps gelten als hnpüzit gli—quantiﬁziert

DROOLY ' 37



## 4.9 Wertausdrücke

expression : "[" [ body ] "]"
body : simple_body

{ expression

! if condition then body [ e_lsg body ]
simple_body : gr; simple_body simple_body

{ and simple_body simple_body
! [ attribute_spec ] value_spec
{ (asmmetric {+ value_spec +} ")"

value_spec : ">=" INTEGER { "<=" INTEGER
{ " > " INTEGER { " < " INTEGER
| "==" INTEGER

attribute_spec : centered { eccentric

\ in current direction
against current direction
condition : gi; condition condition
ai condition condition
c_forward

relation

IDENTIFIER

(classtest IDENTIFIER { INTEGER ! ")"
c_forward : [ [ quantor_spec ] IDENTIFIER ] c_property
property : c_property_type expression

c_property_type :
width { area | dimension { radius { length
{ edge length { voltage { current
{ power { resistance { capacitance { in angle

Wertausdrücke entsprechen in DINGO—II den Design Rule Rümpfen. Syntaktisch
sind sie die komplizierteste Struktur der DROOLY—Zwischensprache. Das entspricht
aber durchaus den Verhältnissen in DINGO—II. Allerdings sind durch die gewählte
Präfixnotation zur Bildung boolescher Ausdrücke zwischen einfachen Wertausdrücken
bzw. zwischen Bedingungen zusätzliche Regeln zur Berücksichtigung von
Assoziativitäten und Präzedenzen unnötig. Da in DROOLY nicht zwischen Design
Rules und Topologiebeschreibungen unterschieden wird, erhält jede RELATION und
jede PROPERTY (mit Ausnahme der Geometrieeinschränkungen) einen Wertausdruck.
Dieser ist bei Ubersetzungsprodukten von Topologiebeschreibungen allerdings leer.
Wertausdrücke werden außerdem zur Spezifikation einer Kardinalität in
Mengentypdeklarationen verwandt. Die dabei zulässige Syntax ist aber wesentlich
eingeschränkt (vgl. Seite 33).

Wertausdrücke werden in DROOLY in eckige Klammern ("[", ")") eingeschlossen.
Ein Wertausdruck kann entweder aus einem einfachen Rumpf bestehen oder er ist
bedingt und besitzt dann einen then— und optional einen gl_sg—Zweig die von einer
Bedingung beherrscht werden. Ein einfacher Rumpf ist ein Präfixausdruck,
bestehend aus den booleschen Operatoren @ und _agi und aus Wertbestimmungen
als Operanden. Im einfachsten Fall besteht eine Wartbestimmung aus einem
Ordnungssymbol (">=", "<=", "<", ">", "==") und einem Zahlenwert. Arithmetische
Ausdrücke sind anstatt der Zahlenwerte nicht zulässig. Diese dienen in DINGO—II
nur der Bequemlichkeit, leichten Lesbar— und Anderbarkeit. werden aber
letztendlich vollständig aus Konstanten gebildet und können daher schon im
Frontend ausgewertet werden. Quantifikationen in DINGO—II (das sind einfache
arithmetische Ausdrücke, Listen von alternativen Ausdrücken oder Intervalle)
müssen im Frontend in einfache Rümpfe übersetzt werden. Die Syntax entspricht
dabei der einer Kardinalitätsangabe.

DROOLY 38



Optional kann einer Wertbestimmung noch ein Attribut zugeordnet sein. Dieses
besteht aus den Schlüsselworten centered, eccentric, in current direction oder
against current direction.

Es gibt eine Sonderform der Wertbestimmung, die im Zusammenhang mit
PROPERTIES vom Typ dimension benutzt wird. Der Wertausdruck für eine solche
PROPERTY spezifiziert Werte für eine Folge vom Polygonkanten. Dabei wird jeder
Polygonkante ein Wert zugewiesen. Eine Wertbestimmung dieser Art beginnt mit dem

Schlüsselwort (asmmetric, gefolgt von der Liste von Wertspezifikationen, und wird
durch eine ")" abgeschlossen. —

Mithilfe solcher einfachen Rümpfe lassen sich Werte festlegen, die innerhalb
des gesamten statischen Kontextes einer Objektklasse Gültigkeit haben. Muß die
Gültigkeit in Abhängigkeit von Bedingungen für den geometrischen Kontext der
betroffenen Struktur/Strukturen eingeschränkt werden, bedient man sich bedingter
Wertausdrücke. Sie bestehen aus Bedingungen und Wertausdrücken, die bei Erfüllung
der Bedingung in Kraft treten.

\ cX<L.)r-.W

Eine Bedingung besteht allgemein aus einem booleschen Ausdruck mit forwards,
RELATIONS oder CLASSTESTS. Die FORWARDS(S und RELATIONS entsprechen syntak—
tisch den FORWARDS und RELATIONS, wie sie als Ubersetzungsprodukt von Design
Rule Köpfen entstehen. Der ihnen zugeordnete Wertausdruck stellt in diesem Fall
jedoch keine Forderung dar, sondern ist als Test zur Auswahl des then— oder %—
Zweiges zu verstehen. In DINGO—II wird dieser semantische Unterschied durch den
fehlenden ":" in Bedingungen angezeigt. In DROOLY gibt es keine syntaktische
Unterscheidung. Das Konstrukt des CLASSTESTS erlaubt es, Wertausdrücke in
Abhängigkeit von der Klasse eines Objektes und speziell von ihrer Belegung mit
aktuellen Parametern zu formulieren. Syntaktisch entspricht dieses Konstrukt der
Festtypdeklaration einer Unterstruktur, wird jedoch nicht durch das Schlüsselwort
(class, sondern durch (classtest eingeleitet.

Als Wertausdrücke im then— und im e_l_s_e—Zweig sind allgemeine (also
möglicherweise wieder bedingte) Wertausdrücke zulässig. Treten bedingte
Wertausdrücke geschachtelt auf, so wird ein else-Zweig dem jeweils innersten 3
zugeordnet. Will man von dieser Regelung abweichen, so kann man dies mit Hilfe

von Klammerungen erreichen. Im folgenden sind einige Beispiele für DROOLY—
Wertausdrücke gezeigt:

[>=21
"Der Wert soll größer oder gleich 2 sein"

[9L<1>=3<=6]
"Der Wert soll im geschlossenen Intervall [3,6] liegen"

[ if gate.poly width [ == 5 ]
then < 6
else > 7



]
"Falls die Breite des Polysilizium—Layers im Gate gleich 5 ist,
soll der Wert kleiner als 6 sein, andernfalls größer als 7"

DROOLY 39







[ i_f gr gate.poly width == 5 ]
gate.diff width [ > 7 ]
then == 15
% £ gate.diff width [ < 3 ]
then @ 3 == 17 == 18 == 19
else == 16

"Falls die Breite des Polysilizium—Layers im Gate entweder gleich
5 oder aber größer als 7 ist, soll der Wert gleich 15 sein.
Andernfalls, falls der Polysilizium—Layer im Gate schmaler als 3
ist, soll der Wert aus der Menge {17,18‚19) sein; sonst wird ein
Wert von 16 gefordert."

[ i_f g gate.poly width [ == 5 ]
. gate.diff width [ < 5 ]
then [ i_f gate.diff width [ < 3 ]
then @ 9; == 17 == 18 == 19
]

else -

]

"Falls die Poly—Breite im Gate entweder gleich 5 oder aber kleiner
als 5 ist, soll der Wert aus der Menge {17,18,19) sein, falls die
Diffusionsbreite auch noch kleiner als 3 ist. Ist weder die Poly—
Breite gleich 5 noch die Diff—Breite kleiner als 5, soll der Wert
gleich 16 sein"

(classdef gate (fpar diff )
param diff (class n_Diff ) (class p_Diff )



16

!

diff width [ i_f diff (classtest n_Diff )
% >= 3
@ >= 5
l
)

"Falls die Unterstruktur diff durch eine aktuelle
Parameterbelegung mit der Klasse n_Diff deklariert ist, soll die
Breite von diff größer als 3 sein, andernfalls größer als 5"
