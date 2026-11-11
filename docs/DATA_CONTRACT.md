# Shared catalogue data contract

## Status, scope, and authority

This is the canonical semantic contract for the application, public catalogue data repository, and private scraper. All three repositories use the same versioned JSON interchange format. The scraper may keep any internal database schema, but its exported JSON must conform to this contract. The data repository packages and releases that JSON. The application validates and imports published releases.

The approved requirements in this document are binding across the three repositories. This document does not claim that a field exists on the source website. The primary source remains `https://nms.source.gs/`; do not use NMS Handbook. Do not crawl, scrape, or download images as part of this documentation work. Source discovery and source-to-contract mapping must happen only when separately authorized.

The logical structure and semantics below are canonical. Exact JSON Schema details that depend on source inspection, category taxonomy, optional/null conventions, image format/conversion rules, and compatibility policy remain pending in [`DECISIONS.md`](DECISIONS.md). Any JSON examples below are **illustrative only**, not catalogue data or evidence about source fields.

## Common conventions

- The interchange document is JSON encoded as UTF-8. Its schema version is separate from the catalogue content version in the release manifest.
- Item IDs and recipe IDs are UUID-based, immutable, and maintained in a persistent ID registry. IDs do not change when names, categories, descriptions, or images change.
- A recipe is its own record and references item IDs; recipe definitions are not duplicated in item records.
- `validationStatus` uses only the vocabulary defined below. It is not a numeric confidence score.
- A numeric stack-limit value may be included only when verified and supported by provenance/evidence. Unknown, unverified, incomplete, or conflicting stack-limit values are not published as usable numeric limits.
- Image references are package-relative paths. Original source URLs and provenance are separate metadata. Reject absolute paths, URL schemes, traversal segments, and any path that escapes the package root.
- Timestamps, when present in interchange data, must represent UTC. RFC 3339 date-time strings are the recommended encoding, pending confirmation in the final JSON Schema. A timestamp must describe an observed or generated event; do not infer source timestamps.
- The final rules for omitted properties versus `null`, localized names, and complete field-level JSON Schema constraints remain to be confirmed. Until then, omit an unavailable optional property rather than fabricate a value; producers and consumers must not assign meaning to `null` that has not been specified.

## Catalogue JSON envelope

The canonical interchange document contains a schema version and separate collections of items, recipes, and image records. It may also include identifier alias/migration records as defined below. The exact final JSON Schema and whether any auxiliary collection is mandatory remain pending; this semantic envelope must remain consistent across repositories.

### Item record

Each item record supports:

| Property concept | Requirement |
|---|---|
| `id` | Required immutable UUID-based item identifier. Never derive it solely from a mutable name, category, description, image, or URL. |
| `name` | Required user-facing item name when verified. |
| `category` | Category in the agreed catalogue taxonomy; actual taxonomy is pending. |
| `subcategory` | Optional subcategory in that taxonomy. |
| `description` | Optional validated description. Do not fabricate missing text. |
| `imageRef` | Optional package-relative path to an image record/package member; never use an external URL as the installed image reference. |
| `stackLimit` | Optional stack-limit status and, only if verified, a numeric value plus evidence/provenance and difficulty applicability where supported. |
| `validationStatus` | Required state describing the item record under the shared validation vocabulary. |
| `provenance` | Source URL and source/record provenance sufficient to trace included information. |
| `timestamps` | Relevant observed timestamps, such as when source data was checked; include only when known and meaningful. |
| retirement state | A retired/legacy record remains in the catalogue with its original ID. It is not deleted when absent from a later source snapshot. |

The table names concepts, not final JSON property spellings beyond the illustrative names shown. The exact JSON Schema, required/optional status of source-dependent fields, and taxonomy representation remain pending. A missing optional value is not a zero, empty string, verified fact, or reason to delete an existing personal inventory record.

### Recipe record

Recipes are independent records with their own immutable UUID-based `id`. A recipe references items by their catalogue item IDs; it does not embed or copy item definitions.

Each recipe record supports:

- `id`: immutable recipe UUID.
- `recipeType`: normalized recipe kind (for example, crafting, refining, or cooking only when verified and included in the eventual taxonomy).
- `inputs`: zero or more item references and quantities.
- `outputs`: one or more item references and quantities.
- Other recipe metadata only when observed, validated, and included in the agreed schema.
- `provenance`, relevant timestamps, and `validationStatus`.

