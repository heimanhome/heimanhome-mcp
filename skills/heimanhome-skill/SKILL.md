---
name: heimanhome-skill
description: Query and control Heiman smart home devices - lights, AC, curtains, temperature and humidity, device logs, homes and rooms, scene editing and automations, device capabilities, alarm arm/disarm, alerts, messages and read-only firmware queries. Supports 8 locales (en/de/fr/es/pt/ar/ru/zh).
version: 1.1.0
author: smart-home-mcp maintainers
---

# Heiman Smart Home Skill

Query and control Heiman smart home devices through MCP tools. Every tool returns structured JSON; on failure it returns a tool error with an explicit reason and a suggested next step.

This document is written in English, which is the authoritative documentation language. The interaction rules below are language-independent.

## Language support (8 locales)

Supported: `en` (English), `de` (German), `fr` (French), `es` (Spanish), `pt` (Portuguese), `ar` (Arabic, RTL), `ru` (Russian), `zh` (Chinese). Language affects phrasing and replies only — **it never changes tool selection, parameter values or permission checks**.

1. **Always reply in the language the user used.** If the user does not specify one, follow the current conversation language.
2. Keep tool names, parameter names and enum values (e.g. `action="set_brightness"`), `device_id`, `trace_id`, scope names and JSON fields verbatim — never translate them.
3. Preserve upstream device / home / room / scene names verbatim; write units according to the target language's convention (`26 °C`, `50 %`, `26 درجة`).
4. Server errors and `warnings` are returned in one of the 8 locales (see "Common errors"). Clients can select the locale via the `lang` query parameter, `X-Heiman-Locale` or `Accept-Language`; otherwise `MCP_DEFAULT_LOCALE` applies. If you still see raw Chinese text (a few static contract strings), **restate it in the user's language before replying** and keep IDs, field names and suggested next steps (such as `list_devices`) intact.
5. Format `ar` replies right-to-left; keep code blocks, tool calls and JSON LTR and unchanged.
6. The marketplace listing is rendered by the platform from its Chinese/English fields, with other locales falling back to English. The single source of truth for all 8 locales is `connector-meta.json` → `i18n.locales`; this Skill governs the interaction layer.
7. MCP `tools/list` is a stateless static list; tool descriptions do not switch language, so language adaptation is the responsibility of the rules in this Skill.

### Utterance quick reference

| Locale | Dim the light | Ask temperature | Arm the alarm |
| --- | --- | --- | --- |
| `en` | Set the living room light to 50% | What's the temperature in the living room? | I'm leaving, arm the alarm |
| `de` | Wohnzimmerlicht auf 50 % dimmen | Wie warm ist es im Wohnzimmer? | Ich gehe, scharf schalten |
| `fr` | Règle la lumière du salon à 50 % | Quelle température fait-il dans le salon ? | Je pars, active l'alarme |
| `es` | Pon la luz del salón al 50 % | ¿Qué temperatura hay en el salón? | Me voy, activa la alarma |
| `pt` | Ajuste a luz da sala para 50% | Qual a temperatura da sala? | Vou sair, ative o alarme |
| `ar` | اضبط إضاءة غرفة المعيشة على ٥٠٪ | ما درجة حرارة غرفة المعيشة؟ | سأخرج، فعّل نظام الإنذار |
| `ru` | Установи свет в гостиной на 50 % | Какая температура в гостиной? | Я ухожу, поставь на охрану |
| `zh` | 把客厅主灯调到 50% 亮度 | 客厅现在多少度？ | 出门了，帮我布防 |

## Authorization scope

On first connect, the browser opens the authorization login page; the user signs in with a smart home account and selects the permissions to grant.
**When no scope is specified, only read-only scopes are granted**; write scopes must be ticked explicitly on the consent page. Tokens are held and injected by the WorkBuddy cloud.

| Scope | Purpose |
| --- | --- |
| `device:read` | Devices, status, climate, logs, homes and rooms, capabilities, security status, alerts and messages |
| `device:control` | Lights / AC / curtains / generic function calls / natural-language control |
| `scene:read` | Scenes, scene logs, automation rules |
| `scene:execute` | Run scenes, enable/disable scenes and automation rules |
| `security:control` | Arm / disarm / acknowledge alarms (also requires `confirm=true` inside the tool) |

When authorization expires, tools report that re-authorization is needed; a missing scope produces an explicit "scope not granted" message; rate limiting returns a message with the number of seconds to wait. Call `whoami` at any time to see the current identity and scopes.

## Server-side security gate (do not try to work around)

Every tool call passes a single server-side guard before it can reach the upstream API:

