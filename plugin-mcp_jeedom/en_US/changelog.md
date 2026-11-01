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

## 01/11/2026

**New: daily anomaly digest**

- AI customization tab: the plugin can send you the box's anomalies every day (low or critical batteries, devices in alert, out-of-range values). Disabled by default.
- Free and AI-free: the list is computed by Jeedom. Nothing is sent when there is no anomaly and, if you wish, only when the list has changed since the last one.
- Sent through the notification command of your choice, or to the Jeedom message center.
- Optionally, an AI summary groups the anomalies by likely cause (for example several sensors of the same plugin offline: gateway down). At most one call per day, only when there are new anomalies (about 1 to 2 k tokens), through the AI Assistant integration. The names of the rooms and devices involved are sent to the chosen provider.
- New setting **Daily cap on automatic AI calls** (20 by default, 0 = none): it limits every AI feature that runs without an action from you. Generations started from the configuration page are not counted.

**More tolerant device search**

- When an AI looks up a command by device name (`find_command`) and the exact search finds nothing, the plugin tries an approximate match: accents, plurals and small typos are tolerated ("lumieres salon" finds "Lumière Salon"). No AI involved, nothing to configure.

**Lighter responses for AI clients**

Tool responses cost fewer tokens for every AI client, with no answer lost on the 22-question reference set (11 % fewer tokens used on that set).

- `get_full_state`: the `compact` format keeps only useful information (8.7 k → 3.3 k tokens on a test box); the `summary` format includes thermostat setpoints.
- Text format (full state, room state, device sheet, device lists): up to 57 % less text (40 % for the full state). A command type's hint appears once per response, and very long values (camera history, JSON, signed URLs) are truncated, with their size and the call that returns the full value.
- The `get_full_state` description points AI clients to targeted tools (`find_command`, `search_devices`) for a specific question, instead of loading the whole home state.
- Faster client connection: the session-opening response drops from 136 KB to 4 KB (the server icon is given by URL instead of being embedded).
- **Fixed**: in `get_full_state` (`summary` and `compact` formats), AI clients sometimes took a device ID for a command ID, causing an error and a wasted tool call. It is now labelled `eq_id`.

**OAuth MCP**

- **Fixed**: Claude (`claude.ai`), Le Chat (`callback.mistral.ai`, and `console.mistral.ai` for the Mistral connector debugger) and ChatGPT (`chatgpt.com`) are always accepted as OAuth redirect addresses. A configuration saved before one of these clients was added could block its registration. The **Redirect hosts autorises** field is now only for other clients' hosts.

**Tokens**

- Token creation dialog: the role's permission details are collapsed by default; click the role description to show them.

**Translations**

- **Fixed**: in English, the AI customization tab showed the description of the shell option instead of its name, and three nearby texts were shifted the same way.
- About 40 recent texts (anomaly digest, AI call cap, role capabilities, OAuth warnings) are now translated in all languages.

## 30/10/2026

**Getting ready for Alexa+**

- The plugin is ready for the upcoming Alexa+ add-ons (an Amazon preview program, currently limited to selected partners). Alexa will be able to discover your box's tools before you link your account, then ask you to link it on first use.
- Until the account is linked, Alexa sees the list of tools but cannot read or control anything in the home.
- **Fixed**: one of the OAuth discovery addresses used by some clients answered "not found" on a standard Jeedom box.
- Nothing changes for clients already connected (Claude, ChatGPT, Le Chat…).

## 29/10/2026

**Requirements: Jeedom 4.6 and Debian 12**

- The plugin now requires **Jeedom 4.6** or later.
- **Debian 11 is no longer supported** (end of life): upgrade to **Debian 12** or later. On Debian 11, installing the dependencies may fail, because Debian 11 security packages are no longer on the usual repositories. A message warns you on install and update; the plugin keeps working.

**External access: standard error codes**

- An invalid, disabled or expired token now gets a **401** response with `error="invalid_token"` (instead of 403), as the OAuth standard requires. A client connected through OAuth (Claude, ChatGPT, Le Chat…) whose token expired, or whose Jeedom token was disabled, now knows it must re-authorize.
- 403 is kept for when external access is not set up (no token, or OAuth disabled).
- **Fixed**: on a box without external access configured, the OAuth discovery address sent to LAN clients was incomplete (`http:/plugins/…`, without the box name).

