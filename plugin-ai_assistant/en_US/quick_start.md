---
layout: default
title: ai_assistant — Quick start
lang: en_US
pluginId: ai_assistant
---

# 🚀 Quick start

The plugin has many options, but **you need none of them to get started**. This page takes you, in two steps, from a freshly installed plugin to an assistant that controls your home. The rest can wait.

---

## Understand it in 30 seconds

Three pieces work together:

| Piece | Its role | Required? |
|---|---|---|
| **An AI provider** (Mistral, Gemini, OpenAI, Claude, Ollama…) | The "brain" that understands your sentences and writes the answers | Yes |
| **Ai Assistant** (this plugin) | The link between you, the brain and Jeedom: panel, mini-chat, mobile app | Yes |
| **MCP AI Server** (`mcp_jeedom` plugin) | The "hands": it gives the AI the tools to read your sensors and control your devices | Recommended |

Without **MCP AI Server**, the AI can chat but cannot see your home.

---

## Step 1: chat with the AI (5 minutes)

### 1. Get an API key

An API key is a password that lets the plugin use the AI service. Create an account with a provider and generate a key:

- **Gemini** ("Free" badge) or **Mistral** offer a free tier, limited in volume, enough for testing.
- **Ollama** runs on your own machine and needs no key (but a powerful enough computer).

Under the key field, the guided creation shows a link to the page where you can create it. If you already entered the key in the plugin configuration, leave the field empty: it is reused.

### 2. Run the guided creation

In **Plugins → Communication → Ai Assistant**, click **Guided creation** (the magic wand icon at the top of the page). It opens by itself if you have no device yet. It asks three questions:

1. **AI provider**: click its logo and paste your API key.
2. **Configuration**: give a name, then pick the type:
   - **Assistant Jeedom** (recommended): it knows your rooms and devices, and only touches the ones you allow;
   - **General assistant**: free chat, with no access to the home.

   With "Assistant Jeedom", it tells you whether **MCP AI Server** is installed, and if not, recommends installing it (see step 2). Leave **Model** empty: the plugin uses the provider's default model.
3. **Creation**: check the summary and click **Create assistant**.

It immediately tests the connection to the provider: ✅ the key works, ❌ it is rejected (fix it on the provider's page).

With "Assistant Jeedom", it creates **two devices**: the provider (which holds the key) and the assistant linked to it. This is expected.

### 3. Ask a first question

Click **Try it now** at the end of the guided creation: the panel opens on your new assistant. After that, two ways to chat:

- **The panel**: menu **Home → Ai Assistant**. This is the full interface (history, attachments, microphone).
- **The mini-chat**: an icon in the Jeedom top bar, for a quick question. Enable it in **Plugin configuration → Navbar → Enable**, then reload the page.

Ask for example: *"Hello, what can you do?"*. If the AI answers, step 1 is done.

---

## Step 2: control the home (10 minutes)

### 1. Install MCP AI Server

Install the **MCP AI Server** plugin (`mcp_jeedom`) from the Market, enable it, install its dependencies and check that its daemon is **OK**.

### 2. Choose what the AI may touch

In **MCP AI Server**, open the **whitelist** and tick the devices the AI may see and control. **Nothing is ticked by default**: an AI with no ticked device can do nothing, on purpose.

Start small: two or three lights and a temperature sensor are enough to test.

### 3. Link the two plugins

In most cases there is nothing to do: as soon as MCP AI Server is active, Ai Assistant creates by itself the **"MCP Jeedom (local)"** server and an **"MCP Jeedom"** device (MCP client) linked to your provider. The local access token is handled automatically.

If the "MCP Jeedom" device does not show up (for example, MCP AI Server was installed afterwards):

- open your **provider** device and **save** it: creation runs again;
- or click **Create a linked MCP Client** on the provider's page.

### 4. Test

In the panel, pick the "MCP Jeedom" device (or your "Assistant Jeedom") and ask:

- *"What is the living room temperature?"*
- *"Turn on the kitchen light."*

If the AI says it cannot find the device, first check that it is ticked in the whitelist.

---

## Not working?

| Symptom | Lead |
|---|---|
| Error 401 / "invalid key" | API key badly copied (extra space) or disabled at the provider. |
| Error 429 / quota | The free tier is used up for the period: wait or switch model. |
| The AI knows no device | Empty whitelist in MCP AI Server. |
| No "MCP Jeedom" device | MCP AI Server daemon stopped, or save the provider device. |
| No icon in the Jeedom bar | Navbar not enabled in the plugin configuration, or page not reloaded. |
| Microphone does not work | HTTPS is required: see the [microphone guide](../fr_FR/tuto_micro.md) (French). |

To understand an error, open the **ai_assistant** log (Analysis → Logs) and attach it to your question on the [forum](https://community.jeedom.com/tag/plugin-ai_assistant).

---

## What next?

Once step 2 works, explore at your own pace:

- **Mobile app (PWA)**: the panel on your phone, with notifications.
- **Vigie**: an agent that watches the home and warns you (low batteries, silent devices…).
- **Quick commands**: "turn on the living room light" run without calling the AI (free and instant).
- **Conversation mode**: talk to the assistant and hear it answer.

Everything is detailed in the [full documentation](index.md).
