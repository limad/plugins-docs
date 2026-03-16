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

- 🤖 Transport **Streamable HTTP** (standard MCP 2025) et SSE legacy
- 🏠 Plus de **35 outils** couvrant l'ensemble de l'API Jeedom
- 🔍 Recherche sémantique par type de commande (`LIGHT_ON`, `TEMPERATURE`…)
- ⚙️ Whitelist des équipements et commandes exposés à l'IA
- 🔐 Token Bearer pour l'accès externe, **mode lecture seule** disponible
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

> ⚠️ **Avertissement** — Ce plugin connecte une IA à votre installation domotique. L'utilisateur est seul responsable de sa configuration. Ni le développeur ni Domadoo ne peuvent être tenus responsables des conséquences d'une erreur de l'IA ou d'un accès non autorisé. Consultez la section [Sécurité](#7-sécurité) avant de démarrer.

---

# 1. Qu'est-ce que MCP ?

## 1.1 Définition

**MCP (Model Context Protocol)** est un protocole ouvert standardisé, publié par Anthropic en 2024, qui définit comment un modèle de langage (LLM) communique avec des outils et des sources de données externes.

Avant MCP, chaque intégration LLM ↔ outil était développée sur-mesure, rendant l'écosystème fragmenté et difficile à maintenir. MCP introduit un contrat commun : n'importe quel client MCP (Claude Desktop, Cursor, Continue…) peut se connecter à n'importe quel serveur MCP sans code spécifique de chaque côté.

## 1.2 Architecture

MCP repose sur trois composants :

- **Client MCP** — l'application hébergeant le LLM (ex : Claude Desktop). Il initie la connexion et envoie les requêtes d'outils.
- **Serveur MCP** — un processus exposant des capacités via le protocole (outils, ressources, prompts). C'est le rôle de `mcp_jeedom`.
- **Transport** — le canal de communication. Deux modes : **Streamable HTTP** (recommandé, standard 2025) et **SSE** (legacy) pour le réseau, **STDIO** pour un pipe local.

> ℹ️ **Important**  
> Le LLM ne s'exécute jamais sur votre serveur Jeedom — il tourne chez Anthropic (ou en local).  
> Le serveur MCP est simplement un pont entre le LLM et l'API Jeedom.  
> Toutes les décisions intelligentes restent côté LLM ; le serveur n'est qu'un exécuteur.

## 1.3 Protocole de communication

La communication suit **JSON-RPC 2.0** sur le transport choisi. Un échange typique :

1. Le client envoie `tools/list` → le serveur retourne la liste des outils disponibles.
2. Le LLM choisit un outil et envoie `tools/call` avec les arguments.
3. Le serveur exécute l'action (appel API Jeedom) et retourne le résultat en texte.
4. Le LLM intègre le résultat dans sa réponse à l'utilisateur.

---

# 2. Rôle du plugin mcp_jeedom

## 2.1 Vue d'ensemble

`mcp_jeedom` est un plugin Jeedom qui démarre un **serveur MCP en arrière-plan** (daemon Python). Ce serveur traduit les appels MCP en requêtes vers l'API JSON-RPC interne de Jeedom, permettant à un LLM de :

- Connaître l'état complet de votre maison en temps réel
- Contrôler vos équipements (lumières, volets, thermostats…) par type sémantique
- Déclencher, analyser et déboguer vos scénarios
- Consulter l'historique et les statistiques de valeurs
- Détecter les changements récents et l'état des batteries
- Accéder aux logs et fichiers du serveur *(optionnel, sécurisé)*

## 2.2 Stack technique

| Composant | Technologie |
|---|---|
| Daemon | Python 3.11 + anyio |
| Protocole | MCP SDK officiel Anthropic v1.26 |
| Serveur HTTP | uvicorn + ASGI pur |
| Transport par défaut | Streamable HTTP (MCP spec 2025) |
| API Jeedom | JSON-RPC interne |
| Plugin PHP | Jeedom Core 4.4+ |

---

# 3. Capacités disponibles

## 3.1 Outils (Tools)

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

### Scénarios

| Outil | Description |
|---|---|
| `list_scenarios` | Liste tous les scénarios avec leur état |
| `trigger_scenario` | Déclenche un scénario |
| `stop_scenario` | Arrête un scénario en cours |
| `set_scenario_active` | Active ou désactive un scénario |
| `get_scenario_code` | **Lit le code et la logique complète d'un scénario** (pour comprendre/déboguer) |
| `get_scenario_log` | Log d'exécution d'un scénario *(optionnel — voir §3.1 accès fichiers)* |

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

## 3.2 Ressources (Resources)

Les ressources MCP sont des URI lisibles directement par le client, sans appel d'outil explicite. Certains clients les affichent dans un panneau latéral ou les injectent automatiquement dans le contexte.

### Ressources de contexte IA *(nouvelles)*

| URI | Contenu | Usage recommandé |
|---|---|---|
| `jeedom://ai_context` | Architecture complète du plugin en JSON | À lire **en priorité** à la première connexion |
| `jeedom://instructions` | Guide opérationnel : workflows, règles, generic_types | Référence pour l'IA pendant la session |
| `jeedom://home_profile` | Profil de la maison : habitants, habitudes, préférences | Personnalise les réponses à votre installation |
| `jeedom://full_map` | Carte pièces→équipements→commandes avec IDs et generic_type | Index complet pour trouver tout cmd_id sans appel supplémentaire |
| `jeedom://generic_types` | Index des commandes groupées par LIGHT_ON / TEMPERATURE / POWER… | Trouver instantanément tous les capteurs d'un type |

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

## 3.3 Generic types — intelligence sémantique

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

## 3.4 Comparaison avec d'autres serveurs MCP domotique

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
| Accès externe gratuit | ✅ | ✅ | ✅ | payant |

*(opt.) = optionnel, sécurisé par whitelist de répertoires*

---

# 4. Installation et configuration

## 4.1 Installation

1. Dans Jeedom, aller dans **Plugins → Gestion des plugins → Installer depuis GitHub**.
2. **Installer les dépendances** depuis la page du plugin (Python venv + librairies MCP).
3. Démarrer le daemon depuis la page de configuration.
4. Vérifier que le statut affiche **✓ Actif**.

## 4.2 Prérequis côté client

- Le port **8765** (ou celui configuré) doit être accessible depuis le poste client.
- Pour le transport **Streamable HTTP** (défaut) : aucun outil supplémentaire requis — connexion directe.
- Pour le transport **SSE** (legacy) : `uvx` ou `npx` pour lancer `mcp-proxy`.

## 4.3 Configuration Claude Desktop

Éditez le fichier `claude_desktop_config.json` :

| OS | Chemin |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

---

### Méthode 1 — Accès local LAN (Streamable HTTP — recommandé)

Connexion directe sans outil intermédiaire. Nécessite que le poste client soit sur le même réseau que Jeedom.

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://192.168.1.79:8765/mcp"
      ]
    }
  }
}
```

> Remplacez `192.168.1.79` par l'IP de votre serveur Jeedom et `8765` par le port configuré.

---

### Méthode 2 — Accès local LAN (SSE legacy)

Si vous utilisez encore le transport SSE (non recommandé pour les nouveaux déploiements) :

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://192.168.1.79:8765/sse"
      ]
    }
  }
}
```

