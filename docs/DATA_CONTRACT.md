# Catalogue data contract

## Purpose and status

This document describes the planned boundary between the application and the published catalogue package. It applies to the application, the public `nmsinventory-data` repository, and the private `nmsinventory-scraper` repository.

The application consumes published releases from `nmsinventory-data`; it does not depend on scraper code. This contract is conceptual and not yet a finalized serialization schema. Do not infer source-site fields, exact property names, formats, or values that have not been verified. See [`DECISIONS.md`](DECISIONS.md) for open details.

The primary agreed source is `https://nms.source.gs/`. NMS Handbook is not a source. The scraper must remain restricted to the agreed source website.

## Package contents

A published catalogue release is planned to include:

- A version manifest with the catalogue version, release date, source and attribution information.
- Machine-readable catalogue data and/or a database package (the exact format is unconfirmed).
- An image package or image assets, only when redistribution permission is verified.
- Checksums for package files.
- Release notes describing the release.

GitHub Releases is the planned distribution mechanism for versioned packages, especially large database or image archives. Exact asset names, archive layout, compression, manifest schema, checksum algorithm, and versioning policy are not yet agreed.

## Normalized catalogue record concepts

Records should support the following concepts, as required by the master specification. These are semantic requirements, not prescribed serialized field names:

| Concept | Meaning and handling |
|---|---|
| Stable internal identifier | Identifies a normalized catalogue record across releases. Generation and stability rules are not yet defined. |
| Item name | The name presented to application users. |
| Category and subcategory | Classification for browsing and logical ordering. The final taxonomy and source mapping are not yet defined. |
| Description | Description when available and validated; missing source content must not be fabricated. |
| Image or icon reference | A reference to an included/approved image when available. Reference syntax and missing-image behavior are open decisions. |
| Stack limit | A verified value only. If unavailable or unverified, represent it as unknown/unverified; never guess. Difficulty-specific limits and their representation are not yet defined. |
| Crafting/refining relationships | Relationships and requirements when available and validated. Exact recipe model and completeness expectations are undecided. |
| Provenance | Source URL and source attribution for the record/material, when available. Preserve provenance through normalization and releases. |
| Last checked/updated | A timestamp or equivalent indication of source review/update, when available. Exact timestamp semantics and format are undecided. |
| Confidence/validation status | Indicates whether data has been checked and validated. The vocabulary and validation rules remain to be agreed. |

The source website's actual fields, structure, and coverage have not been verified in this documentation task. Map only observed and validated source material into the normalized contract; do not claim the source exposes a particular field merely because the application needs the concept.

## Categories and relationships

The catalogue is intended to cover relevant game items available from the source, including elements and resources, raw and refined materials, crafted products, technologies and upgrades, building parts, trade goods, biological/agricultural items, expedition/special items, and recipe/refining information.

This list describes desired application coverage; it does not assert that every category or relationship exists on the source site. Missing or unsupported information must be represented honestly. Categories support browsing and suggested logical ordering only; they do not permanently assign storage containers to item groups.

## Images and redistribution

Image references must resolve to assets included in the release package or another explicitly approved distribution mechanism. How references map to packaged files is undecided. Validate missing and broken references before release.

Do not include or distribute images, text, or other source material until permission has been verified for the relevant content and redistribution scope, including the source owner's rights to grant that permission. Preserve required attribution. An owner-reported permission to scrape and share is not, by itself, documentation that all redistribution rights have been verified.

## Application consumption and update safety

The application must:

1. Identify the installed and available catalogue versions using the agreed manifest.
2. Present release information and obtain user confirmation before update.
3. Verify package checksums and validate the package before installation.
4. Preserve a recoverable prior catalogue and install the new one atomically, rolling back on failure.
5. Continue using the last successfully installed catalogue when offline.
6. Keep catalogue data separate from accounts, Traveller profiles, personal inventory, uploaded personal images, and activity history.

The catalogue is descriptive reference data; it must not be treated as user inventory. Updating or removing a catalogue record must not silently delete a personal inventory entry. Behavior for records removed or changed in a later catalogue release must be decided before implementation.

## Decisions required before implementation

- Choose serialized data format and/or database format; define the exact manifest and record schema.
- Define stable identifier generation, category taxonomy, field optionality, and source-to-contract mapping after source discovery is authorized.
- Define recipe/refining relationship representation and how unknown, incomplete, or conflicting values are expressed.
- Define stack-limit semantics, evidence requirements, difficulty variants, and unknown/unverified representation.
- Define image archive layout, reference syntax, image formats, and redistribution approval evidence.
- Define versioning, release asset names, checksum algorithm, validation requirements, and package compatibility policy.
- Define catalogue update behavior when records change or disappear, without affecting personal inventory.

