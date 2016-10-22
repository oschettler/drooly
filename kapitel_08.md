# 8. Der DROOLY—Compiler

Das eigentliche Backend des DINGO—II Compilers [Krä 891 gliedert sich in neun
Untermodule, die in den Abschnitten 8.2 bis 8.10 beschrieben werden. Der

Präprozessor simuliert während der Entwicklung das Frontend des DINGO—II
Compilers.

# 8.1 Präprozessor

Der Präprozessor ist ein eigenständiges Programm und erfüllt während der
Entwicklung des DROOLY—Compilers zwei Aufgaben:

— Er ermöglicht eine mnemonische Eingabe der DROOLY—Lexeme und die Eingabe
von symbolischen Bezeichnern.

- Er wandelt die mnemonische Eingabe um in das normale DROOLY—Format und
eine externe Symboltabelle.

Der Präprozessors simuliert in seinen Ausgaben ein DINGO-II Compiler-
Frontend und ermöglicht so eine unabhängige Entwicklung des Compiler-Backends.

Umgebung

Der Aufruf des Präprozessors erfolgt von der Betriebssystemebene mit
folgender Syntax:

pre <dateiname>.r

Der Dateiname trägt eine Erweiterung ".r" (für "r_eadable"). Die Ausgabe des
Präprozessors besteht aus der DROOLY—Textdatei mit der Erweiterung ".cod" und
der externen Symboltabelle mit der Erweiterung ".sym". Konnte der Präprozessor
seine Arbeit erfolgreich beenden, liefert er den Exit-Code 0. Bei einer Fehler—
situation (Fehler bei Dateizugriff, Oberlauf der internen Tabellen oder Syntaxfehler
in der Eingabe) wird mit dem Exit—Code -1 abgebrochen.

Der DROOLY—Compiler (Präprozessor) ' 60



Struktur

Der Präprozessor besteht aus einem kurzen Hauptprogramm main, einer
Scanner- und Ausgaberoutine preproc mit ihren Hilfsroutinen mystrtok zum Parsen
eines qualifizierten Bezeichners und preskip_blank zum Überlesen von Leerzeichen
und Kommentaren und einigen Routinen zur Implementierung einer einfachen
Symboltabellenverwaltung (wsymtb zum Schreiben der Symboltabelle auf eine Datei;
prelookup zum Nachschlagen eines Bezeichners in der Symboltabelle; s_new zum
Initialisieren eines Symboltabelleneintrages; new_string zum Allozieren von
Speicherplatz für einen Bezeichner). Das eigentliche Präprozessormodul wird mit dem
Token-Modul des Backends zusammengebunden, um konsistente Kodierungen der
DROOLY—Schlüsselworte zu gewährleisten. Aus diesem Modul werden die Funktionen
gtokencode zur Umwandlung von lesbarer Eingabe in einen internen Code und
gtokenstr zur Umwandlung dieses Codes in die externe DROOLY—Darstellung benutzt.
Selbst bei einer völligen Umstellung der lexikalischen Struktur der DROOLY-
Zwischensprache beschränken sich Änderungen auf die Tabellen und
Zugriffsoperationen im Token—Modul.



plukip_blenk
ieepece
gtokencode (token.c)
gtokenetr (tokon.c)
uugo iedigit

iealphe

myetrtok

m-tn.—.pygpfoc
.

prelookup \.e_new—— new_etrinq

wuymtb—grepetr

Abb. 8.1: Die Struktur des Präprozessors

Verfahren

Im Hauptprogram werden die Ein— und Ausgabedateien geöffnet und
anschließend die Routine preproc aufgerufen, die das Scannen der Eingabedatei, die
Ausgabe der DROOLY—Textdatei und den Aufbau der Symboltabelle steuert.

Die Symboltabelle ist als Vektor von Elementen mit folgender Struktur
implementiert:

name Zeiger auf den Bezeichner
left linker INORDER—Nachbar
right rechter INORDER—Nachbar

Die Verzeigerung über die Felder left und right legt einen binärer Suchbaum
über den Symboltabellenvektor, um die Suche nach Bezeichnern zu beschleunigen.
Ein neuer Bezeichner wird von der Funktion prelookup in die Symboltabelle
eingefügt. Diese sucht mithilfe des Suchbaums den Platz für den gewünschten
Bezeichner in der Symboltabelle. Ist der Bezeichner noch nicht eingetragen, wird
mit der Routine s_new ein neuer Platz im Symboltabellenvektor angefordert und der
neue Bezeichner dort eingetragen. Läuft bei dieser Anforderung die Symboltabelle
über, ist zur Zeit keine Reallozierung vorgesehen. Der Präprozessor bricht mit einer
Fehlermeldung ab und signalisiert sein Scheitern durch den Exit—Code -1. Trat

kein Fehler auf, gibt prelookup den Index des alten oder neuen Platzes in der
Symboltabelle zurück.

Der DROOLY—Compiler (Präprozessor) ' 61



Die Funktion preproc liest in einer Endlosschleife den gesamten Eingabetext.
Bei jedem Schleifendurchlauf werden zunächst Trennzeichen und Kommentare durch
die Funktion preskip_blank überlesen. Wurde dabei das Ende der Eingabedatei noch
nicht erreicht, werden nun Zeichen für ein Schlüsselwort, einen Bezeichner oder
einen numerischen Wert in einem Zwischenspeicher gesammelt. Die Bedingung, daß
einzelne Lexeme in der Eingabe durch mindestens ein Leerzeichen getrennt sein
müssen, vereinfacht hier die Abbruchbedingung. Das so gesammelte Lexem wird mit
den Funktionen gtokencode und gtokenstr aus dem Token—Modul in die DROOLY-
Darstellung umkodiert. War eine solche Umkodierung nicht möglich, wird durch
Überprüfung des ersten Zeichens festgestellt, ob es sich um einen numerischen Wert
handelt. Dieser wird mit einem "#" versehen in die DROOLY—Textdatei ausgegeben.

Ist das erste Zeichen ein Buchstabe, "—" oder "_", wird ein "®" in die
DROOLY—Textdatei ausgegeben und es beginnt die Analyse eines u.U. qualifizierten
Bezeichners. Die durch "." getrennten Komponenten eines solchen Bezeichners

werden durch die Funktion mystrtok einzeln abgetrennt und in der Symboltabelle
nachgeschlagen. Der gefundene bzw. neue Symboltabellenindex wird jeweils in die
DROOLY—Textdatei ausgegeben.

Das beschriebene Verfahren erkennt sicher längst nicht alle illegalen Eingaben
in der lesbaren Datei. Da es sich bei dem Präprozessor jedoch nur um eine Test—
und Entwicklungsumgebung handelt, wurde auf eine weitere Verfeinerung verzichtet.

Nachdem das Ende der Eingabedatei erreicht ist, wird die Symboltabelle in die
externe Datei geschrieben und der Präprozessor beendet seine Arbeit mit dem Exit—
Code 0.

## 8.2 Hilfsmodule für Fehlermeldungen, Dekodierung und
Überprüfung der Codeabdeckung

Diese drei Module fassen bestimmte Aufgaben zusammen, die für eine leichte
Änderbarkeit des Codes in eigenen Modulen untergebracht sind.

Fehlermeldungen im Modul error.c

Fehlermeldungen werden im Compiler Backend von einer zentralen Funktion
error aus dem gleichnamigen Modul ausgegeben. Diese Routine akzeptiert eine
Fehlernummer und eine variable Anzahl von Parametern. Aus der Fehlernummer
wird die Fehlerklasse und der Rumpf der Fehlermeldung mithilfe einer Tabelle
ermittelt. Es werden drei Fehlerklassen unterschieden:

— PANIC: Die Übersetzung muß aufgrund von Inkonsistenzen oder Überläufen in
internen Tabellen abgebrochen werden.

— ERROR: Die Übersetzung wird aufgrund einer fehlerhaften Benutzereingabe
abgebrochen.

— WARNING: Die Übersetzung wird trotz einer fehlerhaften Benutzereingabe
weitergeführt. Die erzeugten Tabellen entsprechen aber nicht notwendigerweise
der Intention des Benutzers.

Eine vollständige Liste der Fehlermeldungen und der zugehörigen Fehlerklassen
findet sich im Anhang B. Die zweite Funktion im error-Modul wird nur aufgerufen,
falls die Compilation abgebrochen oder regulär beendet werden soll. Sie erhält als
Parameter den Exit-Code des Compiler—Backends. Vor Beendigung des Programmlaufs

Der DROOLY—Compiler (error.c, token.c, teval.c) 62



werden alle Trace—Dateien geschrieben und anschließend alle offenen Dateien
geschlossen. Zuletzt wird die exit-Funktion mit dem übergebenen Parameter
aufgerufen.

Dekodierung im Modul token.c

Alle symbolischen Konstanten mit einer lesbaren Entsprechung ebenso wie die
externen Darstellungen von mnemonischen und normalen DROOLY—Schlüsselworten
zusammen mit ihrer internen Kodierung wird in den Tabellen des Token-Modules
zusammengefaßt. Dieses Modul stellt darüberhinaus die Zugriffsoperationen auf
diese Tabellen zur Verfügung. Im einzelnen haben die Funktionen folgende
Aufgaben:

gtokem'ndex liefert zu einem als Zeichenkette vorliegendem Schlüsselwort
(mnemonisch oder normal über einen Ausrufparameter einstellbar) den Index in
der jeweiligen Tokentabelle. Zur Unterstützung der binären Suche sind die
Tokentabellen alphabetisch sortiert.

gtokencode liefert zu einem als Zeichenkette vorliegendem Schlüsselwort
(mnemonisch oder normal über einen Ausrufparameter einstellbar) den vom
Parser vergebenen Code durch Suchen in der jeweiligen Tokentabelle mithilfe
von gtokenindex.

gtokenstr wird nur zur Unterstützung des Präprozessors benötigt. Diese Funktion
liefert zu einem Token—Code die externe DROOLY—Darstellung durch lineare
Suche in der entsprechenden Tokentabelle.

gspecstr wird hauptsächlich zur Erzeugung lesbarer Trace—Ausgaben benutzt. Alle
symbolischen Konstanten sind in einer speziellen Tabelle zusammen mit ihrer
lesbaren Darstellung abgelegt. Die tatsächlichen numerischen Kodierungen der
einzelnen symbolischen Konstanten wird aus dieser Tabelle durch ein Awk—
Programm erzeugt und in einer Header—Datei abgelegt. Durch Inkludieren dieser
Header—Datei zur Compile—Zeit sind die numerischen Kodierungen in der
Tabelle automatisch in aufsteigender Reihenfolge sortiert, so daß nach einer
Kodierung binär gesucht werden kann. Gspecstr liefert die gefundene
Zeichenkette als Funktionsergebnis zurück.

gtokencode— gtokenmdex
gtokenetr
gepeetr

Abb. 8.2: Die Struktur des taken-Moduls

Überprüfung der Codeabdeckung im Modul teval.c

Dieses Modul unterstützt die Instrumentierung des Compiler-Backends mit
Aufrufen zur Protokollierung der Codeabdeckung bei der Abarbeitung einer
Testbibliothek. Protolliert werden soll jeweils der Eintritt und das Verlassen einer
Funktion und der Eintritt in einzelne Codeblöcke.

Der DROOLY—Compiler (error.c, token.c, teval.c) 63



Umgebung

Zur Ausrüstung des Codes wird ein Satz spezieller Makros definiert, der
jeweils die Protokollierung initiiert:

FKT( <name>,<blockanzahl> ); Eintritt in eine Funktion

B( <blocknummer) ) Eintritt in einen Block
END; Verlassen eine Prozedur
RETURN( <rückgabewert> ); Verlassen einer Funktion
POP; ' ' Verlassen einer semantischen

Einhei t ohne return

Die Protokollierungsmakros werden durch die Compileroption —DTEVAL bei der
Übersetzung aktiviert.

Das Teval—Modul sammelt die gewonnenen Informationen während des Ablaufs
des Compilers in einer Tabelle, welche dann nach abgeschlossener Übersetzung in
eine Protokolldatei geschrieben wird. Diese Datei enthält für jede instrumentierte
Funktion eine Zeile mit dem Funktionsnamen in der ersten Spalte. Die zweite Spalte
gibt die Anzahl der Eintritte in die Funktion, alle weiteren Spalten geben für
jeden instrumentierten Block dieser Funktion die Anzahl der Eintritte an. Nicht
betretende Funktionen und Blöcke erhalten eine 0 in diesen Spalten.

Die Teval-Tabelle tv_tab wird zur Compilezeit mithilfe eines Awk-Skriptes
angelegt und zum Compiler—Backend hinzugebunden. Sie ist als Vektor mit
Elementen von folgender Struktur organisiert:

left, right: Zum schnellen Zugriff auf die gespeicherten Funktionsbezeichner wird
durch die Initialisierungsroutine tv_im't vor Beginn der Protokollierung ein
binärer Suchbaum über die Teval-Tabelle gelegt. Die Felder left und right
verweisen jeweils auf den linken bzw. rechten Inorder-Nachbarn.

name: Dieses Feld wird zur Compilezeit mit dem Bezeichner einer Funktion
initialisiert und dient als Schlüssel für den Zugriff auf die Tabelle.

fkt_cnt: Dieses Feld wird mit 0 initialisiert. Bei jedem Eintritt in die zugehörige
Funktion wird es um 1 inkrementiert.

block_cnts: Dieser Vektor von Zählern für die einzelnen instrumentierten Blöcke
der zugehörigen Funktion wird vor Beginn der Protokollierung von tv_1‘nit
alloziert und mit Nullen initialisiert. Auf die einzelnen Zähler wird über die
Blocknummer des zu protokollierenden Blockes zugegriffen. Bei jedem Eintritt
in einen instrumentierten Block wird der zugehörige Zähler um 1
inkrementiert.

nr_blocks: Dieses Feld wird zur Compilezeit mit der Anzahl instrumentierter Blöcke
pro Funktion initialisiert. Die gespeicherte Zahl wird zur Allozierung des
block_cnts-Vektors benötigt.

Struktur

Das Teval—Modul enthält die Routinen tv_1'n1't zur Initialisierung der
Tevaltabelle zu Beginn der Protokollierung und tv_write zum Schreiben der Tabelle
in eine Datei nach Abschluß der Protokollierung. Die Protokollierung wird durch die
Funktionen tv_fkt, tv_block und tv_pop durchgeführt. Diese werden von einigen
lokalen Funktionen unterstützt. Dabei sucht tv_lookup einen Funktionsbezeichner

Der DROOLY—Compiler (error.c, token.c, teval.c) 64



in der Teval—Tabelle, tv_push rettet einen Funktionsindex auf den Aufrufstack
und new_int_list alloziert Speicherplatz für die Blockzähler—Vektoren.

FKT()—‘tv_lkt tv_lookup
RETURNOI'“’_P°P K-—— tv_pueh

P 0 P

E ND

80 tv_bI6ck
tv_init—tv_inordor
tv_write





Abb. 8.3: Die Struktur des tevaI-Moduls

Verfahren

Der Compilerlauf des Backends verläuft aus der Sicht des Teval-Moduls
folgendermaßen: Ganz zu Beginn wird die Routine tv_in1't aufgerufen, um die
Informationen in der Teval—Tabelle zu vervollständigen. Dabei werden für jede
Zeile in der Tabelle die block_cnts—Vektoren angelegt, mit Nullen initialisiert und
die Zeile nach ihrem Funktionsbezeichner in den binären Suchbaum einsortiert.

Während des gesamten Compilerlaufs werden Aufrufe instrumentierter
Funktionen über das FKT—Makro an die Funktion tv_fkt gemeldet. Diese sucht den
übergebenen Funktionsbezeichner in der Teval—Tabelle und merkt sich dessen Index
für die folgenden Blockprotokollierungen. Der alte Funktionsindex wird jedoch zuvor
auf einen Stack gesichert. Der Funktionseintritt wird durch Inkrementieren des
zugehörigen Zählers vermerkt.

Der Eintritt in einen Block der aktuellen Funktion wird über das B—Makro an
die Funktion tv_block gemeldet. Diese überprüft zunächst, ob der übergebene
Blockindex im legalen Bereich für die aktuelle Funktion liegt. Tritt dabei ein
Fehler auf, wird der Benutzer gewarnt und nichts protokolliert, der Programmablauf
aber fortgesetzt. Ist alles korrekt, wird der zum Block gehörende Zähler um 1
inkrementiert.

Beim Verlassen einer Funktion muß der Tabellenindex der rufenden Funktion
wieder vom Aufrufstack geholt werden. Das erledigt die Funktion tv_pop, die von
einem der Makros END, RETURN oder POP gerufen wird.

Wird die Kontrolle nach Abarbeitung der DROOLY—Datei wieder an das
Hauptmodul zurückgegeben, ruft dieses die Routine tv_write, welche den Inhalt der
Teval—Tabelle in eine Datei mit der Erweiterung ".tv" schreibt.

Der DROOLY-Compiler (main.c) ' 65



## 8.3 Hauptmodul main.c

Umgebung

Ein legaler Aufruf des Compiler—Backends hat eines der beiden folgenden
Formate:

drooly [options] <techfile>.r
oder drooly [options] <techfile>.cod

-v Erweiterte Semantiküberprüfung
(siehe Abschnitt 8.9)

Die folgenden Optionen dienen nur zum Einschalten verschiedener
Trace—Ausgaben in der Entwicklungsphase und werden durch Angabe
der Compiler-Option —DTRACE aktiviert:

—x Trace der Aktivitäten des Token—Moduls
—s Trace der Scanner-Eingabe
—t<n> Allgemeiner Trace des Tabellenaufbaus

