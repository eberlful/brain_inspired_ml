Erstelle mir drei Konzepte wie ich einen Co-Occurrence Graphen durch HTM (Hierarchical temporal memory) abbilden/repräsentieren kann. Beschäftige dich hauptsächlich mit der Frage wie ich den die Ähnlichkeiten/Nachbarschaftsbeziehungen nach dem Encodieren behalten kann. Beachte, dass der Co-Occurrence Graph während der Laufzeit wachsen kann, somit kann er sich verändern. Das sollte der SDR-Encoder (Sparse Distributed Representations) abbilden können.

### Konzept 1: Direkte Encodierung der Knoten durch Spatial Pooler mit feature-basiertem Overlap

In diesem Konzept wird der Co-Occurrence Graph durch den Spatial Pooler (SP) von HTM encodiert, der Eingabedaten in SDRs umwandelt. Der Fokus liegt darauf, die Nachbarschaftsbeziehungen (Ähnlichkeiten) durch gezielte Overlaps in den SDRs zu erhalten, indem co-occurrende Knoten gemeinsame Features teilen.

- **Encodierungsprozess:** Jeder Knoten im Graphen (z. B. eine Entität wie ein Wort oder ein Objekt) wird als Eingabevektor dargestellt, der seine Co-Occurrence-Häufigkeiten mit anderen Knoten enthält (z. B. als Vektor der Kantenstärken). Der SP verarbeitet diesen Vektor und erzeugt eine SDR, wobei er Mini-Columns aktiviert, die für häufig co-occurrende Features stehen. Ähnliche Knoten (hohe Co-Occurrence) teilen automatisch mehr aktive Bits, da der SP Proximal-Synapsen lernt, die auf gemeinsamen Eingaben basieren. Der Overlap-Score (Anzahl gemeinsamer aktiver Bits) misst direkt die Nachbarschaftsähnlichkeit – ein hoher Overlap entspricht einer starken Kante im Graphen.

- **Erhaltung von Ähnlichkeiten/Nachbarschaften:** Nach der Encodierung bleibt die Ähnlichkeit erhalten, weil der SP eine topologische Mapping erzeugt: Benachbarte Knoten im Graphen haben SDRs mit hohem Overlap (z. B. >20% der aktiven Bits), während unverbundene Knoten niedrigen Overlap zeigen. Dies wird durch die Permanenz-Mechanismen im SP verstärkt, die Synapsen-Stärken anpassen, um stabile Repräsentationen zu schaffen.

- **Handhabung dynamischen Wachstums:** Der Graph kann während der Laufzeit wachsen, indem neue Knoten oder Kanten als zusätzliche Eingaben dem SP zugeführt werden. Der SP lernt online und inkrementell: Neue Co-Occurrences aktualisieren die Proximal-Synapsen, ohne bestehende SDRs zu zerstören. Der SDR-Encoder passt sich an, indem er Boosting-Mechanismen nutzt, um seltene neue Knoten zu verstärken, und Inhibition, um Overlaps nur bei relevanten Ähnlichkeiten zu erlauben. So bleibt der Encoder skalierbar, und der Graph kann sich verändern, ohne die gesamte Struktur neu zu encodieren.

Dieses Konzept eignet sich für statische Graphen mit inkrementellen Updates, da es auf der biologisch inspirierten Lernfähigkeit des SP basiert.

### Konzept 2: Union-SDRs für Nachbarschaftsgruppen mit Temporal Pooling

Hier wird der Graph durch eine Kombination aus SDR-Union-Operationen und Temporal Memory (TM) repräsentiert, um Nachbarschaften als gruppierte SDRs zu kodieren. Der Ansatz betont die Erhaltung von Beziehungen durch kumulative Overlaps.

