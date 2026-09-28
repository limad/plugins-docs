---
layout: default
title: f2ban — fail2ban plugin for Jeedom
lang: en_US
pluginId: f2ban
---

# Description

Plugin to monitor fail2ban. It retrieves all the live information of a local or remote (over SSH) fail2ban instance, and also keeps daily counters of banned IPs and a counter per country of origin of the IP address (country obtained by geolocating the IP address).

It can also ban and unban an IP address.

> **Important**
>
> This plugin does not install or configure fail2ban on the system.

# Supported versions

| Component | Version                     |
|-----------|-----------------------------|
| Debian    | Bullseye(11) & Bookworm(12) |
| Jeedom    | >= 4.5                      |

# Installation

To use the plugin, download, install and enable it like any Jeedom plugin.

# Plugin configuration

A single option is available: **Disable the geolocation of banned IPs**. By default, each newly banned public IP is sent to [ip-api.com](https://ip-api.com) (third-party service, queried over unencrypted HTTP because the free plan does not offer HTTPS) to get its country and feed the per-country counters. If you prefer that no IP address leaves your installation, tick this option: the per-country counters will then no longer be updated.

The plugin uses the Jeedom cron to refresh the devices (see device configuration) and cronDaily to reset the daily counters.

# Devices

Each device of the plugin corresponds to a fail2ban instance on a machine. Start by adding a device and giving it a name.

In the device configuration, you will find the usual settings common to all Jeedom devices and, below, the settings specific to this plugin:
![params](../images/params.png)

First choose the mode: *local* or *SSH*. *Local* mode reads the information of fail2ban installed on the Jeedom machine, while *SSH* mode connects to a remote machine over SSH. In that case, enter the host name (or IP address), the port (if different from 22), the user name (which must be in the sudoers group) and its password.

## SSH authentication

Two authentication methods are available in *SSH* mode:

- **Password**: simple but less secure.
- **Private key** (recommended): paste the content of the private key (OpenSSH/PEM formats: RSA, ECDSA, Ed25519) and, if it has one, its passphrase. The matching public key must be in the user's `~/.ssh/authorized_keys` on the remote machine.

In both cases, the user must be able to run `fail2ban-client` with `sudo` without a password (`NOPASSWD`), or be *root*. The password, private key and passphrase are stored encrypted.

## Test the connection

The **Test the connection** button (after saving the device) checks that fail2ban answers, locally or over SSH, and shows the number of jails found. On failure, the message gives the likely cause (fail2ban missing, `sudo` asking for a password, authentication refused...). The **Refresh now** button immediately re-reads all jails and creates the commands of jails added since.

# The plugin page

On the plugin page, devices are grouped in collapsible panels by mode (*Local*, *SSH*). In table view, each card shows the host, the number of jails and the number of currently banned IPs.

The **Management** bar notably offers:

- **Health**: a window showing, for each device, the state of each jail (failures, bans, last banned IP) and the per-country counters. Values update live. From this window you can refresh a jail, ban an IP address (orange button) and unban an IP by clicking the padlock on its badge.
- **Assistance**: opens a pre-filled help request on the forum, with a diagnostic of the plugin.

In a device's **Commands** tab, the **Edit mode** button (top right) switches between a compact view and a detailed view where you can adjust the unit, min/max, visibility and historization. The command type is shown by the colour of the row border: blue for an info, orange for an action.

The orange **Default config** button (in the device toolbar) recreates missing commands, for example after an accidental deletion, even without a connection to the target. It also reapplies the iso widget and the default generic type to existing commands. The widget is only replaced if it is the default widget, the old *line* widget or a widget that no longer exists (uninstalled plugin, for example): another existing widget you chose is kept. The generic type is only filled in if it is empty.

In *SSH* mode, the plugin records the server key fingerprint on the first connection, then checks it on every connection. If it changes (reinstalled server, or a server impersonating the target), the connection is refused before the password or private key is sent. After a legitimate reinstallation, the *Reset* button next to the fingerprint lets it learn it again. Changing the host or port also resets it.

You can also set how often data is refreshed, every 10 min by default. On each refresh, the plugin also looks for new jails and creates their commands. If a jail no longer exists on the target, its commands are hidden (history is kept), it is no longer refreshed and a message is added to the message center; if the jail reappears, its commands become visible again.

# Commands

After saving the device, if the configuration is correct and the device is enabled, the plugin retrieves the list of configured *jails* and creates the following commands for each one:

- **Refresh** action command to refresh the matching counters
- **Banip** action/message command to ban the IP given in the message
- **Unbanip** action/message command to unban the IP given in the message
- **Current failures** info giving the current number of failed attempts
- **Total failures** info giving the total number of failed attempts
- **Currently banned** info giving the number of currently banned IPs
- **Total banned** info giving the total number of banned IPs
- **Last banned IP** info giving the last banned IP
- **List of banned IPs** info giving the list of currently banned IPs
- **List of IPs banned today** info giving the list of IPs banned during the day

All commands use by default a widget from the iso widget set (installed automatically with the plugin; without it, Jeedom shows the default widget): *iso_line* for infos (counters, IP lists, last IP, **Online**), *iso_btn* for **Refresh**, **Banip** and **Unbanip**. The names of the created commands follow the Jeedom language. The **Banip** command is linked to the **Last banned IP** info of the same jail and **Unbanip** to the **List of banned IPs** info (linked info command); **Refresh** is not linked to any info, since it updates all counters at once. In the **Commands** tab, the *State* column shows this linked info for actions; in *Edit mode*, a list lets you change or remove it.

Each device also has a binary info command **Online**: it is 1 when fail2ban answered during the last refresh and 0 otherwise (target unreachable, authentication refused, SSH key changed, sudo refused...). It can trigger an alert in a scenario.

In addition to these commands, on each refresh, if a new IP is banned, the plugin geolocates the IP address and creates a new command per country of origin containing the number of distinct visits (per IP address) (public IP addresses only).

# Changelog

[See the changelog](./changelog)

# Support

If you have a problem, start by reading the latest topics related to the plugin on [community]({{site.forum}}/tag/plugin-{{page.pluginId}}).

If you still cannot find an answer, feel free to create a new topic, without forgetting the plugin tag ([plugin-{{page.pluginId}}]({{site.forum}}/tag/plugin-{{page.pluginId}})).

Please provide at least:

- a screenshot of the Jeedom health page
- a screenshot of the plugin configuration page
- all available plugin logs, at *INFO* level, pasted in a `Preformatted text` block (`</>` button on community), no files!
- depending on the case, a screenshot of the error, a screenshot of the problematic configuration...

---

*f2ban — Documentation | Open-source plugin for Jeedom*