Each input/output quantity must be a positive numeric amount in the agreed unit. The exact quantity representation, recipe kinds, optional fields, and metadata schema remain pending. Do not invent recipes or imply source coverage before inspection.

### Image record

Each image record supports:

| Property concept | Requirement |
|---|---|
| Package-relative path | Required safe relative path using `/` separators. It identifies the packaged image, not its source location. |
| Original source URL | Preserve separately from the package path when known. It is provenance, not a runtime image reference. |
| Media format | Record the validated actual media format; exact permitted formats and conversion policy remain pending. |
| Provenance | Record source and relevant acquisition/verification information when known. |
| Rights/redistribution status | Record whether redistribution has been reviewed and cleared. No asset may be publicly released without verified permission. Exact status vocabulary and evidence-reference policy remain pending. Do not publish private permission evidence. |
| `validationStatus` | Required state under the shared validation vocabulary, including whether the asset was checked and its reference is valid. |

`imageRef` on an item resolves to the corresponding image record/path in the installed package. The application must work offline with packaged images. Do not fetch an arbitrary URL found in catalogue JSON.

### Illustrative JSON only — not source data

The following is a shape example using placeholders. It is not a finalized JSON Schema, does not assert source fields, and must not be used as catalogue content:

```json
{
  "schemaVersion": "<approved-schema-version>",
  "items": [
    {
      "id": "<immutable-item-uuid>",
      "name": "<validated display name>",
      "category": "<approved-category>",
      "validationStatus": "incomplete",
      "stackLimit": {
        "status": "unknown"
      },
      "provenance": []
    }
  ],
  "recipes": [],
  "images": [],
  "idMappings": []
}
```

In particular, an example `unknown` stack-limit status has no numeric value. Do not put an unverified or conflicting numeric value into a released stack limit.

## Validation vocabulary

The following five states have the same meaning in all three repositories. Apply a state to the relevant record or field; where a record has field-level states, do not imply that one good field validates every other field.

| State | Meaning |
|---|---|
| `verified` | The value or record was checked against sufficient traceable evidence and satisfies the agreed validation rules. It is not a confidence score or guarantee beyond that evidence. |
| `unknown` | No value is known or established. There is no candidate value to present as fact. |
| `unverified` | A candidate value or claim has been observed, but it has not been checked sufficiently to treat it as fact. |
| `incomplete` | Some information is established, but required information for the intended record/use is missing. Incompleteness does not itself mean the available values are false. |
| `conflicting` | Credible evidence or sources disagree and the conflict has not been resolved. Preserve the competing evidence for review; do not select a winner silently. |

Use the state that describes the data, not a numeric score. If a datum is unavailable, do not invent evidence to move it to `verified`. Exact validation thresholds and field-level JSON representation remain to be finalized after source inspection.

## Stack-limit semantics

- Publish a numeric stack-limit value only with `validationStatus: "verified"` for that value and traceable provenance/evidence.
- Record difficulty applicability only when evidence supports it. The game-difficulty vocabulary, whether one value applies to multiple difficulties, and exact encoding are pending.
- If no value is known, represent the stack limit as `unknown` without a number.
- If a value has been observed but not checked, represent it as `unverified` without publishing it as a usable numeric stack limit.
- If a required source/context is missing, use `incomplete`; if credible values disagree, use `conflicting` and do not publish one as authoritative.
- Never infer, extrapolate, or guess a value from a related item or difficulty.
- Keep evidence/provenance tied to the value and applicability. An item-level `validationStatus` alone is not evidence for a stack limit.

## Provenance and timestamps

Preserve the original source URL separately from normalized IDs, package paths, and application-local data. Provenance should make it possible to trace a normalized record or value to its source and review event. Include only source facts that have actually been observed. Do not store unnecessary copied source content or confidential permission correspondence in the public package.

Potential timestamp concepts include first observation, last source check, and release generation time. The exact item/recipe timestamp fields remain pending; the manifest's release timestamp is separately defined below. Missing source timestamps must not be fabricated.

## Stable identifier registry and lifecycle

