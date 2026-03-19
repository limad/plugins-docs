---
layout: default
title: mcp_jeedom — Accès externe via Cloudflare Tunnel
lang: fr_FR
---

# Accès externe via Cloudflare Tunnel

> Guide d'installation complet · Cloudflared + mcp_jeedom + Claude Desktop  
> Version 1.1 — Mars 2026

---

## Vue d'ensemble

Ce tutoriel explique comment exposer le serveur MCP de votre Jeedom (port 8765) à internet de manière **sécurisée et chiffrée** via un tunnel Cloudflare, **sans ouvrir aucun port** sur votre box/routeur.

```
Claude Desktop  ──HTTPS──►  Cloudflare  ──►  Jeedom (cloudflared)  ──►  MCP Daemon :8765
  (votre PC)               Bearer token       192.168.1.79                (plugin)
```

> ℹ️ **Aucun port à ouvrir sur votre box/routeur.**  
> Cloudflare agit comme reverse proxy HTTPS chiffré.  
> Le tunnel part de votre Jeedom **vers** Cloudflare (connexion sortante), jamais l'inverse.

---

## Prérequis

| Élément | Valeur / Détails |
|---|---|
| Serveur Jeedom | ex : 192.168.1.79 |
| Port daemon MCP | 8765 *(par défaut)* |
| Accès SSH | Requis |
| Compte Cloudflare | Gratuit (plan Free) |
| OS Jeedom | Debian / Ubuntu (amd64, arm64 ou armhf) |
| Token MCP externe | Généré dans le plugin mcp_jeedom |

---

## Étape 1 — Créer un tunnel Cloudflare

### 1.1 Créer un compte gratuit