1. **Sensitive operations** are classified in one policy table. Arm/disarm, alarm acknowledgement, scene create/update/delete and natural-language scene creation are *critical*: they need the write scope **and** `confirm=true` (`dry_run=true` previews are exempt). A tool that is not in the table fails server startup, so no tool can silently skip permission checks.
2. **Identity and scopes** are re-checked on every call; on public HTTPS deployments any write operation additionally requires an authenticated OAuth identity.
3. **Input bounds**: IDs/enums must be `A-Za-z0-9_.@:+-` only, 1–128 characters, with no `..`, slashes or percent-encoding. Free text is length-bounded and filtered for control characters and prompt-injection markers. Nested payloads are capped at 32 keys / 50 array items / 4 levels (`ca_payload` uses a looser tier with a 200 KB cap), keys must be `[\w.-]`. Brightness/position 0–100, AC 16–32 °C, `page_size` 1–200, `start_time <= end_time`.
4. **Frequency / anomaly**: per-scope sliding windows, a per-second burst limit and a temporary block after repeated denials. Request bodies are limited by actual bytes received, including chunked uploads.
5. **Audit**: all write operations and all denials are logged as redacted structured events; error messages never carry tokens or credentials.
6. **Encryption**: OAuth state and tokens are sealed at rest with AES-256-GCM; credentials are only sent over HTTPS; protected endpoints use HSTS / `no-store`.

On a violation the server refuses execution **before** any upstream call and returns a localized error with the reason, the allowed range and the next step. **Do not retry the identical call and do not look for an alternative tool or a crafted parameter** — fix the scope, the parameter or the missing confirmation. These limits do not change normal parameters or results: `dry_run`, `idempotent_key`, `trace_id` and all read-only flows behave exactly as described below.

## Operating principles

1. **Look before you control**: call `list_devices` first to obtain `device_id`.
2. **Discover capabilities first**: if the device's `functionId` values are unknown, call `get_device_capabilities(device_id)`; the server caches the result and auto-corrects parameters for later control tools.
3. **Distinguish dispatch from execution**: control tools return `accepted` (upstream accepted the command) and `executed` (device confirmed it). If `executed` is `null`, verify with `get_command_status(trace_id)` or `get_device_status(device_id)` — **do not resend just because confirmation is missing**.
4. **Use preview and idempotency**: preview high-impact operations with `dry_run=true`; on retry pass the same `idempotent_key` so the server replays the first result.
5. **Security needs two-step confirmation**: `set_guard_mode` / `confirm_guard_alarm` require an explicit `confirm=true` and the `security:control` scope.
6. Brightness / curtain position is 0–100, AC temperature 16–32 °C; out-of-range values return a validation error.
7. A nonexistent device ID returns "device not found" with a hint to call `list_devices` first.

## Available tools (42)

### Devices and logs

| Tool | Description | Key parameters |
| --- | --- | --- |
| `list_devices` | Device list + online status + latest properties | `home_id`, `room_id`, `keyword`, `online_only`, `include_properties`, `page`, `page_size` |
| `get_device_status` | Single-device status and latest properties | `device_id` |
| `get_device_latest_properties` | Read latest properties by identifier | `device_id`, `property_identifiers` |
| `get_device_logs` | Property / event / online-offline logs | `device_id`, `data_type`, `start_time`, `end_time`, `page`, `page_size` |
| `get_device_capabilities` | **Discover real readable/writable properties and functionIds** | `device_id`, `include_panel` |
| `whoami` | Current authorized identity and scopes | none |

### Device control

| Tool | Description | Shared parameters |
| --- | --- | --- |
| `control_light` | On/off and brightness | `device_id`, `action`, `brightness`, `function_id`, `property_identifier` |
| `control_ac` | On/off, temperature, mode | `device_id`, `action`, `temperature`, `mode` |
| `control_curtain` | Open / close / stop | `device_id`, `action`, `position` |
| `invoke_device_function` | Generic function call (escape hatch) | `device_id`, `function_id`, `parameters` |
| `control_device_by_text` | Natural-language control | `text`, `home_id`, `conversation_id` |
| `get_command_status` | Query command accepted/executed status | `trace_id` |

> All control tools support `dry_run` (preview only) and `idempotent_key`. Results contain `accepted` / `executed` / `trace_id` / `warnings`.

### Temperature and humidity

