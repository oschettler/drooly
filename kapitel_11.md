1 1. Ausblick

In diesem Kapitel sollen drei Aspekte betrachtet werden, die in der
Implementierung des DROOLY-Compilers nicht oder nur ansatzweise berücksichtigt
wurden, welche aber für den Komfort des Technologie—Compilers bzw. für die
Optimierung des Einsatzes der Technologiebeschreibung von Bedeutung sind und
somit sinnvolle Erweiterungen darstellen.

Trotz des bereits jetzt erreichten Umfanges des implementierten Verfahrens zur
Identifikation zweier Objekthierarchien sind noch Steigerungen im Komfort für den
Benutzer möglich und sogar wünschenswert. Bisher bleiben die speziellen
semantischen Eigenschaften der einzelnen Operatoren bei der Identifikation völlig
unberücksichtigt. Eine Identifikation kann also nur dann erfolgreich verlaufen,
wenn neben einer bis auf die Reihenfolge gleichen Struktur der Hierarchie
deklarierter Unterstrukturen die Relationen auf diesen Unterstrukturen in beiden
Objekten in gleichem Umfang und mit gleichen Operatoren vorhanden sind. Bei der
Untersuchung der einzelnen Operationen fällt jedoch auf, daß sich einzelne
Topologierrelationen durch unterschiedliche Operatoren beschreiben lassen.

Solcherart äquivalente Relationen könnten aber bei der Identifikation ebenfalls
berücksichtigt werden.

Mit diesem Punkt im Zusammenhang steht auch die Suche nach einem allgemein
anwendbaren Verfahren zur Validitätsüberprüfung topologischer Relationen. Das zur
Zeit implementierte Verfahren eignet sich nur zur Überprüfung gering
geschachtelter Topologierelationen. Darüber hinausgehende Überprüfungen erfordern
eine intelligentere Tabellensstruktur, um noch handhabbar zu sein.

(

Als letzter wichter Punkt sei auf eine Optimieruﬂgsmöglichkeit hingewiesen.
Bei der Erstellung der Tabellen werden quantifiziert Topologierelationen nicht
optimiert. Zwei Ansatzpunkte für derartige Optimierngen aus dem klassischen
Compilerbau lassen sich jedoch auch auf die Überse't wg"'von komplexen Topologie-
ausdrücken anwenden (Aho 78): ‚> '




— Vereinigung gemeinsamer Teilausdrücke
- Herausziehen von Schleifeninvarianten

Ausblick ' 122



Zur Anwendung des zweiten Punktes faßt man einen quantifizierten
Topologieausdruck als eine Schleife über alle Elemente der abhängigen Menge auf.
Das Herausziehen von Invarianten vereinfacht sich durch den einfachen Aufbau
dieser Schleifen und ihre Abhängigkeit von jeweils nur einer Variablen. Dem
Optimierungsverfahren liegt der Aufbau eines gerichteten, azyklischen Graphen
(DAG) zugrunde.

Als Vorbereitung ist es erforderlich, zunächst mehrfach auftretende,
quantifizierte Mengenbezeichner durch neue, eindeutige Bezeichner zu ersetzen und
die Quantoren entsprechend zu vervielfachen. Dieser Schritt ist erforderlich, damit
jede Instanz einer Menge mit jeder anderen in Relation gesetzt werden kann. Dies
ist; gerade die Semantik einer Regel wie

some A
enclose [ ] A A .



Da eine solche Regel eine Beziehung zwischen Elementen einer Menge A und
nicht zwischen zwei Mengen A beschreibt, würde man die Regel mithilfe von
Prädikaten folgendermaßen formulieren:

3 a1, a2 eA: enclose (a1, a2 )

Nach einer derartigen Umformung ergibt sich z.B. für den komplexen
Topologieausdruck von Seite 43 folgender neuer Ausdruck:





lsome A1

2 some A2

3 gl_l c

4 overlap [ ]

5 indist [ ] C B

6 indist [ ]

7 enclose [ ] A1 A2
8 B

Mit dem in [Aho 781 auf den Seiten 418 ff. angegebenen Algorithmus zum
Aufbau eines DAGs aus einem Dreiadress—Code ergibt sich für diesen umgeformten
Topologieausdruck der folgende DAG, in dem gemeinsame Teilausdrücke bereits
zusammengefaßt sind (Die einzelnen Teilausdrücke sind durch ihre Zeilennummer
identifiziert):

overlap(4)

ip(_51' ‚/— in(6)—\ (
c] \ B ———>‘<E'.Tcﬁeq)
/' "\

A1 A'



Ausblick ' 123



Zur Verlagerung von Schleifeninvarianten läßt sich ebenfalls dieser DAG
verwenden, wenn die benutzte Datenstruktur Durchläufe von den Blättern zur”
Wurzel erlaubt. Für jeden Quantor wird ein neuer DAG aufgebaut, der die auf der
Schachtelungstiefe der zugehörigen Schleife berechenbaren Teilausdrücke aufnimmt.
Die Bearbeitung beginnt mit mit der abhängigen Variablen des innersten Quantors,
im Beispiel ist das "a_ll C". Der neu erzeugte DAG erhält als Namen diesen Quantor.
Im ursprünglichen DAG werden von C aus alle Pfade zur Wurzel der Reihe nach
durchlaufen. jeder besuchte Operator wird mit seinen direkten Söhnen in einen
neuen DAG aufgenommen und aus dem alten gelöscht. Nach dem Durchläufen des
Pfades für "a_ll C" ergibt sich folgendes Bild:

“all C':
overlap(4)
/'

ip(_5) (e)

'ursprtlnglicher DAG':

‚/'in(6)’_\\
B -———*<z_;.fn.„

/' \
A1 A2

Abb. 11.2: Der DAG nach Durchlaufen des Pfades für "all 0"

Das Durchläufen des Pfades ab der abhängigen Variable des nächsten Quantors
"some A2" liefert den gesamten DAG als neuen DAG mit der Bezeichnung "some A2".
Für den Quantor "some A1" bleibt kein Operator mehr übrig. Im letzten Schritt der
Übersetzung wird aus den erzeugten DAGS eine Tabellendarstellung erzeugt. wie sie
bei direkter Übersetzung des folgenden DROOLY-Topologieausdruckes entstanden
wären (die resultierende Zusammenfassung gemeinsamer Teilausdrücke ist durch
Angabe der Zeilennummern als Operanden angedeutet):



some A2
indist [ ]
enclose [ ] A1 A2
B
A C

indist [ ] C B
overlap [ ] (7) (3)



00\10'1011ßme