Rendez-vous sur **[dash.cloudflare.com/sign-up](https://dash.cloudflare.com/sign-up)** et créez un compte gratuit.  
Aucun domaine ni carte bancaire n'est requis pour utiliser les tunnels Cloudflare.

### 1.2 Créer le tunnel depuis le dashboard

1. Connectez-vous sur [dash.cloudflare.com](https://dash.cloudflare.com)
2. Menu gauche : **Zero Trust → Networks → Tunnels**
3. Cliquer **Create a tunnel** → choisir **Cloudflared**
4. Donner un nom au tunnel (ex : `jeedom-mcp`) → **Save tunnel**
5. La page suivante affiche la commande d'installation — **copiez le token affiché** (longue chaîne après `--token`) → utilisé à l'étape 3

### 1.3 Configurer le Public Hostname

Dans l'onglet **Public Hostnames** du tunnel :

| Champ | Valeur à saisir |
|---|---|
| Subdomain | `jeedom-mcp` *(ou le nom de votre choix)* |
| Domain | `votre-domaine.workers.dev` *(ou domaine custom)* |
| Service → Type | `HTTP` |
| Service → URL | `localhost:8765` |

> ℹ️ L'URL publique finale sera :  
> `https://jeedom-mcp.votre-domaine.workers.dev`  
> Cloudflare génère un sous-domaine `.workers.dev` gratuit si vous n'avez pas de domaine propre.

---

## Étape 2 — Installer cloudflared sur le serveur Jeedom

### 2.1 Se connecter en SSH

```bash
ssh utilisateur@192.168.1.79
```

### 2.2 Détecter l'architecture du processeur

```bash
dpkg --print-architecture
# amd64  → PC / VM
# arm64  → Raspberry Pi 4/5, Odroid
# armhf  → Raspberry Pi 3 ou moins
```

### 2.3 Télécharger et installer cloudflared

**Pour amd64 (PC / VM) :**

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
cloudflared --version
# cloudflared version 2026.x.x
```

**Pour arm64 (Raspberry Pi 4/5) :**

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb
sudo dpkg -i cloudflared-linux-arm64.deb
cloudflared --version
```

**Pour armhf (Raspberry Pi 3 ou moins) :**

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm.deb
sudo dpkg -i cloudflared-linux-arm.deb
cloudflared --version
```

---

## Étape 3 — Configurer le démarrage automatique (systemd)

### 3.1 Installer le service avec le token du tunnel

> ⚠️ Remplacez `VOTRE_TOKEN_CLOUDFLARE` par le token copié à l'étape 1.2.

```bash
sudo cloudflared service install VOTRE_TOKEN_CLOUDFLARE
```

### 3.2 Activer et démarrer le service

```bash
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
sudo systemctl status cloudflared

# Résultat attendu :
# ● cloudflared.service - cloudflared
#    Active: active (running)
#    Status: "Connected to Cloudflare"
```

### 3.3 Vérifier les logs de connexion

```bash
sudo journalctl -u cloudflared -n 50 --no-pager

# Bonne connexion = lignes contenant :
# INF Connection established connIndex=0 ip=...
# INF Registered tunnel connection ...
```

> ✅ Le service redémarrera automatiquement après chaque reboot, coupure réseau ou crash.

---

## Étape 4 — Générer le token d'accès MCP dans Jeedom

Le plugin intègre son propre système d'authentification **Bearer**, distinct du token Cloudflare.

1. Dans Jeedom : **Plugins → Protocoles → MCP Jeedom**
2. Page de **Configuration du plugin**
3. Section **Token d'accès externe** → cliquer **Générer un token**
4. Copier le token affiché *(96 caractères hexadécimaux)*

> Le proxy PHP `jeemcp_proxy.php` vérifie ce token sur chaque requête entrante.  
> Sans token configuré, toute connexion externe est refusée avec **HTTP 403**.

---

## Étape 5 — Vérifier la connexion end-to-end

### 5.1 Tester le proxy depuis internet

Depuis un appareil **hors de votre LAN** (téléphone en 4G par exemple) :

```bash
# Probe sans auth (toujours accessible)
curl https://jeedom-mcp.votre-domaine.workers.dev/plugins/mcp_jeedom/core/php/jeemcp_proxy.php?probe=1

# Résultat attendu :
# {"status":"ok","daemon_running":true,"auth_configured":true,"port":8765}
```

### 5.2 Tester l'authentification Bearer

```bash
# Sans token → doit retourner 403
curl -I https://jeedom-mcp.votre-domaine.workers.dev/plugins/mcp_jeedom/core/php/jeemcp_proxy.php

# Avec token → doit retourner 200
curl -I \
  -H "Authorization: Bearer VOTRE_TOKEN_MCP" \
  https://jeedom-mcp.votre-domaine.workers.dev/plugins/mcp_jeedom/core/php/jeemcp_proxy.php
# HTTP/2 200
```

### 5.3 Vérifier l'état dans Jeedom

```bash
# Cloudflare tunnel actif
sudo systemctl is-active cloudflared   # → active

# Daemon MCP actif
cat /tmp/mcp_jeedom/deamon.pid | xargs ps -p

# Port MCP en écoute
ss -tlnp | grep 8765
# LISTEN ... *:8765
```

---

## Étape 6 — Configurer Claude Desktop

### 6.1 Localiser le fichier de configuration

| OS | Chemin du fichier |
|---|---|
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

### 6.2 Configuration via Cloudflare Tunnel *(recommandée)*

Transport **Streamable HTTP** via le proxy PHP :

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "https://jeedom-mcp.votre-domaine.workers.dev/plugins/mcp_jeedom/core/php/jeemcp_proxy.php",
        "--header", "Authorization: Bearer VOTRE_TOKEN_MCP"
      ]
    }
  }
}
```

### 6.3 Configuration via accès local LAN *(sans Cloudflare)*

```json
{
  "mcpServers": {
    "jeedom_mcp": {
      "command": "uvx",
      "args": [
        "mcp-remote@latest",
        "http://192.168.1.79:8765/mcp"
      ]
    }
  }
}
```

> Remplacez `192.168.1.79` par l'IP de votre Jeedom et `8765` par le port configuré.  
> Aucun token requis en accès local.

> ⚠️ Remplacez `VOTRE_TOKEN_MCP` par le token généré à l'étape 4, et `jeedom-mcp.votre-domaine.workers.dev` par votre hostname Cloudflare réel.

### 6.4 Redémarrer Claude Desktop

Fermez complètement Claude Desktop et rouvrez-le. Dans une nouvelle conversation, l'icône 🔧 (outils) doit afficher les outils Jeedom.

---

## Commandes SSH utiles

### Gestion du service cloudflared

```bash
sudo systemctl status cloudflared       # Statut
sudo systemctl restart cloudflared      # Redémarrer
sudo systemctl stop cloudflared         # Arrêter
sudo systemctl disable cloudflared      # Désactiver le démarrage auto
sudo journalctl -u cloudflared -f       # Logs en temps réel
sudo journalctl -u cloudflared -n 100 --no-pager   # 100 dernières lignes
```

### Mettre à jour cloudflared

```bash
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
sudo systemctl restart cloudflared
cloudflared --version
```

### Désinstaller cloudflared

```bash
sudo cloudflared service uninstall
sudo dpkg -r cloudflared
```

### Diagnostiquer la connexion MCP

```bash
# Port MCP en écoute
ss -tlnp | grep 8765

# Tester le endpoint MCP localement
curl -s --max-time 3 http://127.0.0.1:8765/mcp -X POST \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"ping","params":{}}'

# Logs daemon MCP (erreurs)
tail -100 /var/www/html/log/mcp_jeedom_daemon | grep -E "(ERROR|WARN|Tool)"
```

---

## Dépannage

### ❌ cloudflared ne démarre pas

```bash
sudo journalctl -u cloudflared -n 50 --no-pager
```

**Token invalide ou expiré :** retournez sur [dash.cloudflare.com](https://dash.cloudflare.com) → Zero Trust → Tunnels → votre tunnel → récupérez le token, puis :

```bash
sudo cloudflared service uninstall
sudo cloudflared service install NOUVEAU_TOKEN
sudo systemctl start cloudflared
```

### ❌ Claude Desktop ne voit pas les outils MCP

1. Vérifier que les deux services tournent :

```bash
sudo systemctl is-active cloudflared   # → active
cat /tmp/mcp_jeedom/deamon.pid         # → PID valide
```

2. Tester le proxy depuis l'extérieur :

```bash
curl -s "https://VOTRE_URL/plugins/mcp_jeedom/core/php/jeemcp_proxy.php?probe=1"
# {"status":"ok","daemon_running":true,...}
```

3. Vérifier que le token dans `claude_desktop_config.json` correspond exactement à celui généré dans Jeedom.

4. Redémarrer Claude Desktop **complètement** (pas juste rouvrir la fenêtre).

### ❌ HTTP 403 sur toutes les requêtes

Le token est absent ou incorrect. Dans Jeedom : **Configuration du plugin → Token d'accès externe** — vérifiez qu'un token est actif. Si nécessaire, régénérez-en un et mettez à jour `claude_desktop_config.json`.

### ❌ Déconnexions fréquentes

Cloudflare impose un timeout de 100 s sur les connexions HTTP longues. `mcp-remote` gère les reconnexions automatiquement — c'est normal et transparent pour l'utilisateur.

Si les déconnexions causent des problèmes, vérifiez les logs :

```bash
tail -f /var/www/html/log/mcp_jeedom_daemon
```

---

## Récapitulatif des étapes

| # | Action | Où |
|:---:|---|---|
| 1 | Créer compte Cloudflare + tunnel + copier le token CF | [dash.cloudflare.com](https://dash.cloudflare.com) |
| 2 | Installer cloudflared (`.deb` selon archi) | SSH → Jeedom |
| 3 | `sudo cloudflared service install TOKEN_CF` | SSH → Jeedom |
| 4 | `sudo systemctl enable && start cloudflared` | SSH → Jeedom |
| 5 | Générer le token MCP dans le plugin | Jeedom UI |
| 6 | Copier l'URL proxy dans `claude_desktop_config.json` | Votre PC |
| 7 | Redémarrer Claude Desktop | Votre PC |
| 8 | Vérifier : icône 🔧 visible avec les outils Jeedom | Claude Desktop |

---

← [Retour à la documentation principale](index.md)

*mcp_jeedom — Tutoriel Cloudflared*