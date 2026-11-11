# Decisions and unresolved details

## Authority

[`MASTER_SPECIFICATION.md`](MASTER_SPECIFICATION.md) is authoritative for product requirements. The following owner-approved technical decisions are requirements across all three repositories. This log tracks only unresolved matters and recommendations that have not been approved; it does not weaken or override the approved requirements.

## Approved requirements

- Keep three independent Git repositories: public application (`nmsinventory`), public catalogue data (`nmsinventory-data`), and private scraper (`nmsinventory-scraper`).
- Use versioned JSON as the common interchange format. The scraper may use an internal database schema of its own, but its exported JSON must conform to the single canonical contract in [`DATA_CONTRACT.md`](DATA_CONTRACT.md).
- Use immutable UUID-based item IDs and maintain a persistent ID registry. IDs survive changes to names, categories, descriptions, and images. Recipes are separate records with immutable UUID-based IDs and item-ID references.
- Use package-relative image references; preserve original source URLs and provenance separately. Reject unsafe paths and prevent path traversal.
- Release packages through versioned GitHub Releases and use `manifest.json` plus SHA-256 checksums.
- Version catalogue content independently from catalogue schema/format. Keep manifest format version distinct as specified in the contract.
- For v1, store catalogue data in logically separate catalogue tables in the application's SQLite database.
- Preserve retired catalogue records and IDs as legacy records. Never delete personal inventory references to a retired item.
- Use only the explicit validation states `verified`, `unknown`, `unverified`, `incomplete`, and `conflicting`. They are states, not confidence scores.
- Publish numeric stack limits only when verified. Record difficulty applicability only when supported by evidence; never guess.
- The application consumes published data releases and never depends on scraper source. The private scraper must never be copied into either public repository.
- Primary source is `https://nms.source.gs/`; do not use NMS Handbook. Crawling remains restricted to the approved source website.
- Never let catalogue updates overwrite or delete household accounts, Traveller profiles, personal inventory, locations, starships, freighters, uploaded photographs, or history.
- Do not publish source material or images until applicable redistribution rights and attribution requirements have been verified.

## Pending owner decisions

| Topic | Question / decision still required | Affected repositories |
|---|---|---|
| Crawl origin and redirects | Confirm exact source host/origin, allowed redirects, whether any subdomains are in scope, and permitted image hosts. Keep source fixed at `https://nms.source.gs/`; do not expand scope by inference. | Scraper, data |
| Collection authorization | Clarify what evidence documents the stated source-owner permission to scrape/share, and the crawl policy/authorization required before collection. This is separate from rights to redistribute each asset. | Scraper, data |
| Text and image rights | Verify permission and chain of rights for each included content/asset type, required attribution/license notices, and whether transformations are allowed. Keep private permission evidence out of public repositories/releases. | Data, scraper |
| Category taxonomy | Approve exact categories/subcategories and their identifiers/names only after source coverage is understood. | All three |
| Optional/missing/null conventions | Approve which fields are required, optional, omitted, or nullable, including empty-string/empty-array semantics. Until then, do not infer missing facts; omit unavailable optional values. | All three |
| Final JSON Schema | Finalize property names, types, constraints, field-level validation representation, timestamps, recipe metadata, and registry/mapping representation after authorized source inspection. The conceptual contract in `DATA_CONTRACT.md` is canonical meanwhile. | All three |
| Timestamp and checksum text encoding | Confirm the recommended UTC RFC 3339 timestamp representation and lowercase hexadecimal encoding of SHA-256 digests in the final schemas. The UTC timestamp requirement and SHA-256 algorithm are approved. | All three |
| Image handling | Approve image formats, conversion/re-encoding policy, archive layout, and any source image host allowlist after rights and source-boundary review. | All three |
| Rights status vocabulary | Confirm exact public rights/redistribution status enum and safe evidence/reference representation. Never include confidential permission evidence. | Data, scraper |
| Catalogue and schema version policy | Approve numbering rules, compatibility ranges, and how catalogue removals/changes affect compatibility. The three version dimensions remain distinct. | All three |
| Release signatures | Decide whether to sign `manifest.json` or use another trusted release attestation, and define verification/rotation. SHA-256 checksums are required regardless. | Data, application |
| ID merge policy | Approve who reviews duplicate merges, canonical-ID selection, alias/migration mapping shape, and app display/resolution behavior. Existing inventory references must remain non-destructive. | All three |
| Stack-limit evidence and difficulty | Confirm acceptable evidence and the difficulty vocabulary/representation; include only verified numeric values. | All three |
| Import limits | Select concrete configurable JSON, download, archive, and image size/resource limits and enforcement behavior. | Application, data |
| Application implementation details | ORM and migration tool, API conventions, session/authentication approach, frontend state/routing/testing choices, Docker layout, environment variable names, and backup details. | Application |

## Technical conventions in the canonical contract

[`DATA_CONTRACT.md`](DATA_CONTRACT.md) records the approved semantic contract and safe provisional handling, including UUID identities, separate recipe/image records, validation meanings, stack-limit rules, UTC timestamps where present, manifest contents, checksum requirements, safe paths, and catalogue-only atomic import.

The final JSON Schema and details explicitly listed above remain pending. Examples in the contract are illustrative only; they are not source data or permission to infer source fields.

## Recording decisions

When an owner decision resolves an open item, record its approved value and rationale here and synchronize the master specification (if product requirements change), canonical contract, architecture, release format, scraper plan, repository instructions, and progress documents as applicable. Never mark implementation, scraping, or release milestones complete without implementation and testing.
