# No Man's Sky Inventory Manager
## Master Product Specification

**Project status:** Requirements agreed; implementation not started  
**Product type:** Open-source, self-hosted web application  
**Primary goal:** Manage a player's No Man's Sky inventory, storage, starships, freighter and game catalogue.

---

# 1. Project vision

Build a polished, responsive, self-hosted inventory management application for No Man's Sky players.

The experience should feel inspired by the game's universe, Atlas, Travellers and interstellar exploration. It must not feel like a generic inventory spreadsheet or administration dashboard.

The application should help players:

- Maintain an accurate record of their items and quantities.
- See where every item is stored.
- Search a comprehensive No Man's Sky catalogue.
- Manage multiple starships, a freighter, exosuit, exocrafts and storage containers.
- Identify duplicate stacks and consolidate inventory.
- Plan transfers between locations.
- Track storage capacity and available slots.
- Manage multiple household members and multiple game saves.
- Optionally use a remote AI assistant for natural-language inventory advice.

The application must work without any AI service or external API.

# 2. GitHub repository structure

The project will use three independent Git repositories.

## 2.1 Public application repository

**Proposed name:** `nmsinventory`

Contains:

- React frontend.
- TypeScript application code.
- Node.js and Express backend.
- SQLite database integration.
- Authentication and account management.
- Inventory and location management.
- Catalogue browsing and searching.
- Consolidation engine.
- Settings and themes.
- Docker configuration.
- Documentation and tests.

This repository must never contain the private scraper or API credentials.

## 2.2 Public catalogue data repository

**Proposed name:** `nmsinventory-data`

Contains:

- Validated No Man's Sky catalogue data.
- Item names, descriptions, categories and metadata.
- Item images and icons, subject to redistribution rights.
- Catalogue version manifest.
- Release notes and source attribution.
- Compressed, versioned catalogue packages.

The application must be able to download a released catalogue package from GitHub.

## 2.3 Private scraper repository

**Proposed name:** `nmsinventory-scraper`

Contains:

- Private scraping scripts.
- Source discovery and crawling logic.
- Data extraction and normalisation.
- Image downloading.
- Duplicate detection and validation.
- Catalogue generation and packaging.
- Local database and JSON export tools.
- Change detection and update workflows.

The scraper is for the owner's private use and must not be published as part of the public application.

## 2.4 Repository boundaries

The repositories must remain independent Git repositories.

The application consumes published catalogue releases, not the scraper's source code.

The scraper generates data packages for the public data repository.

The public application must never require access to the private scraper repository.

# 3. Technology architecture

## Frontend

- React.
- TypeScript.
- Vite.
- Responsive component-based UI.
- Accessible controls and keyboard navigation.

## Backend

- Node.js.
- Express.
- TypeScript.
- REST API initially.
- Secure authentication and session handling.

## Database

- SQLite for the initial release.
- Prisma or an equivalent well-maintained ORM.
- Database migrations.
- Separate application data from catalogue data.

## Deployment

- Docker Compose.
- Persistent database and uploaded-image volumes.
- Default application port: **8787**.
- Configuration through environment variables where appropriate.
- First-run setup through the application interface, not manual `.env` credential editing.

The architecture should remain modular enough to allow a future database migration if the project grows.

# 4. Household accounts and game save profiles

The application must support multiple user accounts on one installation.

This is important for households where multiple people use the same computer or game installation.

## Account behaviour

- The first application launch opens a setup wizard.
- The first user creates the initial administrator account.
- Users can create additional accounts from the application.
- Each account has its own username, password and profile image.
- Users can change their password and profile information.
- Passwords must be securely hashed.
- Sessions must be protected and have configurable expiration.
- Account data must be isolated from other accounts.

## Traveller profiles / save games

An account may contain multiple Traveller profiles, representing different No Man's Sky save games.

For example:

- Older brother — Save 1.
- Older brother — Save 2.
- Younger brother — Save 1.

Each Traveller profile has its own:

- Inventory.
- Exosuit.
- Starships.
- Freighter.
- Storage containers.
- Exocrafts.
- Inventory history.
- Preferences where appropriate.

Switching accounts and switching save profiles are separate operations.

## Profile selection experience

- If only one account exists, do not show an unnecessary household account-selection screen.
- After authentication, automatically open the account's selected Traveller profile if only one exists.
- If multiple accounts exist, provide an account/profile selection experience inspired by streaming-service profile selectors.
- Provide a clear account-switching action.
- Protect account switching with appropriate authentication or an optional quick-switch PIN.

