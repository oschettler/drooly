5. Die Symboltabelle

Die DINGO-II Symboltabelle stellt neben der im folgenden Kapitel vorgestellten
Zwischensprache DROOLY die zweite Schnittstelle zwischen Frontend und Backend
des DINGO—II Compilers dar. Das Compiler—Backend erhält über die Zwischensprache
keine Bezeichner für Objekte, sondern nur deren Symboltabellenindizes. Zur Ausgabe
sinnvoller Fehlermeldungen benötigt das Backend jedoch auch die zugehörigen
Objektbezeichner.

Die Symboltabelle enthält darüber hinaus für eine Applikation u.U. weitere
wichtige Informationen wie etwa die im DINGO—II Deklarationsteil definierten
Layer—Attribute, Defaultwerte für Geometrieklassen und Maßeinheiten usw. Daher
bildet die Symboltabelle neben den vom Backend erzeugten Tabellen für
Objektstrukturen und Wertausdrücke den dritten Bestandteil der
Technologiedatenhaltung in IMAGE. auf den Applikationen zugreifen können. In
dieser Arbeit werden aber nur diejenigen Strukturen und Operationen
berücksichtigt, die für das Compiler—Backend von Bedeutung sind.

“J

Die Symboltabelle - 28



Aus der Sicht des Compiler—Backends hat die DINGO—II Symboltabelle eine
recht einfache Struktur. Sie besteht einfach aus einer Liste von deklarierten, nicht
qualifizierten Bezeichnern. in der jeder Bezeichner nur genau einmal auftritt. Durch
seine Position in der Liste wird jedem Bezeichner ein eindeutiger Index zugeordnet.
Beim Start des Compiler-Backends wird diese Liste in einen Vektor von Zeigern auf
Bezeichner eingelesen.

Das Symboltabellenmodul des Compiler-Backends exportiert nur zwei
Operationen. Die eine Operation rdsymtb hat als einzigen Parameter den File—
Deskriptor der externen Symboltabelle. Rdsymtb ließt beim Start des Compiler—
Backends die Symboltabelle ein und alloziert dabei Speicherplatz für die Bezeichner
der definierten Objekte. Mit einer zweiten Operation grepstr liest das Backend die
Bezeichner aus der Symboltabelle aus. Als Parameter wird dazu der
Symboltabellenindex übergeben. Funktionsergebnis ist ein Zeiger auf den Bezeichner
des gewünschten Objektes.

Zur Veranschaulichung ist hier die resultierende Symboltabelle (aus der naiven

Sicht des Compiler-Backends) für die Beispieltechnologie aus Kapitel 8
wiedergegeben:

- 4 ANYLAYER

- 3 ANYMODULE
- 2 ANYDEVICE
— \ ANYCELL

! Metal 8 diflunion

2 Poly 9 p_wire

3 Conllcl_Cut 10 ilo_Polygon

4 n_Difl 1 1 n_CE_Tr-nui-tor
5 p_Dilf 12 wall

6 n_Woll 13 Q!

7 Gute

Abb. 4.1: Symboltabelle der Beispieltechnologie