- **Encodierungsprozess:** Jeder Knoten erhält eine basis-SDR (z. B. durch einen einfachen Encoder wie ScalarEncoder). Für Nachbarschaften wird eine "Union-SDR" erstellt: Die SDRs aller benachbarten Knoten werden bitweise OR-verknüpft, um eine erweiterte Repräsentation zu bilden. Der TM verarbeitet dann Sequenzen von Co-Occurrences (z. B. zeitliche Abfolgen von Ereignissen), um distale Synapsen zu lernen, die Vorhersagen für zukünftige Nachbarn ermöglichen. Die resultierende SDR für einen Knoten integriert seine eigene Basis plus Overlaps aus Unions.

- **Erhaltung von Ähnlichkeiten/Nachbarschaften:** Die Ähnlichkeiten bleiben erhalten, da Overlaps in den Union-SDRs direkt die Stärke der Nachbarschaften widerspiegeln – je mehr Co-Occurrences, desto höher der Overlap in der Union. Nach der Encodierung kann man Ähnlichkeit berechnen, indem man den Jaccard-Index oder Bit-Overlap zwischen SDRs vergleicht, was die ursprünglichen Graph-Kanten rekonstruiert. Dies verhindert Verlust durch Kompression, da SDRs spärliche Natur Overlaps priorisiert.

- **Handhabung dynamischen Wachstums:** Dynamisches Wachstum wird durch den TM unterstützt, der online lernt und neue Sequenzen (z. B. neue Co-Occurrences) integriert. Wenn der Graph wächst, werden neue Knoten-SDRs hinzugefügt und in bestehende Unions eingebunden, ohne die gesamte Struktur zu invalidieren. Der SDR-Encoder handhabt Veränderungen, indem er Anomalie-Scores nutzt (aus TM), um neue Kanten zu detektieren und Overlaps anzupassen – so bleibt der Encoder adaptiv und skaliert mit wachsendem Graphen.

Dieses Konzept ist ideal für Graphen mit temporalen Aspekten, wie in Streaming-Daten, wo Co-Occurrences sequentiell auftreten.

### Konzept 3: Hierarchische SDR-Layers für Multi-Scale-Nachbarschaften

In diesem hierarchischen Ansatz wird der Graph über mehrere HTM-Layers abgebildet, wobei jeder Layer eine Abstraktionsstufe darstellt. Der Fokus liegt auf der Erhaltung von Beziehungen durch layer-weise Overlap-Propagation.

- **Encodierungsprozess:** Auf der untersten Ebene werden Knoten als SDRs encodiert (z. B. mit CategoryEncoder für diskrete Entitäten). Höhere Layers aggregieren Nachbarschaften: Ein Layer-SP poolt SDRs von Nachbargruppen, um abstrakte "Cluster-SDRs" zu erzeugen, die Co-Occurrence-Muster repräsentieren. Der TM in jedem Layer lernt Übergänge zwischen diesen Clustern, um den Graphen als hierarchische Struktur zu modellieren.

- **Erhaltung von Ähnlichkeiten/Nachbarschaften:** Ähnlichkeiten werden durch inter-layer Feedback erhalten: Overlaps propagieren von unteren zu oberen Layern, sodass Nachbarn in tieferen Layern hohe Overlaps teilen. Nach der Encodierung ermöglicht der Overlap-Score eine Rekonstruktion der Nachbarschaften – z. B. durch Berechnung des Union-Overlaps über Layers. Dies stellt sicher, dass lokale (direkte Nachbarn) und globale (indirekte Pfade) Ähnlichkeiten erhalten bleiben, ohne Informationsverlust.

- **Handhabung dynamischen Wachstums:** Der hierarchische Aufbau erlaubt dynamisches Wachstum, da neue Knoten in unteren Layern hinzugefügt werden und automatisch zu höheren Layern propagieren. Der SDR-Encoder passt sich an, indem er die HTM-Hierarchie online trainiert: Neue Co-Occurrences aktualisieren Synapsen in allen Layern, und der TM handhabt Veränderungen durch prädiktives Lernen. So kann der Graph wachsen, ohne bestehende SDRs zu destabilisieren, und der Encoder bleibt robust gegenüber Skalierungsproblemen.

