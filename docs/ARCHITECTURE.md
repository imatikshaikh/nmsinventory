# Application architecture

## Status and authority

This is the planned architecture for `nmsinventory`, based on [`MASTER_SPECIFICATION.md`](MASTER_SPECIFICATION.md) and the canonical cross-repository [`DATA_CONTRACT.md`](DATA_CONTRACT.md). The project is still in documentation and architecture planning; this is not an implemented design. The master specification and approved decisions take precedence over unresolved recommendations.

## Technology direction

| Area | Agreed direction | Not yet selected or implemented |
|---|---|---|
| Frontend | React, TypeScript, Vite; responsive and accessible | UI libraries, routing, state management, and test tooling |
| Backend | Node.js, Express, TypeScript; REST API initially | API conventions, validation library, and session implementation |
| Application database | SQLite with migrations | ORM and migration tooling |
| Catalogue storage | Separate logical catalogue tables in the application's SQLite database for v1 | Physical table schema, import staging/swap details, and indexes |
| Deployment | Docker Compose; default application port **8787** | Image/build layout, environment variable names, and volume names |

These are architectural constraints, not instructions to scaffold the application during this documentation task.

## Repository and runtime boundaries

The application repository owns the web application, server, authentication and authorization, personal inventory, installation of published catalogue data, deployment, tests, and user documentation.

The public `nmsinventory-data` repository distributes versioned catalogue packages through GitHub Releases. The application consumes those releases only and must continue to work offline with its last installed catalogue. It must not import code from, call, or require access to the private `nmsinventory-scraper`.

The private scraper prepares candidate JSON exports and packages for review and handoff to the data repository. Its internal database may use a different schema; its exported JSON must conform to the single canonical contract in [`DATA_CONTRACT.md`](DATA_CONTRACT.md). The scraper is not part of application runtime or deployment.

## Data ownership and isolation

Keep these scopes distinct:

1. **Household account** — a person who authenticates to an application installation.
2. **Traveller/save profile** — one game save belonging to one household account.
3. **Personal inventory and related data** — owned by a Traveller/save profile and authorized through its owning account.
4. **Catalogue** — installation-wide reference data, not owned by an account or profile.

Account switching changes authenticated account context. Profile switching selects a save within that account. These are separate actions and authorization checks. Every private operation must verify the authenticated account and, where applicable, ownership of the selected profile.

Account-owned data (credentials, account profile information, and account-level preferences) must not be visible to another account. Save-specific inventory, locations, ships, freighters, history, uploaded photographs, and profile preferences must be scoped to the owning account and selected profile. The exact personal-data schema and authorization implementation remain to be designed.

Catalogue item IDs are immutable UUIDs. Personal inventory references a catalogue ID but remains a separate user-owned record. Catalogue import/update is restricted to catalogue tables and must never overwrite or delete household accounts, Traveller profiles, personal inventory, locations, starships, freighters, uploaded photographs, or inventory history.

## SQLite catalogue boundary and retirement

For v1, catalogue tables are logically separate from account, profile, inventory, location, ship, freighter, photo metadata, and history tables in the application's SQLite database. Do not treat the release as a SQLite database; the canonical interchange is versioned JSON. The app validates the JSON and imports it into its own catalogue tables.

Use a recoverable catalogue backup and an atomic transaction/swap when importing. Roll back to the prior catalogue on failure. Import logic must not delete/cascade into personal data. When an item disappears or is retired, preserve the catalogue record with its immutable ID and mark it retired/legacy. Existing inventory references remain valid. ID aliases/migration mappings are retained explicitly and never cause automatic changes to a player's inventory.

## Catalogue release and update

The application reads published releases from `nmsinventory-data`, never from the private scraper. The shared [`DATA_CONTRACT.md`](DATA_CONTRACT.md) defines the JSON record concepts, validation vocabulary, UUID lifecycle, package-relative image references, manifest concepts, and integrity requirements.

The planned update flow checks the latest release, compares catalogue content versions, displays release notes, asks for confirmation, downloads assets, verifies byte sizes and SHA-256 checksums, validates schema and references, backs up the current catalogue, and atomically installs or rolls back. The last successful catalogue remains available offline.

Catalogue content version, catalogue schema version, and manifest format version are distinct. The application must reject unsupported schema versions rather than misinterpret them. Detailed compatibility ranges remain an open decision.

## Security and reliability constraints

- Use secure password hashing, protected sessions, authorization on every private operation, input validation, and rate limits for authentication and sensitive actions.
- Validate personal uploads; store uploaded user images in persistent storage rather than large SQLite blobs.
- Treat release data as untrusted input. Validate JSON against the supported schema and validate UUIDs, references, states, stack-limit evidence, and file metadata.
- Verify SHA-256 and asset size before processing. Enforce configured download, archive, image, and JSON limits.
- Reject absolute, URL-based, traversal, and package-root-escaping image paths; reject archive traversal entries and symlinks.
- Validate image content and media type, not just extension. Do not fetch arbitrary source URLs during import.
- Back up and atomically import catalogue tables with rollback. Never overwrite or delete personal records or history.
- Keep core inventory intelligence usable without an AI service, and never automate in-game inventory changes.
- Keep credentials, private user data, local environment files, and confidential permission evidence out of Git and public packages.
- Test account/profile isolation, catalogue validation/import/rollback, and critical inventory calculations before declaring the relevant milestone complete.

## Decisions still pending

See [`DECISIONS.md`](DECISIONS.md): exact category taxonomy and source mapping; optional/missing/null conventions and final JSON Schema; image format/conversion; source host, redirects, subdomains, and image-host allowlist; redistribution permissions; signature/attestation; and detailed version compatibility policy. These open matters do not change the approved JSON, UUID, SQLite logical-separation, or non-destructive-retirement requirements.
