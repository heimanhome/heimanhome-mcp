# Heiman Smart Home MCP — Guide d'utilisation (Français)

Interrogez et pilotez vos appareils domotiques en langage naturel avec un assistant IA : éclairage, climatisation, rideaux, température et humidité, journaux d'appareils, pièces, scénarios et alarme.

| | |
| --- | --- |
| Point d'accès | `https://spmcp.heimanhome.com/mcp` |
| Transport | MCP Streamable HTTP |
| Autorisation | OAuth 2.1 avec PKCE (connexion avec le compte domotique) |
| Dépôt | `https://github.com/heimanhome/heimanhome-mcp.git` |
| Assistance | `help@heimanhome.com` |

> La version anglaise [`../README.md`](../README.md) fait foi ; cette traduction est fournie à titre indicatif.

## 1. Connexion et autorisation

1. Ajoutez le point d'accès à votre client MCP avec l'une des configurations ci-dessous.
2. Le client ouvre la page d'autorisation ; connectez-vous avec votre compte domotique.
3. Vérifiez les permissions : lecture seule par défaut, droits d'écriture à cocher uniquement si nécessaire.
4. Validez. Le client conserve le jeton ; les identifiants ne sont jamais stockés dans ce dépôt.

## 2. Configuration des clients

**`mcp.json` générique** (Claude Desktop, Cursor, Cline, Windsurf, Zed, VS Code + GitHub Copilot, MCP Inspector) :

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

**Claude Code** :

```bash
claude mcp add --transport http heiman-smart-home https://spmcp.heimanhome.com/mcp
```

**Clients stdio uniquement** — pont avec `mcp-remote` :

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

**Clients hébergés** (ChatGPT, Claude.ai, Copilot Studio, Gemini CLI, WorkBuddy / CodeBuddy) : enregistrez un serveur MCP distant dans l'interface du client avec la même URL.

## 3. Permissions et opérations d'écriture

| Portée | Permet |
| --- | --- |
| `device:read` | Listes d'appareils, état, capacités, firmware, journaux, capteurs |
| `device:control` | Marche/arrêt, luminosité, température cible, rideaux |
| `scene:read` | Scénarios, pièces, maisons, automatisations |
| `scene:execute` | Exécuter, créer, modifier, supprimer des scénarios / automatisations |
| `security:control` | Armement / désarmement, accusé de réception d'alarme |

- Sans demande explicite, seule la lecture est accordée.
- Les actions sensibles exigent `confirm=true` ; prévisualisez avec `dry_run=true`.
- Pour un nouvel essai, utilisez `idempotent_key` puis `get_command_status(trace_id)`. Une réponse positive signifie que la commande a été **acceptée**, pas forcément exécutée.
- `whoami` affiche l'identité et les portées accordées. En cas de refus, corrigez la portée / le paramètre / la confirmation.

## 4. Exemples de requêtes

```
Règle la luminosité du luminaire du salon à 50 %
Quelle est la température et l'humidité actuelles dans le salon ?
Allume la climatisation du salon et règle-la sur 26 °C
Ferme les rideaux puis lance le scénario « Retour à la maison »
Montre la température et l'humidité moyennes des dernières 24 heures
```

L'assistant répond dans votre langue ; les exemples en 8 langues sont dans `connector-meta.json` → `i18n.locales`.

## 5. Dépannage

| Symptôme | Solution |
| --- | --- |
| `401` / autorisation expirée | Reconnectez et autorisez à nouveau |
| Portée non accordée | Réautorisez et cochez la portée nécessaire |
| Commande acceptée, appareil inchangé | Vérifiez `get_command_status(trace_id)` et laissez le temps à l'appareil |
| Trop de requêtes | Attendez avant de réessayer, sans répéter à l'identique |
| Connexion impossible | Vérifiez Streamable HTTP + OAuth et l'accès HTTPS au point d'accès |

## 6. Sécurité et confidentialité

- Les commandes modifient des appareils réels : examinez la prévisualisation `dry_run` et limitez les droits d'écriture.
- Jetons et identifiants restent côté serveur. Ne publiez jamais de jetons ou mots de passe dans les issues ; signalez les failles à `help@heimanhome.com`.
- L'exploitant du service distant est responsable des données — voir `OPEN-SOURCE-STATEMENT.md`.

## Licence et autres documents

MIT (voir `LICENSE`) ; propriété et marques dans `NOTICE` ; liste complète des outils dans `skills/heimanhome-skill/SKILL.md`.
