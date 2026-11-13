# Heiman Smart Home MCP — Nutzungsanleitung (Deutsch)

Mit einem KI-Assistenten Smart-Home-Geräte in natürlicher Sprache abfragen und steuern: Licht, Klimaanlage, Vorhänge, Temperatur und Luftfeuchtigkeit, Geräteprotokolle, Räume, Szenen und Alarmanlage.

| | |
| --- | --- |
| Endpunkt | `https://spmcp.heimanhome.com/mcp` |
| Transport | MCP Streamable HTTP |
| Autorisierung | OAuth 2.1 mit PKCE (Anmeldung mit dem Smart-Home-Konto) |
| Repository | `https://github.com/heimanhome/heimanhome-mcp.git` |
| Support | `help@heimanhome.com` |

> Die englische [`../README.md`](../README.md) ist maßgeblich; diese Übersetzung dient nur der Orientierung.

## 1. Verbinden und autorisieren

1. Endpunkt in der unten gezeigten Konfiguration in den MCP-Client eintragen.
2. Der Client öffnet die Autorisierungsseite; mit dem Smart-Home-Konto anmelden.
3. Berechtigungen prüfen: Standardmäßig nur Lesezugriff; Schreibrechte nur bei Bedarf aktivieren.
4. Bestätigen. Der Client speichert das Token; die Zugangsdaten gelangen nicht in dieses Repository.

## 2. Client-Konfiguration

**Allgemeines `mcp.json`** (Claude Desktop, Cursor, Cline, Windsurf, Zed, VS Code + GitHub Copilot, MCP Inspector):

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

**Claude Code**:

```bash
claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp
```

**Clients nur mit stdio** — Brücke mit `mcp-remote`:

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

**Gehostete Clients** (ChatGPT, Claude.ai, Copilot Studio, Gemini CLI, WorkBuddy / CodeBuddy): in der jeweiligen Oberfläche „Remote-MCP-Server hinzufügen" dieselbe URL eintragen.

## 3. Scopes und Schreibvorgänge

| Scope | Erlaubt |
| --- | --- |
| `device:read` | Gerätelisten, Status, Fähigkeiten, Firmware, Protokolle, Sensoren |
| `device:control` | Ein/Aus, Helligkeit, Zieltemperatur, Vorhänge |
| `scene:read` | Szenen, Räume, Häuser, Automationen |
| `scene:execute` | Szenen / Automationen ausführen, anlegen, ändern, löschen |
| `security:control` | Scharf-/Unscharfschalten, Alarmbestätigung |

- Ohne explizite Anfrage wird nur Lesezugriff erteilt.
- Risikoreiche Aktionen benötigen `confirm=true`; vorher mit `dry_run=true` prüfen.
- Bei Wiederholungen `idempotent_key` verwenden und mit `get_command_status(trace_id)` prüfen. Eine erfolgreiche Antwort bedeutet nur, dass der Befehl **angenommen** wurde.
- `whoami` zeigt Identität und Scopes. Bei Ablehnung Scope / Parameter / Bestätigung korrigieren, nicht identisch wiederholen.

## 4. Beispielanfragen

```
Stelle die Helligkeit des Wohnzimmerlichts auf 50 %
Wie hoch sind Temperatur und Luftfeuchtigkeit im Wohnzimmer?
Schalte die Klimaanlage im Wohnzimmer ein und stelle sie auf 26 °C
Schließe die Vorhänge und starte die Szene „Zuhause"
Zeige die Durchschnittstemperatur und -luftfeuchtigkeit der letzten 24 Stunden
```

Der Assistent antwortet in der Sprache der Anfrage; alle 8 Sprachen stehen in `connector-meta.json` → `i18n.locales`.

## 5. Häufige Probleme

| Symptom | Lösung |
| --- | --- |
| `401` / Autorisierung abgelaufen | Verbindung trennen und erneut autorisieren |
| „scope not granted" | Erneut autorisieren und den benötigten Scope aktivieren |
| Befehl angenommen, Gerät unverändert | `get_command_status(trace_id)` prüfen, Gerät braucht Zeit |
| Zu viele Anfragen | Kurz warten, nicht identisch wiederholen |
| Keine Verbindung | Client muss Streamable HTTP + OAuth unterstützen; Endpunkt per HTTPS erreichbar |

## 6. Sicherheit und Datenschutz

- Steuerungsbefehle verändern reale Geräte: `dry_run`-Vorschau prüfen und Schreibrechte minimieren.
- Tokens und Zugangsdaten bleiben auf dem Server. Niemals Tokens oder Passwörter in öffentliche Issues posten; Sicherheitsmeldungen an `help@heimanhome.com`.
- Der Betreiber des entfernten Dienstes ist für die Daten verantwortlich — siehe `OPEN-SOURCE-STATEMENT.md`.

## Lizenz und weitere Dokumente

MIT (siehe `LICENSE`); Eigentum und Marken in `NOTICE`; vollständige Werkzeugliste in `skills/heimanhome-skill/SKILL.md`.