| Tool | Description | Key parameters |
| --- | --- | --- |
| `get_temperature_humidity` | Current temperature (°C) and humidity (%RH) | `device_id`, `home_id`, `room_id` |
| `get_temperature_humidity_aggregate` | Overall + per-bucket `count/min/max/avg/median/stddev/first/latest/range/delta`; optional hourly or daily trend in `buckets[]` | `device_id`, `home_id`, `room_id`, `start_time`, `end_time`, `page_size`, `bucket` (`none`/`hour`/`day`), `tz_offset_hours` (default 8 = UTC+8), `max_buckets` (default 168) |

> For trends pass `bucket="hour"` (e.g. last 24h) or `bucket="day"`. Buckets are aligned to `tz_offset_hours`, contain only hours/days with samples, are sorted ascending, and each carries `bucket_start` / `bucket_end` (Unix seconds), `label`, `samples`, `devices` and the same stats for temperature / humidity. `bucket="none"` returns overall stats only. `buckets_truncated=true` means older buckets were dropped by `max_buckets`; `unbucketed_samples` counts samples without a timestamp. If `truncated=true` or `buckets_truncated=true`, state that the trend is incomplete.

### Homes and rooms

`list_homes`, `get_home_detail(home_id)`, `list_rooms(home_id)`, `get_home_overview(home_id, room_id, category)`.

### Scenes and automations

| Tool | Description | Key parameters |
| --- | --- | --- |
| `list_scenes` | Manual / automatic scenes | `home_id`, `group_id` (0 manual / 1 automatic), `page`, `page_size` |
| `execute_scene` | Trigger a scene now | `scene_id` |
| `switch_scene` | Enable / disable a scene | `scene_id`, `enable` |
| `get_scene_logs` | Scene execution logs | `home_id`, `result`, `start_time`, `end_time` |
| `list_automation_rules` | Automation rules | `home_id`, `trigger_type` (1 scheduled / 2 device-triggered) |
| `switch_automation_rule` | Enable / disable an automation rule | `rule_id`, `enable` |

### Scene editing (write; requires `confirm=true`)

| Tool | Description | Key parameters |
| --- | --- | --- |
| `create_scene` | Create a manual/automatic scene | `home_id`, `name`, `group_id` (0 manual / 1 automatic), `description`, `actions`, `triggers`, `confirm`, `dry_run` |
| `update_scene` | Update a scene (validates the ID exists first) | `scene_id`, `home_id`, `name`, `description`, `actions`, `triggers`, `enable`, `confirm`, `dry_run` |
| `delete_scene` | Delete a scene (irreversible) | `scene_id`, `home_id`, `confirm`, `dry_run` |
| `list_scene_capabilities` | List TCA capabilities usable in actions/triggers | `home_id` |

> Before creating or editing a scene, call `list_scene_capabilities(home_id)` for valid capability identifiers. Write operations require `scene:execute` + `confirm=true`; preview with `dry_run=true` first.

### Natural-language scene creation (CA model)

| Tool | Description | Key parameters |
| --- | --- | --- |
| `create_scene_from_text` | Parse one natural-language sentence into a full CA scene payload (`caConditions` / `actions` / `mode` / `sceneType="CA"`) | `text`, `home_id`, `name`, `mode`, `dry_run` (default **true**), `confirm` |
| `get_scene_device_abilities` | List real TCA identifiers for one device | `device_id`, `flow_type` (`condition` / `action`) |
| `create_scene` (extended) | Submit an edited CA body | `ca_payload` = the `payload` returned by `create_scene_from_text`, plus `confirm=true` |

**Mandatory two-step flow** — never skip the preview:

1. Call `create_scene_from_text(text, home_id)` with the default `dry_run=true`.
2. Read `description`, `payload`, `unresolved` and `warnings`. Show the user exactly which conditions and actions were inferred.
3. If `unresolved` is non-empty or `success=false`, **do not submit** — ask the user for an unambiguous device name or rephrase.
4. Only after the user confirms, call again with `dry_run=false, confirm=true`.

Supported phrasing: `每天 23:00` / `工作日早上 8 点半` / `周末 9:00` (cron), `18:00 到 23:00` (effective time range), `温度高于 28 度` / `湿度低于 40%` (property condition with `> >= == < <=`), `打开|关闭 客厅灯`, `把客厅灯调到 50%`, `空调设为 26 度`, `延时 30 秒后`, `通知我…`, `执行回家模式`, `且|同时` (AND) / `或|任一` (OR).
A sentence without any condition creates a manual scene (`groupId=0`); with conditions it creates an automation (`groupId=1`).
Real `propertyName` values come from TCA. If TCA is unavailable, the parser falls back to defaults and always reports it in `warnings` — mention that to the user before creating.

### Product type catalog

