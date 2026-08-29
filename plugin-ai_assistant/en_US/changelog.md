---
layout: default
title: ai_assistant — Changelog
lang: en_US
---

[Limad44 Jeedom Home Automation](https://limad.github.io/plugins-docs)

# Changelog — ai_assistant

<a href="https://limad.github.io/plugins-docs/plugin-ai_assistant">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/ai_assistant_icon.png" alt="ai_assistant icon" width="120px">
</a>

- [Presentation](https://limad.github.io/plugins-docs/plugin-ai_assistant)
- [Documentation](https://limad.github.io/plugins-docs/plugin-ai_assistant#documentation)
- Changelog
- [Dedicated forum](https://community.jeedom.com/tags/plugin-ai_assistant)

---

## 29/08/2026

### New features

- **PWA push notifications**: the plugin's PWA can now receive push notifications (Web Push / VAPID). New AJAX endpoints (`pwaVapidKey`, `pwaSubscribe`, `pwaDevices`, `pwaRevoke`, `pwaTestPush`, `pwaRegenerateVapid`), subscriptions stored in `data/push_store.json` (gitignored), multi-device management and VAPID key regeneration from the plugin config.
- **Contextual push**: per-trigger *Notify* option; the result of a scheduled action (success or failure) is pushed automatically to the requester.
- **Scheduled actions — `list` / `cancel`**: parity between MCP mode (`mcp_client`) and the native Jeedom assistant. The AI can now list (`list_scheduled_actions`) and cancel (`cancel_scheduled_action`) scheduled actions, not just create them. `read` ACL to list, `execute` to schedule / cancel. FR / EN / ES prompts updated.

### Bug fixes

- **MCP servers modal — stuck buttons**: *Test* / *Save* / *Refresh tools/list* stayed on an infinite spinner when the MCP server did not answer. `domUtils.ajax()` now accepts an `always` callback run on both success **and** failure.
- **MCP error detail** shown directly in the modal console (HTTP code, body excerpt, RPC message) instead of a plain "see notification".
- **MCP token field**: native Jeedom pattern (`input-group` + `bt_showPass` eye). The saved token is returned to the admin and actually revealed by the eye — it no longer "disappears" after saving. Trash button visible only when a token is set.
- **`testMcpServer`**: returns up to 250 tool names (instead of 30); the UI reports "+N others not shown".

---

## 23/08/2026

### New features

- **Reasoning control (`reasoning_effort`)**: new opt-in field (empty by default) sent in the payload for OpenAI, Groq, DeepSeek, xAI, OpenRouter, Mistral and Perplexity. Free-text field because the accepted vocabulary varies by provider / model (`low`/`medium`/`high`, `none`/`default`…). Shown only for the relevant providers.
- **"Check catalog" button** (plugin config, admin): compares the live provider catalogs against `ai_models.json` and shows a report — never modifies the file.

### Bug fixes

- **Ollama — forced reasoning**: switched to the native `/api/chat` API (instead of `/v1/chat/completions`) with `think:false`. The compatibility API ignored `reasoning_effort` / `think`, so hybrid models (Qwen3…) kept reasoning by default — latency cut from 18–28 s to ~1–2 s.
- **Gemini — reasoning budget not applied**: the `thinkingConfig` / `thinkingBudget` meant to cap reasoning on Gemini 2.5 models was commented out and never sent. Fixed and extended to Gemini 3.x models. Also fixed the `generationConfig` merge that overwrote the temperature.
- **Model catalog refreshed** (verified live against real API keys): Groq (4 models were 404 → replaced), Mistral (`open-mistral-nemo` → `ministral-8b-latest`, added `magistral-small-latest`), OpenRouter (`claude-haiku-4.5`, `gemini-3.5-flash`), Gemini (`gemini-2.5-pro` removed, 404).

---

## 19/08/2026

### Bug fixes

- **Ollama — configured model ignored**: `askAi` (and the streaming fallback) sent an empty model to the provider, which fell back to the hardcoded default (`llama3.2`) instead of the model actually configured on the device.
- **Ollama — URL not inherited from the parent Provider**: the Ollama URL was read only on the current device, ignoring the parent Provider for linked Assistant / MCP devices (silent fallback to `localhost:11434`). New `_resolveOllamaUrl()` helper (local → parent → default cascade).

### New features

- **Chat panel — dynamic Ollama model list**: the model selector now queries `/api/tags` live on the configured Ollama server, falling back to the static catalog if the server is unreachable.
- **Model catalog** (`ai_models.json`): added `qwen3`, `llama3.3` and `gpt-oss` for Ollama.

---

## 06/06/2026

### AI models

- **Claude Opus 4.8** added to the Anthropic catalog.
- **OpenRouter**: added `anthropic/claude-opus-4.8` and `anthropic/claude-opus-4.8-fast` variants.
- **Claude Sonnet 4.6**: context adjusted to 1M tokens in the catalog.
- **Anthropic compatibility**: unsupported sampling parameters are automatically omitted for Opus 4.7 / 4.8 models.

### Quality audit

- Added a general plugin **audit report** (`AUDIT_REPORT.md`): security, consistency, caches, logs, dead code, panel JS.
- Defensive casts harmonized on strict `getConfiguration()` comparisons.
- Silent catches replaced with targeted debug logs; several persistent failures now logged as warnings.

---

## 29/05/2026

### Cost optimization (token economy)

- **`tools/list` cache** per MCP server (`mcp_tools_cache_ttl`, default 6 h): the tool list is no longer reloaded on every message. *Refresh tools/list* button in the MCP modal.
- **Relevance-based tool selection + compact catalog** (`mcpMaxTools`, default 28): only tools useful to the question are sent, with truncated descriptions — now on **all** providers (not just Gemini).
- **Tool-less mode** (`mcp_tools_prompt_mode = auto|always|never`): a conversational question sends no tools and no `tools/list` call; a home-automation request keeps the tools.
- **Filtered Jeedom context** in jeeAssist (`jeedom_context_mode = auto|full`): only devices related to the request are injected; global requests get the full context.
- **Prompt cache tracking**: the share of tokens served from the provider cache is tracked (`cached_tokens`) in the `[tokens]` debug logs.

> `always` / `full` / TTL `0` escape hatches restore the original behavior.

### Documentation

- **Enriched presentation**: "At a glance", *When to use it (vs a classic AI client)* table and concrete examples.
- **Detailed MCP security**: tool **ACL** (`read`/`write`/`execute` levels), **destructive guard** (denied by default), sensitive-action **confirmation**, real-time **preview** (SSE) and **audit** (`tool_call_audit.json`).

---

## 19/04/2026

### Chat panel — New features

- **Adaptive display**: the panel automatically adjusts to screen size (mobile, tablet, desktop). No more overflowing bars or cut-off buttons.
- **Voice dictation**: speak instead of typing. A menu lets you choose which microphone to use if you have several.
- **File attachments**: attach an image, PDF, text file, CSV or DOCX to your message for the AI to analyze.
- **Image display**: when the AI sends back an image (camera photo, chart, visual...), it is displayed directly in the conversation.

---

## 18/04/2026

### New features

- **Native tool calling (function calling)** for OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter and Claude: the AI directly calls 4 structured tools (`execute_jeedom_command`, `run_jeedom_scenario`, `snapshot_camera`, `schedule_action`). Results are re-injected into the conversation, up to 5 consecutive agentic turns (180 s budget). Automatic fallback to legacy JSON protocol if the provider is not supported.
- **Automatic retry for scheduled actions** on error: backoff 60 s → 120 s → 240 s (3 attempts), then user notification and `abandoned` status in audit. Whitelist `denied` refusals are not replayed.

### Bug fixes

- Model and endpoint inconsistencies fixed across all 9 providers — single source of truth.
- Non-atomic JSON writes fixed (tmp + rename) for scheduled actions, audit, and whitelist files.
- Claude model list now derived from `ai_models.json` — no more stale hardcoded entries.

---

## 17/04/2026

### New features

- **Inter-plugin API**: other Jeedom plugins can now query the configured AI via `ai_assistant::askDefault()` and `askDefaultMcp()`.
- **Default Provider / MCP configuration**: dropdowns in the plugin page to designate default devices for inter-plugin calls.
- **Dedicated info commands per action**: each action type (`askAi`, `askWithContext`, `useTemplate`, `analyzeImage`, `analyzeCamera`) now has its own `question<Xxx>` + `reponse<Xxx>` command pair — avoids mutual overwriting.
- **Token/cost rollup info commands**: `usageTokensToday`, `usageCostToday`, `usageTokensMonth`, `usageCostMonth` — auto-reset at midnight and month start.
- **`apiHealth` info command** (provider only): updated by `cronHourly` which pings the API key once every 23 h.
- **Claude prompt caching**: `cache_control` ephemeral on the system prompt — ~90% savings on repeated system tokens.
- **Home conventions**: *Instructions* field unlocked for MCP client devices. Global conventions defined once in `mcp_jeedom → Configuration → Home conventions` and propagated to the system prompt.

### Bug fixes

- **testConnection**: missing cases for deepseek, xai, openrouter, perplexity added.
- **AI response truncated at 120 chars**: fixed — responses now stored up to 8000 chars.
- **MCP tools cache**: deny-list against false positives (11 mutation markers detected).

---

## 27/03/2026

- Panel and pan_light: added floating "Scroll to bottom" button.
- Panel: restored provider device selection in the main select.
- JSON runtime/config paths refactored: from `data/*.json` to `core/config/*.json`.

---

## 22/03/2026

- Support for the new Jeedom API.

---

## 20/03/2026

- MCP support added.

---

## 31/12/2025

- Beta version released.