---

### Méthode 3 — Accès externe via `jeemcp_proxy.php` + token Bearer

Le proxy PHP intégré au plugin gère l'authentification par token et supporte les deux transports. C'est la méthode recommandée pour tout accès depuis l'extérieur du réseau local.

**Prérequis :**
1. Générer un token dans la page de configuration du plugin (*Token d'accès externe → Générer*).
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

**Pour SSE legacy (si votre client ne supporte pas encore Streamable HTTP) :**

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-proxy",
        "--sse-url", "https://votre-jeedom.exemple.fr/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--headers", "{\"Authorization\": \"Bearer VOTRE_TOKEN_ICI\"}"
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

Si vous gérez déjà un serveur web sur votre réseau (nginx, Apache, Caddy), vous pouvez proxifier directement le daemon MCP :

```nginx
# Streamable HTTP
location /jeedom-mcp {
    proxy_pass http://127.0.0.1:8765/mcp;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_buffering off;
    proxy_cache off;
    auth_basic "MCP Jeedom";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

Voir §7.3 pour la configuration complète.

---

### Récapitulatif des méthodes

| Méthode | Réseau requis | Auth | Complexité | Recommandé |
|---|---|---|---|---|
| 1 — LAN Streamable HTTP direct | Local uniquement | Aucune | ⭐ | ✅ Usage quotidien local |
| 2 — LAN SSE direct | Local uniquement | Aucune | ⭐ | Legacy uniquement |
| 3 — Proxy PHP + token | Internet | Bearer token | ⭐⭐ | ✅ Accès externe simple |
| 4 — Cloudflare Tunnel | Internet | Bearer token | ⭐⭐ | ✅ Sans ouverture de port |
| 5 — Reverse proxy nginx | Internet | Basic Auth | ⭐⭐⭐ | Infra existante |

## 4.4 Options de sécurité et permissions

Dans la page de configuration du plugin :

**Accès fichiers**
- **Lecture des logs** — activée par défaut, recommandée pour le diagnostic.
- **Lecture de fichiers** — accès limité à `/plugins`, `/core`, `/var/log`.
- **Écriture de fichiers** — accès limité à `/plugins` et `/tmp/mcp_jeedom`. À n'activer que si nécessaire.
- **Redémarrage Jeedom** — risque élevé, désactivé par défaut.

**Caméras** — active `list_cameras` et `get_camera_snapshot`. Nécessite le plugin Camera.

**Notifications sortantes** — active `list_notification_commands` et `send_notification`.

**Mode lecture seule** — désactive toutes les actions (`execute_action`, `bulk_execute`, `set_variable`, `send_notification`…) sans toucher aux permissions de lecture. Utile pour un accès consultation uniquement.

**Audit des actions** — journal horodaté de chaque appel d'outil par l'IA (nom, arguments, résultat). Consultable et effaçable depuis la page de configuration.

> ℹ️ Après toute modification des options, **redémarrez le daemon** pour appliquer les changements.

## 4.5 Profil maison

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

## 4.6 Whitelist des équipements

La whitelist permet de limiter les équipements et commandes accessibles par l'IA. Elle s'édite via **Plugins → mcp_jeedom → Whitelist**.

- **Autoriser tout** (défaut) : tous les équipements actifs sont accessibles.
- **Mode sélectif** : cochez uniquement les équipements et commandes à exposer.
- Chaque commande affiche son **generic_type** (badge vert), son nom, son ID et un champ *label personnalisé* pour renommer l'élément tel qu'il sera présenté à l'IA.
- **Synchroniser depuis Jeedom** recalcule la liste depuis l'état actuel de votre installation.

---

# 5. Tutoriel d'utilisation

## 5.1 Première connexion

Après avoir configuré `claude_desktop_config.json` et redémarré Claude Desktop :

1. Ouvrez une nouvelle conversation dans Claude Desktop.
2. Cliquez sur l'icône **🔧** (outils) pour vérifier que `jeedom_mcp` apparaît dans la liste.
3. Tapez simplement : **« Quel est l'état de ma maison ? »**
4. Claude appellera automatiquement `get_full_state` et vous présentera un résumé complet.

> 💡 Pour de meilleures réponses personnalisées, complétez le **profil maison** dans la page de configuration du plugin (*Plugins → mcp_jeedom → Profil maison*) — Claude connaîtra les prénoms des habitants, les habitudes et le canal de notification préféré.

## 5.2 Découvrir votre installation

Ces premières questions permettent à Claude de construire une carte mentale de votre maison :

```
Donne-moi l'état complet de la maison.
Quelles pièces contiennent des capteurs de température ?
Montre-moi tous mes scénarios actifs.
Quels équipements ont une batterie faible ?
```

## 5.3 Contrôler des équipements

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

## 5.4 Analyser des données

```
Quelle est la température moyenne du salon cette semaine ?
Montre-moi la consommation électrique des 24 dernières heures.
Y a-t-il eu des présences détectées cette nuit ?
```

## 5.5 Comprendre et déboguer un scénario

```
Explique-moi la logique du scénario "Alarme nuit".
Le scénario "Arrivée maison" s'est-il bien exécuté aujourd'hui ?
Pourquoi le scénario de chauffage ne se déclenche-t-il pas ?
```

## 5.6 Cameras et notifications

```
Montre-moi ce qu'il se passe dans le salon (caméra 3).
Y a-t-il quelqu'un dans le garage ?
Envoie-moi un message Telegram : "Les volets sont fermés."
Préviens-moi sur Slack si la température du cave descend sous 5°C.
```

## 5.7 Diagnostiquer des problèmes

```
Y a-t-il des équipements en erreur ou injoignables ?
Quels plugins ont leur daemon arrêté ?
Quelles batteries sont faibles ?
Que s'est-il passé sur ma maison depuis ce matin ?
Montre-moi les 50 dernières lignes du log zigbee.
```

---

# 6. Exemples d'utilisation

## 6.1 Routine du matin

**Demande :** *« Prépare la maison pour le matin : allume les lumières de la cuisine et du couloir, monte les volets du salon, mets le thermostat à 21°C et dis-moi la météo du jour. »*

Claude va :
- Utiliser `find_command` avec `generic_type="LIGHT_ON"` pour chaque pièce
- Appeler `bulk_execute` pour lumières + volets en une requête
- Régler le thermostat via `find_command(generic_type="THERMOSTAT_SET_SETPOINT")` + `execute_action`
- Compléter avec une recherche météo web

## 6.2 Audit énergétique

**Demande :** *« Analyse ma consommation électrique de la semaine dernière. Quels équipements consomment le plus ? »*

Claude va :
- Appeler `find_command(generic_type="POWER")` pour trouver tous les compteurs de puissance
- Appeler `get_statistics` sur chacun avec `hours=168` (7 jours)
- Comparer les moyennes, identifier les pics et anomalies
- Produire un rapport avec recommandations

## 6.3 Mode vacances

**Demande :** *« Je pars en vacances du 15 au 28 juillet. Configure la maison en mode absence. »*

Claude peut :
- Lister et lire les scénarios via `get_scenario_code` pour comprendre leur logique
- Désactiver les scénarios de confort, activer les scénarios de sécurité
- Créer une variable `mode_maison = 'vacances'` via `set_variable`
- Vous demander confirmation avant chaque action critique

## 6.4 Diagnostic réseau domotique

**Demande :** *« Mon réseau Zigbee a des problèmes depuis ce matin. Aide-moi à diagnostiquer. »*

Claude va :
- Appeler `get_dead_devices` pour identifier les nœuds perdus
- Utiliser `get_changes(minutes=480)` pour voir tous les événements depuis 8h
- Lire le log zigbee via `read_log` avec les 200 dernières lignes
- Analyser les erreurs et suggérer des actions correctives

## 6.5 Comprendre un scénario

**Demande :** *« Quand je rentre le soir, qu'est-ce qui se passe exactement dans le scénario Arrivée maison ? Est-ce qu'il fonctionne bien ? »*

Claude va :
- Appeler `get_scenario_code` pour lire la logique complète du scénario
- Lire son log d'exécution via `get_scenario_log`
- Décrire le déroulé en langage naturel et signaler d'éventuelles anomalies

## 6.6 Tableau de bord complet

**Demande :** *« Fais-moi un résumé complet de la maison : température de chaque pièce, état des lumières, fenêtres ouvertes, et consommation du moment. »*

Claude va :
- Appeler `get_full_state` pour obtenir tout l'état en un seul appel
- Filtrer par generic_type : `TEMPERATURE`, `LIGHT_STATE`, `DOOR_STATE`, `POWER`
- Présenter un résumé structuré et lisible

## 6.7 Maintenance préventive

**Demande :** *« Donne-moi un bilan de santé complet de mon installation. »*

Claude va :
- Appeler `get_battery_report` pour les piles faibles
- Appeler `get_dead_devices` pour les équipements en erreur
- Consulter `get_system_messages` pour les alertes
- Présenter une synthèse avec les actions prioritaires

## 6.8 Surveillance et alertes

**Demande :** *« Vérifie toutes les caméras et envoie-moi un résumé par Telegram. »*

Claude va :
- Appeler `list_cameras` pour obtenir la liste
- Appeler `get_camera_snapshot` sur chacune et décrire la scène
- Appeler `list_notification_commands` pour trouver le canal Telegram
- Envoyer le résumé via `send_notification`

## 6.9 Accès consultation pour un tiers

**Situation :** vous voulez qu'une autre personne puisse consulter l'état de la maison sans risque d'action accidentelle.

Activez le **mode lecture seule** dans la configuration — toutes les actions sont bloquées, la consultation reste complète.

---

# 7. Sécurité

## 7.1 Modèle de sécurité

Le serveur MCP s'exécute en local sur votre serveur Jeedom avec les droits de l'utilisateur `www-data`. Les mesures de sécurité en place :

- **API Key Jeedom** — toutes les requêtes sont authentifiées avec la clé API core.
- **Token Bearer externe** — tout accès via `jeemcp_proxy.php` exige un token généré explicitement.
- **Accès réseau local** — le daemon écoute sur `0.0.0.0` mais est conçu pour un réseau local. Ne pas exposer directement sur Internet sans proxy authentifié.
- **Sandboxing fichiers** — `read_file` et `write_file` sont limités à des répertoires whitelistés. Path traversal impossible (`Path.resolve()` vérifié).
- **Permissions granulaires** — chaque capacité sensible est désactivée par défaut et doit être explicitement activée.
- **Double vérification** — la permission est vérifiée à deux niveaux : liste des outils exposés ET dispatch.
- **Mode lecture seule** — désactive toutes les actions en une option, sans modifier les permissions individuelles.
- **Audit log** — traçabilité complète de chaque action IA avec arguments et résultat. Arguments sensibles masqués automatiquement.
- **Garde-fous exécution locale** — blocklist permanente de commandes dangereuses (rm -rf, mkfs, fork bomb, reverse shell…) + options configurables.

## 7.2 Recommandations

> ⚠️ **Bonnes pratiques**
> - N'activez jamais l'écriture de fichiers sauf besoin explicite et temporaire.
> - `restart_jeedom` et `execute_local` doivent rester désactivés en usage quotidien.
> - Pour un accès tiers en consultation : activez le **mode lecture seule**.
> - Consultez régulièrement l'**audit log** pour détecter des usages anormaux.
> - Pour un accès externe, utilisez le proxy PHP + token plutôt qu'une exposition directe du port daemon.
> - Utilisez HTTPS pour tout accès externe (Cloudflare Tunnel, reverse proxy, DNS avec TLS).
> - Révoquez et régénérez le token externe si vous suspectez une compromission.

## 7.3 Accès externe sécurisé (nginx)

Pour proxifier directement le daemon depuis l'extérieur via nginx :

```nginx
# /etc/nginx/sites-available/jeedom-mcp
# Streamable HTTP
location /jeedom-mcp/mcp {
    proxy_pass http://127.0.0.1:8765/mcp;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_buffering off;
    proxy_cache off;
    proxy_read_timeout 300s;

    auth_basic "MCP Jeedom";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

> Pour une solution sans ouverture de port, voir → [Accès externe via Cloudflare Tunnel](cloudflare.md).

---

*mcp_jeedom — Documentation | Plugin open-source pour Jeedom*