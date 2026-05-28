# CDIF Data Description (profile module)

This repository holds the published artifacts for the **CDIF Data Description profile module** — the `cdifDataDescription` building block from the [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) source register.

> **Scope.** `cdifDataDescription` is a profile *module*: it adds the data-description constraints (variable-level `cdif:physicalDataType`, primary keys, summary statistics, and distribution-level file characterization) on top of the discovery foundation. It is composed by the application profiles published in [doc-discoverydatadescription](https://github.com/Cross-Domain-Interoperability-Framework/doc-discoverydatadescription) and [doc-discoverydatadescriptionstructure](https://github.com/Cross-Domain-Interoperability-Framework/doc-discoverydatadescriptionstructure). Those composite repositories carry the full human-facing implementation guidance and worked examples.

## Specification

- **[CDIFDataDescriptionImplementationGuide.md](CDIFDataDescriptionImplementationGuide.md)** — Documentation for the Data Description profile module.
- **[cdifDataDescriptionStructuredSchema.json](cdifDataDescriptionStructuredSchema.json)** — JSON Schema (Draft 2020-12), generated from the source register with `tools/resolve_schema.py`.
- **[dataDescriptionRules.shacl](dataDescriptionRules.shacl)** — Self-contained SHACL shapes, merged from every composing building block plus the profile-level shapes.

## Conformance

A conforming instance declares, in its `dcterms:conformsTo`:

- `https://w3id.org/cdif/core/1.0`
- `https://w3id.org/cdif/discovery/1.0`
- `https://w3id.org/cdif/data_description/1.0`

## Examples

```bash
python FrameAndValidate.py examples/exampleCdifDataDescription.json --validate \
  --schema cdifDataDescriptionStructuredSchema.json --frame cdifDataDescription-frame.jsonld
```

`FrameAndValidate.py` frames the document, array-wraps the multi-valued data-description properties (e.g. `schema:variableMeasured`, `cdif:hasPhysicalMapping`, `cdif:statistics`), then validates against the JSON Schema. Validation is open-world: unknown properties pass.

## Synced from metadataBuildingBlocks

Generated artifacts; re-sync manually when the source register changes:

| file | source |
|---|---|
| `cdifDataDescriptionStructuredSchema.json` | `python tools/resolve_schema.py cdifDataDescription -o cdifDataDescriptionStructuredSchema.json` |
| `dataDescriptionRules.shacl` | `python tools/validate_shacl.py cdifDataDescription --emit-shapes dataDescriptionRules.shacl` |

Source profile: `_sources/profiles/cdifProfile/cdifDataDescription/`.

## License

This work is dedicated to the public domain under [CC0 1.0 Universal](LICENSE).