Dieses Konzept passt zu komplexen, wachsenden Graphen, da es die natürliche Hierarchie von HTM nutzt, um Skalierbarkeit zu gewährleisten.

---

Meine Idee wäre es, dass jede Cortical Column mehrere Knoten in meinem Co-Occurrence Graphen repräsentiert. Jeder Knoten und jede Kante in dem Graphen müssten durch einen SDR-Encoder in ein SDR übersetzt werden können. Der Co-Occurrence Graph soll zur Laufzeit wachsen können, sprich die Nachbarschaftsbeziehungen können sich nachträglich verändern. Dies würde ein System schaffen welchen kontinuierlich lernen kann.
Erstelle mir drei Konzepte für das CMP. Für Objekte im 3-dimensionalen Raum beinhaltet der CMP ja Pose + Feature. Bei Sprache gibt es diese ja nicht. Was könnte ich stattdessen verwenden? Beachte dabei das der Co-Occurrence Graph dynamisch ist.

### Konzept 1: Sequentielles CMP (Temporal-Position-basiert)

Dieses Konzept erweitert das CMP auf abstrakte, sequentielle Strukturen wie Sprache, indem es die "Pose" durch eine temporale Position in einer Sequenz (z. B. einem Satz oder einer Kette von Konzepten) ersetzt. Es orientiert sich an der Hierarchical Temporal Memory (HTM)-Prinzipien, wo Sequenzen gelernt werden, und integriert den dynamischen Co-Occurrence Graphen als lernendes Netzwerk, das durch Co-Occurrences (z. B. Wortnachbarschaften) wächst. Jede Cortical Column (CC) repräsentiert mehrere Knoten als SDRs, die durch einen SDR-Encoder (z. B. basierend auf HTM-Encodern wie ScalarEncoder oder CategoryEncoder) in sparse Vektoren umgewandelt werden. Der Graph wächst dynamisch, indem neue Kanten basierend auf beobachteten Sequenzen hinzugefügt werden, was kontinuierliches Lernen ermöglicht.

#### Kernkomponenten des CMP:
- **Feature (SDR-encodiert)**: Repräsentiert den Inhalt eines Knotens, z. B. ein Wort, ein Konzept oder eine Phrase. Encodiert als SDR (z. B. ein 2048-Bit-Vektor mit 2% aktiven Bits). Optionale Attribute: Semantische Kategorie (z. B. "Noun", "Verb") oder Konfidenz (z. B. Häufigkeit der Co-Occurrence).
- **"Pose" (abstrakt: Temporale Position)**: Ersetzt die 3D-Pose durch eine Position in einer Sequenz oder Kette im Graphen. Dies umfasst:
  - **Lineare Position**: Eine Skalarwert (z. B. encodiert als SDR via ScalarEncoder), der die Reihenfolge angibt (z. B. Wort 3 in einem Satz).
  - **Kontextuelle Orientierung**: Eine SDR-Repräsentation der umliegenden Knoten (z. B. vorheriges/nächstes Wort), die als "Richtung" in der Sequenz dient (analog zur Orientierung in 3D).
- **Zusätzliche Elemente**:
  - **Graph-Update-Signal**: Ein Flag oder SDR, das anzeigt, ob eine neue Kante hinzugefügt werden soll (z. B. basierend auf Hebbian Learning: Wenn zwei Knoten häufig co-occurieren, wächst eine Kante).
  - **Sender-ID und Konfidenz**: Wie im Original-CMP, um Quellen zu tracken und Unsicherheit zu managen.

