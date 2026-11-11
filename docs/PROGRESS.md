# Application repository progress

## Current phase

**Cross-repository documentation and architecture contract synchronized.** This documentation milestone is complete; application implementation has not started.

## Completed tasks

- Documented the owner-approved canonical, versioned JSON interchange contract in [`DATA_CONTRACT.md`](DATA_CONTRACT.md).
- Synchronized the application specification, architecture, decisions, README, and Copilot guidance with the approved UUID identity, separate recipe/image records, validation states, stack-limit rules, manifest/checksum/version distinctions, SQLite catalogue-table boundary, and non-destructive retirement behavior.
- Aligned the public data release format and private scraper export plan with the same contract and terminology.
- Confirmed and documented that all three repositories remain independent; the application consumes published data releases and does not depend on scraper code.
- Checked that the cross-repository documentation has no contradictory requirements. No implementation, scraping, or release milestone is marked complete.

## Pending tasks

- Resolve remaining owner decisions listed in [`DECISIONS.md`](DECISIONS.md), including source scope and collection policy, redistribution rights, category taxonomy, final JSON Schema and optional/null rules, timestamp/checksum text encodings, image formats/conversion, release signatures, and compatibility policy.
- Implement and test application milestones only after required architecture decisions are settled.
- Do not begin catalogue collection or publish assets until authorization and rights gates are met.

## Decisions still required

See [`DECISIONS.md`](DECISIONS.md). Approved technical requirements are not pending. Open matters are limited to source/permission questions, detailed schema/taxonomy, image processing, compatibility/signature policy, operational limits, and other listed implementation choices.

## Dependencies on other repositories

- **`nmsinventory-data` (public):** application imports validated JSON from its versioned GitHub Releases according to the shared contract.
- **`nmsinventory-scraper` (private):** no runtime or source-code dependency. Its conforming exports may be reviewed and handed to the data repository; the application must never depend on the scraper.

## Next recommended action

Resolve the pending owner decisions and document permitted source/asset scope before any catalogue import implementation, crawling, image downloading, or public release work.
