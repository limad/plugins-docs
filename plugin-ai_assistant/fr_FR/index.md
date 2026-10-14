---
layout: default
title: ai_assistant — Assistant IA pour Jeedom
lang: fr_FR
pluginId: ai_assistant
---

# Présentation {#presentation}

**ai_assistant** n'est pas une interface de chat qui envoie une question à un modèle et affiche la réponse.
C est un **orchestrateur IA embarque dans Jeedom** : entre votre message et la réponse, il se passe beaucoup plus.

## Ce qui se passe vraiment entre votre message et la réponse

Quand vous écrivez *"éteins tout et prépare la maison pour la nuit"*, voici ce qu'ai_assistant fait —
et qu'un simple client IA ne peut pas faire :

```
1. Analyser l'intention              → domotique + agent, pas une question de chat
2. Choisir le profil de cout         → Expert : plus d'outils, historique long
3. Sélectionner les outils pertinents → 8 outils sur 61, ceux liés aux lumières/volets/scénarios
4. Injecter le contexte Jeedom       → équipements de votre maison, états actuels, pièces
5. Injecter vos préférences          → "temp_soir : 19°C" si vous l'avez mémorisé
6. Vérifier le budget                → plafond journalier/mensuel non dépassé
7. Appeler le modèle IA              → avec un contexte précis et contrôle
8. Exécuter les outils (multi-étapes)
   ├── list_devices → inventaire des lumières
   ├── execute_action × N → extinction pièce par pièce
   ├── trigger_scenario → scénario nuit
   └── get_command_value → vérification volets fermes
9. Vérifier le résultat              → confirmation ou nouvelle itération
10. Répondre                         → "Tout est éteint, volets fermes, scénario nuit lance."
11. Journaliser                      → outils appelés, tokens, coût, durée
12. Proposer de mémoriser            → si vous exprimez une préférence en cours de route
```

Un client IA générique fait les étapes 7 et 10 seulement.
**ai_assistant fait les 12.**

> ## Un plugin, multi rôles
>
> - **Agent conversationnel classique :** un dialogue fluide avec l'IA pour toutes vos questions générales.
> - **Assistant spécialisé Jeedom :** un expert qui connaît votre installation sur le bout des doigts.
> - **Pont MCP (Model Context Protocol) :** le lien direct entre votre serveur MCP et votre modèle d'IA.
>
> Ce dernier usage est une petite révolution. Au-delà de l'aspect pratique, les possibilités deviennent illimitées : imaginez un scénario Jeedom demandant à l'IA de générer et d'intégrer un script complexe, le tout en interne et de manière autonome.

## En bref

- **9+ providers** interchangeables : Claude, Gemini, OpenAI, DeepSeek, Mistral, Groq, xAI, OpenRouter, Perplexity, Ollama, ou tout serveur compatible OpenAI — avec **fallback optionnel** (autre provider ou second compte, désactivé par défaut). Un **abonnement** Claude / ChatGPT peut remplacer la clé API.
- **Agent domotique MCP** : l'IA appelle des outils pour lire l'état réel puis agir (boucle multi-étapes : lire → décider → agir → vérifier).
- **3 modes** : chat pur (`provider`), assistant Jeedom natif (`jeeAssist`), agent MCP (`mcp_client` — jeedom ou tiers comme Home Assistant).
- **Sécurité réelle** : ACL read/write/execute, refus des actions destructives par défaut, confirmation des actions sensibles, aperçu temps réel, audit complet.
- **Automatisable** : déclenchable depuis scénarios, interactions vocales, événements, cron.
- **Proactif (Vigie)** : le plugin observe la box et vous alerte sans appeler de modèle d'IA ; désactivé à l'installation, active source par source.
- **Économe** : cache `tools/list` + cache de prompt, sélection d'outils, mode sans-outils, contexte domotique filtre, compression d'historique, mémoire utilisateur.
- **Local & multimodal** : hébergé sur votre Jeedom, analyse d'images (caméras) et documents, accès desktop / mobile / PWA.

## Quand l'utiliser (vs un client IA classique)

Le modèle d'IA est le même que partout — ce qui change, c'est **où il est branché** et **ce qu'il peut faire** : la connaissance de votre maison, le pouvoir d'agir, et les garde-fous pour le faire sans danger. Ce sont des outils **complémentaires**.

