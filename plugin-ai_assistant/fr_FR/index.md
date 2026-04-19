---
layout: default
title: ai_assistant — Assistant IA pour Jeedom
lang: fr_FR
pluginId: ai_assistant
---

# Presentation

**ai_assistant** est le plugin Jeedom qui apporte une interface de chat IA et des assistants specialises pour piloter votre domotique. Il peut fonctionner **en mode API direct** (sans daemon) ou se connecter a un **serveur MCP** via le plugin `mcp_jeedom`.

> ## Un plugin, multi roles
>
> Couteau suisse de l intelligence artificielle, ce plugin se decline en trois piliers :
>
> - **Agent conversationnel classique :** un dialogue fluide avec l IA pour toutes vos questions generales.
> - **Assistant specialise Jeedom :** un expert qui connait votre installation sur le bout des doigts.
> - **Pont MCP (Model Context Protocol) :** le lien direct entre votre serveur MCP et votre modele d IA.
>
> Ce dernier usage est une petite revolution. Au dela de l aspect pratique, les possibilites deviennent illimitees : imaginez un scenario Jeedom demandant a l IA de generer et d integrer un script complexe, le tout en interne et de maniere autonome.

---

# Pourquoi ajouter mcp_jeedom ?

Le plugin **mcp_jeedom** transforme Jeedom en serveur MCP. Associe a **ai_assistant**, cela apporte :

- **Outils avances** (MCP) : lecture d etats, statistiques, snapshots camera, actions composites.
- **Mode Jeedom specialist** : l IA est alimentee par une connaissance riche et structuree de votre maison.
- **Whitelists et securite** : controle fin des equipements et commandes exposes.

Sans MCP, ai_assistant reste pleinement utilisable en **mode API direct** (Gemini, OpenAI, Claude, Mistral, Groq, Ollama, etc.).

---

# Interfaces utilisateur

## 1) Panel (interface complete)

Le panel est l interface principale :

- **Selection d equipement IA** (provider, assistant ou client MCP)
- **Streaming des reponses**
- **Options avancees** : modele, temperature, theme
- **Pieces jointes** (image / pdf / txt / csv / docx)
- **Affichage des images** renvoyees par l IA directement dans la conversation
- **Dictee vocale** (Web Speech API) avec selection du microphone
- **Affichage adaptatif** : mobile, tablette, ordinateur
- **Bouton flottant "Defiler vers le bas"** (affichage automatique si vous remontez dans l historique)

## 1bis) Pan Light (vue provider)

Interface legere orientee provider direct :

- **Selection provider + modele**
- **Streaming**
- **Pieces jointes**
- **Bouton flottant "Defiler vers le bas"**
- **Toggle menu Jeedom**

## 2) Mini chat (navbar)

Un chat rapide integre a la barre Jeedom :

- Simple et discret
- Selection d equipement
- Reponse streaming
- Pas d options avancees

---

# Types d equipements

## Assistant general (Provider)
- Connexion directe a votre fournisseur IA
- Sert de base a d autres equipements

## Assistant Jeedom
- **Lie a un Provider**
- Specialise pour Jeedom
- Recommande pour piloter la maison

## Client MCP
- **Lie a un Provider**
- Sert d interface entre ai_assistant et un serveur MCP HTTP
- Associe a `mcp_jeedom` pour un controle domotique avance
- Supporte les headers HTTP personnalises (champ *Options HTTP avancees*)

---

# Serveurs MCP compatibles

Le client MCP de **ai_assistant** supporte uniquement le **transport HTTP** (Streamable HTTP ou SSE).
Le transport STDIO n est pas pris en charge : seuls les serveurs exposant un endpoint HTTP sont compatibles.

## Transport supporte

| Transport | Compatible |
|---|:---:|
| Streamable HTTP (`/mcp`) | ✅ |
| SSE (`/sse`) | ✅ |
| STDIO (subprocess local) | ❌ |

## Serveurs MCP HTTP compatibles