## Avatar choices

Provide a selection of No Man's Sky-inspired profile portraits, including:

- Korvax.
- Gek.
- Vy'keen.
- Traveller-style portraits.
- Other suitable original or licensed portrait options.

Users may also upload a custom profile photo.

Do not redistribute copyrighted game artwork unless redistribution rights have been confirmed. Prefer original artwork or assets with explicit permission.

# 5. Game catalogue

The primary catalogue source is:

https://nms.source.gs/

Do not use NMS Handbook as a catalogue source.

The scraper must be restricted to the agreed source website and must not crawl unrelated domains or subdomains.

The catalogue should cover all relevant game data available from the source, including:

- Elements and resources.
- Raw materials.
- Refined materials.
- Crafted products.
- Technologies and upgrades.
- Building parts.
- Recipes and crafting requirements.
- Refining recipes.
- Cooking ingredients and products.
- Trade goods.
- Biological and agricultural items.
- Expedition and special items.
- Other relevant game categories.

Catalogue records should support:

- Immutable UUID-based internal identifier maintained in a persistent ID registry.
- Item name.
- Category and subcategory.
- Description.
- Package-relative image reference, with original source URL and provenance stored separately.
- Stack-limit information only when verified; record difficulty applicability only when supported by evidence.
- Crafting/refining relationships represented as separate recipe records referencing item IDs.
- Source URL, provenance, relevant timestamps, and an explicit validation state.

The shared versioned JSON interchange contract, including item, recipe, image, and manifest concepts, is defined in `docs/DATA_CONTRACT.md`. Its validation states are `verified`, `unknown`, `unverified`, `incomplete`, and `conflicting`; they are not confidence scores. Publish a numeric stack-limit value only when verified. Do not guess missing values. Exact category taxonomy, source mapping, and schema details that depend on source inspection remain pending.

Catalogue data and a player's inventory must be separate. For v1, catalogue tables are logically separate from household accounts, Traveller profiles, personal inventory, locations, starships, freighters, uploaded photographs, and history in the application's SQLite database. Updating catalogue information must never overwrite or delete those personal records. Retired catalogue items retain their immutable IDs and remain available as legacy records; personal inventory referencing them is preserved.

# 6. Private scraper requirements

The scraper should:

1. Discover relevant pages through sitemaps, navigation and internal links.
2. Restrict crawling to the agreed source website.
3. Extract item names, descriptions, categories, tables, metadata and relationships.
4. Identify associated image and icon URLs.
5. Download images into an organised local image collection.
6. Preserve source provenance.
7. Normalise and validate extracted records.
8. Detect duplicates and conflicting records.
9. Detect missing images and broken references.
10. Produce a local, complete catalogue database and machine-readable exports.
11. Support incremental updates and full re-crawls.
12. Generate a release-ready catalogue package.

The owner has stated that the source website owner gave permission to scrape and share the material. Before public redistribution, verify that permission covers the relevant text, images and other assets, and that the source owner has the rights to grant it. Preserve attribution and comply with applicable licences.

Do not publish a catalogue release until its data, images, references and redistribution permissions have been reviewed.

# 7. Catalogue release and update system

The public data repository will distribute versioned catalogue packages.

A release should include:

- `manifest.json`, containing separate catalogue content, catalogue schema, and manifest format versions, a UTC release timestamp, source and attribution information, and package-file metadata.
- Source and attribution information.
- The versioned JSON catalogue package.
- An image package, only for assets cleared for redistribution.
- SHA-256 checksums and file sizes for package files.
- Release notes.

Use GitHub Releases for large binary packages rather than continuously adding large database and image archives to Git history.

The application validates the JSON and all package references before import. It verifies SHA-256 checksums, enforces configured size limits, validates image content and safe package-relative paths, and protects archive extraction against path traversal. It backs up the catalogue and imports atomically with rollback. Catalogue writes are restricted to catalogue tables; an update must never overwrite or delete household accounts, Traveller profiles, personal inventory, locations, starships, freighters, uploaded photographs, or inventory history.

The detailed shared contract and unresolved technical decisions are maintained in `docs/DATA_CONTRACT.md` and `docs/DECISIONS.md`.

## Application update process

The Settings page must provide a catalogue update action.

The application should:

1. Check the latest published data release.
2. Compare its version with the installed catalogue.
3. Show the available version and release notes.
4. Ask the user to confirm the update.
5. Download the package.
6. Verify checksums.
7. Back up the existing catalogue.
8. Validate the new package.
9. Import or replace the catalogue atomically.
10. Roll back if the update fails.

