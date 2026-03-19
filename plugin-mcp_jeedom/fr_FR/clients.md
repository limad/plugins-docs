---
layout: default
title: Connexion des clients IA
lang: fr_FR
pluginId: mcp_jeedom
---

# Connexion des clients IA

Ce guide explique comment connecter les principaux clients IA au serveur **mcp_jeedom** installé sur votre serveur Jeedom (Debian/Linux).

> ℹ️ **Prérequis** — Le daemon mcp_jeedom doit être démarré et son statut doit afficher **✓ Actif** dans la page de configuration du plugin.

## Architecture de connexion

Deux modes de connexion sont disponibles selon la situation réseau du client IA.

### Accès local (réseau LAN)

Connexion directe sans authentification. Le client IA contacte le daemon sur son port (défaut : 8765).

```
Client IA (poste local)  →  http://IP_JEEDOM:8765/mcp  →  Daemon mcp_jeedom  →  API Jeedom
```

### Accès externe (Internet)

Connexion via le proxy PHP intégré avec authentification Bearer. Nécessite un token généré dans la page de configuration.

```
Client IA (n'importe où)  →  HTTPS  →  jeemcp_proxy.php  →  Daemon mcp_jeedom  →  API Jeedom
```

---

# Prérequis communs

## Installer `uv` / `uvx`

La plupart des clients IA utilisent `uvx` (fourni par `uv`) pour lancer `mcp-remote` à la demande.

**macOS**

```bash
brew install uv
# ou sans Homebrew :
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Windows (PowerShell)**

```powershell
winget install astral-sh.uv -e
```

**Linux**

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc   # ou source ~/.zshrc
```

> ℹ️ Après installation, **redémarrez votre terminal** (ou le client IA) pour que `uvx` soit disponible dans le PATH.

## Valeurs à remplacer

Dans tous les exemples ci-dessous :

| Placeholder | Valeur réelle |
|---|---|
| `IP_JEEDOM` | IP locale de votre serveur Jeedom (ex : `192.168.1.79`) |
| `PORT` | Port du daemon (défaut : `8765`) |
| `VOTRE_TOKEN` | Token généré dans *Plugins → mcp_jeedom → Token d'accès externe* |
| `VOTRE_URL_EXTERNE` | URL publique de votre Jeedom (ex : `https://jeedom.exemple.fr`) |

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude Desktop

**Transport recommandé :** Streamable HTTP via `mcp-remote`

Éditez le fichier de configuration Claude Desktop :

| OS | Chemin |
|---|---|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

**Accès local LAN**

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://IP_JEEDOM:PORT/mcp"
      ]
    }
  }
}
```

**Accès externe (Internet)**

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--header", "Authorization: Bearer VOTRE_TOKEN"
      ]
    }
  }
}
```

Après modification, **quitter et relancer complètement** Claude Desktop.

**Vérification :** Icône 🔧 en bas à gauche → `jeedom_mcp` doit apparaître dans la liste.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude Code

**Accès local**

```bash
claude mcp add jeedom_mcp \
  --transport http \
  "http://IP_JEEDOM:PORT/mcp"
```

**Accès externe**

```bash
claude mcp add jeedom_mcp \
  --transport http \
  --header "Authorization: Bearer VOTRE_TOKEN" \
  "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php"
```

Vérification :

```bash
claude mcp list
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Claude.ai (web)

**Transport :** HTTP uniquement — nécessite une URL accessible depuis Internet.

1. Aller dans **claude.ai → Paramètres → Intégrations**
2. Cliquer **Ajouter un serveur MCP**
3. Renseigner :
   - **URL :** `https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php`
   - **En-tête :** `Authorization: Bearer VOTRE_TOKEN`

> ⚠️ claude.ai ne peut pas atteindre un serveur sur votre réseau local. Utilisez Cloudflare Tunnel ou un reverse proxy si vous n'avez pas d'URL publique.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/cursor.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Cursor

**Via l'interface**

1. **Cursor → Settings → Cursor Settings → MCP**
2. Cliquer **+ Add new MCP server**

**Via `.cursor/mcp.json`** (à la racine du projet)

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://IP_JEEDOM:PORT/mcp"
      ]
    }
  }
}
```

