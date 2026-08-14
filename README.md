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

## Changelog — reviewRevision202606 (updates since branched from `main`)

This release-review branch has diverged from `main` with the following updates,
synced from the CDIF **metadataBuildingBlocks** source (see
`git log main..reviewRevision202606` for the full per-commit history):

- **Populated from metadataBuildingBlocks** — `*StructuredSchema.json`, merged SHACL,
  JSON-LD frame, examples, and the normative `FrameAndValidate.py` generated from the
  building-block source; `Examples/` renamed to `examples/`.
- **CDIF v1.1** — profile conformance URIs migrated `/1.0` → `/1.1`.
- **License** standardized on CC-BY-4.0.
- **`@id`-reference tightening** — bare `{@id}` reference slots sealed
  (`additionalProperties: false` + `required: ['@id']`); a canonical `objectReference`
  building block introduced as the strict node reference.
- **`prov:used` wrapper reconciliation** — the base `generatedBy.prov:used` accepts
  role-keyed wrappers (`schema:instrument` / `bios:computationalTool` / `prov:reagent`)
  alongside string / `{@id}` / inline `prov:Entity`; profiles pin a wrapper's shape via
  a constraint-only `if/then` (never a narrowed `anyOf`).
- **`skos:notation` → single string** at concept level (consistent with the codelist
  single-notation design).
- **`FrameAndValidate.py`** (normative, drift-checked against
  `Cross-Domain-Interoperability-Framework/validation`) — two-frame root-`@type`
  selection, context-aware `schema:about`, `--conformance` detection, `cdif:`-`@id`
  re-expansion, and (2026-08) reference-collapse on all document types + blank-node
  dedupe + agent `schema:identifier` unwrap, so `@embed:@always`-framed documents
  validate against the tightened schemas.
- **Examples** conformed to the tightened schemas throughout (PrimaryKey →
  `cdi:ComponentPosition`, reference slots → `{@id}`, CVE `hasIntendedDataType` →
  string, `skos:notation` → string, `schema:additionalType` URI → `{@id}`).


## Development branch

Active work for the 2026-06 review revision is on the `reviewRevision202606` branch. `main` reflects the prior release state. New changes should target the review branch; it is merged to main on release.


## License

This work is licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE).