mit Detailiertheit (n) (n in [O..91)

Sämtliche im Compiler-Backend verwandten Dateien und deren Dateinamen
werden im Hauptmodul deklariert, geöffnet und geschlossen. Jede Datei hat eine
charakteﬂstkmhe Erweüerung,cüe nach Löschung der Erweherung Nr" oder Ncod"
an den als Konunandozeﬂenparameter übergebenen Dateinamen angehängt wird:

INOUT <tecfile>.sym Symboltabelle

IN <tecfile>.cod DROOLY-Textdatei

IN <tecfile>.r DROOLY-Textdatei für den Präprozessor
OUT <tecfile>.t Lesbare Form der erzeugten Tabellen
OUT <tecfile>.tec Erzeugte Tabellen

OUT <tecfile>.trf Testreferenz: Tabellen in einer

besonders zum Vergleich in einer
Testbibliothek geeigneten Form
OUT <tecfile>.tv Teval-Protokoll

Struktur

Das Hauptmodul enthält die Funktion main und die Hilfsfunktion usage zur
Ausgabe einer Benutzenneldung bei fehlerhaftem Progranunaufruf

rn ein —-yyperee (persone)
.—-pue2 (persone)

“menge—“_!" linieh (error.c)
brtte_tec (obiect.c)

Abb. 8.4: Die Struktur des Hauptmoduls

Der DROOLY—Compiler (main.c) ‘ 66



Verfahren

Main initialisiert zu allererst die Teval—Tabelle durch einen Aufruf von
tv_init. Anschließend wird die Aufrufzeile auf Optionen und Dateinamen
untersucht. Bei Verwendung eines Eingabedateinamens mit der Erweiterung ".r" wird
zunächst der Präprozessor mit dieser Datei gerufen. Trat dabei kein Fehler auf,
werden die verschiedenen Dateien geöffnet, die Symboltabelle eingelesen und
anschließend der DROOLY-Parser gerufen. Konnte der Parser seine Arbeit
erfolgreich beenden, wird zur Bearbeitung von merges die Funktion pass2 gerufen.
Zuletzt werden die erzeugten Tabellen mithilfe der Funktion write_tec in eine Datei

geschrieben und durch einen Aufruf von finish alle offenen Dateien geschrieben
und geschlossen.

## 8.4 Scanner scanner.c

Die einfache lexikalische Struktur von DROOLY erlaubt eine leichte Kodierung
des Scanners ohne Verwendung des Scannergenerators Lex. Von Lex generierte
Scanner werden im Vergleich zu einem handkodierten Scanner schnell recht
umfangreich. So hat der hier beschriebene Scanner nur einen Umfang von vier DIN—
A4—Seiten. Die für eine einfache Implementierung des DROOLY—Scanners gemachten
Annahmen über die lexikalische Struktur von DROOLY wären allerdings für eine
nicht maschinengenerierte Sprache wie etwa DINGO—II nicht akzeptabel. Eine
wichtige Voraussetzung für den Scanner in seiner jetzigen Form ist, daß Anfang
und Ende eines einzelnen Lexems ohne weiteres zu erkennen sind. In DROOLY
müssen daher Schlüsselworte, Bezeichner und numerische Werte durch mindestens
ein Leerzeichen voneinander getrennt sein. Darüber hinaus ist die DROOLY—Eingabe
aber völlig formatfrei.

Umgebung

Die einzige exportierte Funktion des Scanner—Moduls ist yylex. Sie wird ohne
Parameter vom Parser aufgerufen und liefert als Rückgabewert entweder den
Tokencode eines Schlüsselwortes, den Tokencode für IDENTIFIER für einen
(qualifizierten) Bezeichner oder den Tokencode für INTEGER für einen numerischen
Wert. Darüberhinaus legt sie das gelesene Lexem in der globalen Stringvariable
yytext ab. Fester Bestandteil des Kommunikationsprotokolls mit dem vom Parser-
generator Nemo erzeugten Parser ist außerdem die Übergabe des Tokenwertes in der
globalen Variable yylval. Ihr Typ ist ebenso wie der Typ des Parser-Wertestacks

als Variantentyp YYSTYPE mit folgenden Alternativen in der Headerdatei typedef.h
definiert:

OBJ:
Für Objekte wird ihr Objekttabellenindex und ein Flag für globale
Pfadbezeichner (durch einen "." eingeleitet) in yylval gespeichert.

NUM:
Numerische Werte werden direkt in yylval abgelegt.

EXPR:

Diese Alternative dient zur Speicherung eines Ausdruckstabellenindizes und
wird im Scanner nicht benutzt.

Ebenfalls zur Kommunikation mit dem Parser werden noch folgende globale
Variablen im Scanner benutzt:

yyin (IN) ist der File—Deskriptor der DROOLY—Eingabedatei.

Der DROOLY—Compiler (scanner.c) ' 67



yylineno (OUT) hält die aktuelle Zeilennummer in der DROOLY—Eingabedatei zur
Ausgabe in Fehlermeldungen.

act_class (IN) hält den Objejttabellenindex desjenigen Objektes, welches den
aktuellen statischen Kontext in der DROOLY—Eingabedatei definiert.

Struktur

Das Scanner—Modul wird von der Scanner—Routine yylex beherrscht. Daneben
enthält es noch die beiden Hilfsroutinen skip_blank zum Überlesen von Leerzeichen
und Kommentaren und mystrtok zum Parsen von (qualifizierten) Bezeichnern.

yylex—-ekip_blenk
».myetrtnk
'—-gtokencode (token.c)

Abb. 8.5: Die Struktur des Scanners

Verfahren

Die Funktion yylex wird vom Parser zum Einlesen des nächsten Lexems
aufgerufen. Dazu werden zunächst Leerzeichen und Kommentare durch die Funktion
skip_blank überlesen. Wurde dabei das Ende der Eingabedatei noch nicht erreicht,
werden nun Zeichen für ein Schlüsselwort, einen Bezeichner oder einen numerischen
Wert in der Variablen yytext gesammelt. Die Bedingung, daß einzelne Lexeme in der
Eingabe durch mindestens ein Leerzeichen getrennt sein müssen, vereinfacht hier
die Abbruchbedingung. Die Variable yytext ist als statischer Zeichenvektor von 80
Zeichen Länge deklariert, so daß es prinzipiell bei sehr langen Pfaden in
qualifizierten Bezeichnern zu einem Überlauf kommen könnte. Dabei ist nicht die
Länge der einzelnen Pfadkomponenten wichtig (Nur ihre Symboltabellenindizes

stehen in der DROOLY-Datei), sondern in der Hauptsache die Anzahl der
Pfadkomponenten.

Für das so gesammelte Lexem wird mit. der Funktion gtokencode aus dem
Token—Modul der Tokencode ermittelt. Gtokencode liefert 0, wenn es das Lexem
nicht gefunden hat. Konnte der Code jedoch ermittelt werden, handelt es sich um
eine positive ganze Zahl. Der mögliche Wertebereich wird durch den Code für ein
Pseudotoken BUILTIN in zwei Bereiche unterteilt. Codes größer als der Code von
BUILTIN werden als Ergebnis sofort an den Parser zurückgegeben. Die Token aus
dem unteren Bereich umfassen die Operatoren für PROPERTIES und C_PROPERTIE5.
Sie werden im weitem Verlauf wie Bezeichner von Objekten ("Pseudoobjekte")
behandelt und ihr Tokencode zunächst in einer Variablen builtin gespeichert.

Für das spezielle Token SELF wird in yytext ein Bezeichner generiert, der nur
aus einem "@" besteht. Daraus erzeugt yy1ex im weiteren Verlauf eine Referenz auf
den durch act_class indizierten Objekteintrag. Builtin wird auf den Code für
IDENTIFIER gesetzt.

Als nächstes werden numerische Konstanten an ihrem einleitenden "#"
erkannt. Die folgende Ziffernfolge wird in einen numerischen Wert umgewandelt und
dieser in der Kommunikationsvariable yylval.Nülii gespeichert. Der Tokencode für
INTEGER ist in diesem Fall das an den Parser zurückgelieferte Ergebnis.

Der DROOLY—Compiler (scanner.c) 68



Ist das erste Zeichen des Lexems dagegen ein "@" oder ist die Variable builtin
mit dem Bezeichner eines Pseudoobjektes besetzt, muß der vorliegende
Objektbezeichner analysiert werden. Die Objekttabelle übernimmt dabei mit ihrer
Funktion Iookup die Rolle einer Symboltabelle, die Funktion mystrtok dient als
Parser des Pfades. Die Analyse beginnt mit dem auf den "@" folgenden Zeichen.
Handelt es sich um einen ".", liegt ein globaler Pfadbezeichner vor, der statische
Kontext wird ignoriert. In Normalfall wird der Objekttabellenindex des statischen
Kontextes jedoch als Präfix des analysierten Pfades aufgefaßt. Die Information, daß
ein globaler Bezeichner vorliegt, wird im Parser noch benötigt und wird ihm durch
Setzen des Flags yylval.0BJ.is_global in der Kommunikationsvariablen mitgeteilt.

Bezeichner von Pseudoobjekten bestehen aus dem durch den statischen Kontext
bestimmten Präfix und dem negativen Tokencode als Suffix. Zur Erzeugung eines
Objekttabelleneintrages wird die Funktion Iookup mit diesen beiden
Pfadkomponenten aufgerufen. Ihr Ergebnis, der alte oder neue Objektindex, wird in
der Kommunikationsvariablen yylval.08J.index gespeichert.

Für normale Bezeichner jedoch muß Iookup u.U. mehrmals aufgerufen werden,
um auch für die Zwischenstufen eines Pfades die Objektindizes zu bestimmen. Die
erste Pfadkomponente wird mit mystrtok aus yytext extrahiert. Ist sowohl sie als
auch der Präfix leer, liegt ein Fehler vor und der Scanner bricht mit einer
Fehlermeldung ab. Ist nur die erste Pfadkomponente leer, handelt es sich bei dem
Bezeichner um eine Referenz (mit geﬁ) auf den statischen Kontext. Dessen
Objektindex wird also in yylval.OßJ.index eingetragen.

Der Rest des Pfades wird anschließend in einer Schleife komponentenweise mit
mystrtok aus yytext extrahiert, jeweils als Zwischenstufe mit lookup in der
Objekttabelle gesucht bzw. neu eingerichtet und als Präfix für den nächsten
Schleifendurchlauf verwandt, solange, bis der Pfad in all seine Komponenten zerlegt
ist. Der letzte Aufruf von lookup liefert dabei den Objektindex des Gesamtpfades.
Dieser wird in yylval.0BJ.index eingetragen.

Als Rückgabewert liefert der Scanner bei Bezeichnern von Pseudoobjekten den
jeweiligen Tokencode, bei self und normalen Objektbezeichnern den Code des Tokens
IDENTIFIER.

## 8.5 Parser parser.c

Der im Compiler—Backend benutzte Parser zur syntaktischen Analyse einer
DROOLY—Datei wird aus einer LALR(I)-Grammatik mit an den Ableitungsregeln
hängenden semantischen Aktionen vom Parsergenerator NEMO erzeugt. Der
eigentliche generierte Parser besteht aus einer C—Funktion yyparse, die durch
Anwendung der Ableitungsregeln versucht, ihre Eingabe zum Startsymbol
intermediate zu reduzieren. Im Zuge dieser Reduktion wird nach jeder angewandten
Ableitungsregel die zugehörige semantische Aktion ausgeführt. Die semantischen
Aktionen rufen geeignete Hilfsfunktionen zum Aufbau der Objekt— und der
Wertausdruckstabelle auf. Sie kommunizieren miteinander über den Wertestack des
Parsers, dessen Elemente vom gleichen Typ YYSTYPE wie die globale Variable
yylval sind. Zur Handhabung des Wertestacks stehen in den Semantikroutinen

Pseudovariablen zur Verfügung, die bei der Parsergenerierung in Zugriffe auf den
Wertestack übersetzt werden.

Der linken Seite einer Regel kann über die Pseudovariable 33 ein Wert
zugewiesen werden. Tritt das Nonterminalsymbol der linken Seite in der rechten
Seite einer zweiten Regel auf, kann auf diesen Wert ebenfalls über eine
Pseudovariable zugegriffen werden. Dazu sind alle Symbole und semanischen

Der DROOLY—Compiler (parser.c) 69



Aktionen der rechten Seite einer Regel durchnummeriert. 31 ist dabei die
Pseudovariable des am weitesten links stehenden Symbols. Der Wert der
Pseudovariablen eines Terminalsymbols ist derjenige Wert, den der Scanner beim
Erkennen dieses Terminalsymbols an die Variable yylval zugewiesen hat. Der Wert
der Pseudovariablen eines Nonterminalsymbols ist der Wert, der ss bei seiner
Reduktion zugewiesen wurde. Werden für unterschiedliche Regeln unterschiedliche
Alternativen der als Varianten deklarierten Pseudovariablen verwandt, kann es
durch inkonsistente Benutzung der Alternativen zu Typkonflikten kommen. Sowohl
Terminalsymbole als auch Nichtterminalsymbole werden darum gemäß den
Alternativen in YYSTYPE als von einem bestimmten Typ deklariert. Nemo kann so
die konsistente Benutzung der Alternativen überwachen und gibt ensprechende
Fehlermeldungen aus.

Der von NEMO erzeugte Parser simuliert einen endlichen Automaten mit einem

Stack. Diesem Automaten stehen prinzipiell vier unterschiedliche Aktionen zur
Verfügung.

- ACCEPT: Der Parser ist dazu ausgelegt. ein spezielles Symbol, das Startsymbol
(in DROOLY das Nonterminalsymbol intermediate) zu erkennen. Das Ende der
Eingabe ist im Fall der DROOLY-Eingabe das EOF—Zeichen. Falls die gelesenen
Token bis ausschließlich des EOF-Zeichens eine Struktur bilden, die auf daß
Start-Symbol paßt, kehrt die Parser—Funktion zu ihrem Aufrufer zurück, sie
akzeptiert die Eingabe.

- ERROR: Falls das aktuelle Eingabe-Token zusammen mit dem nächsten
Eingabe—Token keine legale Eingabe bildet, kann der Parser nicht mehr gemäß
seiner Spezifikation fortfahren. Er meldet einen Fehler und versucht, an einer
späteren Stelle in der Eingabe wieder aufzusetzen.

- SHIFT: Dies ist die häufigste der Parser-Aktionen. Basierend auf dem nächsten
Eingabe—Token wird der aktuelle Zustand auf den Stack geschoben und ein
neuer Zustand angesprungen.

- REDUCE: Diese Aktion verhindert, daß der Stack über alle Grenzen anwächst.
Wenn der Parser die rechte Seite einer Grammatik—Regel gesehen hat, holt er
die Anzahl Zustände vom Stack, die den Symbolen der rechten Seite der
Grammatik-Regel entsprechen. Mit dem nun freigelegten Zustand und dem
Symbol der linken Regelseite wird ein neuer Zustand angesprungen [YACC].

NEMO faßt einige dieser Aktionen zusammen, was in diesem Zusammenhang
nicht berücksichtigt zu werden braucht. Bei der Ableitung nach den Regeln der
Eingabegrammatik kann es zu zwei Konflikttypen kommen:

— SHIFT/REDUCE: YACCS wie NEMOS Strategie ist in diesem Fall das SHIFTen.

- REDUCE/REDUCE: YACCS wie NEMOS Strategie ist in diesem Fall, nach der
früheren Regel in der Eingabegrammatik abzuleiten.

Die DROOLY—Grammatik enthält einen SHIFT/REDUCE—Konflikt. Dieser wird von
der eingebauten Konfliktlösungsstrategie NEMOS derart aufgelöst, daß zunächst
geSHIFTet wird. Diese Strategie führt im Zusammenhang mit den verursachenden
Regeln genau zu dem gewünschten Verhalten, daß nämlich ein gﬁ-Zweig dem
letzten i_f in der Eingabe zugeordnet wird.

Der DROOLY—Compiler (parser.c) 70



Umgebung

Die Parserroutine yyparse wird direkt von main ohne Parameter aufgerufen.
Bei korrekter Syntax (die Eingabe konnte zum Startsymbol intermediate reduziert
werden) liefert yyparse 0 zurück, sonst —1. Yyparse wird von verschiedenen
Modulen unterstützt. Grundlegend ist die Zusammenarbeit mit dem Scanner yylex.
der auf Anforderung ein neues Token aus der DROOLY—Eingabe liefert. Als zentrale
Funktion des Compiler—Backends ruft yyparse darüber hinaus folgende Routinen aus
anderen Modulen:

Iink_object ( object.c ) zum Einbinden von neuen Objekten in eine
Komponentenhierarchie

gen_pseudo ( object.c ) zur Erzeugung eines Objektbezeichners für eine RELATION

msg ( object.c ) für den Aufbau der Objekttabelle. Dabei werden die
Nachrichtenschlüssel m_PSPEC, m_GSPEC, m_GFIRSTCOMP, m_GNEXTCOMP,
m_INSTPART, m_INCDEGREE, m_PAPARM, m_PFPARM, m_PEXPR, m_PQUANT,
m_POPS, m_PNEXTOUTER, m_ADDREL, m_GPREF'IX, und m_GSUFFIX benutzt.

is_DECL, is_RELATION, is_CLASSDEF( object.c ) zur Überprüfung des spec-Feldes
eines Objekteintrages

max_comm_path ( object.c ) zur Ermittlung des längsten gemeinsamen Pfadpräfixes
zweier Objektbezeichner

put_outermost ( object.c ) zur Einrichtung eines Verweises auf die Wurzel eines
Relationenbaums

merge ( compare.c ) zur Initiierung eines Vergleiches zweier Objektstrukturen und
Einrichtung von merges

incr_relat, decr_relat( object.c ) zur Markierung der Wurzel eines Relationenbaums

incr_expr, decr_expr ( expr.c ) zur Markierung der äußersten Ebene eines
Wertausdruckes

gen_expr ( expr.c ) zur Erzeugung und Initialisierung eines neuen atomaren
Wertausdruckes

emsg ( expr.c ) für den Aufbau der Wertausdruckstabelle. Dabei werden die
Nachrichtenschlüssel m_ASSYMETRIC, m_COND_AND, m_COND_OR,
m_C_PROPERTY, m_AND, m_OR, m_PATTRIB und m_VALUE benutzt.

error ( error.c ) zur Angabe von Fehlermeldungen und u.U. anschließendem
Abbruch der Übersetzung

Struktur

Das Parser—Modul exportiert als einzige Funktion yyparse. Die NEMO—Eingabe
zur Erzeugung des Parser-Moduls besteht grob betrachtet aus drei Teilen. Der erste
Teil ist reiner C-Programmtext. Hier werden die nötigen Header-Dateien inkludiert

und folgende globale Variable zur Kommunikation zwischen den semantischen
Aktionen deklariert:

Der DROOLY—Compiler (parser.c) ' 71



act_class ( exportiert nach scanner.c ) enthält den Objektindex des statischen
Kontextes und wird im Scanner benutzt, um einen dazu relativen Pfad in
einen absoluten Pfad umzuwandeln.

tmpl_class dient zur Zwischenspeicherung des Wertes von act_class. Dies ist an
drei Stellen im Parser notwendig, um dem Scanner temporär einen anderen
statischen Kontext vorzutäuschen:

— Bei der Ableitung zu forward soll das erzeugte PROPERTY—Objekt als
Komponente an das bezeichnete Objekt angehängt werden und nicht an
die umgebende Objektklasse. Für die Dauer der Ableitung zu
property_type wird daher act_class auf den Index des bezeichneten
Objektes gesetzt.

- Innerhalb einer Ableitung zu property kann es notwendig sein, act_class
ein zweites Mal umzusetzen. Handelt es sich nämlich bei der PROPERTY
um eine Geometrieeinschränkung (shape i_s <Geometrieklasse>), so soll die
Geometrieklasse nicht als Komponente an das bezeichnete Objekt
angehängt werden, sondern soll global bleiben. Um diesen Effekt zu
erzwingen, wird act_class für das Scannen des Geometrieklassen-
bezeichners auf NIL gesetzt, so daß der Scanner für die Geometrieklasse
einen globalen Objekteintrag anlegt.

- Die Klassenbezeichner hinter dem Schlüsselwort (class werden implizit als
global angenommen. Um dem Scanner diese Sonderbehandlung mitzuteilen,
wird ebenfalls für das Scannen des Klassenbezeichners act_class auf NIL
gesetzt.

act_object dient während einer Objektdefinition (Definition von STDDEFs, PARTS,
PARAMS, SETS, (C_)RELATIONS‚ (C_)PROPERTIES und CLASSTESTS) zur
Speicherung und Übermittlung des Objektindizes zwischen semantischen
Aktionen auch über verschiedene Hierarchiestufen von Ableitungen hinweg.

expr_depth wird mit 0 initialisiert. Zu Beginn der Ableitung eines Wertausdruckes
wird expr_depth um 1 inkrementiert, nach erfolgter Ableitung wieder um 1
dekrementiert. Dadurch zeigt ein Wert größer als 0 an, daß gerade ein
Wertausdruck abgeleitet wird. Diese Information ist für die Ableitung von
c_properties und c_reiations als Bedingungen in Wertausdrücken wichtig, da
diese sich syntaktisch nicht von properties und relations außerhalb von
Wertausdrücken unterscheiden.

relat_depth wird mit 0 initialisiert. Zu Beginn der Ableitung eines Operanden
einer relation wird relat_depth um 1 inkrementiert, nach erfolgter Ableitung
wieder um 1 dekrementiert. Dadurch zeigt ein Wert von 0 die Wurzel eines
Relationenbaums an und die nötigen Aktionen können ausgeführt werden.

global dient zur Übermittlung des Wertes von yylval.03J.is_global in die
semantischen Aktionen bei der Ableitung zu property innerhalb der Ableitung
zu forward. Dort wird danach entschieden, ob ein C_PROPERTY den Wert von
main_class als Klassenindex zugeordnet bekommt. Dieser wiederum wird in der
Funktion copy_expr bei der Vererbung eines Wertausdruckes bei der Erzeugung
neuer Referenzen auf C_PROPERTIES benutzt.

yytext ( exportiert nach scanner.c, main.c ) dient zur internen Kommunikation des
gelesenen Lexems zwischen Scanner und Parser. Im Hauptmodul wird der für
yylex allozierte Speicherplatz als Puffer zum Aufbau der Funktionsnamen
mißbraucht.

Der DROOLY—Compiler (parser.c) ' 72



yypereo_—Jink_object (object.c)
j—-gen_pnudo (ob|ect.c)
i—.:1nq (object.c)
Hmeg (object.c)
——put_outormoet (object.c)
——mex_comm_peth (object.c)
—°ie_DECL (object.c)
—°i-_RELATION (object.c)
%._CLASSDEF (object.c)
‘—-incr_relet (object.c)
—decr_relat (object.c)
—-incr_expr (expr.c)
“"decr_expr (expr.c)
L.gen_expr (expr.c)

peee2—.melge



Abb. 8.6: Die Struktur des Parsers

Verfahren

Die Aufgabe des Parsers besteht im Einrichten der durch die DROOLY—Eingabe
spezifizierten Objekte in der Objekttabelle und anhängender Ausdrücke in der
Ausdruckstabelle. Daneben leitet der Parser auch die Mechanismen zur Vererbung
von Unterstrukturen und die Identifikation von Objekten ein.

Da die einzige Kontextinformation für den Scanner im Wert der globalen
Variable act_class liegt, generiert dieser für jeden gelesenen Bezeichner mithilfe
von lookup einen Objekteintrag in der Objekttabelle. Alle typspezifischen Felder
bleiben darin unbelegt, da der Scanner den Objekttyp ja nicht kennt. Erst der
Parser kann aus seinem Wissen über den Kontext eines Objektbezeichners den
Objekttyp bestimmen und die typspezifischen Felder mit Werten füllen. Die
einzelnen Objekttypen werden im folgenden einzeln betrachtet und die Zuweisungen
an die Felder des Objekteintrages beschrieben.

Objekttyp LAYERDEF

Bei diesem Objekttyp muß lediglich das spec-Feld auf LAYERDEF gesetzt
werden.

Objekttyp CLASSDEF

Bei diesem Objekttyp wird zunächst das spec—Feld auf CLASSDEF gesetzt. Der
globalen Variable act_class wird der Objektindex des Klassenobjektes zugewiesen,
um dem Scanner den aktuellen statischen Kontext anzuzeigen. Hat die Klasse
formale Parameter, werden diese in der Ableitung zu fparm_1ist in der Reihenfolge
ihres Auftretens in der Parameterliste einzeln erfaßt. Der Zähler fparm_ix in der
Klassenobjektstruktur wird für jeden formalen Parameter um 1 inkrementiert.
Gleichzeitig erhält der gleichnamige Zähler in der Objektstruktur des formalen
Parameters den neuen Wert als seinen Positionsindex in der Liste formaler
Parameter zugewiesen. Dies erleichtert später einen direkten Zugriff auf den
zugehörigen aktuellen Parameter. Bei der Abarbeitung der Liste formaler Parameter
legt der Scanner schon jetzt für jeden formalen Parameter einen Objekteintrag an.
Die Zuweisung der typspezifischen Werte (ausgenommen fparm_ix) erfolgt aber erst
bei der Abarbeitung der Deklaration des Objektes als PARAM.

Der DROOLY—Compiler (parser.c) ' 73



Es folgt der Deklarationsteil für Unterstrukturen. An dieser Stelle sollen nur
die für das Klassenobjekt relevanten Punkte der Ableitung zu declarations
betrachtet werden. Jede vorgenommene Deklaration einer Unterstruktur erhöht den
Grad des Klassenobjektes (im Feld degree seiner Struktur) um eins. Der Scanner
konstruiert für jede deklarierte Unterstruktur einen Bezeichner aus dem Bezeichner
der Klasse und dem Bezeichner der Unterstruktur und legt ein Objekt mit diesem
Bezeichner in der Objekttabelle an. Erst im Parser jedoch wird dieses Objekt durch
einen Aufruf von iink_object in die Komponentenstruktur des aktuellen
Klassenobjektes eingehängt. Dabei wird die erste Unterstruktur an das compHd—Feld
des Klassenobjektes angehängt; jede weitere Unterstruktur wird an das next_comp—
Feld der zuletzt deklarierten Unterstruktur angehängt. Nach der Deklaration der
letzten Unterstruktur werden die PARTS und SETs instanziiert, d.h. die
Unterstrukturbäume ihrer Klassen werden kopiert und auch an _si_e_ angehängt. Das
Klassenobjekt merkt davon jedoch nichts, da sich alle Änderungen auf die
Komponentenbäume seiner Unterstrukturen beziehen.

Nachdem alle Unterstrukturen deklariert sind, können PROPERTIES, RELATIONS
und merges auf den Unterstrukturen des Klassenobjektes definiert werden. Wurden
keine Unterstrukturen deklariert, können nur PROPERTIE5 des Klassenobjektes
festgelegt werden. Für eine PROPERTY wird schon im Scanner ein eigener
Objekteintrag angelegt, falls ein solcher noch nicht existierte. Der Bezeichner eines
PROPERTY—Objektes besitzt als Pfadpräfix den Index des bezeichneten Objektes, als
Suffix den jeweiligen PROPERTY—Type. Zur Unterscheidung von Symboltabellenindizes
wird der Suffix jedoch negativ gespeichert. Handelt es sich um eine PROPERTY mit
einem neuen PROPERTY—Typ, wird das PROPERTY-Objekt mithilfe eines Iink_object—
Aufrufes im Parser in die Komponentenstruktur des durch seinen Pfadpräfix
bezeichneten Objektes eingehängt. Dieses Objekt besitzt in seiner Struktur einen
Vektor properties mit 5 Komponenten, der zu jedem PROPERTY-Type den
Objektindex des zugehörigen PROPERTY—Objektes enthält. Für ein neues PROPERTY—
Objekt wird dessen Index in diesem Vektor abgelegt, um später ohne Suche darauf
zugreifen zu können.

Ist bereits ein Eintrag für einen bestimmten PROPERTY—Typ zu einem Objekt
erfolgt, wird kein neuer Eintrag in der Objekttabelle vorgenommen. Stattdessen wird
der Wertausdruck für die bisherige PROPERTY mit einem ai—Operator mit den
Wertausdruck der neuen PROPERTY vernüpft. Der resultierende Wertausdruck
überschreibt den Ausdruck im alten PROPERTY—Objekt.

Für eine zweistellige RELATION wird ebenfalls ein eigener Objekteintrag
angelegt. Dies kann jedoch erst in Parser selbst geschehen. Um RELATIONS so lokal
wie möglich an ihre Operanden zu binden, wird dazu durch Aufruf der Funktion
max_comm_path der längste gemeinsame Pfadpräfix beider Operanden der RELATION
ermittelt und als Präfix des Relationsbezeichners benutzt. Für den Suffix wird in
der Funktion gen_pseudo eine global eindeutige, negative Kodierung für jede
RELATION erzeugt. Mit dem so gewonnenen Präfix und Suffix wird mithilfe von
lookup ein neues RELATION—Objekt erzeugt und von iink_objekt in die
Komponentenstruktur seines Klassenobjektes eingehängt.

RELATIONS können nicht nur als einfache zweistellige RELATION auftreten,
sondern auch als hierarchischer Relationenbaum. Als Operanden einer RELATION
sind neben deklarierten Unterstrukturen also wieder RELATIONS zulässig. Der
Parser erkennt, ob er gerade die Wurzel eines Relationenbaums bearbeitet, am Wert
einer globalen Variable relat_depth, die mit 0 initialisiert ist, zu Beginn der
Ableitung einer zweistelligen RELATION um 1 inkrementiert und am Ende ihrer
Ableitung wieder dekrementiert wird. Ein Wert von 1 zeigt damit dem Parser an,
daß er die Wurzel eines Relationenbaums bearbeitet. Um von einem Objekt leicht zu
den Wurzeln dieser Bäume in der Komponentenstruktur des Objektes zu gelangen,

Der DROOLY—Compiler (parser.c) 74



werden die Wurzelobjekte untereinander und mit ihrem Klassenobjekt über die
next_outermost-Felder der Objektstruktur verkettet.

Merges dürfen erst bearbeitet werden, wenn die vollständige Hierarchie mit
allen deklarierten Unterstrukturen und allen RELATIONS der betroffenen Objekte
aufgebaut ist. Die Objektindizes der zu identifizierenden Objekte werden daher in
einem Temporärfile gesammelt und erst nach völliger Abarbeitung der DROOLY—Datei
wieder eingelesen, um die Identifikationen zu erzeugen (siehe Abschnitt 8.8).
Werden zwei (Klassen—) Objekte identifiziert und ist die Identifikation erfolgreich
verlaufen, erhält das linke Objekteinen neuen Eintrag (den Objektindex des
zweiten Objektes) in seinem merges-Vektor und der zugehörige merge_cnt—Zähler
wird um 1 inkrementiert. Entsprechend erhält das rechte Objekt einen neuen
Eintrag in seinem lmerges—Vektor und sein lmerge_cnt—Zähler wird um 1
inkrementiert.

Objekttyp STDDEF

Standardobjektdeklarationen unterscheiden sich in DROOLY syntaktisch nur
durch ein anderes Schlüsselwort und durch die zusätzliche Angabe einer
Objektzuweisung in Form eines Klassenaufrufes von den "normalen" Objekten.
Semantisch entspricht die Deklaration eines Standardobjektes der Bildung einer
Unterklasse in objektorientierten Sprachkonzepten. Jedes Standardobjekt besitzt in
DROOLY eine eindeutig bestimmte Objektzuweisung ("Oberklasse"), multiple
inheritance im Sinne objektorientierter Konzepte ist nicht möglich. Innerhalb der
Objektstruktur wird die Klasse in einem Vektor classes gespeichert. Da die Klasse
eines Standardobjektes eindeutig bestimmt ist, enthält dieser Vektor nur ein
Element (der zugeordnete Zähler class_cnt erhält den Wert 1). Alle übrigen Felder
der Objektstruktur werden wie beim Objekttyp CLASSDEF benutzt.

Ein Standardobjekt erbt die Komponentenstruktur seiner Oberklasse. Trägt
diese formale Parameter, so ﬂi_ß das Standardobjekt die Klasse jeder
parameterdeklarierten Unterstruktur seiner Oberklasse durch Angabe je eines
zugehörigen aktuellen Parameters eindeutig festlegen. Ein Standardobjekt kann
keine neuen Unterstrukturen deklarieren, wohl kann es aber neue PROPERTIES und
RELATIONS auf den ererbten Unterstrukturen definieren. Dafür gilt sinngemäß das
beim Objekttyp CLASSDEF Gesagte.

Objekttyp PART

Objekte vom Typ PART sind festtypdeklarierte Unterstrukturen eines
Klassenobjektes. Die Einrichtung eines entsprechenden Eintrags in der Objekttabelle
kann drei verschiedene Ursachen haben:

— Deklaration in DROOLY. Eine Unterstruktur vom Typ PART wird im DROOLY-
Programmtext deklariert. Dabei wird seine Klasse, falls erforderlich durch
Angabe von aktuellen Parametern eindeutig festgelegt. Durch eine solche
Deklaration wird eine Instanz der Klasse erzeugt. Das PART erbt die gesamte
Komponentenstruktur seiner Klasse.

Der DROOLY—Compiler (parser.c) 75



- Einfache Vererbung. Wird ein Objekt vom Typ PART oder SET deklariert, erhält
es eine eindeutige Klassenangabe. Das Objekt erbt bei seiner Deklaration die
gesamte Komponentenstruktur seiner Klasse, also insbesondere alle PARTS und
SETS (PARAMS werden bei der Vererbung immer in PARTS umgewandelt; siehe
nächster Punkt). Die Vererbung wird realisiert durch ein rekursives Kopieren
aller Objekte, die über die compHd— und next_comp-Verkettungen am
Klassenobjekt hängen. Die neu erzeugten Objekte erhalten jeweils als
Pfadpräfix den Pfad des PARTS zugewiesen.

- Umwandlung eines PARAMS bei der Vererbung. Die Klasse einer parametertyp-
deklarierten Unterstruktur (Objekttyp PARAM, siehe Abschnitt 8.6) wird durch
Angabe aktueller Parameter aus ihrer Alternativliste ausgewählt und dadurch
eindeutig bestimmt. Dies kann entweder durch Instanziierung bei der
Deklaration eines Parts oder durch Unterklassenbildung bei der Deklaration
eines Standardobjektes geschehen. Der Objekttyp des PARAMS wird in PART
umgewandelt. Die Klasse des Objektes ist eindeutig bestimmt, es erbt damit
die gesamte Komponentenstruktur dieser Klasse.

Egal auf welche dieser Arten das Objekt enstanden ist, seinem spec—Feld ist
PART zugewiesen. Im classes—Vektor wird wie bei STDDEFS die einzige Klasse des
Objektes eingetragen, der zugehörige Zähler class_cnt hat den Wert 1. Durch einen
Aufruf von 1ink_object wird das PART in die Komponentenstruktur seines
Aggregates eingehängt und das next_comp—Feld entsprechend gesetzt. Die
Unterstruktur seiner Klasse erbt das PART erst am Ende des Deklarationsteils
seines Aggregates. Durch diese Verzögerung können CLASSTEST-Bedingungen
aufgelöst werden, die die Klasse anderer Unterstrukturen desselben PARTS abfragen
(siehe Abschnitt 8.7). Dort erbt jeder Sohn des Aggregates die Komponentenstruktur
seiner Klasse durch Aufruf von m_INSTPART (siehe Abschnitt 8.6).

Objekttyp PARAM

Der Wert des spec-Feldes für Objekte vom Typ PARAM ist PARAM. Dieser
Objekttyp kann nur durch direkte Parametertypdeklaration in DROOLY erzeugt
werden. Sein besonderes Kennzeichen ist, daß der Klassenvektor classes nicht eine
einzige Klasse enthält, sondern mehrere Alternativen. Der Zähler class_cnt zeigt
die exakte Anzahl gespeicherter Alternativen an. Jede Klasse belegt eine
Vektorkomponente. In deren Struktur ist jeweils der Objekttabellenindex des
Klassenobjektes, u.U. zusammen mit den erforderlichen aktuellen Parametern
abgelegt. Die Anzahl der aktuellen Parameter muß dabei gleich sein mit der Anzahl
formaler Parameter der Klasse. Ein Zähler aparm_cnt gibt für jede Komponente des
Klassenvektor die Anzahl aktueller Parameter an. Die aktuellen Parameter sind als
Index in die Alternativklassen—Liste der parametertypdeklarierten Komponente
angegeben, die zum korrespondierenden formalen Parameter des Klassenobjektes
gehört.

Ein PARAM erbt bei seiner Deklaration keine Unterstrukturen, da seine Klasse
nicht eindeutig bestimmt ist. Es können aber natürlich PROPERTIES dieses PARAMS
definiert sein oder RELATIONS, die dieses PARAM als Operand enthalten. Die
PROPERTIES werden in jedem Fall als Komponenten über die compHd—/next_comp—
Verkettung an das PARAM angehängt und in seinem properties—Vektor referenziert.
Es werden dagegen nur solche RELATIONs als Komponenten an das PARAM
angehängt, deren beide Operanden gleich dem PARAM sind. Andere RELATIONS
werden in die Komponentenstruktur des Aggregates eingehängt.

Der DROOLY—Compiler (parser.c) 76



Obj ekttyp SET

Objekte vom Typ SET sind mengentypdeklarierte Unterstrukturen eines
Klassenobjektes. Die Einrichtung eines entsprechenden Eintrages in der
Objekttabelle kann wie bei einem PART drei verschiedene Ursachen haben:

- Deklaration in DROOLY. Eine Unterstruktur vom Typ SET wird im DROOLY—
Programmtext deklariert. Auch bei einem SET wird dabei die Klasse falls
erforderlich durch Angabe von aktuellen Parametern eindeutig festgelegt. Das
SET erbt die gesamte Komponentenstruktur seiner Klasse. Dabei werden aber
alle PARTs in SETs umgewandelt. Die Klassen von PARAMS müssen durch
aktuelle Parameter eindeutig bestimmt werden. Bei der Vererbung werden auch
PARAMS in SETS umgewandelt.

— Einfache Vererbung. Wird ein Objekt vom Typ PART deklariert, erhält es durch

Vererbung die gesamte Komponentenstruktur seiner Klasse, also insbesondere
SETs.

- Umwandlung eines PARAMS bei der Vererbung. Die Klasse einer parametertyp—
deklarierten Unterstruktur (Objekttyp PARAM, s.u.) wird durch Angabe
aktueller Parameter aus ihrer Alternativliste ausgewählt und dadurch
eindeutig bestimmt. Durch Instanziierung bei der Deklaration eines SETs
werden die ehemaligen PARAMS in SETS umgewandelt.

Bei der Deklaration vom SET—Objekten können neben der Klassenangabe auch
Einschränkungen bezüglich der Mengenkardinalität gemacht werden. Die Angabe
erfolgt in DROOLY in Form eines Wertausdruckes. In der Tabelle für Wertausdrücke
werden hierfür die entsprechenden Einträge gemacht und vom SET—Objekt auf die
Wurzel des Ausdrucksbaums mit dem expr-Feld verwiesen. Erbt ein SET-Objekt ein
SET als Komponente von seiner Klasse, werden die Kardinalitätsausdrücke beider
Mengen durch einen a_nd-Operator vernüpft. Die Komponente erhält den
resultierenden Kardinalitätsausdruck zugewiesen. SET—Objekten, die durch
Vererbung aus PARTS oder PARAMS entstanden sind, wird der Kardinalitätsausdruck
ihres neuen Aggregates zugewiesen.

Objekttyp GEOCLASS

Durch die Einrichtung eines eigenen Objekteintrages mit spec-Wert GEOCLASS
muß in der Wertausdruckstabelle kein neuer Modus bei der Vernüpfung von
Geomtrieklassen mit einem*g—Operator eingeführt werden. Der Verweis auf eine
Geometrieklasse wird wie bei einem Verweis auf eine PROPERTY oder RELATION in
Bedingungen (s.u.) einfach durch den entsprechenden Objekttabellenindex realisiert.
Objekteinträge vom Typ GEOCLASS werden vom Scanner beim Einlesen einer shap —
Anweisung erzeugt. Sie werden allerdings nicht in die Komponentenstruktur irgend
eines Objektes eingehängt. Das prefix-Feld einer GEOCLASS ist immer NIL.

Objekttyp SHAPE, Objekttyp PROPERTY

Die Struktur von Objekteinträgen für diese Typen ist völlig identisch. Neben
dem Wert des spec-Feldes (SHAPE bzw. PROPERTY) enthalten beide in ihrem expr—
Feld einen Verweis in die Ausdruckstabelle. Beide werden mit einem Aufruf von
Iink_objekt in die Komponentenstruktur ihres Aggregates eingehängt und zusätzlich
durch die entsprechende Komponente des property—Vektors referenziert. Aus dem
suffix-Feld eines PROPERTIES geht direkt sein PROPERTY—Typ hervor, da der

Der DROOLY—Compiler (parser.c) ' 77



Scanner die entsprechende Tokenkodierung negativ als Wert des suffix—Feldes
einsetzt.

Obj ekttyp C_PROPERTY

C_PROPERTY—Objekte (Wert des spec—Feldes: C_PROPERTY) dienen zur
Speicherung einer Bedingung über eine PROPERTY eines Objektes. Sie werden wie
PROPERTIES in die Komponentenstruktur dieses Objektes eingehängt. Aus
Kompatibilität zu Objekten vom Typ CLASSTEST erhalten sie den aktuellen
statischen Kontext (das durch die globale Variable act_class referenzierte Objekt)
als Klasse in ihrem classes-Vektor eingetragen.

Objekttyp RELATION, Objekttyp SET_RELATION

Komplexe Topologieausdrücke aus DROOLY werden bei der Übersetzung in die
Tabellen in atomare Ausdrücke zerlegt.

Ein Teilausdruck vom Typ RELATION beschreibt unmittelbar ein mögliche
Relation von Layoutobjekten. Ihm ist im allgemeinen eine Flächenlnterpretation im
Layout zugeordnet: Davon ausgenommen sind Teilausdrücke mit dem Operator touch,
denen im Layout nur die Berührungsstrecke (oder die Berührungspunkte) der
beteiligten Layoutinstanzen zugeordnet sind sowie Ausdrücke mit dem Operator
out angle, die überhaupt keine Topologieeigenschaft beschreiben. Ein Teilausdruck
vom Typ SET_RELATION hat nur im Zusammenwirken mit seinen abhängigen
RELATION-Objekten eine Flächeninterpretation im Layout. Aufgrund ihrer
syntaktischen Ähnlichkeit werden jedoch beide Objekttypen in der Objekttabelle
fast gleich behandelt.

Jeder atomare Ausdruck erhält einen eindeutigen Bezeichner und einen eigenen
Eintrag in der Objekttabelle. Der Bezeichner setzt sich aus einem Pfadpräfix. der
aus den Operanden des Ausdruckes ermittelt wird, und einer generierten Kennung
zur global eindeutigen Identifizierung zusammen. Diese Kennung wird in der
Funktion gen_pseudo durch Weiterzählen eines statischen Zählers bei jedem Aufruf
generiert. Die Kennung wird durch Voranstellen der Buchstaben "ps" zu einen
gültigen Bezeichner erweitert und als solcher in der Symboltabelle abgelegt.

Jeder atomare Topologieausdruck besitzt einen Operator und zwei Operanden.
Bei RELATION—Objekten (Wert des spec-Feldes: RELATION) dürfen beide Operanden
sowohl Objekttabellenindizes von (SET_)RELATION—Objekten als auch von
deklarierten Unterstrukturen der aktuellen Klasse (erlaubte Objekttypen sind PART,
PARAM, SET) sein. Bei SET_RELATION—Objekten (Wert des spec—Feldes:
SET_RELATION) muß der erste Operand der Objekttabellenindex einer deklarierten
Unterstruktur der aktuellen Klasse sein. Der zweite Operand kann entweder der
Index eines weiteren SET_RELATION—Objektes sein, falls im Quellausdruck aus
DROOLY mehrere Quantoren geschachtelt auftreten oder er kann der Index eines
RELATION-Objektes sein.

Wird eine deklarierte Struktur als Operand einer (SET_)RELATION in die opl-
und/oder op2—Felder eingetragen, wird gleichzeitig die RELATION in eine Liste
eingehängt, über die man leicht alle RELATIONS finden kann, die eine bestimmte
Struktur als Operand besitzen. Die erste solche RELATION wird von der
deklarierten Struktur über ihr relatioan—Feld referenziert. Die Verkettung
zwischen den RELATIONS findet für den ersten Operanden über das next_relationl—
Feld, für den zweiten Operanden über das next_relation2—Feld der RELATIONS
statt.

- ."a



Der DROOLY—Compiler (parser.c) ' 78



Zu beachten ist, daß sowohl in DINGO—II als auch in DROOLY Quantoren immer
ganz links im Ausdruck stehen müssen. Im DROOLY—Parser wird dies durch eine
entsprechende Anordnung der Regeln erzwungen. In der Objekttabelle kann aber die
Reihenfolge bei einer Optimierung durch Herausziehen von Schleifeninvarianten
(siehe Kapitel 10.) durchaus auch so sein, daß ein RELATION—Objekt ein
SET_RELATION-Objekt als Operanden hat.

Jedes (SET_)RELATION-Objekt wird in der Objekttabelle durch Aufruf der
Funktion Iink_objekt in die Komponentenstruktur eines deklarierten Objektes
eingehängt. Dieses muß aber nicht mit dem statischen DINGO—II- oder DROOLY—
Kontext übereinstimmen. Das passende Aggregat für eine (SET_)RELATION wird
vielmehr von der Funktion max_comm_path aus den Pfaden ihrer Operanden
ermittelt. Der Pfadpräfix für das (SET_)RELATION—Objekt ergibt sich als der
maximale, gemeinsame Pfadpräfix seiner Operanden. Auf diese Weise wird eine
(SET_)RELATION immer tiefstmöglich in einer Komponentenhierarchie eingehängt
(Prinzip der Lokalität).

Einzelne atomare Ausdrücke eines zusammengesetzten Ausdruckes können damit
durchaus auch an verschiedenen Aggregaten hängen. Um auf einfache Weise aus
einer Referenz auf einen atomaren Teilausdruck den gesamten Topologieausdruck
rekonstruieren zu können, trägt jeder Teilausdruck in der Objekttabelle einen
Verweis auf die Wurzel seines Relationenbaums im outermost—Feld. Die einzelnen

Wurzeln werden von ihrem Aggregat über die next_outermost—Felder miteinander
verkettet.

SET_RELATIONS stellen nur ein syntaktisches Hilfskonstrukt zur Integration
von quantifizierten Ausdrücken zur Verfügung. Sie beschrieben für sich genommen
keine flächigen Layoutobjekte. Daher kann einer SET_RELATION kein Wertausdruck
zugeordnet werden. Einer RELATION dagegen kann, wenn ihr Ursprung eine
zweistellige Design Rule ist, ein Wertausdruck zugeordnet werden. Dies geschieht
durch einen Verweis über das expr—Feld in die Ausdruckstabelle.

Objekttypen C_RELATION, C_SET_RELATION

Objekte der Typen C_RELATION und C_SET_RELATION unterscheiden sich von
RELATION-Objekten bzw. von SET_RELATION nur durch die Belegung ihrer spec—
Felder (RELATION bzw. C_SET_RELATION). Sie werden über die selben
Grammatikregeln erzeugt, wobei jedoch der Wertausdruck bei C_RELATION—Objekten
nicht leer sein darf. Durch den Wert der globalen Variable expr_level kann der
Parser entscheiden, welcher Objekttyp im einzelnen vorliegt. Semantisch stellen
diese Objekttypen Bedingungen dar, die in bedingten Design Rules benutzt werden
können. Als Bedingungen haben sie keine Flächeninterpretation im Layout.

Objekttyp CLASSTEST

Objekte vom Typ CLASSTEST (Wert des spec—Feldes: CLASSTEST) sind neben
C_PROPERTIES, C_RELATIONS und C_SET_RELATIONS eine weitere Möglichkeit,
Bedingungen in bedingten Design Rules zu konstruieren. Mit ihrer Hilfe kann die
Klassenzugehörigkeit und insbesondere deren Belegung mit aktuellen Parametern
überprüft werden. Bis auf den unterschiedlichen Wert des spec—Feldes entspricht
die Belegung des Objekttabelleneintrages der einer deklarierten Unterstruktur, so
daß bei der Auswertung eines CLASSTESTS ein einfacher Vergleich möglich ist.

1,_1

.Té\

Der DROOLY-Compiler (parser.c) ‘ 79



Objekte dieses Typs werden nicht in die Komponentenstruktur eines Aggregates
eingehängt. Stattdessen werden sie ausschließlich über den zugehörigen
Wertausdruck referenziert. Objekte vom Typ CLASSTEST stellen nur eine
Hilfskonstruktion während der Übersetzung dar. Wird eine‘Klasse durch Deklaration
einer Unterstruktur unter Angabe der erforderlichen aktuellen Parameter
instanziiert, werden alle PARAMS dieser Klasse in PARTS umgewandelt, da ihre
Klasse durch die aktuellen Parameter eindeutig bestimmt wird. Ein CLASSTEST,
welches die Klasse eines solchen ehemaligen PARAMS abfragt. kann ausgewertet und
durch seinen then— bzw. _eﬁ—Zweig ersetzt werden.

## 8.6 Objekte object.c

Das Modul object.c des Compiler—Backends stellt die Objekttabelle als
abstrakten Datentyp zur Verfügung. Darin definiert ist die Datenstruktur der
Objekttabelle als Vektor von Elementen des oben beschriebenen Typs objectT. Jedes
Vektorelement speichert einen Eintrag in der Objekttabelle. Die Vektorgröße ist
statisch deklariert. Da jedoch jede Anforderung und "Inbetriebnahme" eines neuen
Objekteintrages ausschließlich über einen Aufruf der Funktion gen_obj_entry
abgewickelt wird, ist für eine Erweiterung auf dynamische Reallozierung bei
drohendem Tabellenüberlauf nur eine entsprechende Anpassung dieser einen
Funktion notwendig.

Umgebung

Neben der Kommunikation mit dem Parser über die exportierten Funktionen
werden die drei Dateideskriptoren tabout zur Ausgabe einer lesbaren Form der
Objekttabelle, trfout zur Ausgabe einer DROOLY—ähnlichen Form der Objekttabelle
(7) und tecout zur Ausgabe der Technologiedatei aus dem Hauptmodul importiert.
Aus dem Parser importiert wird der globaler Zähler relat_depth, der jedoch nur in
den Funktionen incr_relat und decr_reiat manipuliert wird. Es werden explizit
keine Datenstrukturen aus diesem Modul exportiert. Auf die Objekttabelle kann
außerhalb des Moduls object.c nur über die exportierten Funktionen zugegriffen
werden. Als Parameter zur Referenz auf Einträge in die Objekttabelle erwarten alle
diese Funktionen einen positiven, ganzzahligen Wert. Da der nullte
Objekttabelleneintrag nicht benutzt wird, dient der Index 0 als unspezifizierter
(NIL-) Index. Eine entsprechende symbolische Konstante ist in der Kopfdatei
const.h definiert.

Struktur

Der direkte Zugriff auf die Felder eines Objekteintrages erfolgt innerhalb des
Moduls ausschließlich über einen Satz von Makros, wobei für jedes Feld in der
Struktur objectT ein entsprechendes Makro (o_...) definert ist. Außerhalb des
Moduls object.c sind diese Makros nicht zugänglich. Als zentrale Schnittstelle zu
den in der Objekttabelle gespeicherten Informationen dient die Funktion msg, die
gleichsam eine Nachricht an ein bestimmtes Objekt in der Objekttabelle absetzt.
Diese Funktion erwartet als ersten Parameter den Index des betroffenen Objektes
(der Empfänger der Nachricht) und als zweiten Parameter einen Schlüssel, der die
Art der Aktion auf dem Objekt spezifiziert (der Selektor). Die erlaubten Schlüssel
sind in dem Modul token.c definiert und werden bei der Übersetzung des Compiler—
Backends automatisch (mittels eines Awk—Skriptes) in die Kopfdatei specs.h



(7) Die DROOLY-ähnliche Ausgabeiorm eignet sich deshalb besser zu Vergleichszvecken bei der systematischen Überprüfung in einer 'Destbibliothek. seilreierenzierte
Objekte ohne.hngabe des Tabellenindizes protokolliert werden. Damit wird diese Ausgabeiorm in gewissem Umfang iormatunabhängig im Sinne thus.

'.*:'

‘..„n

Der DROOLY—Compiler (object.c) 80



übernommen. Die msg—Funktion überprüft nur, ob der übergebene Objektindex und
der Schlüssel im erlaubten Bereich liegen und ruft dann eine Hilfsfunktionen auf,
die die eigentlichen Manipulationen auf der Objekttabelle ausführt. Zu deren Aufruf
wird aus dem übergebenen Schlüssel ein Index in einen Funktionenvektor
berechnet, dem dann die Adresse der aufzurufenden Hilfsfunktion entnommen wird.
Die Aufruffolge msg —> Hilfsfunktion wird in der folgenden Beschreibung
zusammenfassend als Operation mit dem Schlüssel als Operationsname bezeichnet.































"gf is_ QUANTO
g layerciae
.! bjecth_out iS_SUDS‘fUCk-——_compere_decl
finign—£3biecin_pfO( edd_aparm (ls_RELATION T—compare_2_quent
yypars ecr_relat amsgPAPAﬁäﬂdd_dl$s_cal ompare_2_rel ?;
‘; ncr_relet nmsgPCLAS add_merge compare_relat ;
&& en_obi_entrye7*imi_°bi ‘ndd_corr 2
: en_pseude Insert_tnt “delete_corr ;
& "s_CLASSDEF ! _rnerge &
‘s_DECL evet_classtestﬁuﬁnd_ca„ :
"s_RELATIONI ‘ copy_expr %
ink_ob;ect, *inggrt_ob lest_obj | ,-.o_repstr “Mg 5
ax_comm_p(athj ‘ °°kup.°i.q mp Lrec!uce_condition
59 . msgiNHi-znrr_coMMubsi_pnf ush __I_YY'" ‘5
ut_outerm°$b msgiN$TPART‘—_) op (Ilnk_object) ;
msgPEXPR (gen_obj_entry) 3
&

msg!NHERIT_EXPR

Abb. 8. 7: Die Struktur des object—Moduls

Neben den genannten obligatorischen zwei Parametern akzeptiert msg einen,
zwei oder drei weitere Parameter, die an die jeweilige Hilfsfunktion weitergereicht
werden. Das angebotene Aktionsspektrum umfaßt folgende Operationsklassen:

Elementare Leseoperationen (m_G...)

Elementare Leseoperationen benötigen keinen zusätzlichen Parameter. Sie
ermöglichen entweder den direkten Zugriff auf die Felder der Struktur objectT eines
ausgewählten Objekteintrages oder liefern Informationen, die zwar nicht direkt in
einem Feld enthalten sind, die sich aber aus einem einzelnen Feld ableiten lassen.
Die angebotenen Schlüssel für diese Operationen sind im einzelnen:

m_GOUTERMOST Lesen des Feldes ou“nmoﬂ;

m_GOP1 Lesen des Feldes opl

m_GOP2 Lesen des Feldes op2

m_GQUANT Lesen des Feldes quant

m_GDEGREE Lesen des Feldes demeee

m_GEXPR Lesen des Feldes expr

m_GCLASS Lesen des Objektindizes der ersten
eingetragenen Klasse

m_GCLASSZ Lesen des Objektindizes der zweiten
eingetragenen Klasse

m_GSPEC Lesen des Feldes spec

m_GPREFIX Lesen des Feldes preﬁx

m_GSUFFIX Lesen des Feldes sufﬁx

m_GNEXTOUTER Les en de 5 Fe lde s next_outermost

';äLa „_„„-. , ‚.„„_. . ., „... -„

Der DROOLY—Compiler (object.c) ' 81



Msg mit dem Schlüssel

m_GXOP

benötigt einen weiteren Parameter, in dem der Index eines deklarierten
Objektes übergeben wird. Daraus ermittelt es, ob dieses Objekt erster
(Rückgabewert 1). zweiter (Rückgabewert 2) oder doppelter (Rückgabewert 3)
Operand der Empfänger—RELATION ist. Ist das übergebene Objekt überhaupt nicht
Operand der RELATION, ist 0 der zugehörige Rückgabewert.

Generatoren (m_GFIRST..., m_GNEXT...)

Diese Operationen generieren Folgen von Objekten, die in Listen— oder
Baumstruktur in der Objekttabelle verkettet sind. Allgemein betrachtet liefert ein
Generator alle Objekte einer Menge. Ein Generator besteht allgemein aus einer
Kommunikationsstruktur INFO und zwei Operationen FIRST und NEXT. FIRST
initialisiert die Kommunikationsstruktur und liefert das erste Element der Menge.
NEXT erhält jeweils als Parameter die INFO-Struktur, berechnet daraus das nächste
Element und aktualisiert die INFO—Struktur. Das Ende der generierten Folge wird
durch Rückgabe eines speziellen Wertes signalisiert. Es existieren
Programmiersprachen, die das Generatorkonzept als eingebautes Sprachkonstrukt
enthalten [ICON]. In der Objekttabelle werden Generatoren durch ein Paar von
Operationen FIRST und NEXT implementiert und die INFO-Struktur durch einen oder
zwei zusätzliche Aufrufparameter der NEXT—Operation realisiert. Der erste
Parameter übergibt dabei immer das zuletzt generierte Objekt.

Die beiden Operationen

m_GFIRSTCOMP . Lesen des Feldes compHd
m_GNEXTCOMP Lesen des Feldes next_comp

bilden einen Generator für die unmittelbaren Nachfahren eines Objektes. Der
erste solche Nachfahre wird mittels m_GFIRSTCOMP ermittelt, alle folgenden mit
m_GNEXTCOMP. Die folgenden zwei Operatoren generieren von einem Wurzelobjekt
rekursiv alle seine Komponenten und deren Komponenten in Preorderanordnung:

m_GPREFIRST Lesen der ersten Unterstruktur
m_GPRENEXT Lesen der nächsten Unterstruktur

Die Operation m_GPREFIRST liefert immer das Empfänger—Objekt zurück und ist
damit eigentlich eine Null—Funktion. Sie wurde zur Konsistenz mit dem allgemeinen
Generatorkonzept jedoch aufgenommen. M_GPRENEXT bestimmt durch Vergleich des
generierten Objektindizes mit dem Wert ihres zusätzlichen Parameters das Ende der
Folge und gibt bei Übereinstimmung den Wert NIL zurück. Würde in einem
alternativen Konzept schon die Operation m_GPREFIRST die Wurzel des zu
generierenden Baumes als zusätzlicher Parameter übergeben und von dieser in einer
statischen Variablen zur Benutzung durch m_PRENEXT abgelegt, könnte immer nur
ein Generator mit diesen beiden Operationen gleichzeitig benutzt werden, da ein
zweiter Aufruf von m_PREFIRST die alte Wurzel überschreiben würde.

Der DROOLY—Compiler (object.c) ‘ 82



Die Operationen

m_GFIRSTREL Lesen des Feldes relationﬂd
m_GNEXTREL1 Lesen des Feldes next_relationl
m_GNEXTREL2 Lesen des Feldes next_relation2

bﬂden zusannnen mit rn_XOP ebenfaﬂs einen Generaton der zu einer
deklarierten Struktur alle RELATIONS liefert, die diese Struktur als Operand tragen.
In diesem Fall gibt es zwei NEXT—Operationen, da für jeden Operanden der
RELATION eine entsprechende Verkettung vorhanden seh1 muß. Welche von beiden
Operationen aufgerufen werden inuß, entscheidet man für einen bestünmten
Operanden durch Aufruf von m_XOP.

Elementare Schreiboperationen (m_P..., m_INC...)

Elementare Schreiboperationen schreiben den als Parameter übergebenen Wert
(u.U. erst nach einer Plausibiütätsprüfung) direkt in die Felder der Struktur

objektT eines Objekteintrages. Es werden folgende Operationen zur Verfügung
gestellt:

m_POUTERMOST Schreiben des Feldes outermost

m_POPS Schreiben der Felder opl und op2
m_PQUANT Schreiben des Feldes quant

m_PEXPR Schreiben des Feldes expr
m_PSPEC Schreiben des Feldes spec
m_PNEXTOUTER Schreiben des Feldes next_outermost
m_INCDEGREE Inkrementieren des Feldes degree um 1
m_INCFPARM Inkrementieren des Feldes fparm_ix im

Empfänger und Schreiben des resultierenden
Wertes im Parameter-Objekt

Operationen zum Einfügen in Listen (m_ADD...)

Die Objekttabelle enthält einige Listen, die explizit vom Parser durch Anfügen
der einzelnen Objekte aufgebaut werden müssen:

m_ADDREL Einfügen am Kopf der Liste von RELATIONS mit
dem Empfänger als Operand

m_ADDCLASS Hinzufügen einer neuen Klasse (noch ohne
aktuelle Parameter)

m_ADDAPARM Hinzufügen eines aktuellen Parameters an die

zuletzt hinzugefügte Klasse des Empfängers

Operationen zur Behandlung der Vererbungsmechanismen

Die Instanziierung eines PARTs oder SETS bzw. die Bildung von Unterklassen
für Standardobjekte wird ebenfalls durch eine Nachricht an das zu instanziierende
Objekt bzw. an die Unterklasse ausgelöst

m_INSTPART Instanziierung eines PARTS oder SETs

m_INHERIT_COMP Vererbung aller Komponenten der Klasse

m_INHERIT_EXPR Vererbung aller Wertausdrücke der Klasse

m_COPYCLASS Kopieren einer Klasse mit ihren aktuellen
Parametern

Der DROOLY—Compiler (object.c) 83



Die Vorgehensweise dieser Operationen wird unten noch eingehend beschrieben.

Alle über msg erreichbare Operationen liefern den Wert des gelesen Feldes
(ein Obyndmabenenindex oder eine ganze Zahl) zurück. Neben (üesen Operaüonen
gibt es im Modul object.c noch eine Reihe weiterer Funktionen, die sich entweder
nicht auf nur ein (“ﬁekt beziehen, andere Rückgabetypen liefern oder einfach zur
weiteren Strukturierung dienen. Die Anwendung dieser Funktionen wird im Punkt

"Verfahren" an der jeweiügen SteHe beschrieben. Sie lassen sich in folgende
Klassen einteüen:

Funktionen für allgemeine Verwaltungsaufgaben

new Allgemeine Allozierung von Speicherplatz

renew Neuallozierung bei verändertem Speicherbedarf

gen_obj_entry Bereitstellung und Initialisierung eines
neuen Objekteintrages

inﬁ;obßct Initialisierung eines Objekteintrages

o@Lpush Verwaltung eines Stacks von Symboltabellen—
indizes

_obj_pop - " -

Funktionen zur Verwaltung der Komponentenhierarchie

an_obßct Einbinden einer neuen Komponente

insa1_obi Einfügen in die Komponentenliste

]astbi Aufsuchen das letzten Objektes in der
Komponentenliste

max_comm_paüz Ermittlung des längsten gemeinsamen
Pfadpräfix

subsg1naﬁx Auswechseln einer Anzahl Komponenten am

Anfang eines Objektpfades

Funktionen zum symbolischen Zugriff auf Objekte

cmp Vergleich von Präfix und Suffix zweier
Objekte

]ookup Zugriff auf ein Objekt über seinen Präfix und
Suffix. Neueinrichtung bei Bedarf

gen_pseudo Generierung eines Relationsbezeichners und
Einrichten eines Eintrages in der
Objekttabelle

Funktionen für Ja/Nein—Anfragen

is_RELATYON Anfrage an das spec-Feld

is_DECL - " —

is_CLASSDEF - " —

is_QUANTOR — " -

is_subsnuct Vergleich des Pfades eines Objektes mit dem

Pfadpräfix eines zweiten Objektes

Der DROOLY—Compiler (object.c) 84



Funktionen zur Bearbeitung semantischer Aufgaben

evai_ciasstest Vergleich zweier Klassen

add_memws Anfügen von Identifikationen nach ihrer
Ermittlung im Modul compare.c

put_ouüvmost Rekursives Anfügem einer Referenz auf die
Wurzel eines Relationenbaums in allen
Baumknoten

Sonstige Funktionen

add_aparm Hilfsfunktion für m_ADDAPARM

add_ciass_cail Hilfsfunktion für m_ADDCLASS

puser Schreiben des Feldes user

guser Lesen des Feldes user

o_repstr Ermittlung des textuellen Pfadbezeichners
Verfahren

Die Abläufe zur Verwaltung der Objekttabelle bilden keinen auf das
Objektmodul beschränkten Kontrollfluß, sondern werden beim Parsen des DROOLY—
Textes vom Parser angestoßen. Es erscheint aus diesem Grunde sinnvoll, die
einzelnen Abläufe jeweils anhand eines auslösenden DROOLY—Beispiels darzustellen.

Man betrachte folgenden Ausschnitt aus einer CMOS—Technologiebeschreibung
in DROOLY (Die Nummerierung der Zeilen hier wie in den folgenden Beispielen ist

nicht Bestandteil der Technologiebeschreibung, sondern dient der Erleichterung der
Bezugnahme im Text):

layerdef Metal
layerdef Poly
layerdef Contact_Cut
layerdef n_Diff

layerdef p_Di f f
layerdef n_We 1 1

ChU‘tbh)Nl-‘

Der Ablauf zum Einrichten der entsprechenden Objekte in der Objekttabelle ist
folgender: Der Parser fordert vom Scanner das Einlesen des ersten Lexems an. An
dem zurückgelieferten Wert des Tokens LAYERDEF erkennt er die Layerdefinition
und fordert ein weiteres Lexem an. Der Scanner erkennt einen Bezeichner und ruft
Iookup zur Einrichtung eines Eintrages in der Objekttabelle auf. Lookup wird vom
Scanner derart aufgerufen, daß für eine neue Präfix/Suffix-Kombination in jedem
Fall ein neuer Objekteintrag angelegt wird.

Lookup durchsucht die Objekttabelle binär, beginnend mit dem durch die
statische Variable s_root bezeichneten aktuellen Objekt, durch wiederholten Aufruf
von cmp zum Vergleich des übergebenen Präfix/Suffix—Paares mit dem Präfix/Suffix—
Paar des aktuellen Objekttabelleneintrages. Cmp liefert drei verschiedene
Rückgabewerte für "kleiner", "gleich" oder "größer". Bei “kleiner" oder "größer"
verfolgt Iookup das left— bzw. right—Feld des aktuellen Objekteintrages so lange,
bis es auf den Wert NIL in dem jeweiligen Feld trifft. Ein Aufruf von
gen_obj_entry erzeugt daraufhin einen mit dem übergebenen Präfix und Suffix
initialisierten neuen Objekteintrag. Die einzelnen Einträge der Objekttabelle werden
dabei einer nach dem anderen mit aufsteigendem Index vergeben. Der neue
Objekteintrag wird in den durch die left/right—Felder definierten binären Suchbaum



Der DROOLY-Compiler (object.c) 85



eingehängt und sein Index als Ergebnis von Iookup zurückgegeben. Lieferte cmp
jedoch "gleich" zurück, war das an iookup übergebene Präfix/Suffix—Paar schon in
einem Objekteintrag enthalten. Der Index dieses Objekteintrages wird als Ergebnis
von Iookup zurückgegeben.

Im konkreten Beispiel der ersten Layerdefinition ist der Präfix NIL, als Suffix
wird der Symboltabellenindex des Bezeichners "Metal" übergeben. Die Objekttabelle
ist noch unbesetzt, d.h. s_root hat den Wert NIL. Lookup fordert also durch Aufruf
von gen_obj_entry einen neuen Objekteintrag an, der mit dem übergebenen Präfix
und Suffix initialisiert wird. Lookup-gibt den Objektindex 1 als Ergebnis an den
Scanner zurück. Dieser liefert an den Parser das Token IDENTIFIER und speichert
den Objektindex 1 in der Kommunikationsvariablen yylval.OßJ.index. Der Parser
kann daraufhin die Ableitung

layer_definition : layerdef IDENTIFIER

durchführen. Die an der zugehörigen Regel hängende Semantik setzt über einen
m_PSPEC—Aufruf das spec—Feld des neuen Objekteintrags auf LAYERDEF und weist
es damit als Layerdefinition aus. Nach demselben Schema werden auch
Objekteinträge für alle weiteren Layerdefinitionen des obigen Beispiels eingerichtet,
so daß die Objekttabelle nach Ableitung der letzten Layerdefinition folgende
Einträge enthält: '

prel soil iclslclslctplnxtlnxtlltglttglquaitor/l ! l ! irellnxtlnxtl lnxtifpa
fix! fixlspec ! lcntlld lctplsatlcntlcntloperatorlopllop2lvalldeqlid lrellre2loutloutlix

:::::: : ::::: } :::::::: : :=: : :=: } :=: } :=: : :=: } :=: } :=: : :::::=:: : :=: } :=: : :=: = :=: : :=: : :=: l :=: } :=: { === } ::::
[l] lletall

