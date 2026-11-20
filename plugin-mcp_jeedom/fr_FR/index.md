---
layout: default
title: MCP Server — Plugin MCP pour Jeedom
lang: fr_FR
pluginId: mcp_jeedom
---

# Présentation

Le plugin MCP AI Server (`mcp_jeedom`) transforme votre Jeedom en serveur **MCP (Model Context Protocol)**, le protocole ouvert créé par Anthropic pour connecter les IA à des systèmes externes.

Vos assistants IA (**Claude, ChatGPT, Le Chat, Cursor, VS Code…** et tout client MCP) peuvent alors consulter et piloter votre maison.

## Ce que l'IA peut faire dans votre maison

- Consulter l'état de la maison en un seul appel
- Piloter lumières, volets, thermostats, serrures et scénarios
- Analyser l'historique et les statistiques de vos capteurs
- Repérer les équipements en erreur et les batteries faibles
- Regarder et décrire les images de vos caméras
- Envoyer des notifications via vos plugins (Telegram, Mail…)
- Expliquer, déboguer et écrire vos scénarios

## Fonctionnalités

- 🤖 Transport **Streamable HTTP** (standard MCP)
- 🏠 Une trentaine d'outils (états, équipements, scénarios, analyses, système, fichiers, caméras…)
- 🔍 Recherche par type générique (`LIGHT_ON`, `TEMPERATURE`…)
- ⚙️ Whitelist des équipements et commandes exposés à l'IA
- 🔐 Tokens à **4 rôles** (Utilisateur limité, Opérateur, Administrateur, Super-administrateur), OAuth, **niveau maximum** réglable et **mode lecture seule**
- 📸 Snapshots caméra analysés par l'IA
- 📋 **Journal d'audit** de toutes les actions de l'IA
- 🗂️ Ressources MCP : carte des IDs, profil maison, guide opérationnel
- 🌐 Accès local (LAN) ou externe (DNS Jeedom, Cloudflare Tunnel, votre propre nom de domaine)

## Exemples d'utilisation

> *« Quel est l'état de la maison ? »* — résumé complet en quelques secondes.

> *« Analyse ma consommation électrique de la semaine. »* — statistiques et tendances.

> *« Y a-t-il quelqu'un dans le couloir ? »* — analyse du snapshot caméra.

> *« Prépare la maison pour la nuit. »* — plusieurs actions en une phrase.

> *« Explique-moi le scénario Alarme nuit et dis-moi s'il fonctionne bien. »* — lecture et diagnostic du code.

## Sécurité et contrôle

Vous définissez ce que l'IA peut voir et faire : whitelist des équipements, rôle de chaque token, niveau maximum de la box. Les capacités sensibles (fichiers, shell, redémarrage, écriture de scénarios) sont désactivées par défaut. Chaque action est tracée dans le journal d'audit.

---

