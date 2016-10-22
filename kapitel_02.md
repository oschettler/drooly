2. Die Technologiespezifikation in der Literatur

2. 1 Bisherige Arbeiten

Die einfachsten datengetriebenen Ansätze zur Speicherung von
Technologiedaten für Layout-Applikationen finden sich in der Literatur in Form
von sog. Runsets. Benötigte Steuerinformationen werden in einer externen Datei in
einem Format abgelegt, welches direkt vom jeweiligen Programm interpretiert werden
kann. Ein typischer Vertreter solcher Runsets liegt z.B. mit der Design—Rule—
Tabelle im Design Rule Checker (DRG) MAGIC [Ous 84] vor. In dieser Tabelle werden
Design—Rules kantenorientiert aus folgenden Feldern aufgebaut:

Die Technologiespezifikation 8















!. Typt } Meterlel auf der I. Seite der Kerne [
2. Typ2 ' Meterlel Auf der 2. Seite der Kante
3 d Distanz. innerhllb derer die zweite
‘ Seite zu uberprulen let
‘ erleubte Liete aller Metermlien‚ die um
' Leyer überprüften Gebiet erleubt lind
5 'Corner'— Lille der Meterielien, die eine
' Typen 'Cornet Extenluon' nötig machen
6. g;renn'erion Große der Comer Exteneion











MAGIC stellt spezielle Makros zur Verfügung (SPACING, WIDTH). die das
Erstellen der Design—Rule-Tabelle erleichtern.

Durch Zeile 1 bis 4 werden die Regeln der einfachsten Form beschrieben:





Abb. 2.1: Einfache kantenorientierte Design Rules in MAGIC

Der zu überprüfende Bereich wird evtl. an den Ecken um die Corner Extension
erweitert:

Corner Extension

l

; erlaubte Typen




Corner Types SI
|
|
'



Abb. 2.2: Corner Design Rules

Ein weiterer Vertreter einfacher Runsets wird von W. Schiele in [Schi 851
beschrieben. Schiele benutzt eine Instruktionsliste für die Steuerung eines
Programms zur Adaption von Design Rules. Dieses Programm kann ebenso wie MAGIC

Die Technologiespezifikation 9



nur Manhattan- (d.h. Iso—orientierte) Geometrien verarbeiten. Schiele beschreibt
Design Rules durch Ungleichungen vom Typ

Zi"Zj>C‚

worin Zi und ZJ x— oder y-Koordinaten der Polygonkanten i und j sind. Im
Gegensatz zum Ansatz in MAGIC liegt die Instruktionsliste nicht als Tabelle vor,
sondern als Programm in einer formalen Sprache, die durch Syntax—Regeln
spezifiziert ist. Die Syntax der Sprache ist einfach gehalten und verwendet ein
etwas kryptisches Format. Die einzelnen Instruktionen werden eine nach der
anderen eingelesen und direkt interpretiert:

lnumhtianm
M: Melt V: Overeize
G: Greph generation U: Underei1e
A: And Internet

|:
NI And "°l E: Externe!
O: Or S: Seele
W: Width
M l I met! Dittueion
M 2 | malt Buried
M 3 l mell Poly
M 5 ! melt Contact
M 6 l melt Metal
M 9 [ melt lmplenteu'on
G ! greph generetron
| Minimum |.y„ man. | Trennluor und wire Scnlinq
||;2 A13. 21 [Diff und Poty
| 2; 4 N 21 2.22 ( And not Bund-° Trenelltoru
S 22: 0.4 I Transistor eceling (5'5 —- 2'2)
| 9; 4 W !: 0.4 ! Wire wldth ecellnq

Abb. 2.3: Beispiel für eine Instruktionsliste zur Design Rule Anpassung

Zusammenfassend kann man festhalten, daß die einfachen Runsets zur
Steuerung einer speziellen Applikation entworfen sind und nur die Informationen
zur Verfügung stellen, die zur Bearbeitung der jeweiligen Aufgabe erforderlich sind.
Beim Wechsel der zugrundeliegenden Technologie muß jedes einzelne Runset
gesondert angepaßt werden. Dieser Vorgang ist natürlich zeitintensiv und
fehleranfällig.

In integrierten CAD—Systemen wird daher vielfach versucht, eine gemeinsame
Basis für technologieabhängige Daten anzulegen. Alle beteiligten Applikationen
greifen über ein geeignetes Interface auf die Technologiedaten zu.

Ein CAD—System für den physikalischen IC—Entwurf enthält Applikationen und
Tools mit unterschiedlichen Aufgaben, die sie auf unterschiedlichen
Abstraktionsebenen erledigen. Demzufolge gibt es verschiedene Arten von
Technologieabhängigkeit. Kung—Chao Chu nennt in seinem vielbeachteten Artikel
[Chu 85] vier Kategorien von Technologieabhängigkeit:

Die Technologiespezifikation 10



Abhängigkeit von Werten ist die offensichtlichste Art von Technologieabhängigkeit.
In diese Kategorie fallen z.B. eingebaute Annahmen über die maximal erlaubte
Anzahl von Maskenlayern. minimale Leitungsweiten und Abstände, Annahmen
über die verwendeten Maßeinheiten ebenso wie nicht direkt von einer
bestimmten Technologie abhängige feste Voreinstellungen wie maximale
Matrixgröße oder die Auflösung numerischer Werte.

Abhängigkeit von Schlüsselworten macht bei Technologieänderungen ebenfalls eine
Neu—Compilation des betroffenen Programmes erforderlich. Ein Beispiel wäre
eine vorgegebene Menge von Layerbezeichnern (z.B. "n—diff", "p—diff", "poly",
"alu"), die in einem Vektor im Programmcode definiert sind. Soll bei einem
Technologiewechsel ein neuer Layer hinzugenommen werden (ZB statt "alu"
jetzt "metl" und "metl"), muß nicht nur der definierende Vektor neu übersetzt
werden, sondern alle Stellen im Programm, die sich auf die alten
Schlüsselworte bezogen.

Abhängigkeit vom Format liegt z.B. beim oben beschriebenen MAGIC—Extraktor vor.
Jedes Feld der Design—Rule—Tabelle hat eine vordefirüerte Bedeutung. MAGIC
liest die einzelnen Zeilen der Tabelle, zerlegt sie in die einzelnen Felder und
speichert sie in einer internen Datenstruktur. Wird die Anordnung der Felder
verändert, so ist der Code zum Einlesen der Tabelle unbrauchbar.

Abhängigkeit von der Modellierung. Ein physikalisches IC-Entwurfssystem benötigt
eine abstraktes Modell der zu bearbeitenden Layoutobjekte. So könnte ein
Layouteditor z.B. Kreise und beliebige Winkel im Layout durch
Treppenfunktionen modellieren. Die geignete Wahl eines Modelles ermöglicht
eine effiziente Erledigung der gestellten Aufgabe, indem es nur die relevanten
Aspekte der Realität beschreibt.

Chu weist in seinem Artikel auf eine weitere Klassifizierungsmöglichkeit von
Technologiespezifikationen hin. Wie schon an den oben vorgestellten Runsets des
Extraktors und des Design—Rule—Adaptors erkennbar, gibt es die Möglichkeiten,
Technologien in einem tabellarischen Format oder in einer syntaktischen Form zu
beschreiben. Die tabellarische Form ist besonders leicht direkt von einem Programm
zu interpretieren. Es besteht der genannte Nachteil einer Formatabhängigkeit.
Dieser kann jedoch durch eine definerte Schnittstelle zwischen Daten und Anwender
der Daten gemildert werden. Die Daten bilden zusammen mit den in der
Schnittstelle definierten Zugriffsoperationen einen abstrakten Datentyp mit den
bekannten Vorteilen.

Die syntaktische Form erlaubt die übersichtliche Formulierung komplexerer
Zusammenhänge. Sie muß jedoch allgemein zunächst durch einen
Übersetzungsvorgang für die Benutzung durch ein Programm aufbereitet werden. Ein
wichtiger Vorteil dieser Form ist ihre Eignung zum Austausch von
Technologiespezifikationen z.B. zwischen Prozeßentwickler und Designer. Speziell zu
diesem Zweck wurde das EDIF-Format [EDIF 85] entwickelt. In einer LISP—artigen
Syntax lassen sich hierin sehr viele Aspekte des IC—Designprozesses beschreiben.

Chu beschreibt eine syntaktische Erweiterung von EDIF. Seine Erweiterung
konzentriert sich auf die Unterstützung von Layout—Werkzeugen (Layout—Editoren,
Kompaktoren, Router, DRG, Extraktoren. Plotter, Maskengeneratoren). Er erlaubt nur
die Beschreibung von Manhattan—Geometrien und gibt drei Objektarten fest vor:
(zusammengesetzte) LAYER. VIA und TRANSISTOR. Die Topologie dieser Objektarten
ist festgelegt und wird mithilfe der Operatoren minWIDTH, minAREA,
minSEPARATIONY und minOVERHANG plus Maßangabe quantifiziert.

Die Technologiespezifikation ' 1 1



Ausnahmeregelungen von den global geltenden Mindestmaßen innerhalb von VIAs
oder TRANSISTORen sind eingeschränkt möglich. Zusätzlich zu geometrischen
Maßangaben können jedoch auch elektrische Größen verwandt werden.

Ph. Smith und S. Daniel verwenden im VIVID—System [Smi 85] zur
Technologiebindung eine Reihe von Dateien. Dieses Master-Technologiefile—System
(MTF-System) unterstützt Tools wie einen symbolischen Schaltkreiseditor, einen
Schaltkreis—Extraktor, einen Simulator, einen Maskenlevel-Übersetzer und einen
Kompaktor. Das gesamte System ist auf die Verarbeitung von unterschiedlichen
MCS-Technologien (MOSIS3—CMOS, MOSIS4-NMOS) ausgelegt. Die einzelnen Dateien
enthalten technologieabhängige Informationen aus verschiedenen Bereichen: Layer,
Transistoren und Kontakte, Graphische Attribute und Shapes, Virtuelle Shapes,
Menüpunkte, Symbolisch—nach-Maskenlevel-Übersetzung, elektrische und physi—
kalische Konstanten.

Formuliert werden diese" Informationen teilweise in einer syntaktisch an die
Programmiersprache C angelehnten Daten—Beschreibungssprache. Kompliziertere
Sachverhalte werden direkt in C unter Zuhilfenahme von speziellen
Prozeduraufrufen beschrieben. Beide Formen werden in normale Objektdateien
übersetzt. Die Applikationen laden sie allerdings erst zur Laufzeit (sog.
dynamisches Binden) und müssen damit bei Technologieänderungen nicht neu
compiliert werden.

Einen sehr umfassenden Ansatz zur Speicherung von technologieabhängigen
Daten benutzen Aude und Kahn im SIDESMAN System [And 86]. Zur Beschreibung
von Design Rules dient hier die Sprache DRDL (Design Rule Description Language),
die im Gegensatz zu EDIF sehr an das natürliche Englisch angelehnt ist. Design-
Rule—Beschreibungen werden mit einem Compiler in eine interne Form übersetzt.
Eine Prozedurbibliothek ermöglicht den Applikationen einen kontrollierten Zugriff
auf die gespeicherten Informationen. Diese erhalten verschiedene Attribute, um die
Zugriffsmöglichkeit von Applikationen zu steuern. Es wird dabei unterschieden
zwischen toolbezogenen Attributen, designbereichbezogenen Attributen und
abstraktionsebenen—bezogenen Attributen.

Zur weiteren Strukturierung der gespeicherten Informationen sind einzelne
Technologiespezifikationen baumartig angeordnet.

Silicon
/\\
MCS Bipolnr
c"'°s\ "MCS [ZL/sct\rn
CMOS CMOS CMOS ECLIOk ECLIOOK STTL

n -well p—well twin

Abb. 2.4: Technologiebaum nach [And 861

Ähnlich der Objekthierarchie in objektorientierten Programmiersprachen wie
Smalltalk nimmt die Spezialisierung der Spezifikationen und damit der Grad von
Technologieabhängigkelt von der Wurzel zu den Blättern zu. Design Rules werden

Die Technologiespezifikation ' 1 2



von einer Technologie an ihre Söhne vererbt. Es ist jedoch auch möglich, ererbte
Design Rules mit eigenen Regeln zu überschreiben.

2.2 Bestandteile einer Technologiespezifikation

Im vorigen Abschnitt wurden verschiedene Ansätze für Technologiespezi—
fikationen vorgestellt. Aude und Kahn identifizieren zur Einteilung der
verschiedenen Aspekte einer Technologiespezifikation acht Teilbereiche:

Zeitverhalten
Belastbarkeit

Layout

Verlustwärme
Anschließbarkeit
Testbarkeit
Interface—Kompatibilität
Abschlußstrukturen

G>\IO°UI>$>CJNH

Für ein physikalisches Entwurfssystem, wie es in dieser Arbeit betrachtet
werden soll, ist besonders der Punkt "3. Layout" von Bedeutung. In einer
vollständigen Technologiespezifikation für ein physikalisches Entwurfssystem sollen
alle technologieabhängigen Informationen enthalten sein, die die einzelnen
Applikationen zur Erledigung ihrer Aufgaben benötigen, bei gleichzeitiger Wahrung
ihrer Technologieinvarianz. Die einzelnen Bestandteile einer solchen Spezifikation
sind:

Prozeßbezeichner. Jede Technologiespezifikation muß sich eindeutig einer
bestimmten Technologie zuordnen lassen, um auch die gleichzeitige Verwendung
mehrerer Technologien verwalten zu können.

Maskenbezeichner. Zur Vermeidung von Schlüsselwortabhängigkeit dürfen die
Applikationen keine Annahmen über die Bezeichner erlaubter Maskenlayer
machen. Zu jedem Layer werden weiter Informationen über seinen Bezeichner in
der externen Maskendatei. über seine Darstellung (Farbe, Schraffur) in einem
interaktiven Layouteditor sowie evtl. sein Ursprung aus einer booleschen
Verknüpfung von primitiven Layern benötigt. Der genaue Umfang der
Layerattribute ist dabei stark applikationsabhängig.

Elektrische Konstanten. Komplexe Design Rules lassen sich manchmal nur in
Abhängigkeit von elektrischen Konstanten ausreichend spezifizieren. Zu diesem

Zweck müssen die möglichen Werte elektrischer Konstanten mit ihren Einheiten
definiert werden.

Erlaubte Geometrieklassen. Häufig schränken Layout—Werkzeuge die möglichen
Geometrieklassen (z.B. Manhattan, Polygone mit iso—orientierten und 45°-
Kanten usw.) in einem Layout ein, um besonders effizient arbeiten zu können.
Zur Überprüfung eines Layouts auf die Einhaltung solcher Einschränkungen,
u.U. unter Berücksichtigung eines besonderen Bauteil—Kontextes, muß die
explizite Formulierung von Geometrieklassen möglich sein.

‚(es

Die Technologiespezifikation ' 13



Topologie von erlaubten Bauteilen. Um die Lage eines Bauteils in einem Layout
bestimmen zu können, benötigt ein Layout—Extraktor eine Beschreibung der
möglichen Ausprägungen dieses Bauteils. Dabei kommt es für die Funktionalität
des Bauteils nicht auf die exakt quantifizierten Abmessungen des Bauteils an,
sondern nur auf die relative Lage seiner Komponenten zueinander, auf seine
Topologie also. Es ist daher sinnvoll, die Topologiebeschreibung eines Bauteils
von quantitativen geometrischen Restriktionen zu trennen.

Geometrische Restriktionen. Selbst in einfachen Runsets bilden die geometrischen
Restriktionen den Schwerpunkt der externen Technologieinformation. Zur
Erzielung einer vollständigen Technologiespezifikation ist es allerdings
erforderlich, auch kontextabhängige geometrische Restriktionen beschreiben zu
können. Der Kontext kann dabei die Topologie eines Bauteils sein oder auch

, bestimmte elektrische Gegebenheiten auf den betroffenen Objekten.

2.3 Sprachphilosophie

Im Abschnitt 2.1 wurde deutlich gemacht, daß es sehr unterschiedliche
Ansätze zur Formulierung von Technologiespezifikationen gibt. Besonders geeignet
im Sinne von Handhabbarkeit ist eine textuelle Beschreibung. deren Syntax durch
eine formale Grammatik definert wird. Zur einfachen Konstruktion eines Übersetzers
sollte dabei beachtet werden, daß die Grammatik der Technologie—
Spezifikationssprache vom LALR(1)—Typ ist, da für diesen Typ leistungsfähige
Parser—Generatoren existieren [NEMO].

Der Sprachumfang muß groß genug sein, um eine Technologie in allen ihren für
den physikalischen Entwurf relevanten Aspekten beschreiben zu können, ohne
irgend welche impliziten Annahmen über erlaubte Geometrien zu machen, d.h. die
Technologieinvarianz der Sprache muß gewährleistet sein.

Die Syntax soll gut lesbar sein und nur wenige, aber durchgängige Konzepte
zur Beschreibung enthalten. Eine deskriptive, an natürlichsprachliche
Konstruktionen angelehnte Form bietet sich an. Für die Beschreibung quantitativer
Zusammenhänge kann eine an konventionelle Programmiersprachen wie PASCAL oder
C angelehnte Syntax verwandt werden. Heute kann bei Entwicklern von
Technologien und Layouts die Kenntnis solcher Sprachen vorausgesetzt werden.

Die Beschreibungsmittel der Sprache müssen sich an die Gegebenheiten des zu
unterstützenden CAD—Systems anpassen. Da im folgenden das IMAGE—System
zugrunde gelegt wird, muß die verwandte Technologie-Spezifikationssprache das
semantische Datenmodell in der IMAGE—Datenhaltung unterstützen. Darin wird von
einer objektbezogenen Layoutbearbeitung ausgegangen. Das Layout enthält nur
erlaubte Instanzen von Bauteil-Prototypen. Die Beschreibung der Topologie solcher
Prototypen zusammen mit den auf sie bezogenen Design Rules bilden den
Schwerpunkt der Beschreibung einer Technologie für das IMAGE-System.

Die Technologiespezifikation 14



2.4 Integration in ein CAD—System

Technologien werden im IMAGE-System in der speziell dafür einwickelten
Beschreibungssprache DINGO—II spezifiziert. DINGO—II erfüllt die genannten
Anforderungen an eine solche Sprache. In der folgenden Abbildung wird die
Technologieverarbeitung im IMAGE—System noch einmal detailierter dargestellt:



. /‚—\
umso || \ ." \ .
Technologie- ) ./ M;::‘:„" _ ]
Spezifikatton ‘ /
ADDICTION \T/
, v v
f—‘_'—\

Compiler Frontend )

DROOLY
Compiler Beckend
. \ }

if“?














Ob|ecth Syme J









xl







. Sementik
L Technologie
] Datenhaltung ]
‘K Applikationuintertece \ '
. A ___,'
( Appliketionen )



Abb. 2.5: Die Technologieverarbeitung in IMAGE

Die Syntaxdefinition von DINGO—II liegt in Form einer LALR(I)—Grammatik vor,
die als Eingabe für den Parser—Generator NEMO dient. Der erzeugte Parser bildet
das Frontend eines Technologie—Compilers, der die Technologiespezifikation in zwei
Phasen in eine interne Darstellung übersetzt. Das Frontend erzeugt aus dem
DINGO—II-Programm eine Symboltabelle und ein textuelles Zwischenformat in der
Sprache DROOLY ("Devices 3epresented in Object grionted Lyrics"). Die Syntax
dieser Zwischensprache ist ebenfalls durch eine LALR(I)—Grammatik definert. Das
Compiler—Backend erzeugt aus dem DROOLY-Programm das gewünschte Zielformat,
die interne Form der Technologiespezifikation. Die interne tabellarische Form
besteht aus drei Tabellen (Objekttabelle, Ausdruckstabelle und Symboltabelle) und
einer Bibliothek von Zugriffsoperationen. Über die Zugriffsoperationen können
Applikationen unabhängig von der internen Repräsentation der Daten alle
benötigten Technologieinformationen zu der beschriebenen Technologie erhalten.
