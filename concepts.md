# Konzepte

### Locality Sensitive Hashing" (LSH)
"Locality Sensitive Hashing" (LSH) ist eine Technik, die ähnliche Datenpunkte mit hoher Wahrscheinlichkeit im selben "Hash-Bucket" landen lässt. Im Gegensatz zu herkömmlichen Hash-Funktionen, die darauf abzielen, unterschiedliche Eingaben in unterschiedliche Buckets zu legen, maximiert LSH bewusst Kollisionen für ähnliche Elemente. Dies ist besonders nützlich, um ähnliche Elemente in großen, hochdimensionalen Datensätzen effizient zu finden.

**Wie funktioniert LSH?**
Die Grundidee von LSH besteht darin, hochdimensionale Datenpunkte in eine niedrigere Dimension abzubilden, während die relativen Abstände zwischen den Elementen so weit wie möglich erhalten bleiben. Dies geschieht durch die Verwendung einer Familie von Hash-Funktionen, die so konzipiert sind, dass:

* Ähnliche Elemente mit hoher Wahrscheinlichkeit im selben Bucket landen.
* Unähnliche Elemente mit hoher Wahrscheinlichkeit in unterschiedlichen Buckets landen.

Ein gängiger Ansatz für LSH, insbesondere bei Textdaten, umfasst oft die folgenden Schritte:
1. Shingling: Texte werden in kleinere "Shingles" (z.B. N-Gramme) zerlegt, um sie in Mengenform darzustellen.
2. MinHashing: Aus den Shingle-Mengen werden "Signaturen" erzeugt, die viel kleiner sind als die ursprünglichen Mengen, aber die Ähnlichkeit zwischen den Mengen gut approximieren.
3. Banded LSH: Die Signaturen werden dann in Bänder unterteilt, und für jedes Band wird eine Hash-Funktion angewendet. Wenn zwei Signaturen in mindestens einem Band übereinstimmen, werden sie als potenzielle Duplikate oder ähnliche Elemente betrachtet.

4. 
