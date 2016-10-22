# 6. Übersetzung DINGO-II nach DROOLY

Nachdem die Schnittstellen zwischen Frontend und Backend (Symboltabelle und
DROOLY—Zwischenformat) beschrieben sind, soll in diesem Abschnitt eine Anleitung
zur Übersetzung einer DINGO—II Datei in die resultierende Symboltabelle und die
DROOLY—Datei gegeben werden. Bei der Erzeugung der Symboltabelle werden nur die
für das Backend relevanten Teile betrachtet. Teile einer DROOLY-Beschreibung
werdert in Form: der Präprozessoreingabe angegeben. Dazu werden die gleichen
Konventionen verwandt wie bei der Beschreibung der DROOLY-Syntax. Das Frontend
erzeugt natürlich die im Anhang A aufgelisteten Token für Schlüsselworte, "@" mit
Symboltabellenindizes für (qualifizierte) Bezeichner und "#" mit Zahlenwert für
Zahlen und ausgewertete arithmetische Ausdrücke.

Übersetzung DINGO—II nach DROOLY 40



Eine Technologiespezifikation in DINGO—II beginnt mit dem Deklarationsteil.
Die Informationen aus dem Delarationsteil sind zwar für eine Applikation von
Bedeutung, nur wenige werden jedoch für die Übersetzung von der DROOLY—Datei in
die tabellarische Technologierepräsentation und zur Ausgabe von Fehlermeldungen
im Backend benötigt:

— Bezeichner der deklarierten Layer.

- Bezeichner von (dem System implizit bekannten oder benutzerdefinierten)
Geometrieklassen. -

Zu jedem deklarierten Layer wird eine Zeile der Form
layerdef IDENTIFIER

in die DROOLY-Ausgabe geschrieben. Die Layerattribute erscheinen nur in der
Symboltabelle, werden also nicht in die DROOLY-Ausgabe übernommen.

Es folgen die Objektdeklarationen. Für jedes Objekt wird ein neuer
Symboltabelleneintrag eingerichtet und Objekttyp, Objektbezeichner und Alias dort
eingetragen. Die Deklaration von Modulen, lokalen Modulen, Bauelementen und
Zellen beginnt im DROOLY-Format mit einer Zeile der Form

(classdef IDENTIFIER

Der Objekttyp wird nicht in die DROOLY—Datei übernommen. Zur Deklaration
von lokalen Modulen gibt es in DROOLY kein eigenes syntaktisches Konstrukt. Alle
Objekte werden global deklariert. Um ein Lokalmodul als solches kenntlich zu
machen, wird sein Objektbezeichner qualifiziert, d.h. es erhält die Bezeichner für

die umschließenden Objekte als Präfix seines Pfadnamens. Man betrachte dazu
folgendes Beispiel:

DINGO-II:

module A
using
aa of local module AA
local module AA
using
aaa of local module AAA
local module AAA

local module end
MM

module end

Übersetzung DINGO—II nach DROOLY 41



DROOLY:
(classdef A.AA.AAA
) . . .
(classdef A.AA
) art aaa (class A.AA.AAA )

(classdef A
@ aa Lola A-AA )
)

Durch die qualifizierten Bezeichner wird in DROOLY dieselbe hierarchische
Struktur wie in DINGO—II beschrieben. Zu beachten sind dabei jedoch zwei Punkte:

