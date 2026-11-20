---
layout: default
title: Connexion des clients IA
lang: fr_FR
pluginId: mcp_jeedom
---

# Connexion des clients IA

Ce guide explique comment connecter les clients IA au plugin **mcp_jeedom**.

> 💡 **Le plus simple** : le bouton **Configurer un client IA** de la page du plugin génère la configuration exacte de chaque client, avec votre URL. Ce guide en reprend les modèles.

## Principe

Tout client se connecte au **proxy du plugin**, avec un **token** ou par **OAuth**. Le daemon n'écoute que sur la box et refuse les connexions directes. Le rôle du token fixe ce que le client peut faire : voir [Tokens et rôles](index.md#tokens-et-rôles).

| Accès | URL du proxy |
|---|---|
| Réseau local | `http://IP_JEEDOM/plugins/mcp_jeedom/core/php/jeemcp_proxy.php` |
| Internet | `https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php` |

Les clients qui tournent dans le cloud (claude.ai, ChatGPT, Le Chat) ne peuvent pas joindre votre réseau local : il leur faut l'URL Internet, en HTTPS.

## Valeurs à remplacer

| Dans les exemples | Remplacer par |
|---|---|
| `URL_DU_PROXY` | L'une des deux URL ci-dessus |
| `VOTRE_TOKEN` | Un token créé dans **Gestion des tokens** (page de configuration du plugin) |

## Token ou OAuth ?

- **Token** : le client envoie l'en-tête `Authorization: Bearer VOTRE_TOKEN`. Valable en local comme sur Internet.
- **OAuth** : le client s'enregistre seul, vous vous connectez à Jeedom et choisissez le token dont il reprend le rôle. À activer dans l'onglet **OAuth MCP** de la configuration du plugin. Obligatoire pour claude.ai et Claude Desktop (connecteurs), recommandé pour ChatGPT.

## Pont `uvx` (clients sans HTTP)

Quelques cas passent par un pont local qui convertit stdio en HTTP : `mcp-proxy`, lancé par `uvx`. Installez `uv` une fois :

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```powershell
# Windows (PowerShell)
winget install astral-sh.uv -e
```

Redémarrez ensuite le terminal ou le client pour qu'il trouve `uvx`. L'option `--with "mcp<2.0.0"` des exemples est nécessaire : la dernière version du SDK MCP casse `mcp-proxy` au démarrage.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude Desktop

**Sur Internet : connecteur personnalisé (OAuth)**

1. Plugin → onglet **OAuth MCP** : cochez **Activer OAuth** et sauvegardez.
2. Claude Desktop → **Paramètres → Connecteurs → Ajouter un connecteur personnalisé**.
3. Nom : `Jeedom`, URL : l'URL Internet du proxy (HTTPS).
4. Connectez-vous à Jeedom, choisissez le token dont le client reprend le rôle, validez.

Le connecteur est contacté depuis les serveurs d'Anthropic : il ne fonctionne pas avec une URL locale.

**En local, ou sans OAuth : pont `uvx`** dans `claude_desktop_config.json` (**Paramètres → Développeur → Modifier la configuration**) :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "command": "uvx",
      "args": [
        "--with", "mcp<2.0.0",
        "mcp-proxy", "--transport", "streamablehttp",
        "URL_DU_PROXY",
        "--headers", "Authorization", "Bearer VOTRE_TOKEN"
      ]
    }
  }
}
```

| OS | Fichier |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

Quittez et relancez complètement Claude Desktop. Le serveur apparaît dans **Paramètres → Développeur**.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude Code

```bash
claude mcp add --transport http jeedom-mcp "URL_DU_PROXY" \
  --header "Authorization: Bearer VOTRE_TOKEN"
