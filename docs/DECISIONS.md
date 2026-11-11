# Decisions and unresolved details

## Authority

[`MASTER_SPECIFICATION.md`](MASTER_SPECIFICATION.md) is authoritative for agreed product requirements. This log records decisions that remain open; it does not override the specification. A choice affecting requirements or behavior must be confirmed and recorded here rather than silently assumed.

## Agreed constraints

- The project uses three independent Git repositories: public application (`nmsinventory`), public catalogue distribution (`nmsinventory-data`), and private scraper (`nmsinventory-scraper`).
- The application consumes published catalogue releases and does not depend on or contain the private scraper.
- Planned application architecture: React + TypeScript + Vite; Node.js + Express; SQLite; Docker Compose; default port 8787.
- Multiple household accounts and multiple Traveller/save profiles are supported. Account switching and profile switching are distinct, with account/profile data isolation.
- Primary catalogue source: `https://nms.source.gs/`. Do not use NMS Handbook. Scraper crawling is limited to the agreed source website.
- Unknown stack limits remain unknown; do not guess.
- Catalogue updates must not overwrite personal data.
- Do not publish catalogue material or images until redistribution permissions have been verified.
- Do not mark milestones complete before implementation and testing.

## Open decisions

| Topic | What remains to be confirmed | Affected repositories |
|---|---|---|
| Catalogue schema and encoding | Machine-readable format and/or database format, manifest schema, exact record names/types, field optionality, and compatibility policy | Application, data, scraper |
| Stable identifiers | How normalized catalogue identifiers are created and remain stable across updates | Application, data, scraper |
| Source mapping and coverage | Actual fields/pages available at the approved source and supported category/recipe coverage; determine only after source discovery is authorized | Data, scraper |
| Provenance and validation | Exact provenance metadata, confidence/status vocabulary, validation rules, and timestamp semantics | Data, scraper, application |
| Stack limits | Verified source evidence, unknown-value representation, difficulty-specific behavior, and validation rules | Data, scraper, application |
| Images | Image package layout, reference syntax, formats, missing-image handling, and documented redistribution permissions | Data, scraper, application |
| Release mechanics | Versioning policy, asset names, compression, checksum algorithm, release notes and manifest conventions | Data, scraper, application |
| Catalogue update semantics | How the application handles changed or removed records while preserving inventory references | Application, data |
| Application implementation details | ORM and migration tool, frontend state/routing/testing choices, session/authentication approach, API conventions, Docker layout, environment variable names, and backup format | Application |
| Content rights | Verify permission covers each relevant text/image/material and that the source owner can grant those rights; define attribution/licensing notices before public distribution | Data, scraper |

These are intentionally unresolved; no serialization format, source field names, stack-limit values, version scheme, or licensing outcome is asserted here.

## Recording future decisions

When a decision is confirmed, record the selected option, rationale, date/context, and repositories affected in this document. Update [`DATA_CONTRACT.md`](DATA_CONTRACT.md), architecture documentation, and the corresponding repositories' progress documents when the decision changes their planned work.

