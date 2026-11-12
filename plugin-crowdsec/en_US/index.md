---
layout: default
title: CrowdSec — Documentation
lang: en_US
pluginId: crowdsec
---

# Description

The plugin monitors a [CrowdSec](https://www.crowdsec.net/) instance through its local API (LAPI). It reports the number of active decisions, separating local detections from the community blocklist, as well as the list and the last locally blocked IP address. It can also **ban** and **unban** an IP address from Jeedom (scenarios, widget).

Reading uses a *bouncer* key, which can only read decisions. To ban and unban, and for the detection counters, a *machine* account is also needed.

CrowdSec can run on the Jeedom box itself (the plugin can install it) or on another machine on the network. One Jeedom equipment matches one LAPI, so several instances can be monitored.

# Prerequisites

| Component | Version |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | ≥ 12 |
| CrowdSec | a reachable instance (on the box or elsewhere) |

To only monitor a remote instance, the plugin has **no dependency**.

# Installing CrowdSec on the box (optional)

If CrowdSec already runs elsewhere and you only want to monitor it, go straight to the next section.

To install CrowdSec on the Jeedom box itself, in the plugin configuration (**Local installation (optional)** box):

1. Check **Install CrowdSec on this box**. Optionally:
   - **LAPI port**: 8080 by default (between 1024 and 65535). If it is already in use, the next free port is used. Ignored if CrowdSec is already installed;
   - **Install the firewall bouncer** (nftables): without it, CrowdSec detects and decides but blocks nothing on the box.
2. Save, then click **Relaunch** in the *Dependencies* box.

The installation adds the official CrowdSec repository (the Debian one is too old), installs the package, creates a bouncer key and a `jeedom-local` machine account, then creates the already configured **CrowdSec (box)** equipment. Progress is shown in the `crowdsec_update` log.

If CrowdSec is already installed, its configuration is kept: only the `jeedom-local` key and account are recreated (a bouncer or machine named `jeedom` created by hand is not touched). The log reports a possible CrowdSec upgrade to the official repository version.

**Docker**: this installation relies on `systemd` and `nftables`, usually absent from a container. In Docker, only monitoring a remote CrowdSec instance is usable.

Unchecking the option does not uninstall CrowdSec (`sudo apt remove crowdsec`).

# Preparing CrowdSec (manual or remote installation)

On the machine hosting CrowdSec, create a bouncer key:

```
sudo cscli bouncers add jeedom
```

Copy the displayed key: it cannot be shown again.

Optional, to ban / unban and get the detections in Jeedom, create a machine account:

```
sudo cscli machines add jeedom --password '<password>'
```

# Equipment configuration

Add an equipment from **Plugins > Monitoring > CrowdSec**, then fill in:

**LAPI connection**

- **LAPI URL**: `http://127.0.0.1:8080` if CrowdSec runs on the Jeedom box. If the LAPI is on another machine, it must listen on a reachable interface (`api.server.listen_uri` in `/etc/crowdsec/config.yaml`); prefer https in that case, as the key is sent with every request.
- **CA certificate (PEM file)**: optional, for an https LAPI whose certificate is self-signed or issued by an internal authority. Path of a PEM file readable by `www-data`. Empty: system authorities. TLS verification stays strict (host name included).
- **Bouncer key**: the key created above (stored encrypted).

**Ban / unban (optional)**

- **Machine account** / **Machine password**: the account created above (password stored encrypted). Leave empty for read-only use: the Ban / Unban commands then fail, and the detection commands stay empty.
- **Ban duration**: duration of the bans made from Jeedom, in CrowdSec format (`4h`, `30m`, `1h30m`...), `4h` by default.

**Specific settings**

- **Auto-refresh**: reading frequency (cron expression, helper available). Empty: every 10 minutes. `off`: no automatic refresh (the Refresh command and the **Refresh now** button still work). The community blocklist (thousands of IPs) is only fully read once per hour; in between, the community counters and scenarios come from the last full read and only the local decisions are read again.

On save, an invalid URL, cron expression, ban duration or certificate is refused with an explicit message.

Equipment buttons:

- **Test the connection**: checks that the LAPI answers and accepts the bouncer key, then the machine account if one is set. It tests the **saved** configuration: save first.
- **Refresh now**: reads the decisions immediately.
- **Raw information**, **Commands edit mode**, **Default config** (reapplies the default widget to the equipment commands).

# Commands

| Command | Type | Description |
|---|---|---|
| Active decisions | numeric info | All active decisions |
| Local decisions | numeric info | Decisions from the machine itself (detections, `cscli`...) |
| Community decisions | numeric info | Decisions from the community blocklist (CAPI) and subscribed lists |
| Locally blocked IPs | string info | IPs of the local decisions, most recent first (50 max). Decisions on a range or a country are counted but not listed |
| Last locally blocked IP | string info | IP of the most recent local decision |
| LAPI reachable | binary info | 1 if the last LAPI read succeeded, 0 otherwise (the other values then stay frozen) |
| Top scenarios | string info | The 3 most frequent scenarios among the active decisions (e.g. `http:scan 7772, ssh:bruteforce 4357`) |
| Local detections (24 h) | numeric info | Alerts raised by the machine itself over the last 24 h (excluding community list updates, manual bans and simulated alerts). Requires the machine account |
| Last local detection | string info | Scenario, IP and date of the last local detection. Requires the machine account |
| Last list update | string info | Date (box time) of the last update of the community blocklist, normally about every 2 h. If it stops moving, the community protection is stale. Requires the machine account |
| Refresh | action | Reads the decisions immediately |
| Ban an IP | message action | Adds a local `ban` decision on the entered IP, for the configured duration (same as `cscli decisions add --ip`). Refused for the loopback and the Jeedom box's own addresses, so as not to lock yourself out |
| Unban an IP | message action | Deletes every active decision on the entered IP (same as `cscli decisions delete --ip`) |

Scenario example: `Ban an IP` action with the address to block in the message field (e.g. `203.0.113.10`).

# Monitoring

- **LAPI outage**: after 3 consecutive failed reads, a message is added to the Jeedom message center (once), with the cause. For the "CrowdSec (box)" equipment it tells whether the `crowdsec` service is stopped or the LAPI does not answer while the service runs. The message goes away when the LAPI answers again. The `LAPI reachable` command goes to 0 on the first failure.
- **Stale community blocklist**: a message is added if the last update is older than 6 h (it happens about every 2 h). Requires the machine account.
- Jeedom **Health page**: one line per enabled equipment with the LAPI state and the last error.

The plugin does not restart CrowdSec: run `sudo systemctl restart crowdsec` on the relevant machine.

# FAQ / troubleshooting

**"Bouncer key rejected by the LAPI (HTTP 403)"**
The key was deleted or badly copied. Create a new one (`cscli bouncers add`) and paste it into the equipment.

**"Machine account rejected by the LAPI (HTTP 403)"**
The machine account is not validated: `sudo cscli machines validate <name>`.

**"Machine account credentials rejected (HTTP 401)"**
Wrong account name or password.

**"LAPI unreachable"**
Check the URL and port, that the service runs (`systemctl status crowdsec`) and, for a remote LAPI, that it listens on an interface reachable from Jeedom.

**The "Last list update" date no longer moves**
CrowdSec no longer receives the community blocklist: check its connection to the central service with `sudo cscli capi status`.

**Banned IPs are not blocked on the box**
A decision blocks nothing without a remediation bouncer. On the box, check **Install the firewall bouncer** in the plugin configuration, then relaunch the dependencies.

**Where are the errors?**
`crowdsec` log (reads, bans) and `crowdsec_update` log (local installation).
