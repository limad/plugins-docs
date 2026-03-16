---
layout: default
title: mcp_jeedom — Changelog
lang: fr_FR
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — mcp_jeedom

<a href="https://limad.github.io/plugins-docs/plugin-mcp_jeedom">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/mcp_jeedom_icon.png" alt="mcp_jeedom icon" width="120px">
</a>

- [Présentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#presentation)
- [Documentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#documentation)
- Changelog
- [Forum dédié](https://community.jeedom.com/tags/plugin-mcp_jeedom)

---

## Version du 16/03/2026

**Transport**
- Nouveau transport par défaut : **Streamable HTTP** (`/mcp`, MCP spec 2025). SSE conservé en mode legacy.
- Nouveau proxy externe `jeemcp_proxy.php` supportant les deux transports avec auth Bearer.
- `testExternalSSE` renommé `testExternalReach` (les deux noms acceptés) — test adapté au transport actif.

**Nouveaux outils — intelligence sémantique**
- `get_full_state` — snapshot complet de la maison en 1 seul appel API (pièces + équipements + valeurs + generic_type)
- `find_command` — recherche sémantique par `generic_type` (`LIGHT_ON`, `TEMPERATURE`…) et/ou nom de pièce
- `get_statistics` — min, max, moyenne, tendance sur une commande historisée
- `get_changes` — tous les changements d'état depuis N minutes (`event::changes`)
- `get_battery_report` — état des batteries avec seuil d'alerte configurable
- `get_scenario_code` — lit la logique complète d'un scénario pour l'analyser
- `list_logs` — liste les fichiers de log disponibles via RPC
- `get_plugin_status` — état daemons et dépendances de tous les plugins actifs
- `get_room_summary` — résumé Jeedom d'une pièce (nb lumières allumées, volets ouverts…)

**Nouveaux outils — caméras et notifications**
- `list_cameras` — liste les caméras disponibles (plugin Camera Jeedom)
- `get_camera_snapshot` — capture un snapshot et le retourne comme `ImageContent` analysable directement par Claude
- `list_notification_commands` — découvre les commandes d'envoi disponibles (Telegram, Mail, Slack, Pushover…)
- `send_notification` — envoie un message via un plugin notification Jeedom

**Sécurité et audit**
- **Mode lecture seule** (`--read-only`) — désactive toutes les actions (`execute_action`, `bulk_execute`, `set_variable`, `send_notification`…) sans toucher aux permissions de lecture
- **Audit log** (`--audit-log`) — journal horodaté de chaque action IA (outil, arguments, résultat) dans un fichier dédié ; visible et effaçable depuis la page de configuration
- Arguments sensibles (`token`, `key`, `pass`, `secret`) masqués automatiquement dans l'audit

**Ressource `jeedom://home_profile`** — profil personnalisé de la maison (habitants, habitudes, préférences, canal de notification par défaut) lu à chaque session par l'IA ; éditeur JSON modal dans la page de configuration

**Améliorations**
- `generic_type` exposé dans tous les outils, ressources et la whitelist (nouveau badge dans l'UI)
- `get_room_state` → `jeeObject::fullById` (1 appel au lieu de N+1)
- `get_device_info` → `eqLogic::fullById` (1 appel, + batterie et dernière comm)
- `list_devices` affiche batterie et dernière communication
- `read_log` passe d'abord par l'API RPC `log::get`, lecture fichier en fallback
- `execute_action` / `bulk_execute` — invalidation ciblée du cache temps-réel uniquement

**Nouvelles ressources MCP** — `jeedom://ai_context`, `jeedom://instructions`, `jeedom://home_profile`, `jeedom://full_map`, `jeedom://generic_types`

**Cache à deux niveaux** — structurel 5 min (noms, types) / temps-réel 30 s (valeurs courantes)

**Niveau de log configurable** — `--log-level` dérivé du niveau configuré dans Jeedom, DEBUG forcé en démarrage manuel. Formatter amélioré (nettoyage `\r\n`, alignement).

**PHP** — `DEFAULT_TRANSPORT` → `streamable-http`, endpoints et health-check adaptés, `--log-level` passé au daemon, `generic_type` dans `buildWhitelistFromJeedom()`, nouvelles actions AJAX (`getAuditLog`, `clearAuditLog`, `getHomeProfile`, `saveHomeProfile`).

---

## Version du 16/03/2026 — Mineure

- Ajustement du niveau de log au démarrage et messages du daemon (merci Neurall).

---

## Versions antérieures

*(à compléter)*