The update must never overwrite personal inventories, account information, uploaded profile photos, starship photos or activity history.

The application must continue working with its last downloaded catalogue when offline.

# 8. Personal inventory and locations

The personal inventory system must support:

- Exosuit.
- Multiple individually named starships.
- Freighter.
- Storage containers 0–9.
- Exocrafts, including Minotaur, Colossus, Roamer, Nomad, Pilgrim and Nautilon.
- Custom locations.

Users must be able to add, rename, archive and manage locations.

Deleting or renaming a location must not silently destroy inventory. Require the user to transfer, archive or explicitly remove affected inventory first.

## Starship management

Users can create as many starship records as they need.

Each ship can have:

- User-defined name.
- Ship type.
- Class.
- Inventory.
- Notes.
- Uploaded screenshot or photo.
- Optional multiple images and a primary image.

The app must support adding, editing, archiving and restoring ship records.

Uploaded photos must be stored in persistent application storage, not directly inside SQLite as large binary blobs. Store their paths and metadata in the database.

## Inventory entries

An inventory entry should record:

- Catalogue item.
- Quantity.
- Location.
- Relevant inventory type or slot context.
- Optional notes.
- Created and updated timestamps.

The application must distinguish ordinary cargo from installed technology and upgrades. Do not treat installed technology as ordinary movable stock.

# 9. Quantity management

Provide:

- Plus and minus controls for small quantity changes.
- Direct numeric quantity editing.
- Optional quick adjustments such as ±100, ±500 and ±1000.
- Validation against negative quantities and invalid input.
- Removal of an entry from a particular location.
- A separate, clearly confirmed action to remove an item from all locations.

When quantity becomes zero, remove the entry from the active inventory listing but preserve the action in inventory history.

Removing a personal inventory entry must never delete its catalogue record.

# 10. Inventory intelligence and consolidation

This is a core application feature and must not depend on AI.

The application should calculate:

- Total quantity of each item across all locations.
- Duplicate stacks.
- Required stack count.
- Available destination slots.
- Remaining capacity.
- Potential space savings.
- Transfer quantities and suggested source/destination locations.

Stack limits must be item-specific and based on verified catalogue information and the selected game difficulty.

The initial user difficulty is Normal, but the application should allow users to configure their difficulty.

## Flexible organisation

Do not permanently assign storage containers to fixed categories.

Instead, use logical category ordering for browsing and planning. For example:

1. Minerals and raw materials.
2. Gases and elements.
3. Refined materials.
4. Technologies and upgrades.
5. Crafted products.
6. Biological and agricultural items.
7. Trade goods.
8. Special items.
9. Other items.

This is a suggested organisation and display order, not a permanent container assignment.

## Consolidation planner

The planner should:

- Identify duplicate stacks across locations.
- Calculate whether stacks can be combined.
- Suggest practical transfers.
- Show exactly what to move and where.
- Account for stack limits and available slots.
- Allow users to mark transfers as completed.
- Recalculate the plan after completed transfers.

The application must never move or modify game inventory automatically. The player performs the in-game actions and updates the application.

# 11. User interface and design

The application should be immersive, premium and recognisably inspired by No Man's Sky.

Suggested navigation:

- Atlas — Dashboard.
- Inventory.
- Catalogue.
- Storage Network.
- Starship Registry.
- Consolidation.
- Atlas Intelligence.
- Settings.

Suggested login language:

- WELCOME, TRAVELLER.
- Your journey. Your discoveries. Your inventory.
- CONNECT TO ATLAS.

Use an original visual design rather than copying proprietary game interfaces or assets.

## Visual direction

- Dark atmospheric backgrounds.
- Subtle gradients and holographic accents.
- Thin borders and restrained glow effects.
- Green/cyan accents.
- Carefully selected monospaced typography.
- Smooth, restrained animations.
- Clear information hierarchy.
- Premium visual quality without sacrificing usability.

## Themes

Default theme: two-tone green palette.

Suggested alternatives:

- Galactic Blue.
- Sentinel Amber.
- Void Violet.

Keep the layout and background system consistent. Themes should change accent colours and selected visual details, not redesign the entire application.

Theme preference should be saved per user or Traveller profile as appropriate.

Ensure sufficient contrast and accessibility.

# 12. Responsive design

Responsive behaviour must be considered from the beginning, not added at the end.

Support:

- Mobile phones from approximately 360px width.
- Tablets and iPads.
- 13-inch and 14-inch laptops.
- 17-inch laptops.
- Desktop monitors.
- 2K and 4K displays.
- Larger high-resolution displays.

