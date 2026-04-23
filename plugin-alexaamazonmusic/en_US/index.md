---
layout: default
title: Alexa Amazon Music
lang: en_US
pluginId: alexaamazonmusic
---

# Alexa Amazon Music

## Overview

The **Alexa Amazon Music** plugin lets you control Amazon Music playback on your Alexa devices directly from Jeedom. It relies on the **alexaapiv2** plugin (Alexa Premium) as a required dependency and does not require a daemon of its own.

With this plugin you can:

- Play, pause, stop, skip to the next or previous track
- Control the volume of each Alexa device
- Launch an Amazon Music playlist or a TuneIn radio station
- Play a specific track by name
- Transfer playback to another Alexa device (Cast)
- Toggle repeat and shuffle modes
- Adjust the equalizer (bass, mid, treble)
- Create multi-room groups (WHA — Whole Home Audio)

---

## Prerequisites

Before installing the Alexa Amazon Music plugin, make sure the following conditions are met:

1. The **alexaapiv2** plugin must be installed, enabled, and correctly configured on your Jeedom.
2. A **valid Alexa cookie** must be set in the alexaapiv2 plugin configuration.
3. The **alexaapiv2 daemon** must be running (status "OK" on the plugin page).
4. Your Alexa devices must be linked to the same Amazon account used for the cookie.

> **Note:** The alexaamazonmusic plugin has no daemon of its own. All communication with Amazon servers goes through alexaapiv2.

---

## Installation

1. From the **Jeedom Market**, search for "Alexa Amazon Music" and install the plugin.
2. Enable the plugin under **Plugins > Plugin management**.
3. Verify that the **alexaapiv2** plugin is active and that its daemon is running.

---

## General configuration

Go to **Plugins > Multimedia > Alexa Amazon Music**, then click the gear icon (Configuration).

| Parameter | Description |
|---|---|
| Playlist list | Inventory of your Amazon Music playlists, auto-refreshed approximately every 17 minutes. |
| Refresh playlists | Button to force an immediate update of the playlist list. |

---

## Devices

### Automatic scan

Devices are created automatically via the **scan** feature of the alexaapiv2 plugin. All compatible Alexa devices (Echo, Echo Dot, Echo Show, Fire TV…) are imported.

**WHA (Whole Home Audio)** groups are also supported and appear as separate devices, allowing audio to be broadcast across multiple devices simultaneously.

### Device parameters

| Parameter | Description |
|---|---|
| Name | Name of the device as it appears in Jeedom |
| Parent object | Jeedom object the device is attached to |
| Enabled | Enables or disables the device |
| Visible | Makes the device visible on the dashboard |

---

## Commands

### Action commands

| Command | Type | Description |
|---|---|---|
| `textCommand` | Action / Message | Sends any text command to Alexa (e.g. "play some jazz") |
| `volume` | Action / Slider (0–100) | Sets the device volume |
| `command` | Action / List | Media controls: `play`, `pause`, `stop`, `next`, `previous`, `forward`, `rewind`, `repeat` (on/off), `shuffle` (on/off) |
| `playlist` | Action / List | Plays an Amazon Music playlist selected from the scanned list |
| `playmusictrack` | Action / Message | Plays an Amazon Music track by name or ID |
| `radio` | Action / Message | Plays a TuneIn radio station by name |
| `castMediaSession` | Action / Message | Transfers the current playback session to another Alexa device |
| `seekPlayer` | Action / Slider (0–100) | Seeks within the current track (position as a percentage) |
| `eqBass` | Action / Slider (-6 to +6) | Adjusts the bass level on the equalizer |
| `eqMid` | Action / Slider (-6 to +6) | Adjusts the mid level on the equalizer |
| `eqTreble` | Action / Slider (-6 to +6) | Adjusts the treble level on the equalizer |

### Info commands

| Command | Type | Description |
|---|---|---|
| `volumeinfo` | Info / Numeric | Current device volume |
| `repeat` | Info / Binary | Repeat mode state (1 = on, 0 = off) |
| `shuffle` | Info / Binary | Shuffle mode state (1 = on, 0 = off) |
| `eqBassInfo` | Info / Numeric | Current bass value on the equalizer |
| `eqMidInfo` | Info / Numeric | Current mid value on the equalizer |
| `eqTrebleInfo` | Info / Numeric | Current treble value on the equalizer |
| `mediaLength` | Info / Numeric | Total duration of the current track (in seconds) |

---

## Usage examples

### Start a playlist in the morning

In a Jeedom scenario triggered at 7:00 AM:

1. Command `volume` → value `30`
2. Command `playlist` → select "My morning playlist"

### Pause on an alarm

Create a scenario triggered by your Jeedom alarm:

1. Command `command` → value `pause`

### Broadcast throughout the home (WHA)

1. Select the device corresponding to your WHA group.
2. Command `playlist` → choose the desired playlist.
3. Command `volume` → adjust the global volume.

### Play a specific track

Command `playmusictrack` → enter the track name, for example: `Bohemian Rhapsody Queen`.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Devices do not appear after the scan | Check that the alexaapiv2 daemon is active and the cookie is valid. |
| The playlist does not start | Manually refresh the playlist list from the plugin configuration. |
| Volume does not update | Wait for the next refresh cycle or check the Alexa device's network connection. |
| "Expired cookie" error | Renew the cookie in the alexaapiv2 plugin configuration. |
| Equalizer not available | Some Alexa device models do not support the equalizer (e.g. Echo Dot 2nd generation). |

---

> To report a bug or request a feature, visit the plugin page on the Jeedom Market.
