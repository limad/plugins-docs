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

## 01/11/2026

### New features

- **More accurate Jeedom Assistant answers**: it now only receives the devices the question is about. Answers are better (batteries, gas consumption, last camera detection) and each question costs about a third less.
- **Follow-up questions**: "And in the bedroom?" or "And its setpoint?" after a home automation question read the devices again, instead of answering from memory.
- **"Auto" mode of MCP clients**: a diagnosis question (failure, logs, scenario, script…) switches to Agent mode.
- **Vigie — available updates**: ordinary updates are reported like the other alerts, from the first one (at most once a day). Before, nothing showed below 21 pending packages.
- **PWA**: confirming an alert action happens inside the alert itself (Confirm / Cancel), without a browser dialog.

### Fixes

- **ChatGPT subscription lost**: saving the plugin configuration replaced the subscription (and any equipment-specific key) with the provider's global API key. The global key now only fills an equipment that has no key. If your subscription was lost, sign in once again.
- **AI connectors**: an AI connector could control the home as soon as a question contained a home automation word. It now only converses, as intended; the Jeedom assistant and MCP clients remain in charge of the home. Its answers also use fewer tokens.
- **Action safety**: the AI could run, with no value, an action that expects one (setpoint, slider, message…), for instance to "read" a temperature. Such an action is now refused and the AI is told it cannot read a state this way.
- **DeepSeek**: an answer could come back empty when the model spent all its answer time thinking. Worse, the following home automation questions then failed with an error. The plugin now asks again without thinking, and these errors are gone.
- **Panel**: an error from another plugin during an answer is now shown, instead of an empty answer.
- **"Stop" button**: the answer kept being generated (and billed) to the end. Stopping now cancels the request, with no retry or action afterwards.
- **Vigie — "What Vigie watches"**: saved sources were shown unchecked, and checking a box erased the others. The boxes now show the saved setting. Same fix for "Where to send alerts".
- **Vigie — security alerts**: an administrator account named by an e-mail address was unreadable. It is now partly shown ("l…@gmail.com"). The "new remembered device" alert groups the devices of one sign-in, gives a readable date, says whether the address is already known and where to remove the device.
- **MCP clients**: the word "Array" could appear in the instructions sent to the model. Fixed.
- **Panel**: the token counter matches real usage in a long conversation, and the history is summarized correctly again.
- **Panel**: the conversation follows the answer to the end, even when it arrives fast.
- **Plugin configuration**: two lists ("Action when exceeded", mini-chat interval) were blank until saved once.

### Miscellaneous

- Panel: clearer mode menu with an explanation of each mode, sidebar button highlighted correctly, more compact header.
- PWA on large screens: the conversation is as wide as the header, with no empty band under the input.

## 29/10/2026

### Fixes

- **Jeedom Assistant**: scenarios placed in a group could not be started or scheduled. Fixed.
- **"Confirm before execution"**: with some providers (OpenAI, Claude, OpenAI-compatible), the command ran without a confirmation card. The card now shows, in the mini-chat too. An authorized non-admin user can approve it, and it shows the device concerned ("Gate — on").
- **MCP clients with mcp_jeedom**: in the default tools mode, the equipment rights and the confirmation of sensitive targets (gate, alarm, lock) were not applied. They are now. Two clients on the same server with different rights no longer mix up their tools.
- **MCP clients**: "Retry with account 2" really uses account 2; "Tools mode: Never" sends no tools at all; mcp_jeedom house conventions are passed to the AI.
- **Fallback to another AI connector**: the fallback used the address and key of the original equipment. It now uses its own.
- **Response cache**: a request that controlled a device could be served from the cache, and then do nothing. Only answers without an action are cached, and never a question with an image.
- **AI connector page**: opening the page replaced the saved model with the default one. Fixed. Settings inherited from the parent AI connector are locked on linked equipment.
- **"Chat model (panel)"** now applies when the panel model selector is on "Auto".
- **Whitelist**: if mcp_jeedom is disabled or uninstalled, ai_assistant uses the last mcp_jeedom list instead of allowing unchecked devices.
- **Vigie**: "Analyze" is reserved for administrators; passwords and tokens in watched logs are hidden in more formats; an alert meant for specific users can only be acted on by them or an administrator.
- **AI connectors**: the model knows today's date; the equipment instructions are read correctly.