- **Fixed**: right after the box starts, the daemon could ignore other plugins' extension tools for 30 seconds.

**Configuration page**

- Tabs now appear in the page address (`#tab-…`): you can bookmark or share a direct link to a tab, and the tab stays open after a reload.
- "Role capabilities" tab: each role's **View capabilities** button opens the list of tools and actions that role can use. It reflects your settings: an action blocked by a disabled option or by read-only mode is struck through, with the reason.
- The "Read-only" role is now called **Limited user**, so it is no longer confused with the box's **read-only mode** (which blocks all actions for all tokens). Its permissions are unchanged.
- The "Advanced" tab is merged into "Startup" (transport, port, tools mode).
- OAuth MCP: a warning reminds you that a remote client can reach your home at any time and that its token is stored by the provider. Keep it for trusted clients and real needs.
- AI Assistant integration: the options stay visible but greyed out, with a message, when the AI Assistant plugin is missing or disabled.
- Tokens, Clients and Whitelist windows are wider; token switches are easier to read, including when off.
- Plugin page: the Documentation and Community tiles now come last.

**Translations**

- Interface fully translated into English, German, Spanish, Italian and Portuguese (about 170 texts were shown in French).
- The "… ago" duration (last use of a token) and the whitelist "Enter to add" hint are now translated correctly.

## 28/10/2026

**Security: SSE (legacy) and STDIO transports removed**

- **Fixed (serious), installations set to the SSE transport only**: token roles were not enforced there, and every token acted as a Super-administrator. The Streamable HTTP transport, the default setting, was not affected.
- **The SSE (legacy) and STDIO transports are removed.** Only Streamable HTTP remains. On update, a configuration set to SSE or STDIO automatically switches to Streamable HTTP and a message lets you know. Jeedom restarts the daemon if its automatic management is enabled; otherwise it stays stopped, as you chose.
- **Plugin update**: Jeedom restarts the daemon according to your automatic management setting. The plugin no longer forces a restart (as the 23/10 entry stated).
- **Clients set up with `mcp-proxy --sse-url …`**: replace their configuration with the one from the plugin's **Clients** window (Streamable HTTP, or the `mcp-proxy --transport streamablehttp` / `mcp-remote` bridge).
- Defense in depth: a request whose role is unknown no longer has any right (no tool listed, every call refused).
- A whitelist refusal (device or command not allowed) is now reported as an error to the AI client. `execute_action_by_name` no longer reveals that a device outside the whitelist exists.

**Self-test and fixes found by the new tests**

- **New: self-test** ("Auto-diagnostic" button in the Health window, and `system` → `selftest` action for an Administrator token). It checks the MCP SDK version, role consistency, permissions, tool schemas, third-party plugin extensions, the daemon, and the backup and log folders.
- **Fixed: empty backup list** (`system` → `backup_list`) when the backup folder is relative (the default). Restoring by file name was refused for the same reason.
- **Fixed: a scenario's execution log was always empty.**
- **Fixed: presence simulation did not turn the lights off** when stopped.
- **Fixed: audit log size always 0** in the Health window.
- **Security, third-party plugin extensions**: a tool that declares `readOnlyHint` other than as the boolean `true` (for example the string `"false"`) is treated as a write, so reserved to the Administrator. Badly declared tools are dropped or fixed, with a warning in the log.
- Internal errors that went unnoticed are now logged.

**Refocused roles: the Operator controls, the Administrator configures**

- **Operator**: views and controls the home (allowed devices, start/stop scenarios, modes, presence, notifications, variables). It no longer changes the Jeedom configuration.
- Now **Administrator**: rooms and devices (create, rename, move, disable, delete), crons, enable/disable a scenario, delete a variable, acknowledge messages, views and designs, mode schedule.
- Now **Super-administrator** (shell equivalents): writing files and cleaning logs, restoring a backup, raw API calls (`rpc`) that write.
- The plugin's internal variables (`home_mode`, `mcp_…`) can only be changed by an Administrator; the Operator changes mode through `home` → `mode`.
- **Check your Operator tokens**: if they were used for these configuration actions, switch them to Administrator.

**The "Read-only" role is back**

