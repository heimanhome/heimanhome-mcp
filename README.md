# Heiman Smart Home MCP Server

Use an AI assistant to query and control Heiman smart home devices in natural language — lights, air conditioning, curtains, temperature and humidity, device logs, rooms, scenes and security — through one remote MCP endpoint.

| | |
| --- | --- |
| Endpoint | `https://spmcp.heimanhome.com/mcp` |
| Transport | MCP Streamable HTTP |
| Authorization | OAuth 2.1 with PKCE (sign in with your smart home account) |
| Repository | `https://github.com/heimanhome/heimanhome-mcp.git` |
| Support | `help@heimanhome.com` |

This is a third-party integration, not an official Heiman / WorkBuddy / CodeBuddy / Anthropic / OpenAI / Microsoft product. Ownership and licensing boundaries are in `NOTICE` and `OPEN-SOURCE-STATEMENT.md`.

---

## What you can do

| Capability | Example request |
| --- | --- |
| Device status and control | "Set the living room light brightness to 50%" |
| Climate and sensors | "What is the current temperature and humidity in the living room?" |
| Appliances | "Turn on the living room AC and set it to 26 degrees" |
| Scenes | "Close the curtains and run the Home scene" |
| History and aggregates | "Show the average temperature and humidity of the last 24 hours" |
| Automations | "Create an automation that turns off the living room light every day at 11 pm" |
| Security | Arm / disarm, alarm acknowledgement, alarm logs |

The full tool list, parameters and dialogue examples are in [`skills/heimanhome-skill/SKILL.md`](skills/heimanhome-skill/SKILL.md).

---

## 1. Connect and authorize

1. Add `https://spmcp.heimanhome.com/mcp` to your MCP client using one of the configurations below.
2. Your client opens the authorization page. Sign in with your smart home account.
3. Review the requested scopes. Read-only access is granted by default; tick write scopes only when you intend to control devices.
4. Approve. The client stores the token; your upstream credentials never enter this repository.

Authorization uses OAuth 2.1 with PKCE. Client ID Metadata Documents, pre-registered clients and dynamic client registration are all accepted, so most MCP clients work without extra setup.

---

## 2. Client configuration

### Generic `mcp.json`

Claude Desktop, Cursor, Cline, Windsurf, Zed, VS Code + GitHub Copilot and MCP Inspector:

```json
{
  "mcpServers": {
    "heiman-smart-home": {
      "type": "streamableHttp",
      "url": "https://spmcp.heimanhome.com/mcp",
      "timeout": 30000
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp
```

### Clients that only support stdio

Bridge to the remote endpoint with `mcp-remote`:

```json
{
  "mcpServers": {
    "heiman-smart-home": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://spmcp.heimanhome.com/mcp"]
    }
  }
}
```

### Hosted clients

ChatGPT connectors / apps, Claude.ai connectors, Copilot Studio, the OpenAI Responses API `mcp` tool, Gemini CLI and the WorkBuddy / CodeBuddy Connector marketplace: register a new remote MCP server in the client's own UI and paste the same URL. No file from this repository is required for that registration.

---

## 3. Scopes and safe writes

| Scope | Allows |
| --- | --- |
| `device:read` | Device lists, status, capabilities, firmware, logs, sensor readings |
| `device:control` | Switching devices on/off, brightness, target temperature, curtains |
| `scene:read` | Scenes, rooms, homes, automation listings |
| `scene:execute` | Running, creating, updating and deleting scenes / automations |
| `security:control` | Arm / disarm, alarm acknowledgement |

- Without an explicit scope request the server grants read-only access.
- High-impact actions require `confirm=true`; preview them first with `dry_run=true`.
- When retrying a control command, pass `idempotent_key`, then check `get_command_status(trace_id)`. A successful response means the command was **accepted**, not necessarily executed by the device.
- `whoami` shows the current identity and granted scopes.
- If a call is refused, fix the scope / parameter / confirmation instead of retrying the identical call.

---

## 4. Example prompts

```
Set the living room light brightness to 50%
What is the current temperature and humidity in the living room?
Turn on the living room AC and set it to 26 degrees
Close the curtains and run the Home scene
Show the average temperature and humidity of the last 24 hours
Create an automation that turns off the living room light every day at 11 pm
When the temperature is above 28 degrees, turn on the AC and set it to 26 degrees
```

The assistant answers in the language you use. Example utterances in 8 languages are in `connector-meta.json` → `i18n.locales`.

---

## 5. Troubleshooting

| Symptom | What to do |
| --- | --- |
| `401` / authorization expired | Reconnect and authorize again in the client |
| "scope not granted" | Re-authorize and tick the required scope on the consent page |
| Command accepted but device unchanged | Check `get_command_status(trace_id)`; allow time for the device to report back |
| Too many requests | Wait before retrying; do not loop on the identical call |
| Cannot connect | Confirm the client supports Streamable HTTP + OAuth and the endpoint is reachable over HTTPS |

---

## 6. Privacy and safety

- Device control changes the physical world. Review `dry_run` previews, keep write scopes to the minimum and use two-step confirmation.
- Access tokens and upstream credentials stay on the server. Never paste tokens, passwords or verification codes into public issues; report security issues to `help@heimanhome.com`.
- The operator of the remote service is the data controller for home and device data — see `OPEN-SOURCE-STATEMENT.md`.

---

## Documentation languages

| Language | Document |
| --- | --- |
| English (authoritative) | [`README.md`](README.md) |
| Deutsch | [`i18n/README.de.md`](i18n/README.de.md) |
| Français | [`i18n/README.fr.md`](i18n/README.fr.md) |
| Español | [`i18n/README.es.md`](i18n/README.es.md) |
| Português | [`i18n/README.pt.md`](i18n/README.pt.md) |
| العربية (RTL) | [`i18n/README.ar.md`](i18n/README.ar.md) |
| Русский | [`i18n/README.ru.md`](i18n/README.ru.md) |
| 简体中文 | [`i18n/README.zh.md`](i18n/README.zh.md) |

English is authoritative; translations are convenience copies. Arabic is right-to-left, while code blocks, tool names and JSON stay LTR.

## License

MIT — see `LICENSE`. Ownership and trademark boundaries: `NOTICE`. Full statement: `OPEN-SOURCE-STATEMENT.md`.
