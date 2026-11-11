# Repository guidance

- Treat [`docs/MASTER_SPECIFICATION.md`](../docs/MASTER_SPECIFICATION.md) as authoritative product requirements and [`docs/DATA_CONTRACT.md`](../docs/DATA_CONTRACT.md) as the one canonical cross-repository JSON contract.
- Keep this repository independent from `nmsinventory-data` and the private `nmsinventory-scraper`. Consume published GitHub Releases only; never copy scraper source into this repository or make runtime depend on it.
- Follow the React + TypeScript + Vite frontend, Node.js + Express backend, SQLite, and Docker Compose direction. Default application port is 8787.
- For v1, catalogue records belong in separate logical catalogue tables in the application's SQLite database. Catalogue data is installation-wide reference data, distinct from household accounts, Traveller/save profiles, personal inventory, locations, starships, freighters, uploaded photos, and history.
- Account switching and Traveller/save-profile switching are distinct. Enforce account ownership and profile isolation for personal data.
- Use immutable UUID-based item IDs and a persistent ID registry. IDs survive changes to descriptive fields. Recipes are independent records with their own immutable UUIDs and item-ID references.
- Use only the shared validation states `verified`, `unknown`, `unverified`, `incomplete`, and `conflicting`; never use a confidence score as a substitute.
- Publish numeric stack limits only when verified, with evidence and difficulty applicability where supported. Never guess.
- Use package-relative image references; preserve source URLs and provenance separately. Reject unsafe paths, do not fetch arbitrary URLs during import, and protect archive extraction against traversal.
- Validate JSON against the supported schema; verify package sizes and SHA-256 checksums. Import catalogue tables atomically with backup and rollback. Preserve retired/legacy IDs and never delete personal records or their references.
- The agreed primary source is `https://nms.source.gs/`. Do not use NMS Handbook. Collection belongs only to the private scraper and must wait for authorization and source-boundary decisions.
- Do not redistribute text, images, or other source material until applicable rights and attribution have been verified. Keep private permission evidence, secrets, credentials, private user data, and local environment files out of Git and public releases.
- Decisions listed in [`docs/DECISIONS.md`](../docs/DECISIONS.md) remain pending; do not silently resolve them or imply source fields not inspected and verified.
- Do not describe a feature or milestone as complete until implemented and tested. Keep progress records current.
