12. Zusammenfassung





Ziel der vorliegenden Arbeit war die Realisi-ung eines Verfahrens zur
Erzielung von Technologieinvarianz und dessen Intégrgn in die Datenhaltung des
Layout—CAD Systems IMAGE. Nach einer einleite—i'ie“) _E‘sc'hreibung der Aufgaben
eines Layout—CAD Systems und seiner Bestan t.:nillg werden im 2. Kapitel
verschiedene Ansätze der Technologieverarbeitung’ in der Literatur untersucht. Aus
den vorhandenen Ansätzen läßt sich eine Hier'ä’rchie'‚._\0n möglichen Technologie—
Spezifikationsmechanismen nach dem Grad ihrer Ei_'ung für ein integriertes

. Layout-CAD-System ableiten: ‘




‚«

Zusammenfassung 124



1. Formale Sprachen zur Beschreibung aller relavanten Aspekte einer Technologie.
Dieser Ansatz zeichnet sich durch folgende Merkmale aus:

a) Flexibel. Für verschiedene Applikationen relevante Techno-
logieinformationen lassen sich gemeinsam in einer Spezifikation
unterbringen und bei Bedarf erweitern, ohne das eine Anpassung bereits
unterstützter Applikationen notwendig ist.

b) Komplexe Zusammenhänge beschreibbar. Durch die Verwendung von
Schlüsselworten, Formatfreiheit und geeigneter syntaktischer Regeln
können auch komplexe Abhängigkeiten übersichtlich beschrieben werden.
Eine weitgehend dem Englischen angenäherten Syntax ist einem
kryptischen Format vorzuziehen, da dadurch eine Technologiespezifikation
sowohl vom Technologen als auch vom Designer leicht nachvollziehbar ist.

c) Überprüfbar. Da die Technologiespezifikation in einem textuellen Format
vorliegt, ist sie einer manuellen Überprüfung unmittelbar zugänglich.

d) Kommunizierbar. Ein textuelles Format eignet sich besonders zur
Übermittlung von Technologiedaten vom Chiphersteller zum Designer.
Mißverständnisse lassen sich durch eine formale Beschreibung vermeiden;
technologieabhängige (aber durch Verwendung einer expliziten
Technologiebeschreibung technologieinvariante) Applikationen beim
Designer lassen sich direkt durch eine vom Chiphersteller gelieferte,
maschinenlesbare Technologiespezifikation mit Technologieinformationen
versorgen.

2. Verwendung einer Datenbank zu Speicherung von Technologieinformationen.
Gemessen an den Merkmalen der Verwendung einer formalen Sprache sind
sicher die Punkte (a) und (b) erfüllt. Eine Datenbank ist aber weder einfach
manuell zu Überprüfen noch einfach kommunizierbar.

3. Tabellarisches Format. Dies ist der ursprüngliche datengetriebene Ansatz zur
Spezifikation von Technologieinformationen für einzelne Applikationen.
Gegenüber dem Vorteil einer direkten Les— und Auswertbarkeit für eine
Applikation lassen sich in einem manuell erstellten tabellarischen Format nur
wenig komplexe Zusammenhänge darstellen. Sollen Abhängigkeiten vom Kontext
einer Entwurfsregel formuliert werden, wird die Tabelle aufgrund von
erforderlichen Querverweisen schnell so komplex, daß sie nicht mehr manuell
wartbar ist. Geht man allerdings von einer automatischen Erstellung einer
tabellarischen Technologiebeschreibung aus einem textuellen Format aus,
scheint dieser Ansatz aufgrund seiner leichten Handhabbarkeit auf der
Applikationsseite zur Speicherung von Technologieinformationen in einem
Layout—CAD System geeignet zu sein.

Im Rahmen dieser Arbeit wurde ein Compiler für eine
Technologiebeschreibungssprache DROOLY entwickelt, weicher als Ausgabe eine
tabellarisches Format erzeugt. Zum Zugriff auf diese Tabellé'n wird einer
Layoutapplikation ein umfangreicher Satz auch komplexer Zugriffsoperationen zur
Verfügung gestellt, um eine Formatabhängigkeit zu vermeiden. Obwohl DROOLY
durch den zu Testzwecken implementierten Präproze er ein durchaus lesbares
Format erhält, ist diese Sprache nur als Zwischensprac .- in. einem Compiler für die
Technologiebeschreibungssprache DINGO-II konzipieiﬁt. Ja)l-GO-II erfüllt durch seine
dem Englischen angenäherte Syntax die Anfor 11,5 an eine Technologie-
beschreibungssprache. Es stellt einen umfangreia_r Satz unterschiedlicher
Objekttypen und eine komfortable Syntax zun M'i‘eibung sowohl allgemeiner
Technologieaspekte als auch qualitativer wie ' antir„
Technologieobjekte zur Verfügung. '







