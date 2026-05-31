---
layout: default
title: ai_assistant — AI Assistant for Jeedom
lang: en_US
pluginId: ai_assistant
---

# Presentation

**ai_assistant** is not a chat interface that sends a question to a model and displays the answer.
It is an **AI orchestrator embedded in Jeedom**: between your message and the response, a lot more happens.

## What actually happens between your message and the response

When you type *"turn everything off and get the house ready for the night"*, here is what ai_assistant does —
and what a simple AI client cannot do:

```
1. Analyse the intent               → domotique + agent, not a chat question
2. Choose the cost profile          → Expert: more tools, longer history
3. Select relevant tools            → 8 tools out of 61, those linked to lights/shutters/scenarios
4. Inject the Jeedom context        → your home devices, current states, rooms
5. Inject your preferences          → "evening_temp: 19°C" if you stored it
6. Check the budget                 → daily/monthly cap not exceeded
7. Call the AI model                → with a precise, controlled context
8. Execute tools (multi-step)
   ├── list_devices → inventory of lights
   ├── execute_action × N → switch off room by room
   ├── trigger_scenario → night scenario
   └── get_command_value → confirm shutters closed
9. Verify the result                → confirm or iterate
10. Respond                         → "Everything is off, shutters closed, night scenario started."
11. Log                             → tools called, tokens, cost, duration
12. Suggest memorising              → if you expressed a preference during the conversation
```

A generic AI client does steps 7 and 10 only.
**ai_assistant does all 12.**

> ## One plugin, multiple roles
>
> - **Classic conversational agent:** a smooth dialogue with the AI for all your general questions.
> - **Specialized Jeedom assistant:** an expert that knows your installation inside out.
> - **MCP bridge (Model Context Protocol):** the direct link between your MCP server and your AI model.
>
> This last use is a small revolution. Beyond the practical aspect, the possibilities become unlimited: imagine a Jeedom scenario asking the AI to generate and integrate a complex script, all internally and autonomously.

## At a glance

- **9+ interchangeable providers**: Claude, Gemini, OpenAI, DeepSeek, Mistral, Groq, xAI, OpenRouter, Perplexity, Ollama — with **automatic fallback**.
- **MCP home-automation agent**: the AI calls tools to read the real state then act (multi-step loop: read → decide → act → verify).
- **3 modes**: pure chat (`provider`), native Jeedom assistant (`jeeAssist`), MCP agent (`mcp_client` — jeedom or third-party such as Home Assistant).
- **Real security**: read/write/execute ACL, destructive actions denied by default, confirmation of sensitive actions, real-time preview, full audit.
- **Automatable**: triggerable from scenarios, voice interactions, events, cron.
- **Cost-efficient**: `tools/list` cache + prompt caching, tool selection, tool-less mode, filtered home context, history compression, user memory.
- **Local & multimodal**: hosted on your Jeedom, image (camera) and document analysis, desktop / mobile / PWA access.

## When to use it (vs a classic AI client)

The AI model is the same as everywhere — what changes is **where it is plugged in** and **what it can do**: knowledge of your home, the power to act, and the safeguards to do so safely. These are **complementary** tools.

| You want to… | Use |
|---|---|
| Code, long general-purpose tasks | A dedicated client (Codex, ChatGPT…) |
| Understand and **control your home** | **ai_assistant + MCP** |
| An AI triggered by a **scenario / voice** | **ai_assistant** |
| Act on devices **safely** | **ai_assistant** (ACL + confirmation) |
| Avoid depending on a single vendor | **ai_assistant** (multi-provider + fallback) |

### Examples

```text
"Why isn't the heating warming the bedroom?"
   → reads setpoint + temperature + history, explains the cause.

"Get the house ready for the night."
   → checks openings, lowers setpoints, runs the scenario
     (with confirmation if the action is sensitive).

[Triggered by Jeedom] abnormal consumption detected
   → the AI analyzes the faulty device and proposes a fix.
```

---

# Why add mcp_jeedom?

The **mcp_jeedom** plugin turns Jeedom into an MCP server. Combined with **ai_assistant**, this brings:

- **Advanced tools** (MCP): reading states, statistics, camera snapshots, composite actions.
- **Jeedom specialist mode**: the AI is fed with rich, structured knowledge of your home.
- **Whitelists and security**: fine-grained control over exposed devices and commands.

Without MCP, ai_assistant remains fully usable in **direct API mode** (Gemini, OpenAI, Claude, Mistral, Groq, Ollama, etc.).

