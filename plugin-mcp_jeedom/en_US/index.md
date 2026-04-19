---
layout: default
title: MCP Server — MCP Plugin for Jeedom
lang: en_US
pluginId: mcp_jeedom
---

# Presentation

The MCP Server plugin (`mcp_jeedom`) turns your Jeedom installation into an **MCP (Model Context Protocol)** server, Anthropic's open standard for connecting artificial intelligences to external systems.

Your favorite AI assistants — **Claude Desktop, Cursor, Continue** and any compatible MCP client — can now interact directly and in real time with your connected home.

## What the AI can do in your home

- Check the complete state of the home in a single call
- Control lights, shutters, thermostats, locks and scenarios
- Analyze the history and statistics of your sensors
- Detect devices in error and low batteries
- Read and analyze images from your cameras
- Send notifications via your plugins (Telegram, Mail…)
- Understand and debug your automation scenarios

## Features

- 🤖 **Streamable HTTP** transport (MCP standard 2025) and legacy SSE
- 🏠 Over **40 tools** covering the entire Jeedom API
- ⚡ `jeedom_api` tool for advanced composite operations (health report, virtual devices, action sequences…)
- 🔍 Semantic search by command type (`LIGHT_ON`, `TEMPERATURE`…)
- ⚙️ Whitelist of devices and commands exposed to the AI
- 🔐 Bearer token for external access, **read-only mode** available
- 📸 Visual analysis of **camera snapshots** (Camera plugin)
- 📋 Timestamped **audit log** of all AI actions
- 🗂️ MCP contextual resources: ID map, home profile, operational guide
- 🌐 Local (LAN) or secure external access (Cloudflare Tunnel, reverse proxy)

## Usage examples

> *"What is the state of the home?"* — complete summary in a few seconds.

> *"Analyze my electricity consumption for the week."* — automatic statistics and trends.

> *"Is there anyone in the hallway?"* — live camera snapshot analysis.

> *"Prepare the house for the night."* — orchestration of multiple scenarios in one sentence.

> *"Explain the Night Alarm scenario and tell me if it works correctly."* — reading and diagnosis of the code.

## Security and control

You precisely define what the AI can see and do. The plugin never does anything without you having explicitly activated the corresponding permission. Every action is traced in the audit log.

---

