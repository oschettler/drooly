\„ Beschreibung
und
Integration
, von
IC — Technologie —
spezifikationen

Diplomarbeit von Olav Krämer
Dortmund im Mai 1989

Betreuer: Herr Dr. rer.nat. Rainer Brück
v Lehrstuhl 1, Abteilung Informatik
Universität Dortmund



_ ;_;‚«-_;__.;g.;‚_.' „

Gliederung der Arbeit

1. Einleitung

1
1.1 Der IC—Entwurfsprozeß 1
1.2 Die Bestandteile eines CAD-Systems für den
physikalischen Entwurf 3
1.2.1 Applikationen 4
1.2.2 Technologiespezifikation 4
1.2.3 Maskendaten 5
1.2.4 Datenhaltung 5
2. Die Technologiespezifikation in der Literatur 7
2.1 Bisherige Arbeiten 7
2. 2 Bestandteile einer Technologiespezifikation 12
2 3 Sprachphilosophie 13
2. 4 Integration in ein CAD— System 14
3. Die Sprache DINGO— —II zur Spezifikation von Technologien 14
3.1 Syntaktische Konventionen 15
3. 2 Aufbau einer Technologiespezifikation 16
3.2.1 Deklarationsteil 17
‘7 3. 2. 2 Objektdeklarationsteil 18
‚5/ Die Symboltabelle 27
Ä; Die DROOLY Zwischensprache 28
4.1 Grundlagen 28
4.2 Vererbungsmechanismen 29
4.3 Die Beispieltechnologie in DROOLY 30
4.4 Syntaktische Struktur 31
4.5 Layerdefinitionen 33
4.6 Objektklassendefinitionen 33
4.7 Deklaration von Unterstrukturen 34
4. 8 Beschreibungsteil 34
4. 9 Wertausdrücke 37
6. Übersetzung DINGO—II nach DROOLY 39
7. Die Tabellen 47
7.1 Objekttabelle 47
7.2 Tabelle der Wertausdrücke 58
8. Der DROOLY—Compiler 59
8.1 Präprozessor 59
8.2 Hilfsmodule für Fehlermeldungen, Dekodierung und
Überprüfung der Codeabdeckung 61
8.3 Hauptmodul main. c 64
8.4 Scanner scanner.c 66
8.5 Parser parser.c 68
8.6 Objekte object.c 79
8.7 Wertausdrücke expr.c 97
8.8 Topologievergleich compare. c . 108
8. 9 Erweiterte semantische Überprüfungen validate. c ‚ 117
9. Das Technologieinterface für Applikationen / 119
10. Beispiel für eine technologieinvariante Applikation:
Ein Bauelement—Extraktor __. 119
Anhang A: Liste der DROOLY-Token und Schlüsselworte ] 127
Anhang B. Fehlermeldungen 129
Anhang C. Die vollständigen Tabellen der Beispieltechnologie 130
Literatur 133

/M _Amg/ f<
«Z_ 3’| GMVL\ EJk\/Inl(_l‚ ‚(A/\\

J!



- u

?;j#-
