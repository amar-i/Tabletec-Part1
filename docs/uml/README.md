# Tabletec - draft UML

| Diagram | Editable source | Image | Vector |
| --- | --- | --- | --- |
| Domain classes | [domain.puml](domain.puml) | [domain.png](domain.png) | [domain.svg](domain.svg) |
| Application classes | [services.puml](services.puml) | [services.png](services.png) | [services.svg](services.svg) |
| Confirmation sequence | [booking-sequence.puml](booking-sequence.puml) | [booking-sequence.png](booking-sequence.png) | [booking-sequence.svg](booking-sequence.svg) |

The PlantUML sources are the single source of truth. PNG and SVG exports are generated directly from them; the report embeds all three PNGs. The sequence includes both success and failure alternatives.

To regenerate locally with Java and a PlantUML jar:

```sh
java -Djava.awt.headless=true -jar /path/to/plantuml.jar -tpng docs/uml/*.puml
java -Djava.awt.headless=true -jar /path/to/plantuml.jar -tsvg docs/uml/*.puml
```

Sources use Smetana layout, so no external Graphviz process or remote service is required. Conceptual value objects are plain records in the local JavaScript baseline. The status enumeration is implemented as strings, and observer listeners are functions. The detailed sequence occupies a larger PDF page to preserve legibility.

After changing UML, regenerate its images and update the corresponding report pages. The local report generator is retained with the private supporting prototype and is not part of this Part 1 publication.
