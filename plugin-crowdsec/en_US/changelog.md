---
layout: default
title: CrowdSec — Changelog
lang: en_US
pluginId: crowdsec
---

# CrowdSec changelog

## Version under development

### 09/11/2026

- **New features and fixes**: new "CA certificate (PEM file)" option for an https LAPI with a self-signed certificate; the community blocklist is no longer downloaded at every read (once per hour), which lightens the load on the box and the LAPI; "Local detections (24 h)" is no longer capped; "Locally blocked IPs" now only holds addresses (no more ranges or countries); entering `off` in Auto-refresh disables automatic reading; clearer error messages (bouncer key or machine account at fault, explanation given by CrowdSec); an error is shown if resetting the widgets fails; translations updated.
- **Local installation**: dedicated `jeedom-local` bouncer key and machine account (those named `jeedom` are no longer touched); the listen address of an already installed CrowdSec is respected; an existing local configuration is backed up before being replaced; fix for a reinstallation after an incomplete uninstallation; the CrowdSec upgrade warning is shown whatever the box language.
- **Security and reliability**: banning the loopback or an address of the Jeedom box is refused, so as not to lock yourself out; maximum duration of a LAPI request reduced to 15 s; the widget pack is only installed if it exactly matches the expected version.

### 08/11/2026

- **Security and reliability**: the widget pack is only downloaded if it changed; widgets already installed are kept if the download fails.

### 24/10/2026

- **New features and fixes**: new commands "Top scenarios", "Local detections (24 h)", "Last local detection" and "Last list update" (the last three require the machine account); redesigned Commands tab (compact mode / edit mode, raw information).
- **Monitoring**: message in the Jeedom message center when the LAPI is unreachable (3 reads in a row, with the service state for the local equipment) or when the community blocklist has not been updated for more than 6 h; one line per equipment in the Jeedom Health page.

### 22/10/2026

- **New features and fixes**: optional installation of CrowdSec on the box through the dependencies (official repository, LAPI port of your choice, optional firewall bouncer) with automatic creation of the "CrowdSec (box)" equipment; fix for ban / unban, which failed with CrowdSec 1.8.
- **Security and reliability**: the machine account password no longer appears in the installation log.

### 15/10/2026

- **New features and fixes**: consistent display of the commands on the dashboard (iso widgets).

### 14/10/2026

- **New features and fixes**: new "LAPI reachable" command; the list of locally blocked IPs is limited to the 50 most recent; secret fields with a show / hide button.
- **Security and reliability**: an invalid LAPI URL, cron expression or ban duration is refused on save; an invalid configuration no longer blocks the plugin update.

### 28/09/2026

- **New features and fixes**: first version; reading of the active decisions through the LAPI (bouncer key); ban / unban an IP (machine account, configurable duration); connection test button; English translation.
