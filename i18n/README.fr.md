# Heiman Smart Home Connector — Français

Un **ensemble de métadonnées de connecteur MCP** pour les clients compatibles WorkBuddy / CodeBuddy. Il permet à un assistant IA d'interroger et de piloter les appareils domotiques en langage naturel : éclairage, climatisation, rideaux, température et humidité, journaux d'appareils, maisons et pièces, scénarios, capacités des appareils, alarme, notifications et informations de micrologiciel (lecture seule).

> **Ce dépôt contient uniquement les métadonnées du connecteur, pas l'implémentation du serveur.**
> Il s'agit d'une intégration tierce : **ni un produit officiel Heiman, ni un produit officiel WorkBuddy / CodeBuddy**, sans aval ni certification de ces parties. Voir `OPEN-SOURCE-STATEMENT.md`.

**Langue :** l'anglais fait foi. Cette traduction est fournie à titre indicatif ; en cas de divergence, [`../README.md`](../README.md) prévaut. Les textes juridiques (`LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`) ne font foi qu'en anglais.

## Langues

| Langue | Code | Document |
| --- | --- | --- |
| English (fait foi) | `en` | [`../README.md`](../README.md) |
| Deutsch | `de` | [`README.de.md`](README.de.md) |
| Français | `fr` | `README.fr.md` |
| Español | `es` | [`README.es.md`](README.es.md) |
| Português | `pt` | [`README.pt.md`](README.pt.md) |
| العربية (RTL) | `ar` | [`README.ar.md`](README.ar.md) |
| Русский | `ru` | [`README.ru.md`](README.ru.md) |
| 简体中文 | `zh` | [`README.zh.md`](README.zh.md) |

Les codes correspondent à `connector-meta.json` → `i18n.supported_locales`. La source de vérité des textes localisés est `i18n.locales`.

## Contenu du paquet

Les chemins sont relatifs à la racine du dépôt.

| Chemin | Rôle |
| --- | --- |
| `connector-meta.json` | Descripteur du connecteur, textes localisés, `auth_mode: server-side`, scopes, URL OAuth |
| `mcp.json` | Configuration de connexion du client MCP (`streamableHttp`) |
| `icon.svg` | Icône (création graphique tierce ; hors licence MIT tant que les droits ne sont pas confirmés) |
| `skills/heimanhome-skill/SKILL.md` | Instructions du Skill : 39 outils, 5 scopes, règles multilingues |
| `README.md`, `i18n/` | Documentation principale en anglais et traductions |
| `LICENSE`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md` | Licence, mentions de marque et de propriété, déclaration open source |

## Avant toute utilisation

`https://smart-home-mcp.example.com/mcp` dans `mcp.json` et `connector-meta.json` est une **adresse fictive**. Installé tel quel, le paquet ne se connecte à aucun service. Il faut :

1. Déployer un serveur MCP accessible publiquement en HTTPS (Streamable HTTP + OAuth, RFC 9728 / 8414 / 7591 / 7636 / 7009) avec les 39 outils décrits dans `SKILL.md`.
2. Remplacer `example.com` dans les deux fichiers JSON par votre adresse réelle `https://<domaine>/mcp`.
3. Vérifier la version client requise par `auth_mode: server-side` (`minWorkbuddyVersion: 4.24.0`).
4. Obtenir légalement `clientId` / `clientSecret` et l'autorisation d'intégration auprès de la plateforme amont ; les identifiants restent **uniquement sur le serveur**.
5. Valider les deux fichiers : `python3 -m json.tool connector-meta.json` et `python3 -m json.tool mcp.json`.

Sans autorisation de marque : utiliser un nom de projet neutre et une icône créée par vos soins, puis mettre à jour les deux JSON, `SKILL.md` et toutes les traductions.

## Scopes

Sans demande explicite, le serveur ne doit accorder que la lecture (`device:read`, `scene:read`). Les droits d'écriture (`device:control`, `scene:execute`, `security:control`) doivent être cochés explicitement sur la page de consentement. L'armement et la modification de scénarios exigent en plus `confirm=true` ; prévisualiser les actions sensibles avec `dry_run=true`.

## Périmètre des paquets

- **Publication GitHub :** tous les fichiers de ce répertoire, y compris `i18n/`, `LICENSE`, `NOTICE` et `OPEN-SOURCE-STATEMENT.md`.
- **Soumission au marketplace WorkBuddy Connector :** uniquement les fichiers de métadonnées requis (`connector-meta.json`, `mcp.json`, `icon.svg`, `skills/**/SKILL.md`). Ne joindre ni textes juridiques, ni `README*`, ni `i18n/`.

## Déclaration open source (résumé)

- Publié sous MIT : `connector-meta.json`, `mcp.json`, `SKILL.md`, `README*`, `NOTICE`, `OPEN-SOURCE-STATEMENT.md`.
- Exclus : l'image intégrée dans `icon.svg`, toutes les marques et tout contenu privé non livré.
- Sans garantie, fourni « en l'état ». L'exploitant du service distant est responsable du traitement des données personnelles.
- Texte complet et faisant foi : `OPEN-SOURCE-STATEMENT.md`.

## Textes localisés pour le marketplace (`fr`)

Source : `connector-meta.json` → `i18n.locales.fr`.

- **Nom :** Heiman Smart Home
- **Description :** Pilotez et interrogez vos appareils Heiman en langage naturel : lumières, climatisation, rideaux, température, journaux, pièces et scénarios.
- **Exemples :**
  1. Règle la luminosité du luminaire du salon à 50 %
  2. Quelle est la température et l'humidité actuelles dans le salon ?
  3. Allume la climatisation du salon et règle-la sur 26 °C
  4. Ferme les rideaux puis lance le scénario « Retour à la maison »
  5. Affiche la température et l'humidité moyennes des dernières 24 heures

## Licence et contact

Les contenus originaux sont sous MIT (voir `LICENSE`) ; marques, `icon.svg` et contenus non livrés sont exclus (voir `NOTICE`). La liste des points à valider avant publication (marque, icône, valeurs fictives) figure dans la section « Remediation status » de [`../README.md`](../README.md).

Signaler les problèmes de sécurité à `<security@example.com>` — ne jamais publier d'identifiants dans une issue publique.