---

# ai_assistant vs other MCP clients connected to mcp_jeedom

When multiple tools connect to the same `mcp_jeedom` server, the difference does not come from the AI model (identical everywhere) but from what each client does with the exposed tools.

`mcp_jeedom` typically exposes **60+ tools** with their full JSON schemas — around **7,500 tokens** per message if everything is sent.

## Schema tokens sent per message

Measured on a mcp_jeedom server exposing 61 tools (~7,500 schema tokens):

| MCP client | Tools sent | Schema tokens/msg | tools/list cache |
|---|---|---|---|
| Claude Desktop | 61 (all) | ~7,500 | No |
| LibreChat | 61 (all) | ~7,500 | No |
| Open WebUI | 61 (all) | ~7,500 | No |
| Cursor / Cline | 61 (all) | ~7,500 | No |
| **ai_assistant domotique mode** | ~8 relevant | **~1,500** | Yes (6 h) |
| **ai_assistant chat mode** | 0 | **0** | Yes (not called) |

Measured reduction: **-80% schema tokens** on a targeted home-automation request,
0 tokens on a conversational question.

The `tools/list` cache also saves a network round-trip on every message — all other clients re-query the server each session.

## Features exclusive to ai_assistant

These capabilities are not available in other MCP clients connected to mcp_jeedom:

| Feature | Claude Desktop | LibreChat | Open WebUI | **ai_assistant** |
|---|---|---|---|---|
| Tool selection by intent | No | No | No | Yes (15 families) |
| Dangerous tools excluded by default | No | No | No | Yes |
| Adaptive profile eco / normal / expert | No | No | No | Yes |
| Cost cap €/day or €/month | No | Partial | No | Yes |
| Jeedom triggers → AI automatic | No | No | No | Yes |
| Equipment / room Jeedom context | No | No | No | Yes (jeeAssist) |
| Persistent user memory | No | Partial | No | Yes |

## What other clients cannot do

Generic MCP clients (Claude Desktop, LibreChat, Open WebUI, Cursor) treat `mcp_jeedom`
as any ordinary server: they send all tools, with no home-automation context,
no safeguards, and no cost control.

`ai_assistant` knows it is dealing with **home automation**:

- it selects relevant tools based on the question ("turn on" → action tools, "temperature" → read tools);
- it excludes sensitive tools (`restart_jeedom`, `delete_interaction`, `write_file`…) unless explicitly requested;
- it knows the devices, rooms and home state (jeeAssist mode);
- it protects the budget with per-equipment configurable cost caps.

MCP is a transport layer — the value is in the orchestration around it.

---

# User interfaces

## 1) Panel (full interface)

The panel is the main interface:

- **AI device selection** (provider, assistant or MCP client)
- **Response streaming**
- **Advanced options**: model, temperature, theme
- **Attachments** (image / pdf / txt / csv / docx)
- **Image display** returned by the AI directly in the conversation
- **Voice dictation** (Web Speech API) with microphone selection
- **Adaptive display**: mobile, tablet, computer
- **Floating "Scroll to bottom" button** (auto-display when scrolling up in history)

## 1bis) Pan Light (provider view)

Lightweight interface focused on direct provider:

- **Provider + model selection**
- **Streaming**
- **Attachments**
- **Floating "Scroll to bottom" button**
- **Jeedom menu toggle**

## 2) Mini chat (navbar)

A quick chat integrated into the Jeedom bar:

- Simple and discreet
- Device selection
- Streaming response
- No advanced options

---

# Device types

## General assistant (Provider)
- Direct connection to your AI provider
- Serves as the base for other devices

## Jeedom assistant
- **Linked to a Provider**
- Specialized for Jeedom
- Recommended for controlling the home

## MCP client
- **Linked to a Provider**
- Acts as an interface between ai_assistant and an HTTP MCP server
- Associated with `mcp_jeedom` for advanced home automation control
- Supports custom HTTP headers (field *Advanced HTTP options*)

---

# Compatible MCP servers

The **ai_assistant** MCP client only supports **HTTP transport** (Streamable HTTP or SSE).
STDIO transport is not supported: only servers exposing an HTTP endpoint are compatible.

## Supported transport

| Transport | Compatible |
|---|:---:|
| Streamable HTTP (`/mcp`) | ✅ |
| SSE (`/sse`) | ✅ |
| STDIO (local subprocess) | ❌ |

## Compatible HTTP MCP servers