- New **Read-only** token role: the client views the home (states, history, logs and cameras if allowed) but cannot control or change anything. Ideal for a low-cost voice assistant next to an Operator assistant.
- The restriction is enforced by the plugin, not by the AI, and also applies to clients connected through OAuth (role read again on every request).
- An old "read-only" token disabled by the 26/10 update can now be switched back to Read-only from its menu, **before** being re-enabled (the role can be changed on a disabled token).

**Each client only sees what it can do**

- The tool list sent to the AI no longer contains the actions refused to its role (nor in read-only mode): actions, description and parameters are trimmed. Fewer tokens used, no more useless attempts. Reconnect the client after a role change to refresh its list.

**Security**

- `interactions` → `test` really runs the interaction: reserved to the Administrator and blocked in read-only mode.
- `notify` → `send` only accepts the notification commands of allowed devices (whitelist).
- `camera` → `send`, `execute_action_by_name`, `devices` → `refresh` and write extension tools are blocked in read-only mode, on both the daemon and PHP sides.

**Extension tools (other plugins, e.g. Alexa)**

- They were no longer loaded: fixed.
- Tools that only read (e.g. listing Alexa devices) are open to every role. Those that act (speak, announce, voice command, routine…) are **reserved to the Administrator**: they bypass the list of allowed devices.

**AI Assistant integration: explicit permission**