- Normalerweise werden Bezeichner innerhalb eines Objektes relativ zum
Objektbezeichner betrachtet. Die Unterstruktur aaa im lokalen Modul A.AA hat
also den absoluten Pfadnamen A.AA.aaa . Klassenbezeichner werden jedoch
nicht automatisch mit dem Präfix des Objektes versehen, in dessen Kontext sie
benutzt werden. Objektklassen können ja in DROOLY nur global deklariert
werden. Hinter dem Schlüsselwort (class müssen also immer absolute
Pfadbezeichner stehen. Fehlt der einleitende Punkt, wird er vom Backend
automatisch ergänzt.

— Die Bezeichner lokaler Module sind durch die globale Deklaration auch
außerhalb ihres statischen DINGO—II Kontextes bekannt. Das Backend würde
also auch die Verwendung des Lokalmodulbezeichners außerhalb dieses
statischen Kontextes akzeptieren. Bei Übersetzungsprodukten von DINGO—II
Beschreibungen kann dieser Fall aber nicht auftreten.

Standardbauelementen und Standardzellen wird bei ihrer Deklaration eine
Bauelement— bzw. Zellenzuweisung zur Deklaration der Oberklasse mitgegeben. Die
äquivalente Deklaration von Standardobjekten hat in DROOLY folgendes Format:

(stddef IDENTIFIER (class IDENTIFIER { INTEGER ) ")"

Darin ist der erste Bezeichner der Bezeichner des Standardobjektes. Der zweite
Bezeichner, zusammen mit der Liste aktueller Parameter, bezeichnet das
zugewiesene Bauelement oder die zugewiesene Zelle. Im Gegensatz zu DINGO-II
werden in DROOLY aktuelle Parameter nicht durch den jeweiligen Klassenbezeichner
u.U. zusammen mit dessen aktuellen Parametern (...) angegeben, sondern durch
einen Positionsindex der Liste alternativer Klassen in der Parametertypdeklaration
des formalen Parameters. Die alternativen Klassen werden dazu beginnend bei 1
durchnummeriert. Man betrachte folgendes Beispiel:

Übersetzung DINGO—II nach DROOLY 42



DINGO-II:

device CMOS_Transistor ( diffusion, gate )
usi_ng
diffusion one of ( layer n_Diff, layer p_Diff )
gate M (
module CMOS_Gate ( n_Diff, p_Well )
module CMOS_Gate ( p_Diff, n_Well )

device end

standard device Mini_p_Transistor
i_s
CMOS_Transistor (p_Diff, CMOS_Gate (p_Diff, n_Well))

DROOLY:

(ciassdef CMOS_Transistor f ar diffusion gate )
param diffusion (class n_Diff ) class p_Diff )
param gate (class CMOS_Gate 1 2 )

(class CMOS Gate 2 1 )

...

)

(stddef Mini_p_Transistor
gelass CMOS_Transistor 2 2 )

In diesem Beispiel sieht man auch schon das Übersetzungsprodukt einer Liste
formaler Parameter. Ein Objektkopf mit formalen Parametern hat in DROOLY
allgemein folgendes Format:

(ciassdef IDENTIFIER (fgar [+ IDENTIFIER +) ")"

Die Bezeichner in der Parameterliste bezeichnen jede parametertypdeklarierte
Unterstruktur des Objektes in der Reihenfolge ihrer Deklaration.

Auf die Angabe formaler Parameter folgt in DINGO—II die Deklaration der
benutzten Unterstrukturen. Diese Reihenfolge wird auch in DROOLY beibehalten.
Unspezifische und Festtypdeklarationen werden in DROOLY durch die Verwendung
von Pseudoobjekten zusammengefaßt. Jedes dieser Pseudoobjekte NOLAYER,
ANYLAYER, ANYMODULE, ANYDEVICE und ANYCELL hat einen eigenen
Symboltabelleneintrag.

Eine Festtypdeklaration sieht in DROOLY folgendermaßen aus:

part IDENTIFIER (class IDENTIFIER { INTEGER ) ")"

Bei einer Deklaration mit unspezifischem Typ wird der Bezeichner des
jeweiligen Pseudoobjektes als Klassenbezeichner eingesetzt. Aktuelle Parameter

werden auch hier einfach durch Angabe der Positionen in der Liste alternativer
Klassen einer parametertypdeklarierten Unterstruktur übergeben.

Übersetzung DINGO-II nach DROOLY 43



Parametertypdeklarationen haben in DROOLY folgendes Format:
param IDENTIFIER [+ (class IDENTIFIER { INTEGER | ")" +}

Parametertypdeklarationen werden durch ein spezielles Schlüsselwort param
eingeleitet. Fiir die Liste der Klassen werden die einzelnen Klassen u.U. mit

aktuellen Parametern aufgelistet, jeweils eingeleitet durch das Schlüsselwort (class
und beendet durch eine ")".

Mengentypdeklarationen schließlich haben in DROOLY folgendes Format:

s_et; u[n card_body u]n
IDENTIFIER (class IDENTIFIER [ INTEGER ) ")"

Wieder gibt es ein spezielles Schlüsselwort s_et zur Einleitung einer
Mengentypdeklaration. In eckigen Klammern erfolgt die Angabe eines erlaubten
Kardinalitätsbereiches. Wird der Kardinalitätsbereich nicht weiter eingeschränkt,
müssen demnach die leeren eckigen Klammern in der DROOLY—Datei erscheinen.
Syntaktisch entspricht eine Kardinalitätsangabe einem Wertausdruck, wie er in
quantitativen Beschreibungen verwandt wird. Dabei sind jedoch nur folgende
Konstruktionen zulässig:

- Arithmetische Ausdrücke hinter dem Schlüsselwort exactly werden in einen
einfachen Wertausdruck übersetzt:



DINGO—II: cardinality exactly 4 + 8
DROOLY: [ == 12 ]

- Alternative Listen von Werten werden in einen durch 3 verknüpften
Wertausdruck in Präfixnotation übersetzt:

% cardinality one of ( 2, 4, 5 )
DROOLY: [ £ „_, == == 4 == 6 ]

— Intervallangaben werden in einen durch and verknüpften Wertausdruck
übersetzt:

DINGO—II: cardinality range [ 5, 10 ]
DROOLY: [ and >= 5 (= 10 ]

- Andere Formen werden vom Compiler—Backend syntaktisch erkannt und
zurückgewiesen.

Mit der Deklaration aller Unterstrukturen ist der Objektkopf abgeschlossen. In
DINGO—II können nun lokale Module definiert werden, deren Bezeichner nur
innerhalb des statischen Kontextes einer Objektdefinition bekannt ist. In DROOLY
existiert kein äquivalentes syntaktisches Konstrukt. Stattdessen werden alle
Objekte global definiert, wie schon weiter oben vorgeführt. Dieser Punkt dürfte bei
der Übersetzung von DINGO—II nach DROOLY die größten Schwierigkeiten bereiten.
Ordnet man jedoch im Frontend die "Codegenerierungs"—Semantikregeln erst nach
Abarbeitung aller Lokalmodule an, kann man unter Ausnutzung des NEMO—
Wertestacks auf eine explizite Zwischenspeicherung der Informationen aus dem
Objektkopf verzichten. (5)



(5) Das Problem der Entllechtung verschachtelter Objektdeﬁnitionen existiert auch bei der Übersetzung konventioneller Programmiersprachen. sie ab. bei der
Übersetzung von PASCAL in das maschinennähere C, welches keine geschachtelten Prozedurdeklarationen kennt.

Übersetzung DINGO—II nach DROOLY ' 44



Es folgt der Beschreibungsteil, der in DINGO—II in die Topologiebeschreibung
und die Design Rules unterteilt wird. Topologieanweisungen bestehen entweder aus
Geometrieeinschränkungen. ldentifikationen oder aus Topologierelationen. Während
für die ersten beiden Formen äquivalente Konstrukte in DROOLY existieren, teilen
sich die letzteren eine gemeinsame Syntax mit den zweistelligen Design Rules.
Geometrieeinschränkungen mit einer Liste von Objekten müssen bei der Übersetzung
nach DROOLY in einzelne Anweisungen aufgespalten werden:

DINGO—II: shape o_f polyline, diffline i_s manhattan g poly_45
DROOLY: polyline shape manhattan

polyline shape poly_45
diffline shape manhattan
diffline shape poly_45

Mehrfache Geometrieeinschränkungen auf demselben Objekt gelten als implizit
o_r—verknüpft. Identifikationsanweisungen bestehen in DROOLY aus einem
Schlüsselwort (merge, gefolgt von der Liste zu identifizierender Objekte,
abgeschlossen durch eine ")":

DINGO—II: Contl.diff i_s Gatel.diff & Gate2.diff
i_s Cont2.diff i_s Diff
DROOLY: (merge Contl.diff Gatel.diff Gate2.diff

Cont2.diff Diff )

Topologierelationen werden in DINGO—II als beliebig geklammerter Ausdruck mit
qualifizierten Bezeichnern als Operanden und den Operatoren cross, overlap,
enclose, _ip, touch und notouch in Infix—Schreibweise notiert. Durch die Wahl einer
Präfix—Notation können die Klammern innerhalb solcher Ausdrücke in DROOLY
entfallen. In der Tabelle auf Seite 36 sind die DROOLY—Entsprechungen der DINGO—
II Operatoren für Topologie— und Design Rule Beschreibungen aufgeführt. Wie in
DINGO—II, so können auch in DROOLY Topologie— und Design Rule Ausdrücke
quantifiziert sein, falls Operanden von einem Mengentyp auftreten. Die Quantoren
sind wie in DINGO—II a_ll, some, @y und @. Das Beispiel gibt einen komplexen
Topologieausdruck in DINGO—II und seine Entsprechung in DROOLY an. Die
Abbildung zeigt eine mögliche geometrische Ausprägung dazu. Dabei gilt









A = al, a2, a3 )
B = ( b )
C = { C1, C2, c3 ) :
DINGO-II:
some A: A C:
(C i_ B) overlap ( (A enclose A) _i__ B )
DROOLY:
some A A C
overlap [ ]
indist [ ] C B
indist [ ]
nclose [ ] A A

übersetzung umso—n nach DROOLY ' 45





—@-> indist „
—@-— wm e«—
—@—> enclose. /“ ‚.*/“„?

















Abb. 6.1: Mögliche Ausprägung eines komplexen Topologieausdruckes

Der letzte Teil einer Objektbeschreibung in DINGO—II umfaßt die internen und
externen Design Rules. Deren DINGO—II Syntax ist reichlich umfangreich und
unterscheidet sich stark von der Syntax des Topologiebeschreibungsteils. Da aber
die Informationen eines Design Rule Kopfes ähnlich zu denen eines
Topologieausdruckes sind, wurde in DROOLY die Syntax von Design Rules und
Topologiebeschreibungen zusammengefaßt. Haupthindernis dabei ist die teilweise
unterschiedliche Bezeichnung der Operatoren. Durch die Zuordnung der Tabelle auf
Seite 36 ist jedoch eine Vereinheitlichung möglich. Es bleibt die Syntax für
einstellige Design Rules und für den Design Rule Rumpf; für beide Konstrukte gibt
es im Topologieteil keine Entsprechung.

Einstellige Design Rules und Geometrieeinschränkungen werden in DROOLY zu
forwards zusammengefaßt. Beide beschreiben eine Eigenschaft (property) eines
einzelnen Objektes. Als Objekt wird dabei entweder implizit das den statischen
Kontext bestimmende Objekt angenommen (der selbe Effekt wird durch Verwendung
des Schlüsselwortes ﬁals Bezeichner erreicht) oder es kann explizit durch einen

(qualifizierten) Bezeichner spezifiziert sein. Drei einfache Beispiele für forwards in
DROOLY sind:

Gate.poly width [ < 5 ]
Metal_line shape poly_45
self length [ @ == 12 == 10 ]

Wertausdrücke bei PROPERTIES, RELATIONS und auch bei einer Kardinalitäts—
angabe werden in DROOLY grundsätzlich in eckige Klammern eingeschlossen. Wird
bei RELATIONS oder Mengentypdeklarationen kein Wertausdruck benötigt, kann diese
Klammernpaar auch leer sein (als Lexem müssen sie dann durch mindestens ein
Trennzeichen voneinander getrennt sein !). Wertausdrücke sind entweder einfach
oder bedingt. Einfache Wertausdrücke bestehen aus einem mit gi_d und/oder @
verknüpften booleschen Ausdruck in Präfix—Notation. Die Operanden eines solchen
Ausdruckes sind (u.U. attributierte) Wertbestimmungen, bestehend jeweils aus
Ordnungssymbol und Zahlenwert. Bei PROPERTIES vom Typ dimension darf auch eine
Liste von Wertbestimmungen stehen, eingeleitet durch das Schlüsselwort (asymmetric
und abgeschlossen durch eine ")". Als Attribute sind die aus DINGO—II
übernommenen Schlüsselworte centered, eccentric, in current direction und
against current direction zulässig.

Bedingte Wertausdrücke haben wie in DINGO—II eine Bedingung und zwei
Zweige mit u.U. wieder bedingten Wertausdrücken. Die einzelnen Bestandteile
werden durch die drei Schlüsselworte _i_f, then und _e3 voneinander getrennt. Zur
Umgehung der automatischen Zuordnung eines giga_-Zweiges zum letzten _i_f können
die beiden Zweige optional in eckige Klammern eingeschlossen werden. Eine
Bedingung kann ein boolescher Ausdrück mit gp_Q und/oder pr; als Operatoren sein.

Übersetzung DINGO-II nach DROOLY ' 46



Für die Operanden werden gewöhnliche forwards, RELATIONS oder ein spezielles
Konstrukt, der CLASSTEST eingesetzt. Die beim Einsatz als Bedingung
obligatorischen Wertausdrücke dienen hier nicht zur Formulierung einer Forderung.
sondern bilden die Bedingung. nach der entweder der then— oder der @-Zweig
ausgewählt werden. Ein CLASSTEST testet die Klasse, speziell die Belegung der
Parameter eines Objektes. Die Syntax ist identisch mit einer Festtypdeklaration,
nur das statt des Schlüsselwortes (class hier (classtest steht. Die folgende
Abbildung zeigt einige Beispiele für Design Rules in DINGO—II zusammen mit ihren
Entsorechungen in DROOLY:

DINGO—II:
dimension o_f Contact. cont_Cut:
== 500 p;
assnpmetric ( == 375, == 600, == 375, == 600 )

DROOLY:

Contact . cont_Cut dimension
[ @ == 500

(assmetric == 375 == 600 == 375 == 600 )
]

DINGO—II:

distance o_f P_A_Cont.cont_Cut from A_Wire:
>= 3 g
in current direction >= 2

DROOLY:

ist
[ @ >= 3

in current direction >= 2
] P_A_Cont.cont_Cut A_Wire



DINGO-II:
i_f voltage o_f A_Wire: == Vdd
then >= 3
DROOLY:
A_Wire width
[ i_f A_Wire voltage [ == 12 ]
then >= 3



]

DINGO-II:

distance 91 P_Wire from A_Wire:
ä width o_f A_Wire range [S, 10] and
width g P_Wire > 10







then >=

% g w1dth o_f A Wire > 10
then >= 7
sig >= 3

Übersetzung DINGO—II nach DROOLY 47



DROOLY:

[ if an A_Wire width [ @ >= 5 <= 10 ]
P_Wire width [ > 10 ]



then >= 5

% ! A_Wire width [ > 10 ]
then >= 7
else >= 3

] P_Wire A_Wire