| Server | Typical URL | Notes |
|---|---|---|
| **mcp_jeedom** | `http://127.0.0.1:8765/mcp` | Dedicated Jeedom plugin, recommended |
| **mcp_jeedom (SSE)** | `http://127.0.0.1:8765/sse` | Alternative SSE mode |
| **mcp_jeedom (proxy)** | `https://YOUR_DOMAIN/plugins/mcp_jeedom/core/php/jeemcp_proxy.php` | External access with token |
| Any HTTP MCP server | `http://host:port/mcp` | MCP standard 2025-03-26 |

## Configuring an external MCP server

In the *MCP Client* device:

- **URL**: HTTP endpoint of the server (e.g. `http://192.168.1.50:3000/mcp`)
- **Token**: Bearer token if the server requires it
- **Advanced HTTP options**: additional headers in JSON format

```json
{
  "headers": {
    "X-Api-Key": "my-key",
    "X-Tenant-Id": "my-tenant"
  }
}
```

> **Note:** Servers that only work in STDIO (local subprocess) are not directly compatible.
> Some STDIO servers have an HTTP mode that can be enabled (e.g. `--transport streamable-http`).
> In that case, start them in HTTP mode and enter their URL.

---

# Quick setup

1. **Get an API key** (Gemini, OpenAI, Claude, etc.)
2. **Enter the key** in the plugin configuration
3. **Create a device** "General assistant"
4. **Create a device** "Jeedom assistant" linked to the previous one
5. (Optional) **Install `mcp_jeedom`** to activate the MCP bridge

The MCP client is created automatically if the MCP server is detected.

---

# API-only mode (no daemon)

For Gemini, you can enable **API-only mode**:

- No daemon
- No Node dependencies
- Direct API connection

Same for other providers: the plugin works in direct API mode.

---

# Native tool calling (function calling)

For compatible providers, the AI uses the model's **native function calling** rather than the legacy JSON text protocol. The assistant sees four tools and can chain multiple in the same response:

| Tool | Usage |
|---|---|
| `execute_jeedom_command` | Executes a Jeedom command (cmd_id + optional value) |
| `run_jeedom_scenario` | Triggers a scenario |
| `snapshot_camera` | Captures + visually analyzes a camera |
| `schedule_action` | Schedules an action in N minutes |

## Agentic loop

When the AI requests a tool, ai_assistant executes it, re-injects the result into the conversation, then asks the model to conclude. The loop runs up to **5 consecutive turns** (`MAX_AGENTIC_TURNS`) with a total budget of **180 s** — enough to chain state reading + decision + execution without blocking.

Example: *"read the living room temperature and turn off the heating if > 22°"* → tool_call `execute_jeedom_command` (read) → result 23° → tool_call `execute_jeedom_command` (turn off heating) → final response.

## Providers with native support

| Provider | Native tool calling |
|---|:---:|
| OpenAI, Mistral, Groq, DeepSeek, xAI, OpenRouter | ✅ |
| Claude (Anthropic) | ✅ |
| Perplexity, Gemini, Ollama | ❌ (fallback to legacy JSON protocol) |

The legacy fallback remains active if **strictToolCalling** is disabled, ensuring full backwards compatibility.

---

# Default system prompts

Each device type comes with a default system prompt (FR / EN / ES according to Jeedom language). They are defined in `core/api/ai_assistantPrompts.php` and can be **overridden per device** via the *Instructions* field in the configuration.

## Customization

- **Per device**: *Instructions* field in the device config → replaces (or completes) the default prompt.
- **Global conventions** (jeeAssist only): a *Global conventions* block in the plugin config is prepended to the system prompt.
- **Placeholder** `{{JEEDOM_CONTEXT}}` (jeeAssist): replaced by the list of whitelisted devices + current values at call time.

---

# Scheduled actions — automatic retry

Actions scheduled via `schedule_action` (e.g. *"turn off the living room in 30 min"*) are persisted in `scheduled_actions.json` and executed by the plugin's 1-min cron. In case of transient error (unavailable command, network):

- **3 attempts** with exponential backoff: 60 s, 120 s, 240 s
- After final failure, **user notification** via Jeedom message center and audit trace with status `abandoned`
- A **whitelist** refusal (`denied`) is not replayed (a product choice, not an error)

---

# Cost optimization (token economy)

The plugin automatically reduces the number of tokens sent on each message, without degrading home-automation use cases. These optimizations are enabled by default and tunable per device (the `tools/list` cache is global).

