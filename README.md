# No Man's Sky Inventory Manager

A planned, self-hosted application for managing No Man's Sky inventories, storage, ships, and catalogue information. Requirements are maintained in [`docs/MASTER_SPECIFICATION.md`](docs/MASTER_SPECIFICATION.md).

## Project status

This repository is in the documentation and bootstrap phase. The application has not been implemented. Do not treat architecture plans or milestones as completed features.

## Planned architecture

- React, TypeScript, and Vite frontend.
- Node.js, Express, and TypeScript backend.
- SQLite application database, with migrations and an ORM choice still to be confirmed.
- Docker Compose deployment, using port **8787** by default.
- Independent household accounts, each with isolated data and one or more Traveller/save profiles. Switching accounts is separate from switching save profiles.
- Catalogue data installed from published releases in the independent `nmsinventory-data` repository. The application does not depend on the private `nmsinventory-scraper` repository.

See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) for boundaries and open decisions, and [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md) for the planned catalogue interface.

## Documentation

- [`docs/MASTER_SPECIFICATION.md`](docs/MASTER_SPECIFICATION.md) — authoritative product requirements.
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — planned system structure.
- [`docs/DATA_CONTRACT.md`](docs/DATA_CONTRACT.md) — catalogue and release data requirements.
- [`docs/DECISIONS.md`](docs/DECISIONS.md) — unresolved technical and product decisions.
- [`docs/PROGRESS.md`](docs/PROGRESS.md) — current phase, dependencies, and next action.

## Development

No application runtime, install procedure, or development commands exist yet. Do not install dependencies or infer implementation details from this documentation. Agree the open decisions and update the documentation before starting implementation.

## Security and privacy

Never commit credentials, API keys, passwords, private user data, local environment files, or generated personal inventory data. Account and Traveller-profile data must remain isolated. Catalogue updates must not overwrite personal data.