! lllllitllil ! l l l l ! ll0ll } l l l l l l l i !
------l-----l--------l---l---l---I---l---I---l---l--------l---l---l---l---I---l---I-°-l---i---l----
[l] [Polyl

: Zilliilniil } l i l l : ll0ll : i i l l l l i l :
----—-I-----l-----—--l---I—-—l---l---l---l---l-—-l--------l---l---l---l---l---l---l---l-—-i---l----
[ll lContact_tutl

: illllltßiil ! l l I l ! ll0ll ! I l l l l l l l {
------l-----l---—----i---l---l—--I---l---l--—l---l--------l---l---l---l---l---l---l---l---l---i----
[l] In_Diiil

} 4llllllllil ! l l l l ! IIOIE } l l l l l l l l !
------i-----i--------l---l---l---l---l---l---l---l--------l---l---l---l---l---l---l---i---l---l----
[S] lp_Diifl

! SILIYERDEYl } l l i l ! ll0ll { l l l l l I ! l !
------l-----i--------l---l---l---l---l---l---i---l--------l---l---l°--I---l---l---l'--l---i---l°°--
[6] in tell!

li0liE

| |
| |

----- --------l_--l---l---
| t |

{‘ hmmm}
[
I

Diese Tabelle ist ein direkter Auszug aus der Ausgabe, wie sie daß Compiler-
Backend zum Trace der Objekttabelle in der Datei mit der Erweiterung ".t" ausgibt.
Zum Format dieser Tabelle sind einige Erläuterungen notwendig:

Die Tabellierung eines Objekteintrages beginnt jeweils mit der Angabe des
Objekttabellindizes in eckigen Klammern. Ihm folgt der vollständige Pfadbezeichner
in geschweiften Klammern. Diese Ausgabe erfolgt nur zur leichteren Lesbarkeit der
Tabelle. Der Objektbezeichner wird dazu aus dem Symboltabelleneintrag des
jeweiligen Objektes und aller seiner Pfadvorgänger ermittelt. Bis auf die Felder
left, right, lmerges, merges und user gibt es für jedes Feld der objectT—Struktur
eine Spalte in der Tabelle. Um die Tabelle übersichtlich zu halten, wurden für die
Spaltenüberschriften folgende Abkürzungen gewählt:

Der DROOLY—Compiler (object.c) ' 86



prefix "
suffix "

spec "
classes[0] c1s
class_cnt cls.cnt
compHd cmp.Hd
next_comp nxt.cmp
next_same nxt.sam
lmerge_cnt lmg.cnt
rmerge_cnt rmg.cmt
quant quantor/operator
opl '

op2 "

expr val
degree deg
relatioan rel.Hd
next_relationl nxt.rel
next_relation2 nxt.re2
outermost out
next_outermost nxt.out
fparm_ix fpa.ix

Mit Ausnahme von spec und quant werden alle Felder als Zahlenwerte
tabelliert. Ist der jeweilige Wert gleich NIL, bleibt die zugehörige Spalte leer. Für
die Felder spec und quant wird die symbolische Entsprechung des Feldinhaltes
angegeben. Bei der Tabelüerung der Klassen und inerges ist eine Sonderbehandlung
vorgesehen. Der Klassenindex des ersten Elementes im classes—Vektor wird als
nonnaler Feldinhalt angegeben. Enthält das Objekt entweder mehrere Klassenaufrufe
(z.B. bei PARAMS) oder enthält ein Klassenaufruf aktuelle Parameter, so wird für
jedes Element des classes-Vektor eine zusätzüche Zeüe der Form

: CLISS (liessenindex) ( (altuelle_tartteter) )

ausgegeben. Enthält der lmerges— oder nnerges—Vektor Elemente. so werden
diese ebenfaüs einzeh1in zusätzhchen Zeﬂen der folgenden Formen tabeﬂiert

! LIEIGE (Objektindex)
: lllldl (Objektindex>

Wie man der Tabelle entnimmt, ist für die einzelnen Layerdefinitionen jeweils
ein eigener Otüekteintrag angelegt worden, der eindeutig durch einen
Objekttabellenindex identifiziert werden kann. Die Felder prefix und suffix
enthalten die Verweise auf den Pfadpräﬁx. bzw. den Symboltabeﬂenindex des
jeweiligen Objektbezeichners.

Der DROOLY—Compiler (object.c) ' 87



In der Beispiel—Technologiebeschreibung folgt auf die Layerdefinitionen die
Definition einer Klasse mit einem formalen Parameter:

7 gelassdef Gate f ar diffusion )

8 part p_wire $class Poly )
9 param diffusion iclass n_Diff ) class p_Diff )

