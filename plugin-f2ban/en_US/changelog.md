---
layout: default
title: f2ban — Changelog
lang: en_US
pluginId: f2ban
---

[Limad44 Jeedom Home Automation](https://limad.github.io/plugins-docs)

# Changelog — f2ban

## 2026-09-20

- All commands use a widget from the iso widget set, downloaded from GitHub when the plugin is installed and updated (Jeedom default widget if missing): **iso_line** for infos, **iso_btn** for *Refresh*, *Banip* and *Unbanip*. Existing commands still using the default widget, the old *line* widget, a widget that does not exist or *iso_msg_dialogue* (reserved for "chat" commands with a linked info) are migrated once on update; other widgets are not changed
- Commands are now described in `core/config/commands.json`: translatable names (Jeedom language at creation) and generic type filled in (*Connected* for *Online*, *Generic info/action* for the others)
- The *Banip* and *Unbanip* commands are linked (linked info command) to the *Last banned IP* and *List of banned IPs* infos of the same jail, visible and editable in the *State* column of the commands table
- New **Default config** button on the device page: recreates missing commands (even without a connection to the target) and reapplies the iso widget, the generic type and the default linked info command, without overwriting another existing widget you chose
- Robustness: if `commands.json` cannot be read, the error is logged only once and IPs waiting for geolocation are kept instead of causing a fatal error

## 2026-09-19

- The plugin is renamed **f2ban** (the id changes from `fail2ban` to `f2ban`). On activation, existing devices, commands and settings are taken over automatically
- New icon
- New interface inspired by the smartctrl plugin: devices grouped by mode, *Health* window with live updates, ban/unban an IP from the window, *Assistance* button, commands table with compact/edit mode
- SSH authentication with a private key (in addition to the password)
- *Test the connection* button with a diagnosis of the cause on failure, and *Refresh now* button
- The plugin no longer uses the `mips/httpclient` and `mips/jeedom-tools` packages (geolocation now uses `connector.php` with timeouts)
- phpseclib update (3.0.57, enforced minimum version)
- AGPL v3 licence
- Fix: the per-country counter caused an error (TypeError) in PHP 8 on the first IP of a new country, which interrupted the refresh
- Per-country counters: an IP banned in several jails is counted only once, and an IP whose geolocation failed (ip-api limit, network) is retried on the next refresh instead of being lost. 30 requests maximum per pass, country cached per IP
- SSH: a single connection per refresh instead of one per jail, and jail detection is no longer disturbed by sudo error messages (e.g. `unable to resolve host`)
- Security: PHP files in `plugin_info/`, `core/php/` and `core/class/` are no longer directly reachable over HTTP
- SSH security: the server key fingerprint is recorded on the first connection then checked before sending the password or private key (*Reset* button on the device page)
- New **Online** info command (1 if fail2ban answered during the last refresh, 0 otherwise)
- Jails added on the target are detected on each refresh, not only when the device is saved
- A failing device no longer interrupts the refresh of the following ones
- A jail removed on the target is no longer refreshed forever with frozen values: its commands are hidden (history kept) and a message is added; they become visible again if the jail reappears
- New option *Disable the geolocation of banned IPs* (plugin configuration): no IP address is then sent to ip-api.com

## 2026-06-12

- New deployment flow for the documentation

## 2026-05-12

- Added dependencies to install fail2ban-client
- Dependency updates
- Jeedom v4.5 required

## 2025-08-11

- Dependency updates

## 2024-12-25

- Dependency updates
- Icon update

## 2024-10-17

- Dependency updates
- Jeedom v4.4 required

## 2024-09-16

- Dependency updates
- Plugin translated into English, German, Spanish, Italian, Portuguese
- Debian 11 minimum required

## 2024-04-10

- Dependency updates

## 2023-11-01

- Fix for the **List of banned IPs** commands, which were not correctly cleared when no IP was banned any more
- Default template changed for info/numeric commands

## 2023-10-23

- Dependency updates

## 2023-10-21

- Change: default cron set to 10 min

## 2023-10-06

- Added a **Last banned IP** command per jail

## 2023-10-01

First version

# Documentation

[See the documentation]({{site.baseurl}}/plugin-{{page.pluginId}}/{{page.lang}})