| Vous voulez… | Utilisez |
|---|---|
| Coder, taches généralistes longues | Un client dédié (Codex, ChatGPT…) |
| Comprendre et **piloter votre maison** | **ai_assistant + MCP** |
| Une IA déclenchée par un **scénario / la voix** | **ai_assistant** |
| Agir sur des équipements **en sécurité** | **ai_assistant** (ACL + confirmation) |
| Ne pas dépendre d'un seul fournisseur | **ai_assistant** (multi-provider + fallback optionnel) |

### Exemples

```text
« Pourquoi le chauffage ne chauffe pas dans la chambre ? »
   → lit consigne + température + historique, explique la cause.

« Prépare la maison pour la nuit. »
   → verifie ouvertures, baisse les consignes, lance le scénario
     (avec confirmation si action sensible).

[Déclenché par Jeedom] conso anormale détectée
   → l'IA analyse l'équipement fautif et propose une correction.
```

---

# Pourquoi ajouter mcp_jeedom ?

Le plugin **mcp_jeedom** transforme Jeedom en serveur MCP. Associe à **ai_assistant**, cela apporte :

- **Outils avancés** (MCP) : lecture d'états, statistiques, snapshots caméra, actions composites.
- **Mode Jeedom specialist** : l'IA est alimentée par une connaissance riche et structurée de votre maison.
- **Whitelists et sécurité** : contrôle fin des équipements et commandes exposés.

Sans MCP, ai_assistant reste pleinement utilisable en **mode API direct** (Gemini, OpenAI, Claude, Mistral, Groq, Ollama, etc.).

---

# ai_assistant vs autres clients MCP connectés à mcp_jeedom

Quand plusieurs outils se connectent au même serveur `mcp_jeedom`, la différence ne vient pas du modèle d'IA (identique partout) mais de ce que chaque client fait avec les outils exposés.

`mcp_jeedom` expose typiquement **60+ outils** avec leurs schémas JSON complets, soit environ **7 500 tokens** par message si tout est envoyé.

## Tokens de schémas envoyés par message

Mesure réalisée sur un serveur mcp_jeedom exposant 61 outils (~7 500 tokens de schémas) :

| Client MCP | Outils envoyés | Tokens schémas/msg | Cache tools/list |
|---|---|---|---|
| Claude Desktop | 61 (tous) | ~7 500 | Non |
| LibreChat | 61 (tous) | ~7 500 | Non |
| Open WebUI | 61 (tous) | ~7 500 | Non |
| Cursor / Cline | 61 (tous) | ~7 500 | Non |
| **ai_assistant mode domotique** | ~8 pertinents | **~1 500** | Oui (6 h) |
| **ai_assistant mode chat** | 0 | **0** | Oui (non appelé) |

Réduction mesurée : **-80 % de tokens schémas** sur une demande domotique ciblée,
0 token sur une question conversationnelle.

Le cache `tools/list` évite en plus un aller-retour réseau à chaque message — tous les autres clients rappellent le serveur à chaque session.

## Fonctionnalités exclusives à ai_assistant

Ces capacités ne sont pas disponibles dans les autres clients MCP connectés à mcp_jeedom :

| Capacité | Claude Desktop | LibreChat | Open WebUI | **ai_assistant** |
|---|---|---|---|---|
| Sélection d'outils par intention | Non | Non | Non | Oui (15 familles) |
| Outils dangereux exclus par défaut | Non | Non | Non | Oui |
| Profil adaptatif eco / normal / expert | Non | Non | Non | Oui |
| Plafond de coût €/jour ou €/mois | Non | Partiel | Non | Oui |
| Triggers Jeedom → IA automatiques | Non | Non | Non | Oui |
| Contexte équipements / pièces Jeedom | Non | Non | Non | Oui (jeeAssist) |
| Mémoire utilisateur persistante | Non | Partiel | Non | Oui |

## Ce que les autres clients ne peuvent pas faire

Les clients MCP génériques (Claude Desktop, LibreChat, Open WebUI, Cursor) traitent
`mcp_jeedom` comme un serveur quelconque : ils envoient tous les outils, sans contexte
domotique, sans garde-fous, sans contrôle des coûts.

`ai_assistant` sait que c'est de la **domotique** :