10

11 shape iso_Polygon

12 cross [ ] p_wire diffusion

13 diffusion width [ ;; diffusion gelasstest n_Diff )
14 then >= 15

15 e_l_s_g >= 10

16 ]

17 )

Auf die Anforderung eines neuen Tokens liefert der Scanner das Token
CLASSDEF an den Parser. Beim Einlesen des folgenden Lexems erkennt der Scanner
einen Bezeichner und ruft Iookup zur Einrichtung eines Objekteintrages. Lookup
wird dazu der Präfix NIL und der Suffix 7 (Symboltabellenindex von "Gate")
übergeben. Lookup findet zu diesem Paar keinen Objekteintrag und richtet einen
neuen Eintrag ein. dessen Index es an den Scanner zurückliefert. Dieser meldet das
Token IDENTIFIER an den Parser. Im Parser kann an dieser Stelle das spec—Feld
des neuen Objekteintrages (dessen Index der Parser aus der Kommunikations—
variablen yylval.0ßJ.index ausliest) auf CLASSDEF gesetzt werden. Das so erzeugte
Klassenobjekt definiert bis zur Ableitung zu class_deﬁnition den statischen
Kontext. Sein Index wird in der globalen Variable act_class gespeichert und dient

im Scanner als Pfadpräfix bei der Erzeugung neuer Objekteinträge innerhalb dieser
statischen Umgebung.

Es folgt das Parsen des formalen Parameters durch Übergabe des Tokens
FPARM nach dem Einlesen des nächsten Lexems. Für jeden der folgenden Bezeichner
wird beim Einlesen durch den Scanner ein Objekteintrag angelegt. Der Scanner
übergibt dazu den Wert von act_class als Präfix und den jeweiligen
Symboltabellenindex als Suffix an Iookup. Als Folge dieser Konstellation werden die
Bezeichner von formalen Parametern als relativ zur aktuellen Klasse angesehen. Im
Beispiel wird also ein Objekt mit dem absoluten Bezeichner Gate.dlffusion vom
Scanner angelegt.

Sowohl das Klassenobjekt als auch das Objekt zu jedem formalen Parameter
haben ein Feld fparm_ix. Bei einen Aufruf der Operation m_ADDFPARM mit dem
Klassenobjekt als Empfänger wird dieses Feld im Klassenobjekt für jeden formalen
Parameter um 1 inkrementiert. Der selbe Aufruf speichert den inkrementierten Wert
im fparm_ix—Feld des Parameterobjektes als dessen Index in der Parameterliste.

Mit dem Schlüsselwort art beginnt im DROOLY-Text der Deklarationsteil der
Klasse. Der Scanner liefert nach dem Token PART den Objektindex der neu
erzeugten Komponente Gate.p_wire an den Parser. Der Parser speichert diesen
Objektindex in der globalen Variable act_object zwischen, um bei der folgenden
Ableitung zu class_call darauf zugreifen zu können.

Als PART erhält die Komponente p_wire eine eindeutig bestimmte Klasse
zugewiesen. Zu beachten ist, daß Klassenbezeichner implizit als global angesehen
werden. Um diesen Sachverhalt dem Scanner bekannt zu machen, wird nach
Erkennen des Tokens CLASS die Kommunikationsvariable act_class auf den Wert NIL
gesetzt. Der folgende Klassenbezeichner wird dann mit eben diesem Wert als Präfix
in der Objekttabelle gesucht. Der Parser erhält den Index des gefundenen Objektes
zurück. Handelt es sich bei diesem Objekt um keine Layer— oder Klassendeklaration

Der DROOLY—Compiler (object.c) ' 88



(erkennbar an Wert des spec—Feldes), wird die Übersetzung mit einer
entsprechenden Fehlermeldung abgeprochen. Andernfalls wird die Klassenzuweisung
dem aktuellen Objekt (im Beispiel also die Klasse Poly dem PART p_wire) über
einen Aufruf von m_ADDCLASS zugewiesen. Diese Operation erledigt ihre Aufgabe
durch Aufruf der Funktion add_class_call‚ der dazu die Indizes des aktuellen
Objektes und des Klassenobjektes als Parameter mitgegeben werden.

Die Klassenzuweisungen einer Komponente sind im Objekteintrag als Vektor
classes von Klasseneinträgen organisiert. Beim ersten Eintrag einer
Klassenzuweisung (bei PARTs bleibt es die Einzige) wird ein neuer Vektor mit
CLASSINC Elementen angelegt. Jedes dieser Elemente kann den Index eines
Klassenobjektes und einen Vektor aktueller Parameter speichern. Werden bei der
Deklaration eines PARAMs mehr als CLASSINC Klassenzuweisungen benötigt,
realloziert add_c]ass_call den Klassenvektor mit zusätzlich CLASSINC Elementen.
Der Wert der symbolischen Konstante CLASSINC ist mit 3 am Anfang des Objekt—
Moduls definiert.