- New "Intégration AI Assistant" section (AI customization tab): **Autoriser le plugin MCP à utiliser AI Assistant** checkbox (allow the MCP plugin to use AI Assistant), **off by default**, and choice of the **AI Assistant device** (default: AI Assistant's default provider).
- As long as it is unchecked, nothing is sent to AI Assistant: camera descriptions, generation of the home profile, labels, aliases, generic types and mode schedule. **After the update, check it if you used these generations.**

**Text description of cameras (Claude voice mode)**

- In voice mode, the Claude app does not show the model the images returned by a tool: Claude receives the snapshot but does not see its content. New `camera` → `describe` action (`describe_camera` tool in legacy mode): the image is described on the Jeedom side and Claude receives a text it can read aloud.
- Works for every camera already supported, Netatmo included.
- Requires the **AI Assistant** plugin and a device that reads images. Each description is billed by that provider.
- "Description texte des caméras" setting (text description of cameras), on by default, under the Cameras option of the configuration.
- Text mode does not change: `snapshot` still returns the image.
- Generations through AI Assistant (home profile, labels, aliases, generic types, mode schedule) no longer keep history between two calls and can no longer trigger a Jeedom command.

## 26/10/2026

**New token role model: Operator, Administrator, Super-administrator**

- Tokens now have 3 roles: **Operator** (view and control the home), **Administrator** (plus: scenarios, files, system, updates) and **Super-administrator** (plus: shell on the server and restarts). See the documentation, "Tokens and roles" section.
- The former "guest" and "read-only" roles are removed. On update, such tokens become Operator and are **disabled**: re-enable them if needed. An administrator token that had the shell becomes Super-administrator, and its list of allowed commands is kept.
- New **Maximum allowed level** setting (State only, Control, Advanced, Full): it caps what tokens can do on the box, whatever their role. Capabilities can still be set one by one ("Custom" level).
- A role change or a disabled token takes effect immediately, including for clients connected through OAuth, without a restart.
- Each AI client now only sees the tools allowed for its role.
- A request without a valid token is now refused, even when sent directly to the MCP server.
- Redesigned token management: rights table per role, enable/disable switch on each card, All/Active/Disabled filter, up-to-date "Used … ago" for OAuth sessions.
- Reorganised configuration page: "Global capabilities" (maximum level) and "Role capabilities" tabs; the "restart the daemon" banner no longer shows up wrongly when the page opens.

**Security: each role strictly stays within its limits**

- Only a Jeedom administrator can now authorize an AI client through OAuth (Claude, ChatGPT, Le Chat…). A regular Jeedom account can no longer grant administrator or super-administrator access.
- Shell commands limited to a list (on a token or in the configuration) can no longer be abused to run another command: chaining characters (`;`, `&&`, `|`, `$(…)`…) are refused.
- An Operator token can no longer run administrator maintenance actions (database cleanup, file permissions, cache flush…) by bypassing the intended tools.
- Read-only mode also blocks these maintenance actions and message acknowledgement.
- The advanced call tool (`rpc`) now honours the plugin options (camera, logs, file writing) and can no longer restart the server or create scenarios without the usual checks.
- Scenarios containing PHP code or a box restart/shutdown action are reserved to Super-administrator tokens. Other scenarios are created as before.
- An Operator token now only manages devices of the whitelist (enable, rename, move…), and presence simulation only switches on allowed lights. Administrators still manage all devices.
- Browsing server folders is now reserved to administrators, like reading files.
- Fix: the "token unused for 30 days" alert was never sent.
- Fix: deleting a device through the AI always failed, even when confirmed; conversely, deleting an object went through even with `confirm=false`.
- Fix: after a restart requested by the AI, the server refused every connection.
- Fix: the "active only" filter of the scenario list, and unknown IDs in grouped updates.
- Lighter logs: at "info" level, the server now only writes one line at startup plus the useful events (shell commands run, OAuth sessions opened, administration actions, errors). Details of tool calls, HTTP requests to Jeedom and startup move to "debug".

## 25/10/2026

**Secret files protected and cleaner logs**

- No AI client, not even an administrator, can read or modify the box's secret files anymore (database credentials, OAuth tokens, private keys…).
- The server log no longer shows a "connection" for every tool call, and follows the log level set in Jeedom.
- One audit log line always matches one action.
- Fix: in "individual tools" mode, the instructions sent to the AI mentioned tools that do not exist in that mode.

## 23/10/2026

**Hardened security: direct access, file reading, logs, and a new diagnostic guide**

- Log search no longer returns the passwords, tokens or cookies it comes across — they are masked before being displayed.
- Internal connections (from the box itself) are no longer rate-limited like external ones.
- New internal guide to produce a reliable security report, without depending on optional extensions that may not be installed.
- Fix: the server restart tool was missing in one of the two tool modes.

## 25/09/2026

**MCP best practices guide and public skill**

- New resource `jeedom://best_practices`: token savings (`state find` vs `full`, `format=summary|compact`, `etag` reuse, bulk read/execute, avoid `full_map` unless really needed) and scenario syntax pitfalls (`trigger_tags`, quotes, the old `#cmdId#` notation deprecated since Jeedom ≥ 4.5). Also published as a public skill for Claude Code / Codex: [github.com/limad/mcp_jeedom_skills](https://github.com/limad/mcp_jeedom_skills)
- Daily alert when the Python dependencies drift from `requirements.txt`.
- Python `mcp` SDK updated to 2.2.0 (MCP spec revision 2025-11-25).
- Fix: the documented folder for third-party MCP extensions did not match the one actually scanned (`plugin_info/mcp_extension/`).

## 29/08/2026

**History analysis across several commands at once**
The history and statistics tools now accept a list of commands in a single call. You can filter data points on a value threshold, keep only state changes, or aggregate by hour or by day. Statistics additionally return the standard deviation, the sum, the last value and the timestamps of the min/max.
Example: *"Compare the living room, bedroom and outdoor temperatures over the last 7 days, daily average"*.

**Detection of deleted command references**
A new diagnostic tool lists the `#cmdId#` references left in your scenarios, objects, views, designs and interactions after a command or device was deleted. The number of dead references is also surfaced in the system diagnosis. A second tool rewrites those references to a valid command, in bulk, with a preview before applying (admin only).
Example: *"Which deleted commands are still referenced somewhere?"*.

**"Where is it used?" search**
Ask the AI where a command or device is used: it scans scenarios (triggers and expressions), views, designs and the configuration of other commands. A free-text search inside scenario expressions is also available.
Example: *"In which scenarios is the boiler temperature used?"*.

**Automatic daemon restart on update**
After a plugin update, the daemon is restarted automatically to load the new code, provided it was already running (or automatic start is enabled) and the dependencies are ready. No more manual restart in most cases.

**Fixes**
The log analysis, log cleanup and disk usage tools were pointing at the wrong folder and finding no files: they work again. Very high-precision history values (`49.000000000`) are now displayed cleanly (`49`).

---

## 19/08/2026

**Migration to MCP SDK 2.0**
The Python daemon now runs on version 2.0 of the official MCP SDK. The `uvx mcp-proxy` bridge used by some clients that do not yet support native HTTP (JetBrains, Google Antigravity) was fixed by pinning its version.

**External authentication cleanup**
The old single external access token has been removed: only multi-client tokens (with roles) and OAuth 2.0 remain. The old file-based token storage is migrated automatically and definitively to the Jeedom database on update, transparently.

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
