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

- [19/04/2026](#19042026)
- [18/04/2026](#18042026)
- [17/04/2026](#17042026)
- [27/03/2026](#27032026)
- [22/03/2026](#22032026)
- [20/03/2026](#20032026)
- [31/12/2025](#31122025)
- [21/12/2025](#21122025)

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
