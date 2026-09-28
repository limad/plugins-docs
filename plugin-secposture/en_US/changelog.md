---
layout: default
title: secposture — Changelog
lang: en_US
pluginId: secposture
---

[Limad44 Jeedom Home Automation](https://limad.github.io/plugins-docs)

# Changelog — secposture

## 0.4

- Translations: English, German, Spanish, Italian, Portuguese.
- Market-format icon (309 × 348).
- Documentation: requirements, list of system commands run.

## 0.3

- File integrity: SHA-256 baseline of the core, plugins and sensitive system files; changes correlated with Jeedom updates (expected) or reported until accepted (unexpected).
- Executables in `/tmp`, `/var/tmp`, `/dev/shm`.
- "Modified files (unexpected)" and "Accept file changes" commands; configurable exclusions.

## 0.2

- New checks: listening TCP ports, nftables firewall (IPv4/IPv6), effective SSH configuration, sensitive folders served by Apache, HTTP headers, web server PHP, fail2ban/CrowdSec and f2ban plugin.
- Configuration: allowed ports, web checks toggle.
- Default configuration values in `core/config/secposture.config.ini`.

## 0.1

- First version: 12 checks (Jeedom, system, MariaDB, backups), score and commands.