| Option | Purpose | Values | Default |
|---|---|---|---|
| `mcp_tools_prompt_mode` | MCP tools injection into the prompt | `auto` / `always` / `never` | `auto` |
| `mcpMaxTools` | Max MCP tools sent to the model (relevance selection) | integer (`0` = unlimited) | `28` |
| `mcp_tools_cache_ttl` | `tools/list` cache duration per MCP server (seconds) | integer (`0` = disabled) | `21600` (6 h) |
| `jeedom_context_mode` | Injected Jeedom context (jeeAssist) | `auto` (filtered by the question) / `full` | `auto` |

Mechanisms:

- **`tools/list` cache**: a server's tool list is no longer reloaded on every message. Use *Refresh tools/list* in the MCP modal to force a refresh.
- **Compact catalog + tool selection**: only relevant tools (question keywords) are sent, with truncated descriptions — instead of dozens of full schemas.
- **Tool-less mode**: a conversational question (*"summarize our discussion"*, *"explain this option"*) sends no tools and no `tools/list` call; a home request (*"turn on the living room"*) keeps the tools.
- **Filtered Jeedom context**: in jeeAssist, only devices related to the question are injected. A global request (*"list all my devices"*, *"home overview"*) receives the full context.
- **History compression**: beyond a threshold, older exchanges are summarized by a small model.
- **User memory**: useful facts (preferences) are stored separately (`dataStore`) and injected selectively, not via the whole history.
- **Prompt caching**: leverages provider prompt caching (Claude, OpenAI/DeepSeek, Gemini). The share of tokens served from cache is tracked (`cached_tokens`) in the `[tokens]` debug logs.

> The `always` / `full` / TTL `0` escape hatches fully restore the original behavior if needed.

---

# Security

## General safeguards

- **Whitelists**: fine-grained control of authorized commands
- **Read-only mode** recommended for testing
- **Confirmations for sensitive actions** (according to configuration)
- **Atomic writes** (tmp + rename) on `scheduled_actions.json`, `whitelist.json`, `tool_call_audit.json`

## MCP tool ACL

Each MCP client device enforces level-based access control, independent of the connected server:

- **`read` / `write` / `execute` levels**: enabled separately per device. A tool is auto-classified by name (`get_`/`list_`/`read_` = read; `set_`/`create_`/`update_` = write; `exec_`/`run_`/`trigger_`/`send_` = execute).
- **Destructive guard**: deletion / purge / shell-execution tools (`delete_`, `purge_`, `exec_shell`...) are **denied by default** — explicit opt-in required per device (*Allow destructive actions*).
- **Confirmation** of sensitive actions before execution (lock, alarm, gate...).
- **Real-time preview** (SSE) of side-effect actions, shown before execution.
- **Full audit**: every tool call (goal, arguments, decision, status) is logged in `tool_call_audit.json`.
- **Explicit denial returned to the model**: if a tool is blocked by ACL, the AI receives the error and can adapt rather than fail.

---

# Compatible models

The plugin supports the following providers:

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

This list is subject to change.

---

# Inter-plugin API

Other Jeedom plugins can query the AI configured in ai_assistant via a static PHP API:

```php
if (class_exists('ai_assistant') && method_exists('ai_assistant', 'askDefault')) {
    $response = ai_assistant::askDefault('My question', ['caller' => 'myplugin']);
}
```

Exposed methods:
- `ai_assistant::askDefault($message, $options)` — pure AI (without MCP tools)
- `ai_assistant::askDefaultMcp($message, $options)` — AI + MCP tools
- `ai_assistant::getDefaultProvider()` / `getDefaultMcp()` — returns the default device
- `ai_assistant::getMcpJeedomStatus()` — MCP server diagnostic

---

# FAQ

**Can I use ai_assistant without mcp_jeedom?**
Yes. The plugin works in direct API mode (without MCP).

**Why add mcp_jeedom?**
For advanced features: rich home automation tools, analysis, multi-device control.

**Is it secure?**
The plugin offers safeguards (whitelist, confirmations). The user remains responsible for the configuration.

---

# Changelog

[See the changelog](./changelog)

# Support

If despite this documentation and after reading the topics related to the plugin on [community]({{site.forum}}/tags/plugin-{{page.pluginId}}) you do not find an answer to your question, do not hesitate to create a new topic with the tag of the plugin ([plugin-{{page.pluginId}}]({{site.forum}}/tags/plugin-{{page.pluginId}})).