**Accès externe**

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--header", "Authorization: Bearer VOTRE_TOKEN"
      ]
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/vscode.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">VS Code / GitHub Copilot

**Via `.vscode/mcp.json`** (workspace)

```json
{
  "servers": {
    "jeedom_mcp": {
      "type": "http",
      "url": "http://IP_JEEDOM:PORT/mcp"
    }
  }
}
```

**Accès externe avec token**

```json
{
  "servers": {
    "jeedom_mcp": {
      "type": "http",
      "url": "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
      "headers": {
        "Authorization": "Bearer VOTRE_TOKEN"
      }
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/windsurf.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Windsurf

**Fichier :** `~/.codeium/windsurf/mcp_config.json`

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://IP_JEEDOM:PORT/mcp"
      ]
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/cline.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Cline

1. Ouvrir la vue Cline dans VS Code
2. **MCP Servers → Configure MCP Servers**
3. Ajouter dans `cline_mcp_settings.json` :

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://IP_JEEDOM:PORT/mcp"
      ],
      "disabled": false
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/continue.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Continue

**Fichier :** `~/.continue/config.yaml`

**Accès local**

```yaml
mcpServers:
  - name: jeedom_mcp
    transport:
      type: http
      url: http://IP_JEEDOM:PORT/mcp
```

**Accès externe**

```yaml
mcpServers:
  - name: jeedom_mcp
    transport:
      type: http
      url: https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php
      headers:
        Authorization: "Bearer VOTRE_TOKEN"
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/openai.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">ChatGPT

**Transport :** HTTP uniquement — nécessite une URL accessible depuis Internet (même contrainte que Claude.ai).

1. **ChatGPT → Paramètres → Connecteurs**
2. Ajouter un connecteur MCP :
   - **URL :** `https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php`
   - **En-tête :** `Authorization: Bearer VOTRE_TOKEN`

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/gemini.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Gemini CLI

**Accès local**

```bash
gemini mcp add --name jeedom_mcp \
  --type http \
  --url "http://IP_JEEDOM:PORT/mcp"
```

**Accès externe**

```bash
gemini mcp add --name jeedom_mcp \
  --type http \
  --url "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php" \
  --header "Authorization: Bearer VOTRE_TOKEN"
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/open-webui.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Open WebUI

**Transport :** HTTP uniquement.

1. **Open WebUI → Paramètres → Connexions**
2. Ajouter un serveur MCP :
   - **URL :** `http://IP_JEEDOM:PORT/mcp`
   - **Type :** Streamable HTTP

> ℹ️ Si Open WebUI tourne sur un hôte différent de Jeedom, utiliser l'IP réseau de Jeedom. Sur le même hôte : `http://localhost:PORT/mcp`.

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/jetbrains.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">JetBrains IDEs

**Via le plugin AI Assistant :**

1. **Settings → Tools → AI Assistant → Model Context Protocol (MCP)**
2. Cliquer **+** → **As stdio process**
3. Configurer :
   - **Command :** `uvx`
   - **Arguments :** `mcp-remote@latest http://IP_JEEDOM:PORT/mcp`

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/zed.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Zed

**Fichier :** `~/.config/zed/settings.json`

```json
{
  "context_servers": {
    "jeedom_mcp": {
      "command": {
        "path": "uvx",
        "args": [
          "mcp-remote@latest",
          "http://IP_JEEDOM:PORT/mcp"
        ]
      }
    }
  }
}
```

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/raycast.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Raycast

**Nécessite :** Raycast Pro.

1. **Raycast → Settings → Extensions → MCP Servers**
2. Cliquer **+** → **HTTP Server**
3. Renseigner :
   - **Name :** `jeedom_mcp`
   - **URL :** `http://IP_JEEDOM:PORT/mcp`

---

# <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/codex.svg" width="28" style="vertical-align:middle;margin-right:8px;border-radius:6px;">Codex (OpenAI CLI)

**Fichier :** `~/.codex/config.toml`

> ℹ️ Codex utilise le format **TOML** (et non YAML). La syntaxe native avec `url` est la seule méthode fiable, en particulier sous Windows où le PATH peut ne pas être disponible dans le contexte sandboxé de Codex.

**Accès local LAN**