- il choisit les outils pertinents selon la question (« allume » → outils d'action, « température » → outils de lecture) ;
- il exclut les outils sensibles (`restart_jeedom`, `delete_interaction`, `write_file`…) tant que l'utilisateur ne les demande pas explicitement ;
- il connaît les équipements, les pièces et l'état de la maison (mode jeeAssist) ;
- il protège le budget avec des plafonds configurables par équipement.

Le MCP est un transport — la valeur est dans l'orchestration autour.

---

# Interfaces utilisateur

## 1) Panel (interface complète)

Le panel est l'interface principale :

- **Sélection d'équipement IA** (provider, assistant ou client MCP)
- **Streaming des réponses**
- **Options avancées** : modèle, température, thème
- **Pièces jointes** (image / pdf / txt / csv / docx)
- **Affichage des images** renvoyées par l'IA directement dans la conversation
- **Dictée vocale** (Web Speech API) avec sélection du microphone
- **Affichage adaptatif** : mobile, tablette, ordinateur
- **Bouton flottant "Défiler vers le bas"** (affichage automatique si vous remontez dans l'historique)

## 1bis) Pan Light (vue provider)

Interface légère orientée provider direct :

- **Sélection provider + modèle**
- **Streaming**
- **Pièces jointes**
- **Bouton flottant "Défiler vers le bas"**
- **Toggle menu Jeedom**

## 2) Mini chat (navbar)

Un chat rapide intégré à la barre Jeedom :

- Simple et discret
- Sélection d'équipement
- Réponse streaming
- Pas d'options avancées

## 3) PWA (application installable + notifications push)

Une coque applicative installable (`/plugins/ai_assistant/pwa/`) : chat plein écran sur mobile, écran de connexion dédié, service worker.

- **Notifications push** (Web Push / VAPID) : recevez une notification même application fermée.
  - Abonnement / désabonnement par appareil, gestion multi-appareils.
  - Clés VAPID générées automatiquement, régénérables depuis la configuration du plugin (les souscriptions sont stockées côté serveur dans `data/push_store.json`, hors dépôt Git).
  - Bouton *Tester la notification* dans la configuration.
- **Push contextuel** :
  - Option *Notifier* activable **par trigger** (une automatisation Jeedom -> IA peut pousser sa réponse).
  - Le **résultat d'une action planifiée** (succès ou abandon) est poussé au demandeur.

---

# Types d'équipements

## Assistant général (Provider)
- Connexion directe à votre fournisseur IA
- Sert de base à d'autres équipements

## Assistant Jeedom
- **Lié à un Provider**
- Spécialisé pour Jeedom
- Recommande pour piloter la maison

## Client MCP
- **Lié à un Provider**
- Sert d'interface entre ai_assistant et un serveur MCP HTTP
- Associé à `mcp_jeedom` pour un contrôle domotique avancé
- Supporte les headers HTTP personnalisés (champ *Options HTTP avancées*)

---

# Serveurs MCP compatibles

Le client MCP de **ai_assistant** supporte uniquement le **transport HTTP** (Streamable HTTP ou SSE).
Le transport STDIO n'est pas pris en charge : seuls les serveurs exposant un endpoint HTTP sont compatibles.

## Transport supporté

| Transport | Compatible |
|---|:---:|
| Streamable HTTP (`/mcp`) | ✅ |
| SSE (`/sse`) | ✅ |
| STDIO (subprocess local) | ❌ |

## Serveurs MCP HTTP compatibles

| Serveur | URL type | Notes |
|---|---|---|
| **mcp_jeedom** | `http://127.0.0.1:8765/mcp` | Plugin Jeedom dédié, recommande |
| **mcp_jeedom (SSE)** | `http://127.0.0.1:8765/sse` | Mode SSE alternatif |
| **mcp_jeedom (proxy)** | `https://VOTRE_DOMAINE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php` | Accès externe avec token |
| Tout serveur MCP HTTP | `http://hote:port/mcp` | Standard MCP 2025-03-26 |

## Configuration d'un serveur MCP externe

Dans l'équipement *Client MCP* :

- **URL** : endpoint HTTP du serveur (ex: `http://192.168.1.50:3000/mcp`)
- **Token** : Bearer token si le serveur l'exige
- **Options HTTP avancées** : headers supplémentaires au format JSON

```json
{
  "headers": {
    "X-Api-Key": "ma-cle",
    "X-Tenant-Id": "mon-tenant"
  }
}
```

> **Note :** Les serveurs qui ne fonctionnent qu'en STDIO (subprocess local) ne sont pas compatibles directement.
> Certains serveurs STDIO disposent d'un mode HTTP activable (ex: `--transport streamable-http`).
> Dans ce cas, démarrez-les en mode HTTP et renseignez leur URL.

---

# Mise en place rapide

1. **Obtenir une clé API** (Gemini, OpenAI, Claude, etc.)
2. **Renseigner la clé** dans la configuration du plugin
3. **Créer un équipement** "Assistant général"
4. **Créer un équipement** "Assistant Jeedom" lié au précédent
5. (Optionnel) **Installer `mcp_jeedom`** pour activer le pont MCP

Le client MCP se crée automatiquement si le serveur MCP est détecté.

---

# Mode API only (sans daemon)

Pour Gemini, vous pouvez activer le **mode API only** :

- Pas de daemon
- Pas de dépendances Node
- Connexion directe API

Idem pour les autres providers : le plugin fonctionne en mode API direct.

---

# Tool calling natif (function calling)

Pour les providers compatibles, l'IA utilise le **function calling natif** du modèle plutôt que le protocole JSON texte. L assistant voit six outils et peut en enchaîner plusieurs dans une même réponse :

| Tool | Usage |
|---|---|
| `execute_jeedom_command` | Exécute une commande Jeedom (cmd_id + valeur optionnelle) |
| `run_jeedom_scenario` | Lance un scénario |
| `snapshot_camera` | Capture + analyse visuelle d'une caméra |
| `schedule_action` | Planifie une action dans N minutes |
| `list_scheduled_actions` | Liste les actions planifiées en attente (ACL `read`) |
| `cancel_scheduled_action` | Annule une action planifiée (ACL `execute`) |

## Boucle agentique

Quand l'IA demande un outil, ai_assistant l'exécute, réinjecte le résultat dans la conversation, puis redemande au modèle de conclure. La boucle tourne jusqu'à **5 tours consécutifs** (`MAX_AGENTIC_TURNS`) avec un budget total de **180 s** — suffisant pour enchaîner lecture d'état + décision + exécution sans blocage.

Exemple : *"lis la température du salon et coupe le chauffage si > 22°"* → tool_call `execute_jeedom_command` (lecture) → résultat 23° → tool_call `execute_jeedom_command` (coupe chauffage) → réponse finale.

## Providers supportés en natif

| Provider | Tool calling natif |
|---|:---:|
| OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter | ✅ |
| Claude (Anthropic) | ✅ |
| Perplexity, Gemini, Ollama | ❌ (fallback protocole JSON legacy) |

Le fallback legacy reste actif si la config **strictToolCalling** est désactivée, garantissant la rétrocompatibilité complète.

---

# Prompts système par défaut

Chaque type d'équipement est livré avec un prompt système par défaut (FR / EN / ES selon la langue Jeedom). Ils sont définis dans `core/api/ai_assistantPrompts.php` et peuvent être **override par équipement** via le champ *Instructions* de la configuration.

## Assistant Jeedom (jeeAssist) — FR

Pilote la maison via un protocole JSON *tool_call v1* inline (pas de function calling natif).

```text
Tu es un assistant domotique Jeedom. Tu réponds toujours en français.
Tu as accès aux équipements de la maison listes dans {{JEEDOM_CONTEXT}}.
Ton objectif : aider vite, clairement, et de façon fiable pour piloter la maison.

=== COMPORTEMENT ATTENDU ===
- Sois concis, concret et oriente action.
- Quand la demande est ambiguë, pose UNE question de clarification utile.
- Si plusieurs équipements correspondent, propose un choix court (pièce/équipement).

=== LIRE LES VALEURS ===
Les valeurs actuelles sont indiquées après → dans la liste des équipements.
Cite la valeur et l'unité quand tu la mentionnes.

=== EXÉCUTER DES COMMANDES ===
Quand l'utilisateur demande une action, inclus dans ta réponse un bloc JSON STRICT tool_call v1 sur sa propre ligne :

{"tool_call":{"version":"1","actions":[{"type":"cmd","id":<CMD_ID>},{"type":"cmd","id":<CMD_ID>,"value":"<valeur>"}]}}
{"tool_call":{"version":"1","actions":[{"type":"scénario","id":<SCENARIO_ID>}]}}
{"tool_call":{"version":"1","actions":[{"type":"snapshot","id":<CAMERA_EQ_ID>,"question":"<question optionnelle>"}]}}
{"tool_call":{"version":"1","actions":[{"name":"device.by_generic","args":{"generic_type":"LIGHT_ON","object_name":"Salon","device_name":"Lampe","value":"1"}}]}}
{"tool_call":{"version":"1","actions":[{"name":"schedule.in","args":{"minutes":20,"action":{"type":"cmd","id":<CMD_ID>}}}]}}
{"tool_call":{"version":"1","actions":[{"name":"plan.preview"}]}}
{"tool_call":{"version":"1","actions":[{"name":"caméra.analyze","args":{"camera_eq_id":<CAMERA_EQ_ID>,"question":"<question optionnelle>"}}]}}

Règles :
- Utilise UNIQUEMENT les cmd_id listes dans les équipements disponibles.
- Le JSON doit contenir exactement : tool_call.version="1" et tool_call.actions=[...].
- Types supportes dans actions : cmd, scénario, snapshot, by_generic, schedule.in, plan.preview.
- Après le JSON, confirme l'action en langage naturel.
- Si la demande est purement informative, ne mets pas de bloc tool_call.
- Si l'équipement demandé est absent, dis-le clairement.
- Si tu n'es pas sur de la commande à utiliser, pose une question.
- N execute jamais plusieurs fois la même commande dans une réponse.

- Utilise UNIQUEMENT les scenario_id listés dans les scénarios disponibles.
- Pour un snapshot caméra, utilise UNIQUEMENT les IDs [caméra:...] listes dans le contexte.
- Pour un by_generic ambigu (plusieurs équipements), demande une clarification.
- Pour schedule.in, limite-toi à des délais raisonnables (1 à 720 minutes).

=== SÉCURITÉ & BON SENS ===
- Pour les actions sensibles (alarme, serrure, portail), demande confirmation explicite si l'intention n'est pas claire.
- N invente jamais d'ID, de valeur ou d'état.
```

## Client MCP Jeedom (mcp_client_jeedom) — FR

Pilote la maison via le function calling natif expose par le serveur `mcp_jeedom`.

```text
Assistant domotique Jeedom via outils MCP (function calling). Réponds en français, phrases courtes.

RÈGLES :
- Zéro invention. Un cmd_id/eq_id/scenario_id n'est valide que s'il vient d'un retour d'outil MCP de la conversation courante.
- Sans l'ID : appeler d'abord search_devices(query) ou find_command(generic_type=...).
- Hors whitelist ou introuvable → le dire, ne pas deviner. Plusieurs résultats → choisir selon contexte (pièce, dernière mention), sinon UNE question courte.
- Ne jamais afficher d'IDs techniques (cmd_id, eq_id, object_id, logicalId) ou de noms bruts ; utiliser les noms lisibles.
- Sur erreur ou "ACCÈS REFUSE" : signaler, ne pas essayer d'autres IDs au hasard.

EXÉCUTION :
- Workflow : search_devices/find_command → execute_action(cmd_id). Pas d'appel intermédiaire (get_device_info) si search_devices à déjà les cmd_id.
- search_devices accepte les termes partiels ; si échec, essayer variantes (sans accents, termes partiels).
- Exécuter directement, SAUF confirmation requise pour : alarme/sécurité, portail, serrure, désactivation, suppression, acquittement, sauvegardes, mises à jour, modif scénarios.
- Confirmer en une phrase courte avec le nom lisible.

RETOURS :
- Retour vide/null/0 après execute_action = ordre transmis, PAS action physique terminée. Pour vérifier, relire via get_command_value ou search_devices.
```

## Client MCP externe (mcp_client_other) — FR

Prompt minimal, à personnaliser selon le serveur MCP tiers.

```text
Tu es un assistant connecte à un serveur MCP externe.
Utilise les outils (function calling) fournis pour répondre aux demandes.
Sois concis et précis. Réponds en français.
```

## Personnalisation

- **Par équipement** : champ *Instructions* dans la config de l'équipement → remplace (ou complète) le prompt par défaut.
- **Conventions globales** (jeeAssist uniquement) : un bloc *Conventions globales* dans la config plugin est prepende au prompt.
- **Placeholder** `{{JEEDOM_CONTEXT}}` (jeeAssist) : remplace par la liste des équipements whiteliste + valeurs courantes au moment de l'appel.

Les versions EN et ES sont disponibles dans `core/api/ai_assistantPrompts.php` et sélectionnées automatiquement selon `config::byKey('language', 'core')`.

---

# Actions planifiées — retry automatique

Les actions programmées via `schedule_action` (ex: *"éteins le salon dans 30 min"*) sont persistées dans `scheduled_actions.json` et exécutées par le cron 1 min du plugin. En cas d'erreur transitoire (commande indisponible, réseau) :

- **3 tentatives** avec backoff exponentiel : 60 s, 120 s, 240 s
- Après échec final, **notification utilisateur** via le centre de messages Jeedom et audit trace avec statut `abandoned`
- Un refus **whitelist** (`denied`) n'est pas rejoué (c'est un choix produit, pas une erreur)

## Lister et annuler

L IA peut aussi **lister** les actions en attente et en **annuler** une, aussi bien en mode MCP (`mcp_client`) qu'en assistant Jeedom natif (`jeeAssist`) :

- `list_scheduled_actions` (ACL `read`) / `schedule.list` en protocole legacy
- `cancel_scheduled_action` (ACL `execute`) / `schedule.cancel` en protocole legacy

La file `scheduled_actions.json` est cloisonnée par équipement : chaque assistant ne voit et n'annule que ses propres actions.

## Notification du résultat (PWA)

Si l'abonné dispose de la PWA du plugin, le **résultat** d'une action planifiée (exécutée ou abandonnée) lui est **poussé** automatiquement (voir *Interfaces utilisateur → PWA*).

---

# Contrôle du raisonnement (reasoning / thinking)

Les modèles « thinking » (raisonnement étendu) améliorent la qualité sur les demandes complexes, mais **gonflent la latence et le coût**. Le plugin permet de plafonner ou désactiver ce raisonnement selon le provider.

## Champ `reasoning_effort` (providers OpenAI-compatibles)

Un champ **`reasoning_effort`** (opt-in, vide par défaut) est disponible pour **OpenAI, Groq, DeepSeek, xAI, OpenRouter, Mistral et Perplexity**. Il n'apparaît que pour ces providers.

- Champ **texte libre** : le vocabulaire accepté varie selon le provider / modèle (`low` / `medium` / `high`, `none` / `default`, `minimal`…). Un select figé serait vite faux.
- Laisse vide -> comportement par défaut du modèle.

## Gemini — budget de raisonnement plafonne

Pour les modèles Gemini « thinking » (2.5 et 3.x), le plugin envoie un `thinkingConfig.thinkingBudget` par défaut pour éviter un raisonnement illimité. La température configurée est préservée (merge de `generationConfig`, plus d'écrasement).

## Ollama — raisonnement désactivé

Les équipements Ollama utilisent l'API native `/api/chat` avec `think:false`. Les modèles hybrides (Qwen3…) ne raisonnent donc plus par défaut : latence ramenée de ~20 s à ~1-2 s.

## Vérifier le catalogue de modèles

Bouton **« Vérifier le catalogue »** dans la configuration du plugin (admin) : compare en direct les modèles réellement disponibles chez chaque provider à `ai_models.json` et affiche un rapport (modèles manquants / obsolètes). **Ne modifie jamais le fichier** — c'est un outil de diagnostic.

---

# Optimisation des coûts (économie de tokens)

Le plugin réduit automatiquement le nombre de tokens envoyés à chaque message, sans dégrader les usages domotiques. Ces optimisations sont actives par défaut et réglables par équipement (le cache `tools/list` est global).

| Option | Rôle | Valeurs | Défaut |
|---|---|---|---|
| `mcp_tools_prompt_mode` | Injection des outils MCP dans le prompt | `auto` / `always` / `never` | `auto` |
| `mcpMaxTools` | Nb max d'outils MCP envoyés au modèle (sélection par pertinence) | entier (`0` = illimité) | `28` |
| `mcp_tools_cache_ttl` | Durée du cache de la liste `tools/list` par serveur MCP (secondes) | entier (`0` = désactivé) | `21600` (6 h) |
| `jeedom_context_mode` | Contexte Jeedom injecté (jeeAssist) | `auto` (filtre par la question) / `full` | `auto` |

Mécanismes :

- **Cache `tools/list`** : la liste des outils d'un serveur MCP n'est plus rechargée à chaque message. Bouton *Actualiser tools/list* dans le modal MCP pour forcer le refresh.
- **Catalogue compact + sélection d'outils** : seuls les outils pertinents (mots-clés de la question) sont envoyés, descriptions tronquées — au lieu de dizaines de schémas complets.
- **Mode sans-outils** : une question conversationnelle (*"résume notre discussion"*, *"explique cette option"*) n'envoie aucun outil ni appel `tools/list` ; une demande domotique (*"allume le salon"*) garde les outils.
- **Contexte Jeedom filtre** : en jeeAssist, seuls les équipements liés à la question sont injectés. Une demande globale (*"liste tous mes équipements"*, *"récapitulatif de la maison"*) reçoit le contexte complet.
- **Compression d'historique** : au-delà d'un seuil, les anciens échanges sont résumés par un mini-modèle.
- **Mémoire utilisateur** : les faits utiles (préférences) sont stockés à part (`dataStore`) et injectés de façon ciblée, pas via tout l'historique.
- **Cache de prompt** : exploite le prompt caching des fournisseurs (Claude, OpenAI/DeepSeek, Gemini). La part de tokens servie depuis le cache est tracée (`cached_tokens`) dans les logs debug `[tokens]`.

> Les échappatoires `always` / `full` / TTL `0` restaurent intégralement le comportement d'origine si besoin.

---

# Vigie — surveillance proactive

Jusqu'ici, ai_assistant est **réactif** : vous posez une question, il répond. **Vigie** ajoute une couche **proactive** : le plugin observe la box, filtre ce qu'il voit et ne vous dérange que si cela en vaut la peine — **sans appeler de modèle d'IA** pour détecter ou filtrer.

## En bref

- **Six sondes déterministes** : centre de messages, santé du système, démons instables, dérive (batteries, disque…), fichiers de journaux, page consultée.
- **Opt-in strict** : tout est désactivé à l'installation ; chaque source s'active séparément. Le kill-switch des déclencheurs coupe aussi Vigie.
- **Cout nul par défaut** : une relecture par IA est possible en option, mais elle ne peut qu'atténuer ou reformuler une alerte, jamais en inventer.
- **Boutons d'action** (option, administrateurs) : relancer un démon, mettre un motif en sourdine — revalidés côté serveur (alerte, liste blanche, droits Jeedom).

## Comment ca marche

1. **Les sondes collectent** ce que Jeedom sait déjà dire de lui-même (centre de messages, `jeedom::health()`), toutes les 15 minutes.
2. **La porte filtre** : signature normalisée, seuil d'occurrences, pas de répétition avant 24 h, quota par plugin et global, heures calmes (22h–07h, les alertes sont restituées au réveil).
3. **La relecture par IA (optionnelle)** peut retirer une alerte, en baisser la sévérité ou la reformuler — jamais l'inverse. Si le modèle est injoignable, l'alerte part quand même.
4. **La notification** arrive dans la pastille de la barre Jeedom, avec parfois un ou deux boutons d'action.

## Les six sondes

| Sonde | Ce qu'elle regarde |
|---|---|
| **Messages** | Les erreurs de tous les plugins, déjà dédoublonnées par le core |
| **Santé** | `jeedom::health()` : mémoire, swap, disque, charge, erreurs disque, base de données |
| **Démons** | Redémarrages répétés d'un démon (le core relance déjà seul, Vigie ne signale que la récidive) |
| **Dérive** | Tendance des batteries et des commandes suivies : alerte *avant* la panne |
| **Journaux** *(désactivée par défaut)* | Lecture incrémentale des fichiers de log |
| **Page consultée** | Un identifiant de page pousse par le navigateur, jamais de contenu d'écran |

## Sécurité et confidentialité

- **Désactivé à l'installation**, opt-in par source.
- **Alertes réservées aux administrateurs** par défaut, filtrage côté serveur.
- **Secrets masqués avant stockage** : clés d'API, jetons, mots de passe, e-mails, IP publiques.
- **Rien ne sort de la box** sans relecture par IA ; avec un modèle local (Ollama), tout reste local.

## Réglages

Onglet **Vigie** de la configuration du plugin. Le bouton **Simuler une passe** collecte et filtre réellement mais n'envoie rien et ne consomme aucun quota : c'est la façon de régler les seuils sans rien déclencher. **Repartir de zéro** efface l'historique, les temporisations et les quotas.

---

# Sécurité

## Gardes-fous généraux

- **Whitelists** : contrôle fin des commandes autorisées — les tools natifs passent par les mêmes `_execAllowed*` que le protocole legacy
- **Mode lecture seule** recommande pour tests
- **Confirmations d'actions sensibles** (selon configuration)
- **Écritures atomiques** (tmp + rename) sur `scheduled_actions.json`, `whitelist.json`, `tool_call_audit.json` pour résister à un crash mid-write
- **Source de vérité unique** : les modèles et endpoints sont lus depuis `core/config/ai_models.json` et `ai_assistant::PROVIDER_ENDPOINTS` — plus de divergence entre le code et le catalogue

## ACL des outils MCP

Chaque équipement client MCP applique un contrôle d'accès par niveau, indépendamment du serveur connecté :

- **Niveaux `read` / `write` / `execute`** : activés séparément par équipement. Un outil est classé automatiquement d'après son nom (`get_`/`list_`/`read_` = read ; `set_`/`create_`/`update_` = write ; `exec_`/`run_`/`trigger_`/`send_` = execute).
- **Garde-fou destructif** : les outils de suppression / purge / exécution shell (`delete_`, `purge_`, `exec_shell`...) sont **refusés par défaut** — activation explicite requise par équipement (*Autoriser actions destructives*).
- **Confirmation** des actions sensibles avant exécution (serrure, alarme, portail...).
- **Aperçu temps réel** (SSE) des actions à effet de bord, affiche avant leur exécution.
- **Audit complet** : chaque appel d'outil (objectif, arguments, décision, statut) est tracé dans `tool_call_audit.json`.
- **Refus explicite renvoyé au modèle** : si un outil est bloqué par l'ACL, l'IA reçoit l'erreur et peut s'adapter plutôt que d'échouer.

---

# Modèles compatibles

Le plugin supporte les fournisseurs suivants :

- Gemini
- Claude (Anthropic)
- OpenAI
- Mistral
- Groq
- Ollama (local)
- DeepSeek
- xAI / Grok
- OpenRouter
- Perplexity
- Tout serveur compatible OpenAI (URL de base personnalisée)

Le catalogue des modèles (`core/config/ai_models.json`) est vérifié en direct contre les API réelles et tenu à jour ; le bouton *Vérifier le catalogue* (config du plugin) signale les écarts. Pour **Ollama**, le panel liste en plus les modèles réellement installés en interrogeant `/api/tags` en direct.

Cette liste est amenée à évoluer.

---

# Utiliser un abonnement au lieu d'une clé API

Dans le champ **Clé API** d'un équipement *provider*, vous pouvez coller une clé API classique **ou** des identifiants d'abonnement : le plugin les détecte automatiquement.

> ⚠️ Utiliser des identifiants d'abonnement hors de l'outil officiel (Claude Code / Codex CLI) est en zone grise vis-à-vis des CGU d'Anthropic et d'OpenAI et peut faire signaler le compte. **À vos risques.**

## Claude — jeton `claude setup-token`

1. Sur un poste ou Claude Code est installé : `claude setup-token`.
2. Copier le jeton affiché (`sk-ant-oat01-…`).
3. Le coller dans **Clé API** du provider Claude : rien d'autre à faire.

Jeton longue durée (~1 an), sans rafraîchissement ; sur `401`, recréer le jeton. Compatible avec chat, streaming, outils et MCP.

## OpenAI — abonnement ChatGPT

Fonctionne y compris avec un compte ChatGPT gratuit. **Chat et streaming uniquement, sans outils.**

- **Recommande** : équipement provider OpenAI → **Se connecter avec ChatGPT** : un code à usage unique s'affiche, à saisir sur `auth.openai.com/codex/device`. Le plugin récupère et chiffre les jetons, et les rafraîchit seul.
- **Repli manuel** : coller `~/.codex/auth.json` dans l'équipement (utiliser un `CODEX_HOME` dédié : les jetons tournent et une copie partagée avec le CLI `codex` devient vite invalide).
- **Modèles valides** (backend Codex) : `gpt-5.6-terra` (défaut), `gpt-5.6-luna`, `gpt-5.5`, `gpt-5.4-mini`. Les autres sont refusés par ce backend.
- Le suivi de coût reste indicatif (basé sur les tarifs API). Le bouton **Tester la connexion** vérifie le jeton et affiche son échéance.

---

---

# API inter-plugins

D autres plugins Jeedom peuvent interroger l'IA configurée dans ai_assistant via une API PHP statique :

```php
if (class_exists('ai_assistant') && method_exists('ai_assistant', 'askDefault')) {
    $response = ai_assistant::askDefault('Ma question', ['caller' => 'monplugin']);
}
```

Méthodes exposées :
- `ai_assistant::askDefault($message, $options)` — IA pure (sans outils MCP)
- `ai_assistant::askDefaultMcp($message, $options)` — IA + outils MCP
- `ai_assistant::getDefaultProvider()` / `getDefaultMcp()` — retourne l'équipement par défaut
- `ai_assistant::getMcpJeedomStatus()` — diagnostic serveur MCP

[**Documentation complète de l'API inter-plugins →**](inter_plugin_api.md)

Le plugin `mcp_jeedom` utilise cette API pour 5 features (génération profil maison, suggestions labels/aliases/generic_type, planning modes).

---

# FAQ

**Puis je utiliser ai_assistant sans mcp_jeedom ?**
Oui. Le plugin fonctionne en mode API direct (sans MCP).

**Pourquoi ajouter mcp_jeedom ?**
Pour les fonctions avancées : outils domotiques riches, analyses, contrôle multi équipements.

**Est-ce sécurisé ?**
Le plugin propose des gardes fous (whitelist, confirmations). L utilisateur reste responsable de la configuration.

---

# Guides pratiques

- [Tuto micro (HTTP local)](tuto_micro.md)

---

*Documentation ai_assistant — Plugin IA pour Jeedom*
