# Repository guidance

- Treat [`docs/MASTER_SPECIFICATION.md`](../docs/MASTER_SPECIFICATION.md) as the authoritative product requirements. Where this file or other documentation is incomplete, record the gap as a decision; do not silently invent a requirement.
- Keep this repository independent from `nmsinventory-data` and the private `nmsinventory-scraper`. The application consumes published catalogue releases only; never copy scraper source into this repository.
- Preserve the boundary between household accounts and Traveller/save profiles. Account switching and profile switching are distinct, and all user- and profile-owned data must be isolated.
- Follow the documented React + TypeScript + Vite frontend, Node.js + Express backend, SQLite, and Docker Compose direction. The default application port is 8787. Do not make unresolved technology choices on the project's behalf.
- Keep catalogue data separate from personal inventory. Unknown or unverified stack limits must remain unknown; never guess them.
- Do not use NMS Handbook. The agreed catalogue source is `https://nms.source.gs/`; source collection belongs only to the private scraper repository.
- Do not redistribute artwork or other source material until the relevant permissions and attribution requirements have been verified.
- Never commit secrets, credentials, private user data, local environment files, or personal inventory exports.
- Do not describe a feature or milestone as complete until it has been implemented and tested. Keep the progress document and decision log accurate.
- During the documentation/bootstrap phase, do not create application code, install dependencies, or claim that a runtime is available.