```toml
[mcp_servers.jeedom_mcp]
url = "http://IP_JEEDOM:PORT/mcp"
```

**Accès externe (Internet)**

```toml
[mcp_servers.jeedom_mcp]
url = "https://VOTRE_URL_EXTERNE/plugins/mcp_jeedom/core/php/jeemcp_proxy.php"
headers = { Authorization = "Bearer VOTRE_TOKEN" }
```

> ⚠️ **Ne pas utiliser** `command` + `args` avec `uvx mcp-proxy` sous Windows : le sandbox élévé de Codex ne résout pas le PATH utilisateur, ce qui provoque une erreur silencieuse. La clé `url` contourne ce problème en se connectant directement au daemon via HTTP.

---

# Tableau récapitulatif

| Client | LAN | Externe | Config |
|---|:---:|:---:|---|
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude Desktop | ✅ | ✅ | `claude_desktop_config.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude Code | ✅ | ✅ | `claude mcp add` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/anthropic.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Claude.ai (web) | ❌ | ✅ | Interface web |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/cursor.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Cursor | ✅ | ✅ | `.cursor/mcp.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/vscode.svg" width="18" style="vertical-align:middle;border-radius:4px;"> VS Code / Copilot | ✅ | ✅ | `.vscode/mcp.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/windsurf.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Windsurf | ✅ | ✅ | `mcp_config.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/cline.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Cline | ✅ | ✅ | `cline_mcp_settings.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/continue.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Continue | ✅ | ✅ | `~/.continue/config.yaml` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/openai.svg" width="18" style="vertical-align:middle;border-radius:4px;"> ChatGPT | ❌ | ✅ | Interface web |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/gemini.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Gemini CLI | ✅ | ✅ | `gemini mcp add` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/open-webui.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Open WebUI | ✅ | ✅ | Interface web |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/jetbrains.svg" width="18" style="vertical-align:middle;border-radius:4px;"> JetBrains IDEs | ✅ | ✅ | Settings AI Assistant |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/zed.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Zed | ✅ | ✅ | `~/.config/zed/settings.json` |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/raycast.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Raycast | ✅ | ✅ | Interface Raycast |
| <img src="{{site.baseurl}}/plugin-mcp_jeedom/images/clients/codex.svg" width="18" style="vertical-align:middle;border-radius:4px;"> Codex | ✅ | ✅ | `~/.codex/config.toml` |

> ℹ️ **LAN ❌** signifie que le client tourne sur les serveurs du fournisseur et ne peut pas atteindre votre réseau local. Une URL publique est obligatoire.

---

# Dépannage

## `uvx` introuvable après installation

```bash
source ~/.bashrc      # bash
source ~/.zshrc       # zsh
~/.local/bin/uvx --version   # vérifier le chemin complet
```

## Tester le serveur directement

```bash
curl -s -X POST \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' \
  http://IP_JEEDOM:PORT/mcp
```

Une réponse JSON contenant `"protocolVersion"` confirme que le serveur répond correctement.

## Le serveur n'apparaît pas dans le client

1. Vérifier que le daemon est **Actif** dans la page de configuration Jeedom
2. Vérifier que le port n'est pas bloqué par un firewall côté Jeedom
3. Vérifier la syntaxe JSON du fichier de configuration (pas de virgule finale, guillemets corrects)
4. **Redémarrer complètement** le client IA (ne pas juste fermer la fenêtre)

## Erreur SSL (certificat auto-signé)

Ajouter `--allow-insecure` à `mcp-remote`, ou utiliser Cloudflare Tunnel qui gère automatiquement le TLS.

## Token refusé (401 / 403)

1. Vérifier que le token est copié sans espace ni retour à la ligne
2. Ne pas encadrer le token de guillemets dans les variables d'environnement
3. Régénérer le token depuis *Plugins → mcp_jeedom → Token d'accès externe* si nécessaire

## Claude ne voit pas les outils

Demander à Claude : **« Liste tes outils disponibles »** — si `jeedom_mcp` est connecté, Claude listera `get_full_state`, `find_command`, etc.

Dans Claude Desktop : **Paramètres → Développeur → Serveurs MCP locaux** pour voir les erreurs éventuelles.

---

*mcp_jeedom — Documentation | Plugin open-source pour Jeedom*
