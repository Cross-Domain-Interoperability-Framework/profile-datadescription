# CDIF Data Description (profile module)

This repository holds the published artifacts for the **CDIF Data Description profile module** — the `cdifDataDescription` building block from the [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) source register.

> **Scope.** `cdifDataDescription` is a profile *module*: it defines only  data-description classes and properties (variable-level `cdif:physicalDataType`, primary keys, summary statistics, and distribution-level file characterization) to extend the discovery foundation. It is composed by the document specification published in [doc-discoverydatadescription](https://github.com/Cross-Domain-Interoperability-Framework/doc-discoverydatadescription) and [doc-discoverydatadescriptionstructure](https://github.com/Cross-Domain-Interoperability-Framework/doc-discoverydatadescriptionstructure). Those specifications carry full implementation guidance and worked examples for instance documents incorporating this profile.

## Specification

- **[CDIFDataDescriptionImplementationGuide.md](CDIFDataDescriptionImplementationGuide.md)** — Documentation for the Data Description profile module.
- **[cdifDataDescriptionStructuredSchema.json](cdifDataDescriptionStructuredSchema.json)** — JSON Schema (Draft 2020-12), generated from the source register with `tools/resolve_schema.py`.
- **[dataDescriptionRules.shacl](dataDescriptionRules.shacl)** — Self-contained SHACL shapes, merged from every composing building block plus the profile-level shapes.

## Conformance

A conforming instance declares, in its `dcterms:conformsTo`:

- `https://w3id.org/cdif/data_description/1.1`

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

## Development branch

Active work for the 2026-06 review revision is on the `reviewRevision202606` branch. `main` reflects the prior release state. New changes should target the review branch; it is merged to main on release.


## License

This work is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).