.\

Zusammenfassung 125



DROOLY kennt demgegenüber nur zwei unterschiedliche Objekttypen. Lokale
Module werden durch die Möglichkeit vermieden, Objekte mit qualifizierten
Bezeichnern zu definieren. Der Parametermechanismus ist gegenüber DINGO—II durch
die Übergabe von Indizes in Parameterlisten vereinfacht. Topologiebeschreibgung
und Design Rules sind durch eine gemeinsame Syntax zusammengefaßt.

Durch diese Vereinfachungen liegt der Abstraktionsgrad einer DROOLY—
Beschreibung zwischen dem einer DINGO—II Beschreibung und dem tabellarischen
Format. Über das Compiler—Frontend werden dabei möglichst wenige Annahmen
gemacht, da ein solches noch nicht zur Verfügung stand. Im 5. Kapitel wird die
Struktur der erwarteten Symboltabelle beschrieben; in 6. Kapitel wird eine
Anleitung zur Übersetzung von DINGO—II nach DROOLY gegeben.

Das tabellarische Format setzt sich aus den drei Tabellen Symboltabelle.
Objekttabelle und Wertausdruckstabelle zusammen. Die Aufteilung in diese drei
Tabellen ergibt sich aus der unterschiedlichen Struktur der zu speichernden Daten:

In der Symboltabelle werden alle nicht strukturellen Informationen über die
Technologieobjekte gespeichert. Im Compiler—Backend werden daraus nur die
Objektbezeichner benötigt.

Die Objekttabelle speichert qualitative Informationen über die Struktur der
Technologieobjekte. Einzelne Tabellenelemente sind die Technologieobjekte mit ihren
Unterstrukturen sowie Relationen und Eigenschaften dieser Objekte. Wichtiges
Prinzip ist dabei die Lokalität der Speicherung von auf ein Objekt bezogenen
informationen. Ein Objekt erhält Verweise auf alle Unterstrukturen. Die Vererbung
von Unterstrukturen aus dem Klassenobjekt trägt viel zur Komplexität des DROOLY—
Compilers bei.

Von der Objekttabelle wird auf quantitative Informationen in der
Wertausdruckstabelle verwiesen. Aus Atomarausdrücken aufgebaut geben die hier
gespeicherten Baumstrukturen die Syntaxbäume der DROOLY—Wertausdrücke wieder.

Der Compiler zum Aufbau der Tabellen aus einer DROOLY—Beschreibung
arbeitet im wesentlichen syntaxgetrieben. Aus einer Syntaxbeschreibung der
DROOLY—Sprache zusammen mit der Angabe semantischer Aktionen erzeugt der
Parsergenerator NEMO einen Parser für die DROOLY—Sprache. Von den semantischen
Aktionen werden beim Erkennen der jeweiligen syntaktischen Konstrukte in der
DROOLY-Eingabe Operationen aus den object- und expr—Modulen zum Aufbau der
Tabellenstrukturen aufgerufen. Einen besonders komplexen Punkt bei der Erstellung
der Tabellen stellt die Übersetzung einer Identifikationsanweisung dar. welche von
den Operationen des compare—Moduls behandelt wird. Ein Modul mit Operationen zur
Validitätsüberprüfung von Topologierelationen, ein Scanner. ein Modul zur
Fehlerbehandlung und ein solches zur Protokollierung des Compilerlaufs
vervollständigen den implementierten DROOLY-Compiler. Sein Aüfbau wird im
8. Kapitel beschrieben.

Die Zusammenarbeit mit Layoutapplikationen wird ‘m
im 10. Kapitel anhand eines konkreten Beispiels bes
der Baulementextraktor MONDRIAN gewählt. MONDRIAN
Informationen in der DINGO Design Rule Beschrei
der so erhaltenen Technologiedaten erfolgt in einem
Manager. Durch Anpassung dieses Moduls ließe sich
Aufwand zur Benutzung der hier entwickelten Tabelle

‚m Kapitel allgemein und
;;riee ,. Als Beispiel wurde
éfgwartet seine Technologie—
sis:i>_rache. Die Interpretation
‘égieilen Modul, dem DINGO-
.NDRIAN mit relativ geringem
odifizieren.








Zusammenfassung . ‘ . 126





Im 11. Kapitel schließlich werden drei Aspekte für mögliche Erweiterungen der
Operationen auf den Tabellen aufgezeigt, die in bestimmten Applikationen
erforderlich sein könnten bzw. die den Komfort des Technologie-Compilers erhöhen
würden.
