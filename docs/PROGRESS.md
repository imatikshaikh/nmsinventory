# Application repository progress

## Current phase

**Project documentation and bootstrap.** Requirements are agreed; application implementation has not started. The progress document does not mark a product milestone complete.

## Completed tasks

- Reviewed the authoritative master specification.
- Confirmed the application repository boundary and the presence of the independent data and private scraper repositories.
- Added the initial application README, architecture, catalogue contract, decision log, and repository guidance.

## Pending tasks

- Confirm and synchronize the catalogue schema, manifest, versioning, checksums, image references, and update semantics across all three repositories.
- Verify redistribution permissions for relevant source text, images, and other assets before any public data release.
- Decide application implementation details recorded in [`DECISIONS.md`](DECISIONS.md).
- Implement and test each milestone before marking it complete.

## Decisions still required

See [`DECISIONS.md`](DECISIONS.md). In particular: exact catalogue serialization and manifest, stable identifiers, validated source mapping, image rights/layout, stack-limit semantics, update behavior for changed/removed records, and application implementation tooling.

## Dependencies on other repositories

- **`nmsinventory-data` (public):** application catalogue consumption and updates depend on its published, versioned releases and the shared data contract.
- **`nmsinventory-scraper` (private):** no runtime or source-code dependency. Its prepared releases may feed the data repository, but the application must never depend on the scraper.

## Next recommended action

Resolve and record the cross-repository catalogue contract and release-package decisions before implementing the application catalogue importer or updater.

