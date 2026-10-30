---
layout: default
title: MCP Server — Plugin MCP pour Jeedom
lang: fr_FR
pluginId: mcp_jeedom
---

# Présentation

Le plugin MCP Server(`mcp_jeedom`) transforme votre installation Jeedom en serveur **MCP (Model Context Protocol)**, le standard ouvert d'Anthropic pour connecter les intelligences artificielles à des systèmes externes.

Vos assistants IA préférés — **Claude Desktop, Cursor, Continue** et tout client MCP compatible — peuvent désormais interagir directement et en temps réel avec votre maison connectée.

## Ce que l'IA peut faire dans votre maison

- Consulter l'état complet de la maison en un seul appel
- Contrôler lumières, volets, thermostats, serrures et scénarios
- Analyser l'historique et les statistiques de vos capteurs
- Détecter les équipements en erreur et les batteries faibles
- Lire et analyser les images de vos caméras
- Envoyer des notifications via vos plugins (Telegram, Mail…)
- Comprendre et déboguer vos scénarios d'automatisation

## Fonctionnalités

- 🤖 Transport **Streamable HTTP** (standard MCP 2025)
- 🏠 Plus de **40 outils** couvrant l'ensemble de l'API Jeedom
- ⚡ Outil `jeedom_api` pour les opérations composites avancées (bilan de santé, équipements virtuels, séquences d'actions…)
- 🔍 Recherche sémantique par type de commande (`LIGHT_ON`, `TEMPERATURE`…)
- ⚙️ Whitelist des équipements et commandes exposés à l'IA
- 🔐 Tokens à **4 rôles** (Utilisateur limité, Opérateur, Administrateur, Super-administrateur), OAuth, **niveau maximum** réglable et **mode lecture seule**
- 📸 Analyse visuelle des **snapshots caméra** (plugin Camera)
- 📋 **Journal d'audit** horodaté de toutes les actions de l'IA
- 🗂️ Ressources contextuelles MCP : carte des IDs, profil maison, guide opérationnel
- 🌐 Accès local (LAN) ou externe sécurisé (Cloudflare Tunnel, reverse proxy)

## Exemples d'utilisation

> *« Quel est l'état de la maison ? »* — résumé complet en quelques secondes.

> *« Analyse ma consommation électrique de la semaine. »* — statistiques et tendances automatiques.

> *« Y a-t-il quelqu'un dans le couloir ? »* — analyse du snapshot caméra en direct.

> *« Prépare la maison pour la nuit. »* — orchestration de plusieurs scénarios en une phrase.

> *« Explique-moi le scénario Alarme nuit et dis-moi s'il fonctionne bien. »* — lecture et diagnostic du code.

## Sécurité et contrôle

Vous définissez précisément ce que l'IA peut voir et faire. Le plugin ne fait jamais rien sans que vous ayez explicitement activé la permission correspondante. Chaque action est tracée dans le journal d'audit.

---

> ## ⛔ AVERTISSEMENT — À LIRE AVANT TOUTE UTILISATION
>
> Ce plugin expose des **outils ultra-avancés** donnant à une intelligence artificielle un accès direct à votre installation domotique : lecture d'état, contrôle d'équipements, exécution de commandes shell, lecture/écriture de fichiers, gestion de scénarios…
>
> Des efforts particuliers ont été apportés à la sécurisation du code, mais **aucun système n'est infaillible**. Les risques suivants existent et ne peuvent pas être totalement éliminés :
>
> - 🔓 **En cas de piratage** — si un attaquant accède à votre endpoint MCP (URL externe, token volé, réseau compromis), il dispose d'un contrôle total sur votre installation domotique.
> - 🐛 **En cas de bug dans le plugin** — une erreur de code peut entraîner des comportements inattendus, des actions non souhaitées ou une exposition de données sensibles.
> - 🤖 **En cas d'erreur de l'IA** — un modèle de langage peut mal interpréter une demande, exécuter une mauvaise action ou enchaîner des commandes non prévues. L'IA n'est pas infaillible.
> - 🌐 **En cas de configuration réseau inadaptée** — exposer directement le port du daemon MCP sur Internet sans protection revient à ouvrir un accès root à votre maison.
>
> ### Règles essentielles
>
> - ✅ **Ne connectez que des IA auxquelles vous faites entièrement confiance** et dont vous contrôlez la configuration.
> - ✅ **Privilégiez une IA locale** (Claude Desktop, serveur local…) plutôt qu'un service cloud quand c'est possible — les données ne quittent pas votre réseau.
> - ✅ **N'utilisez jamais le mode accès externe** sans avoir lu et appliqué la section [Sécurité](#sécurité).
> - ❌ **Ne partagez jamais** votre URL externe, votre token Bearer ni votre clé API Jeedom — même partiellement, même dans une capture d'écran.
> - ❌ **N'activez pas** `execute_local`, `write_file` ou `restart_jeedom` en usage quotidien — ces outils doivent rester désactivés sauf besoin explicite et temporaire.
> - ❌ **N'exposez jamais directement** le port du daemon (8765) sur Internet sans proxy authentifié.
>
> ### Responsabilité
>
> **L'utilisateur est seul responsable** de la configuration, de l'exposition réseau et de l'usage du plugin. Ni le développeur ni Domadoo ne peuvent être tenus responsables des conséquences d'un accès non autorisé, d'une erreur de l'IA, d'un bug du plugin ou d'une mauvaise configuration réseau.
>
> Consultez la section [Sécurité](#sécurité) pour les bonnes pratiques détaillées.

---

---

# Qu'est-ce que MCP ?

## Définition

**MCP (Model Context Protocol)** est un protocole ouvert standardisé, publié par Anthropic en 2024, qui définit comment un modèle de langage (LLM) communique avec des outils et des sources de données externes.

Avant MCP, chaque intégration LLM ↔ outil était développée sur-mesure, rendant l'écosystème fragmenté et difficile à maintenir. MCP introduit un contrat commun : n'importe quel client MCP (Claude Desktop, Cursor, Continue…) peut se connecter à n'importe quel serveur MCP sans code spécifique de chaque côté.

## Architecture

MCP repose sur trois composants :

- **Client MCP** — l'application hébergeant le LLM (ex : Claude Desktop). Il initie la connexion et envoie les requêtes d'outils.
- **Serveur MCP** — un processus exposant des capacités via le protocole (outils, ressources, prompts). C'est le rôle de `mcp_jeedom`.
- **Transport** — le canal de communication : **Streamable HTTP** (standard MCP 2025). Les transports SSE (legacy) et STDIO ont été retirés : une configuration qui les utilisait passe automatiquement en Streamable HTTP à la mise à jour.

> ℹ️ **Important**  
> Le LLM ne s'exécute jamais sur votre serveur Jeedom — il tourne chez Anthropic (ou en local).  
> Le serveur MCP est simplement un pont entre le LLM et l'API Jeedom.  
> Toutes les décisions intelligentes restent côté LLM ; le serveur n'est qu'un exécuteur.

## Protocole de communication

La communication suit **JSON-RPC 2.0** sur le transport choisi. Un échange typique :

1. Le client envoie `tools/list` → le serveur retourne la liste des outils disponibles.
2. Le LLM choisit un outil et envoie `tools/call` avec les arguments.
3. Le serveur exécute l'action (appel API Jeedom) et retourne le résultat en texte.
4. Le LLM intègre le résultat dans sa réponse à l'utilisateur.

---

# Rôle du plugin mcp_jeedom

## Vue d'ensemble

`mcp_jeedom` est un plugin Jeedom qui démarre un **serveur MCP en arrière-plan** (daemon Python). Ce serveur traduit les appels MCP en requêtes vers l'API JSON-RPC interne de Jeedom, permettant à un LLM de :

- Connaître l'état complet de votre maison en temps réel
- Contrôler vos équipements (lumières, volets, thermostats…) par type sémantique
- Déclencher, analyser et déboguer vos scénarios
- Consulter l'historique et les statistiques de valeurs
- Détecter les changements récents et l'état des batteries
- Accéder aux logs et fichiers du serveur *(optionnel, sécurisé)*

## Stack technique

| Composant | Technologie |
|---|---|
| Daemon | Python 3.11+ + anyio |
| Protocole | SDK MCP officiel (Python) 2.3 |
| Serveur HTTP | uvicorn + ASGI pur |
| Transport par défaut | Streamable HTTP (MCP spec 2025) |
| API Jeedom | JSON-RPC interne |
| Plugin PHP | Jeedom Core 4.6+ |
| Système | Debian 12 ou plus récent (Debian 11 n'est plus pris en charge) |

---

# Capacités disponibles

## Outils (Tools)

Les outils sont des fonctions appelables par le LLM, organisées par catégorie.

### Vue d'ensemble optimisée *(nouveaux outils recommandés en premier)*

| Outil | Description |
|---|---|
| `get_full_state` | **État complet de la maison en 1 appel** : pièces, équipements, commandes, valeurs, generic_type |
| `find_command` | **Recherche sémantique** par `generic_type` (LIGHT_ON, TEMPERATURE…) et/ou pièce/équipement |
| `get_statistics` | Statistiques d'une commande historisée : min, max, moyenne, tendance |
| `get_changes` | Tous les changements d'état depuis N minutes |
| `get_battery_report` | État des batteries avec seuil d'alerte configurable |
| `get_plugin_status` | État daemons et dépendances de tous les plugins actifs |
| `get_room_summary` | Résumé Jeedom d'une pièce : nb lumières allumées, volets ouverts… |
| `jeedom_api` | **Opérations composites avancées** : bilan de santé global, état optimisé, équipements virtuels, séquences, duplication de scénarios… |

> 💡 **Conseil** : commencer par `get_full_state` pour une vue complète, puis utiliser `find_command` pour cibler une commande précise avant d'agir.

### Informations système

| Outil | Description |
|---|---|
| `get_system_info` | Version Jeedom, hardware, adresse IP, OS |
| `get_jeedom_summary` | Dashboard complet : version, plugins actifs, résumés globaux |
| `list_plugins` | Liste tous les plugins installés (actifs et inactifs) |
| `get_system_messages` | Messages système en attente |
| `clear_system_messages` | Efface les messages système |
| `get_cron_list` | Tâches planifiées internes Jeedom avec leur état |
| `get_timeline` | Événements récents de la timeline Jeedom |
| `invalidate_cache` | Vide le cache interne du serveur MCP |

### Équipements et pièces

| Outil | Description |
|---|---|
| `list_rooms` | Liste toutes les pièces avec leurs IDs |
| `list_devices` | Liste les équipements (filtrable par pièce, type, actif/inactif) — avec batterie et dernière comm |
| `search_devices` | Recherche plein-texte sur les noms d'équipements |
| `get_device_info` | Détails complets d'un équipement (1 appel API) : commandes, generic_type, batterie |
| `get_command_value` | Valeur courante d'une commande info avec generic_type |
| `execute_action` | Exécute une commande action (on/off, slider, message…) |
| `get_room_state` | État de tous les équipements actifs d'une pièce (1 appel API) avec generic_type |
| `bulk_execute` | Exécute plusieurs commandes en une seule requête |
| `get_dead_devices` | Équipements injoignables ou en erreur |
| `get_command_history` | Historique des valeurs d'une commande sur une période |

### Opérations composites avancées (`jeedom_api`)

> Cet outil unique donne accès à des opérations PHP natives impossibles ou inefficaces via l'API RPC standard. Il est toujours disponible (aucune permission supplémentaire requise).

| Action | Description |
|---|---|
| `healthReport` | Bilan de santé global en 1 appel : équipements en erreur, batteries faibles, messages système, daemons NOK, mises à jour disponibles |
| `fullStateOptimized` | État complet de la maison avec valeurs temps réel, format épuré pour le LLM |
| `getCommandValues` | Valeurs de plusieurs commandes en un seul appel |
| `duplicateScenario` | Copie complète d'un scénario existant avec nouveau nom |
| `bulkScenarioAction` | Activer, désactiver, déclencher ou arrêter tous les scénarios d'un groupe |
| `saveVirtualDevice` | Créer ou mettre à jour un équipement virtuel avec ses commandes |
| `saveCommand` | Créer ou mettre à jour une commande sur un équipement |
| `removeDevice` | Supprimer un équipement (confirmation obligatoire) |
| `objectSummary` | Résumés Jeedom agrégés d'un ou de tous les objets |
| `sequenceActions` | Séquence d'actions avec délai configurable entre chacune |

Exemples : `jeedom_api(action='healthReport')` · `jeedom_api(action='duplicateScenario', params={scenario_id:8, new_name:'Copie'})` · `jeedom_api(action='saveVirtualDevice', body={device:{name:'Temp ext', object_id:1, commands:[{name:'Température', type:'info', subType:'numeric', unite:'°C'}]}})`

---

### Scénarios

| Outil | Description |
|---|---|
| `list_scenarios` | Liste tous les scénarios avec leur état |
| `trigger_scenario` | Déclenche un scénario |
| `stop_scenario` | Arrête un scénario en cours |
| `set_scenario_active` | Active ou désactive un scénario |
| `get_scenario_code` | **Lit le code et la logique complète d'un scénario** (pour comprendre/déboguer) |
| `get_scenario_log` | Log d'exécution d'un scénario *(optionnel — voir §3.1 accès fichiers)* |

> 🆕 **Scénarios multi-déclencheurs (Jeedom ≥ 4.5)** — l'IA peut générer des conditions basées sur les tags `#trigger_id#`, `#trigger_name#`, `#trigger_value#`, `#trigger#` (qui remplacent les fonctions dépréciées `trigger()`/`triggerId()`/`triggerValue()`) via `scenario_write(action=build_trigger, cmd_id=...)` pour une condition unique, ou `scenario_write(action=dispatch_trigger, cmd_ids=[...])` pour générer directement un squelette if/elseif complet routant sur plusieurs commandes déclenchantes. Un garde-fou détecte automatiquement l'ancienne syntaxe dépréciée dans les scénarios importés/modifiés et remonte un avertissement (non bloquant).

### Vues et tableaux de bord *(nouveau)*

| Outil (`views`) | Description |
|---|---|
| `create_view` / `create_zone` / `add_zone` | Crée un dashboard Jeedom et y ajoute des zones (widgets, tableaux) liées à des équipements, commandes ou scénarios |
| `set_zone_table` | Construit un tableau de commandes avec disposition personnalisée (lignes/colonnes) |
| `tile_to_table` | Convertit la tuile d'un équipement en tableau compact (regroupe automatiquement les paires On/Off) — réversible |
| `list` / `get` | Liste ou détaille les vues existantes |
| `list_designs` / `get_design` | Liste ou détaille les plans 2D (designs) Jeedom |

Exemple : *« Crée un dashboard Salon avec un tableau de commandes pour les lumières et volets »*.

### Variables et interaction

| Outil | Description |
|---|---|
| `list_variables` | Liste toutes les variables globales Jeedom |
| `get_variable` | Lit la valeur d'une variable globale |
| `set_variable` | Définit la valeur d'une variable globale |
| `ask_jeedom` | Commande en langage naturel via l'API interact Jeedom |

### Accès fichiers *(optionnels, sécurisés)*

> ⚠️ Ces outils sont **désactivés par défaut**. Chaque option doit être activée explicitement dans la page de configuration du plugin.

| Outil | Activation requise | Description |
|---|---|---|
| `list_logs` | Lecture des logs | Liste les fichiers de log disponibles |
| `read_log` | Lecture des logs *(activé par défaut)* | Lit les dernières lignes d'un log |
| `read_file` | Lecture de fichiers | Lit un fichier (chemins restreints) |
| `list_directory` | Lecture de fichiers | Liste un répertoire (chemins restreints) |
| `write_file` | Écriture de fichiers *(risque élevé)* | Écrit un fichier dans `/plugins` ou `/tmp/mcp_jeedom` |
| `restart_jeedom` | Redémarrage Jeedom *(risque élevé)* | Redémarre le service Jeedom |
| `execute_local` | Exécution locale *(risque élevé)* | Exécute une commande shell sur le serveur (avec garde-fous) |

### Caméras *(optionnel)*

> Nécessite le **plugin Camera** installé et configuré dans Jeedom.

| Outil | Description |
|---|---|
| `list_cameras` | Liste les caméras disponibles avec leurs IDs |
| `get_camera_snapshot` | Capture un snapshot et le retourne comme image analysable directement par Claude (présence, scène, objet…) |

### Notifications sortantes *(optionnel)*

| Outil | Description |
|---|---|
| `list_notification_commands` | Découvre toutes les commandes d'envoi disponibles (Telegram, Mail, Slack, Pushover…) |
| `send_notification` | Envoie un message via un plugin notification Jeedom |

---

## Ressources (Resources)

Les ressources MCP sont des URI lisibles directement par le client, sans appel d'outil explicite. Certains clients les affichent dans un panneau latéral ou les injectent automatiquement dans le contexte.

### Ressources de contexte IA *(nouvelles)*

| URI | Contenu | Usage recommandé |
|---|---|---|
| `jeedom://ai_context` | Architecture complète du plugin en JSON | À lire **en priorité** à la première connexion |
| `jeedom://instructions` | Guide opérationnel : workflows, règles, generic_types | Référence pour l'IA pendant la session |
| `jeedom://home_profile` | Profil de la maison : habitants, habitudes, préférences | Personnalise les réponses à votre installation |
| `jeedom://full_map` | Carte pièces→équipements→commandes avec IDs et generic_type (format compact) | Index complet pour trouver tout cmd_id sans appel supplémentaire |
| `jeedom://generic_types` | Index des commandes groupées par LIGHT_ON / TEMPERATURE / POWER… | Trouver instantanément tous les capteurs d'un type |
| `jeedom://scenario_schema` *(nouveau)* | Format JSON des blocs de scénarios + syntaxe des conditions, dont les tags `#trigger_*#` | Référence avant de créer/modifier un scénario complexe |
| `jeedom://views_guide` *(nouveau)* | Guide détaillé des vues/designs : types de zones, link_types, schéma position/style | Référence à la demande pour dashboards avancés |

### Ressources état maison

| URI | Contenu |
|---|---|
| `jeedom://summary` | Dashboard Jeedom complet |
| `jeedom://rooms` | Liste de toutes les pièces |
| `jeedom://devices` | Tous les équipements actifs |
| `jeedom://scenarios` | Scénarios et leur état |
| `jeedom://variables` | Variables globales |
| `jeedom://messages` | Messages système en attente |
| `jeedom://plugins` | Plugins installés |
| `jeedom://room/{id}` | État détaillé d'une pièce spécifique *(dynamique)* |

---

## Generic types — intelligence sémantique

Chaque commande Jeedom dispose d'un `generic_type` qui décrit son rôle fonctionnel indépendamment de son nom. Le plugin expose ce champ partout, permettant à l'IA de raisonner sémantiquement.

| Catégorie | Generic types |
|---|---|
| Lumières | `LIGHT_STATE`, `LIGHT_ON`, `LIGHT_OFF`, `LIGHT_SLIDER`, `LIGHT_COLOR` |
| Température / Humidité | `TEMPERATURE`, `HUMIDITY` |
| Énergie | `POWER` (W), `ENERGY` (kWh) |
| Volets | `FLAP_STATE`, `FLAP_UP`, `FLAP_DOWN`, `FLAP_SLIDER` |
| Serrures | `LOCK_STATE`, `LOCK_OPEN`, `LOCK_CLOSE` |
| Sécurité | `ALARM_STATE`, `ALARM_ARMED`, `PRESENCE`, `DOOR_STATE`, `MOTION`, `SMOKE_STATE` |
| Divers | `BATTERY`, `FAN_STATE`, `FAN_SPEED` |

Exemple d'utilisation : `find_command(generic_type="LIGHT_ON", room_name="salon")` retourne directement le `cmd_id` de l'interrupteur ON du salon, sans avoir à parcourir les équipements manuellement.

---

## Skills — expertise Jeedom embarquée

Le plugin fournit à l'IA des guides métier spécialisés qu'elle consulte automatiquement avant d'agir. Objectif : des réponses précises, conformes aux conventions Jeedom, sans hallucination.

| Skill | Domaine | Déclenche sur |
|---|---|---|
| `jeedom-expert-scenarios` | Conception de scénarios (blocks, conditions, code PHP) | création / modification / debug de scénarios |
| `jeedom-history-analytics` | Analyse d'historique de commandes, statistiques, tendances | questions sur consommation, moyennes, évolutions |
| `jeedom-devices-energy` | Mesure et optimisation énergétique (W, kWh, coûts) | audits énergie, alertes de consommation |
| `jeedom-interaction-ask` | Règles de dialogue (quand demander confirmation, comment clarifier) | toute interaction longue ou à risque |

Les skills sont exposés comme ressources MCP (`jeedom://skill/<nom>`) : l'IA les lit à la demande selon le contexte de la conversation.

---

## Extensions MCP de plugins tiers

Les développeurs d'autres plugins Jeedom peuvent exposer leurs propres outils MCP **sans modifier `mcp_jeedom`**. Un plugin tiers devient acteur du serveur MCP en déposant deux fichiers :

```
plugins/{votre_plugin}/resources/mcp_extension/mcp_tools.json    ← définitions (schémas)
plugins/{votre_plugin}/resources/mcp_extension/McpExtension.php  ← handler (exécution)
```

Les outils apparaissent automatiquement avec le préfixe `ext__{votre_plugin}__{nom_outil}` dès le premier `tools/list` (cache à la demande, revalidation automatique).

**Exemple** : un plugin météo exposant `ext__meteo__get_forecast` → l'IA peut ensuite l'appeler comme n'importe quel outil natif pour intégrer la météo dans ses réponses ou scénarios.

Le guide complet pour les développeurs se trouve dans `plugins/mcp_jeedom/resources/mcp_extension/EXTENSION_GUIDE.md`.

---

## Comparaison avec d'autres serveurs MCP domotique

| Capacité | **mcp_jeedom** | HA officiel | HA communauté | Gladys |
|---|:---:|:---:|:---:|:---:|
| Contrôle équipements | ✅ | ✅ | ✅ | ✅ |
| Recherche sémantique (generic_type) | ✅ | ❌ | ❌ | ❌ |
| Snapshot maison 1 appel | ✅ | ❌ | ❌ | ❌ |
| Statistiques (min/max/avg/tendance) | ✅ | ❌ | ❌ | ❌ |
| Historique valeurs | ✅ | ❌ | ❌ | ❌ |
| Changements récents (event stream) | ✅ | ❌ | ❌ | ❌ |
| Rapport batteries | ✅ | ❌ | ❌ | ❌ |
| Snapshots caméra (ImageContent) | ✅ *(opt.)* | ❌ | ❌ | ✅ |
| Envoi notifications | ✅ *(opt.)* | ❌ | ❌ | ❌ |
| Scénarios complexes | ✅ | partiel | ✅ | ✅ |
| Code scénario lisible par IA | ✅ | ❌ | ❌ | ❌ |
| Variables globales | ✅ | ❌ | ❌ | ❌ |
| Langage naturel natif | ✅ | ❌ | ❌ | ❌ |
| Ressources MCP contexte IA | ✅ | ❌ | ✅ | ❌ |
| Profil maison personnalisé | ✅ | ❌ | ❌ | ❌ |
| Mode lecture seule | ✅ | ❌ | ❌ | ❌ |
| Audit log des actions IA | ✅ | ❌ | ❌ | ❌ |
| Accès fichiers serveur | ✅ *(opt.)* | ❌ | ❌ | ❌ |
| Opérations PHP composites | ✅ | ❌ | ❌ | ❌ |
| Accès externe gratuit | ✅ | ✅ | ✅ | payant |

*(opt.) = optionnel, sécurisé par whitelist de répertoires*

---

# Installation et configuration

## Installation

1. Dans Jeedom, aller dans **Plugins → Gestion des plugins → Market**.
2. Taper "MCP Jeedom" dans "Rechercher" et selectionner "MCP Jeedom" par Limad44
3. **Installer le plugin comme d'habitude**
4. **Activer le plugin et installer les dépendances** (Python venv + librairies MCP).
5. Démarrer le daemon depuis la page de configuration.
6. Vérifier que le statut affiche **OK**.

## Assistant de configuration

Dès la première ouverture de la page du plugin (tant qu'elle n'a pas été terminée), un **assistant de configuration** s'ouvre automatiquement en 5 étapes :

1. **Santé** — vérifie que le daemon Python est démarré (bloquant tant qu'il ne l'est pas, avec un bouton pour le lancer).
2. **Profil d'usage** — choisissez un des 3 préréglages (Consultation = mode lecture seule, Pilotage, Avancé avec écriture de scénarios) ; le préréglage détecté correspondant à votre configuration actuelle est présélectionné.
3. **Whitelist** — génère automatiquement la liste des équipements autorisés depuis votre installation Jeedom.
4. **Premier token** — crée un token avec le rôle choisi : **Opérateur** (par défaut) ou **Administrateur**. Voir [Tokens et rôles](#tokens-et-rôles).
5. **Connexion client** — récupère l'URL à donner à votre IA et teste l'accessibilité externe si configurée.

Vous pouvez ignorer une étape (`Passer cette étape`) ou rouvrir l'assistant à tout moment depuis la vignette **Assistant** de la page du plugin. Les réglages sensibles (`allow_local_exec`, `allow_write_file`, `allow_restart`) ne sont **jamais** modifiés par l'assistant, quel que soit le préréglage choisi — ils restent à activer manuellement dans la configuration avancée si besoin.

## Complter la configuration du plugin
 Voir §7.3 pour la configuration complète.

Prérequis côté client

- Le poste client (qui exécute l'IA) doit joindre Jeedom par son adresse web habituelle. Le port du daemon (**8765** par défaut) n'écoute que sur la box elle-même : toute connexion passe par le proxy du plugin, avec un token.
- Pour le transport **Streamable HTTP** (défaut) : aucun outil supplémentaire requis si le client sait envoyer un en-tête `Authorization`.

## Configuration des clients IA

Pour la configuration complète de tous les clients IA supportés (Claude Desktop, Claude Code, Cursor, VS Code, Codex, Windsurf, Continue, Zed, JetBrains, Raycast…), consultez le guide dédié → [Connexion des clients IA](clients.md)

## Configuration Claude Desktop

Éditez le fichier `claude_desktop_config.json` :

| OS | Chemin |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

---

### Méthode 1 — Accès local LAN (Streamable HTTP — recommandé)

Le poste client est sur le même réseau que Jeedom. La connexion passe par le proxy du plugin avec un token (voir [Tokens et rôles](#tokens-et-rôles)) : le daemon n'accepte aucune connexion directe.

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://192.168.1.79/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--allow-http",
        "--header", "Authorization: Bearer VOTRE_TOKEN_ICI"
      ]
    }
  }
}
```

> Remplacez `192.168.1.79` par l'IP de votre serveur Jeedom. L'URL exacte est affichée dans la page de configuration du plugin (onglet « Tokens & rôles », « Accès LAN »).

---

### Méthode 2 — SSE legacy *(retirée)*

Le transport SSE legacy n'est plus proposé. Si votre client utilisait `mcp-proxy --sse-url …jeemcp_proxy.php`, remplacez cette configuration par celle de la méthode 1 (ou de la méthode 3 depuis l'extérieur). Les clients qui ne savent pas parler Streamable HTTP peuvent passer par un pont : `mcp-proxy --transport streamablehttp` ou `mcp-remote` (configurations prêtes dans la fenêtre **Clients** du plugin).

---

### Méthode 3 — Accès externe via `jeemcp_proxy.php` + token Bearer

Le proxy PHP intégré au plugin gère l'authentification par token. C'est la méthode recommandée pour tout accès depuis l'extérieur du réseau local.

**Prérequis :**
1. Créer un token dans **Gestion des tokens** (page de configuration du plugin) en choisissant son rôle — voir [Tokens et rôles](#tokens-et-rôles). Les clients cloud (claude.ai, ChatGPT, Le Chat…) peuvent aussi se connecter par **OAuth**.
2. Votre Jeedom doit être accessible depuis l'extérieur via HTTPS (DNS Jeedom, reverse proxy, Cloudflare Tunnel…).

**Pour Streamable HTTP :**

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "https://votre-jeedom.exemple.fr/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--header", "Authorization: Bearer VOTRE_TOKEN_ICI"
      ]
    }
  }
}
```

> ⚠️ **Note sur le DNS Jeedom / OVH** : ces accès ne sont actuellement pas compatibles avec le service DNS interne Jeedom. Utilisez un nom de domaine personnel, Cloudflare Tunnel, ou un reverse proxy.

---

### Méthode 4 — Accès externe via Cloudflare Tunnel *(sans ouverture de port)*

Cloudflare Tunnel crée un tunnel chiffré entre votre serveur Jeedom et les serveurs Cloudflare, sans ouvrir aucun port entrant sur votre box/routeur.

**Avantages :** gratuit, HTTPS automatique, pas de redirection de port, fonctionne derrière CG-NAT.

Voir le tutoriel dédié → [Accès externe via Cloudflare Tunnel](cloudflare.md)

Une fois le tunnel configuré, utilisez la Méthode 3 avec l'URL Cloudflare Tunnel à la place de l'URL Jeedom.

---

### Méthode 5 — Accès externe via reverse proxy nginx/Apache

Si vous gérez déjà un serveur web devant Jeedom, faites-le pointer vers le **proxy du plugin** (le daemon refuse toute connexion qui ne vient pas de ce proxy). Le client IA envoie toujours son token dans l'en-tête `Authorization` :

```nginx
# Streamable HTTP
location /jeedom-mcp {
    proxy_pass http://IP_JEEDOM/plugins/mcp_jeedom/core/php/jeemcp_proxy.php;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_buffering off;
    proxy_cache off;
    proxy_read_timeout 300s;
}
```

---

### Récapitulatif des méthodes

| Méthode | Réseau requis | Auth | Complexité | Recommandé |
|---|---|---|---|---|
| 1 — LAN Streamable HTTP (proxy) | Local uniquement | Token Bearer | ⭐ | ✅ Usage quotidien local |
| 3 — Proxy PHP + token | Internet | Token Bearer ou OAuth | ⭐⭐ | ✅ Accès externe simple |
| 4 — Cloudflare Tunnel | Internet | Token Bearer ou OAuth | ⭐⭐ | ✅ Sans ouverture de port |
| 5 — Reverse proxy nginx | Internet | Token Bearer ou OAuth | ⭐⭐⭐ | Infra existante |

## Tokens et rôles

Chaque client IA se connecte avec son propre **token**, créé dans **Gestion des tokens** (page de configuration du plugin, onglet « Tokens & rôles »). Le token porte un **rôle** qui définit ce que le client peut faire.

| Rôle | Ce que le token peut faire |
|---|---|
| **Utilisateur limité** | Consulter la maison (états, historiques, analyses, journaux et caméras si autorisés) sans rien piloter ni modifier. Idéal pour un assistant vocal ou un accès tiers en consultation. |
| **Opérateur** | Lire l'état de la maison, piloter les équipements et variables de la whitelist, lancer des scénarios, consulter les journaux, envoyer des notifications, voir les caméras. |
| **Administrateur** | En plus : créer et modifier des scénarios, lire et écrire des fichiers, récupérer des pages Jeedom, maintenance du système, sauvegardes, mises à jour et plugins. |
| **Super-administrateur** | En plus : shell sur le serveur et redémarrages. À réserver à un client de confiance. |

> ℹ️ Ne confondez pas le rôle **Utilisateur limité** (propre à un token) avec le **mode lecture seule** de la box, qui bloque toutes les actions de tous les tokens. Dans la page de configuration, l'onglet « Capacités des rôles » propose, pour chaque rôle, un bouton **Voir les capacités** qui liste les outils et actions autorisés, en tenant compte de vos réglages.

**Droits effectifs = rôle du token, dans la limite du niveau maximum de la box.** Un token Administrateur ne peut pas écrire de fichiers si l'écriture de fichiers est désactivée sur la box.

### Détail des capacités

| Capacité | Opérateur | Administrateur | Super-admin | Case de la box qui la plafonne |
|---|:-:|:-:|:-:|---|
| Lire l'état, rechercher, historiques, analyses, infos système | ✅ | ✅ | ✅ | — |
| Piloter les commandes de la whitelist, variables, modes, présence | ✅ | ✅ | ✅ | — |
| Lancer, arrêter, activer des scénarios ; lire leur code | ✅ | ✅ | ✅ | — |
| Vues et designs (créer, modifier) | ✅ | ✅ | ✅ | — |
| Gérer les équipements (activer, renommer, déplacer) | Whitelist seulement | ✅ | ✅ | — |
| Journaux Jeedom | ✅ | ✅ | ✅ | Lecture des logs |
| Notifications | ✅ | ✅ | ✅ | Activer l'envoi de notifications |
| Caméras | ✅ | ✅ | ✅ | Activer les snapshots caméra |
| Créer et modifier des scénarios | ❌ | ✅ | ✅ | Activer l'écriture de scénarios |
| Lire et lister des fichiers | ❌ | ✅ | ✅ | Lecture de fichiers |
| Écrire des fichiers | ❌ | ✅ | ✅ | Écriture de fichiers |
| Récupérer des pages Jeedom | ❌ | ✅ | ✅ | Activer la récupération de pages HTML |
| Maintenance, sauvegardes, mises à jour, plugins, interactions, appels avancés (`rpc`) | ❌ | ✅ | ✅ | — |
| Shell sur le serveur | ❌ | ❌ | ✅ | Exécution locale |
| Redémarrer Jeedom | ❌ | ❌ | ✅ | Redémarrage Jeedom |
| Scénarios contenant du code PHP ou un redémarrage/arrêt de la box | ❌ | ❌ | ✅ | Activer l'écriture de scénarios |

Chaque client ne voit que les outils autorisés pour son rôle. Un changement de rôle, une désactivation ou une suppression de token s'applique **immédiatement**, sans redémarrer le daemon.

### Niveau maximum autorisé

Dans l'onglet **Capacités globales**, le **Niveau maximum autorisé** plafonne toute la box, quel que soit le rôle des tokens :

| Niveau | Capacités activées |
|---|---|
| **État seulement** | Lecture de l'état et pilotage des équipements. Ni journaux, ni notifications, ni caméras, ni fichiers. |
| **Pilotage** | + journaux, notifications, caméras (de quoi utiliser pleinement le rôle Opérateur). |
| **Avancé** | + écriture de scénarios, lecture et écriture de fichiers, pages Jeedom (rôle Administrateur). |
| **Complet** | + shell et redémarrage (rôle Super-administrateur). |

Les capacités restent réglables une par une (onglets « Capacités globales » et « Capacités des rôles ») ; le niveau affiche alors « Personnalisé ».

> ℹ️ Après une modification des capacités, **sauvegardez et redémarrez le daemon**. Les changements de rôle des tokens, eux, sont immédiats.

### Shell (Super-administrateur)

Le shell est réservé au rôle Super-administrateur et à la case **Exécution locale**. Pour un token donné, vous pouvez limiter le shell à une liste de commandes (option avancée du token, séparées par `|`, `*` comme joker : `systemctl status *|df -h`). Dans ce cas, les caractères qui enchaînent une autre commande (`;`, `&`, `|`, `` ` ``, `$`, `<`, `>`, parenthèses, accolades, retour à la ligne) sont refusés. Une liste noire permanente bloque en plus les commandes destructrices (formatage, `rm -rf`, reverse shell…).

### OAuth

Les clients cloud (claude.ai, ChatGPT, Le Chat…) peuvent se connecter par OAuth (à activer dans la configuration). Lors de l'autorisation, un **administrateur Jeedom** choisit le token dont le client reprendra le rôle. Le rôle est relu à chaque requête : changer le rôle de ce token ou le désactiver agit aussitôt sur le client OAuth.

### Autres réglages

**Mode lecture seule** (« Désactiver toutes les actions ») — bloque toutes les actions pour tous les tokens (pilotage, variables, notifications, écritures, maintenance…) ; la consultation reste complète.

**Fichiers** — la lecture est limitée à `/var/www/html/plugins`, `/var/www/html/core` et `/var/log`, l'écriture à `/var/www/html/plugins` et aux dossiers temporaires du plugin. Les fichiers de secrets (identifiants de base de données, jetons OAuth, clés privées) ne sont jamais accessibles, quel que soit le rôle.

**Audit des actions** — journal horodaté de chaque appel d'outil par l'IA (token, nom, arguments, résultat). Consultable et effaçable depuis l'onglet « Sécurité / Audit ».

## Profil maison

Le profil maison est un fichier JSON (`resources/data/home_profile.json`) lu à chaque session par l'IA pour personnaliser ses réponses : noms des habitants, animaux, horaires habituels, canal de notification par défaut…

Éditez-le via **Plugins → mcp_jeedom → Profil maison** (éditeur JSON avec validation) ou directement dans le fichier.

Exemple :
```json
{
  "household": { "members": ["Alice", "Bob"], "pets": ["Max le chien"] },
  "habits": { "wake_time": "07:30", "sleep_time": "23:00" },
  "preferences": { "notification_channel_cmd_id": 142 }
}
```

L'IA accède à ce profil via la ressource `jeedom://home_profile`.

## Whitelist des équipements

La whitelist permet de limiter les équipements et commandes accessibles par l'IA. Elle s'édite via **Plugins → mcp_jeedom → Whitelist**.

- **Autoriser tout** (défaut) : tous les équipements actifs sont accessibles.
- **Mode sélectif** : cochez uniquement les équipements et commandes à exposer.
- Chaque commande affiche son **generic_type** (badge vert), son nom, son ID et un champ *label personnalisé* pour renommer l'élément tel qu'il sera présenté à l'IA.
- **Synchroniser depuis Jeedom** recalcule la liste depuis l'état actuel de votre installation.

---

# Tutoriel d'utilisation

## Première connexion

Après avoir configuré `claude_desktop_config.json` et redémarré Claude Desktop :

1. Ouvrez une nouvelle conversation dans Claude Desktop.
2. Cliquez sur l'icône **🔧** (outils) pour vérifier que `jeedom_mcp` apparaît dans la liste.
3. Tapez simplement : **« Quel est l'état de ma maison ? »**
4. Claude appellera automatiquement `get_full_state` et vous présentera un résumé complet.

> 💡 Pour de meilleures réponses personnalisées, complétez le **profil maison** dans la page de configuration du plugin (*Plugins → mcp_jeedom → Profil maison*) — Claude connaîtra les prénoms des habitants, les habitudes et le canal de notification préféré.

## Découvrir votre installation

Ces premières questions permettent à Claude de construire une carte mentale de votre maison :

```
Donne-moi l'état complet de la maison.
Quelles pièces contiennent des capteurs de température ?
Montre-moi tous mes scénarios actifs.
Quels équipements ont une batterie faible ?
```

## Contrôler des équipements

Le workflow recommandé grâce aux generic_types :

1. **Trouver la commande** : *« Allume la lumière du salon »* → Claude utilise `find_command(generic_type="LIGHT_ON", room_name="salon")` automatiquement.
2. **Exécuter** : Claude appelle `execute_action` avec l'ID trouvé.

Vous pouvez aussi être direct :

```
Éteins toutes les lumières du rez-de-chaussée.
Mets le thermostat du salon à 20 degrés.
Ferme les volets de la chambre.
Ouvre la serrure du portail.
```

## Analyser des données

```
Quelle est la température moyenne du salon cette semaine ?
Montre-moi la consommation électrique des 24 dernières heures.
Y a-t-il eu des présences détectées cette nuit ?
```

## Comprendre et déboguer un scénario

```
Explique-moi la logique du scénario "Alarme nuit".
Le scénario "Arrivée maison" s'est-il bien exécuté aujourd'hui ?
Pourquoi le scénario de chauffage ne se déclenche-t-il pas ?
```

## Cameras et notifications

```
Montre-moi ce qu'il se passe dans le salon (caméra 3).
Y a-t-il quelqu'un dans le garage ?
Envoie-moi un message Telegram : "Les volets sont fermés."
Préviens-moi sur Slack si la température du cave descend sous 5°C.
```

## Diagnostiquer des problèmes

```
Y a-t-il des équipements en erreur ou injoignables ?
Quels plugins ont leur daemon arrêté ?
Quelles batteries sont faibles ?
Que s'est-il passé sur ma maison depuis ce matin ?
Montre-moi les 50 dernières lignes du log zigbee.
```

---

# Exemples d'utilisation

## Routine du matin

**Demande :** *« Prépare la maison pour le matin : allume les lumières de la cuisine et du couloir, monte les volets du salon, mets le thermostat à 21°C et dis-moi la météo du jour. »*

Claude va :
- Utiliser `find_command` avec `generic_type="LIGHT_ON"` pour chaque pièce
- Appeler `bulk_execute` pour lumières + volets en une requête
- Régler le thermostat via `find_command(generic_type="THERMOSTAT_SET_SETPOINT")` + `execute_action`
- Compléter avec une recherche météo web

## Audit énergétique

**Demande :** *« Analyse ma consommation électrique de la semaine dernière. Quels équipements consomment le plus ? »*

Claude va :
- Appeler `find_command(generic_type="POWER")` pour trouver tous les compteurs de puissance
- Appeler `get_statistics` sur chacun avec `hours=168` (7 jours)
- Comparer les moyennes, identifier les pics et anomalies
- Produire un rapport avec recommandations

## Mode vacances

**Demande :** *« Je pars en vacances du 15 au 28 juillet. Configure la maison en mode absence. »*

Claude peut :
- Lister et lire les scénarios via `get_scenario_code` pour comprendre leur logique
- Désactiver les scénarios de confort, activer les scénarios de sécurité
- Créer une variable `mode_maison = 'vacances'` via `set_variable`
- Vous demander confirmation avant chaque action critique

## Diagnostic réseau domotique

**Demande :** *« Mon réseau Zigbee a des problèmes depuis ce matin. Aide-moi à diagnostiquer. »*

Claude va :
- Appeler `get_dead_devices` pour identifier les nœuds perdus
- Utiliser `get_changes(minutes=480)` pour voir tous les événements depuis 8h
- Lire le log zigbee via `read_log` avec les 200 dernières lignes
- Analyser les erreurs et suggérer des actions correctives

## Comprendre un scénario

**Demande :** *« Quand je rentre le soir, qu'est-ce qui se passe exactement dans le scénario Arrivée maison ? Est-ce qu'il fonctionne bien ? »*

Claude va :
- Appeler `get_scenario_code` pour lire la logique complète du scénario
- Lire son log d'exécution via `get_scenario_log`
- Décrire le déroulé en langage naturel et signaler d'éventuelles anomalies

## Tableau de bord complet

**Demande :** *« Fais-moi un résumé complet de la maison : température de chaque pièce, état des lumières, fenêtres ouvertes, et consommation du moment. »*

Claude va :
- Appeler `get_full_state` pour obtenir tout l'état en un seul appel
- Filtrer par generic_type : `TEMPERATURE`, `LIGHT_STATE`, `DOOR_STATE`, `POWER`
- Présenter un résumé structuré et lisible

## Maintenance préventive

**Demande :** *« Donne-moi un bilan de santé complet de mon installation. »*

Claude va :
- Appeler `jeedom_api(action='healthReport')` pour obtenir en un seul appel : équipements en erreur, batteries faibles, messages système, daemons NOK et mises à jour disponibles
- Présenter une synthèse structurée avec les actions prioritaires

## Créer un équipement virtuel

**Demande :** *« Crée un équipement virtuel 'Météo ext' avec une commande température et une commande humidité. »*

Claude va :
- Appeler `jeedom_api(action='saveVirtualDevice', body={device:{name:'Météo ext', commands:[{name:'Température', type:'info', subType:'numeric', unite:'°C'}, {name:'Humidité', type:'info', subType:'numeric', unite:'%'}]}})` 
- Confirmer la création avec les IDs des commandes générées

## Surveillance et alertes

**Demande :** *« Vérifie toutes les caméras et envoie-moi un résumé par Telegram. »*

Claude va :
- Appeler `list_cameras` pour obtenir la liste
- Appeler `get_camera_snapshot` sur chacune et décrire la scène
- Appeler `list_notification_commands` pour trouver le canal Telegram
- Envoyer le résumé via `send_notification`

## Accès consultation pour un tiers

**Situation :** vous voulez qu'une autre personne puisse consulter l'état de la maison sans risque d'action accidentelle.

Créez-lui un token **Opérateur** avec une whitelist limitée aux équipements utiles, et choisissez le niveau maximum **État seulement**. Pour une consultation stricte, activez en plus le **mode lecture seule** : toutes les actions sont alors bloquées, pour tous les tokens de la box.

---

# Sécurité

## Modèle de sécurité

Le serveur MCP s'exécute en local sur votre serveur Jeedom avec les droits de l'utilisateur `www-data`. Les mesures de sécurité en place :

- **Token obligatoire** — toute connexion passe par le proxy `jeemcp_proxy.php` avec un token (ou une session OAuth). Le daemon n'écoute que sur `127.0.0.1` et refuse toute requête qui ne vient pas du proxy.
- **Rôles et niveau maximum** — le rôle du token (Opérateur, Administrateur, Super-administrateur) décide de ce que le client peut faire, dans la limite du niveau maximum de la box. Voir [Tokens et rôles](#tokens-et-rôles).
- **Whitelist** — l'IA ne voit et ne pilote que les équipements et commandes autorisés.
- **Capacités sensibles désactivées par défaut** — fichiers, shell, redémarrage, écriture de scénarios doivent être activés explicitement.
- **Fichiers** — accès limité à des dossiers autorisés ; les fichiers de secrets sont toujours refusés.
- **Double vérification** — un client ne voit que les outils de son rôle, et le droit est revérifié à chaque appel.
- **Mode lecture seule** — désactive toutes les actions en une option, sans modifier les autres réglages.
- **Audit log** — traçabilité de chaque action IA (token, arguments, résultat). Arguments sensibles masqués automatiquement.
- **Garde-fous du shell** — liste noire permanente de commandes dangereuses (rm -rf, mkfs, fork bomb, reverse shell…) et liste de commandes autorisées par token.

## Recommandations

> ⚠️ **Bonnes pratiques**
> - Un token par client IA, avec le rôle le plus bas suffisant : **Opérateur** pour l'usage quotidien.
> - Réservez **Super-administrateur** à un client de confiance, et limitez si possible son shell à une liste de commandes.
> - Gardez le niveau maximum sur **Pilotage** sauf besoin ; n'activez l'écriture de fichiers que temporairement.
> - Pour un accès tiers en consultation : token Opérateur + niveau **État seulement**, voire **mode lecture seule**.
> - Consultez régulièrement l'**audit log** pour détecter des usages anormaux.
> - Utilisez HTTPS pour tout accès externe (Cloudflare Tunnel, reverse proxy, DNS avec TLS).
> - En cas de doute, désactivez ou régénérez le token concerné : l'effet est immédiat.

## Accès externe sécurisé (nginx)

Pour exposer le serveur MCP via nginx, pointez vers le proxy du plugin (jamais vers le port du daemon, qui refuse les connexions directes). L'authentification reste celle du token :

```nginx
# /etc/nginx/sites-available/jeedom-mcp
# Streamable HTTP
location /jeedom-mcp/mcp {
    proxy_pass http://IP_JEEDOM/plugins/mcp_jeedom/core/php/jeemcp_proxy.php;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_buffering off;
    proxy_cache off;
    proxy_read_timeout 300s;
}
```

> Pour une solution sans ouverture de port, voir → [Accès externe via Cloudflare Tunnel](cloudflare.md).

---

*mcp_jeedom — Documentation | Plugin open-source pour Jeedom*