| Serveur | URL type | Notes |
|---|---|---|
| **mcp_jeedom** | `http://127.0.0.1:8765/mcp` | Plugin Jeedom dedie, recommande |
| **mcp_jeedom (SSE)** | `http://127.0.0.1:8765/sse` | Mode SSE alternatif |
| **mcp_jeedom (proxy)** | `https://VOTRE_DOMAINE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php` | Acces externe avec token |
| Tout serveur MCP HTTP | `http://hote:port/mcp` | Standard MCP 2025-03-26 |

## Configuration d un serveur MCP externe

Dans l equipement *Client MCP* :

- **URL** : endpoint HTTP du serveur (ex: `http://192.168.1.50:3000/mcp`)
- **Token** : Bearer token si le serveur l exige
- **Options HTTP avancees** : headers supplementaires au format JSON

```json
{
  "headers": {
    "X-Api-Key": "ma-cle",
    "X-Tenant-Id": "mon-tenant"
  }
}
```

> **Note :** Les serveurs qui ne fonctionnent qu en STDIO (subprocess local) ne sont pas compatibles directement.
> Certains serveurs STDIO disposent d un mode HTTP activable (ex: `--transport streamable-http`).
> Dans ce cas, demarrez-les en mode HTTP et renseignez leur URL.

---

# Mise en place rapide

1. **Obtenir une cle API** (Gemini, OpenAI, Claude, etc.)
2. **Renseigner la cle** dans la configuration du plugin
3. **Creer un equipement** "Assistant general"
4. **Creer un equipement** "Assistant Jeedom" lie au precedent
5. (Optionnel) **Installer `mcp_jeedom`** pour activer le pont MCP

Le client MCP se cree automatiquement si le serveur MCP est detecte.

---

# Mode API only (sans daemon)

Pour Gemini, vous pouvez activer le **mode API only** :

- Pas de daemon
- Pas de dependances Node
- Connexion directe API

Idem pour les autres providers : le plugin fonctionne en mode API direct.

---

# Tool calling natif (function calling)

Pour les providers compatibles, l IA utilise le **function calling natif** du modele plutot que le protocole JSON texte. L assistant voit quatre outils et peut en enchainer plusieurs dans une meme reponse :

| Tool | Usage |
|---|---|
| `execute_jeedom_command` | Execute une commande Jeedom (cmd_id + valeur optionnelle) |
| `run_jeedom_scenario` | Lance un scenario |
| `snapshot_camera` | Capture + analyse visuelle d une camera |
| `schedule_action` | Planifie une action dans N minutes |

## Boucle agentique

Quand l IA demande un outil, ai_assistant l execute, re-injecte le resultat dans la conversation, puis redemande au modele de conclure. La boucle tourne jusqu a **5 tours consecutifs** (`MAX_AGENTIC_TURNS`) avec un budget total de **180 s** — suffisant pour enchainer lecture d etat + decision + execution sans blocage.

Exemple : *"lis la temperature du salon et coupe le chauffage si > 22°"* → tool_call `execute_jeedom_command` (lecture) → resultat 23° → tool_call `execute_jeedom_command` (coupe chauffage) → reponse finale.

## Providers supportes en natif

| Provider | Tool calling natif |
|---|:---:|
| OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter | ✅ |
| Claude (Anthropic) | ✅ |
| Perplexity, Gemini, Ollama | ❌ (fallback protocole JSON legacy) |

Le fallback legacy reste actif si la config **strictToolCalling** est desactivee, garantissant la retro-compatibilite complete.

---

# Prompts systeme par defaut

Chaque type d equipement est livre avec un prompt systeme par defaut (FR / EN / ES selon la langue Jeedom). Ils sont definis dans `core/api/ai_assistantPrompts.php` et peuvent etre **override par equipement** via le champ *Instructions* de la configuration.

## Assistant Jeedom (jeeAssist) — FR

Pilote la maison via un protocole JSON *tool_call v1* inline (pas de function calling natif).

