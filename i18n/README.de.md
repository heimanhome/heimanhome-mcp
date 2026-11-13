# Heiman Smart Home Connector — Deutsch

Ein **MCP-Connector-Metadatenpaket** für WorkBuddy / CodeBuddy-kompatible Clients. Ein KI-Assistent kann damit Smart-Home-Geräte in natürlicher Sprache abfragen und steuern: Licht, Klimaanlage, Vorhänge, Temperatur und Luftfeuchtigkeit, Geräteprotokolle, Häuser und Räume, Szenen, Gerätefähigkeiten, Alarmanlage, Meldungen sowie Firmware-Informationen (nur lesend).

> **Dieses Repository enthält ausschließlich Connector-Metadaten, keine Server-Implementierung.**
> Es handelt sich um eine Integration von Drittanbietern — **kein offizielles Produkt von Heiman, WorkBuddy oder CodeBuddy** — ohne Unterstützung oder Zertifizierung einer dieser Parteien. Siehe `OPEN-SOURCE-STATEMENT.md`.

**Sprache:** Englisch ist maßgeblich. Diese Übersetzung dient nur der Orientierung; bei Abweichungen gilt [`../README.md`](../README.md). Rechtstexte (`LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`) sind nur auf Englisch verbindlich.

## Sprachen

| Sprache | Code | Dokument |
| --- | --- | --- |
| English (maßgeblich) | `en` | [`../README.md`](../README.md) |
| Deutsch | `de` | `README.de.md` |
| Français | `fr` | [`README.fr.md`](README.fr.md) |
| Español | `es` | [`README.es.md`](README.es.md) |
| Português | `pt` | [`README.pt.md`](README.pt.md) |
| العربية (RTL) | `ar` | [`README.ar.md`](README.ar.md) |
| Русский | `ru` | [`README.ru.md`](README.ru.md) |
| 简体中文 | `zh` | [`README.zh.md`](README.zh.md) |

Die Codes entsprechen `connector-meta.json` → `i18n.supported_locales`. Die verbindliche Quelle für lokalisierte Texte ist `i18n.locales`.

## Paketinhalt

Pfade beziehen sich auf das Repository-Stammverzeichnis.

| Pfad | Zweck |
| --- | --- |
| `connector-meta.json` | Connector-Beschreibung, lokalisierte Texte, `auth_mode: server-side`, Scopes, OAuth-URL |
| `mcp.json` | MCP-Client-Verbindungskonfiguration (`streamableHttp`) |
| `icon.svg` | Icon (Grafik eines Drittanbieters; nicht unter MIT, solange die Rechte ungeklärt sind) |
| `skills/heimanhome-skill/SKILL.md` | Skill-Anweisungen: 39 Tools, 5 Scopes, mehrsprachige Regeln |
| `README.md`, `i18n/` | Englische Hauptdokumentation und Übersetzungen |
| `LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` | Lizenz, Marken- und Urheberhinweise, Open-Source-Erklärung |

## Vor der Nutzung

`https://smart-home-mcp.example.com/mcp` in `mcp.json` und `connector-meta.json` ist ein **Platzhalter**. Ohne Änderung verbindet sich das Paket mit nichts. Erforderlich:

1. Einen öffentlich per HTTPS erreichbaren MCP-Server bereitstellen (Streamable HTTP + OAuth, RFC 9728 / 8414 / 7591 / 7636 / 7009) mit den 39 Tools aus `SKILL.md`.
2. `example.com` in beiden JSON-Dateien durch die echte Adresse `https://<Domain>/mcp` ersetzen.
3. Die für `auth_mode: server-side` nötige Client-Version prüfen (`minWorkbuddyVersion: 4.24.0`).
4. `clientId` / `clientSecret` und die Integrationsfreigabe rechtmäßig von der Upstream-Plattform einholen; Zugangsdaten gehören **nur auf den Server**.
5. Beide Dateien prüfen: `python3 -m json.tool connector-meta.json` und `python3 -m json.tool mcp.json`.

Ohne Markenfreigabe: neutralen Projektnamen und ein selbst erstelltes Icon verwenden und beide JSON-Dateien, `SKILL.md` sowie alle Übersetzungen anpassen.

## Scopes

Ohne explizite Anfrage sollte der Server nur Leserechte vergeben (`device:read`, `scene:read`). Schreibrechte (`device:control`, `scene:execute`, `security:control`) müssen auf der Zustimmungsseite aktiv ausgewählt werden. Scharf-/Unscharfschalten und Szenenbearbeitung erfordern zusätzlich `confirm=true`; riskante Aktionen zuerst mit `dry_run=true` prüfen.

## Paketgrenzen

- **GitHub-Veröffentlichung:** alle Dateien dieses Verzeichnisses, einschließlich `i18n/`, `LICENSE`, `NOTICE` und `OPEN-SOURCE-STATEMENT.md`.
- **Einreichung beim WorkBuddy Connector Marketplace:** nur die geforderten Metadatendateien (`connector-meta.json`, `mcp.json`, `icon.svg`, `skills/**/SKILL.md`). Weder Rechtstexte noch `README*` oder `i18n/` beilegen.

## Open-Source-Erklärung (Kurzfassung)

- Offen: `connector-meta.json`, `mcp.json`, `SKILL.md`, `README*`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` — unter MIT.
- Nicht umfasst: das eingebettete Bild in `icon.svg`, alle Marken sowie sämtliche nicht mitgelieferten privaten Inhalte.
- Keine Gewährleistung; Bereitstellung „wie besehen". Der Betreiber des entfernten Dienstes ist der Verantwortliche für personenbezogene Daten.
- Vollständiger und maßgeblicher Text: `OPEN-SOURCE-STATEMENT.md`.

## Lokalisierte Marktplatz-Texte (`de`)

Quelle: `connector-meta.json` → `i18n.locales.de`.

- **Name:** Heiman Smart Home
- **Beschreibung:** Heiman-Smart-Home-Geräte in natürlicher Sprache steuern und abfragen: Licht, Klima, Vorhänge, Temperatur, Protokolle, Räume und Szenen.
- **Beispielsätze:**
  1. Stelle die Helligkeit des Wohnzimmerlichts auf 50 %
  2. Wie hoch sind Temperatur und Luftfeuchtigkeit im Wohnzimmer?
  3. Schalte die Klimaanlage im Wohnzimmer ein und stelle sie auf 26 °C
  4. Schließe die Vorhänge und starte die Szene „Zuhause"
  5. Zeige die Durchschnittstemperatur und -luftfeuchtigkeit der letzten 24 Stunden

## Lizenz und Kontakt

Eigene Inhalte stehen unter MIT (siehe `LICENSE`); Marken, `icon.svg` und nicht mitgelieferte Inhalte sind ausgenommen (siehe `NOTICE`). Der Stand der Veröffentlichungs-Prüfpunkte (Markenfreigabe, Icon-Rechte, Platzhalter) steht im Abschnitt „Remediation status" der englischen [`../README.md`](../README.md).

Sicherheitsmeldungen an `<security@example.com>` — niemals Zugangsdaten in öffentlichen Issues posten.