| Tool | Description | Key parameters |
| --- | --- | --- |
| `get_product_catalog` | Look up real thing-model identifiers for a product **category** (not a product ID) | `category`, `device_name` (auto-infers category), `role`, `access` (`writable` / `readable`) |

The catalog is generated from the company's product table and contains **no product IDs** — only
category names, property/function/event identifiers, read/write permissions and frequencies
(235 products / 38 categories / 17 semantic roles). Examples of real identifiers:

| Category | Switch | Brightness / position | Read-only |
| --- | --- | --- | --- |
| 灯 (light) | `LightSwitch` | `Brightness`, `ColorTemperature` | `RSSI`, `BatteryPercentage` |
| 入墙开关 (wall switch) | `PowerSwitch_1` / `_2` / `_3` | — | `CurrentTemperature` |
| 插座 (socket) | `PowerSwitch` | — | `RSSI` |
| 窗帘 (curtain) | — | `CurtainPosition` | `BatteryPercentage` |
| 温湿度采集单元 | — | — | `CurrentTemperature`, `CurrentHumidity` |
| 烟雾/燃气报警器 | — | — | `SmokeSensorState`, `GasSensorState` |

Before calling `invoke_device_function` or hand-writing a scene `propertyName` for an unfamiliar
product type, call `get_product_catalog(device_name=..., role=...)`. Upstream TCA results always
take precedence; the catalog is the fallback, never a replacement for live capability discovery.

### Read-only extensions

| Tool | Description | Key parameters |
| --- | --- | --- |
| `list_home_members` | Household members, roles and invitation status | `home_id` |
| `list_shared_devices` | Shared device list | `page`, `page_size` |
| `list_pending_firmware_updates` | Devices with pending updates and versions (**does not trigger OTA**) | `home_id` |
| `get_device_firmware` | Current / latest firmware version for one device | `device_id` |

### Alarm arm / disarm

| Tool | Description | Key parameters |
| --- | --- | --- |
| `list_guard_modes` | Guard modes and the active one | `home_id` |
| `get_guard_status` | Whether the alarm is armed and which mode is active | `home_id` |
| `set_guard_mode` | **Arm / disarm** | `home_id`, `mode_id`, `arm`, `confirm` (must be true), `dry_run` |
| `list_guard_logs` | Arm/disarm operation logs | `home_id`, `page`, `page_size` |
| `confirm_guard_alarm` | Acknowledge (silence) an alarm | `alarm_id`, `confirm` (must be true) |

### Alerts and messages

| Tool | Description | Key parameters |
| --- | --- | --- |
| `list_alarms` | Alarm records | `home_id`, `unconfirmed_only`, `page`, `page_size` |
| `list_messages` | Message-center pushes and system notices | `page`, `page_size` |

## Resources and Prompts

**Read-only context (Resources)**: `resource://homes` (home list), `resource://homes/{home_id}/overview` (summary statistics), `resource://homes/{home_id}/devices` (device list), `resource://homes/{home_id}/security` (arm status + unacknowledged alarms).
Read a snapshot before answering "how is everything at home?" — it saves round trips compared with calling tools one by one.

**Workflow prompts**: `leaving_home(home_id)`, `bedtime_check(home_id)`, `daily_home_report(home_id, hours)`.
They return instruction text only and do not execute tools; `bedtime_check` and `daily_home_report` explicitly require read-only behaviour.

## Example dialogues

```text
User: The living room is a bit dark
→ list_devices(keyword="living room")
→ get_device_capabilities(device_id="dev-light-1")      # optional, confirm functionId
→ control_light(device_id="dev-light-1", action="set_brightness",
                brightness=70, idempotent_key="livingroom-dim-1")
→ get_command_status(trace_id=<trace_id from previous step>)   # confirm real execution

User: I'm leaving
→ control_light(device_id="dev-light-1", action="off")
→ control_curtain(device_id="dev-curtain-1", action="close")
→ list_guard_modes(home_id="home-1")
→ set_guard_mode(home_id="home-1", mode_id="guard-away", arm=true, confirm=true)
→ get_guard_status(home_id="home-1")                    # verify

User: Anything unusual at home today?
→ list_alarms(unconfirmed_only=true)
→ list_messages(page_size=20)

User: How were temperature and humidity over the last day?
→ get_temperature_humidity_aggregate(start_time=<now-86400>, end_time=<now>,
                                      bucket="hour", tz_offset_hours=8, page_size=100)
→ use temperature/humidity for the overall stats and buckets[] for the hourly trend;
  mention it explicitly if truncated / buckets_truncated is true

User: Create a scene that turns the lights off at 11 pm
→ create_scene_from_text(text="每天 23:00 关闭客厅灯", home_id="home-1")   # default dry_run
→ read description / payload / unresolved / warnings, show the inferred timer + action
→ after user confirmation:
  create_scene_from_text(text="每天 23:00 关闭客厅灯", home_id="home-1",
                         dry_run=false, confirm=true)
→ if the user wants a tweak, edit payload and submit with
  create_scene(home_id="home-1", name="...", ca_payload=payload, confirm=true)
```

