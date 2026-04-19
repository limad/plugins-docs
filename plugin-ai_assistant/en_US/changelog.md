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
