---
layout: default
title: Security Posture — secposture plugin for Jeedom
lang: en_US
pluginId: secposture
---

# Description

**Read-only** audit of the Jeedom box security posture. The plugin checks the configuration every day and on demand, shows each finding with a suggested fix, and exposes a score and counters as commands (scenarios, notifications, history).

> **Important**
>
> The plugin changes nothing: it does not touch SSH, the firewall or MariaDB. It detects configuration drift, not an attacker who already has root rights (who can disable it).

# Requirements

| Component | Version |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | 11 (Bullseye) or 12 (Bookworm) |

No dependency to install. The plugin uses the `sudo` rights of `www-data` present on a standard Jeedom installation; without them, the related checks become **Unknown** (never OK).

Docker installations are not supported: system checks would inspect the container, not the host.

# Checks

| Check | Critical / Warning when… |
|---|---|
| Default admin account | the `admin` account has the password `admin` (hash comparison, no login attempt) |
| Two-factor authentication | an active administrator has no 2FA |
| Banning | the whitelist contains every address (critical); banning disabled |
| API access | an API is "Enabled" without IP restriction |
| Jeedom updates | core/plugin updates are pending |
| Plugin sources | *(info)* plugins outside the Market or in beta |
| www-data sudo | *(info)* unlimited sudo, standard on Jeedom: reminder of the consequences |
| Automatic Debian updates | `unattended-upgrades` missing or inactive |
| MariaDB exposure | listening outside loopback (critical); `local_infile` enabled |
| SQL privileges | Jeedom's SQL user has a global privilege |
| Backup age | no backup, or older than the threshold (48 h by default) |
| Copy outside the box | no Samba/Market upload configured (normal if a NAS pulls the backups) |
| Listening ports | a TCP port outside loopback is not in the allowed list |
| Firewall | no `input` chain with `drop` policy (nftables), or IPv4/IPv6 not covered |
| SSH | root + password (critical); root or password allowed (effective `sshd -T` values) |
| Served folders | `backup/`, `log/`, `data/` listed or `.git/` readable over HTTP (critical) |
| HTTP headers | *(info)* Apache version visible, `X-Content-Type-Options` / `X-Frame-Options` missing |
| Web server PHP | `display_errors` or `expose_php` enabled (read from the Apache/FPM php.ini, not the CLI one) |
| Detection | neither fail2ban nor CrowdSec active; *(info)* f2ban plugin without device |
| File integrity | file added/modified/deleted outside an update; critical for code (`.php`, `.sh`, `.py`…), `sudoers`, `cron` or `authorized_keys` |
| Temporary executables | executable file in `/tmp`, `/var/tmp` or `/dev/shm` |

A check that cannot run is marked **Unknown**, never OK.

# Score

`100 − 25 × critical − 5 × warnings` (minimum 0). *Info* and *Unknown* findings do not count.

# Commands

| Command | Type |
|---|---|
| Score | numeric info (historized) |
| Critical | numeric info (historized) |
| Warnings | numeric info (historized) |
| Last backup age | numeric info (h) |
| Last audit | string info |
| Summary | string info (for a notification) |
| Modified files (unexpected) | numeric info (historized) |
| Run audit | action |
| Accept file changes | action |

Scenario example: trigger `#[Security posture][Critical]# > 0` → send `#[Security posture][Summary]#` as a notification.

# File integrity

On the first audit, the plugin records a **baseline** (SHA-256 fingerprint) of the Jeedom core files (`core/`, `desktop/`, `mobile/`, `install/`), the plugins, and sensitive system files read through sudo: `sudoers`, crontabs, local systemd units, `authorized_keys` of root and users.

On each audit:

- a change in the core or a plugin **updated since the baseline** (different Jeedom update date) is **expected**: that group's baseline is replaced automatically;
- any other change is **unexpected** and stays reported until you click **Accept file changes** (plugin page or action command);
- a system file change is always unexpected (including during a Debian update: accept it after checking).

Always excluded: `data/`, `venv/`, `node_modules/`, `.git/`, `__pycache__/`. Add other patterns in the configuration.

Limits:

- a file replaced while keeping exactly its size and modification date is not re-hashed;
- the baseline is stored on the box (`plugins/secposture/data/baseline.json`): a root attacker can modify it;
- the first audit hashes every file and can take more than a minute on an SD card.

# System commands run

Every command is **fixed** (no user input is inserted) and **read-only**:

| Command | Check |
|---|---|
| `sudo -n -l` | www-data sudo rights |
| `dpkg-query -W unattended-upgrades` | automatic updates |
| `ss -H -lnt` | listening ports |
| `sudo -n nft -j list ruleset` | firewall |
| `sudo -n sshd -T` | effective SSH configuration |
| `systemctl is-active fail2ban` / `crowdsec` | detection |
| `sudo -n find … -exec sha256sum` on `sudoers`, crontabs, `/etc/systemd/system`, `authorized_keys` | system file integrity |
| `find /tmp /var/tmp /dev/shm -type f -perm -u+x` | temporary executables |

MariaDB queries: `SHOW VARIABLES` and `SHOW GRANTS` only. HTTP requests: only to Jeedom's own internal access.

The plugin only writes to its own folder (`plugins/secposture/data/`). **No data is sent outside.**

# Configuration

- **Maximum age of the last backup (h)**: default 48.
- **Allowed listening TCP ports**: default `22,80,443`. Add intentionally open services (MQTT 1883, etc.).
- **Web checks**: HTTP requests to Jeedom's internal access (served folders, headers). Disable if that access is not reachable from the box itself.
- **File integrity exclusions**: one pattern per line, compared with the path from the Jeedom root (e.g. `plugins/camera/captures/`).

The "Security posture" device is created at installation. The audit runs through Jeedom's daily cron.

---

*secposture — Documentation | Open-source plugin for Jeedom*