- Assign each new item an immutable UUID when the record is first accepted into the maintained catalogue. Assign recipes their own immutable UUIDs.
- Maintain a durable registry so full crawls, changed names/categories/images, and incremental updates do not allocate new IDs for existing entities.
- Do not derive identity from mutable descriptive/source attributes. Source URLs and observed source identifiers may be recorded as provenance or matching clues, but are not the application-facing immutable ID.
- Detect possible duplicates using available evidence and send uncertain matches for review. Do not merge records automatically based only on equal names or images.
- When records are confirmed to represent one entity, preserve a canonical existing ID and retain an explicit mapping/alias from any retired duplicate ID to the canonical ID. The canonical-selection and approval workflow remains to be finalized.
- Renamed or recategorized items retain the same ID.
- Retired items remain represented with the same ID and an explicit retired/legacy state. IDs are never reassigned to a different entity.
- Keep alias/migration mappings explicit, traceable, and non-destructive. The exact `idMappings` schema, retention rules, and application behavior when resolving a mapping remain pending.

## Catalogue manifest and release package

The public data repository distributes versioned packages through GitHub Releases. Each release includes a `manifest.json`, the canonical JSON catalogue, optional rights-cleared images, and release notes.

The manifest must provide:

| Manifest concept | Requirement |
|---|---|
| Manifest format version | Version of the manifest structure itself. |
| Catalogue version | Version of catalogue content, independent of schema version. |
| Schema version | Version of the catalogue JSON schema/format. |
| Release timestamp | UTC timestamp; RFC 3339 date-time string is recommended, pending final schema approval. |
| Source information | Approved source identifiers/URLs actually used; the agreed primary source is `https://nms.source.gs/`. |
| Attribution information | Required public attribution/notices supported by verified rights information. Do not assert an unverified license. |
| Package files | Relative package asset paths/names and their byte sizes. |
| SHA-256 | SHA-256 digest for every listed downloadable package asset. Lowercase hexadecimal is the recommended textual encoding, pending final schema approval. The manifest does not checksum itself. |
| Compatibility information | Consumer/schema compatibility metadata when defined. Detailed compatibility policy remains pending. |

The suggested release asset arrangement is `manifest.json`, a catalogue JSON asset, and an optional image archive whose package-relative members match image paths in the catalogue. Exact asset names, archive format, and whether images are individual files or archived remain to be confirmed. Verify the SHA-256 digest and byte size of each downloaded asset before processing it.

### Version distinction and compatibility

Catalogue content version changes when records or validated catalogue facts change. Schema version changes only when the interchange structure or interpretation changes. Manifest format version changes when the manifest structure changes. These version dimensions must not be conflated.

Use the schema version to decide whether a consumer can parse the contract; an application must reject an unsupported schema rather than silently misinterpret it. Catalogue version identifies content and supports update comparison; it is not a schema compatibility declaration. Exact version numbering rules, compatibility ranges, and release policy require owner approval and are listed in [`DECISIONS.md`](DECISIONS.md).

## Security, validation, and non-destructive application import

Before import, the application must:

1. Verify the SHA-256 digest and byte size for every listed package asset.
2. Enforce configured download, archive, image, and JSON size/resource limits; concrete limits remain an implementation decision.
3. Validate JSON against the supported, versioned schema. Reject malformed or unsupported data.
4. Validate UUID uniqueness, references between recipes/items/images, validation-state values, and stack-limit evidence/state consistency.
5. Reject absolute, URL-based, traversal, or package-root-escaping paths. During archive extraction, reject traversal entries, symlinks, and unsafe file types.
6. Validate actual image bytes and media type rather than trusting file extensions or metadata. Image decoding/re-encoding policy remains to be decided.
7. Back up the current catalogue and import only into logically separate catalogue tables in the application's SQLite database, using an atomic transaction/swap and rollback on failure.

Catalogue data is installation-wide reference data, not household-owned data. A catalogue update must never overwrite or delete household accounts, Traveller profiles, personal inventory, locations, starships, freighters, uploaded photographs, or inventory history. Catalogue import must not use cascading deletion into personal records. If an item disappears or is retired, preserve its UUID as a legacy record so personal inventory references remain valid. Catalogue changes must not automatically modify a player's inventory or in-game state.

Keep credentials, private user data, local environment files, and confidential permission evidence out of Git and public release packages. Do not follow catalogue-provided URLs during application import.

## Decisions that remain open

See [`DECISIONS.md`](DECISIONS.md), including exact category taxonomy and source mapping; optional/missing/null rules and final JSON Schema; image formats and conversion; exact source host, redirects, subdomains, and image-host allowlist; per-asset redistribution rights; signature/attestation for manifests; and detailed application/schema compatibility policy.
