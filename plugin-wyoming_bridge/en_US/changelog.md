---
layout: default
title: Wyoming Bridge — Changelog
lang: en_US
pluginId: wyoming_bridge
---

# Wyoming Bridge changelog

## 2026-11-10

- Core heartbeat: a running daemon is now seen as communicating (otherwise, with a heartbeat set, the core would have reported it silent).
- Tracking commands: last sentence, last reply, handled or not, duration, last satellite (updated after the reply is sent).
- Documentation and changelog.

## 2026-11-09

- Plugin page in the Jeedom format: Configuration, Test, Health, equipment and commands.
- **Test** window: sends a sentence over the Wyoming protocol, like a satellite.
- **Health** window: overall state, installed versions and latest available versions.
- Default values shown in the configuration (port, bind, timeout, failure message).
- Rate limit and history per satellite.
- Service version announced from the plugin.
- Timeout passed to AI Assistant: no action is started after a "not handled" reply.
- Failure message spoken to the satellite instead of silence; announced language = Jeedom language.
- Security: Allowed IPs mandatory outside `127.0.0.1`, token kept off the command line, bounded network reads, daemon stop limited to its own processes.
- Daemon autostart handled by the core; installation libraries embedded.

## 2026-10-13

- `handle.php` answers 401 when the plugin is disabled.

## 2026-09-20

- Token encrypted in the database, AGPL-3.0 license.

## 2026-09-08

- First version.
