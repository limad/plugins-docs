---
layout: default
title: Alexa Premium — Changelog
lang: en_US
---

[Limad44 Jeedom Home Automation](https://limad.github.io/plugins-docs)

# Changelog — Alexa Premium

<a href="https://limad.github.io/plugins-docs/plugin-alexaapiv2">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png" alt="alexaapiv2 icon" width="120px">
</a>

- [Presentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#presentation)
- [Documentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#documentation)
- Changelog
- [Dedicated forum](https://community.jeedom.com/tags/plugin-alexaapiv2)

---

## Version in development

### Voice Query (AI)

- **AI context adapted to large installations**: the maximum number of commands (350 by default) and the maximum context size (6,000 characters by default) can now be set in the plugin configuration
- **Exposed devices**: new "Choose devices" modal (search, per-room checkboxes, counters) to select precisely what Voice Query can see and control
- **Echo room first**: devices in the room of the Echo that receives the request are processed first and are no longer dropped by the command limit
- **Clean truncation**: the context is cut at the end of a line, no longer in the middle of a device
- **No more duplication with ai_assistant**: a dedicated system prompt prevents ai_assistant's device list from being added to the plugin's own, and action execution remains validated by the plugin
- **Selection required**: with no device selected, Voice Query exposes nothing and raises an alert in the message center; the sentence is then handled by the regular Jeedom interactions (no more voice outage)
- **Sensitive commands never exposed**: alarm, lock, gate, garage and siren (generic type or command name) can no longer be controlled by the AI; a padlock flags them in the modal
- **Truthful replies**: if execution fails, the reply says so instead of confirming "done"
- **Checked values**: sliders clamped to the command's min/max, strict command ids, choice-list commands are not controllable
- **Reliable context**: line breaks and brackets removed from names and values sent to the AI, device order identical from one request to the next, and only commands actually visible in the context can be executed (a truncated command no longer can)
- **Counter and limits**: the configuration badge only counts truly exposable devices, deleted ids are dropped from the selection; limits of 1,000 commands and 20,000 characters; a warning is logged when the context is truncated

### Reliability and performance

- **No more zombie daemon at startup**: the "PID active but HTTP unreachable" warning no longer appears during legitimate restarts (30 s grace period before reporting)
- **End of hourly 429 errors** on `/homegroups/{id}/devices`: the SIP device cache goes from 1 h to 7 days, with automatic invalidation on a manual scan
- **Atomic cookie refresh**: no more concurrent double refresh on simultaneous 401s (avoids failed authentication cascades)
- **Targeted daemon listeners**: no more listener memory leak at each daemon restart
- **"Speak" modal now uses direct PHP**: the "Speak" button uses the 4-way round-robin (P1/P2/com/actor) instead of the daemon, ending curl error(28) timeouts
- **Gradual daemon decommissioning**: the `addList`, `addItem`, `modifyItem`, `deleteItem`, `getPlayerInfo` and `getMedia` commands now go directly through PHP — lower latency and better resilience
- **`comCookie` no longer stays frozen indefinitely**: automatic renewal every hour (previously it only happened by chance depending on routine execution)
- **Automatic reconnection of the expired Amazon developer session**: removes the *"Vendor ID is required."* error when deploying the voice skill
- **Better categorization of Alexa scenes**: third-party scenes (TaHoma, Somfy…) and disabled scenes are now displayed and refreshed correctly

### Interface and translations

- **Account Linking works without jQuery**: the "Check status", "Configure" and "Delete" buttons no longer return "$ is not defined" when Jeedom runs in jQuery-less mode (`core::jqueryless`)
- **Skill modals and interactions wizard without jQuery**: "Deploy skill", "Manage skills", "Skill health" and the interactions wizard work in jQuery-less mode (`core::jqueryless`); an HTTP error is now reported instead of leaving the spinner running
- **Dashboard widgets without jQuery**: the Bluetooth and mute icons update and the "speak" widget button works in jQuery-less mode
- **Translations completed**: the configuration texts (Voice Query, authentication panel, session logout, P2 → P1 import) and the device selection modal are translated into English, German, Spanish, Italian and Portuguese

### Security

- **`pluginKey` (Jeedom API key) no longer appears in logs**: level lowered from `info` to `debug`
- **Session cookies (`at-main`) masked in debug logs**
- **Daemon forwarder blocks private IPs**: SSRF hardening
- **Controlled recursion** on `setCookie`: protection against malformed cookies with infinite structure

### Compatibility

- PHP 7.4.33 compatibility: `match()` expressions (PHP 8.0+) and `str_contains()` (PHP 8.0+) replaced with PHP 7.4 equivalents
- Startup log message adjustments *(thanks Neurall)*

### Cleanup

- `.gitignore` rewritten (153 → 66 rules, -57%), organized by category
- Sprint D SmartHome Skill files removed from the public repo (parked pending Amazon Lambda changes) — code kept locally
- Obsolete native "SmartHome Skill" deployment code removed (moved to a dedicated plugin)

### 06/06/2026

- ASK flow improvement: question state isolated per Echo device to avoid multi-device collisions.
- Added protection against late answers: an ASK answer is ignored if it no longer matches the expected event or device.
- Added the `alexaAskSilent` command to chain multi-turn ASK dialogs without triggering a new voice announcement.
- Cleanup of the old `reponseASK` paths and removal of divergent legacy code.
- Fixed the ASK Skill model on the `VoiceQuery` slot and improved the related tests.
- Improved internationalization of scenario and widget HTML templates, with the translation workflow now triggered on `.html` files.
- Documented the internal behavior of the ASK flow and added unit tests on the ASK state.

### 30/05/2026

- Added automated deployment of the Alexa Premium ASK Skill from Jeedom.
- Added the JeeViewer Skill to display Jeedom on compatible Alexa screens.
- Added a voice interaction creation wizard.
- Added VoiceQuery mode to query Jeedom in natural language.
- Added an Alexa Skill health panel.
- Added an Alexa Skills management screen.
- Added OAuth support for Alexa / Jeedom account linking.
- Improved Amazon / Alexa authentication.
- Improved management of devices, routines, reminders, alarms and notifications.
- Improved SmartHome scan and GraphQL commands.
- Improved management windows: reminders, routines, history, health, requests and speak.
- Improved multilingual translations.
- Fixes on Alexa commands, voice interactions and SmartHome feedback.
- Security fixes on logs and protection of sensitive information.
- Internal plugin cleanup and removal of obsolete files.

## April 2026 — Major overhaul

> This version is a deep rewrite of the plugin (~80% of the code). It lays new foundations that are more stable, safer and better suited to Amazon's evolutions.

### Authentication

- **New proxy-free authentication method**: the connection to Amazon no longer goes through an intermediate proxy — authentication is now direct and independent of the daemon
- **Dual profile**: a second authentication profile can be created, giving better resilience against Amazon throttling (429 error)
- The daemon is now only used for some authentication information; its code no longer depends on the usual external libraries
- Support for **19 Amazon countries**, detected automatically (fr, de, uk, it, es, jp, com…) — no more hard-coded values

### SmartHome

- **Full migration to Amazon's new endpoints** (GraphQL API): better reliability and compliance with Amazon's current infrastructure
- **Grouped refresh**: several devices are updated in a single request (4× fewer Amazon calls, lower throttling risk)
- New: support for **robot vacuums** — Dock, Clean and room selection actions
- New: support for **instance switches** (ToggleController)
- Enriched scan: supported modes, value ranges and states are detected and configured automatically
- Fixed an error when scanning devices with a mode controller (washing machine, air conditioner…)

### Voice history

- History acquisition is now **disabled by default** — it is strongly recommended to leave it disabled if you have no regular use for it (significant performance impact)

### Interface

- **Complete modal overhaul**: Alarms, Routines, History, Speak, Reminders — each fully rewritten
- jQuery removed: the whole interface moves to native JavaScript (Jeedom 4.4+)
- Routines modal: resizable table, reorganized columns, per-routine configuration button
- Reminders/Alarms modal: device filter, edit button, tab navigation
- History modal: translated voice command types, optimized display
- Speak modal: fixed test bar, 40+ SSML examples, copy by double-click
- Improved dark theme (native Jeedom CSS variables)
- Configuration page: Amazon country selector replaces the free text field

### Security

- Logs no longer contain any token, cookie or password in clear text
- CSRF protection, XSS fixes, hardened system calls

### Optimization

- Massive code cleanup: removal of dev files, dead code and runtime data committed by mistake
- Full PHP, JS and daemon audit: 80+ bugs fixed
- Daemon response format standardized across all routes
