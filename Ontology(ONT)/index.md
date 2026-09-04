# Groundwork Ontology (ONT)

The toolkit's semantic foundation: where the words every other document uses are ratified as stable identifiers. Class level only (segment types, measurement terms, canonical models); bindings live in the DIR, instances live on the data platform.

* [Groundwork Ontology](ontology.md) - The main document: the four layers, the class/binding/instance split, the parameter/measurement/metric rule, and the identity and relation rules.
* [Segment Taxonomy](segment-types.md) - Layer 1: the classes of things a plant decomposes into, with type IDs, containment parents, definitions, and synonyms. Seeded with a generic BESS taxonomy.
* [Taxonomy (naming)](taxonomy.md) - How things are named: organization codes, segment hierarchy and instance-code grammar, topic paths, prefix/ID conventions, and the identifier-scheme registry (segment types, measurement terms, PG_xxx, MT_). Moved here from Definitions_Taxonomy(DT) 2026-08-31.
* [Measurement Vocabulary](measurements.md) - Layer 2: the controlled quantity list, one canonical name / unit / datatype / definition per term, with the near-miss each term is not.
* [Canonical Data Models](canonical-models.md) - Layer 3: per segment type, the standard time-series record shape. Ships with the Battery String model populated as the worked example; other segment types are stubs to ratify.
* [Platform Projection](platform-projection.md) - The agent build spec: how a data-platform agent turns the ratified layers into dimension data, canonical tables, and column comments. Reference implementation, explicitly swappable.

## Status

* **Template, v0.1 (2026-08-31).** New resource. Layer 4 (metrics) is deliberately owned by the Metrics Tree; this folder registers only `metric_code` identifiers. Populate via the `ontology` skill: prune the taxonomy, ratify the vocabulary, then one canonical model per sitting.
