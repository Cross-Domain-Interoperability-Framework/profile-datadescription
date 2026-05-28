# CDIF Data Description Profile — Implementation Guide

## 1. Purpose and scope

The **CDIF Data Description profile module** (`cdifDataDescription`) documents the *internal structure of a dataset's values* so that the data can be understood and integrated, not merely discovered. It builds on the CDIF discovery foundation and adds constraints that describe the variables a dataset measures, how those variables map to the physical file, the dataset's keys, and summary statistics.

This module is the register entry for data description. The full application profiles that combine discovery and data description — with worked, domain-specific examples — are published as composites:

- **doc-discoverydatadescription** — discovery + data description.
- **doc-discoverydatadescriptionstructure** — discovery + data description + data-structure (keys, components, foreign keys).

This guide documents the module's added constraints; consult those repositories for end-to-end examples.

## 2. Conformance

A data-description-conformant catalog record declares all three profile identifiers on `dcterms:conformsTo`:

```json
"schema:subjectOf": {
  "@type": ["schema:CreativeWork", "dcat:CatalogRecord"],
  "dcterms:conformsTo": [
    "https://w3id.org/cdif/core/1.0",
    "https://w3id.org/cdif/discovery/1.0",
    "https://w3id.org/cdif/data_description/1.0"
  ]
}
```

Data description builds on discovery, which builds on core, so all three identifiers are required.

## 3. What this module adds

On top of the discovery/core properties, a Data Description record describes its data content:

- **`schema:variableMeasured`** — an array of `cdi:InstanceVariable` nodes, one per measured variable. Each variable carries:
  - `cdif:physicalDataType` (**array** on the variable) — the physical representation type(s) of the variable's values;
  - the variable's name, definition, and (optionally) its value domain, units, and the concept it represents.
- **`cdif:hasPrimaryKey`** — a `cdif:Key` whose `cdif:isComposedOf` is an ordered list of the `cdi:InstanceVariable`s (drawn from `schema:variableMeasured`) that uniquely identify each data instance.
- **`cdif:statistics`** — summary statistics for the dataset: each entry is a `cdi:Statistics` bundle or a `cdi:StatisticsCollection`, given inline or as an `@id` reference to a node declared elsewhere in the document.
- **Distribution-level characterization** — properties describing the physical file(s) on `schema:distribution` (e.g. character set, content size).

> **Note on file-size properties.** Earlier drafts defined `cdif:fileSize` / `cdif:fileSizeUofM`; these have been removed. Use `schema:contentSize` (a **string**) on the distribution instead.

## 4. Physical data type — dual context

`cdif:physicalDataType` appears in two places with two shapes:

- On a **variable** (`cdi:InstanceVariable`, an item of `schema:variableMeasured`) it is an **array**.
- On a **physical mapping** it is a plain **string**.

`FrameAndValidate.py` performs context-aware array wrapping so that a framed document satisfies both forms.

## 5. Validation

- **JSON Schema** — `cdifDataDescriptionStructuredSchema.json` (Draft 2020-12).
- **SHACL** — `dataDescriptionRules.shacl`, a self-contained shapes graph merged from every composing building block plus the profile-level shapes.

```bash
python FrameAndValidate.py examples/exampleCdifDataDescription.json --validate \
  --schema cdifDataDescriptionStructuredSchema.json --frame cdifDataDescription-frame.jsonld
```

Validation is **open-world**: properties beyond the profile are permitted.

## 6. Provenance of the artifacts

Generated from the canonical [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) register:

- `cdifDataDescriptionStructuredSchema.json` ← `tools/resolve_schema.py cdifDataDescription`
- `dataDescriptionRules.shacl` ← `tools/validate_shacl.py cdifDataDescription --emit-shapes`

Source profile directory: `_sources/profiles/cdifProfile/cdifDataDescription/`. Re-sync whenever the source register changes.
