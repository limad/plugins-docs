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

### 30/10/2026

- **Alexa skill (voice control)**: in French, the skill is now called "ma jeebox" ("Alexa, demande à ma jeebox…"); free-form questions are much better understood and passed to the AI, including questions about times or durations; answers in the Echo's language (French, English, Spanish); when Alexa is not sure which command it understood, it asks for confirmation before acting ("Did you mean…?"); a command is no longer run twice when Jeedom answers slowly; Jeedom interaction answers are easier to understand. Redeploy the skill from the plugin to benefit from these changes
- **Security and reliability**: the AI is no longer wrongly set aside after a slightly slow answer; API key hidden in command logs

### 28/10/2026

- **New features and fixes**: the "Run scene" and "Stop scene" commands of Alexa scenes actually trigger the scene again (they could report success without any effect); a failure is now reported as an error; voice history is retrieved reliably (it failed every other time)

### 16/10/2026

- **New features and fixes**: "Last dialog" and "Last response" update again after every exchange with Alexa (they could stay frozen); the History window now shows Alexa's answer, and a missing answer is fetched automatically when Amazon has recorded it

### 15/10/2026

- **New features and fixes**: SmartHome states (plugs, lights, sensors, thermostats) updated in real time within seconds; more stable Alexa connection (interruptions are recovered automatically); voice history: fewer missing sentences; History window without duplicates and up to 80 entries displayed (instead of 10); no more repeated alerts when Amazon Music playlists are unavailable
- **Security and reliability**: unreachable devices automatically disabled after 7 days without any answer (configurable, 7 minimum) and re-enabled as soon as they answer again

### 14/10/2026

- **Voice Query (AI)**: new "Choose devices" window to decide what the AI can see and control; number of commands and context size adjustable for large installations; alarm, lock, gate, garage and siren can never be controlled by the AI; faithful answers (a failed action is no longer confirmed); with no device selected, Voice Query does nothing and warns you in the message center
- **New features and fixes**: third-party scenes (TaHoma, Somfy…) and disabled scenes displayed correctly; works on Jeedom installations without jQuery (Account Linking, skill management, interaction wizard, widgets); translations completed (English, German, Spanish, Italian, Portuguese)
- **Security and reliability**: authentication renewed more safely (fewer interruptions and "429" errors); "Speak" button more reliable; "Vendor ID is required" error fixed; no more false alerts on restart *(thanks Neurall)*; API key and session cookies hidden from logs

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
