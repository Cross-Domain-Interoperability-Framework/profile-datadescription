# CDIF Data Description Profile — Implementation Guide

# Table of contents

  - [1. Purpose and scope](#1-purpose-and-scope)
  - [2. Conformance](#2-conformance)
- [Properties added to schema:Dataset](#properties-added-to-schemadataset)
  - [schema:Dataset](#schemadataset)
- [Properties added to schema:DataDownload](#properties-added-to-schemadatadownload)
- [Classes added by this profile](#classes-added-by-this-profile)
  - [cdif:InstanceVariable](#cdifinstancevariable)
  - [cdi:SentinelValueDomain, cdi:SubstantiveValueDomain](#cdisentinelvaluedomain-cdisubstantivevaluedomain)
  - [cdif:EnumerationDomain](#cdifenumerationdomain)
  - [cdif:Codelist](#cdifcodelist)
  - [cdif:ValueAndConceptDescription](#cdifvalueandconceptdescription)
  - [cdi:Statistics](#cdistatistics)
  - [cdi:CategoryStatistics](#cdicategorystatistics)
  - [cdi:StatisticsCollection](#cdistatisticscollection)
  - [cdifConceptOrTerm](#cdifconceptorterm)
  - [cdif:PhysicalMapping](#cdifphysicalmapping)
  - [cdif:TextMapping](#cdiftextmapping)
  - [cdif:LocatorMapping](#cdiflocatormapping)
- [Validation](#validation)
- [Provenance of the artifacts](#provenance-of-the-artifacts)

## 1. Purpose and scope

The **CDIF Data Description profile** (`cdifDataDescription`) documents the *internal structure of a dataset's values* so that the data can be understood and integrated, not merely discovered. It builds on the CDIF discovery foundation and adds constraints that describe the variables a dataset measures, how those variables map to the physical file, the dataset's keys, and summary statistics. 

This profile is combined with core, discovery and data description profiles in these document specifications:

- **doc-discoverydatadescription** — core + discovery + data description.
- **doc-discoverydatadescriptionstructure** — core + discovery + data description + data-structure (keys, components, foreign keys).

This guide documents the profiles's added constraints; consult those repositories for end-to-end examples.

## 2. Conformance

An instance document that includes this profile must declare conformance:

```json
"schema:subjectOf": {
  "@type": ["schema:CreativeWork"], "schema:additionalType":["dcat:CatalogRecord"],
  "dcterms:conformsTo": [
    "https://w3id.org/cdif/data_description/1.0"
  ]
}
```
and must include descriptions of variables using cdif:InstanceVariable.

# Properties added to schema:Dataset

## schema:Dataset

A Data Description record adds the following properties on the root schema:Dataset class:

### cdif:hasPrimaryKey

- the value is an object reference to a cdif:Key, or an inline cdif:Key. The cdif:Key is  an ordered set of cdi:InstanceVariables that uniquely identify a data instance. Each variable's position in the key is has a cdi:value integer that positions the variable in an ordered key. The cdif:Key object is a profile of the canonical DDI-CDI PrimaryKey structure matching. In the DataStructure profile, key (primary and foreign) are indexed by RepresentedVariable, not InstanceVariable.

### cdif:statistics

- summary statistics for the dataset: each entry is a **cdi:Statistics** bundle, **cdi:CategoryStatistics**, or a **cdi:StatisticsCollection**, given inline or as an `@id` reference to a node declared elsewhere in the document.

### schema:variableMeasured

The DataDescription profile add **cdi:InstanceVariable** as a type for the schema:variableMeasured property. If the DataDescription profile is composed with the Discovery profile, the schema @type will include both schema:PropertyValue and cdi:InstanceVariable. 

# Properties added to schema:DataDownload

### cdif:hasPhysicalMapping

Distribution-level characterization — properties describing the physical file(s) on `schema:distribution` (e.g. character set, content size). Per-field physical mappings linking the variable measured to the physical representation in the file - column index, format, physical data type, null sequence, etc. Each item is a cdifPhysicalMapping or one of its specializations (cdifTextMapping for tabular text, cdifLocatorMapping for structured data); the dataset-type branches below tighten the item type. **Note on file-size properties.** Earlier drafts defined `cdif:fileSize` / `cdif:fileSizeUofM`; these have been removed. Use `schema:contentSize` (a **string**) on the distribution instead.

# Classes added by this profile

## cdif:InstanceVariable

### cdif:physicalDataType

- Identifier or name for the data type concept. A single value wherever it appears — on a variable(`cdi:InstanceVariable`) and on a physical mapping alike. The value may be a plain string (e.g. `"xsd:decimal"`)or a cdifConceptOrTerm. 

### cdif:role

- Specifies the role this variable plays in a data structure. UnitIdentifier names the unit a row describes; Measure holds observed/derived values; Attribute qualifies an observation; Dimension addresses a cell in a multi-dimensional cube; Descriptor names the variable that a Reference column records values for (long format); ReferenceVariable holds those recorded values. Enumeration: {UnitIdentifier, Measure, Attribute, Dimension, Descriptor, ReferenceVariable} 

### cdif:simpleUnitOfMeasure':

- The unit in which the data values are measured (kg, pound, euro), expressed as a simple string, in cases where no additional information is available (in the legacy system)or needed (as in the case of broad agreement within the community of use [i.e., ISOcountry codes, currencies, etc. in SDMX]). Use cdi:describedUnitOfMeasure if there is a unit of measure definition from a published vocabulary that can be cited.  type: string

### cdif:uses':

- Essentially the same as schema:propertyID. Specify the concept that this variable measures or represents. When the dataset's distribution carries cdi:isStructuredBy (CDIF Data Structure profile), cdif:uses references the RepresentedVariable that supplies the represented-variable-level properties below, which are then NOT duplicated on the InstanceVariable. type: string or cdifConceptOrTerm'

### cdi:function':

- Immutable characteristic of the variable such as geographic designator, weight, temporal designation, etc. type: array of string or cdifConceptOrTerm

### cdi:platformType':

- The application or technical system context in which the variable has been realized, typically a statistical processing package or processing environment.type: string or cdifConceptOrTerm

### cdi:source':

- Reference capturing provenance information for this InstanceVariable, either a string citation or object reference to the source resource.

### cdif:isDescribedBy_StatisticsCollection':

- A StatisticsCollection holding summary / category statistics for this InstanceVariable. type: **cdif:StatisticsCollection** or object reference

### cdi:describedUnitOfMeasure':

- The unit in which the data values are measured, expressed as a controlled-vocabulary entry. For a plain-string unit, use cdif:simpleUnitOfMeasure instead. type: cdifConceptOrTerm

### cdi:qualifies':

- reference to an instance variable defined for this dataset. This property only applies if the role is 'Attribute'. type: object reference 

### cdi:takesSentinelValuesFrom':

- an enumerated domain that specifies possible sentinel values used by a variable. Sentinel values are special values used to indicate various invalid conditions for a variable, e.g. below detection, missing value.  type: array of SentinelValueDomain or object reference to a SentinelValue Domain

### cdi:takesSubstantiveValuesFrom':

- Specification of the set of valid, meaningful values for a variable. Might be an enumeration/controlled vocabulary, a numeric limit, or constraints on strings - e.g. length, regular expressions. Array of SubstantiveValueDomain or object reference to SubstantiveValueDomain

## cdi:SentinelValueDomain, cdi:SubstantiveValueDomain

- value domain specifications are the same except for the @type values cdif:SentinelValueDomain and cdif:SubstantiveValueDomain respectively

### cdif:takesValuesFrom

- value is a **cdif:EnumerationDomain** or an object reference to an EnumerationDomain. The enumeration domain is  wrapper for a cdif codelist object.  

### cdif:displayLabel':

- A human-readable display label for the value domain. type: string        

### cdif:recommendedDataType':

- The data type(s) that are recommended for use with this domain. type: array of xsdDataTypes.

### cdi:isDescribedBy':

- A **cdif:ValueAndConceptDescription** object or reference to a ValueAndConceptDescription. This is a formal description (ranges, patterns, classification level, expressions) of the values this domain admits.

## cdif:EnumerationDomain

- A wrapper acting as an extension point to allow a cdif Codelist  to be documented as enumerated value domain.

### cdif:identifier':

- Identifier for this enumerated (categorical). Type: schema.org Identifier

### schema:name

- Label to identify this domain in user interfaces, if different from the Codelist name. type: string

### cdif:references

- a CDIF codelist (skos:ConceptScheme) or link to a codelist whose notation values define the allowed values of this enumeration domain.

### cdif:purpose

- Intent or reason for the Enumerated Domain.    type: string 

## cdif:Codelist

See [cdifCodelistProfile]().    A controlled-vocabulary codelist implemented as a skos:ConceptScheme constrained for CDIF use. The scheme must have a resolvable @id and identify its top concepts via skos:hasTopConcept; each concept (CdifCodelistConcept) must have a resolvable @id, skos:inScheme, skos:prefLabel, and skos:notation. Hierarchical concepts must declare both skos:narrower (for JSON tree traversal) and skos:broader (for upward navigation) where hierarchy exists.

## cdif:ValueAndConceptDescription

- A Formal description of a set of values.

### cdi:classificationLevel':

- Indicates the type of relationship, nominal, ordinal, interval, ratio, or continuous. Use where appropriate for the representation type. type: Enumeration {Continuous, Interval, Nominal, Ordinal, Ratio}

### cdi:description

- A description of the value restrictions for this domainin human-readable language. type: string

### cdi:formatPattern':

- A pattern for a number as described in Unicode Locale Data Markup Language (LDML) (http://www.unicode.org/reports/tr35/tr35.html) Part 3: Numbers (http://www.unicode.org/reports/tr35/tr35-numbers.html#Number_Format_Patterns) and Part 4. Dates (http://www.unicode.org/reports/tr35/tr35-dates.html#Date_Format_Patterns). Examples would be #,##0.### to describe the pattern for a decimal number, or yyyy.MM.ddTHH:mm:ss zzz for a datetime pattern..  type: string

### cdi:logicalExpression':

- A logical expression where the values of "x" making the expression true are the members of the set of valid values. For example, "(all reals x such that x > 0)" describes the real numbers greater than 0. type: string

### cdi:maximumValueExclusive

- A string denoting the maximum possible value (excluding this value). From the W3C  Recommendation "Metadata Vocabulary for Tabular Data" section 5.11.2: "maxExclusive: An atomic property that contains a single number or string that is the maximum valid value (exclusive). The value of this property becomes the maximum exclusive annotation for the described datatype. See Value Constraints in [tabular-data-model](https://www.w3.org/TR/tabular-metadata/) for details." type: string

### cdi:maximumValueInclusive

- A string denoting the maximum possible value (including this value). From the W3C  Recommendation "Metadata Vocabulary for Tabular Data" section 5.11.2: "maxExclusive: An atomic property that contains a single number or string that is the maximum valid value (exclusive). The value of this property becomes the maximum exclusive annotation for the described datatype. See Value Constraints in [tabular-data-model](https://www.w3.org/TR/tabular-metadata/) for details." type: string

### cdi:minimumValueExclusive':

- A string denoting the minimum possible value (excluding this value) (see above for origin). type: string

### cdi:minimumValueInclusive':

- A string denoting the minimum possible value (including this value) (see above for origin). type: string

### cdi:regularExpression':

- A [regular expression](https://en.wikipedia.org/wiki/Regular_expression) that defines the valid syntax for valid value strings. type: string

## cdi:Statistics

A named bundle of one or more Statistic value objects for an instance      variable, optionally weighted, optionally broken down by Category.

### cdi:typeOfStatistic

Controlled-vocabulary entry naming the kind of statistic — e.g. mean, median, count, sum, stdDev. Value is a skos:Concept

### cdi:Statistic

Ordered list of Statistic value objects carried by this bundle. Order is significant — consumers MAY rely on array position. The Statistic value object represents a single computed value (mean, count, median, etc.), with optional computationBase and typeOfNumericValue.

### cdi:hasWeight

The InstanceVariable whose values were used as weights when computing the Statistic entries, linked with an object reference (@id-reference).

### cdif:appliesTo

CDIF addition (not in canonical DDI-CDI): the InstanceVariable(s) this Statistics bundle summarizes — the per-bundle "what these numbers describe" link. When a Statistics node sits inside a StatisticsCollection that indexes more than one variable, cdif:appliesTo disambiguates which variable each bundle describes. Value is an object reference to an InstanceVariable. 

### cdif:has_CategoryStatistics

value is an array of **CategoryStatistics** objects that maps a Statistic value object to a category concept in the Statistics bundle. When CategoryStatistics is used in the context of a Statistics bundle, use object references to the Statistic definitions in the bundle.

## cdi:CategoryStatistics

Statistics for a specific Category of an instance variable within a data set. Maps a **Statistic** value object to a category concept. The Statistic value object represents a single computed value (mean, count, median, etc.), with optional computationBase and typeOfNumericValue.

### cdi:for

The Category this CategoryStatistics is for (inline CDIF Concept or an object reference).

### cdi:typeOfStatistic

Controlled-vocabulary entry naming the kind of statistic. Inline CDIF concept or object reference

### cdi:statistic

Per-category Statistic value objects. In line Statistic instance or object reference.

### cdi:hasWeight

The InstanceVariable whose values were used as weights (an @id-reference).

## cdi:StatisticsCollection

Groups one or more Statistics nodes for an instance variable. A typical use is a dataset-level collection holding row-count / mean / stddev Statistics for each measured variable.

### cdif:has_Statistics

Array of Statistic value objects carried by this collection (inline or @id-ref). 

### cdi:hasWeight

The InstanceVariable whose values were used as weights (an @id-reference).

### cdif:indexedBy

CDIF addition (not in canonical DDI-CDI): the InstanceVariable(s) the contained Statistics index— the collection-level coordinate space. Array of object references.

## cdifConceptOrTerm

a data type that can be an object reference, a schema:DefinedTerm, or a skos:Concept as defined in the cdif ConceptScheme profile.

## cdif:PhysicalMapping

- Defines implementation-specific properties for the representation of a variable in a dataset. Uses 2026 DDI-CDI update flat per-column mapping structure.

### cdif:index:

   Non-negative integer that orders the fields in the data structure (column number). type: integer
    minimum: 0
    description: 

### cdif:format:

    type: string
    description: A format for number expressed as a string, or date format like YYYY/MM or MM-DD-YY.

### cdif:physicalDataType:

- identifier or name for the data type concept; string, object reference or concept

### cdi:numberPattern:

- Number format pattern for the field. Note that text-format properties (column width, decimal/digit-group separators, display label) are specified in a TextMapping subclass instance. type: string

### cdi:nullSequence:

- character string that flags a null value for this variable. type: string

### cdi:defaultValue:

- A default string indicating the value to substitute for an empty string. type: string

### cdi:scale:

- type: integer

### cdi:decimalPositions:

- number of decimal positions in a decimal number value. type: integer

### cdi:minimumLength:

- minimum number of characters in a string value. type: integer

### cdi:maximumLength:

- maximum number of characters in a string value. type: integer

### cdi:isRequired:

- flag to indicate if value is required. type: boolean

### cdif:formats_InstanceVariable:

- Object reference to a variable defined in schema:variableMeasured. 

## cdif:TextMapping

- Physical mapping for a variable in a delimited or fixed-width text dataset. Extends cdifPhysicalMapping with text-format properties (column width and number formatting).

### cdi:length:

- The column width if the tabular text is fixed width (TextMapping.length). type: integer

### cdi:defaultDecimalSeparator

- Decimal separator used when not otherwise specified (TextMapping.defaultDecimalSeparator). type: string

### cdi:defaultDigitGroupSeparator

- Digit-group (thousands) separator (TextMapping.defaultDigitGroupSeparator). type: string

### cdif:displayLabel

- Human-readable text string for display. CDIF simplification (plain string) of DDI-CDI TextMapping.displayLabel, whose model type is LabelForDisplay. Per the cdi/cdif namespace policy a structured-string type simplified to a plain string is in the cdif namespace.

## cdif:LocatorMapping

- extends cdif:PhysicalMapping. Locates a variable's value(s) within a structured (hierarchical) dataset such as XML or JSON, via a locator expression (e.g., XPath or JSONPath). DDI-CDI LocatorMapping. Used in place of column-index/text mappings for cdi:StructuredDataSet distributions.

### cdi:locator:

- Expression locating the variable's value(s) in the structured document (XPath, JSONPath, pointer, etc.). type: string

# Validation

- **JSON Schema** — `cdifDataDescriptionStructuredSchema.json` (Draft 2020-12).
- **SHACL** — `dataDescriptionRules.shacl`, a self-contained shapes graph merged from every composing building block plus the profile-level shapes.

```bash
python FrameAndValidate.py examples/exampleCdifDataDescription.json --validate \
  --schema cdifDataDescriptionStructuredSchema.json --frame cdifDataDescription-frame.jsonld
```

Validation is **open-world**: properties beyond the profile are permitted.

# Provenance of the artifacts

Generated from the canonical [metadataBuildingBlocks](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks) register:

- `cdifDataDescriptionStructuredSchema.json` ← `tools/resolve_schema.py cdifDataDescription`
- `dataDescriptionRules.shacl` ← `tools/validate_shacl.py cdifDataDescription --emit-shapes`

Source profile directory: [_sources/profiles/cdifProfile/cdifDataDescription/](https://github.com/Cross-Domain-Interoperability-Framework/metadataBuildingBlocks/tree/main/_sources/profiles/cdifProfile/cdifDataDescription). Re-sync whenever the source register changes.