Non-English requests use the same tool chain; only the reply language changes:

```text
User (de): Wohnzimmerlicht auf 50 % dimmen
→ list_devices(keyword="Wohnzimmer")
→ control_light(device_id="dev-light-1", action="set_brightness", brightness=50)
→ Reply in German: "Helligkeit des Wohnzimmerlichts auf 50 % gesetzt …"

User (ar): ما درجة حرارة غرفة المعيشة؟
→ get_temperature_humidity(home_id="home-1")
→ Reply in Arabic, RTL; keep numbers and units unchanged
```

## Authorization login methods

The login page offers only **Password** and **Third-party**, both writing to the same HttpOnly session. For social login the third-party authorization code must be obtained by the MCP client first; the server only exchanges it.

**Security policy (do not attempt to work around):** verification-code login, user registration, sending/obtaining verification codes, password reset, and direct device add/bind/provisioning have been removed. The corresponding upstream paths are hard-blocked server-side (`FORBIDDEN_UPSTREAM_PATHS` and the `/api-notice/*` prefix). If a task requires any of these, tell the user it must be done in the official Heiman app — do not retry, do not look for an alternative tool, and do not pass a crafted upstream path.

## Common errors

The Chinese strings below are the **literal messages currently returned by the server** and are quoted as data, not documentation. Per rule 4 in "Language support", restate them in the user's language; keep tool names, field names and IDs verbatim.

| Error | Action |
| --- | --- |
| `设备不存在：device_id=...` | Call `list_devices` for a valid ID |
| `参数不正确：set_brightness 必须提供 brightness` | Add the required parameter and check its range |
| `当前授权不包含 device:control 权限` | Disconnect, re-authorize and tick the corresponding scope |
| `LOCAL_SCOPES 未授予 X 权限` | In stdio mode, configure `.env` and restart |
| `调用过于频繁：... 请约 N 秒后重试` | Wait or batch calls; do not retry immediately |
| `检测到秒级突发调用…已拒绝执行` | Batch the calls or wait one second; do not retry immediately |
| `检测到异常访问…已临时暂停约 N 秒` | Too many consecutive denials; verify scopes and parameters before trying again |
| `参数 X 含非法字符…已拒绝执行` | Use an ID returned by `list_*`; do not craft paths or percent-encoded values |
| `参数 X 结构不合法…已拒绝执行` | Split oversized `parameters` / `actions` / `triggers` / `ca_payload` into smaller calls |
| `需要已认证身份…已拒绝执行` | Reconnect and re-authorize in the MCP client before write operations |
| `上游地址必须为 HTTPS…已拒绝执行` | Server-side misconfiguration; contact the administrator, do not retry |
| `布撤防会改变家庭安防状态…confirm=true` | Ask the user explicitly, then call with `confirm=true` |
| `授权已失效` | Reconnect / re-authorize; `whoami` warnings show why the upstream refresh failed |
| Temperature result `success=false` | Check that the sensor is online and reporting, or configure `property_identifiers.temperature/humidity` |
| `executed=null` | Upstream did not confirm execution; verify with `get_command_status` / `get_device_status`, do not resend |

## Notes

- Model `functionId` values are product-specific; the static fallback mapping can be overridden server-side via `HEIMAN_CAPABILITIES_FILE`.
- The upstream platform has no dedicated temperature/humidity time-series endpoint; aggregation and `hour`/`day` bucketing are computed locally from property-report logs, so bucketing adds no upstream requests.
- For control tools, `success` means the command was accepted — **not** that the device executed it.
- `dry_run=true` never occupies an `idempotent_key`: you may preview and then send the real command with the same key. A real command with a repeated key returns the first result instead of issuing a second command.
- Inputs and outputs are bounded. If you receive `payload_too_large`, `响应体 ... 超过上限`, `调用过于频繁` or a timeout, narrow `page_size` / the time window or split the work — **do not immediately retry the identical call**.
- `truncated=true` (samples not fully read) or `buckets_truncated=true` (older buckets dropped) means the data / trend is incomplete; state that explicitly in any summary you give the user.