Eine Klassenzuweisung kann mit der Angabe aktueller Parameter verbunden
sein. Diese werden im DROOLY—Text als Liste von Indizes in die
Klassenzuweisungslisten der PARAM—Komponenten des jeweiligen Klassenobjektes
dargestellt. In der Objekttabelle wird eine solche Liste als aparms—Vektor in der
Struktur einer Klassenzuweisung gespeichert. Die aktuellen Parameter werden
einzeln bei Abarbeitung der aktuellen Parameterliste von der Operation m_PAPARM
in die Objekttabelle eingetragen. Die eigentliche Arbeit erledigt auch hier eine
Dienstfunktion add_aparm. Beim ersten aktuellen Parameter der zuletzt
eingetragenen Klassenzuweisung wird ein Parametervektor von PARINC Elementen
alloziert. Sollten mehr als PARINC aktuelle Parameter benötigt werden, realloziert
add_aparm den Parametervektor mit zusätzlich PARINC Elementen. Der Wert der
symbolischen Konstante PARINC ist ebenfalls mit 3 am Anfang des Objekt-Moduls
definiert.

Nachdem die Klassenzuweisung u.U. mit aktuellen Parametern eingelesen ist.
wird das neue PART in die Komponentenhierarchie der aktuellen Klasse eingehängt.
Dies geschieht im Parser bei der Ableitung zu part durch einen Aufruf von
link_object. Link_object wird benötigt, da der Scanner pauschal für jeden
eingelesenen, neuen Bezeichner über Iookup einen Objekteintrag anlegt. Beim
Einlesen eines qualifizierten Bezeichners legt er alle Komponenten des Pfades neu
an. falls sie noch nicht in der Objekttabelle existierten. Aufgrund der ihm zur
Verfügung stehenden semantischen Informationen kann er jedoch nicht die Funktion
des erzeugten Objektes in einer Komponentenstruktur erkennen. Dies ist Aufgabe
des Parsers. welcher mit einen Aufruf von Iink_object die notwendigen
Verkettungen in der Objekttabelle herstellt.

Link_object hat zwei Parameter. Der Erste (Obj) übergibt den Index des
einzuhängenden Objektes, der zweite Parameter (force_entry) ist ein Flag, mit
welchem die Neueinrichtung eines Objekteintrages erzwungen werden kann, auch
wenn ein Eintrag mit dem selben Präfix und Suffix bereits in der Objekttabelle
vorhanden ist. Link_object erhält alle benötigten Informationen aus dem
übergebenen Objekteintrag. Wichtige Bedingung dabei ist, daß im Parser Iink_object
@ der Zuweisung eines Wertes an das spec—Feld aufgerufen wird. An dem
unbelegten spec—Feld erkennt Iink_object, daß es sich bei dem Objekt um einen
neuen Eintrag in die Objekttabelle handelt. Ist der Präfix des Objektes belegt,
handelt es sich also um kein globales Objekt. wird zum eigentlichen Verketten
insert_obj mit dem Objektindex und dem Index des Vaterobjektes (gleich dem Wert
des prefix—Feldes des Objektes) aufgerufen. War force_entry gesetzt oder handelt
es sich bei dem Objekt um ein C_PROPERTY- oder C_RELATION—Objekt, wird ein

Der DROOLY—Compiler (object.c) 89



neuer Objekteintrag mit identischem Präfix und Suffix erzeugt, dieser aber in keine
Komponentenhierarchie eingehängt.

Insert_object hängt den übergebenen Objekteintrag als letzte Komponente an
die Liste der unmittelbaren Söhne des übergebenen Vaterobjektes ein. Dabei wird
auch die Inorder—Fädelung des Komponentenbaums des Vaterobjektes aktualisiert,
mit deren Hilfe man ohne rekursive Funktionen oder einen zusätzlichen Stack jede
Komponente und Unterkomponente des Komponentenbaums des Vaterobjektes in
Inorderreihenfolge bzw. in Preorderreihenfolge erreichen kann (dies wird vom
Generatorpaar m_PREFIRST, m_PRENEXT ausgenutzt). Insert_object bedient sich der
Funktion Iast_object‚ um daß letzte Objekt der Komponentenliste des Vaterobjektes
aufzufinden.

Im Beispiel wird nach dem PART p_wire noch ein PARAM diffusion deklariert.
Dessen Objekteintrag wurde schon beim Einlesen der Liste formaler Parameter des
aktuellen Klassenobjektes angelegt. Der Index dieses Objektes wird vom Scanner an
den Parser übergeben und von diesem in der Variable act_object gespeichert. Es
folgt das Einlesen der einzelnen Klassenzuweisungen. deren jede ein Element des
classes-Vektors des PARAMS belegt. Mit der Deklaration des PARAMS ist der
Deklarationsteil der Klasse Gate abgeschlossen. Die Objekttabelle enthält folgende
neuen Objekteinträge (8):

pre) s ) ) ) s)cwp)nxt)uxt)lwqirwaq ) ) ) )rel)uxt)nxt) )nxt)tpe
fix) fix)spec ) )cnt)id )cwp)saw)cnt)cut)operatorlopl)op2)val)deq)id )rel)re2)out)out)lx
------l-----l--------l--..I---)---I---I.--l---)---l-----——-I---I---!---(--—l---i---)---i---l---l----
"""l""'I""""l"'l"'l"'l"'l"'l"'l"'l""""l"'l"'i"'l"'l"'l"'l"'l"'l"'l""
[7] )Gatﬂ
) l)CLISSDEEI ) ) 9) ) ) ) )l0l! ) ) ) ) 2) ) ) ) ) ) 1
°-----)-----)--------)---)---)---)---)---)---)---)-------')---)---)---)---)---)---)---)---)---)----
[8] )Gate.ditiusionl
) CLISS 4 ( )
) CLASS 5 ( )

)) 8)Plill ) ) 2) ) ) ) ) )l0l8 ) ) ) ) ) ) ) ) ) ) 1
----—-)-----)—-------)---)---)-°-)-——)--—)---)---)--------)---)---)---)---l---)---)---)---)---i----
(9) )Gete.p_rirel

1 )l0il '

)) 9)Plli ) 2) ) ) 8) ) ) ) ) ) ) ) ) ) ) . )
__-___l_____i________l___i___i___l___I___l___l___o________l___l___! I---)--_I___l___l )
| i I l l l l l I l l l l | I l I (

Mit der Ableitung zu declarations sind alle Unterstrukturen der aktuellen
Klasse deklariert. Etwas anschaulicher als durch die Verkettungen in der
Objekttabelle läßt sich die Komponentenhierarchie durch folgenden Baum darstellen:

‘ Gute

ellliﬂul .

pulm

dr"union
el.-l: n_0lﬂ
cl-u: p_Dm

pm }
p_wvre
el.": In»;





Abb. 8.1: Tabellen der Beispieltechnologie (I)



(8) Negative Werte in den mehr oder sufﬁx-Feldern gehören zu einer Fädelung, die zusätzlich zur normalen Komponentenverkettung in die Objekttabelle
eingebracht wird und zur Generierung aller Unterstrukturen eines Objektes verwandt werden kann, ohne dazu rekursive Funktionen verwenden zu müssen.

Der DROOLY—Compiler (object.c) 90



Es folgt das Einlesen des Beschreibungsteils. Eine Beschreibungsanweisung
kann in DROOLY eine von drei Formen forward. merge oder relation annehmen,
welche in beliebiger Reihenfolge aufgeführt sein können. Der Beschreibungsteil der
Klasse Gate beginnt mit einem forward. Forwards genügen der allgemeinen Syntax





forward : [ [ quantor_spec ] IDENTIFIER ] property
property : property_type expression ) shape IDENTIFIER
property_type : width ) area ) dimension ) radius ) length
quantor_spec : a_ll ) some ) a_nx : %

Ein forward spezifiziert eine Eigenschaft eines Objektes. Im allgemeinen geht
der Beschreibung der Eigenschaft der Bezeichner des betroffenen Objektes voran.
Fehlt dieser oder ist er durch das Schlüsselwort &lf ersetzt, wird eine Eigenschaft
der aktuellen Klasse spezifiziert. Die Zeile 11 im Beispiel legt also eine erlaubte
Geometrieklasse für das Klassenobjekt Gate fest, in diesem Fall iso_Polygon. Der
Scanner erzeugt beim Einlesen dieser Zeile zwei Objekte, ein PROPERTY—Objekt mit
dem Bezeichner Gate.shape sowie ein zunächst völlig unspezifizertes Objekt mit dem
globalen Bezeichner iso_Polygon. Der Parser hängt das PROPERTY—Objekt durch
Aufruf von link_object in die Komponentenhierarchie der Klasse ein und weist dem
spec—Feld mit m_PSPEC den Wert SHAPE zu. Das zweite Objekt wird in keine
Komponentenhierarchie eingehängt. Lediglich seinem spec—Feld wird der Wert
GEOCLASS zugewiesen.

In der Zeile 12 folgt eine relation zwischen den Objekten Gate.p_wire und
Gate.Diffusion‚ der in diesem Fall kein Wertausdruck zugeordnet ist. Für die
RELATION wird vom Parser durch Aufruf von gen_pseudo ein Objekteintrag mit
einem eindeutigen Bezeichner erzeugt. Der Pfadpräfix dieses Bezeichners wird dazu
zunächst durch die Funktion max_comm_path aus den Pfadbezeichnern der beiden
Relationsoperanden bestimmt. Durch diese Maßnahme wird erreicht, daß eine
RELATION möglichst tief in der Komponentenhierarchie der aktuellen Klasse
eingehängt wird (Lokalitätsprinzip). Im Beispiel sind die Pfade der beiden
Operanden gegeben durch

Gate.p_wire und
Gate.diffusion .

Als längster gemeinsamer Pfadpräfix ergibt sich daraus Gate. Gen_pseudo
erzeugt für eine RELATION jeweils einen neuen Symboltabelleneintrag und trägt
dort einen Bezeichner der Form stXXX ein (XXXX ist eine fortlaufende
Nummerierung, beginnend bei 5000). Das RELATION—Objekt erhält somit den
Bezeichner Gate.p55000 und wird als direkter Sohn an das Gate—Objekt angehängt.
Seinem spec—Feld wird RELATION, seinem quant-Feld der Operator CROSS und
seinen Feldern opl und op2 werden die Indizes der Operandenobjekte Gate.p_wire
und Gate.diffusion zugewiesen. Um von den Operanden leicht alle betreffenden
RELATIONS erreichen zu können, werden von deklarierten Komponenten beginnend
alle RELATIONS miteinander verkettet, die diese Komponente als einen ihrer
Operanden haben. Der Parser ruft dazu die Operation m_ADDREL mit einer
zusätzlichen Kennung. Je nach dem Wert dieser Kennung erfolgt die Verkettung der
RELATION über ihr next_relationl- oder ihr next_relation2—Feld.

Handelt es sich bei der RELATION um die Wurzel eines Relationenbaums,
werden noch zwei weitere Verkettungen vorgenommen. Alle Wurzeln von unter einer
Klasse definierten Relationenbäumen werden als Liste an das Klassenobjekt durch
die Operation m_ADDNEXTOUTER angehängt. Diese Operation erhält den Objektindex
der aktuellen Klasse (aus act_class) und den Index der RELATION als Parameter.
Die zweite Verkettung wird durch einen Aufruf von put_outermost mit dem
Objektindex der RELATION als Parameter eingeleitet. Diese Funktion steigt rekursiv

‚Aj— ‚ ;. „..—.- . _„ __Z'..;w.

Der DROOLY—Compiler (object.c) 91



in den Relationenbaum hinab und weist dem outermost—Feld jeder besuchten
RELATION den Index der Baumwurzel zu.

Die Zeilen 13—16 enthalten noch ein weiteres forward. Dieses bezieht sich
jedoch auf die Unterstruktur diffusion. Dem Scanner wird durch temporäres
Umsetzen der Kommunikationsvariable act_class mitgeteilt, daß er ein Objekt mit
dem Bezeichner Gate.diffusion.width zu erzeugen hat. Dieses Objekt benötigt einen
Wertausdruck, der in DROOLY von eckigen Klammern eingeschlossen wird. Der Parser
erzeugt durch Aufrufe verschiedener Funktionen aus dem Wertausdrucksmodul (siehe
Abschnitt 8.7) entsprechende Wertausdrücke. Im Beispiel enthält der Wertausdruck
eine Bedingung

diffusion (classtest n_Diff )

Für diese Bedingung wird durch Aufruf von gen_obj_entry ein neuer Eintrag
mit dem Bezeichner Gate.diffusion in der Objekttabelle angelegt, dieser jedoch nicht
in die Komponentenhierarchie von Gate eingehängt. Diesem neuen Objekt wird die
Klasse n_Diff zugewiesen und es erhält als Wert seines spec—Feldes CLASSTEST.
Dieses Objekt wird noch bei der Vererbung der Komponenten der Klasse Gate eine
Rolle spielen.

Mit dem Beschreibungsteil ist die Definition der Klasse Gate abgeschlossen. Die
Objekttabelle hat sich inzwischen folgendermaßen entwickelt:

pre) sat) ) ) s)cwp)u xt)nxt) lwg)rwq) quantor/) ) ) ) )rel)nxtlnrt) )uxt)ipa

fix) fix)s mp ) )cnt)ﬂd )cwp)sa w)cut)cnt)operetorlopllo p2)val)deg)ld )re1)re2)out)ou t)ix
::::::==:::=g: :::::::):::}:::I:::i:::}:::{=::):::I:=::::::{:::}:::}:::{:::}:::l:::):::):::}:::):z::
))) (Gate)

) l)CLASSDEF) ) ) 9) ) ) ) )IOAE ) ) ) ) 2) ) ) ) ) 12) 1
------)-----)--------)---)---)---)---)---)---)---)--------)---)---)---)---)---)---)---)---)---)----
[8] (Gate.diffusioul

) CLASS 4 ( )

) CLASS 5 ( )

)) 8)Pllil ) ) 2) 13) 10) ) ) )IOIE ) ) ) ) ) 12) ) ) ) ) 1
------'-----)--------)---)---)-—-)---)---)---)---)--------)---)---)---)---)---)---)---)---)---)----
(9) (Gate.p_wire)

)) 9)PAAI ) 2) l) ) 8) ) ) )IOIE ) ) ) ) ) 12) ) ) ) ‘
----—-)-----)--------)---)---)—'-)---)---)---)---)------- )-'-)---)---)---)---)---)—--)---)---)----
[10] (Gate.shape)

l)-103))SHAPE ) ) ) -8) 1 ) ) ) )IOIE ) ) ) 1) ) ) ) ) ) .
------)-----)--------)---)---)---)---)---)---)---)°-'-----)---)---)---)-—-)---)---)---)---)---)----
[11] )iso _Polygou)

) 10)GEOCLASS) ) ) ) ) ) ) )IOIE ) ) ) ) ) ) ) ) ) )
------)-----)--------)---)---)---)---)---)---)---)----—---)---)"')---)---)---)-—-)---)---)---)----
(12) )Gate. p55000l

l)-5000)11111101) ) )-10) -l. ) ) )CAOSS ) 9) 8) ) ) ) ) ) )
---—--)-----)--------)---)---)---)--—)---)---)"')"""")---)---)---)---)---)---)---)---)---)----
[ll] (Gate. diiiusiou. width)

8)-1036)01011111) ) ) -9) -8) ) ) )AOIE ) ) ) S) ) ) ) ) ) )
______I_____)________l___I__‚l___l___l___l___l___l________l___l___)___!___)___l___I-__l_-_l___l____

| 1 l l | | i I | | i 1 | i l l | | l l
[l)] (Gate.diffusioul a..

) CLASS) ( ) '“"‘ ‘

) CLASS 4 ( )

') [Olli

8)CLASSTST) )
|
I

l
|

------I----- --------'---
|



Der DROOLY—Compiler (object.c) ' 92



In der graphischen Darstellung ergibt sich folgendes Bild:

| chill."
diffusion '
| alu-: n_0.n )

mu; am ’ '/ . . . ,
‘ )g-ncun ) f

) Gate )









pur-m |
diUu-mn

eine: n_Dm
gun: n_0m

p-n _
p_wwre
ein-: Vom











llllilﬂl!
pl5000

«an

Abb. 8. 9: Tabelle der Beispieltechnologie (II)

Das Beispiel wird mit der Definition einer weiteren Klasse fortgesetzt:

18 (classdef n_CE_Trans

19 M well (class n_Well )

20 s_et[gr_u_<_i>=l<=5]gt(classGate2)
21

22 a_ll gt (merge gt gt )

23 a_ll gt indist [ ] gt well

24 _aﬂ gt indist [ >= 20 ] gt well

25 % [ >= 30 ] &lf &?

26 )

Diese Klasse deklariert keine PARAMS als Unterstrukturen, demzufolge besitzt
sie keine Liste formaler Parameter. Der Deklarationsteil deklariert zwei
Komponenten. Die erste, ein PART. erhält als Klassenzuweisung den Layer n_Well.
Das folgende SET erhält die Klassenzuweisung Gate mit Auswahl der zweiten
alternativen Klasse für den ersten (und einzigen) formalen Parameter von Gate. Wie
bei parameterlosen Klassenzuweisungen wird dem classes—Vektor des neuerzeugten
SET—Objektes zunächst der Index des Klassenobjektes Gate mithilfe der Operation
m_ADDCLASS hinzugefügt. Der aktuelle Parameter wird anschließend dieser
Klassenzuweisung mithilfe von m_PAPARM hinzugefügt. Diese Operation ermittelt
aus dem class_cnt-Feld (bei SETs und PARTS immer gleich 1) des SET-Objektes die
letzte Klassenzuweisung und fügt den neuen, aktuellen Parameter in deren über

das aparm_cnt—Feld der aktuellen Klassenzuweisung indizierten Vektor aktueller
Parameter ein.

Die Deklaration des SET—Objektes enthält im Beispiel noch eine Einschränkung
des erlaubten Kardinalitätsbereiches in Form eines Wertaudruckes. Die an dieser
Stelle erlaubte Syntax für den Wertausdruck ist eine echte Untermenge der Syntax
von Wertausdrücken im Beschreibungsteil. In DINGO—II sind zur Festlegung eines
erlaubten Kardinalitätsbereiches exakte Werte (Schlüsselwort exactly),
Alternativlisten von Werten (Schlüsselwort one of) oder Intervalle (Schlüsselwort
range) erlaubt. In DROOLY werden diese Beschreibungsarten durch mit ad und g
verknüpfte boolesche Ausdrücke dargestellt und auch so in die Ausdruckstabelle
übernommen. Auf die Übersetzung von DROOLY—Wertausdrücken in ihre tabellarische
Darstellung wird bei der Beschreibung des Ausdrucks—Moduls noch eingegangen. An
dieser Stelle ist nur wichtig, daß im expr—Feld eines SET—Objektes mit
eingeschränktem Kardinalitätsbereich ein Ausdruckstabellenindex eingetragen wird.

Der DROOLY-Compiler (object.c) ' 93



Mit der Deklaration des SET—Objektes sind alle Unterstrukturen der Klasse
n_CE_Trans deklariert. Für das Klassenobjekt n_CE_Trans und seine
Unterstrukturen sind in der Objekttabelle folgende Einträge angelegt worden:

pre) sul) )cls)cls)cwp)nxt)uxt)lwq)rwaquaotar/I ) ) ) )rel)uxtlnxt) )uxtlfpa
fix) fix)spec ) )cnt)ld )cup)saw)cnt)cntloperator)op1)o )val)deq)ld )re1)re2)out)out)ix
------)-----l-------.|__-|___l--—l---I---I---i---l--------l---l---|---I---l—--l---l---l-..[--—I----
"""l""‘l""""l“'l"'l"'I"'l"'l"‘l"'l""""l"'l"')"'l"'l"'l"'l"'l"'l"'l""
H5)lnjijrmsl
) 11)CLASSDEF) ) ) 16) ) ) ) )iOHE ) ) ) ) 2) 28) ) ) ) 25)
------)-----)--------)---)---)---)---)---)---)---)--------)---)—--)---)---)---'---)---)---)---)----
[16) (u_CE_Trans.welll
15) 12)PAAT ) 6) 1) ) ll) ) ) )IOIE ) ) ) ) ) 26) ) ) ) )
------)-----)--------)---)---)---)---)---)---l---)--------)---)---)---)---)--°)°°-)---)---)---)----
[ll] (u_CL_Traus.qtl
' CLASS 7 ( 2 )
l 1 [ONE

Zu beachten ist an dieser Stelle besonders die Klasse des Objektes
n_CE_Trans.gt. Bei ihr handelt es sich um ein strukuriertes Objekt, d.h. sie selbst
enthält wiederum Unterstrukturen. Die Semantik eines Objektes in DINGO-ll wie in
DROOLY schreibt nun vor, daß ein Objekt rekursiv alle Unterstrukturen seiner
Klasse erbt. Im Beispiel heißt das, daß der Vererbungsmechanismus noch folgende
Objekte in der Objekttabelle erzeugen muß:

n_CE_Trans.gt.diffusion
n_CE_Trans.gt.p_wire
n_CE_Trans.gt.shape
n_CE_Trans.gt.diffusion.width

Es muß also der gesamte am Objekt Gate hängende Teilbaum kopiert und an
das Objekt n_CE_Trans.gt angehängt werden. Im Anschluß an die letzte Deklaration
wird daher im Parser jede deklarierte Unterstruktur der aktuellen Klasse zunächst
überprüft, ob ihre Klasse eindeutig bestimmt ist (Dies trifft genau bei PART- und
SET—Objekten zu). Nur dann macht das Kopieren des Klassenbaums Sinn. Für eine
Unterstruktur mit eindeutig bestimmter Klasse ruft der Parser jeweils die Operation
m_INSTPART.

M_INSTPART ist bei weitem die komplexeste Operation des Objektmoduls. Beim
Aufruf werden ihr der Index des zu instanziierenden PART— bzw. SET—Objektes
sowohl als Empfängerobjekt als auch als zusätzlicher Parameter übergeben. Während
bei rekursiven Aufrufen in diesem zusätzlichen Parameter immer der Index des
ursprünglichen Objektes und damit die Wurzel des neu zu erzeugenden
Komponentenbaumes für die Anpassung der Klassen in bedingten Wertausdrücken
übergeben wird, bestimmt das Empfängerobjekt die jeweilig bearbeitete
Unterstruktur des Klassenobjektes. Aufgabe von m_INSTPART ist es, dem
Empfängerobjekt Kopien aller Komponenten seiner Klasse zuzuordenen. Ist die
Klasse parametrisiert, werden Komponenten mit den aktuellen Parametern als
Klassen instanziiert, nachdem die Zahl der aktuellen Parameter mit der Zahl der
formalen Parameter verglichen wurde. Stimmen die Parameteranzahlen nicht überein,
wird der Instanziierungsprozeß abgebrochen und eine Fehlermeldung ausgegeben. Die
eigentliche Arbeit des Kopierens wird von m_INSTPART auf zwei Hilfsoperationen
m_INHERIT_COMP und m_INHERIT_EXPR aufgeteilt. Diese werden in zwei getrennten
Durchläufen über alle direkten Nachfahren der Klasse aufgerufen.

Der DROOLY—Compiler (object.c) ' 94



M_INHERIT_COMP erhält neben dem Empfänger zwei weitere Parameter: Im
ersten Parameter wird die gerade zu ererbende Unterstruktur des Klassenobjektes
übergeben, der zweite Parameter übernimmt den Parameter act_object aus
m_INSTPART unverändert. Im ersten Schritt wird der Objekttyp der zu ererbenden
Komponente aus ihrem spec—Feld bestimmt. Handelt es sich um ein C_PROPERTY-
oder C_RELATION-Objekt aus einem bedingten Wertausdruck. wird es übersprungen.
Für alle anderen Objekttypen legt Iookup ein neues Objekt mit dem
Empfängerindex als Präfix und dem Suffix der Klassenkomponente als Suffix an. Der
Typ dieses Objektes hängt sowohl vom Typ der Instanz als auch vom Typ der
Klassenkomponente ab. Ist mindestens eines dieser beiden Objekte ein SET, ist auch
das neu erzeugt Objekt vom Typ SET und sein spec—Feld wird entsprechend
gesetzt. War nur eines der beiden Objekte ein SET—Objekt, wird dessen
Kardinalitätseinschränkung einfach an das neue Objekt vererbt. Waren jedoch
beides SET-Objekte, wird dem neuen Objekt die AND-Verknüpfung der beiden
Kardinalitätseinschränkung als eigene Einschränkung zugewiesen.

Handelte es sich bei der Klassenkomponente um ein PARAM—Objekt, wird seine
Klasse durch einen aktuellen Parameter der Instanzklasse eindeutig bestimmt und
das neu erzeugte Objekt wird ein PART oder SET je nach dem Typ des Instanz-
objektes. Der Vektor der aktuellen Parameter des zu instanziierenden PARTS oder
SETs enthält an einer bestimmten Stelle denjenigen aktuellen Parameter, der für
die momentan bearbeitete Klassenkomponente (ein PARAM) zuständig ist. Der Index
des aktuellen Parameters im Vektor ergibt sich aus dem fparm_ix-Feld des PARAMS.
Über den aktuellen Parameter wird der gewünschte Klassenaufruf ausgewählt. Die
aktuellen Parameter dieses Klassenaufrufes werden zum Klassenaufruf des oben mit
Iookup neu erzeugten Objektes hinzukopiert. Mit der derart ausgerüsteten PART-
Instanz wird nun rekursiv m_INSTPART gerufen, da die Komponenten eines PARAMS
ja erst bei der gerade erfolgten Festlegung seiner exakten Klasse bestimmt sind.

Bei der Behandlung von RELATION— oder SET_RELATION-Objekten müssen auch
die Operanden an den neuen statischen Kontext angepaßt werden. Nachdem das
spec-Feld und der Operator gesetzt sind, muß für jeden der beiden Operanden der
neue Pfadpräfix bestimmt werden. Die Hilfsfunktion subst_prefix erhält zu
Erledigung dieser Aufgabe drei Parameter: Im ersten Parameter wird der Objektindex
des jeweiligen Operanden übergeben, der zweite Parameter übernimmt den Präfix des
neuerzeugten Objektes und der dritte Parameter übernimmt den Präfix der aktuellen
Klassenkomponente. Subst_präfix verfolgt den Pfad des als ersten Parameter
übergebenen Objektes zurück, bis es auf den als zweiten Parameter übergebenen
Objektindex trifft. Die Suffizes der dabei durchlaufenen Objekte werden auf einem
Stack gesichert. Zur Erzeugung des neuen Pfades wird der dritte Parameter als
Präfix benutzt, und iterativ zusammen mit dem nächsten vom Stack zurückgeholten
Suffix mittels Iookup das zugehörige Objekt in der Objekttabelle aufgesucht. Sind
alle Suffizes vom Stack geholt, wird der resultierende Pfad an m_INHERIT_COMP
zurückgegeben und dort als Operand in das neue Objekt eingetragen. Da jedes
Objekt eine Liste aus Verweisen auf alle RELATIONS trägt, in denen es als Operand
auftritt, wird bei einem neu erzeugten RELATION—Objekt dieses nun noch durch
m_ADDREL in die entsprechenden Listen seiner Operanden eingefügt.

Damit ist die Kopie der Klassenkomponente auf dieser Hierarchiestufe
abgeschlossen. In einer Schleife ähnlich der in m_INSTPART ruft sich
m_INHERIT_COMP mit dem gerade erzeugten neuen Objekt und jeder einzelnen der
Unterstrukturen der aktuellen Klassenkomponente rekursiv auf. um so den gesamten
Unterstrukurbaum der Klasse auf das aktuelle PART bzw. SET zu kopieren.

Wieder zurück in m_INSTPART werden noch einmal alle direkten Nachfahren
des Klassenobjektes durchlaufen. wobei aber statt m_INHERIT_COMP jetzt
m_INHERIT_EXPR mit derselben Parameterbelegung gerufen wird. Diese Operation

Der DROOLY—Compiler (object.c) 95



vererbt jeweils den Wertausdruck der aktuellen Klassenkomponente an die
entsprechende Komponente des aktuellen Objektes und ruft sich anschließend
rekursiv für jede Unterstruktur der Klassenkomponente auf.

Die Kopie eines Wertausdruckes erfolgt über einen Aufruf der Operation
m_INHERIT aus dem Wertausdrucksmodul und wird bei dessen Beschreibung
erläutert.

In der Objekttabelle wurden durch die Vererbungsmechanismen folgende neue
Objekte erzeugt:

pre) sui) )cls)cls)cup)uxt)uxt)lwarwq)quautorll ) ) ) )relluxtlnxt) )n1tlipa
fix) iixlspec ) )cutlld )cwp)saw)cnt)cnt)operatorlo l)op2)val)deqlld )re1)re2lout)outlix
=::==={=:=::)=:==::::::::=:::{:::===:):::}:==):::)=:=:==::{:::l ::l:-:::-:l:::):::::::{:::}::=)=:::
. [il] )n_Ci_lrans.qtl
) CLASS ] ( 2 )