#### Wie es mit dem dynamischen Graphen interagiert:
- **Lernen und Wachstum**: Bei der Verarbeitung einer Sequenz (z. B. "Der Hund bellt") wird jede CC (die mehrere Knoten repräsentiert) aktiviert. Wenn eine neue Co-Occurrence erkannt wird (z. B. "Hund" und "bellt" in einem neuen Kontext), sendet das CMP ein Update-Signal, das den Graphen erweitert (z. B. neue Kante mit Gewicht basierend auf Häufigkeit). Der SDR-Encoder übersetzt neue Knoten dynamisch in SDRs, indem er bestehende Encoders (z. B. für Wörter) erweitert.
- **Kontinuierliches Lernen**: Der Graph passt sich an, indem Kanten-Gewichte (als SDRs encodiert) inkrementell aktualisiert werden. Z. B. via Temporal Memory in HTM: Vorhersagen basierend auf Sequenzen führen zu Graph-Anpassungen.
- **Vorteile**: Gut für lineare Strukturen wie Sätze; ermöglicht Vorhersagen (z. B. nächstes Wort). 
- **Nachteile**: Weniger geeignet für nicht-lineare Beziehungen (z. B. semantische Hierarchien wie "Hund ist ein Tier").

#### Beispiel-Nachricht (XML-ähnlich, wie in Tools):
```
<cortical_message>
  <feature>SDR: [0,1,0,...,1] (z.B. Wort "Hund")</feature>
  <temporal_position>SDR: [1,0,0,...,0] (Position 3 in Sequenz)</temporal_position>
  <context_orientation>SDR: [0,0,1,...,0] (Vorheriges Wort "Der")</context_orientation>
  <graph_update>add_edge: Hund -> bellt, weight=0.8</graph_update>
  <sender_id>CC_42</sender_id>
  <confidence>0.95</confidence>
</cortical_message>
```

### Konzept 2: Graph-Knoten-basiertes CMP (Relational-Position-basiert)

Hier wird die "Pose" durch die Position eines Knotens im dynamischen Co-Occurrence Graphen ersetzt, der als abstrakter Raum dient. Der Graph repräsentiert Beziehungen (z. B. semantische Nähe oder syntaktische Links), und Knoten/Kanten sind SDR-encodiert. Jede CC repräsentiert eine Gruppe von Knoten (z. B. ein Subgraph), und der Graph wächst durch Hinzufügen neuer Knoten/Kanten basierend auf neuen Beobachtungen (z. B. neue Wortpaare). Dies nutzt Sparse Distributed Representations (SDRs) für dynamische Encodierung, z. B. via HTM's Spatial Pooler für Knoten und Union-Operationen für Kanten.

#### Kernkomponenten des CMP:
- **Feature (SDR-encodiert)**: Der Wert des Knotens (z. B. Wort oder Konzept als SDR). Optionale Attribute: Knotentyp (z. B. "Entity", "Relation") oder Metadaten (z. B. Häufigkeit).
- **"Pose" (abstrakt: Graph-Position)**: Definiert als relative Position im Graphen:
  - **Knoten-Location**: Eine SDR-Repräsentation der Knoten-ID oder -Koordinaten (z. B. via CoordinateEncoder in HTM für multidimensionale Graph-Embedding).
  - **Orientierung**: SDR der ausgehenden/eingehenden Kanten (z. B. Nachbarn im Graphen), die "Richtung" zu verwandten Konzepten angibt.
- **Zusätzliche Elemente**:
  - **Graph-Update-Signal**: SDR-basierte Operationen wie "add_node" oder "update_edge_weight", die den Graphen dynamisch erweitern (z. B. neue Kante bei Co-Occurrence).
  - **Sender-ID und Konfidenz**: Standard.

#### Wie es mit dem dynamischen Graphen interagiert:
- **Lernen und Wachstum**: Bei Verarbeitung von Input (z. B. "Hund bellt laut") aktiviert eine CC Knoten und prüft Co-Occurrences. Neue Relationen (z. B. "bellt" -> "laut") triggern ein Update-Signal, das den Graphen erweitert. SDR-Encoder (z. B. Random Distributed Scalar Encoder) generiert neue SDRs für Knoten, und Kanten werden als Overlap zwischen SDRs berechnet (dynamisch anpassbar via Hebbian-Regeln).
- **Kontinuierliches Lernen**: Der Graph wächst laufzeitlich; Kanten-Gewichte ändern sich basierend auf Temporal Memory (z. B. Sequenzvorhersagen führen zu stärkeren Kanten). Jede CC kann lokale Subgraphen lernen und über CMP synchronisieren.
- **Vorteile**: Ideal für relationale Strukturen; unterstützt Hierarchien (z. B. "Hund" als Kind von "Tier").
- **Nachteile**: Komplexität bei sehr großen Graphen; benötigt effiziente SDR-Suche.

