---
layout: default
title: ai_assistant — AI Assistant for Jeedom
lang: en_US
pluginId: ai_assistant
---

# Presentation

**ai_assistant** is the Jeedom plugin that brings an AI chat interface and specialized assistants to control your home automation. It works in **direct API mode** (no daemon) or can connect to an **MCP server** via the `mcp_jeedom` plugin.

> ## One plugin, multiple roles
>
> The Swiss Army knife of artificial intelligence, this plugin has three pillars:
>
> - **Classic conversational agent:** a smooth dialogue with the AI for all your general questions.
> - **Specialized Jeedom assistant:** an expert that knows your installation inside out.
> - **MCP bridge (Model Context Protocol):** the direct link between your MCP server and your AI model.
>
> This last use is a small revolution. Beyond the practical aspect, the possibilities become unlimited: imagine a Jeedom scenario asking the AI to generate and integrate a complex script, all internally and autonomously.

---

# Why add mcp_jeedom?

The **mcp_jeedom** plugin turns Jeedom into an MCP server. Combined with **ai_assistant**, this brings:

- **Advanced tools** (MCP): reading states, statistics, camera snapshots, composite actions.
- **Jeedom specialist mode**: the AI is fed with rich, structured knowledge of your home.
- **Whitelists and security**: fine-grained control over exposed devices and commands.

Without MCP, ai_assistant remains fully usable in **direct API mode** (Gemini, OpenAI, Claude, Mistral, Groq, Ollama, etc.).

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

# Security

- **Whitelists**: fine-grained control of authorized commands
- **Read-only mode** recommended for testing
- **Confirmations for sensitive actions** (according to configuration)
- **Atomic writes** (tmp + rename) on `scheduled_actions.json`, `whitelist.json`, `tool_call_audit.json`

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
