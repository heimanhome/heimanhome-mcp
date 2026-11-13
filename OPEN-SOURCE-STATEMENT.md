# Open-Source Statement

> Project: Heiman Smart Home Connector (`connector/` metadata bundle)
> Version: 1.1.0 | Date: 2026-11-13
> This statement, together with `LICENSE` and `NOTICE`, forms the legal documentation of this public release.

> **Before publishing:** fill the remaining `<...>` placeholders (copyright holder, maintaining organization) and obtain Legal / Brand approval.
>
> **Language:** English is authoritative. Translations under `i18n/` cover `README.md` only and are provided for convenience; the English text prevails in case of conflict.

---

## 1. What this project is

This repository publishes **a Connector metadata bundle only**: descriptors telling an MCP-compatible client how to discover and connect to a remote smart home MCP service. It contains the connector descriptor, client connection config, an icon and the Skill instructions.

**This repository contains no server implementation.** Server source code, tests, scripts, upstream API contract documents, the authorization login page and their brand assets remain private, are not distributed here, and are outside the scope of this statement.

This is a third-party integration. It is **not an official Heiman product, nor an official WorkBuddy / CodeBuddy product**, and has received no endorsement, certification or authorization from either party. Brand names identify the integration target only and imply no affiliation or partnership.

---

## 2. What is open source

| Included | License |
| --- | --- |
| `connector-meta.json`, `mcp.json` | MIT (see `LICENSE`) |
| `skills/heimanhome-skill/SKILL.md` | MIT |
| `README.md`, `i18n/README.<locale>.md` | MIT |
| `OPEN-SOURCE-STATEMENT.md`, `NOTICE` | MIT |

| Excluded | Reason |
| --- | --- |
| The base64-embedded PNG artwork in `icon.svg` | Copyright / redistribution rights not confirmed; MIT does not cover it |
| Trademarks: "Heiman" / 海曼, "WorkBuddy", "CodeBuddy", "Copilot" | Owned by their respective holders; MIT grants no trademark rights |
| Upstream platform API definitions, API docs and server source | Not assets of this project; not distributed here |
| Private repository content not shipped with this bundle | Out of scope; its paths and names are not listed here |

---

## 3. License

Original content in this bundle uses the **MIT License** (full text in `LICENSE`): free use, copy, modify, publish, distribute, sublicense and sell, provided the copyright and permission notices are retained; provided "AS IS" without warranty.

Boundaries: MIT covers only the files in §2; it does not cover trademarks, the icon artwork or private content not shipped with the bundle, and grants no trademark rights. If Legal requires explicit patent or trademark terms, switch to Apache-2.0 (replace the full `LICENSE` text and update `NOTICE` accordingly).

---

## 4. Prerequisites for use

`mcp.json` → `url` and `connector-meta.json` → `server_auth.data.mcp_oauth.server_url` point to the deployed HTTPS endpoint `https://spmcp.heimanhome.com/mcp`.

- Connecting requires that endpoint to be publicly reachable. A deployer running their own server must replace the address in both files with a publicly reachable HTTPS MCP server address.
- The server must implement MCP Streamable HTTP and OAuth (RFC 9728 / 8414 / 7591 / 7636 / 7009) and support the five scopes `device:read`, `device:control`, `scene:read`, `scene:execute`, `security:control`.
- `clientId` / `clientSecret`, user accounts and tokens for the upstream smart home platform must be obtained lawfully by the deployer. This bundle does not supply, embed or apply for them.
- The deployer must obtain integration authorization from the upstream platform and comply with its terms of service, developer agreement, quota limits and data-use restrictions. Upstream API changes may break this bundle; no continuing compatibility is warranted.

---

## 5. Trademarks

"Heiman", 海曼 and their device marks, "WorkBuddy", "CodeBuddy", "Copilot" and related marks belong to their respective owners. Their appearance here is nominative and descriptive only.

No one may infer a right to use those marks from the MIT License; obtain written authorization instead. Without authorization, the publisher must switch to a neutral name and a self-created icon and update `connector-meta.json`, `mcp.json`, `SKILL.md` and every README accordingly.

---

## 6. Data and privacy

This bundle **does not collect, store or transmit** personal data. The remote MCP service it references may process homes, rooms, device status, logs, temperature and humidity, alarms and household members — data that can contain personal information.

**The deployer of that service is the data controller** and is responsible for notice and consent, data minimization, access control, retention periods, deletion responses, audit logging and incident handling, as well as compliance with the PIPL, DSL, CSL and any other applicable law (including GDPR where relevant). Cross-border transfers require a separate assessment.

---

## 7. No warranty and limitation of liability

This bundle is provided "AS IS" without warranty of any kind, express or implied, including merchantability, fitness for a particular purpose, non-infringement, availability, security, or compatibility with the upstream platform or any particular MCP client version. See `LICENSE`.

The remote service described here offers write operations including device control, scene editing and alarm arm/disarm. Users must assess the risk themselves. The server should provide `dry_run` previews, `confirm=true` two-step confirmation, least-privilege scopes, idempotency keys and rate limiting — but those mechanisms do not replace the user's judgement and on-site safety checks. Maintainers are not liable for device state changes, data loss or service interruption arising from use of this bundle or the service it references.

---

## 8. Security reporting

- Channel: `help@heimanhome.com`.
- Include the affected version, reproduction steps, impact and a suggested fix.
- **Never paste** real `client_secret`, access/refresh tokens, passwords, verification codes or internal domains into public issues, PRs or discussions.
- For vulnerabilities involving the upstream platform, also follow that platform's security process.
- A `SECURITY.md` reusing this section is recommended for the public repository.

---

## 9. Contributing

1. Contributions must be original or lawfully redistributable content that the contributor has the right to submit, and are accepted under this bundle's `LICENSE`.
2. **Do not submit** upstream internal API documents, full API inventories, upstream source code, real credentials, real user data, internal domains, private IP ranges or third-party brand assets.
3. Field changes must be reflected in `connector-meta.json`, `mcp.json`, `SKILL.md`, `README.md` and all `i18n/README.<locale>.md`, and must pass JSON validation.
4. Version bumps must align `connector-meta.json` → `version`, `SKILL.md` → `version` and the Git tag.
5. New or changed user-facing copy must be added to `connector-meta.json` → `i18n.locales` for all 8 supported locales (`en`, `de`, `fr`, `es`, `pt`, `ar`, `ru`, `zh`); `ar` is RTL.
6. A DCO or CLA may be required at the maintainer's discretion.

---

## 10. Miscellaneous

- Users must comply with applicable export control, sanctions, industry regulation and product compliance requirements.
- This statement is not legal advice. Have Legal / Brand / Compliance review `LICENSE`, `NOTICE`, this statement and all brand usage before publication.
- Repository: `https://github.com/heimanhome/heimanhome-mcp.git`; maintainer: `<maintaining organization>`.
- Changes are recorded in Release Notes.

---

**Revision history**

| Version | Date | Change |
| --- | --- | --- |
| v1.0.0 | 2026-11-13 | First version: defined scope, license boundaries, non-official status, trademarks, data/privacy, security reporting and contribution rules for the metadata-bundle-only release |
| v1.1.0 | 2026-11-13 | English made authoritative; 7 translated READMEs added; version alignment; contribution rules extended to the 8-locale copy source |