> ## ⛔ AVERTISSEMENT — À LIRE AVANT TOUTE UTILISATION
>
> Ce plugin donne à une IA un accès direct à votre installation : lecture d'état, pilotage des équipements, gestion des scénarios et, si vous les activez, fichiers et shell.
>
> Le code est sécurisé avec soin, mais **aucun système n'est infaillible** :
>
> - 🔓 **Token volé** : quiconque possède un token a les droits de son rôle sur votre installation.
> - 🐛 **Bug du plugin** : une erreur peut provoquer une action non souhaitée ou exposer des données.
> - 🤖 **Erreur de l'IA** : un modèle peut mal comprendre une demande ou enchaîner des actions non prévues.
>
> ### Règles essentielles
>
> - ✅ **Ne connectez que des IA auxquelles vous faites confiance.** Vos états et vos demandes sont envoyés au fournisseur de l'IA.
> - ✅ Donnez à chaque client le **rôle le plus bas suffisant** (voir [Tokens et rôles](#tokens-et-rôles)).
> - ❌ **Ne partagez jamais** un token ni votre clé API Jeedom, même dans une capture d'écran.
> - ❌ **N'activez pas** l'exécution locale, l'écriture de fichiers ou le redémarrage en usage quotidien : seulement le temps d'un besoin précis.
>
> ### Responsabilité
>
> **L'utilisateur est seul responsable** de la configuration, de l'exposition réseau et de l'usage du plugin. Le développeur ne peut être tenu responsable des conséquences d'un accès non autorisé, d'une erreur de l'IA, d'un bug du plugin ou d'une mauvaise configuration.
>
> Voir la section [Sécurité](#sécurité).

---

# Qu'est-ce que MCP ?

## Définition

**MCP (Model Context Protocol)** est un protocole ouvert, publié par Anthropic en 2024, qui définit comment un modèle de langage (LLM) communique avec des outils et des sources de données externes. N'importe quel client MCP peut se connecter à n'importe quel serveur MCP sans code spécifique.

## Architecture

- **Client MCP** — l'application qui héberge l'IA (Claude, ChatGPT, Cursor…). Il se connecte au serveur et appelle ses outils.
- **Serveur MCP** — le processus qui expose des outils et des ressources. C'est le rôle de `mcp_jeedom`.
- **Transport** — **Streamable HTTP**. Les anciens transports SSE et STDIO ont été retirés : une configuration qui les utilisait passe automatiquement en Streamable HTTP à la mise à jour.

> ℹ️ **Important**  
> L'IA ne s'exécute pas sur votre Jeedom : elle tourne chez son fournisseur (Anthropic, OpenAI, Mistral…) ou sur votre propre machine si vous utilisez un modèle local.  
> Le serveur MCP fait le pont entre l'IA et Jeedom : il exécute les appels, l'IA décide.

## Protocole de communication

La communication suit **JSON-RPC 2.0** :

1. Le client demande `tools/list` → le serveur renvoie les outils autorisés pour ce token.
2. L'IA choisit un outil et envoie `tools/call` avec ses arguments.
3. Le serveur exécute l'action dans Jeedom et renvoie le résultat.
4. L'IA s'en sert pour répondre.

---

# Rôle du plugin mcp_jeedom

## Vue d'ensemble

`mcp_jeedom` démarre un **serveur MCP en arrière-plan** (daemon Python). Les clients s'y connectent par le **proxy du plugin** (`jeemcp_proxy.php`), qui vérifie le token. Le daemon appelle ensuite Jeedom pour :

- connaître l'état de votre maison ;
- piloter vos équipements ;
- lancer, analyser, déboguer et écrire vos scénarios ;
- consulter l'historique et les statistiques ;
- repérer les changements récents, les batteries faibles, les équipements injoignables ;
- lire les logs et les fichiers du serveur *(optionnel)*.

## Prérequis techniques

| Composant | Technologie |
|---|---|
| Jeedom | Core 4.6 ou plus récent |
| Système | Debian 12 ou plus récent (Debian 11 n'est plus pris en charge) |
| Daemon | Python 3, SDK MCP officiel, uvicorn (installés avec les dépendances) |
| Transport | Streamable HTTP |

---

# Capacités disponibles

## Outils

Les outils sont les fonctions que l'IA peut appeler. Chaque client ne voit que ceux de son rôle (voir [Tokens et rôles](#tokens-et-rôles)).

Le mode par défaut, **Fusionnés**, regroupe les fonctions en une vingtaine d'outils à actions. Le mode **Individuels** (une cinquantaine d'outils, un par fonction) convient mieux aux petits modèles (Haiku, GPT-4o-mini, Gemini Flash). Le choix se fait dans la configuration du plugin (« Mode outils MCP »).

### Maison et équipements

| Outil | Ce qu'il fait |
|---|---|
| `state` | **À utiliser en premier.** État complet de la maison, d'une pièce, d'une ou plusieurs commandes ; recherche par type générique ; changements récents |
| `devices` | Lister et rechercher les équipements, détail d'un équipement, batteries, équipements injoignables, résumé d'une pièce, usages d'une commande |
| `execute` | Exécuter une ou plusieurs commandes action |
| `home` | Modes de la maison (absent, nuit, vacances…), présence, planning des modes |
| `variables` | Lire, modifier, supprimer les variables globales |
| `analytics` | Anomalies, sécurité, énergie, météo, activité, statistiques, historique, timeline, durée dans un état |
| `lookup_generic_type` | Catalogue des types génériques Jeedom |

### Scénarios

| Outil | Ce qu'il fait |
|---|---|
| `scenarios` | Lister, lancer, arrêter, activer ou désactiver, lire le code, simuler |
| `scenario_write` | Créer, modifier, dupliquer, importer, exporter, supprimer un scénario ; valider ; générer les conditions sur déclencheurs *(capacité « écriture de scénarios »)* |

> 🆕 **Scénarios multi-déclencheurs (Jeedom ≥ 4.5)** — l'IA génère les conditions avec les tags `#trigger_id#`, `#trigger_name#`, `#trigger_value#` (qui remplacent les fonctions dépréciées `trigger()`, `triggerId()`, `triggerValue()`), pour une condition unique ou un squelette si/sinon-si complet sur plusieurs commandes déclenchantes. L'ancienne syntaxe est signalée par un avertissement dans les scénarios importés ou modifiés.

### Vues et designs

| Outil | Ce qu'il fait |
|---|---|
| `views` | Lister et lire les vues et designs ; créer une vue, ses zones et ses tableaux de commandes ; convertir la tuile d'un équipement en tableau compact (réversible) |

Exemple : *« Crée une vue Salon avec un tableau de commandes pour les lumières et les volets. »*

### Système et administration

| Outil | Ce qu'il fait |
|---|---|
| `system` | Santé, ressources, diagnostic, sauvegardes, mises à jour, maintenance |
| `plugins` | État des démons et dépendances, réparation, activation, installation, configuration |
| `market` | Rechercher un plugin sur le Market |
| `crons` | Tâches planifiées de Jeedom : lister, piloter |
| `messages` | Centre de messages : lire, acquitter |
| `interactions` | Interactions Jeedom (lister, tester, créer, régénérer…) |
| `device_control` / `object_control` | Administration des équipements (activer, renommer, déplacer…) et des objets (pièces) |
| `set_cmd_widget` | Widget d'une commande |
| `rpc` | Appels composites avancés (bilan de santé, équipement virtuel, duplication de scénario…) |

### Capacités optionnelles

> ⚠️ Désactivées par défaut (sauf la lecture des logs). Chacune s'active dans la configuration du plugin.

| Outil | Ce qu'il fait |
|---|---|
| `logs` | Lister, lire, rechercher, analyser les logs ; log d'un scénario |
| `files` | Lire, lister, écrire, supprimer des fichiers (dossiers autorisés seulement) |
| `camera` | Lister les caméras, snapshot analysé par l'IA, description texte, envoi d'un snapshot, personnes inconnues |
| `notify` | Lister les canaux de notification (Telegram, Mail, Pushover…) et envoyer un message |
| `fetch_jeedom_page` | Récupérer le HTML d'une page Jeedom |
| `execute_local` | Commande shell sur le serveur, avec garde-fous *(Super-administrateur)* |
| `restart_jeedom` / `restart_daemon` | Redémarrer Jeedom ou le daemon MCP *(Super-administrateur)* |

---

## Ressources

Les ressources MCP sont des documents que le client peut lire sans appel d'outil. Certains clients les affichent ou les ajoutent au contexte.

| URI | Contenu |
|---|---|
| `jeedom://overview` | Liste commentée des ressources |
| `jeedom://ai_context` | Contexte domotique opérationnel |
| `jeedom://instructions` | Guide opérationnel de l'IA |
| `jeedom://best_practices` | Bonnes pratiques (économie de tokens, pièges des scénarios) |
| `jeedom://home_profile` | Profil de la maison (voir [Profil maison](#profil-maison)) |
| `jeedom://full_map` | Carte pièces → équipements → commandes avec IDs et types génériques |
| `jeedom://generic_types` | Commandes regroupées par type générique |
| `jeedom://scenario_schema` | Format des blocs de scénarios et syntaxe des conditions |
| `jeedom://views_guide` | Guide des vues et designs |
| `jeedom://troubleshooting` | Problèmes connus |
| `jeedom://security_diagnostic` | Diagnostic de sécurité |
| `jeedom://summary`, `jeedom://jeeobjects`, `jeedom://devices`, `jeedom://scenarios`, `jeedom://variables`, `jeedom://messages`, `jeedom://plugins` | Résumés, objets, équipements, scénarios, variables, messages, plugins |
| `jeedom://jeeobject/{id}` | État d'un objet (pièce) |

---

## Types génériques

Chaque commande Jeedom peut porter un **type générique** qui décrit son rôle, indépendamment de son nom. Le plugin s'en sert pour que l'IA trouve la bonne commande.

| Catégorie | Types génériques |
|---|---|
| Lumières | `LIGHT_STATE`, `LIGHT_ON`, `LIGHT_OFF`, `LIGHT_SLIDER`, `LIGHT_COLOR` |
| Température / Humidité | `TEMPERATURE`, `HUMIDITY` |
| Chauffage | `THERMOSTAT_SETPOINT`, `THERMOSTAT_SET_SETPOINT`, `THERMOSTAT_MODE` |
| Énergie | `POWER` (W), `ENERGY` (kWh) |
| Volets | `FLAP_STATE`, `FLAP_UP`, `FLAP_DOWN`, `FLAP_STOP`, `FLAP_SLIDER` |
| Serrures | `LOCK_STATE`, `LOCK_OPEN`, `LOCK_CLOSE` |
| Sécurité | `ALARM_STATE`, `ALARM_MODE`, `PRESENCE`, `MOTION`, `OPENING`, `OPENING_WINDOW`, `SMOKE` |
| Divers | `BATTERY`, `SWITCH_STATE`, `SWITCH_ON`, `SWITCH_OFF` |

Si vos commandes n'ont pas de type générique, l'assistant de whitelist peut en proposer.

---

## Extensions MCP de plugins tiers

Un autre plugin Jeedom peut exposer ses propres outils MCP **sans modifier `mcp_jeedom`**, en déposant deux fichiers :

```
plugins/{votre_plugin}/resources/mcp_extension/mcp_tools.json    ← définitions (schémas)
plugins/{votre_plugin}/resources/mcp_extension/McpExtension.php  ← exécution
```

Ses outils apparaissent avec le préfixe `ext__{votre_plugin}__{nom_outil}` (par exemple `ext__alexaapiv2__speak`).

Le guide développeur est dans `plugins/mcp_jeedom/resources/mcp_extension/EXTENSION_GUIDE.md`.

---

# Installation et configuration

## Installation

1. Dans Jeedom, aller dans **Plugins → Gestion des plugins → Market**.
2. Rechercher « MCP AI Server » (auteur Limad44).
3. Installer le plugin, l'activer, puis installer les dépendances.
4. Démarrer le daemon depuis la page de configuration et vérifier que son statut est **OK**.

## Assistant de configuration

Tant qu'il n'a pas été terminé, un **assistant de configuration** s'ouvre à l'arrivée sur la page du plugin, en 5 étapes :

1. **Santé** — vérifie que le daemon est démarré (bouton pour le lancer).
2. **Profil d'usage** — 3 préréglages : Consultation (mode lecture seule), Pilotage, Avancé (avec écriture de scénarios). Celui qui correspond à votre configuration actuelle est présélectionné.
3. **Whitelist** — génère la liste des équipements autorisés depuis votre installation.
4. **Premier token** — crée un token **Opérateur** (par défaut) ou **Administrateur**. Voir [Tokens et rôles](#tokens-et-rôles).
5. **Connexion client** — donne l'URL à fournir à votre IA et teste l'accès externe s'il est configuré.

Chaque étape peut être passée, et l'assistant se rouvre depuis la vignette **Assistant**. Il ne touche jamais à l'exécution locale, à l'écriture de fichiers ni au redémarrage : ces cases restent à activer à la main.

## Prérequis côté client

- Le poste client joint Jeedom par son adresse web habituelle. Le daemon n'écoute que sur la box elle-même : toute connexion passe par le proxy du plugin, avec un token.
- Un client qui sait envoyer un en-tête `Authorization` n'a besoin de rien d'autre. Sinon, un pont local (`mcp-proxy`) fait la liaison : la fenêtre **Clients** du plugin génère la configuration.

## Configuration des clients IA

La fenêtre **Clients** de la page du plugin génère la configuration prête à copier pour chaque client (Claude Desktop, Claude Code, Cursor, VS Code, Codex, Windsurf, Continue, Zed, JetBrains, Raycast…), avec votre URL et votre token. Voir aussi le guide [Connexion des clients IA](clients.md).

### Méthode 1 — Réseau local (LAN)

Le poste client est sur le même réseau que Jeedom. Il se connecte au proxy du plugin avec un token (voir [Tokens et rôles](#tokens-et-rôles)) :

```
http://IP_JEEDOM/plugins/mcp_jeedom/core/php/jeemcp_proxy.php
```

Exemple pour Claude Desktop (`claude_desktop_config.json`), avec le pont `mcp-proxy` lancé par `uvx` :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "command": "uvx",
      "args": [
        "--with", "mcp<2.0.0",
        "mcp-proxy", "--transport", "streamablehttp",
        "http://IP_JEEDOM/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--headers", "Authorization", "Bearer VOTRE_TOKEN"
      ]
    }
  }
}
```

> L'URL exacte est affichée dans la page de configuration du plugin (onglet « Tokens & rôles », « Accès LAN »).

### Méthode 2 — Accès externe par le proxy du plugin

Même URL, mais par l'adresse externe de votre Jeedom en HTTPS (DNS Jeedom, votre nom de domaine…) :

```
https://votre-jeedom.exemple.fr/plugins/mcp_jeedom/core/php/jeemcp_proxy.php
```

Le client envoie un token, ou se connecte par **OAuth** (claude.ai, ChatGPT, Le Chat…). Voir [OAuth](#oauth).

> ⚠️ **DNS Jeedom (`*.jeedom.link`)** : si le plugin **mcpIA** est installé, désactivez-le. Il intercepte les adresses OAuth et empêche les clients cloud de se connecter.

### Méthode 3 — Cloudflare Tunnel *(sans ouverture de port)*

Cloudflare Tunnel relie votre Jeedom aux serveurs Cloudflare sans ouvrir de port sur votre box : gratuit, HTTPS automatique, fonctionne derrière un CG-NAT. Une fois le tunnel en place, utilisez la méthode 2 avec l'adresse du tunnel.

Tutoriel → [Accès externe via Cloudflare Tunnel](cloudflare.md)

### Récapitulatif

| Méthode | Réseau | Authentification | Pour qui |
|---|---|---|---|
| 1 — LAN | Local | Token | Usage à la maison |
| 2 — Proxy du plugin en externe | Internet | Token ou OAuth | Jeedom déjà joignable en HTTPS |
| 3 — Cloudflare Tunnel | Internet | Token ou OAuth | Sans ouverture de port |

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
| Lancer et arrêter des scénarios ; lire leur code | ✅ | ✅ | ✅ | — |
| Activer ou désactiver des scénarios | ❌ | ✅ | ✅ | — |
| Lire les vues et designs | ✅ | ✅ | ✅ | — |
| Créer et modifier des vues et designs | ❌ | ✅ | ✅ | — |
| Gérer les équipements et les pièces (activer, renommer, déplacer) | ❌ | ✅ | ✅ | — |
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

Le profil maison est un texte libre (Markdown, 16 Ko maximum) que l'IA lit pour personnaliser ses réponses : habitants, pièces clés, habitudes, préférences (température cible, canal de notification…).

Dans la page de configuration du plugin : **Éditer le profil maison** pour l'écrire, ou **Générer depuis Jeedom** pour obtenir un premier jet à partir de votre installation.

Exemple :

```markdown
# Habitants
- Alice et Bob, un chien (Max)

# Habitudes
- Lever 7 h 30, coucher 23 h
- Chauffage à 20 °C le jour, 17 °C la nuit

# Préférences
- Notifications par Telegram
```

L'IA y accède par la ressource `jeedom://home_profile`.

## Whitelist des équipements

La whitelist décide quels équipements et commandes l'IA peut voir et piloter. Elle s'applique à tous les tokens, quel que soit leur rôle. Tant qu'elle n'est pas configurée, l'IA n'a accès à aucun équipement.

- **Gérer la whitelist** (page de configuration) : cochez les équipements et commandes à exposer.
- Chaque commande affiche son type générique, son nom, son ID et un *libellé personnalisé* pour la présenter autrement à l'IA.
- L'assistant de configuration génère une première whitelist depuis votre installation.

---

# Tutoriel d'utilisation

## Première connexion

Une fois le client configuré (et redémarré s'il le faut) :

1. Vérifiez dans le client que le serveur `jeedom-mcp` est connecté et que ses outils apparaissent.
2. Demandez : **« Quel est l'état de ma maison ? »**
3. L'IA appelle `state` et vous présente un résumé.

> 💡 Complétez le **profil maison** (page de configuration du plugin) : l'IA connaîtra les habitants, les habitudes et le canal de notification préféré.

## Découvrir votre installation

Quelques premières questions :

```
Donne-moi l'état complet de la maison.
Quelles pièces contiennent des capteurs de température ?
Montre-moi tous mes scénarios actifs.
Quels équipements ont une batterie faible ?
```

## Piloter des équipements

L'IA trouve la commande (par son nom ou son type générique, par exemple `LIGHT_ON` dans le salon), puis l'exécute avec `execute`. Vous pouvez donc parler naturellement :

```
Éteins toutes les lumières du rez-de-chaussée.
Mets le thermostat du salon à 20 degrés.
Ferme les volets de la chambre.
```

Les actions sensibles (alarme, serrure, portail, suppression…) demandent votre confirmation avant d'être exécutées.

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

## Caméras et notifications

```
Montre-moi ce qu'il se passe dans le salon (caméra 3).
Y a-t-il quelqu'un dans le garage ?
Envoie-moi un message Telegram : "Les volets sont fermés."
Crée un scénario qui me prévient sur Telegram si la cave descend sous 5 °C.
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

*« Allume les lumières de la cuisine et du couloir, monte les volets du salon, mets le thermostat à 21 °C et donne-moi la météo du jour. »*

L'IA cherche les commandes avec `state`, les exécute ensemble avec `execute`, puis lit la météo avec `analytics`.

## Audit énergétique

*« Analyse ma consommation électrique de la semaine dernière. Quels équipements consomment le plus ? »*

L'IA repère les compteurs (`POWER`, `ENERGY`), calcule leurs statistiques sur 7 jours avec `analytics`, compare et signale les pics.

## Mode vacances

*« Je pars en vacances du 15 au 28 juillet. Passe la maison en mode absence. »*

L'IA lit vos scénarios pour comprendre leur logique, propose d'activer le mode vacances (`home`) et les scénarios utiles, et vous demande confirmation avant chaque action sensible.

## Diagnostic Zigbee

*« Mon réseau Zigbee a des problèmes depuis ce matin. Aide-moi à diagnostiquer. »*

L'IA liste les équipements injoignables (`devices`), les changements depuis le matin (`state`), lit le log du plugin (`logs`) et propose des corrections.

## Comprendre un scénario

*« Quand je rentre le soir, que fait exactement le scénario Arrivée maison ? Est-ce qu'il fonctionne bien ? »*

L'IA lit le code du scénario et son log, décrit le déroulé en langage courant et signale les anomalies.

## Bilan de santé

*« Fais-moi un bilan de santé de mon installation. »*

En un appel : équipements en erreur, batteries faibles, messages système, démons arrêtés, mises à jour disponibles. L'IA en tire les actions prioritaires.

## Surveillance caméras

*« Regarde toutes les caméras et envoie-moi un résumé par Telegram. »*

L'IA prend un snapshot de chaque caméra (`camera`), décrit les scènes et envoie le résumé (`notify`).

## Accès consultation pour un tiers

**Situation :** vous voulez qu'une autre personne puisse consulter l'état de la maison sans risque d'action accidentelle.

Créez-lui un token **Utilisateur limité** : il consulte la maison sans rien piloter ni modifier, sans toucher aux autres tokens. Le **mode lecture seule**, lui, bloque les actions de tous les tokens de la box.

---

# Sécurité

## Modèle de sécurité

Le serveur MCP s'exécute en local sur votre serveur Jeedom avec les droits de l'utilisateur `www-data`. Les mesures de sécurité en place :

- **Token obligatoire** — toute connexion passe par le proxy `jeemcp_proxy.php` avec un token (ou une session OAuth). Le daemon n'écoute que sur `127.0.0.1` et refuse toute requête qui ne vient pas du proxy.
- **Rôles et niveau maximum** — le rôle du token (Utilisateur limité, Opérateur, Administrateur, Super-administrateur) décide de ce que le client peut faire, dans la limite du niveau maximum de la box. Voir [Tokens et rôles](#tokens-et-rôles).
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
> - Pour un accès tiers en consultation : token **Utilisateur limité**.
> - Consultez régulièrement l'**audit log** pour détecter des usages anormaux.
> - Utilisez HTTPS pour tout accès externe (DNS Jeedom, Cloudflare Tunnel, votre nom de domaine).
> - En cas de doute, désactivez ou régénérez le token concerné : l'effet est immédiat.
