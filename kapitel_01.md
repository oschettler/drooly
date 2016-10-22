# 1. Einleitung

Mit dem Entstehen wirtschaftlich einsetzbarer Computer wurde zu Beginn der
60er Jahre eine rasende Entwicklung der Digitaltechnik eingeleitet. Anfang der
70er Jahre war man so weit. ganze Subsysteme von mehreren tausend Transistoren
auf einem Chip unterbringen zu können (Abb. 1.1). Um dem Konflikt aus hohen
Entwicklungskosten bei kleinen Produktionsstückzahlen zu entrinnen. wurden
vielfach einsetzbare Standard—Bausteine entwickelt, deren bekanntester Vertreter
schließlich der Mikroprozessor wurde.

xou ——<omalenly oouoles
every year




SVLSI

i

VLSI
IOOK

( omponenls pn IC (CMOS)

5
x

15!

IK
77 M 76 75 80 82 BL 56 58 90
Irma

\ Inlrvmlﬂl (|rﬂnl mmhtlilr "rm/1
—»—- Memory
. C uuom

Abb. 1.1: Zahl der Komponenten pro IC gegen Jahr der Einführung [Era 85]

Steigende Ansprüche an Kompaktheit. Zuverlässigkeit. Leistungsaufnahme und
Kostenverringerung führten bei vielen Anwendern zu dem'Wunsch, ihre Schaltungen
auf einem eigenen Chip zu integrieren. Das ungünstige Verhaltnis aus
Entwicklungskosten zu Produktionsstückzahlen solcher kundenspezifischer Chips
führte allerdings zu unvertretbar hohen Stückkosten.

Aus einer fortschreitenden Formalisierung und damit möglichen
Automatisierung des Entwurfsprozesses integrierter Schaltungen entstanden zu
Beginn der 80er Jahre IC-Designsysteme. Diese Systeme erlauben durch weitgehende
Standardisierung des Entwurfsprozesses den kostengünstigen Entwurf
kundenspezifischer Chips mittlerer Größe bei mäßigen Anforderungen an Kompaktheit
und Schnelligkeit des Entwurfs [Hör 86].

## 1.1 Der IC—Entwurfsprozeß

Das Ziel des Entwurfs integrierter Schaltungen ist die schrittweise Erstellung
eines vollständigen Satzes von Fertigungsunterlagen, die beim nachfolgenden
Produktionsprozeß eine korrekte Implementierung des Entwurfs garantieren.

Der Entwurf kundenspezifischer Chips (ASICS) mit mehreren Millionen
elektrischer Komponenten bei gleichzeitig hoher Komplexität der implementierten
' Schaltungen entzieht sich der Verwendung von Standardentwurfssystemen. Um den

'?

Einleitung 2



Rechnereinsatz trotzdem zu ermöglichen, bedarf es einer geeigneten Modellierung

des Entwurfsprozesses. Bewährt hat sich zu diesem Zweck das Y-Modell nach Walker
und Thomas [Wal 85]:

iunktionnlo\\ //_\ l‚./ strukturelle
. ;."

Sichtweise Sichtweise



|
h liknlinchel
pSlychtwm-o

Abb. 1.2: Das Y—Mode]!

Dieses Modell gliedert den Entwurfsprozeß in eine Hierachie von
Abstraktionsebenen, dargestellt durch die konzentrischen Kreise im Modell:

Systemebene
Algorithmische Ebene
Registertransfer—Ebene
Gatterebene
Schaltkreisebene
Layout

o>cna>com»-

Die auf jeder Ebene erstellten Dokumente sind eine Verfeinerung der
Dokumente der darüber liegenden Ebene.

Neben den Abstraktionsebenen beschreibt das Modell drei Sichtweisen des IC—
Entwurfs. die sich durch alle Abstraktionsebenen ziehen, auf den unterschiedlichen
Ebenen aber verschieden stark ausgeprägt sind:

— Die funktionale Sichtweise beschreibt das Systemverhalten auf den
verschiedenen Abstraktionsebenen. Auf einer hohen Ebene wird dabei eine
funktionale Spezifikation erstellt. Die niedrigeren Abstraktionsebenen
beschreiben das Verhalten unter Verwendung geeigneter Modelle, wie z.B.
Differentlalgleichungen für Transistoren.

- Die strukturelle Sichtweise bildet eine Art Brücke zwischen der funktionalen
und der physikalischen Sichweise. Sie beschreibt die Aufgliederung des
Entwurfs in Untereinheiten und die Beziehungen zwischen diesen
Untereinheiten. Als Dokumente werden auf den verschiedenen
Abstraktionsebenen verschiedene Formen von Netzlisten erstellt. Diese
Netzlisten enthalten jedoch keinerlei geometrische Angaben.

Einleitung ' 3



- Die physikalische Sichtweise beinhaltet diejenigen Aspekte des IC—
Entwurfsprozesses, die speziell auf die Erstellung von Fertigungsvorlagen
abzielen. Auf den hohen Abstraktionsebenen wird die Anordnung der
funktionalen Unterstrukuren auf der Chipoberfläche festgelegt. Auf den
niedrigen Ebenen wird die exakte Auslegung einzelner Schaltungselemente in

ihrer geometrischen Strukur, ihren Abmessungen und Positionierung
beschrieben.

Das Y—Modell kann dazu benutzt werden, eine Entwurfsmethodik in ihren
einzelnen Schritten darzustellen. Eine andere Anwendung ist die Einordnung von
Teilaufgaben, die ein CAD—System ausführt.

## 1.2 Die Bestandteile eines CAD-Systems für den physikalischen Entwurf

Allgemein versteht man unter einem physikalischen Entwurf alle Dokumente,
die unter der physikalischen Sichtweise erstellt werden. In der vorliegenden Arbeit
soll der Begriff des physikalischen Entwurfs enger gefaßt werden. Er umfaßt im
wesentlichen die geometrischen Aspekte des Entwurfs, d.h. die erstellten Dokumente
beschreiben den Entwurf mithilfe exakter geometrischer Strukturen. die auf
verschiedenen Ebenen ("Layern") nach absoluten Koordinaten angeordnet sind. Ziel
des physikalischen Entwurfs ist die Erstellung eines geometrischen Layouts. Dieses
umfaßt alle Vorlagen für die anschließende Maskenfertigung.

Bei dem im folgenden vorgestellten System für den physikalischen Entwurf
handelt es sich entsprechend nur um eine Komponente des CAD—Systems ALICE-l.
welches den gesamten IC—Entwurfsprozeß unterstützt und am Lehrstuhl Informatik 1
(Uni Dortmund) entwickelt wird. Seine Aufgaben werden durch den mit IMAGE
bezeichneten Pfeil in Abb. 1.2 beschrieben. IMAGE ("integrated Module Adaption
and @neration system") bildet den technologischen Sockel des ALICE—l-Systems
[Brü 88]. Mithilfe der drei Mechanismen Bibliothekszugriff. Modifikation und
Neugenerierung werden in IMAGE Realisierungen (d.h. geometrische Layouts) der

Module erzeugt, die mithilfe der anderen Komponenten des Systems geplant und
entworfen wurden.

/‚—\
Technologie— , Musken-
Speziﬁkation ©
Technologie— Meeken —
Compiler inter1u:e
?.
[[ Technologie ][ Sementlk JL Geometrie J1

I
i
Detenheltunq ,



















( Applikationeinterfece ‚‘
.
[ ’ 1
| inch konenukhve ;
me yt e ) ( ’ :
Applikationen [:



Abb. 1 .3: Image

Einleitung ' 4





### 1.2.1 Applikationen

Die Applikationen sind die eigentlichen Benutzer des IMAGE-Systems.
Entsprechend muß die Datenhaltung auf die Unterstützung der einzelnen
Applikationen abgestimmt sein. Man unterscheidet zwei Klassen von Applikationen:

— analytische Applikationen bearbeiten vorhandene geometrische Layouts. Ein
typisches Beispiel stellen Bauteilextraktoren dar. Mit ihrer Hilfe gewinnt man
aus einem geometrischen Layout einen Lageplan der abgefragten semantischen
Strukturen. Speichert die Datenhaltung neben den reinen Geometriedaten auch
bereits semantische Informationen, so beschränkt sich die Arbeit eines
Bauteilextraktors auf den Zugriff auf diese Daten. Weitere Beispiele für
analytische Applikationen sind

— Design Rule Checker (DRG)
— Electrical Rule Checker (ERC)
— Parameterextraktoren

— konstruktive Applikationen dienen zur gezielten Manipulation von
geometrischen Layouts durch Hinzufügen, Löschen oder Verändern
geometrischer Strukturen. Die Spannweite solcher Applikationen reicht vom
einfachen Maskeneditor, mit dessen Hilfe sich geometrische Strukturen direkt
am Bildschirm eingeben lassen, bis hin zu vollständigen Layoutsynthese—
systemen. Weitere Beispiele für konstruktive Applikationen sind

— Kompaktoren
- Design—Rule-Anpasser
— Plazierungs- und Verdrahtungssysteme

### 1.2.2 Technologiespezifikation

Ein wesentlicher Bestandteil des IMAGE-Systems und der Angelpunkt für die
vorliegende Arbeit ist die explizite Repräsentation von Technologiedaten. Viele
heute existierende CAD—Systeme sind für eine bestimmte Technologie entwickelt
worden und sind so implementiert, daß die Verwendung von anders strukurierten
Technologien nicht ohne große Änderungen am Code des Systems möglich ist. Aus
wirtschaftlichen Gründen operieren IC—Hersteller heute jedoch mit verschiedenen
Technologien, in zunehmendem Maße sogar gemeinsam in einem Entwurf, so daß die
Anwendbarkeit eines Satzes von CAD—Werkzeugen für unterschiedliche Technologien
zu einer zentralen Forderung wird [Ehr 83].

Geometrische Layouts werden nach den Regeln einer spezifischen Technologie
aufgebaut und manipuliert. Eine Applikation, die auf einem Layout arbeitet, muß
die relevanten Aspekte der zugrunde liegende Technologie kennen. Zu Beginn der
Automatisierung des Chip—Entwurfs war es üblich, Software—Werkzeugen das
benötigte Technologiewissen direkt über die Steuerung ihres Kontrollflusses im Code
einzuprogrammieren. ‘

Es könnte beispielsweise ein Switch—Level—Simulator mit der Annahme
programmiert worden sein, das eine Schaltung aus einer Hierarchie von Zellen
aufgebaut ist, von denen wiederum jede ein Netzwerk von Transistoren mit drei
Anschlüssen enthält. In diesem Fall würde der Wechsel auf eine Technologie, die
einen zusätzlichen Substratanschluß pro Transistor erfordert, eine umfangreiche und
fehleranfällige Änderung am Code des Simulators erforderlich machen (nach
[Chu 851).

Einleitung 5



Software—Werkzeuge mit solcheart eingebauten Annahmen sind nicht
technologieinvariant. Technologieinvarianz bedeutet, daß bei einem Wechsel der

Technologie keine softwaretechnologischen Änderungen an der Applikation nötig
sind.

Der Begriff der Technologieinvarianz ist nicht synonym zum Begriff der
Technologieunabhängigkeit. Physikalische Applikationen arbeiten direkt auf den
geometrischen Strukturen eines Layouts und sind damit inhärent
technologieabhängig. Der Begriff der Technologieunabhängigkeit ist eher für höhere
Abstraktionsstufen adäquat. Für dieArbeit eines Architekturplaners z.B. ist die
verwandte Technologie irrelevant.

Im IMAGE-System wird die Technologieinvarianz durch einen datengetriebenen
Ansatz realisiert. Technologiedaten werden nicht implizit in den Code der
beteiligten Applikationen hineinprogrammiert, sondern werden in einer formalen
Sprache beschrieben und von außen dem System zugänglich gemacht.

### 1.2.3 Maskendaten

Um Layouts mit der Umwelt austauschen zu können, gibt es im IMAGE—System
eine Schnittstelle für Maskendaten. Geometrische Layouts von anderen CAD-
Systemen können über diese Schnittstelle eingelesen und in die interne Darstellung
umgewandelt werden. Ebenso werden erstellte Layouts über diese Schnittstelle in
standardisierten Formaten ausgelesen.

### 1.2.4 Datenhaltung

Heute existieren vorwiegend CAD—Werkzeuge, die für eine spezielle Teilaufgabe
des Layout-Entwurfsprozesses ausgelegt sind. Diese Systeme sind aufgrund ihrer
Datenstrukturen nicht oder nur schwer zu vollständigen Entwurfssystemen
integrierbar. Eine abgestimmte, integrierte Implementation von Entwurfswerkzeugen
für unterschiedliche Teilaufgaben verspricht jedoch Synergieeffekte, die eine
erhöhte Leistungsfähigkeit des Gesamtsystems erwarten lassen. Kern des IMAGE-
Systems ist daher die zentrale Datenhaltung mit ihren drei Teilbereichen.

Einleitung 6



Geometriedaten

In diesem Teilbereich der zentralen Datenhaltung werden Strukturen zur
internen Repräsentation konkreter Layouts während ihrer Bearbeitung durch IMAGE

zur Verfügung gestellt. Das Datenmodell fußt auf einer fünfstufigen Hierarchie
(Abb. 1.4):

Leyout

'

‚", /\ \ Leyer
1
& %— Gebiet (mit Löchern)
1
” Elemente (ein AußenE.)

l
\_‚/ Atome (Kunten. Punkte)

Abb. 1.4: Darstellung geometrischer Strukturen in IMAGE

Die unterste Hierarchiestufe enthält Atome zur Beschreibung von Anfangs-—
und Endkoordinaten, Funktionsvorschriften und Durchlaufrichtungen.

Die nächste Stufe beschreibt elementare Objekte, jeweils gebildet aus einer
sequentiellen Liste atomarer Objekte. Elementare Objekte können äußere oder innere
Gebietsabgrenzungen darstellen.

Die dritte Stufe enthält die Gebietsobjekte. Ein Gebietsobjekt besteht aus
einem Elemtarobjekt zur Beschreibung seiner Außenabgrenzung und einer (mglw.

leeren) Menge von Elementarobjekten zur Beschreibung von inneren Gebiets-
abgrenzungen.

Die vierte Hierarchiestufe ordnet Gebietsobjekte Layern zu. Ein Layerobjekt
beschreibt jeweils eine Maskenebene der zugrunde liegenden Technologie bzw. eine
durch Verknüpfung solcher Ebenen entstandene Pseudoebene.

Die oberste Hierarchiestufe faßt die Layerobjekte eines Entwurfs mit allen
geometrischen Strukturen der darunterliegenden Hierarchiestufen zu einem Layout
zusammen [Wro 87]. [Pre 88].

Semantikdaten

Dieser Teilbereich der IMAGE-Datenhaltung geht über die gewöhnlich in CAD-
Systemen für den IC-Entwurf gespeicherten Daten hinaus. Gerade die Speicherung
zusätzlicher semantischer Informationen ermöglicht es den Applikationen, die
Geometriedaten in "intelligenter" Weise — d.h. in diesem Zusammenhang: abhängig
von ihrem sematischen Kontext — zu überprüfen oder zu manipulieren. Semantische
Daten können einmal direkt bei der Erstellung eines Layouts (z.B. durch einen
technologiegesteuerten Layouteditor) in IMAGE eingebracht werden. Eine zweite
Möglichkeit ist die nachträgliche Extraktion der semantischen Informationen aus
einem extern erstellten Layout mithilfe eines Bauteilextraktors. Dem Datenmodell
liegen folgende Objekttypen zugrunde [Mör 88]:

Einleitung ' 7



.
. _ {.*-r-

- Relationship Objekts definieren gerichtete Relationen zwischen zwei oder
mehreren Objekten. Gegenüber Datenmodellen für konventionelle Datenbanken
[Che 76] dürfen Relationship Objects Relationen auf anderen Relationship
Objects defineren. Zur Beschreibung von Relationen zwischen (Teil—) Mengen
von Objekten stehen verschiedene Stelligkeiten von Relationship Objects zur
Verfügung:

1:1 Relationship Objects definieren eine Relation zwischen genau zwei
Objekten.

1:n Relationship Objects setzen ein Objekt in Relation zu n anderen Objekten.

n:m Relationship Objects setzen n Objekte zu m anderen Objekten in Relation.

- Composition Objects definieren neue Objekte, die aus verschiedenen
Komponenten bestehen und ermöglichen damit eine Hierarchiebildung innerhalb
der Objektstruktur. Ein Composition Object definiert somit eine Komponenten—
beziehung zwischen sich und den anhängenden Unterstrukturen.

— Set Objects fassen Objekte oder Teilstrukturen zusammen und strukturieren
damit die Objektstruktur. Ein Set Object definiert eine Elementbeziehung
zwischen sich und den anhängenden Unterstrukturen.

— Quantity Objects parametrisieren die Anzahl von Objekten oder Teilstrukturen
bei Set Objects.

— Property Objects binden Eigenschaften an Objekte.

Technologiedaten

Wie oben bereits erwähnt, liegen Technologiedaten im IMAGE—System nicht
implizit in den Kontrollstrukturen von Applikationen vor, sondern werden explizit
in einer formalen Sprache beschrieben. Ein Compiler übersetzt diese
Technologiespezifikation in eine interne Repräsentation, welche den Applikationen
das benötigte Technologiewissen über einen Satz von Operationen zur Verfügung
stellt. Der Compiler und die interne Repräsentation der Technologiedaten wird an
späterer Stelle in dieser Arbeit noch detailiert beschrieben.