15) 11)Sil ) ) 1) 18) i)) ) ) )1011 ) ) ) 6) 2) 26) ) ) ) 22)
-—----)--°'-)--------)---)---)---)---)---)-°-)--°)------'-)---)---)—--)---)---)---)---)'--)°--)--'-
[18] (n_CE_Trans.gt.p_rirel

17) 9)511 ) 2) 1)-16) 19) ) ) )IOIE ) ) ) 8) ) 22) ) ) ) )
------)-—"-)--------)—--)---)---)---'---)---)---)--------)---)---)---)---)---)---)---)---)---)----
[19] )u_CE_Traus.qt.diiiusioul

il) 8)SLT ) 5) 1) 20) 21) ) ) )!011 ) ) ) 10) ) 22) ) ) ) )
------)-----)--------)---)---)---)---)---)---)---)--------)---)---)---)---)---)---)--—)---)---)----
[20) (n_CE_Trans.gt.difiusiou.widthl

19)-1036)P10Pi117) ) )-18)-19) ) ) )IOIL ) ) ) I)) ) ) ) ) ) )
------) __--- I ________l ...I ___) ___ I ___l ___) ___I ___) ________l ___ ) ___) ___ I ___l ___ ) ---! ___) ___l ___l ___-

| | l l l l | | | | l l l l l l l | | |
[21) )n_Cl_iraus.qt.shapel

17)—1034)50192 ) ) )—19) 22) ) ) )1010 ) ) ) 15) ) ) ) ) )
-‘----)-'---)------—-)---)---)---)---)---)---)--—)--------)---)---)—--)---)---)---)---)---)---)—---
[ll] (n_CE_Trans.qt.p55000l

11)-5000)11L11I01) ' -21)-17) )CIOSS 18) 19)

| I l | I l
| | I l l I

------l -.--.! --------) ...l ---l ---)---) ---l ...l ---l-------_l ---l ---) ---l ...l ---
l I l l l l | |

Damit ist auch der Deklarationsteil der Klasse n_CE_Trans vollständig
bearbeitet und der Parser beginnt mit dem Einlesen des Beschreibungsteils. In Zeile
22 werden alle Elemente der Menge n_CE_Trans.gt miteinander identifiziert. Im
Layout bedeutet das, daß alle Gates des beschriebenen n—Kanal-Transistors
miteinander verbunden sind. Da bei der Identifikation auch alle RELATIONS
miteinander verglichen werden müssen, werden merge—Anweisungen zunächst in
einer temporären Datei gesammelt und erst bei der Ableitung zu class_def‘inr'tion
wieder eingelesen und bearbeitet. Das Verfahren zur Bearbeitung einer
Identifikationsanweisung wird bei der Beschreibung des compare—Moduls im
Abschnitt 8.8 erläutert.

Die Anweisungen der Zeilen 23 und 24 sind quantifizierte RELATIONS. Dabei
entstand die Zeile 23 aus einer entsprechenden Anweisung im DINGO—II —
Topologieteil, Zeile 24 dagegen spezifiziert einen Wertausdruck und entstammt damit
dem Design Rule Teil der entsprechenden DINGO-II Objektbeschreibung. Wie schon
bei der Bearbeitung der Klasse Gate beschrieben, erzeugt der Parser aus jeder
dieser zwei Anweisungen zwei RELATION—Objekte in der Objekttabelle. Die
Anweisung der Zeile 25 entstand aus einer Design Rule des extern—Blockes des
DINGO—II — Design Rule Teils. Die Operanden _se_lij werden als Referenzen auf das



Der DROOLY-Compiler (object.c) 96



aktuelle Klassenobjekt in die Objekttabelle eingetragen. Damit sind alle Objekte
dieses Beispiels in der Objekttabelle eingetragen: (9)

pre) soil )cls)cls)cwplﬂxtlnxt)lwq)rnq) quantor/l ) ) ) )rel)nxt) nit) )ort)ipa
fix) iir)spec ) )cutlld (cup)san)cnt)cnt)operator)o pl)° Pl)va lde eq)ld )rel) rel)out)ou t)ix
::::::)=::::'::::::=:‘:::l: ::::=:):::l:::):::i:: =:)::::::::l :::: =:l:::::::)=:= }:::':::}: -:):::=::::
[24] )u_CE_Traus.ps5001l
15)—5001)REL11101) ) )-17) 25) ) ) 'IIDIST ) 11) 16) ) ) ) ) ) 25)
------)-----)------'-'---)---l---)---)---)---)°--'--------)-——)-°-)---)---)---)---)---)---l-—-)--—-
(25) (u _CE_1raus. p55002| '
15)-5002)s_12111.' ) )- 24) 26) ) ) )ALL ) 11) 24) ) ) ) ) ) ) 27)
----.-) _____l ________ |___) ___I ___)---l ---l ---l --- l ________ ) ___) ___) ___) ___)---l ---) ___) ___I ...l ___-
l l l | I | | I I I l 1 l l | I I l | |
[26) (n_CE_Trans.p55003I
15)-5003)AEL11101) ) )-25) 21) ) ) )IIDIST ) 17) 16) 14) ) ) 24) 24' 21' )
------)---—-)--------)---)---)---)---)---)---)---l--------)---)---)---'---)---)---'---'---'---)----
(21] (u _CE _iraus. p55004|
15)-5004)S_15L11.' ) )-26) 28) ) ) )ALL ) 11) 26) ) ) ) ) ) ) 28)
------'-----)--------)---)---)---)---)---)---)---)-—------)---)---)---)---)---)---)---)---)---)-"'
[28) In_CS_1rans.ps$005l
15)-5OOSIRELATIOI) -22)-15)

)))IST )15)15) 15)
l
l

Die vollständige Verkettung aller Objekte sei noch einmal in einer Graphik
dargesteﬂt:

mm Aulﬂ'utllllbol.



„
(.. .':’f
aufj\_“.. ......

'/////'////’ 9'P'5095 //\
.ﬁ Qi"
"" gun-au











well . _ nel-u
:llll: n_Won h_CE_TIIHIIIIOI luo$olyq,
,///j// „
/»/.






"‘ llllh°n
p05002
.. “ nuwlon
gl.pu50°1
.

m„£

ﬁng-‚uw

. VIHI/

-’1 > auf/';

/ 4 f.,

f/».‚ ’/////////
„






nl








9' '
ein-: an. (p_Di!) "











_//” Ill.lloll
///„ gl. p95004
/////////////// non

Abb. 8.10: Tabelle der Beispieltechnologie (III)



MhMMMMmdmmmnmmmmdemmmMamwmnmmﬂMmm%thü%üMﬂnmmm

Der DROOLY—Compiler (expr.c) 97



## 8.7 Wertausdrücke expr.c

Das Modul expr.c des Compiler—Backends stellt die Ausdruckstabelle als
abstrakten Datentyp zur Verfügung. Darin definiert ist die Datenstruktur der
Ausdruckstabelle als Vektor von Elementen des oben beschriebenen Typs exprT.
Jedes Vektorelement speichert einen atomaren Wertausdruck in der
Ausdruckstabelle. Die Vektorgröße ist wie die der Objekttabelle statisch deklariert.
Da jedoch auch hier jede Anforderung und "Inbetriebnahme" eines neuen
Atomarausdruckes ausschließlich über einen Aufruf einer Funktion gen_expr_entry
abgewickelt wird, ist für eine Erweiterung auf dynamische Reallozierung bei
drohendem Tabellenüberlauf nur eine entsprechende Anpassung dieser einen
Funktion nötig.

Umgebung

Neben der Kommunikation mit dem Parser bei der Neuerzeugung von
Wertausdrücken und mit dem Objektmodul bei der Vererbung von Ausdrücken werden
zwei Dateideskriptoren tabout zur Ausgabe einer lesbaren Form der
Ausdruckstabelle (nach Protokollierung der Objekttabelle), trfout zur Ausgabe einer
DROOLY—ähnlichen Form der Wertausdrücke und tecout zur Ausgabe der
Technologiedatei aus dem Hauptmodul importiert (siehe Fußnote auf Seite 79). Aus
dem Parser importiert wird der globale Zähler expr_depth, der jedoch nur in den
Funktionen incr_expr und decr_expr manipuliert wird. Es werden explizit keine
Datenstrukturen aus diesem Modul exportiert. Auf die Ausdruckstabelle kann
außerhalb des Moduls expr.c nur über die exportierten Funktionen zugegriffen
werden. Als Parameter zur Referenz auf Einträge in der Ausdruckstabelle erwarten
all diese Funktionen einen positiven, ganzzahligen Wert. Da der nullte
Ausdruckstabelleneintrag nicht benutzt wird, dient der Index 0 auch hier als
unspezifizierter (NIL-) Index.

Struktur

Der direkte Zugriff auf die Felder eines atomaren Ausdrucks erfolgt innerhalb
des Moduls ausschließlich über einen Satz von Makros, wobei für jedes Feld in der
Struktur exprT ein entsprechendes Makro (e_...) definiert ist. Außerhalb des Moduls
expr.c sind diese Makros nicht zugänglich. Als zentrale Schnittstelle zu den in der
Ausdruckstabelle gespeicherten Informationen dient die Funktion emsg, die
gleichsam eine Nachricht an einen bestimmten Atomarausdruck in der
Ausdruckstabelle absetzt. Diese Funktion erwartet als ersten Parameter den Index
des betroffenen Atomarausdruckes (der Empfänger der Nachricht) und als zweiten
Parameter einen Schlüssel, der die Art der Aktion auf dem Atomarausdruck
spezifiziert (der Selektor). Die erlaubten Schlüssel sind in dem Modul token.c
definiert und werden bei der Übersetzung des Compiler—Backends automatisch
zusammen mit den Schlüsseln der Funktion msg aus dem Objektmodul in die
Kopfdatei specs.h übernommen. Die emsg-Funktion ruft jeweils eine Hilfsfunktion
auf, die dann die eigentliche Manipulationen auf der Ausdrucksigabelle ausführt. Die
Aufruffolge emsg —> Hilfsfunktion wird in der folgenden Beschreibung
zusammenfassend als Operation mit dem Schlüssel als Operationsname bezeichnet.

Der DROOLY—Compiler (expr.c) ' 98



—.incr_oxpr

)—-decr_expr

(pur-cm:)

yypuno———gen exprj—-mon_oxpr_anlry

"“ —_oinii_oxp _4

'; r3duce_condilion-...evnl_cllulell (par-cm:)
' —ocop ex ’

‚ä " " )d?.°.“ZZ.-‘.ZTISZÄL..
° Wer_unreß— ‘ '

—-amwg——°omuqlNHEan
ﬂmlgC_PROPERTY
‘—-omlgCOND_OR
—„mqu0ND_AND
“«——omngR
——=remogANo

Cam-qPATTRIB

om-qASYMMETHIC







Abb. 8.1]: Die Struktur des expr—Moduls
Neben den genannten obligatorischen zwei Parametern akzeptiert emsg keinen,

einen oder zwei weitere Parameter, die an die jeweilige Hilfsfunktion weitergereicht
werden. Das angebotene Aktionsspektrum umfaßt folgende Operationsklassen.

Elementare Leseoperationen

m_GCOND Lesen des Feldes cond
m_GSPEC Lesen des Feldes spec
m_GLEFT Lesen des Feldes left
m_GRIGi-IT Lesen des Feldes right
m_GATTRIB Lesen des Feldes attrib
m_GMINVAL Lesen des Feldes minval
m_GEQVAL Lesen des Feldes eqval
m_GMAXVAL Lesen des Feldes maxval

Im Compiler—Backend wird zwar keine dieser Operationen benötigt, im Hinblick
auf eine universelle Einsetzbarkeit der Tabellen als abstrakter Datentyp sind sie
jedoch aufgenommen worden.

Elementare Schreiboperationen (m_P...)

Zum Aufbau der Wertausdrücke müssen nur zwei Felder eines
Atomarausdruckes gesondert geschrieben werden. Alle übrigen Felder werden
entweder direkt bei der Allozierung eines neuen Atomarausdruckes oder implizit bei
einer der komplexeren Operationen und Funktionen gesetzt:

m_PREF Schreiben des Feldes cond
m_PATTRIB Schreiben des Feldes attrib
Operationen zur Erzeugung von Verknüpfungen i

Mithilfe dieser Klasse von Operationen können Ausdrucksbäume erzeugt
werden. Dabei erhält ein Atomarausdruck mit einem dyadischejn Operator (OR, AND,
COND_OR, COND_AND) zwei Söhne, die er über seine le3t- und right-Felder
referenziert. Bedingte Ausdrücke werden mit dem triadischeri.’0perator C_PROPERTY
erzeugt. Auf den then-Zweig wird mit dem left—Feld verwiese'n,|auf den else-Zweig

'.<1.-s'‚

Der DROOLY—Compiler (expr.c) ' 99



mit dem right—Feld und auf die Bedingung wird mit dem cond—Feld verwiesen. Der
Operator ASYMMETRIC ist ebenfalls dyadisch und wird anstelle des monadischen
Operators VALUE in den wertspeichernden Atomarausdrücken einer 'asymmetrr'c—
Wertliste verwandt.

m_OR ) jeweils Eintragen des entsprechenden
m_AND ) Operators in das spec—Feld und Neuerzeugung
m_COND_OR ) eines Sohn—Ausdruckes, falls vorher schon
m_COND_AND ) ein Operator ungleich VALUE eingetragen
) war
m_C_PROPERTY Erzeugung eines Bedingungs-Ausdruckes mit
then— und eße—Zweig als Söhne
m_ASYMMETRIC Eintragen von ASYMMETRIC in das spec—Feld

und Verweis auf die anhängende Liste

Operationen und Funktionen zur Behandlung der Vererbungsmechanismen

Auch im Ausdrucksmodul gibt es eine Operation und eine Funktion, die
Vererbungsmechanismen bereitstellen. Bei der Instanziierung einer Klasse durch
einem PART oder SET wird ja nicht nur die Komponentenhierarchie von der Klasse
auf das PART oder SET übertragen, sondern es müssen auch die in der Kompo-
nentenhierarchie eingebundenen Wertausdrücke mit Verweisen auf C_PROPERTIES
und C_RELATIONS kopiert werden.

m_INHERI'I‘ Aufruf von copy_expr
copy_expr Kopieren der gesamten Ausdrucksstruktur

Funktionen zur Behandlung bedingter Ausdrücke

Enthält die Klasse eines PARTS oder SETS Objekte vom PARAM-Typ und
darüber hinaus bedingte Wertausdrücke mit CLASSTESTS auf diesen PARAMS, kann
durch die aktuellen Parameter des Klassenaufrufs bei der Instanziierung der Klasse
durch ein PART oder SET die Klasse der PARAMS eindeutig festgelegt werden. Sie
verwandeln sich damit in PARTS bzw. SETS. Die darauf bezogenen CLASSTESTS
können aufgelöst und durch den zutreffenden Zweig ersetzt werden. Die
CLASSTEST—Objekte in der Objekttabelle werden nicht mehr benötigt und können
freigegeben werden.

reduce_condltion Reduzieren von CLASSTESTS, falls die Klasse
inzwischen eindeutig bestimmt ist
set_unref Rekursives Freigeben des Parameters und aller

referenzierter Ausdrücke und Objekte

Funktionen für allgemeine Verwaltungsaufgaben

Jede Allozierung eines atomaren Wertausdruckes wird über die Funktion
gen_obj_entry abgewickelt. Es gibt noch eine komfortablere variante gen_expr, mit
deren Hilfe der neu allozierte Wertausdruck gleich initialisier ‘Iiverden kann. Dies
ist die Hauptfunktion zur Erzeugung neuer Atomarausdrücke v Parser aus. Da die
Syntax von properties und c_properties bzw. von relations und c_ relations
identisch ist, wird zu ihrer Unterscheidung die Ausdrucks—Schachtelungstiefe
benötigt. Befindet der Parser sich innerhalb der Ableitungteines Wertausdruckes,
kann es sich nur um den jeweiligen c_ .. .-Typ handeln. ;

Der DROOLY-Compiler (expr.c) ' 100



gen_expr_entry Allozieren eines neuen Atomarausdruckes
gen_expr Allozieren und initialisieren eines neuen
Atomarausdruckes
init_expr initialisieren eines Atomarausdruckes
incr_expr ) Verfolgung der Schachtlungstiefe
decr_expr ) von DROOLY—Wertausdrücken
Verfahren

Da auch die Erzeugung von Wertausdrücken in erster Linie syntaxgetrieben
beim Parsen einer DROOLY—Technologiebeschreibung erfolgt, soll sie anhand einiger
Beispiele erläutert werden. Diese Beispiele sind der ursprünglichen DINGO—
Diplomarbeit [Wie 85] entnommen und von Hand nach DROOLY übersetzt.

Anhand des ersten, einfachen Beispiels soll der prinzipielle Aufbau von
Wertausdrücken durch den Parser demonstriert werden:

S.65 unten
einfache unbedingte Design Rule

Q.—. —u }—l

ist [ >= 10 ] PA_Contact PA_Contact

Der Wertausdruck besteht hier nur aus einer einzelnen Wertbestimmung, die

der Parser über die Zwischenschritte term, simple_body und body zu expression
ableitet.

Im ersten Ableitungsschritt legt der Parser durch Aufruf von gen_expr einen
neuen Atomarausdruck an und initialisiert dessen Felder folgendermaßen:

cond minval

) left ) eqval
spec attrib ) ) right ) ) maxval
) ----------- ) ------- )---)--—) ------- )—--)---l -----
VALUE NIL NIL NIL NIL 10 10 NIL

Einfache Wertbestimmungen erhalten also den Typ VALUE (In den folgenden
Beispielen werden die NIL—Belegungen nicht mehr aufgeführt). Die folgende
Abbildung stellt den Syntaxbaum des Wertausdruckes graphisch dar:

oxptouign
,’ body \
(

)
o'implc_bod"f

) )

vl' .
) nue_<pecl

i>-rol

Abb. 8.12: Syn taxbaum eines einfachen Werta usdruckes

. ‘-..__.J'\- ...?-_.

Der DROOLY—Compiler (expr.c) 101



Das folgende Beispiel zeigt eine zwar unbedingte. aber aus zwei OR—
verknüpften Wertbestimmungen bestehende Design Rule:

1. 3.62 unten
; OR—verknüpfte einfache und ASYMMETRIC-Wertbestimmung
Contact.Cont_Cut dimension

[ @ == 500 as motric == 375 == 600 == 375 == 600 ) ]

Die folgende Abbildung zeigt den zugehörigen Syntaxbaum.

expr-union
/ body \
)

ﬂimplb_body

|imple_body simple_body
(



lo'rm (arm
\
vulue_tpoc_linl

/ vn)uo_lpec vuluo_lpec vulue wpecvnluo_upecvnlue_cpoq

// (\ (\ (\ (\\

ior—'500(uyrnmetnc -375 -6w -3'75 —600ll



Abb. 8.133: Syntaxbaum einer OR-Verknüpfung

