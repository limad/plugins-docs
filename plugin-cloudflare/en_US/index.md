---
layout: default
title: Cloudflare Tunnel — Documentation
lang: en_US
pluginId: cloudflare
---

# Cloudflare Tunnel Plugin (`cloudflare`)

The **Cloudflare Tunnel** plugin provides secure remote access to your Jeedom server without opening any inbound ports on your router/firewall and without exposing your public IP address.

It leverages **Cloudflare Tunnel** (`cloudflared`) and Cloudflare's v4 REST API to establish an encrypted outbound tunnel between Jeedom and Cloudflare's global network edge.

---

## 1. How it works

1. A dedicated local daemon (`cloudflared`) runs as a systemd service (`cloudflared-jeedom.service`) on your Jeedom box.
2. The daemon maintains secure outbound connections to Cloudflare Edge servers.
3. The plugin automatically configures the ingress rule routing traffic from your chosen public hostname (e.g. `jeedom.mydomain.com`) to your local Jeedom instance.
4. A DNS CNAME record pointing to the tunnel is added to your Cloudflare zone.

Direct local access (LAN IP) remains unaffected and unchanged.

---

## 2. Prerequisites

- A Cloudflare account with at least one active **zone** (a domain whose DNS is managed by Cloudflare).
- A custom Cloudflare **API Token** with the following 4 minimal scopes:

| Resource | Permission |
|---|---|
| Zone → Zone | Read |
| Zone → DNS | Edit |
| Account → Cloudflare Tunnel | Read |
| Account → Cloudflare Tunnel | Edit |

---

## 3. Configuration

1. Go to **Plugins → Plugin Management → Cloudflare Tunnel**.
2. Paste your API token in the **Cloudflare API Token** field and click **Validate and Save**.
3. The plugin immediately verifies the token and its scopes with the Cloudflare API before storing it.

---

## 4. Equipment Management

In **Plugins → Security → Cloudflare Tunnel**:
1. Open the single Cloudflare device.
2. In the **Tunnel & Daemon** tab:
   - Install `cloudflared`.
   - Select your zone.
   - Enter your tunnel name and public hostname (e.g. `jeedom.mydomain.com`).
   - Click **Create Tunnel**.
   - Start the daemon and use **End-to-end Test** to confirm DNS and HTTPS reachability.

---

## 5. Scope & Out-of-Scope Features

### Cloudflare Pages
Cloudflare Pages is a standalone static site hosting platform (similar to Vercel, Netlify, or GitHub Pages).
The Jeedom plugin does not provide any interface for deploying static websites, Git integrations, or Cloudflare Pages APIs.
Cloudflare Pages management is performed directly via the Cloudflare web dashboard, through a linked Git repository (GitHub/GitLab), or locally using Cloudflare's official CLI (`wrangler`).

---

## 6. Security

- No open ports required on your firewall/router.
- Strictly scoped sudoers rules for daemon management.
- Token and credentials never stored in plain text.
