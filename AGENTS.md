# AGENTS.md — AI Agent Guidance for CDIF Data Description (profile module)

## Project context

This repository publishes the **CDIF Data Description profile module** (`cdifDataDescription`). It adds data-description constraints on top of the discovery foundation: variable-level `cdif:physicalDataType`, primary keys (`cdif:hasPrimaryKey`), summary statistics (`cdif:statistics`), and distribution-level file characterization. It is composed by the `doc-discoverydatadescription` and `doc-discoverydatadescriptionstructure` application profiles — keep full human-facing guidance there, not here.

## Key files

- `CDIFDataDescriptionImplementationGuide.md` — module documentation
- `cdifDataDescriptionStructuredSchema.json` — JSON Schema (generated)
- `dataDescriptionRules.shacl` — merged SHACL shapes (generated)
- `cdifDataDescription-frame.jsonld` — JSON-LD frame used by `FrameAndValidate.py`
- `examples/` — validated JSON-LD examples
- `FrameAndValidate.py` — frame + JSON Schema validation

## Synced files (manual sync from metadataBuildingBlocks)

- `cdifDataDescriptionStructuredSchema.json` ← `python tools/resolve_schema.py cdifDataDescription -o <file>`
- `dataDescriptionRules.shacl` ← `python tools/validate_shacl.py cdifDataDescription --emit-shapes <file>`

Source profile dir: `metadataBuildingBlocks/_sources/profiles/cdifProfile/cdifDataDescription/`.

## Conventions that bite

- `schema:contentSize` is a **string**.
- A variable (`cdi:InstanceVariable` on `schema:variableMeasured`) carries `cdif:physicalDataType` as an **array**; on a physical mapping it is a plain **string**. `FrameAndValidate.py` handles this dual-context wrapping.
- Catalog record `dcterms:conformsTo` must include `core/1.0`, `discovery/1.0`, and `data_description/1.0`.
- Never strip unknown properties — validation is open-world.

## Validation

```bash
python FrameAndValidate.py examples/<file>.json --validate \
  --schema cdifDataDescriptionStructuredSchema.json --frame cdifDataDescription-frame.jsonld
```