### Miscellaneous

- Panel and PWA: lighter header, "Export conversation" and "Notifications" in the Options menu (export available on mobile).
- Phone and tablet: Options menu easier to tap, no more automatic iOS zoom on fields.
- PWA: content centered on large screens; "Remove this device from notifications" moved to the notifications pane, with a confirmation, and the removal is lasting.
- Panel: message times ("3 minutes ago") update when you come back to the tab.

## 28/10/2026

### New features

- **Images sent to every provider**: OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter, Perplexity, Ollama and OpenAI-compatible servers now receive attached images. They used to be ignored silently.
- **Camera analysis**: also works with Netatmo and Eufy cameras and any camera providing a snapshot or an image URL.

### Fixes

- Calls outside the panel (scenarios, other plugins, camera analysis) to recent Claude models failed. Fixed.
- Temporary image analysis files are always deleted.

## 27/10/2026

### New features

- **Vigie — system updates**: reports pending packages, uninstalled security fixes, stopped automatic updates and a required reboot. Nothing is ever installed.
- **Vigie — security**: reports repeated failed sign-ins, banned addresses, a new administrator, two-factor authentication removed, a new remembered device on an administrator account and a new open port on the network.
- **Vigie — simpler settings**: an enable switch, one explanation per source, advanced settings folded. Options that call an AI are flagged.
- **Vigie — AI choice**: "Review alerts with" and "Analyze an alert with" list your active equipment.
- **ChatGPT subscription**: model list read live, plan and quota shown by the connection test, Free plan recognized.
- **MCP clients**: tools forbidden by the equipment rights are no longer offered to the AI.

### Fixes

- **Vigie**: several alerts (trend, correlation, logs) were never sent; a charging battery no longer raises an alert.
- **ChatGPT subscription**: no more error on "Available models"; the chosen reasoning effort is passed on; clear message for a model that is not accepted.
- **Chat**: non-streamed answers and the confirmation card work again; "turn on" / "turn off" always sends the command.
- **Models**: the "Available models" list follows your selection; changing provider picks its default model.

### Security

- The AI can no longer use a tool it was not offered, and "confirm" no longer unlocks dangerous tools.
- Panel for non-admin users: only whitelisted values can be read.

## 25/10/2026

### New features

- **Always up-to-date model list**: it comes from a shared catalog updated every week and downloaded automatically, without waiting for a new plugin version.
- **Choose the offered models**: in the plugin configuration, "Choose models" lists models per provider with context, price and capabilities. New models can be added automatically or left for you to check.

### Fixes

- "List models" no longer offers models that cannot be used for chat (voice, images, transcription…).

## 14/10/2026

### New features

- **Up-to-date model catalog**: GPT-6 (OpenAI), Gemini 3.8 and 4 Argon, Claude 5.5, new OpenRouter, DeepSeek and Mistral models. Retired or outclassed models (o1/o3/o4, Gemini 2.0 and 1.5, Claude 3.5 Sonnet…) are removed: every model kept is recent or has a cost advantage.
- **Automatic equipment migration**: on update, an equipment configured on a retired model is switched to its successor (explicit table; hand-typed models, Ollama and OpenAI-compatible are left alone). Each replacement is logged.
- **Filtered "Check catalog"**: dated snapshots, variants (`:free`, `:batch`), older versions and outdated generations are no longer suggested; a line separates each provider. The list drops from hundreds of identifiers to a few dozen.
- **Plugin description and quick start** rewritten, in six languages.

### Fixes