In den ersten Ableitungsschritten werden die "=="—Operatoren mit den ihnen
jeweils folgenden Zahlenwerten zu value_desc abgeleitet. Ein Aufruf von gen_expr
erzeugt für jede dieser Wertbestimmungen einen Atomarausdruck und initialisiert
ihn wie im ersten Beispiel gezeigt. Die Wertbestimmungen hinter dem Schlüsselwort
as etrk: werden iterativ zu einer liste zusanunengefaßt. Die 'Verkettung der
Listenelemente in der Ausdruckstabelle nimmt die Operation m_ASYMMETRIC vor,
welche jeweﬂs die ahß liste und das neue Idstenelement als Parameter erhält Im
nächsten Ableitungsschritt werden die aus der ersten Wertbestimmung und aus der
Wertbesthnmungsüste abgeleh£ten Terme nüt dem gg-Operator zu shnple_body
zusammengefaßt. Die Verknüpfung in der Ausdruckstabelle wird von der Operation
nr_OR erzeugt. Diese erhält die beiden 'Penne als Parameter und üefert den Index
des resulüerenden Ausdruckes zurück. Bei den noch verbkübenden
Ableitungsschritten simple_body —> body —> expression werden keine
Manipulaüonen auf der Ausdruckstabeﬂe rnehr vorgenonunen. Die Ausdruckstabehe
enthält danach folgende Elemente (Die Wurzel des Ausdrucksbaumes ist der
Atomarausdruck nüt dem Index 1%

“\\-. ‚_

Der DROOLY—Compiler (expr.c) 102



cond minval
) left ) eqval
# spec attrib ) ) right ) ) maxval
---—) ----------- ) ------- )---l---l ------- )—-—)———) -----
1 OR 2 500
2 ASYMMETRIC 3 375
3 ASYMMETRIC 4 600
4 ASYMMETRIC 5 375
5 VALUE 600

An diesem Beispiel wird ein wichtiges Prinzip beim Aufbau der Wertausdrücke
deutlich. Der Wert VALUE im spec—Feld eines Atomarausdruckes wird wenn möglich
durch eine zusätzliche Funktion überschrieben. Dies spart Atomarausdrücke, weil
jeweils sowohl die Referenzfelder (cond, left, right) als auch die Wertfelder (minval,
eqval, maxval) benutzt werden. Eine graphische Darstellung des resultierenden
"Baums" verdeutlicht dies:

Abb. 8.13b: Gaphische Darstellung der Atome

Das folgende Beispiel zeigt eine bedingte Design Rule, deren Bedingung noch
aus einer Verknüpfung zweier Terme besteht. Jeder dieser Terme entspricht
syntaktisch einem forward und wird auch über dieselben Regeln abgeleitet. Der
Parser erkennt am Wert der globalen Variable expr_depth, ob es sich um ein
einfaches forward (gleiches gilt für eine relation) oder um Bedingungen handelt:

1. 3.73 unten
; bedingte Design Rule mit OR—verknüpften Bedingungen
PC_Trans.Diff width
[ i_f g; PC_Trans.Diff area [ < 28 ]
Gate.Poly Rngﬂ1[ > 3 ]
@ >= 3
l

Der DROOLY-Compiler (expr.c) 103

Der Parser arbeitet hier folgende Reduktionsschritte ab (gestrichelte Linien
markieren nicht vollständig aufgetragene Pfade):

/„.cxvplrenuion—\\
// \
/ bed

/ condition\mdilion body
(



forward (om-rd simple ho
/ oxpreuion expronio )
/ / term
, (
( vun/er.? &luo_lpe value npec‘
. . J \ “
[ if or PC_TransDrl‘l‘ area (<28 ) Gilt=Poly lenth [>3'l then \>—\3 ]

Abb. 8.14a: Syntaxbaum einer bedingten Design Rule

Wieder werden zunächst relationale Operatoren und Zahlenwerte zu
Wertbestimmungen und diese schließlich zu body bzw. expression abgeleitet. Von
den für die Bedingungen erzeugten Einträgen in der Objekttabelle werden die
Ausdrücke "[<28)" und "DS)" über das expr—Feld referenziert. In der Wertetabelle
wird je ein Atomarausdruck mit VALUE und dem Objektindex im cond-Feld über
einen Aufruf von gen_expr erzeugt. Diese beiden Atomarausdrücke werden bei der
Ableitung zu condition über einen Atomarausdruck mit COND_OR im spec—Feld
verknüpft. Dieser Ausdruck wird von einem Aufruf der Operation m_COND_OR
erzeugt, die analog zu m_OR arbeitet. Der Satz 3 condition then body wird dann
zu body und mit den umschließenden eckigen Klammern zu expression abgeleitet.
Die Operation m_C_PROPERTY erzeugt den zugehörigen Atomarausdruck. Sie erhält
die Ausdruckindizes der Bedingung, des then—Zweiges und bei Bedarf eines eli-
Zweiges und generiert über gen_expr einen neuen Atomarausdruck mit C_PROPERTY
im spec-Feld, dem Index der Bedingung im cond—Feld, dem Index des then—Zweiges
im left—Feld und u.U. dem el_se—Zweig im right—Feld. Es resultieren folgende
Einträge in der Ausdruckstabelle sind (Die Wurzeln der Teilbäume sind jeweils mit

"'" gekennzeichnet; die nüt "“" rnarkierten Felder enthalten Indizes in die
Objekttabeﬂe):
cond minval
) left ) eqval
# spec attrib ) ) right ) ) maxval
----) ----------- ) ------- )---)---) ------- )--—)——-) -----
* 1 VALUE 28
* 2 VALUE 3
3 VALUE 3
4 VALUE " "
5 VALUE "
6 COND_OR 4 5 \
* 7 C_PROPERTY 6 3

Der DROOLY—Compiler (expr.c) 104



Zur Verdeutlichung der Verkettungen auch hier die zugehörige graphische
Darstellung:

®—— @)

'—l
Emicxﬁ. TETTTj
on,-m-grug E Ob|„lolnlrng
_‚_.1—..

@@
@

Abb. 8.14b: Darstellung der Atome

Das letzte Beispiel enthält zwei geschachtelte, bedingte Design Rules. Die
innere Design Rule enthält eine spezielle Form von Bedingung, ein Classtest. Mit
diesem Konstrukt ist die Formulierung von Design Rules in Abhängigkeit von einer
bestimmten Parameterbelegung einer Objektklasse möglich. Wie im vorherigen
Beispiel wird auch für diese Form der Bedingung ein Eintrag in der Objekttabelle
angelegt. Seine Feldbelegung entspricht der eines deklarierten PARTS, nur enthält
das spec—Feld den speziellen Wert CLASSTEST.

1. 3.75 oben

; geschachtelt bedingte Design Rule mit AND-verknüpfter
; Bedingung und Classtest

@
[ } gn_d A_Wire width [ al >= 5 <= 10 ]
P_Wire width [ > 10 ]
then >= 5
% if A_Wire (classtest Metall )
then >= 7 % >= 3
] P_Wire A_Wire





[ir condillon [hen value_spec else )! condition (hen value spec elze value spec)
. '- .' - “') ‘

) ) l

’5 »7 3

A_Wire (damen man") ‘

ind A_Wire width [ und » 5 < 10 ] P_Wire width (> rd]
\

\_ „’
Abb. 8.153: verkürzter Syntaxbaum einer geschachtelt bedingt'c}n Design Rule

{

J

Der DROOLY—Compiler (expr.c) 105



Es resultieren folgende Einträge in der Wertetabelle (Die Wurzeln von
Teilbäumen sind auch hier mit "' markiert):

cond minval
) left ) eqval
# spec attrib ) ) right ) ) maxval
—---l ----------- l ------- l-—-)---) ------- l-—-l-—-l -----
* 1 AND 2 5 5
2 VALUE 10 10
* 3 COND_VALUE 5 ' 5 10
4 VALUE 10
5 VALUE 6
6 VALUE 5 5
7 CLASSTEST 7
8 VALUE 7 7
9 VALUE 3 3
10 C_PROPERTY 7 8 9
11 C_PROPERTY 3 6 10
* 7 C_PROPERTY 6 3

Die graphische Darstellung macht die Verkettungen wesentlich klarer:

‚—
:: Emlrlge in der Objekttabelle -_a:in_____ ‘) 3'
___

, . ‚\,
_/////f/x„.
„„-„...,

%r0bi /‘/„' ___.
e







Wurzel
!

EJ)—

Abb. 8.15b: Darstellung der Atome

erzeugt werden. Erbt eine Komponente die Unterstrukturen se er Klasse, so müssen
zwischen den neu erzeugten Unterstrukturen dies lben geometrischen
Einschränkungen wie in der Klasse generiert werden. Bei d -’ Instanziierung einer
Klasse werden also auch neue Wertausdrücke angelegt. Im fachsten Fall handelt
es sich um ein direktes Kopieren der einzelnen Atomarausdrücke, natürlich unter
Beachtung der neuen Indizes. Bedingte Wertausdrücke enthalten jedoch zusätzlich
Referenzen auf Objekte in der Objekttabelle. Auch diese Referenzen müssen

Wertausdrücke können nicht nur direkt beim Einlese)a| des DROOLY—Textes

aa.

Der DROOLY-Compiler (expr.c) 106



angepaßt werden. Schließlich werden bei der Instanziierung eines Klassenobjektes
mit formalen Parametern die Klassen aller enthaltener PARAMS eindeutig festgelegt.
Als Bedingung eines Wertausdruckes auftretente CLASSTESTS können bei der
Übertragung in die Instanz aufgelöst und durch den jeweils zutreffenden Zweig
ersetzt werden.

Die Vererbung eines Wertausdruckes wird im Objektmodul von der Operation
m_INHERIT_EXPR angestoßen. Diese ruft die Operation m_INHERIT aus dem
Ausdrucksmodul mit zwei Parametern. Als Empfänger wird der zu vererbendc
Ausdruck übergeben. Der zusätzlicheParameter übergibt die Objektinstanz, deren
Unterstruktur den aktuellen Audruck erben soll. M_INHERIT gibt den
Ausdrucksindex der erzeugten Kopie an m_INHERIT_EXPR zurück.

Die eigentliche Arbeit wird von der Funktion copy_expr erledigt, an welche
m_INHERIT den alten Audruck und den Index der erbenden Instanz weiterreicht.
Copy_expr erzeugt zunächst einen neuen Atomarausdruck, dessen Index am Ende
als Wurzel der Kopie an die rufende Funktion zurückgegeben wird. Die Wertfelder
(minval, eqval, maxval) des neuen Atomarausdruckes werden mit den
entsprechenden Werten aus dem alten Ausdruck belegt, ebenso wie das attrib—Feld.
Die Werte dieser Felder sind ja vom Index innerhalb der Ausdruckstabelle
unabhängig. Für jedes der drei Referenzfelder (cond, left. right) wird anschließend
ein eigener Zweig durchlaufen, falls der Wert des jeweiligen Feldes nicht NIL ist. In
jedem dieser Zweige ruft sich copy_expr rekursiv mit dem Wert des Feldes als
erstem und mit dem einfach weitergereichten Objektinstanz—Index als zweitem
Parameter auf, um von den referenzierten Atomarausdrücken ebenfalls Kopien
anzulegen. Die Indizes dieser Kopien werden als neue Referenzen in das left-,
right- bzw. cond—Feld eingetragen. Für das cond—Feld ist bei einem spec-Wert von
CLASSTEST, VALUE, COND_OR oder COND_AND allerdings eine Sonderbehandlung
nötig, da in diesen Fällen das cond-Feld auch einen Objektindex enthält
(CLASSTEST) bzw. enthalten kann.

In der Ausdruckskopie müssen alle Objektreferenzen nicht mehr auf
Unterstrukturen des Klassenobjektes verweisen, sondern auf Unterstrukturen der
Objektinstanz. Ein Teil des Pfadpräfixes der Klassenkomponenten ist dazu
abzutrennen und durch den der Instanzindex zu ersetzen. Eine Schwierigkeit ergibt
sich daraus, daß das Klassenobjekt nicht unbedingt immer ausschließlich von der
ersten Pfadkomponente bestimmt wird. Handelt es sich bei der zu instanziierenden
Klasse nämlich um ein lokales Modul, ist sein Pfad zwei- oder sogar mehrelementig
und entsprechend müssen in einem solchen Fall die ersten beiden oder mehr
Pfadkomponenten des Bedingungsobjektes ausgetauscht werden. Um entscheiden zu
können, welche Pfadkomponenten ersetzt werden müssen, ordnet der Parser Objekten
vom Typ C_PROPERTY, C_RELATION und CLASSTEST das den statischen Kontext
bestimmende Objekt (aus der globalen Variable act_class) als Klasse zu. Copy_expr
liest die Klasse aus dem betroffenen Objekt aus und stapelt die Suffizes des Pfades
solange auf einem Stack, bis es auf diese Klasse trifft. Die alte Klasse wird durch

den Index der aktuellen Objektinstanz ersetzt und der Pfad schrittweise mit Iookup
wieder aufgebaut.

Der letzte Aufruf von Iookup liefert den Index derjenigen Komponente der
Objektinstanz, deren Klassenzuweisung durch den bedingten Wertausdruck überprüft
wird. Zur Erzeugung eines neuen CLASSTEST—Objektes w-i‘d gen_obj_entry mit
Präfix und Suffix der Komponente aufgerufen. Dem dabei er eugten neuen Objekt
wird der spec—Wert CLASSTEST und der erste und zweite Klasseneintrag des
ursprünglichen CLASSTEST-Objektes zugewiesen. CLASSTEST?ObEMe werden nicht
in die Komponentenhierarchie ihrer Klasse eingehängt. « ';i

Der DROOLY—Compiler (expr.c) ‘ 107



Bei cond—Verweisen auf C_PROPERTY— oder C_RELATION—Objekte jedoch wird
das vom letzten Iookup—Aufruf gelieferte Objekt über link_object in die
Komponentenhierarchie eingehängt. Dort existiert allerdings schon das Objekt mit
gleichem Präfix und Suffix wie das C_PROPERTY— bzw. C_RELATION—Objekt. Um ein
individuelles, neues Objekt für die Bedingung zu erzeugen, wird daher link_object
derart aufgerufen (durch Setzen des Parameters force_entry), daß es in jedem Fall
einen neuen Objektindex zurückliefert und beide Objekte über das next_same—Feld
miteinander verkettet. Der Wertausdruck, den das C_PROPERTY— bzw. C_RELATION—
Objekt trägt, wird zuletzt durch einen rekursiven Aufruf von copy_expr kopiert.

Damit ist die Bearbeitung des Wertausdruckes aber noch nicht abgeschlossen.
Handelt es sich nämlich um einen bedingten Wertausdruck, so können in der
Bedingung enthaltene CLASSTESTS u.U. ausgewertet und dadurch die gesamte
Bedingung zu TRUE oder FALSE reduziert werden. Die Reduktion einer Bedingung
wird von der Funktion reduce_condition vorgenommen. Diese erhält als einzigen
Parameter einen Verweis auf die Wurzel der zu reduzierenden Bedingung. Ihr
Rückgabewert ist der Index der (reduzierten) Bedingung. Konnte die Bedingung
durch Auswertung der enthaltenen CLASSTESTS vollständig berechnet werden, hat
der zurückgegebene Atomarausdruck einen spec-Wert von CONST und einen eqval—
Wert von entweder TRUE oder FALSE. In copy_expr werden diese Felder nach der
Rückkehr von reduce_condition überprüft. Fand sich tatsächlich ein konstanter
TRUE— oder FALSE—Wert, kann die Wurzel des bedingten Wertausdruckes durch
set_unref freigegeben werden und statt ihrer mit dem then-Zweig (bei TRUE) bzw.
mit dem @—Zweig (bei FALSE) fortgefahren werden.

Reduce_condition liefert die übergebene Bedingung unverändert zurück, falls
sie keine CLASSTESTS enthält. Eine Bedingung kann folgende spec-Werte haben, für
die jeweils unterschiedliche Aktionen durchzuführen sind:

— VALUE. Die Bedingung besteht aus einem einzelnen Verweis auf ein
C_PROPERTY oder C_RELATION—Objekt. Reduce_condition gibt die Bedingung
unverändert zurück.

— C_PROPERTY. Eine Bedingung ist als Teil einer Bedingung nicht zulässig.
Dieser Fall wird mit einer Fehlermeldung quittiert und die Übersetzung
abgebrochen.

-— CLASSTEST. Dies ist der elementar auswertbare Fall. Zur eigentlichen
Überprüfung der Klassenzuweisung des betroffenen PARAMS wird die Funktion
eval_classtest aus dem Objektmodul gerufen. Diese kann die Klasse des
überprüften Objekteintrages in jedem Fall eindeutig bestimmen, weil bei der
Instanziierung einer Klasse alle formalen Parameter durch entsprechende
aktuelle Parameter belegt sein müssen und daher jedes PARAM in der Klasse
eine eindeutig bestimmte Klassenzweisung besitzt. Eval_classtest liefert je
nach Ergebnis des Klassenvergleichs entweder TRUE oder FALSE. Die ur—
sprüngliche Bedingung wird über set__ unref freigegeben und stattdessen ein
neuer Atomarausdruck mit dem spec— Wert CONST und dem eqval- Wert TRUE
bzw. FALSE erzeugt. _

