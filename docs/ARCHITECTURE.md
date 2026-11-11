# Application architecture

## Status and authority

This is the planned architecture for `nmsinventory`, based on [`MASTER_SPECIFICATION.md`](MASTER_SPECIFICATION.md). The project is still in documentation and bootstrap; this is not an implemented design. The master specification takes precedence if a detail here conflicts with it. Unresolved choices are tracked in [`DECISIONS.md`](DECISIONS.md).

## Technology direction

| Area | Agreed direction | Not yet selected or implemented |
|---|---|---|
| Frontend | React, TypeScript, Vite; responsive and accessible | UI libraries, routing, state management, and test tooling |
| Backend | Node.js, Express, TypeScript; REST API initially | API conventions, validation library, and session implementation |
| Application database | SQLite, separate from catalogue data; migrations | Prisma or another ORM; concrete schema and migration tooling |
| Deployment | Docker Compose; default application port **8787** | Image/build layout, environment variable names, and volume names |

These are architectural constraints, not instructions to scaffold the application during this documentation task.

## System boundaries

The application repository owns the web application, server, authentication and authorization, personal inventory, local catalogue installation and update behavior, deployment, tests, and user documentation.

The catalogue is distributed by the independent public `nmsinventory-data` repository. The application consumes a published, versioned data release and must continue to work offline with its last installed catalogue. It must not import code from, call, or require access to the private `nmsinventory-scraper` repository.

The private scraper prepares candidate catalogue data and release packages. It is not part of application runtime or deployment.

## Data ownership and isolation

Model two distinct selection scopes:

1. **Household account** — a person who authenticates to an application installation.
2. **Traveller/save profile** — one game save belonging to an account.

An account can own multiple Traveller profiles. Account switching changes the authenticated account context; profile switching selects a save within that account. These must remain separate user actions and authorization checks. Do not use profile switching as a substitute for account switching.

Account-owned data (including credentials, account profile information, and account-level preferences) must not be visible to another account. Save-specific inventory, locations, ships, history, and profile preferences must be scoped to the owning account and the selected Traveller profile. Every private operation must verify the authenticated account and, when applicable, ownership of the selected profile. The exact database key structure and authorization implementation remain to be decided.

The first-run flow creates the initial administrator through the application interface. The application must support additional accounts and multiple save profiles without conflating them. When there is one account/profile, avoid unnecessary selection screens as specified in the master document.

## Database responsibility

SQLite is the initial application database. It stores application-owned user and save data, inventory history, and the installed catalogue version/reference as needed. Catalogue records and personal inventory are separate data domains: catalogue refreshes may update catalogue information but must never delete or overwrite accounts, profiles, inventory, uploaded personal images, or activity history.

The catalogue's published storage format and the application's local import representation are not yet specified. Do not assume the release will be a SQLite database or prescribe tables until the cross-repository contract has been confirmed. Schema, ORM, migration format, backup mechanics, and exact catalogue import boundaries are open decisions.

Uploaded user images must use persistent application storage; large photo binaries must not be stored directly in SQLite. Database paths and image metadata may be persisted once their implementation details are decided.

## Catalogue update boundary

The application reads only published releases from the data repository. The planned update flow checks the published version and notes, requests confirmation, downloads and verifies the package, backs up the installed catalogue, validates and installs atomically, and rolls back on failure. Offline use must retain the last successful catalogue. No update may mutate personal inventory.

The precise manifest schema, serialization, version comparison rules, checksum algorithm, package layout, and import procedure are decisions to confirm in [`DATA_CONTRACT.md`](DATA_CONTRACT.md) and [`DECISIONS.md`](DECISIONS.md).

## Deployment and configuration

Docker Compose is the planned deployment model. The default application port is **8787**. Persistent storage is required for the SQLite database and uploaded images. The first-run administrator account must be created in the application, not by requiring manual credential edits in a `.env` file.

Configuration should use environment variables where appropriate, but names, defaults, secret provisioning, and container layout must be selected during implementation planning. Keep secrets and local environment files out of Git.

## Reliability and security constraints

- Use secure password hashing, protected sessions, authorization on every private operation, input validation, and rate limits for authentication and sensitive actions.
- Validate and safely store uploaded images.
- Back up application data and provide a recovery workflow.
- Treat catalogue installation as a validated, atomic operation with rollback.
- Keep the core inventory intelligence usable without an AI service.
- Never automate changes to a player's in-game inventory.
- Test account/profile isolation and critical inventory calculations before declaring the relevant milestone complete.

