# Heiman Smart Home MCP Server — Connector & Registry metadata

This directory is the **public distribution bundle** for a standard, vendor-neutral MCP server. It targets any MCP-compliant client — Claude Desktop / Claude Code, ChatGPT and the OpenAI Responses API, VS Code + GitHub Copilot, Cursor, Cline, Zed, Windsurf, Gemini CLI, `mcp-remote`, MCP Inspector — the official **MCP Registry**, and, as one additional target among several, the WorkBuddy / CodeBuddy Connector marketplace.

The server speaks MCP `2026-07-28` plus the earlier `initialize`-era revisions, Streamable HTTP and stdio, and OAuth 2.1 with PKCE. **It is not WorkBuddy-only**: `connector-meta.json` is merely one host-specific descriptor; `server.json` is the vendor-neutral Registry listing.

> **This directory contains connector / registry metadata only, not the server implementation.**
> This is a third-party integration. It is **not an official Heiman product, nor an official WorkBuddy / CodeBuddy / Anthropic / OpenAI / Microsoft product**, and is endorsed by none of them. See `OPEN-SOURCE-STATEMENT.md`.

**Documentation language:** English is authoritative. Translations are provided for reference; if a translation conflicts with the English text, the English text prevails. Legal texts (`LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`) are English-only and authoritative.

---

## Languages

| Language | Code | Document |
| --- | --- | --- |
| English (authoritative) | `en` | [`README.md`](README.md) |
| Deutsch | `de` | [`i18n/README.de.md`](i18n/README.de.md) |
| Français | `fr` | [`i18n/README.fr.md`](i18n/README.fr.md) |
| Español | `es` | [`i18n/README.es.md`](i18n/README.es.md) |
| Português | `pt` | [`i18n/README.pt.md`](i18n/README.pt.md) |
| العربية (RTL) | `ar` | [`i18n/README.ar.md`](i18n/README.ar.md) |
| Русский | `ru` | [`i18n/README.ru.md`](i18n/README.ru.md) |
| 简体中文 | `zh` | [`i18n/README.zh.md`](i18n/README.zh.md) |

The locale codes match `connector-meta.json` → `i18n.supported_locales`. `ar` is right-to-left; code blocks, tool names and JSON stay LTR. The single source of truth for localized listing copy (name / description / example utterances in all 8 languages) is `connector-meta.json` → `i18n.locales`; the translated READMEs quote it, not the other way round.

> The WorkBuddy marketplace currently consumes only the top-level `*_zh` / `*_en` fields and falls back to the English `name` / `description` for other locales. The `i18n` object is a project-owned copy source shared by the Skill, the login page and packaging scripts. Confirm with the platform before relying on additional locale fields.

---

## Contents

| Path | Purpose |
| --- | --- |
| `server.json` | **MCP Registry** publishing manifest (`remotes` / `streamable-http`) — vendor-neutral |
| `mcp.json` | Generic MCP client connection config (`streamableHttp`) |
| `connector-meta.json` | WorkBuddy / CodeBuddy descriptor (one optional publishing target, not a requirement for other clients) |
| `icon.svg` | Connector icon (third-party artwork; not covered by MIT until rights are confirmed) |
| `skills/heimanhome-skill/SKILL.md` | Skill instructions for the agent: 39 tools, 5 scopes, Resources / Prompts, multilingual rules, example dialogues |
| `README.md` | This file (English, authoritative) |
| `i18n/README.<locale>.md` | Translated READMEs for the 7 additional locales |
| `LICENSE` / `NOTICE` / `OPEN-SOURCE-STATEMENT.md` | MIT license, trademark / ownership notice, full open-source statement |

---

## Before you use it

The bundle points to `https://spmcp.heimanhome.com/mcp` and is published at `https://github.com/heimanhome/heimanhome-mcp.git`. Connecting requires a publicly reachable HTTPS deployment, real upstream credentials, and a completed `--check-release` run. If you deploy your own server, replace the endpoint URL in `server.json`, `mcp.json` and `connector-meta.json`. See the next section.

---

## Publishing as a public MCP server (not WorkBuddy-only)

### 1. Deployment prerequisites

| Requirement | Setting |
| --- | --- |
| Public HTTPS origin (TLS terminated at a reverse proxy) | `MCP_PUBLIC_BASE_URL=https://spmcp.heimanhome.com` |
| Real upstream account, never the mock backend | `HEIMAN_MOCK=0`, `HEIMAN_CLIENT_ID`, `HEIMAN_CLIENT_SECRET` |
| Multi-instance-safe encrypted state | `STORE_BACKEND=sqlite|redis` + `STORE_ENCRYPTION_KEYS` |
| Tightened client allowlist | `OAUTH_ALLOWED_REDIRECT_HOSTS` (default covers mainstream clients; avoid `*`) |
| Protocol | MCP `2026-07-28` preferred, `initialize`-era revisions accepted; Streamable HTTP `/mcp`, stdio for local use |

