---
layout: default
title: AI Assistant (ai_assistant) — Changelog
lang: fr_FR
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — ai_assistant

<a href="https://limad.github.io/plugins-docs/plugin-ai_assistant">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/ai_assistant_icon.png" alt="ai_assistant icon" width="120px">
</a>

- [Présentation](https://limad.github.io/plugins-docs/plugin-ai_assistant#presentation)
- [Documentation](https://limad.github.io/plugins-docs/plugin-ai_assistant#documentation)
- Changelog
- [Forum dédié](https://community.jeedom.com/tags/plugin-ai_assistant)

## Sommaire

- [01/11/2026](#01112026)
- [29/10/2026](#29102026)
- [28/10/2026](#28102026)
- [27/10/2026](#27102026)
- [25/10/2026](#25102026)
- [14/10/2026](#14102026)
- [25/09/2026](#25092026)
- [31/08/2026](#31082026)
- [29/08/2026](#29082026)
- [23/08/2026](#23082026)
- [19/08/2026](#19082026)
- [06/06/2026](#06062026)
- [29/05/2026](#29052026)
- [19/04/2026](#19042026)
- [18/04/2026](#18042026)
- [17/04/2026](#17042026)
- [27/03/2026](#27032026)
- [22/03/2026](#22032026)
- [20/03/2026](#20032026)
- [31/12/2025](#31122025)
- [21/12/2025](#21122025)

## 01/11/2026

### ✨ Nouveautés

- **Réponses plus justes de l'Assistant Jeedom** : il ne reçoit plus que les équipements concernés par la question. Il répond mieux (batteries, consommation de gaz, dernière détection d'une caméra) et coûte environ un tiers de moins par question.
- **Questions de relance** : « Et dans la chambre ? » ou « Et sa consigne ? » après une question domotique relisent de nouveau les équipements, au lieu d'une réponse de mémoire.
- **Mode « Auto » des clients MCP** : une question de diagnostic (panne, logs, scénario, script…) passe en mode Agent.
- **PWA** : la confirmation d'une action d'alerte s'affiche dans l'alerte elle-même (Confirmer / Annuler), sans fenêtre du navigateur.

### 🐛 Corrections

- **Abonnement ChatGPT perdu** : enregistrer la configuration du plugin remplaçait l'abonnement (et toute clé propre à un équipement) par la clé API globale du fournisseur. La clé globale ne remplace plus que la clé d'un équipement qui n'en a pas. Si l'abonnement a été perdu, reconnectez-vous une fois.
- **Bouton « Arrêter »** : la réponse continuait d'être générée (et facturée) jusqu'au bout. L'arrêt coupe maintenant la demande, sans relance ni action après coup.
- **Vigie — « Ce que Vigie surveille »** : les sources enregistrées s'affichaient décochées, et cocher une case effaçait les autres. Les cases reflètent maintenant le réglage enregistré. Même correction pour « Où envoyer les alertes ».
- **Vigie — alertes de sécurité** : un compte administrateur identifié par une adresse e-mail apparaissait illisible. Il s'affiche maintenant en partie (« l…@gmail.com »). L'alerte « nouvel appareil mémorisé » regroupe les appareils d'une même connexion, donne une date lisible, dit si l'adresse est déjà connue et où supprimer l'appareil.
- **Clients MCP** : le mot « Array » pouvait apparaître dans les instructions envoyées au modèle. Corrigé.
- **Panel** : le compteur de tokens correspond à la consommation réelle dans une longue conversation, et l'historique est de nouveau résumé correctement.
- **Panel** : la conversation suit la réponse jusqu'au bout, même quand elle arrive vite.
- **Configuration du plugin** : deux listes (« Action en cas de dépassement », intervalle du mini-chat) s'affichaient vides avant tout enregistrement.

### 🔧 Divers

- Panel : menu des modes plus lisible avec une explication de chaque mode, bouton de la barre latérale correctement mis en évidence, en-tête plus compact.
- PWA sur grand écran : la conversation a la largeur de l'en-tête, sans bande vide sous la zone de saisie.

## 29/10/2026

### 🐛 Corrections

- **Assistant Jeedom** : les scénarios rangés dans un groupe ne pouvaient être ni lancés ni planifiés. Corrigé.
- **« Confirmer avant exécution »** : avec certains fournisseurs (OpenAI, Claude, compatibles OpenAI), la commande partait sans carte de confirmation. La carte s'affiche maintenant, aussi dans le mini-chat. Un utilisateur non administrateur autorisé peut la valider, et elle affiche l'équipement concerné (« Portail — on »).
- **Clients MCP avec mcp_jeedom** : dans le mode d'outils par défaut, les droits de l'équipement et la confirmation des cibles sensibles (portail, alarme, serrure) n'étaient pas appliqués. Ils le sont. Deux clients sur le même serveur avec des droits différents ne se mélangent plus leurs outils.
- **Clients MCP** : « Réessayer avec le compte 2 » utilise bien le compte 2 ; « Mode outils : Jamais » n'envoie plus aucun outil ; les conventions maison de mcp_jeedom sont transmises à l'IA.
- **Secours vers un autre Connecteur IA** : le secours utilisait l'adresse et la clé de l'équipement d'origine. Il utilise maintenant les siennes.
- **Cache des réponses** : une demande qui pilotait un équipement pouvait être servie depuis le cache, donc ne plus rien faire. Seules les réponses sans action sont mises en cache, et jamais une question avec une image.
- **Fiche d'un Connecteur IA** : l'ouverture de la fiche remplaçait le modèle enregistré par le modèle par défaut. Corrigé. Les réglages hérités du Connecteur IA parent sont verrouillés sur les équipements liés.
- **« Modèle chat (panel) »** s'applique maintenant quand le choix du modèle du panel est sur « Auto ».
- **Liste blanche** : si mcp_jeedom est désactivé ou désinstallé, ai_assistant reprend la dernière liste de mcp_jeedom au lieu d'autoriser des équipements non cochés.
- **Vigie** : « Analyser » est réservé aux administrateurs ; mots de passe et jetons des journaux surveillés sont masqués dans plus de formats ; une alerte destinée à des utilisateurs précis ne peut être actionnée que par eux ou un administrateur.
- **Connecteurs IA** : le modèle connaît la date du jour ; les instructions de l'équipement sont lues correctement.

### 🔧 Divers

- Panel et PWA : en-tête allégé, « Exporter la conversation » et « Notifications » dans le menu Options (export accessible sur mobile).
- Téléphone et tablette : menu Options plus facile à toucher, plus de zoom automatique d'iOS sur les champs.
- PWA : contenu centré sur grand écran ; « Retirer cet appareil des notifications » déplacé dans le volet des notifications, avec confirmation, et le retrait est durable.
- Panel : l'heure des messages (« il y a 3 minutes ») se met à jour au retour sur l'onglet.

## 28/10/2026

### ✨ Nouveautés

- **Images envoyées à tous les fournisseurs** : OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter, Perplexity, Ollama et les serveurs compatibles OpenAI reçoivent maintenant les images jointes. Avant, elles étaient ignorées sans message.
- **Analyse de caméra** : fonctionne aussi avec les caméras Netatmo, Eufy et toute caméra qui fournit une capture ou une URL d'image.

### 🐛 Corrections

- Les appels hors panel (scénarios, autres plugins, analyse de caméra) vers les modèles Claude récents échouaient. Corrigé.
- Les fichiers temporaires d'analyse d'image sont toujours supprimés.

## 27/10/2026

### ✨ Nouveautés

- **Vigie — mises à jour du système** : signale les paquets en attente, les correctifs de sécurité non installés, les mises à jour automatiques arrêtées et un redémarrage requis. Rien n'est jamais installé.
- **Vigie — sécurité** : signale les connexions ratées répétées, les adresses bannies, un nouvel administrateur, une double authentification retirée, un nouvel appareil mémorisé sur un compte administrateur et un nouveau port ouvert sur le réseau.
- **Vigie — configuration simplifiée** : un interrupteur d'activation, une explication par source, des réglages avancés repliés. Les options qui appellent une IA sont signalées.
- **Vigie — choix de l'IA** : « Relire les alertes avec » et « Analyser une alerte avec » proposent vos équipements actifs.
- **Abonnement ChatGPT** : liste des modèles lue en direct, plan et quota affichés au test de connexion, plan Free reconnu.
- **Clients MCP** : les outils interdits par les droits de l'équipement ne sont plus proposés à l'IA.

### 🐛 Corrections

- **Vigie** : plusieurs alertes (tendance, corrélation, journaux) ne partaient jamais ; une batterie en charge ne déclenche plus d'alerte.
- **Abonnement ChatGPT** : plus d'erreur sur « Modèles disponibles » ; l'effort de réflexion choisi est transmis ; message clair pour un modèle non accepté.
- **Chat** : la réponse non streamée et la carte de confirmation fonctionnent de nouveau ; « allume » / « éteins » envoie toujours la commande.
- **Modèles** : la liste « Modèles disponibles » suit votre sélection ; changer de fournisseur reprend son modèle par défaut.

### 🔒 Sécurité

- L'IA ne peut plus utiliser un outil qui ne lui a pas été proposé, et « confirme » ne débloque plus les outils dangereux.
- Panel des non-administrateurs : seules les valeurs de la liste blanche sont lisibles.

## 25/10/2026

### ✨ Nouveautés

- **Liste de modèles toujours à jour** : elle vient d'un catalogue commun mis à jour chaque semaine, téléchargé automatiquement, sans attendre une nouvelle version du plugin.
- **Choisir les modèles proposés** : dans la configuration du plugin, « Choisir les modèles » liste les modèles par fournisseur avec contexte, prix et capacités. Les nouveaux modèles peuvent être ajoutés automatiquement ou laissés à cocher.

### 🐛 Corrections

- « Lister les modèles » ne propose plus de modèles inutilisables pour le chat (voix, images, transcription…).

## 14/10/2026

### ✨ Nouveautés

- **Catalogue de modèles à jour** : GPT-6 (OpenAI), Gemini 3.8 et 4 Argon, Claude 5.5, nouveaux modèles OpenRouter, DeepSeek et Mistral. Les modèles fermés ou dominés (o1/o3/o4, Gemini 2.0 et 1.5, Claude 3.5 Sonnet…) sont retirés : chaque modèle conservé est récent ou présente un avantage de coût.
- **Migration automatique des équipements** : à la mise à jour, un équipement configuré sur un modèle retiré bascule vers son successeur (table explicite ; les modèles saisis à la main, Ollama et OpenAI-compatible ne sont pas touchés). Chaque remplacement est tracé dans le journal.
- **« Vérifier le catalogue » filtré** : snapshots datés, variantes (`:free`, `:batch`), anciennes versions et générations dépassées ne sont plus proposés ; une ligne sépare chaque provider. La liste passe de plusieurs centaines d'identifiants à quelques dizaines.
- **Description du plugin et démarrage rapide** réécrits, en six langues.

### 🐛 Corrections

- **Gemini — erreur 503** : en streaming comme hors streaming, un 429/5xx persistant bascule sur le modèle suivant (3.8 Flash, puis 3.6 Flash) au lieu d'échouer ; message dédié « modèle surchargé ».
- **Gemini — raisonnement en streaming** : le budget de raisonnement n'était pas appliqué en streaming ; il est aligné sur le mode hors streaming, avec un filet de sécurité si le modèle refuse ce réglage.
- **Panel et navbar activés par défaut** : à l'installation (le panel restait masqué et la case « Activer » de la navbar apparaissait décochée) et à la mise à jour tant que le choix n'a jamais été enregistré.

### 🔧 Divers

- Documentation : les pages Vigie et « abonnement à la place d'une clé API » rejoignent la documentation publiée.

## 25/09/2026

### ✨ Nouveautés

- **Client MCP générique renforcé** : le champ `instructions` renvoyé par un serveur MCP à l'`initialize` était capturé côté transport (`McpClient`) mais jamais exploité — désormais injecté dans le prompt système, mis en cache aux côtés de `tools/list` (même clé/TTL). `resources/list`/`resources/read` (déjà présents dans `McpClient`) sont maintenant branchés via un nouvel outil local `read_mcp_resource(uri)`, avec mémoïsation dans le tour agentique. Générique à tout serveur MCP (Jeedom, Home Assistant…), pas seulement mcp_jeedom.

### 🐛 Corrections

- **Résultats d'outil en erreur mis en cache comme des succès** : `isError` (spec MCP — un outil qui échoue côté serveur sans lever d'exception JSON-RPC) n'était jamais vérifié ; le résultat était audité `'ok'` et mémoïsé dans `_mcpToolCache`, rejouant une erreur transitoire pour le reste du tour agentique. Distingue désormais `status='tool_error'` et ne met plus en cache.
- **Pagination MCP ignorée** : `tools/list` et `resources/list` ne suivaient pas `nextCursor` — un serveur MCP qui pagine ses réponses était silencieusement tronqué à la première page.
- 19 images obsolètes supprimées de `core/img/` (badges + icônes provider `_32x32.png`, remplacées de longue date par les variantes `_96x96.png`) — aucune référence dans le code.

### 🔧 Divers

- CI : plancher PHP relevé à 8.2 (Debian 12, désormais la version préconisée par Jeedom) — la matrice de lint n'inclut plus PHP 7.4.

## 31/08/2026

### ✨ Nouveautés

- **Vigie — surveillance proactive** : le plugin ne se contente plus d'attendre une question, il observe la box et vous alerte. Six sondes, toutes déterministes et sans le moindre appel d'IA : le centre de messages (où Jeedom range déjà les erreurs de tous les plugins, dédoublonnées), les contrôles de santé du système (mémoire, swap, disque, charge, erreurs disque, base de données), les démons instables, les pannes prévisibles par analyse de tendance, les fichiers de journaux, et la page que vous consultez. **Tout est désactivé à l'installation** : rien ne s'active sans votre accord, source par source, et le bouton d'arrêt d'urgence des déclencheurs coupe également Vigie.
- **Un filtre avant tout le reste** : signature normalisée, seuil d'occurrences, temporisation par motif, quota par plugin et quota global, heures calmes. Sur une installation réelle, les 1 655 lignes d'erreur d'une journée se réduisent à une poignée d'alertes lisibles.
- **Relecture facultative par une IA** : un modèle — local ou en ligne — peut retirer une alerte, en réduire l'importance ou la reformuler en français clair. Il ne peut jamais en ajouter une ni en aggraver une. S'il est injoignable, les alertes partent quand même. Compteur d'appels propre à Vigie, séparé de vos conversations, avec mise en pause automatique et notification unique en cas de dépassement.
- **Boutons d'action dans les alertes** (désactivés par défaut) : relancer un démon, mettre un motif en sourdine. Le navigateur n'envoie que deux identifiants ; le serveur revérifie l'alerte, la liste blanche et vos droits Jeedom avant d'agir, et les actions sensibles demandent une confirmation.
- **Relecture de scénario** : sur l'éditeur, après une pause prolongée, Vigie signale les commandes référencées qui n'existent plus, les boucles où un scénario agit sur ce qui le déclenche, les variables jamais définies et les scénarios déclenchés sans déclencheur. Analyse entièrement locale ; le code ne part vers une IA que si vous le demandez.
- **L'assistant sait ce qui va mal** : les alertes ouvertes et la page consultée enrichissent le contexte du chat. Demander « pourquoi ma box rame ? » part désormais avec la réponse en main.
- **Onglet de configuration dédié** avec un bouton de simulation : il collecte et filtre réellement, mais n'envoie aucune alerte et ne consomme aucun quota — la façon de régler les seuils sans rien déclencher.

### 🔒 Sécurité

- Les alertes proactives sont réservées aux administrateurs par défaut, avec un filtrage **côté serveur** de la pastille de notification : une alerte contenant un extrait de journal ne peut plus atteindre un utilisateur non administrateur.
- Les secrets repérés dans les journaux (clés d'API, jetons, mots de passe, adresses e-mail, adresses IP publiques) sont masqués **avant** d'être enregistrés, et non au moment de l'affichage.
- L'exécution d'une action revérifie les droits Jeedom de l'utilisateur appelant, en plus de la liste blanche du plugin.

## 29/08/2026

### ✨ Nouveautés

- **Notifications push PWA** : l'application PWA du plugin peut désormais recevoir des notifications push (Web Push / VAPID). Nouveaux endpoints AJAX (`pwaVapidKey`, `pwaSubscribe`, `pwaDevices`, `pwaRevoke`, `pwaTestPush`, `pwaRegenerateVapid`), stockage des souscriptions dans `data/push_store.json` (gitignoré car secret), gestion multi-appareils et régénération des clés VAPID depuis la configuration du plugin.
- **Push contextuel** : option *Notifier* activable par trigger ; le résultat d'une action planifiée (succès ou échec) est poussé automatiquement au demandeur.
- **Actions différées — `list` / `cancel`** : parité entre le mode MCP (`mcp_client`) et l'assistant Jeedom natif. L'IA peut désormais lister (`list_scheduled_actions`) et annuler (`cancel_scheduled_action`) les actions programmées, en plus de les créer (`schedule_action`). File `scheduled_actions.json` cloisonnée par équipement ; ACL `read` pour lister, `execute` pour planifier / annuler. Prompts FR / EN / ES mis à jour.

### 🐛 Corrections

- **Modale Serveurs MCP — boutons bloqués** : *Tester* / *Enregistrer* / *Actualiser tools/list* restaient en spinner infini si le serveur MCP ne répondait pas. `domUtils.ajax()` accepte désormais un callback `always` exécuté en succès **et** en échec.
- **Détail d'erreur MCP** affiché directement dans la console de la modale (code HTTP, extrait du corps, message RPC) au lieu d'un simple renvoi vers la notification.
- **Champ token MCP** : pattern natif Jeedom (`input-group` + œil `bt_showPass`). Le token enregistré est renvoyé à l'admin et réellement révélé par l'œil — il ne « disparaît » plus après enregistrement. Bouton corbeille visible uniquement si un token est renseigné.
- **`testMcpServer`** : renvoie jusqu'à 250 noms d'outils (au lieu de 30) ; l'UI signale « +N autres non affichés ».

## 23/08/2026

### ✨ Nouveautés

- **Contrôle du raisonnement (`reasoning_effort`)** : nouveau champ (opt-in, vide par défaut) transmis dans le payload pour OpenAI, Groq, DeepSeek, xAI, OpenRouter, Mistral et Perplexity. Champ texte libre car le vocabulaire accepté varie selon le provider / modèle (`low`/`medium`/`high`, `none`/`default`…). Le champ ne s'affiche que pour les providers concernés.
- **Bouton « Vérifier le catalogue »** (configuration du plugin, admin) : compare en direct les catalogues renvoyés par les providers à `ai_models.json` et affiche un rapport — sans jamais modifier le fichier.

### 🐛 Corrections

- **Ollama — raisonnement forcé** : passage à l'API native `/api/chat` (au lieu de `/v1/chat/completions`) avec `think:false`. L'API de compatibilité ignorait `reasoning_effort` / `think`, laissant les modèles hybrides (Qwen3…) raisonner par défaut — latence ramenée de 18–28 s à ~1–2 s.
- **Gemini — budget de raisonnement non appliqué** : le `thinkingConfig` / `thinkingBudget` prévu pour plafonner le raisonnement des modèles Gemini 2.5 était présent en commentaire mais jamais envoyé. Corrigé, puis étendu aux modèles Gemini 3.x. Correction également du merge de `generationConfig` qui écrasait la température.
- **Catalogue de modèles à jour** (vérifié en direct avec les clés API réelles) :
  - **Groq** : les 4 modèles étaient en 404 → remplacés par `openai/gpt-oss-120b` / `20b` et `qwen/qwen3.6-27b`.
  - **Mistral** : `open-mistral-nemo` retiré → `ministral-8b-latest` ; ajout de `magistral-small-latest` (reasoning).
  - **OpenRouter** : `anthropic/claude-haiku-4-5` → `claude-haiku-4.5` ; `google/gemini-2.0-flash-001` retiré → `gemini-3.5-flash`.
  - **Gemini** : `gemini-2.5-pro` marqué comme retiré (404 côté Google).

## 19/08/2026

### 🐛 Corrections

- **Ollama — modèle configuré ignoré** : l'action `askAi` (et le fallback streaming) envoyait un modèle vide au provider, qui retombait alors sur le défaut codé en dur (`llama3.2`) au lieu du modèle réellement configuré sur l'équipement (ex: `qwen3:1.7b`).
- **Ollama — URL non héritée du Provider parent** : `askAiProvider`, `streamAiProvider`, `resetHistory`, `listModels` et `testConnection` lisaient l'URL Ollama uniquement sur l'équipement courant, ignorant le Provider parent pour les équipements Assistant / MCP liés (repli silencieux sur `localhost:11434`). Nouveau helper `_resolveOllamaUrl()` (résolution en cascade local → parent → défaut), aligné sur `_resolveApiKey()`.

### ✨ Nouveautés

- **Panel de discussion — liste des modèles Ollama dynamique** : le sélecteur de modèle interroge désormais `/api/tags` en direct sur le serveur Ollama configuré, avec repli sur le catalogue statique si le serveur est injoignable.
- **Catalogue de modèles** (`ai_models.json`) : ajout de `qwen3`, `llama3.3` et `gpt-oss` pour Ollama.

## 06/06/2026

### 🤖 Modèles IA

- **Claude Opus 4.8** ajouté au catalogue Anthropic.
- **OpenRouter** : ajout des variantes `anthropic/claude-opus-4.8` et `anthropic/claude-opus-4.8-fast`.
- **Claude Sonnet 4.6** : contexte ajusté à 1M tokens dans le catalogue.
- **Compatibilité Anthropic** : les paramètres de sampling non acceptés sont automatiquement omis pour les modèles Opus 4.7 / 4.8.

### 🧹 Audit qualité

- Ajout d'un **rapport d'audit général** du plugin (`AUDIT_REPORT.md`) : sécurité, cohérence, caches, logs, dead code, JS panel.
- Casts défensifs harmonisés sur les comparaisons strictes `getConfiguration()` pour éviter les régressions PHP/Jeedom.
- Lecture de `cost_profile_auto` simplifiée et cohérente avec le comportement checkbox Jeedom.
- Documentation interne des clés cache et de la relation `MAX_HISTORY_ENTRIES` / `budget_history`.
- Listes MCP sensibles mieux documentées : lien explicite entre ACL, outils destructifs et outils dangereux.
- Catches silencieux remplacés par des logs debug ciblés ; plusieurs échecs persistants passent en warning.

### 🧪 Tests

- Vérifications locales : `php -l` OK sur les PHP principaux et `php tests/run.php` OK.

## 29/05/2026

### ⚡ Optimisation des coûts (économie de tokens)

- **Cache `tools/list`** par serveur MCP (`mcp_tools_cache_ttl`, défaut 6 h) : la liste des outils n'est plus rechargée à chaque message. Bouton *Actualiser tools/list* dans le modal MCP pour forcer le refresh.
- **Sélection d'outils par pertinence + catalogue compact** (`mcpMaxTools`, défaut 28) : seuls les outils utiles à la question sont envoyés, descriptions tronquées — désormais sur **tous** les providers (plus seulement Gemini).
- **Mode sans-outils** (`mcp_tools_prompt_mode = auto|always|never`) : une question conversationnelle (*"résume notre discussion"*) n'envoie aucun outil ni appel `tools/list` ; une demande domotique garde les outils.
- **Contexte Jeedom filtré** par la question en jeeAssist (`jeedom_context_mode = auto|full`) : seuls les équipements liés à la demande sont injectés ; les demandes globales reçoivent le contexte complet.
- **Suivi du cache de prompt** : la part de tokens servie depuis le cache fournisseur (Claude, OpenAI/DeepSeek, Gemini) est tracée (`cached_tokens`) dans les logs debug `[tokens]`.

> Échappatoires `always` / `full` / TTL `0` pour restaurer le comportement d'origine.

### 📚 Documentation

- **Présentation enrichie** : « En bref » (capacités clés), tableau *Quand l'utiliser (vs un client IA classique)* et exemples concrets.
- **Sécurité MCP détaillée** : **ACL d'outils** (niveaux `read`/`write`/`execute`), **garde-fou destructif** (refus par défaut), **confirmation** des actions sensibles, **aperçu temps réel** (SSE) et **audit** (`tool_call_audit.json`).

## 19/04/2026

### 🎨 Panel de discussion — Nouveautés

- **Affichage adaptatif** : le panel s'ajuste automatiquement à la taille de l'écran (mobile, tablette, ordinateur). Plus de barre qui déborde ou de boutons coupés.
- **Dictée vocale** : parlez directement au lieu d'écrire. Un menu permet de choisir le micro à utiliser si vous en avez plusieurs.
- **Envoi de fichiers** : joignez une image, un PDF, un fichier texte, CSV ou DOCX à votre message pour que l'IA les analyse.
- **Affichage des images** : quand l'IA renvoie une image (photo de caméra, graphique, visuel…), elle s'affiche directement dans la conversation.

## 18/04/2026

### ✨ Nouveautés

- **Tool-calling natif (function calling)** pour OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter et Claude : l'IA appelle directement 4 tools structurés (`execute_jeedom_command`, `run_jeedom_scenario`, `snapshot_camera`, `schedule_action`). Les résultats sont ré-injectés dans la conversation, jusqu'à 5 tours agentic consécutifs (`MAX_AGENTIC_TURNS`, budget 180 s). Fallback automatique sur le protocole JSON legacy si `strictToolCalling=0` ou provider non supporté (Perplexity/Gemini/Ollama).
- **Retry automatique des actions planifiées** en cas d'erreur : backoff 60 s → 120 s → 240 s (3 tentatives), puis notification utilisateur via `message::add` et statut `abandoned` dans l'audit. Les refus `denied` (whitelist) ne sont pas rejoués.

### 🐛 Corrections

- **Divergences `default_model`** (Claude, Mistral, Perplexity) : les fallbacks PHP différaient de `ai_models.json`. Source de vérité unique désormais via `ai_assistant::getDefaultModel()` pour les 9 providers.
- **Écritures JSON non-atomiques** sur `scheduled_actions.json`, `tool_call_audit.json`, `whitelist.json` : corrompues possiblement en cas de crash mid-write. Toutes passées en `tmp + rename` atomique.
- **`_appendToolAudit` bloquant** : `LOCK_EX` remplacé par `LOCK_EX | LOCK_NB` avec backoff (max 200 ms) — plus de latence cumulée sur tool-calls concurrents.
- **Liste des modèles Claude** hardcodée en dur et divergente du JSON (`claude-opus-4-5-20251101` obsolète, `claude-opus-4-6` manquant) : `listModels()` dérive désormais de `ai_models.json`.

### 🏗️ Refactor

- **4 tables d'endpoints dupliquées** (`$oaiUrlMap`, `$openAiCompat`, `$urlMap`, switch `listModels`) supprimées : tout dérive de la constante `ai_assistant::PROVIDER_ENDPOINTS` (source unique).
- **Liste des providers hardcodée** dans `ajax.php` et les tests remplacée par `array_keys(PROVIDER_ENDPOINTS)`.

### 🧪 Tests

- Suite d'intégration enrichie : **54 assertions** (vs 35) couvrant atomicité d'écriture, schémas tool-calling, politique de retry, gate native tool-calling.
- Nouveau runner **`tests/live_providers.php`** pour valider end-to-end contre les API réelles (testConnection + listModels + ask court). À lancer manuellement.

---

## 17/04/2026

### ✨ Nouvelles fonctionnalités

- **API inter-plugins** : expose `ai_assistant::askDefault()`, `askDefaultMcp()`, `getDefaultProvider()`, `getDefaultMcp()`, `getMcpJeedomStatus()` pour que d'autres plugins Jeedom puissent interroger l'IA configurée. Documentation complète dans `docs/inter_plugin_api.md`. Rate limit par caller (1 appel/s pour `askDefault`, 2 s pour `askDefaultMcp`).
- **Configuration "Provider / MCP par défaut"** dans la page plugin : dropdowns pour désigner les équipements à utiliser pour les appels inter-plugins.
- **Commandes info dédiées par action** : chaque action `type=message` (`askAi`, `askWithContext`, `useTemplate`, `analyzeImage`, `analyzeCamera`) possède désormais sa paire `question<Xxx>` + `reponse<Xxx>`. Évite l'écrasement mutuel des réponses dans `reponseAi` (conservé comme miroir global legacy).
- **Commandes info tokens/coûts agrégés** : `usageTokensToday`, `usageCostToday`, `usageTokensMonth`, `usageCostMonth` — réset auto minuit et début de mois, protégés par flock contre les race conditions.
- **Commande info `apiHealth`** (provider only) : mise à jour par `cronHourly` qui ping la clé API une fois par 23 h. En cas d'échec, notification via `message::add` (dédupliquée).
- **Prompt caching Claude** activé dans `askClaude` + `streamClaude` (header `anthropic-beta: prompt-caching-2024-07-31` + `cache_control` ephemeral sur le system prompt). Économie ~90 % sur les tokens système répétés quand la taille dépasse le seuil (1024 Sonnet/Opus, 2048 Haiku).
- **Conventions maison** : champ Instructions débloqué pour les équipements `mcp_client` (jee_mcp et ext_mcp). Les conventions globales (ex: "FLAPSTATE=0 = fermé") sont définies une seule fois dans `mcp_jeedom → Configuration → Conventions maison` et propagées au prompt système avec mention "priorité absolue". Override local possible par équipement.
- **Verrouillage UI contextuel** : le select `kind`, `provider` et l'input `ApiKey` se verrouillent automatiquement sur les équipements enfants (jeedom_assistant / mcp_client liés à un Provider) avec badge cadenas + tooltip expliquant le comportement.

### 🐛 Corrections

- **testConnection** : ajout des cas manquants pour **deepseek, xai, openrouter, perplexity** (avant : "Provider non supporté" alors que le streaming/MCP fonctionnaient).
- **Réponse IA tronquée à 120 chars** : corrigé. Les cmds `reponseAi` et `reponse<SourceAction>` stockent désormais jusqu'à 8000 chars (constante `MAX_RESPONSE_STORED_CHARS`) — permet d'exploiter la vraie réponse dans les scénarios.
- **Race condition rollup tokens/jour/mois** : `_updateUsageRollup` protégé par `flock()` par équipement ; re-lecture DB sous verrou ; early-return sur `(0, 0)` pour idempotence stricte.
- **Cache tools MCP** : deny-list contre les faux-positifs de cachage (ex: `get_and_set_temperature`, `read_then_update_config` — 11 marqueurs de mutation détectés).
- **Historique MCP** : sanitization au save (filtre rôles invalides, contenu vide) + skip si réponse = `[Timeout multi-step]`.
- **Warnings throttlés** sur cmds manquantes : helper `_eventOrWarn` log 1× par heure par `(eqId, logicalId)` si une cmd JSON n'a pas été créée (diagnostic migration).
- **ai_assistantMcpTrait** : variables `$textN`, `$textParts2` initialisées avant boucle (suppression warnings PHP 8).
- **Connector** : suppression de `htmlspecialchars_decode($url)` qui masquait les bugs upstream.
- **PDF extraction** : branche morte `if ($pdftotext !== '')` supprimée, null-check ajouté.
- **preSave** : bloc vide qui lisait `hour_cron` sans action corrigé — sauvegarde effective d'une valeur par défaut.
- **Panel mcp_jeedom** : fix URL SSE (`jeemcp_sse.php` → `jeemcp_proxy.php`) + reconnexion exponentielle avec cap 20 tentatives.
- **McpRpcException** : ordre des arguments constructeur corrigé (ligne 401 de mcpClient.php).
- **Connector timeouts** : valeurs par défaut cohérentes (CONNECT 10 s < TOTAL 30 s).
- **streamGemini** : écriture atomique (`.tmp` + `rename`) + sanitization historique.
- **testConnection** : utilise `_resolveApiKey()` (chaîne local → parent → global).

### 🏗️ Refactor

- **Trait MCP extrait** : création de `core/php/ai_assistantMcpTrait.php` (~780 lignes) regroupant `askMcpClient`, `_mcpCallGemini/Claude/OpenAICompat`, `_mcpExecTools` (avec cache), `getMcpJeedomStatus`. La classe principale passe de 4453 à ~3900 lignes.
- **Boucle agentique** : passée de 6 à 15 itérations max (`MAX_AGENTIC_STEPS`) pour gérer les chaînes de tool calls longues.
- **Constantes métier centralisées** : `MAX_HISTORY_ENTRIES`, `STREAM_TIMEOUT_S`, `MAX_AGENTIC_STEPS`, `MAX_RESPONSE_STORED_CHARS`, `HEALTH_CHECK_COOLDOWN_S`, `INTER_PLUGIN_RATE_LIMIT_S` — élimine 15+ valeurs magiques dupliquées.
- **`_prepareImagePayload`** : helper statique remplace 3 blocs d'encodage base64 image dupliqués.
- **Streaming OpenAI-compat** : switch remplacé par une map `$oaiUrlMap` + 4 `if`.
- **Headers `askChatCompletions`** : format 100 % associatif (plus de mix numérique/associatif).

### 🧪 Tests

- **`tests/integration_ai_assistant.php`** : suite d'intégration exécutable en CLI (`php tests/integration_ai_assistant.php`) — 35 assertions couvrant constantes, routage providers, idempotence rollup, allow/deny-list cache tools, getCameraSnapshot, rate limit inter-plugins. Retour shell 0/1 pour intégration CI.

### ⚠️ Migration — action utilisateur requise

Après cette version, **ré-enregistrer chaque équipement `ai_assistant`** (clic "Sauvegarder" sur chaque fiche) pour déclencher `makeCmd` et créer les 7 nouvelles commandes info :

- `questionAskAi` / `reponseAskAi`
- `questionAskWithContext` / `reponseAskWithContext`
- `questionUseTemplate` / `reponseUseTemplate`
- `questionAnalyzeImage` / `reponseAnalyzeImage`
- `questionAnalyzeCamera` / `reponseAnalyzeCamera`
- `usageTokensToday`, `usageCostToday`, `usageTokensMonth`, `usageCostMonth`
- `apiHealth` (provider uniquement)

Sans ce re-save, le plugin fonctionne toujours mais log un avertissement une fois par heure par commande manquante (visible dans les logs). Le miroir global `reponseAi` continue de recevoir la dernière réponse pour rétro-compatibilité totale.

---

## 27/03/2026

- Panel et pan_light : ajout du bouton flottant "Defiler vers le bas" (affichage dynamique hors bas de chat, retour rapide en bas, indicateur visuel pendant streaming).
- Panel : retour de la selection des equipements de type provider dans le select principal.
- Panel : affichage des entetes de sidebar reserve aux equipements MCP.
- Refacto chemins JSON runtime/config : passage de `data/*.json` vers `core/config/*.json` (modeles, audit, etc.).
- Deplacement de `ai_assistantPrompts.php` vers `core/api/ai_assistantPrompts.php`.
- Compatibilite PHP 7.4 verifiee et corrigee sur l ensemble des fichiers PHP.

---
## 22/03/2026
- Prise en charge de la nouvelle API de Jeedom.
---
## 20/03/2026

- Prise en charge du MCP
---

## 31/12/2025

- Mise en ligne de la version bêta.

## 21/12/2025

- Amorçage des plugins(vCoder, jee_Ai, ai_assistant, Ai_Assist, browserless, jeedom_mcp, jeeMcp, mcp_jeedom, inBrowser).

---