```

Ajoutez `--scope user` pour l'avoir dans tous vos projets. Vérification : `claude mcp list`.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude.ai (web)

Uniquement par OAuth, avec l'URL Internet en HTTPS.

1. Plugin → onglet **OAuth MCP** : cochez **Activer OAuth** et sauvegardez.
2. claude.ai → **Paramètres → Connecteurs → Ajouter un connecteur personnalisé** (Team/Enterprise : un propriétaire l'ajoute dans les paramètres de l'organisation).
3. Nom : `Jeedom`, URL : l'URL Internet du proxy.
4. Connectez-vous à Jeedom, choisissez le token, validez.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/cursor.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Cursor

Fichier `~/.cursor/mcp.json` (tous les projets) ou `.cursor/mcp.json` (un projet) :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "url": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

Rechargez Cursor.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/vscode.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">VS Code / GitHub Copilot

Fichier `.vscode/mcp.json` du workspace :

```json
{
  "servers": {
    "jeedom-mcp": {
      "type": "http",
      "url": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

Démarrez le serveur depuis le fichier ou la vue **Extensions → MCP**.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/windsurf.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Windsurf (Devin Desktop)

Cascade → icône **MCP** → **View raw config** (ce bouton ouvre le bon fichier, dont l'emplacement a changé avec Devin Desktop) :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "serverUrl": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

Rafraîchissez la liste des serveurs MCP.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/cline.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Cline

Vue Cline → **MCP Servers → Configure MCP Servers**, puis dans `cline_mcp_settings.json` :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "type": "streamableHttp",
      "url": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

La clé `type` est obligatoire : sans elle, Cline tente une connexion SSE et échoue.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/continue.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Continue

Fichier `~/.continue/config.yaml` (ou un fichier de `.continue/mcpServers/`) :

```yaml
mcpServers:
  - name: jeedom-mcp
    type: streamable-http
    url: URL_DU_PROXY
    requestOptions:
      headers:
        Authorization: "Bearer VOTRE_TOKEN"
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/jetbrains.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">JetBrains IDEs

**Settings → Tools → AI Assistant → Model Context Protocol (MCP)** → **Add**, saisie JSON. La documentation JetBrains ne prévoit pas d'en-tête pour un serveur HTTP : passez par le pont `uvx`.

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "command": "uvx",
      "args": [
        "--with", "mcp<2.0.0",
        "mcp-proxy", "--transport", "streamablehttp",
        "URL_DU_PROXY",
        "--headers", "Authorization", "Bearer VOTRE_TOKEN"
      ]
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/zed.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Zed

Réglages de Zed (`zed: open settings`) :

```json
{
  "context_servers": {
    "jeedom-mcp": {
      "url": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/google.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Google Antigravity

Panneau agent → **…** → **MCP Servers → Manage MCP Servers → View raw config** (`~/.gemini/config/mcp_config.json`) :

```json
{
  "mcpServers": {
    "jeedom-mcp": {
      "serverUrl": "URL_DU_PROXY",
      "headers": { "Authorization": "Bearer VOTRE_TOKEN" }
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/openai.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">ChatGPT

Uniquement avec l'URL Internet en HTTPS. ChatGPT ne sait pas envoyer d'en-tête personnalisé : utilisez **OAuth**.

1. Plugin → onglet **OAuth MCP** : cochez **Activer OAuth** et sauvegardez.
2. ChatGPT → **Paramètres → Applications → Avancé → Mode développeur**, puis créez une application MCP (plans Business et Enterprise ; accès limité sur Pro).
3. URL : l'URL Internet du proxy, authentification **OAuth**.

Sans OAuth : URL `URL_DU_PROXY?token=VOTRE_TOKEN` et authentification **Aucune**. Le token figure alors dans l'URL : réservez-lui un token dédié, au rôle le plus bas possible.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/mistral.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Mistral Le Chat

Uniquement avec l'URL Internet en HTTPS.

1. Pour OAuth : plugin → onglet **OAuth MCP** → **Activer OAuth** (les adresses de rappel Mistral sont acceptées d'office).
2. Le Chat → **Connecteurs → Ajouter un connecteur → Custom MCP Connector** (réservé aux administrateurs de l'espace).
3. Nom et URL du proxy → **Connecter** : choisissez **OAuth 2.1** ou **HTTP Bearer Token** (avec votre token).
4. Onglet **Fonctions** du connecteur : autorisez les outils voulus, ou approuvez-les au premier appel.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/gemini.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Gemini CLI

```bash
gemini mcp add --scope user --transport http \
  --header "Authorization: Bearer VOTRE_TOKEN" \
  jeedom-mcp "URL_DU_PROXY"
```

Vérification : `gemini mcp list`.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/open-webui.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Open WebUI

Version 0.6.31 ou plus. **Réglages admin → Intégrations → External Tool Servers → +** :

- **Type** : MCP (Streamable HTTP)
- **URL** : `URL_DU_PROXY`
- **Auth** : Bearer, **Key** : `VOTRE_TOKEN` (sans le mot « Bearer »)

C'est le serveur Open WebUI qui contacte Jeedom : l'URL doit être joignable depuis lui.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/raycast.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Raycast

Commande **Install MCP Server**, transport **HTTP** :

- **Name** : `jeedom-mcp`
- **URL** : `URL_DU_PROXY`
- **HTTP Headers** : `Authorization` = `Bearer VOTRE_TOKEN`

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/codex.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Codex (OpenAI CLI)

Fichier `~/.codex/config.toml` :

```toml
[mcp_servers.jeedom-mcp]
url = "URL_DU_PROXY"
http_headers = { Authorization = "Bearer VOTRE_TOKEN" }
```

Sans token en clair dans le fichier :

```bash
codex mcp add jeedom-mcp --url "URL_DU_PROXY" --bearer-token-env-var JEEDOM_MCP_TOKEN
```

puis définissez la variable d'environnement `JEEDOM_MCP_TOKEN`. Vérification : `codex mcp list`.

---

# Récapitulatif

| Client | Réseau local | Internet |
|---|:---:|:---:|
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude Desktop | Pont `uvx` | Connecteur OAuth |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude Code | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude.ai (web) | ❌ | OAuth |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/cursor.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Cursor | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/vscode.svg" width="18" style="vertical-align:middle;border-radius:4px;"> VS Code / Copilot | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/windsurf.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Windsurf | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/cline.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Cline | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/continue.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Continue | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/jetbrains.svg" width="18" style="vertical-align:middle;border-radius:4px;"> JetBrains IDEs | Pont `uvx` | Pont `uvx` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/zed.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Zed | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/google.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Google Antigravity | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/openai.svg" width="18" style="vertical-align:middle;border-radius:4px;"> ChatGPT | ❌ | OAuth |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/mistral.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Mistral Le Chat | ❌ | OAuth ou token |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/gemini.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Gemini CLI | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/open-webui.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Open WebUI | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/raycast.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Raycast | ✅ | ✅ |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/codex.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Codex | ✅ | ✅ |

✅ = connexion directe avec le token. ❌ = client hébergé dans le cloud, qui ne peut pas joindre votre réseau local.

---

# Dépannage

## Vérifier que le proxy répond

Sans token, la sonde du proxy indique si le daemon tourne et si un token existe :

```bash
curl -s "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php?probe=1"
```

Puis un vrai appel MCP avec le token :

```bash
curl -s -X POST \
  -H "Authorization: Bearer VOTRE_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' \
  "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php"
```

Une réponse qui contient `"protocolVersion"` confirme que tout fonctionne.

## Le serveur n'apparaît pas dans le client

1. Vérifiez que le daemon est démarré (page de configuration du plugin).
2. Vérifiez la syntaxe du fichier de configuration (virgule en trop, guillemets).
3. Redémarrez complètement le client (pas seulement sa fenêtre).
4. Avec un pont `uvx` : vérifiez que `uvx --version` répond dans un terminal, puis redémarrez le client pour qu'il retrouve `uvx` dans le PATH.

## Token refusé (401 / 403)

- **401** : token absent ou invalide (inconnu, désactivé, révoqué, ou jeton OAuth expiré). Un client OAuth se réautorise de lui-même.
- **403** : aucun token n'existe dans le plugin et OAuth n'est pas activé.

Vérifiez que le token est copié sans espace ni retour à la ligne. Au besoin, régénérez-le dans **Gestion des tokens**.

## L'IA ne voit pas les outils

Demandez-lui : **« Liste tes outils disponibles »**. Elle doit citer `state`, `devices`, `execute`… Si la liste est courte, c'est normal : chaque client ne voit que les outils de son rôle (voir [Tokens et rôles](index.md#tokens-et-rôles)).