Use adaptive grids, touch-friendly controls, compact mobile cards and suitable navigation patterns.

Do not simply shrink desktop tables onto mobile. Provide mobile-friendly layouts for inventory and location management.

Test Chrome, Firefox, Safari and Edge, including Safari on iPad and Mac.

# 13. Search and item details

Provide global search across:

- Personal inventory.
- Catalogue.
- Starships and locations where relevant.

Search results should show relevant personal quantity and storage locations alongside catalogue information.

Item detail pages should include:

- Image.
- Name.
- Description.
- Category.
- Verified stack limit.
- Related recipes and refining information.
- Source/provenance.
- Total personal quantity.
- Locations where the item is stored.

# 14. History, backups and exports

Include:

- Inventory activity history.
- Quantity changes.
- Transfers.
- Additions and removals.
- Location changes.
- Catalogue version history where appropriate.

Provide:

- JSON export.
- CSV export where useful.
- Database backup.
- Restore workflow.
- Clear backup and recovery documentation.

# 15. Optional remote AI assistant

AI is a later milestone, not a dependency for the application.

The built-in inventory intelligence must always work without an API.

The optional remote AI assistant may support questions such as:

- How should I consolidate Ferrite Dust?
- Which locations contain this item?
- How can I free up freighter space?
- Explain this proposed organisation plan.

Use configurable remote AI providers or user-supplied API keys where feasible.

Requirements:

- No local AI model requirement.
- API keys stored securely on the server.
- Send only relevant inventory context.
- Token and usage limits.
- Rate limiting.
- Clear privacy controls.
- No automatic inventory changes.
- AI recommendations must be reviewable and optional.

Do not promise unlimited free AI access. The application must remain fully useful without it.

# 16. Security and reliability

- Secure password hashing.
- Protected sessions.
- Authentication and authorization checks on every private API.
- Strict separation of user and Traveller-profile data.
- Input validation.
- Rate limiting for authentication and sensitive actions.
- Secure file upload validation.
- Safe image processing and storage.
- No secrets committed to Git.
- Database migrations and backups.
- Automated tests for critical inventory calculations.
- Atomic catalogue updates and rollback.
- Docker volumes for persistent data.
- Clear installation and recovery instructions.

# 17. Development milestones

## Milestone 1 — Project foundation
- Repository structure and documentation.
- React, TypeScript and Vite.
- Node.js and Express.
- SQLite and migrations.
- Docker Compose on port 8787.
- First-run setup.
- Authentication and multiple accounts.
- Traveller/save profile management.
- Initial Atlas-inspired design.

## Milestone 2 — Catalogue system
- Catalogue schema.
- Catalogue import.
- Search and item details.
- Image handling.
- Catalogue version tracking.

## Milestone 3 — Private scraper and data package
- Source discovery.
- Data extraction.
- Image downloading.
- Validation.
- Local complete catalogue.
- Public data package and release process.

## Milestone 4 — Personal inventory
- Exosuit.
- Starships.
- Freighter.
- Storage containers.
- Exocrafts.
- Custom locations.
- Quantity editing.
- Photo uploads.

## Milestone 5 — Inventory intelligence
- Duplicate detection.
- Stack calculations.
- Consolidation planner.
- Transfer tracking.
- Storage utilisation.
- History.

## Milestone 6 — Catalogue updater
- GitHub release checking.
- Download and verification.
- Backup and atomic import.
- Rollback and offline support.

## Milestone 7 — Optional remote AI
- Provider configuration.
- Inventory-aware questions.
- Recommendations.
- Usage controls.

## Milestone 8 — Polish and public release
- Responsive testing.
- Themes.
- Accessibility.
- Security review.
- Documentation.
- Docker deployment tests.
- Release and maintenance workflow.

# 18. Non-negotiable project rules

1. Do not combine the three repositories.
2. Do not publish the private scraper.
3. Do not use NMS Handbook as a catalogue source.
4. Use NMS Source Database as the primary catalogue source.
5. Do not guess unverified item stack limits.
6. Never overwrite personal inventory during catalogue updates.
7. Never automatically change in-game inventory.
8. Do not require AI for core functionality.
9. Do not permanently reserve storage containers for categories.
10. Do not commit credentials, API keys or private data.
11. Do not publish data or images without confirming redistribution rights.
12. Do not mark a milestone complete without testing it.
13. Keep documentation and progress records up to date.
14. Work in small, reviewable phases rather than one uncontrolled implementation run.