# Open-Source Statement

> Heiman Smart Home Connector · v1.1.0 · 2026-11-13
> English is authoritative. Translations under `i18n/` cover the usage guide only and are provided for convenience.

This repository publishes **connector metadata and usage documentation** — the descriptors an MCP-compatible client needs to connect to the remote Heiman Smart Home MCP service. It contains no server implementation.

This is a third-party integration, **not an official Heiman / WorkBuddy / CodeBuddy product**, and it has no endorsement or certification from those parties. Use of the "Heiman" / 海曼 marks has been authorized by the trademark holder; that authorization does not make this an official product, and the MIT License grants no trademark rights.

---

## 1. License and scope

Original content here is licensed under the **MIT License** (see `LICENSE`): `connector-meta.json`, `mcp.json`, `server.json`, `skills/**/SKILL.md`, `README.md`, `i18n/README.*.md`, `NOTICE` and this statement.

Not covered by MIT: the artwork embedded in `icon.svg`, all trademarks and trade names, the upstream platform's API definitions / documentation / server code, and any private content not shipped with this bundle.

## 2. Using the service

- Endpoint: `https://spmcp.heimanhome.com/mcp` (MCP Streamable HTTP, OAuth 2.1 + PKCE).
- Five scopes: `device:read`, `device:control`, `scene:read`, `scene:execute`, `security:control`. Read-only access is granted by default; write scopes must be selected on the consent page.
- Upstream credentials and user accounts are obtained lawfully by the operator of the remote service; this repository supplies none of them.
- Write operations change physical devices. Use `dry_run=true` previews, `confirm=true` and least-privilege scopes. A successful response means a command was accepted, not that the device has executed it.

Client setup and the full tool list are in `README.md` and `skills/heimanhome-skill/SKILL.md`.

## 3. Data and privacy

This repository collects, stores and transmits no personal data. The remote service may process homes, rooms, device status, logs, temperature and humidity, alarms and household members — data that can contain personal information.

**The operator of that service is the data controller** and is responsible for notice and consent, data minimization, access control, retention, deletion responses and audit logging, as well as compliance with applicable law (including PIPL, DSL, CSL and GDPR where relevant). Cross-border transfers require a separate assessment.

## 4. No warranty

Provided "AS IS" without warranty of any kind, including availability, security, compatibility with the upstream platform or any particular MCP client version. Maintainers are not liable for device state changes, data loss or service interruption arising from the use of this bundle or the remote service. See `LICENSE`.

## 5. Security reporting

- Channel: `help@heimanhome.com`. Include the affected version, reproduction steps, impact and a suggested fix.
- **Never paste** real `client_secret`, access / refresh tokens, passwords, verification codes or internal domains into public issues, PRs or discussions.
- For vulnerabilities involving the upstream platform, also follow that platform's security process.
- A `SECURITY.md` based on this section is recommended for the public repository.

## 6. Contributing

- Submit only original or lawfully redistributable content, accepted under this bundle's `LICENSE`.
- **Do not submit** upstream internal API documents, upstream source code, real credentials, real user data, internal domains, private IP ranges or third-party brand assets.
- Keep `connector-meta.json`, `mcp.json`, `SKILL.md`, `README.md` and all `i18n/README.*.md` in sync; validate the JSON files.
- New or changed user-facing copy must be added to `connector-meta.json` → `i18n.locales` for all 8 locales (`en`, `de`, `fr`, `es`, `pt`, `ar`, `ru`, `zh`); `ar` is RTL.

## 7. Contact

- Repository: `https://github.com/heimanhome/heimanhome-mcp.git`
- Maintainer: HeimanHome · Copyright (c) 2026 HeimanHome & 肖力
- Support / security: `help@heimanhome.com`
- Changes are recorded in Release Notes.