> ## ⛔ WARNING — READ BEFORE USE
>
> This plugin exposes **highly advanced tools** giving an artificial intelligence direct access to your home automation installation: state reading, device control, command execution, scenario management…
>
> Particular care has been taken to secure the code, but **no system is infallible**. The following risks exist and cannot be completely eliminated:
>
> - 🔓 **In case of hacking** — if an attacker accesses your MCP endpoint (external URL, stolen token, compromised network), they have full control over your home automation installation.
> - 🐛 **In case of a plugin bug** — a code error can lead to unexpected behavior, unwanted actions or exposure of sensitive data.
> - 🤖 **In case of AI error** — a language model can misinterpret a request, execute the wrong action or chain unintended commands. AI is not infallible.
> - 🌐 **In case of inadequate network configuration** — directly exposing the MCP daemon port on the Internet without protection is equivalent to opening root access to your home.
>
> ### Essential rules
>
> - ✅ **Only connect AIs you fully trust** and whose configuration you control.
> - ✅ **Prefer a local AI** (Claude Desktop, local server…) over a cloud service when possible — data does not leave your network.
> - ✅ **Never use external access mode** without reading and applying the [Security](#security) section.
> - ❌ **Never share** your external URL, Bearer token or Jeedom API key — even partially, even in a screenshot.
> - ❌ **Do not enable** `execute_local`, `write_file` or `restart_jeedom` in daily use — these tools must remain disabled except for explicit and temporary need.
> - ❌ **Never directly expose** the daemon port (8765) on the Internet without an authenticated proxy.
>
> ### Responsibility
>
> **The user is solely responsible** for the configuration, network exposure and use of the plugin. Neither the developer nor Domadoo can be held responsible for the consequences of unauthorized access, AI error, plugin bug or misconfiguration.
>
> Consult the [Security](#security) section for detailed best practices.

---

# What is MCP?

## Definition

**MCP (Model Context Protocol)** is a standardized open protocol, published by Anthropic in 2024, which defines how a language model (LLM) communicates with external tools and data sources.

Before MCP, each LLM ↔ tool integration was developed custom, making the ecosystem fragmented and difficult to maintain. MCP introduces a common contract: any MCP client (Claude Desktop, Cursor, Continue…) can connect to any MCP server without specific code on either side.

## Architecture

MCP relies on three components:

- **MCP Client** — the application hosting the LLM (e.g. Claude Desktop). It initiates the connection and sends tool requests.
- **MCP Server** — a process exposing capabilities via the protocol (tools, resources, prompts). This is the role of `mcp_jeedom`.
- **Transport** — the communication channel. Two modes: **Streamable HTTP** (recommended, 2025 standard) and **SSE** (legacy) for network, **STDIO** for a local pipe.

> ℹ️ **Important**
> The LLM never runs on your Jeedom server — it runs at Anthropic (or locally).
> The MCP server is simply a bridge between the LLM and the Jeedom API.
> All intelligent decisions remain on the LLM side; the server is only an executor.

## Communication protocol

Communication follows **JSON-RPC 2.0** on the chosen transport. A typical exchange:

1. The client sends `tools/list` → the server returns the list of available tools.
2. The LLM chooses a tool and sends `tools/call` with the arguments.
3. The server executes the action (Jeedom API call) and returns the result as text.
4. The LLM integrates the result into its response to the user.

---

# Role of the mcp_jeedom plugin

## Overview

`mcp_jeedom` is a Jeedom plugin that starts an **MCP server in the background** (Python daemon). This server translates MCP calls into requests to Jeedom's internal JSON-RPC API, allowing an LLM to:

- Know the complete state of your home in real time
- Control your devices (lights, shutters, thermostats…) by semantic type
- Trigger, analyze and debug your scenarios
- Consult history and value statistics
- Detect recent changes and battery status
- Access server logs and files *(optional, secured)*

## Technical stack

| Component | Technology |
|---|---|
| Daemon | Python 3.11 + anyio |
| Protocol | Official Anthropic MCP SDK v1.26 |
| HTTP server | uvicorn + pure ASGI |
| Default transport | Streamable HTTP (MCP spec 2025) |
| Jeedom API | Internal JSON-RPC |
| PHP plugin | Jeedom Core 4.4+ |

---

# Available capabilities

## Tools

Tools are callable functions for the LLM, organized by category.

### Optimized overview *(new recommended tools first)*

| Tool | Description |
|---|---|
| `get_full_state` | **Complete home state in 1 call**: rooms, devices, commands, values, generic_type |
| `find_command` | **Semantic search** by `generic_type` (LIGHT_ON, TEMPERATURE…) and/or room/device |
| `get_statistics` | Statistics of a historized command: min, max, average, trend |
| `get_changes` | All state changes since N minutes |
| `get_battery_report` | Battery status with configurable alert threshold |
| `get_plugin_status` | Daemon and dependency status of all active plugins |
| `get_room_summary` | Jeedom summary of a room: number of lights on, shutters open… |
| `jeedom_api` | **Advanced composite operations**: global health report, optimized state, virtual devices, sequences, scenario duplication… |

> 💡 **Tip**: start with `get_full_state` for a complete view, then use `find_command` to target a specific command before acting.

### System information

| Tool | Description |
|---|---|
| `get_system_info` | Jeedom version, hardware, IP address, OS |
| `get_jeedom_summary` | Complete dashboard: version, active plugins, global summaries |
| `list_plugins` | Lists all installed plugins (active and inactive) |
| `get_system_messages` | Pending system messages |
| `clear_system_messages` | Clears system messages |
| `get_cron_list` | Internal Jeedom scheduled tasks with their status |
| `get_timeline` | Recent events from the Jeedom timeline |
| `invalidate_cache` | Clears the MCP server's internal cache |

### Devices and rooms

| Tool | Description |
|---|---|
| `list_rooms` | Lists all rooms with their IDs |
| `list_devices` | Lists devices (filterable by room, type, active/inactive) — with battery and last communication |
| `search_devices` | Full-text search on device names |
| `get_device_info` | Complete device details (1 API call): commands, generic_type, battery |
| `get_command_value` | Current value of an info command with generic_type |
| `execute_action` | Executes an action command (on/off, slider, message…) |
| `get_room_state` | State of all active devices in a room (1 API call) with generic_type |
| `bulk_execute` | Executes multiple commands in a single request |
| `get_dead_devices` | Unreachable or errored devices |
| `get_command_history` | History of a command's values over a period |

### Advanced composite operations (`jeedom_api`)

> This single tool gives access to native PHP operations impossible or inefficient via the standard RPC API.

| Action | Description |
|---|---|
| `healthReport` | Global health report in 1 call: devices in error, low batteries, system messages, NOK daemons, available updates |
| `fullStateOptimized` | Complete home state with real-time values, clean format for the LLM |
| `getCommandValues` | Values of multiple commands in a single call |
| `duplicateScenario` | Complete copy of an existing scenario |
| `bulkScenarioAction` | Activate, deactivate, trigger or stop all scenarios in a group |
| `saveVirtualDevice` | Create or update a virtual device with its commands |
| `saveCommand` | Create or update a command on a device |
| `removeDevice` | Delete a device (mandatory confirmation) |
| `objectSummary` | Aggregated Jeedom summaries (lights on, shutters open…) |
| `sequenceActions` | Sequence of actions with configurable delay between each |

### Scenarios

| Tool | Description |
|---|---|
| `list_scenarios` | Lists all scenarios (active/inactive, groups, last execution) |
| `get_scenario` | Complete code and state of a scenario (with expressions, actions, conditions) |
| `trigger_scenario` | Triggers a scenario immediately |
| `enable_scenario` / `disable_scenario` | Activates or deactivates a scenario |
| `create_scenario` | Creates a new scenario from scratch |
| `update_scenario` | Modifies an existing scenario |
| `delete_scenario` | Deletes a scenario (confirmation required) |
| `duplicate_scenario` | Duplicates an existing scenario |
| `export_scenario` | Exports a scenario in importable JSON format |
| `import_scenario` | Imports a previously exported scenario |
| `get_scenario_log` | Reads the execution log of a scenario |

### History and statistics

| Tool | Description |
|---|---|
| `get_statistics` | Statistics: min, max, average, median, standard deviation, sum, trend |
| `get_command_history` | Raw history with configurable period and resolution |
| `get_changes` | List of command changes in the last N minutes |
| `get_trends` | Multi-command trend analysis |

### Logs and files

| Tool | Description |
|---|---|
| `list_logs` | Lists available Jeedom logs |
| `get_log` | Reads a log (with filtering and truncation) |
| `clear_log` | Clears a log |
| `list_files` | Lists files in an allowed directory |
| `read_file` | Reads a file *(require permission)* |
| `write_file` | Writes a file *(require permission, dangerous)* |

### Notifications and variables

| Tool | Description |
|---|---|
| `list_notification_commands` | Lists notification commands (Telegram, Mail, Alexa, Sonos…) |
| `send_notification` | Sends a notification via a plugin *(require permission)* |
| `list_variables` | Lists all Jeedom global variables |
| `get_variable` | Gets the value of a variable |
| `set_variable` | Sets the value of a variable |

### Camera

| Tool | Description |
|---|---|
| `list_cameras` | Lists cameras configured in the Camera plugin |
| `get_camera_snapshot` | Captures a snapshot and returns it in base64 *(require permission)* |

## Resources

MCP resources are contextual documents injected into the AI's memory:

| Resource | Content |
|---|---|
| `jeedom://home/id-map` | Complete map of IDs (rooms, devices, commands, scenarios) |
| `jeedom://home/profile` | Home profile (residents, schedules, preferences) |
| `jeedom://home/operational-guide` | Operational guide for the AI (rules, workflows, examples) |
| `jeedom://home/skills/scenarios` | Expert scenarios guide |
| `jeedom://home/skills/history-analysis` | Historical data analysis guide |
| `jeedom://home/skills/energy` | Energy management guide |
| `jeedom://home/skills/interaction` | User interaction guide |

---

# Skills — Embedded Jeedom expertise

Since version 18/04/2026, four specialized guides are embedded in the plugin. Before responding or acting, the AI automatically consults the guide best suited to your request:

| Skill | Role |
|---|---|
| **Expert scenarios** | Creates reliable scenarios with correct structure and validated templates |
| **Historical analysis** | Interprets statistics, trends and consumption comparisons |
| **Energy management** | Analyzes consumption, detects anomalies, suggests optimizations |
| **User interaction** | Adapts the communication style, asks targeted clarifications |

---

# MCP extensions from third-party plugins

Since version 18/04/2026, other Jeedom plugins can expose their own MCP tools without modifying `mcp_jeedom`.

## How it works

A plugin wanting to expose tools simply deposits two files in its own directory:

1. **`mcp_tools.json`** — declarative description of the tools (name, parameters, descriptions).
2. **`McpExtension.php`** — PHP handler executing the tools when the AI calls them.

The tools then appear automatically in the MCP server with the prefix `ext__{plugin}__{tool}`.

Example: a weather plugin exposes `ext__meteo__get_forecast` — the AI can call it directly without any configuration.

---

# Installation

1. Install the plugin from the Jeedom market
2. Install the dependencies (Node.js + Python packages)
3. Start the daemon
4. Configure the whitelist (devices authorized for the AI)

---

# Configuration

## Main options

| Option | Description |
|---|---|
| **Transport** | Streamable HTTP (recommended) or SSE |
| **Port** | Daemon listening port (default: 8765) |
| **Read-only mode** | Blocks all write actions |
| **Bearer token** | Authentication token for external access |
| **Scenarios** | Authorize the AI to create/modify scenarios |
| **Notifications** | Authorize the AI to send notifications |
| **Cameras** | Authorize the AI to read camera snapshots |
| **File reading** | Authorize reading server files |
| **File writing** | Authorize writing files (dangerous) |

## Home profile

The home profile is a free text injected into every conversation:
- Names of residents
- Usual schedules
- Preferred notification channel
- Any useful contextual information

## Whitelist

The whitelist precisely defines what the AI can see and do:
- **Authorized devices**: which devices are visible
- **Labels**: a more readable alias for the AI (e.g. "living room lamp" instead of a technical name)
- **Allowed commands**: which commands can be read or executed
- **Aliases**: alternative names to help the AI find a command

---

# Security

## Recommended architecture

For local (LAN) use only:
```
Claude Desktop → HTTP → mcp_jeedom (port 8765, local only)
```

For external access (strongly secured):
```
Claude Desktop → HTTPS → Cloudflare Tunnel → reverse proxy → jeemcp_proxy.php → mcp_jeedom
```

## Cloudflare Tunnel

The recommended method for external access:
1. Create a free Cloudflare Tunnel account
2. Configure the tunnel to point to `http://127.0.0.1:8765`
3. Use the proxy URL with Bearer token in your AI client

Never directly expose port 8765 on the Internet.

## Token Bearer

The Bearer token secures access to the external endpoint:
- Generate a strong token (at least 32 characters)
- Configure it in the plugin
- Set it in your AI client as an HTTP header: `Authorization: Bearer YOUR_TOKEN`

---

# Compatible clients

| Client | Transport | Notes |
|---|---|---|
| **Claude Desktop** | Streamable HTTP or SSE | Configuration in `claude_desktop_config.json` |
| **Cursor** | Streamable HTTP | MCP Tools section |
| **Continue** | Streamable HTTP | `.continue/config.json` |
| **Cline** | Streamable HTTP | VS Code extension |
| **Windsurf** | Streamable HTTP | Cascade section |
| **ai_assistant** | Streamable HTTP | Jeedom plugin, recommended |

---

# Changelog

[See the changelog](./changelog)

# Support

If despite this documentation and after reading the topics related to the plugin on [community]({{site.forum}}/tags/plugin-{{page.pluginId}}) you do not find an answer to your question, do not hesitate to create a new topic with the tag of the plugin ([plugin-{{page.pluginId}}]({{site.forum}}/tags/plugin-{{page.pluginId}})).