```text
Tu es un assistant domotique Jeedom. Tu reponds toujours en francais.
Tu as acces aux equipements de la maison listes dans {{JEEDOM_CONTEXT}}.
Ton objectif : aider vite, clairement, et de facon fiable pour piloter la maison.

=== COMPORTEMENT ATTENDU ===
- Sois concis, concret et oriente action.
- Quand la demande est ambigue, pose UNE question de clarification utile.
- Si plusieurs equipements correspondent, propose un choix court (piece/equipement).

=== LIRE LES VALEURS ===
Les valeurs actuelles sont indiquees apres → dans la liste des equipements.
Cite la valeur et l unite quand tu la mentionnes.

=== EXECUTER DES COMMANDES ===
Quand l utilisateur demande une action, inclus dans ta reponse un bloc JSON STRICT tool_call v1 sur sa propre ligne :

{"tool_call":{"version":"1","actions":[{"type":"cmd","id":<CMD_ID>},{"type":"cmd","id":<CMD_ID>,"value":"<valeur>"}]}}
{"tool_call":{"version":"1","actions":[{"type":"scenario","id":<SCENARIO_ID>}]}}
{"tool_call":{"version":"1","actions":[{"type":"snapshot","id":<CAMERA_EQ_ID>,"question":"<question optionnelle>"}]}}
{"tool_call":{"version":"1","actions":[{"name":"device.by_generic","args":{"generic_type":"LIGHT_ON","object_name":"Salon","device_name":"Lampe","value":"1"}}]}}
{"tool_call":{"version":"1","actions":[{"name":"schedule.in","args":{"minutes":20,"action":{"type":"cmd","id":<CMD_ID>}}}]}}
{"tool_call":{"version":"1","actions":[{"name":"plan.preview"}]}}
{"tool_call":{"version":"1","actions":[{"name":"camera.analyze","args":{"camera_eq_id":<CAMERA_EQ_ID>,"question":"<question optionnelle>"}}]}}

Regles :
- Utilise UNIQUEMENT les cmd_id listes dans les equipements disponibles.
- Le JSON doit contenir exactement : tool_call.version="1" et tool_call.actions=[...].
- Types supportes dans actions : cmd, scenario, snapshot, by_generic, schedule.in, plan.preview.
- Apres le JSON, confirme l action en langage naturel.
- Si la demande est purement informative, ne mets pas de bloc tool_call.
- Si l equipement demande est absent, dis-le clairement.
- Si tu n es pas sur de la commande a utiliser, pose une question.
- N execute jamais plusieurs fois la meme commande dans une reponse.

- Utilise UNIQUEMENT les scenario_id listes dans les scenarios disponibles.
- Pour un snapshot camera, utilise UNIQUEMENT les IDs [camera:...] listes dans le contexte.
- Pour un by_generic ambigu (plusieurs equipements), demande une clarification.
- Pour schedule.in, limite-toi a des delais raisonnables (1 a 720 minutes).

=== SECURITE & BON SENS ===
- Pour les actions sensibles (alarme, serrure, portail), demande confirmation explicite si l intention n est pas claire.
- N invente jamais d ID, de valeur ou d etat.
```

## Client MCP Jeedom (mcp_client_jeedom) — FR

Pilote la maison via le function calling natif expose par le serveur `mcp_jeedom`.

```text
Assistant domotique Jeedom via outils MCP (function calling). Reponds en francais, phrases courtes.

REGLES :
- Zero invention. Un cmd_id/eq_id/scenario_id n est valide que s il vient d un retour d outil MCP de la conversation courante.
- Sans l ID : appeler d abord search_devices(query) ou find_command(generic_type=...).
- Hors whitelist ou introuvable → le dire, ne pas deviner. Plusieurs resultats → choisir selon contexte (piece, derniere mention), sinon UNE question courte.
- Ne jamais afficher d IDs techniques (cmd_id, eq_id, object_id, logicalId) ou de noms bruts ; utiliser les noms lisibles.
- Sur erreur ou "ACCES REFUSE" : signaler, ne pas essayer d autres IDs au hasard.

EXECUTION :
- Workflow : search_devices/find_command → execute_action(cmd_id). Pas d appel intermediaire (get_device_info) si search_devices a deja les cmd_id.
- search_devices accepte les termes partiels ; si echec, essayer variantes (sans accents, termes partiels).
- Executer directement, SAUF confirmation requise pour : alarme/securite, portail, serrure, desactivation, suppression, acquittement, sauvegardes, mises a jour, modif scenarios.
- Confirmer en une phrase courte avec le nom lisible.

RETOURS :
- Retour vide/null/0 apres execute_action = ordre transmis, PAS action physique terminee. Pour verifier, relire via get_command_value ou search_devices.
```