#### Beispiel-Nachricht:
```
<cortical_message>
  <feature>SDR: [1,0,1,...,0] (Knoten "Hund")</feature>
  <graph_location>SDR: [0,1,0,...,1] (Knoten-Koordinaten)</graph_location>
  <edge_orientation>SDR: [0,0,0,...,1] (Kante zu "bellt")</edge_orientation>
  <graph_update>update_edge: Hund -> bellt, weight+=0.2</graph_update>
  <sender_id>CC_17</sender_id>
  <confidence>0.85</confidence>
</cortical_message>
```

### Konzept 3: Latenter-Vektorraum CMP (Embedding-Position-basiert)

Dieses Konzept verwendet einen latenten Vektorraum (z. B. ein hochdimensionales Embedding-Space) als abstrakten "Raum" für die "Pose", inspiriert von Grid Cells in HTM für abstrakte Räume. Der Co-Occurrence Graph wird als dynamisches Embedding modelliert, wo Knoten Positionen in diesem Raum haben und Kanten Distanzen repräsentieren. SDRs encodieren Vektoren (z. B. via VectorEncoder), und CCs repräsentieren Cluster von Knoten. Der Graph wächst durch Interpolation neuer Positionen basierend auf Co-Occurrences.

#### Kernkomponenten des CMP:
- **Feature (SDR-encodiert)**: Knoten-Inhalt als SDR (z. B. Wort-Embedding).
- **"Pose" (abstrakt: Latente Position)**: Position im Vektorraum:
  - **Location**: SDR-encodierter Vektor (z. B. 3D-Koordinaten im latenten Raum, analog zu Grid Cells).
  - **Orientierung**: SDR der Gradienten/Richtungen zu Nachbarn (z. B. Vektor zu co-occurierenden Knoten).
- **Zusätzliche Elemente**:
  - **Graph-Update-Signal**: Vektor-Operationen wie "insert_vector" oder "adjust_distance", um den Raum dynamisch zu erweitern.
  - **Sender-ID und Konfidenz**: Standard.

#### Wie es mit dem dynamischen Graphen interagiert:
- **Lernen und Wachstum**: Bei Input (z. B. neue Phrase) platziert eine CC Knoten in den latenten Raum. Co-Occurrences erzeugen neue Vektoren (z. B. Mittelwert bestehender), und Kanten als Distanzen. SDR-Encoder passt sich an, indem er neue Dimensionen hinzufügt (z. B. via Dimensionality Reduction in HTM).
- **Kontinuierliches Lernen**: Embeddings aktualisieren sich via Gradient-ähnliche Anpassungen (Hebbian), ermöglicht durch Temporal Memory für Vorhersagen.
- **Vorteile**: Flexibel für abstrakte Räume; gut für Generalisierung (z. B. ähnliche Wörter nah beieinander).
- **Nachteile**: Weniger interpretierbar; benötigt Initialisierung des Raums.

#### Beispiel-Nachricht:
```
<cortical_message>
  <feature>SDR: [0,1,1,...,0] (Embedding "Hund")</feature>
  <latent_location>SDR: [1,0,0,...,1] (Vektor [0.5, 0.3, 0.7])</latent_location>
  <vector_orientation>SDR: [0,1,0,...,0] (Richtung zu "bellt")</vector_orientation>
  <graph_update>adjust_distance: Hund -> bellt, dist=0.1</graph_update>
  <sender_id>CC_99</sender_id>
  <confidence>0.90</confidence>
</cortical_message>
```

---
