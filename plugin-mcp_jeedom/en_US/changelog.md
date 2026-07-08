---
layout: default
title: mcp_jeedom — Changelog
lang: en_US
---

[Limad44 Jeedom Home Automation](https://limad.github.io/plugins-docs)

# Changelog — mcp_jeedom

<a href="https://limad.github.io/plugins-docs/plugin-mcp_jeedom">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/mcp_jeedom_icon.png" alt="mcp_jeedom icon" width="120px">
</a>

- [Presentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#presentation)
- [Documentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#documentation)
- Changelog
- [Dedicated forum](https://community.jeedom.com/tags/plugin-mcp_jeedom)

---

## 08/07/2026

**Multi-trigger scenarios with Jeedom ≥ 4.5 tags**
The AI can now generate scenario conditions based on `#trigger_id#`, `#trigger_name#`, `#trigger_value#` and `#trigger#`, which replace the deprecated `trigger()`/`triggerId()`/`triggerValue()` functions. Two new actions in `scenario_write`: a single ready-to-paste condition, or a complete if/elseif skeleton automatically routing on several triggering commands. Deprecated syntax detected in an imported or modified scenario now raises a warning (without blocking the operation).
Example: *"Create a scenario that reacts differently depending on whether it was the living room switch or the kitchen switch that triggered it"*.

**New configuration assistant**
A 5-step wizard now opens automatically the first time you use the plugin: daemon health check, usage profile selection (Read-only / Control / Advanced), whitelist generation, first access token creation, and AI client configuration. Risky settings (local execution, file writing, restart) always remain to be enabled manually, whatever profile is chosen.

**AI-controllable views and dashboards**
Ask the AI to create a dashboard, add widget zones or command tables to it, or convert a device's tile into a compact table. It can also list your existing views and 2D floor plans (designs).
Example: *"Create a Living Room dashboard with a table grouping the lights and shutters"*.

**Lighter AI context**
The full home map format (`jeedom://full_map`) has been compacted (nearly two thirds fewer tokens), and the internal instructions sent to the AI have been simplified, for faster and cheaper onboarding, especially in long conversations.

---

## 06/06/2026

**Simpler installation: Composer dependency removed**
The plugin no longer needs to install PHP dependencies via Composer. The required elements have been integrated directly into the plugin, making installation and activation more reliable, especially on Jeedom instances where Composer is unavailable or not kept up to date.

**Home profile automatically generated from Jeedom**
The `home_profile.md` file can now be pre-filled from your Jeedom installation. The plugin keeps any personal sections already filled in, then adds the structure of visible rooms and active plugins to give the AI more context.

**New built-in MCP prompts**
Three new prompts are available on the MCP side: help creating Jeedom scenarios, a complete battery report, and a plugin audit. They guide the AI step by step and favor the Jeedom tools best suited to the task.

**Configuration interface fixes**
The configuration page now correctly loads the plugin class, no longer depends on jQuery for its recent internal actions, and fixes several cases where the dependencies or daemon blocks could remain empty on the Jeedom side.

**Daemon and maintenance fixes**
Python dependency information is correctly computed again after the Composer removal. The proxy, MCP prompts and several internal files have been synchronized with the plugin's stable state.

---

## 18/04/2026

**The AI becomes an expert of your Jeedom thanks to "skills"**
Four specialized guides are now embedded in the plugin: expert scenarios, historical analysis, energy management and user interaction. Before responding or acting, the AI automatically consults the guide best suited to your request.
Result: more reliable scenario creation, more relevant consumption analysis, fewer out-of-context responses.

**Other Jeedom plugins can expose their own MCP tools**
An extension mechanism allows developers of other plugins to add their tools to the MCP client without modifying `mcp_jeedom`. Simply drop two files in the third-party plugin (`mcp_tools.json` + `McpExtension.php`) and the tools appear automatically with the prefix `ext__{plugin}__{tool}`.
Example: a weather plugin could expose `ext__meteo__get_forecast` which the AI would use directly.

**The AI knows Jeedom generic types**
It has built-in documentation on command types (`info`, `action`, `numeric`, `binary`...) and device types (`light`, `heating`, `presence`...). It chooses the right category when creating or modifying virtual devices.

**Complete redesign of the admin interface**
The Configuration, Tokens, Clients, Whitelist and Health pages have been completely rewritten for greater clarity: better mobile display, more readable tables, better grouped actions, explicit error messages.

**Internal code reorganized (invisible to you, but more robust)**
The plugin core has been split into specialized modules (scenarios, devices, health, administration on the PHP side; references, smart tools, extensions on the Python side). Future improvements and fixes will be faster, with less risk of regression.

---

## 19/03/2026

**The AI can create, modify and manage your Jeedom scenarios** *(option to activate)*
Describe what you want, the AI takes care of everything: create a presence scenario, a temperature alert, a scheduled program with PHP code... It can also duplicate, export, import and delete existing scenarios.
Examples: *"Create a scenario that turns off the lights 30 minutes after sunset"*, *"Duplicate my Wake-up scenario and adapt it for the weekend"*, *"Export my Heating scenario so I can back it up"*.

**The AI now detects your Alexa speakers and other voice assistants**
`list_notification_commands` automatically recognizes the `alexaapiv2` plugin and other voice assistants (Google Home, Sonos...). The AI can therefore make your speakers speak directly from a scenario or in response to a question.
Example: *"Create a scenario that announces dinner time on the Echo 8"*.

**`MCP System` supervision device**
A new device appears in Jeedom, created automatically on the first daemon start. It exposes in real time:
- The daemon state (active / stopped), with history
- The currently connected client (yes/no), its IP and name
- The access type (local, LAN or external)
- The connection counter for the day, with history
You can use this information in your Jeedom scenarios (e.g. action if Claude connects, alert if the daemon stops).

**Name your AI clients**
Edit the `resources/data/client_names.json` file to associate an IP with a readable name.
Example: `"192.168.1.10": "Claude Desktop — Office"`. The change takes effect immediately, without restarting the daemon.

**The whitelist synchronizes automatically**
Each time the whitelist window is opened, devices are automatically synchronized with the real state of Jeedom: new devices added, removed devices withdrawn, new commands integrated. Your choices (authorized devices, labels, aliases) are always preserved.

**Bug fixes**
- *Comment* blocks in AI-created scenarios no longer block execution.
- The execution log of a scenario (`get_scenario_log`) is now correctly read via the official Jeedom API.

---

## 17/03/2026

**New `jeedom_api` tool — composite PHP operations**
The AI has direct access to advanced operations impossible via the standard API.
Examples: *"Give me a complete health report"*, *"Duplicate my Wake-up scenario"*, *"Create a virtual device with a temperature command"*.

Available actions:
- `healthReport` — global health report in 1 call (devices in error, low batteries, system messages, NOK daemons, available updates)
- `fullStateOptimized` — complete home state with real-time values, optimized format for the AI
- `getCommandValues` — values of multiple commands in a single call
- `duplicateScenario` — complete copy of an existing scenario
- `bulkScenarioAction` — activate, deactivate, trigger or stop all scenarios in a group
- `saveVirtualDevice` — create or update a virtual device with its commands
- `saveCommand` — create or update a command on a device
- `removeDevice` — delete a device (mandatory confirmation)
- `objectSummary` — aggregated Jeedom summaries (lights on, shutters open...)
- `sequenceActions` — sequence of actions with configurable delay between each

**New available tools**
- `get_cron_list` — lists 27 internal Jeedom scheduled tasks with their state and last execution
- `list_variables` / `get_variable` / `set_variable` — Jeedom global variables now accessible

---

## 16/03/2026

**The AI can now create and modify your scenarios** *(option to activate)*
Ask the AI to create a gradual wake-up scenario, a presence alert, a thermostat program... It has 8 ready-to-use templates, validates the structure before saving and always asks for confirmation before deleting.

**The AI can read the pages of your Jeedom interface** *(option to activate)*
Useful for analyzing a dashboard render, inspecting a widget or debugging a configuration page.

**Complete health page**
A new *Health* button in the plugin management displays at a glance: daemon state, dependencies, transport, external access, active permissions and home profile.

---

## 15/03/2026

**The AI better understands your home**
The AI can now see the complete state of the home in a single call, find any command by its role (`light`, `temperature`, `shutter`...) without knowing its ID, and detect what has changed since this morning.

**The AI can see your cameras** *(option to activate)*
Ask the AI what is happening in the living room or if someone is in the garage — it analyzes the live snapshot.

**The AI can send you notifications** *(option to activate)*
Via Telegram, Mail, Slack, Pushover... or any other notification plugin already configured in Jeedom.

**Read-only mode**
Give the AI consultation-only access, with no risk of accidental action.

**Audit log**
Every action performed by the AI is traced (tool, command, result) in a file viewable from the configuration.

**Home profile**
Enter the names of residents, usual schedules and preferred notification channel — the AI adapts its responses to your installation.

**Streamable HTTP transport**
New, more modern and more stable connection method, enabled by default. The old SSE mode remains available.

---

## 14/03/2026

- Beta version released.