i
— COND_ AND / COND_ OR. Besteht die Bedingung aus eintem booleschen Ausdruck,
bestehen folgene Möglichkeiten. 70
99 ‚;))/( ?>
— Das cond—Feld ist unbelegt. Am betrachteten 'tomarausdruck hängen
somit zwei Zweige, die über das left— bzw. right—Feld referenziert sind.

- Das cond—Feld verweist auf einen Objekteintrag? vom Typ C_PROPERTY
oder C_RELATION. Der andere Operand des COND_AND bzw. COND_OR
häng"ig dann am right-Feld.

Der DROOLY-Compiler (expr.c) ' 108



Entsprechend dieser möglichen Belegungen werden nacheinander die über die
left- und right—Felder referenzierten Teilausdrücke untersucht und das
jeweilige Ergebnis in zwei Variablen left_result und right_result gespeichert.
Diese Variablen können einen der drei Werte TRUE, FALSE oder FAILED
annehmen, wobei sie mit FAILED initialisiert werden. Hat der jeweilige Zweig
den spec—Wert CONST, so wird der zugehörigen Variablen der Wert des eqval-
Feldes (TRUE oder FALSE) zugewiesen. Handelt es sich bei dem Zweig um einen
CLASSTEST, wird eval_classtest aufgerufen und dessen Rückgabewert (TRUE
oder FALSE) der Variablen right_result zugewiesen.

Es folgt die Auswertung von left_result und right_result. Haben beide den
Wert TRUE, ist unabhängig von COND_AND oder COND_OR der Wert der
gesamten Bedingung ebenfalls TRUE und ein entsprechend initialisierter
Atomarausdruck wird für die Rückgabe dieses Ergebnis initialisiert. Ähnliches
gilt, falls beide Variablen den Wert FALSE haben. In jedem Fall ist dann das
Ergebnis der gesamten Bedingung ebenfalls FALSE und der Atomarausdruck für
die Rückgabe wird entsprechend initialisiert. Die Kombination TRUE/FALSE bzw
FALSE/TRUE führt bei COND_OR und COND_AND zu unterschiedlichen
Ergebnissen.

Bei COND_OR wird ein Atomarausdruck mit CONST TRUE als Ergebnis von
reduce_condition zurückgegeben.

COND_AND dagegen liefert für die Kombinationen TRUE/FALSE— bzw.
FALSE/TRUE einen Atomarausdruck, der mit CONST FALSE belegt ist. Ist einer
der Zweige TRUE, der andere dagegen noch nicht eindeutig ausgewertet, wird
der COND_AND—Atomarausdruck gelöscht und das Ergebnis eines rekursiven
Aufrufs von reduce_condition mit dem jeweils unaufgelöstem Zweig als
Ergebnis zurückgegeben. Sind schließlich beide Zweige nicht eindeutig
auswertbar, bleibt der ursprüngliche Wurzel—Atomarausdruck erhalten, seine
Zweige sind die Ergebnisse von rekursiven reduce_condition—Aufrufen.

Im Beispiel aus Abschnitt 8.7 enthält das Gate—Modul eine einfache
CLASSTEST—Bedingung (vgl. auch Tabellen in Anhang C), welche durch
Instanziierung mit aktuellen Parametern aufgelöst wird.

8.8 Topologievergleich compare.c

Die Operationen, die im Modul compare.c zusammengefaßt sind, arbeiten
zusammen zur Erledigung einer einzigen Aufgabe: dem Vergleich zweier
Objektstrukturen. Eigentliches Ziel dabei ist, korrespondierende Elemente der
Komponentenhierarchien zu identifizieren und miteinander in der Objekttabelle zu
verketten.

Umgebung

Die Operationen zum Topologievergleich werden bei der Ableitung einer merge—
Anweisung, der DROOLY—Entsprechung der Identifikation in DINGO-II nach folgenden

Regeln angestoßen: .

. (

merge : (merge merge_list ;!
merge_list : merge_inner_list ") "

merge_inner_list : IDENTIFIER IDENTIFIER ‘
merge_inner_list IDENTIFIER



Der DROOLY—Compiler (compare.c) ' 109



Wichtige Vorraussetzung für den Vergleich der beiden Objektstrukturen ist,
daß sie bereits vollständig mit allen Deklarationen und RELATIONS aufgebaut sind.
In DINGO—II und deshalb auch in DROOLY darf eine Identifikationsanweisung jedoch
an beliebiger Stelle im Beschreibungsteil eines Objektes stehen, um zwei seiner
Unterstrukturen miteinander zu identifizieren. Würde diese Anweisung sofort an
dieser Stelle zum Vergleich der beiden Topologien führen, könnten später noch
eingeführte RELATIONs nicht mehr berücksichtigt werden. Um dies zu vermeiden,
werden die Indizes von an einer Identifikationsanweisung beteiligten Objekten
zunächst paarweise auf eine temporäre Datei gesichert. Ist die Bearbeitung des
DROOLY—Textes abgeschlossen, wird für jedes gespeicherte Objektpaar die Funktion
merge mit den Indizes der beiden Objekte als Parameter aufgerufen.

Datenstrukturen

Um die gewonnenen (Zwischen-) Ergebnisse den beiden Objekten und ihren
Unterstrukturen einfach zuordnen zu können, bedienen sich die Funktionen dieses
Moduls des Zeigerfeldes user in der Objektstruktur. Das Objektmodul stellt zwei
Zugriffsoperationen puser und guser bereit, um diesen Zeiger zu schreiben bzw. zu
lesen. Zur Speicherung der Korrespondenzen wird ein Listenknoten corrT verwandt,
der ein Feld value und einen Zeiger next auf eine corrT—Struktur enthält. Variable
von diesem Typ werden im compare—Modul beim Auffinden einer neuen
Korrespondenz zwischen zwei Objekten angelegt, und bilden für jede Unterstruktur
des ersten Objektes eine Liste von korrespondieren Objekten in der
Komponentenstruktur des zweiten Objektes.

Die Vektoren lmerges und merges in der Objektstruktur sowie ihre
zugehörigen Zähler lmerge_cnt und rmerge_cnt werden erst nach Abschluß der
eigentlichen Vergleiche in merge durch Aufruf der Funktion add_merges aus dem
Objektmodul in beiden Objekten gesetzt. Die Korrespondenzlisten werden
anschließend nicht mehr benötigt und können wieder freigegeben werden.

Struktur

Bei der Abarbeitung der Identifikationsanweisungen durch Einlesen des
Temporärfiles wird jeweils die Funktion merge aufgerufen. Nacheinander werden von
ihr die Funktionen compare_decl zur Herstellung von Korrespondenzen zwischen
deklarierten Unterstrukturen und anschließend compare_relat zur Verifizierung
dieser Korrespondenzen durch Vergleich der auf den deklarierten Komponenten
definierten RELATIONS aufgerufen. Compare_relat wird allerdings nur gerufen, falls
compare_decl überhaupt irgend welche Korrespondenzen gefunden hat. Compare_decl
liefert true als Ergebnis, falls es zu jeder Unterstruktur des ersten Objektes
mindestens eine korrespondierende Unterstruktur im zweiten Objektes gefunden hat,
anderenfalls liefert es false. Konnte auch compare_relat seine Arbeit erfolgreich
beenden, liefert es ebenfalls den Wert true als Erbenis zurück. Merge durchläuft
daraufhin mithilfe des Generators (m_GPREFIRST, m_GPRENEXT) alle Komponenten
des ersten Objektes und ordnet durch Aufruf von add_merges der jeweiligen
Komponente im ersten Objekt ihre Identifikation im zweiten Objekt zu und
umgekehrt.

f.
„g.(

4

' "\o_

.. A..-x . . .-J:

Der DROOLY—Compiler (compare.c) 110



Compare_decl und compare_relat selbst rufen eine Reihe weiterer Funktionen
zur Erledigung bestimmter Teilaufgaben auf. Die gesamte Aufrufstruktur läßt sich
am besten mithilfe einer Graphik darstellen:

merge—mompare_de%Inyorclass —ns_DECL (object.c)
_} compare_2_card——-add_or———olnsott_int

!.
\——edd_merges (object.c)

ls_RELATION (obpct.c)
—-compare_ralal % corr_rolat_obj is_subsrruct (object.c)

compare_2_quint%us_0UANTOﬁ (object.c)

compore_2_rel3
delole_corr1 ‚ '

—-udd_cor. (ind_...„.
tpuser (object.c)
gusor (object.c)



Abb. 8.16: Die Struktur des compare

Verfahren

Der erste Schritt beim Aufbau der Identifikationen zwischen den einzelnen
Unterstrukturen ist der Vergleich der deklarierten Komponenten beider Objekte
durch die Funktion compare_decl. Ziel dabei ist der Aufbau von 1-zu—n—
Korrespondenzen zwischen je einer Komponente des ersten Objektes und einer
Anzahl möglicherweise korrespondierender Komponenten des zweiten Objektes (10).
Der zweite Schritt, der Vergleich der RELATIONS in beiden Objekten, löscht
diejenigen Korrespondenzen, deren RELATIONS "nicht zueinander passen", so daß bei
erfolgreicher Identifikation nur noch I—zu—I-Korrespondenzen übrigbleiben.

Der Vergleich der deklarierten Unterstrukturen durch compare_decl beginnt mit
der Überprüfung, ob es sich bei beiden Objekten um Layer handelt. Ist nur eines
der beiden Objekte ein Layer, kann ihre Struktur nicht übereinstimmen,
compare_decl liefert sofort false zurück. Sind beide Objekte Instanzen der gleichen
Layerklasse, werden bei SET—Deklarationen die erlaubten Kardinalitätsbereiche
durch die Funktion compare_2_card überprüft. Obwohl diese Funktion nur
Warnungen ausgibt und die Identifikation nur in dem Fall abbricht, daß sie auf
eine nicht korrekt aufgebaute Kardinalitätsangabe stößt, muß doch einiger Aufwand
getrieben werden, um alle in DINGO—II gegebenen Möglichkeiten der
Kardinalitätseinschränkung zu berücksichtigen. Aus den Möglichkeiten in DINGO-II
ergibt sich in DROOLY folgende Syntax für Kardinalitätseinschränkungen:

card_range : ' [' card_body '] '
card_body : eq_spec

) or_card .

) and ">=" INTEGER "<=" INTEGER
or_card : gr; or_card eq_spec '—‚

) @ eq_spec (
eq_spec : ”==" INTEGER }

;.
-i
(10) Der Begriff Korrespondenz wird im folgenden anstelle von Relation benutzt, um eine Verwechselung mit den Topologierelationen zu
vermeiden.

fi“.



Der DROOLY—Compiler (compare.c) 111



Für die Formulierung einer Kardinalitätseinschränkung bestehen in DROOLY
folgende Möglichkeiten:

- Einzelne Wertbestimmung: [
- Alternativliste: [ g g- ==
— Halboffenes Intervall: [ >=
— Geschlossenes Intervall: [

Compare_2_card beginnt mit der Analyse der Kardinalitätseinschränkung des
ersten übergebenen Objektes. Anhand ihres spec-Feldes lassen sich drei Fälle
unterscheiden, die später beim Vergleich mit der Kardinalitätseinschränkung des

zweiten Objektes anhand der Belegung eines Selektors unterschieden werden
können:

- VALUE: Entweder handelt es sich um eine einzelne Wertbestimmung (nur das
eqval-Feld ist belegt) oder um ein halboffenes Intervall (eins der Felder
minval bzw. maxval und das eqval—Feld sind belegt). Der Fall einer einzelnen
Wertbestimmung soll im folgenden wie eine Alternativliste behandelt werden
(Selektor = 0), der Fall eines halboffenen Intervalls bildet einen Spezialfall
der Intervallbehandlung (Selektor = 1).

- OR: Es liegt eine Liste alternativer Kardinalitätseinschränkungen in Form
eines ausschließlich mit @ verknüpften Wertausdruckes vor. Die einzelnen
Alternativen werden von einer speziellen Funktion add_or aus dem
Wertausdruck extrahiert und in einen Vektor in aufsteigender Reihenfolge
einsortiert. Der Selektor wird auf 0 gesetzt.

— AND: In diesem Fall liegt ein geschlossenes Intervall mit Untergrenze und
Obergrenze vor. Dabei ist die eine Grenze direkt im minval— bzw. maxval—Feld
des AND—Eintrages vermerkt, auf die andere Grenze wird mit dem right-Feld
verwiesen. Die beiden Grenzen werden extrahiert und in den Variablen lbl
(untere Grenze) und ub1 (obere Grenze) gemerkt. Der Selektor wird auf 1
gesetzt.

Bei der Analyse der zweiten Kardinalitätseinschränkung werden wieder diese
drei spec—Werte unterschieden. Alternativlisten werden in einen zweiten Vektor
einsortiert und Intervallgrenzen in zwei Variablen lb2 und ub2 gemerkt. Im Fall
eines Intervalls wird dem alten Selektorwert 2 hinzuaddiert.

Der folgende Vergleich der so aufbereiteten Kardinalitätseinschränkungen wird
vom resultierenden Wert des Selektors gesteuert:

Selektor=0 (Einzelne(r) Wert(e) - Einzelne(r) Wert(e)). Die beiden Vektoren werden
verglichen. Nur bei identischer Anzahl und gleicher Werte resultiert der
Rückgabewert true. Alle anderen Fälle werden mit einer Warnung und dem
Rückgabewert false quittiert.

|

Selektor=l (Intervall — Einzelne(r) Wert(e)). '

Selektor=2 (Einzelne(r) Wert(e) - Intervall). Diese bei en Fälle resultieren in
' jedem Fall in einem Rückgabewert false. Je nachdem. ob alle Alternativen im
Intervall liegen oder nicht wird jedoch eine terschiedliche Warnung
ausgegeben.

Selektor=3 (Intervall - Intervall). Die jeweiligen Intervallgrenzen werden
verglichen (lbl mit lb2. ub1 mit ub2). Sind die Intevalle identisch, ist der
Rückgabewert true, sonst false bei entsprechender Wa »; ung.

Der DROOLY—Compiler (compare.c) 112



In der Funktion compare_decl wird der Rückgabewert von compare_2_card
ignoriert, d.h. mit Ausnahme der ausgegebenen Warnungen haben unterschiedliche
Kardinalitätsvereinbarungen keine Auswirkungen auf den weiteren Verlauf der
Identifikation. Compare_decl meldet bei zwei Layern einfach aufgrund gleicher oder
ungleicher Layerzugehörigkeit Erfolg (true) bzw. Nichterfolg (false) an merge

zurück.

Handelt es sich bei den übergebenen Objekten nicht um Layer, werden ihre
Unterstrukturvereinbarungen miteinander in zwei ineinander geschachtelten
Schleifen miteinander verglichen.- Aus dieser Struktur resultiert ein
Laufzeitverhalten von Komponentenzahl (objl) mal Komponentenzahl (obj2). Der
Vergleich verläuft nach folgendem Schema:

whüe (noch weitere Komponenten compi des ersten Objektes

and wenigstens eine Korrespondenz für die aktuelle
Komponente gefunden)

(
whüe (noch weitere Komponenten comp2 des zweiten Objektes)
(

rufe rekursiv compare_daﬂ mit den aktuellen Komponenten
if (erfolgreich)
)
merke "mindestens eine Korrespondenz gefunden"
füge comp2 zur Korrespondenzliste von compi hinzu
)
nächste Komponente comp2 des zweiten Objektes
)
nächste Komponente compi des ersten Objektes

)

Ergebnis von compare_daﬂ ist true, falls zu jeder Komponente
compi mindestens eine Komponente comp2 mit gleicher
Unterkomponentenstruktur gefunden wurde; sonst ist das
Ergebnis false

Falls compare_decl erfolgreich eine l—zu—n—Korrespondenz zu jeder
deklarierten Unterstruktur des ersten Objektes herstellen konnte, fügt merge das
zweite Objekt der Korrespondenzliste des ersten Objektes hinzu und ruft
compare_relat, um durch Vergleich der RELATIONS in beiden Objekten die
Korrespondenzen zu 1—zu—l-Korrespondenzen zu reduzieren und dadurch eine
eindeutige Identifikation herbeizuführen.

Compare_relat vergleicht die RELATIONS zwischen den mit compare_decl
gewonnenen Korrespondenzen, und streicht dabei Korrespondenzen aus der
Korrespondenzliste des ersten Objektes. Im Pseudocode für compare_relat werden
folgende Variablen verwandt:

011 wird nacheinander mit allen Komponenten des I. Objektes belegt
c21 alle zu c11 korrespondierenden Komponenten des 2. Objektes

rl eine RELATION im 1. Objekt mit c11 und einem weit 'ren Operanden
r2 eine RELATION im 2. Objekt mit c21 und einem weit ren Operanden:

Der DROOLY-Compiler (compare.c) 113



for (alle Unterstrukturen und
Unterunterstrukturen c11 des 1. Objektes)
)

if (cll ist RELATION)
)
rufe corr_relat_obj zur Sonderbehandlung von RELATIONS
weiter mit nächstem c11 (äußerste for-Schleife)
)
while (alle Korrespondenzen c21 von c11)
[ .
setze "mindestens ein r2 gefunden"
while (alle RELATIONS rl, die sich auf c11 beziehen
and mindestens ein r2 gefunden)
(

setze “noch nicht mindestens ein r2 gefunden"
While (alle RELATIONS r2, die sich auf c21 beziehen)
(

i; (die zur Ermittlung der Korrespondenz der RELATIONS
rl und r2 gerufene Funktion compare_2_relat
liefert true)

(

12 (die rl und r2 beherrschenden Quantoren
werden von der Funktion compare_2_quant
als übereinstimmend makiert)

)

setze "mindestens ein r2 gefunden"
)
else
Fehler: Quantorketten nicht identisch
)
nächste RELATION r2
)
nächste RELATION rl
)
if (nicht mindestens ein r2 gefunden)
(
da keine korrespondierende RELATION r2 zu rl gefunden,
lösche Korrespondenz zwischen c11 und c21
)
nächste Korrespondenz c21
) /* c21 */
) /* c11 */

/* Feststellung der Eindeutigkeit: */
setze Rückgabewert auf true
for (alle Unterstrukturen und Unterunterstrukturen c11
des 1. Objektes) .
{ .
if (c11 ist SET_RELATION)
(
überspringe dieses c11 (SET_RELATIONS‘ eben keine

Korrespondenzen)

.g-w

Der DROOLY—Compiler (compare.c) 114



i£ (Korrespondenzliste von c11 ist leer (1—zu—O-Relation)
g_ —"— enthält mehr als ein
Element (1—zu—n—Relationll
(
Fehler: Korrespondenzen zu c11 nicht eindeutig
setze Rückgabewert auf false
)
)
Funktionsergebnis von compare_relat ist
der resultierende Rückgabewert

Wie man dem Pseudocode entnehmen kann, ruft compare_relat (neben einigen
nicht erwähnten Hilfsfunktionen) die drei Funktionen corr_relat_obj,
compare_2_rel, und compare_2_quant auf.

Corr_relat_obj erledigt die Sonderbehandlung für RELATIONS. Eine übergebene
RELATION rl aus dem ersten Objekt wird mithilfe der Funktionen compare_2_rel
und compare_2_quant mit allen RELATIONS aus dem zweiten Objekt verglichen.

Compare_2_rel überprüft dabei zunächst, ob zwischen den beiden übergebenen
RELATIONS rl aus dem ersten Objekt und r2 aus dem zweiten Objekt eine
Korrespondenz besteht. In diesem Fall ist compare_2_rel fertig und liefert TRUE
zurück (11). Es folgt ein etwas länglicher, rekursiver Vergleich der beiden
RELATIONS anhand ihrer Operanden und Operatoren, der sich auch hier am klarsten
durch seinen Pseudocode beschreiben läßt (es werden Variablenbezeichner mit
denselben Bedeutungen wie bei compare_relat benutzt):

5 " «ﬂ ‘;„iä‘._ ‚.



(II) Zur Unterscheidung vom ‚booleschen Wert true ist dieser Rückgabewert Element der llertemenge ) TRUE, 811.91), 9111111) |.

Der DROOLY-Compiler (compare.c) ' 115



/* Überprüfung der jeweils ersten Operanden c11 von rl und c21
von r2: */
;; (c11 ist RELATION)
(
if(c21 ist keine RELATION)
(
Funktionsergebnis ist FALSE
22922
|
/* sowohl c11 als auch c21 sind RELATIONS: */
if(cll und c21 korrespondieren nicht (ermittelt durch
rekursiven Aufruf von compare_2_rehl
(

Funktionsergebnis ist FALSE bzw. FAILED, je nach Ergebnis
des rekursiven Aufrufes
return
)
/* c11 und c21 sind korrespondierende RELATIONs: */
füge c21 zur Korrespondenzliste von c11 hinzu
) /* cll ist RELATION */
%
l
/* c11 ist deklariert: */
iI(cll ist nicht Unterstrukur des ersten Objektes)
)
Funktionsergebnis ist FALSE bzw. FAILED, je nachdem
ob c21 Unterstruktur des zweiten Objektes ist oder nicht
return

)
if (zwischen c11 und c21 besteht keine Korrespondenz)
[
Funktionsergebnis ist FALSE
return
)
) /* c11 ist deklariert */

/* Überprüfung der jeweils zweiten Operanden c12 von rl und
c22 von r2: */
i_f (c12 ist RELATION)
(
1I(c22 ist keine RELATION)
(
Funktionsergebnis ist FALSE
return
)
/* sowohl c12 als auch c22 sind RELATIONS: */
i£(c12 und c22 korrespondieren nicht (ermittelt durch
rekursiven Aufruf von compare_ 2_ reD)
(

Funktionsergebnis ist FALSE bzw. FAILSD, je nach Ergebnis
des rekursiven Aufrufes (
return ?

)

/* c12 und c22 sind korrespondierende RE ATIONS: */
füge c22 zur Korrespondenzliste von c12 nzu
) /* c12 ist RELATION */

Der DROOLY—Compiler (compare.c) ' 116



else
)

/* c12 ist deklariert: */
i_f (c12 ist nicht Unterstrukur des ersten Objektes)
(

Funktionsergebnis ist FALSE bzw. FAILED, je nachdem
ob c22 Unterstruktur des zweiten Objektes ist oder nicht
return

)
i_f (zwischen c12 und c22 besteht keine Korrespondenz)
[

Funktionsergebnis ist FALSE
return
)
) /* c12 ist deklariert */

if (Operatoren ungleich)
)
Funktionsergebnis ist FALSE
return
)
/* da wir bis hierher gekommen sind, hat sowohl cl]
als auch c12 aus dem ersten Objekt eine Korrespondenz
im zweiten Objekt: */
füge r2 zur Korrespondenzliste von rl hinzu
Funktionsergebnis ist TRUE

Die dritte große, von compare_relat gerufene Funktion ist compare_2_quant.
Sie vergleicht zu zwei gegebenen Outermost-RELATIONs rl und r2 die
Quantorketten. Beim Vergleich verläßt sich die Funktion darauf, daß die Quantoren
in einer RELATION die äußersten Objekte darstellen. Die DINGO—II—Syntax ebenso
wie die DROOLY—Syntax läßt jedoch keine anderen Konstruktionen zu, so daß dies
keine Einschränkung darstellt. Der Vergleich der beiden Quantorketten kann also
abgebrochen werden, sobald es sich bei der jeweils am rechten Operanden hängenen
RELATION nicht mehr um eine SET_RELATION handelt. Compare_2_quant liefert
true, falls beide Quantorketten gleich sind, sonst liefert sie false.

Das Modul compare.c enthält noch eine Reihe weiterer Funktionen, die zum
Aufbau, Löschen oder Suchen von Korrespondenzen dienen oder die Listen von
alternativen Kardinalitätseinschränkungen verwalten. Die Aufrufe dieser Funktionen
sind in der Aufrufstruktur zu Beginn dieses Abschnittes dargestellt. Ihre Funktion
soll jedoch nicht weiter beschrieben werden, da es sich durchweg um die einfache
Verwaltung linearer Listen handelt.

Nach erfolgreicher Rückkehr aus der Funktion compare_relat bleibt für merge
noch als Aufgabe, die gewonnenen und durch Überprüfung in compare_relat
garantiert eindeutigen Korrespondenzen aus den Verweisen über die user—Felder des
ersten Objektes in die rmerges—Vektoren des erst'n Objektes und seiner
Unterstrukturen sowie in die lmerges—Vektoren des zweiten Objektes und seiner
Unterstrukturen zu übertragen und die user—Felder anschließend wieder
freizugeben. Merge ruft dazu für das erste Objekt, für alle seine Unterstrukturen
und deren Unterunterstrukturen die Funktion add_merge aus dem Objektmodul auf.
Add_merges erledigt die genannten Aktualisierun'géf"ü d' rmerge5— bzw. merges—
Vektoren. In einem zweiten Durchlauf über alle Unrfggrr und Unterunterstrukturen
des ersten Objektes werden die user—Felder wieder f'i‘eig“ eben.



Der DROOLY-Compiler (validate.c) ' 117



## 8.9 Erweiterte semantische Überprüfungen validate.c

Im normalen Übersetzungsvorgang wird die Semantik der Topologieoperatoren
nicht zu Überprüfungen herangezogen. Durch Setzen der Option -v beim Aufruf des
Compiler—Backends ist es jedoch möglich, auch derartige Überprüfungen
durchzuführen. Bei jedem Eintrag einer neuen Topologierelation in die Objekttabelle
wird dann überprüft, ob die gewünschte Relation zwischen den Operanden nicht
einer bereits in der Tabelle enthaltenen Relation widerspricht. Widersprüchliche
Relationen werden mit einer Fehlermeldung zurückgewiesen. Zusätzlich werden
neben der einen geforderten auch alle solchen Topologierelationen in die
Objekttabelle eingetragen, die automatisch aus der geforderten Relation folgen.

Die Überprüfung findet im Modul validate.c statt. Sie beruht auf eingebauten
Tabellen, die resultierende bzw. verbotene Relationen zwischen den Operanden einer
Topologierelation verzeichnen. Der erste Schritt legt eine einfache Topologierelation
zugrunde, die aus zwei Flächenelementen a und b und einem Operator besteht.
Dabei spielt es keine Rolle, ob die Flächenelemente Instanzen von deklarierten
Strukturen oder Resultate anderer Topologierelationen sind. Die folgende Tabelle
benennt jeweils zu einer gegebenen Relation diejenigen Relationen,

— die nicht erlaubt sind (" "),
— die erlaubt aber nicht unbedingt gültig sind ("e") bzw.
- die in jedem Fall gültig sind ("x"):













- o o o - o.

= H O _.

e. o 3 f) = r.

@ (0 q _ n

.- w ; 0 :1'

”° 3
ab ba ab ba
indist X 8 |
cross X [
|

overlap ) X ' ) | |
enclose x e e
touch e e x
dist e e x





















Abb. 8.17: Valitabi: Relationen zwischen zwei Flächenelementen

In den Spalten für INDIST und ENCLOSE muß zusätzlich berücksichtigt werden,
daß die Relationen nicht symmetrisch sind, so daß hier unterschiedliche Fälle für
a'b und b‘a zu berücksichtigen sind. Beispielsweise ist bei einer gegebenen
Relation "a INDIST b" die Umkehrung "b INDIST a" nur möglich, wenn a und b die
gleiche Flächenrepräsentation haben.



Der DROOLY—Compiler (validate.c) 118



In einem zweiten Schritt werden die Verhältnisse zwischen den
Flächenelementen und der resultierenden Fläche x untersucht. Die Relationen a'x
bzw. x‘a und b'x bzw. x'b werden in zwei getrennten Tabellen untersucht:













" O 0 0 " Q. 5 2 2 g 8 E:
:| -. < =, o _. neu—» 9: ° 0 n = 21
neu—» & g ... „ .: g 9, : __. °_ „
3 u 1 5' % a'b "' m '7
. ° 10 . '° 0
a b a. 'a 'U ‚° . b 'b b' ob
& a . .
. . Indie) 6 ):
Indie!
cross x
cross
overla
overlap p "





one)
enc

touch touch

dist d'“

Abb 8.18: Valitab2: Relationen zwischen Flächenelementen und Resultatflächen

In einem dritten Schritt werden Topologierelationen aus drei Flächenlementen
a, b und c und zwei (verschiedenen) Operatoren x und y untersucht. In der
Tabelle werden bei einer Klammerung nach (axb)yc Relationen zwischen den
Flächenelementen a und c bzw. b und c aufgeführt, bei einer Klammerung nach
ax(byc) werden Relationen zwischen a und b bzw. zwischen a und c aufgeführt.
Auch in diesem Schritt lassen sich wiederum die Relationen zwischen den
Flächenelementen und den Resultatflächen untersuchen.

Würde die Untersuchung in dieser Weise fortgesetzt, müßten immer
umfangreichere Tabellen konsultiert werden. Erst ein rekursives Verfahren zur
gleichmäßigen Behandlung beliebiger Schachtelungstiefen kann dieses Problem lösen.
Im Rahmen dieser Arbeit soll dieser Weg jedoch nicht weiter verfolgt werden.

Implementiert ist lediglich eine Überprüfung nach der ersten Stufe. Ist beim
Aufruf des Compiler—Backends die —v — Option gesetzt, ruft der Parser nach dem
Eintragen einer neuen Relation die Funktion valid_topo mit dem neu eingetragenen
Relationsobjekt als Parameter auf. Valid_topo extrahiert daraus den Operator und
die beiden Operanden. Unter Verwendung des Generatorpaars (m_GFIRSTREL,
m_GNEXTREL2) werden zum ersten Operanden alle Relationen zum zweiten
Operanden überprüft. Durch Konsultierung einer Tabelle gemäß Abb. 8.17 wird
jeweils diese Relation und die als Parameter übergebene Relation überprüft. Gemäß
dem Tabelleneintrag können drei Fälle auftreten:

1. Die Relation widerspricht der neuen Relation. Eine Warnung wird ausgegeben.

2. Die Relationen können nebeneinander bestehen. Keine Aktion ist notwendig.

3. Die Relation gehört zu der Klasse von Relationenfdie in jedem Fall zusammen
mit der neuen Relation gelten. Ihre Existenz; wird beim anschließenden

"Auffüllen" dieser Klasse berücksichtigt.

Im folgenden werden diejenigen Relationen e;z_ehä die in jedem Fall zusammen
mit der neuen Relation gelten. Für jeden der operäiiz'or‘n INDIST, CROSS, OVERLAP,
ENCLOSE, TOUCH und DIST wird eine Relation erzeugt, falls nicht eine solche schon
besteht (Fall 3). Wird die neue Relation von Quantoren beherrscht, werden die
zugehörigen SET_RELATIONS aus der neuen Relation übernommen. Die neu erzeugte

Der DROOLY—Compiler (validate.c) 119



Relation wird in diesem Fall an das op2-Feld der innersten SET_RELATION
angehängt.