- **Gemini — 503 error**: with or without streaming, a persistent 429/5xx falls back to the next model (3.8 Flash, then 3.6 Flash) instead of failing; dedicated "model overloaded" message.
- **Gemini — reasoning while streaming**: the reasoning budget was not applied when streaming; it now matches the non-streaming mode, with a safety net if the model rejects that setting.
- **Panel and navbar enabled by default**: at install (the panel stayed hidden and the navbar "Enable" box showed unchecked) and on update as long as the choice was never saved.

### Miscellaneous

- Documentation: the Vigie and "subscription instead of an API key" pages join the published documentation.

## 25/09/2026

### New features

- **Stronger generic MCP client**: the `instructions` field returned by an MCP server at `initialize` was captured by the transport (`McpClient`) but never used — it is now injected into the system prompt and cached alongside `tools/list` (same key/TTL). `resources/list` / `resources/read` (already in `McpClient`) are now wired through a new local tool `read_mcp_resource(uri)`, memoized within the agentic turn. Generic to any MCP server (Jeedom, Home Assistant…), not just mcp_jeedom.

### Fixes

- **Failing tool results cached as successes**: `isError` (MCP spec — a tool that fails server-side without raising a JSON-RPC exception) was never checked; the result was audited `'ok'` and memoized, replaying a transient error for the rest of the agentic turn. It now records `status='tool_error'` and is no longer cached.
- **MCP pagination ignored**: `tools/list` and `resources/list` did not follow `nextCursor` — a paginating MCP server was silently truncated to its first page.
- 19 obsolete images removed from `core/img/` (badges + `_32x32.png` provider icons, long replaced by the `_96x96.png` variants) — no references in the code.

### Miscellaneous

- CI: PHP floor raised to 8.2 (Debian 12, now the version recommended by Jeedom) — the lint matrix no longer includes PHP 7.4.

## 31/08/2026

### New features

- **Vigie — proactive monitoring**: the plugin no longer just waits for a question, it watches the box and alerts you. Six probes, all deterministic with no AI call at all: the message center (where Jeedom already collects every plugin's errors, deduplicated), system health checks (memory, swap, disk, load, disk errors, database), unstable daemons, predictable failures through trend analysis, log files, and the page you are viewing. **Everything is disabled at install**: nothing turns on without your consent, source by source, and the plugin's emergency trigger kill-switch also stops Vigie.
- **A filter before anything else**: normalized signature, occurrence threshold, per-pattern cooldown, per-plugin and global quotas, quiet hours. On a real installation, a day's 1,655 error lines boil down to a handful of readable alerts.
- **Optional AI review**: a model — local or online — can drop an alert, lower its severity or reword it in plain language. It can never add one or raise one. If it is unreachable, alerts go out anyway. Vigie has its own call counter, separate from your conversations, with automatic pause and a single notification when exceeded.
- **Action buttons in alerts** (disabled by default): restart a daemon, mute a pattern. The browser sends only two identifiers; the server re-checks the alert, the whitelist and your Jeedom rights before acting, and sensitive actions ask for confirmation.
- **Scenario review**: in the editor, after a long pause, Vigie flags referenced commands that no longer exist, loops where a scenario acts on its own trigger, variables never defined and scenarios triggered without a trigger. Fully local analysis; code only goes to an AI if you ask.
- **The assistant knows what is wrong**: open alerts and the viewed page enrich the chat context. Asking "why is my box slow?" now goes out with the answer at hand.
- **Dedicated configuration tab** with a simulation button: it really collects and filters, but sends no alert and uses no quota — the way to tune thresholds without triggering anything.

### Security

- Proactive alerts are reserved for administrators by default, with **server-side** filtering of the notification badge: an alert containing a log excerpt can no longer reach a non-administrator.
- Secrets found in logs (API keys, tokens, passwords, e-mail addresses, public IP addresses) are masked **before** being stored, not at display time.
- Running an action re-checks the calling user's Jeedom rights, on top of the plugin whitelist.

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