`MCP_PUBLIC_BASE_URL` is the single source of truth for the MCP endpoint, the OAuth issuer and the RFC 8707 `resource` audience. Do not edit per-host URLs by hand.

### 2. Render all publishing artifacts

```bash
export MCP_PUBLIC_BASE_URL=https://spmcp.heimanhome.com
python -m smart_home_mcp --render-publish
```

This rewrites the remote URL in `server.json`, `mcp.json` and `connector-meta.json` in one pass and is idempotent. `server.json` uses the namespace `io.github.heimanhome/heimanhome-mcp`; only the owner of that namespace can publish it (the Registry verifies namespace ownership; it does **not** verify the remote URL).

### 3. Publish to the MCP Registry

```bash
mcp-publisher validate          # validates server.json against the official schema
mcp-publisher login github      # or DNS / HTTP auth for a custom-domain namespace
mcp-publisher publish
```

`server.json` is a remote-only listing (`remotes[].type = streamable-http`); no package entry is required. Keep `description` ≤ 100 characters and `version` in sync with `pyproject.toml`.

### 4. Connect mainstream MCP clients

`mcp.json` is the generic `mcpServers` form used by Claude Desktop, Cursor, Cline, Windsurf, Zed, VS Code + GitHub Copilot and MCP Inspector:

```json
{ "mcpServers": { "heiman-smart-home": { "type": "streamableHttp", "url": "https://spmcp.heimanhome.com/mcp" } } }
```

Clients that only speak stdio can bridge to the remote endpoint with `mcp-remote`:

```json
{ "mcpServers": { "heiman-smart-home": {
  "command": "npx", "args": ["-y", "mcp-remote", "https://spmcp.heimanhome.com/mcp"] } } }
```

Claude Code: `claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp`.
Hosted runtimes (ChatGPT connectors / apps, Claude.ai connectors, Copilot Studio, OpenAI Responses API `mcp` tool, Gemini CLI) use their own connector registration UI or API with the same URL; they do not consume `connector-meta.json`.

### 5. Client registration (three supported tracks)

The server declares `client_id_metadata_document_supported: true` and accepts all three mechanisms, so no client is forced into a WorkBuddy-specific flow:

| Track | How | Notes |
| --- | --- | --- |
| **Client ID Metadata Documents** (preferred, `2026-07-28`) | client uses an HTTPS URL with a path as `client_id` | Server fetches the JSON document, enforces exact `client_id` match, validates `redirect_uris`, caches it, and rejects private-network targets (SSRF guard) |
| Pre-registration | operator provisions a `client_id` out of band | For clients with an existing relationship |
| DCR (RFC 7591) | `POST /oauth/register` | Deprecated but retained for older clients; supports `application_type` (SEP-837) |

PKCE S256, `state`, one-time consent, CSRF protection, RFC 9207 `iss` and incremental scopes are enforced for every track. `resource` (RFC 8707) comparisons tolerate omitted values, issuer-root forms and trailing slashes while tokens are always minted with the canonical `/mcp` audience.

### 6. Release gate

```bash
python -m smart_home_mcp --check-release
```

Exits non-zero while any blocker remains (local/Mock credentials, single-instance JSON store, missing encryption keys, placeholder URLs, Registry namespace/repository, description length, version drift, mismatched artifact URLs). Wire it into CI before `mcp-publisher publish`. `python -m pytest -q tests/test_public_compat.py` covers the cross-client registration, callback, `resource` and publishing checks.

If brand authorization has not been obtained, replace the names with a neutral project name, swap `icon.svg` for a self-created icon, and update both JSON files, `SKILL.md` and all translated READMEs.

---

## Scopes

When no scope is requested the server should grant read-only access (`device:read`, `scene:read`). Write scopes (`device:control`, `scene:execute`, `security:control`) require the user to tick them explicitly on the consent page. Arm/disarm and scene editing additionally require `confirm=true`; high-impact actions should be previewed with `dry_run=true` first.

## Server-side security policy

The MCP server enforces these restrictions on **every** tool call; the client and Skill cannot bypass them:

| Area | Restriction |
| --- | --- |
| Sensitive operations | A single policy table classifies every tool as read / write / critical. Critical operations (arm/disarm, alarm acknowledgement, scene create/update/delete, natural-language scene creation) require their write scope **and** `confirm=true`; `dry_run=true` previews are exempt. A tool missing from the table fails server startup (fail-closed). |
| Authentication & permissions | `/mcp` requires an OAuth bearer token (401 otherwise). Each tool is checked against the granted scopes; public HTTPS deployments additionally require an authenticated identity for any write operation. `whoami` shows the current identity and scopes. |
| Input validation | IDs and enums must match `^[A-Za-z0-9_.@:+-]{1,128}$` (no `..`, slashes, percent-encoding or control characters). Free text is length-bounded and filtered for control characters / prompt-injection markers. Structured payloads are capped by key count, array length, depth and node count, with a safe key pattern. Numeric and time ranges are re-checked server-side. Upstream paths are restricted to normalized `/api-*` static paths plus a hard-blocked capability list. |
| Rate & anomaly limits | Per-scope/identity sliding windows, a per-second burst limit, and a temporary block after repeated denials. Request bodies are counted by actual bytes (chunked uploads included). |
| Audit logging | Every write operation and every denial is written as a redacted structured audit event (JSONL file, mode 0600) plus a metric. Exception text returned to the client is scrubbed of tokens and credentials. |
| Encryption | OAuth state, command journal and rate-limit windows are sealed with AES-256-GCM (`STORE_ENCRYPTION_KEYS`); credentials/tokens are only sent over HTTPS, and non-local plain-HTTP deployments are refused at startup. All protected endpoints send HSTS / `nosniff` / `no-store`. |
| Violations | Execution is refused **before** any upstream call with an explicit, localized error stating the reason, the allowed range and the next step. Do not retry the identical call; fix the scope, parameter or confirmation instead. |

These controls do not change normal tool parameters or results: read-only calls, `dry_run` previews, `idempotent_key` retries, stdio/local mode and all existing dialogues continue to work.

---

## Packaging boundary

| Target | Files to include |
| --- | --- |
| **GitHub public repository** | Everything in this directory, including `i18n/`, `LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` and all READMEs |
| **MCP Registry (public, vendor-neutral)** | `server.json` only; validate with `mcp-publisher validate`, publish after `--check-release` passes |
| **Generic MCP clients** | `mcp.json` (or the equivalent host-specific config); no WorkBuddy-specific files needed |
| **WorkBuddy Connector marketplace submission** (optional target) | Only the metadata files required by the platform spec: `connector-meta.json`, `mcp.json`, `icon.svg`, `skills/**/SKILL.md`. Do **not** include the legal files, `README*`, `i18n/` or `server.json` in the review package |

---

## Remediation status

Issues resolved in this revision:

| Item | Change |
| --- | --- |
| Documentation language | English is now authoritative; `README.md`, `NOTICE`, the open-source statement and `SKILL.md` were rewritten in English; Chinese moved to `i18n/README.zh.md` |
| Localization | 7 translated READMEs added; locale codes aligned with `i18n.supported_locales`; RTL handling documented for `ar` |
| Version drift | `connector-meta.json` and `SKILL.md` aligned to **1.1.0** |
| Author attribution | `SKILL.md` frontmatter no longer claims the brand owner as author |
| Internal source hints | The reference to the internal login-page source was removed from `SKILL.md` |
| Legal file naming | The former Chinese-named statement file became `OPEN-SOURCE-STATEMENT.md`; legal texts are English-only authoritative |
| Packaging | `i18n/` explicitly included for GitHub and excluded from marketplace submissions |
| Skill rules | Multilingual response rules, the 8-language utterance table and the language-independent tool chain are preserved in English |

Still requires a human decision before publishing (do not assume these are done):

- [ ] Trademark / brand authorization for "Heiman" / 海曼, or a neutral rename
- [ ] Rights to the embedded artwork in `icon.svg`, or replacement with a self-created icon
- [ ] Replace the remaining `<...>` placeholders in `LICENSE` and `NOTICE` (copyright holder, maintaining organization); the endpoint, contact mailbox and repository URL are already set
- [ ] Confirm with the platform whether the custom `i18n` extension object in `connector-meta.json` is acceptable for marketplace submission
- [ ] Confirm disclosure of WorkBuddy spec fields (`auth_mode`, `auth_type`, `minWorkbuddyVersion`)

---

## License and notices

Original content in this bundle is MIT licensed (see `LICENSE`). Trademarks, the `icon.svg` artwork and everything not shipped with this bundle are outside that license — see `NOTICE` and `OPEN-SOURCE-STATEMENT.md`.

Report security issues to `help@heimanhome.com`; never post credentials or vulnerability details in a public issue.