## Client MCP externe (mcp_client_other) — FR

Prompt minimal, a personnaliser selon le serveur MCP tiers.

```text
Tu es un assistant connecte a un serveur MCP externe.
Utilise les outils (function calling) fournis pour repondre aux demandes.
Sois concis et precis. Reponds en francais.
```

## Personnalisation

- **Par equipement** : champ *Instructions* dans la config de l equipement → remplace (ou complete) le prompt par defaut.
- **Conventions globales** (jeeAssist uniquement) : un bloc *Conventions globales* dans la config plugin est prepende au prompt.
- **Placeholder** `{{JEEDOM_CONTEXT}}` (jeeAssist) : remplace par la liste des equipements whiteliste + valeurs courantes au moment de l appel.

Les versions EN et ES sont disponibles dans `core/api/ai_assistantPrompts.php` et selectionnees automatiquement selon `config::byKey('language', 'core')`.

---

# Actions planifiees — retry automatique

Les actions programmees via `schedule_action` (ex: *"eteins le salon dans 30 min"*) sont persistees dans `scheduled_actions.json` et executees par le cron 1 min du plugin. En cas d erreur transitoire (commande indisponible, reseau) :

- **3 tentatives** avec backoff exponentiel : 60 s, 120 s, 240 s
- Apres echec final, **notification utilisateur** via le centre de messages Jeedom et audit trace avec statut `abandoned`
- Un refus **whitelist** (`denied`) n est pas rejoue (c est un choix produit, pas une erreur)

---

# Securite

- **Whitelists** : controle fin des commandes autorisees — les tools natifs passent par les memes `_execAllowed*` que le protocole legacy
- **Mode lecture seule** recommande pour tests
- **Confirmations d actions sensibles** (selon configuration)
- **Ecritures atomiques** (tmp + rename) sur `scheduled_actions.json`, `whitelist.json`, `tool_call_audit.json` pour resister a un crash mid-write
- **Source de verite unique** : les modeles et endpoints sont lus depuis `core/config/ai_models.json` et `ai_assistant::PROVIDER_ENDPOINTS` — plus de divergence entre le code et le catalogue

---

# Modeles compatibles

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

Cette liste est amennee a evoluer.

---

# API inter-plugins

D autres plugins Jeedom peuvent interroger l IA configuree dans ai_assistant via une API PHP statique :

```php
if (class_exists('ai_assistant') && method_exists('ai_assistant', 'askDefault')) {
    $response = ai_assistant::askDefault('Ma question', ['caller' => 'monplugin']);
}
```

Methodes exposees :
- `ai_assistant::askDefault($message, $options)` — IA pure (sans outils MCP)
- `ai_assistant::askDefaultMcp($message, $options)` — IA + outils MCP
- `ai_assistant::getDefaultProvider()` / `getDefaultMcp()` — retourne l equipement par defaut
- `ai_assistant::getMcpJeedomStatus()` — diagnostic serveur MCP

[**Documentation complete de l API inter-plugins →**](inter_plugin_api.md)

Le plugin `mcp_jeedom` utilise cette API pour 5 features (generation profil maison, suggestions labels/aliases/generic_type, planning modes).

---

# FAQ

**Puis je utiliser ai_assistant sans mcp_jeedom ?**
Oui. Le plugin fonctionne en mode API direct (sans MCP).

**Pourquoi ajouter mcp_jeedom ?**
Pour les fonctions avancees : outils domotiques riches, analyses, controle multi equipements.

**Est ce securise ?**
Le plugin propose des gardes fous (whitelist, confirmations). L utilisateur reste responsable de la configuration.

---

# Guides pratiques

- [Tuto micro (HTTP local)](tuto_micro.md)

---

*Documentation ai_assistant — Plugin IA pour Jeedom*
