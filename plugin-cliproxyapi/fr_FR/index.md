---
layout: default
title: CLIProxyAPI — Documentation
lang: fr_FR
pluginId: cliproxyapi
---

# Plugin Jeedom CLIProxyAPI

Le plugin **CLIProxyAPI** permet d'intégrer, superviser et piloter le service [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) directement au cœur de votre domotique Jeedom.

CLIProxyAPI est un proxy haute performance écrit en Go qui transforme vos comptes CLI (authentifiés en OAuth) pour Claude, OpenAI/Codex, Google Gemini/Antigravity, Grok et Kimi en une API locale standard unifiée, compatible avec le protocole OpenAI (`/v1/chat/completions`, `/v1/models`).

---

## 1. Prérequis & Compatibilité

- **Jeedom Core** : Version 4.4 ou supérieure (optimisé 4.5+).
- **Système d'exploitation** : Debian 11 (Bullseye), Debian 12 (Bookworm), ou conteneur officiel Docker Jeedom.
- **Architectures supportées** :
  - `x86_64` / `amd64` (PC, VM Proxmox, Intel NUC) : Téléchargement et installation automatique.
  - `aarch64` / `arm64` (Raspberry Pi 4/5 64 bits, Odroid, box ARM 64 bits) : Téléchargement et installation automatique.
  - *Remarque architecture 32-bit (`armv7`)* : Le projet upstream officiel ne fournissant pas d'archives précompilées 32-bit, une compilation Go manuelle est requise sur ces architectures.

---

## 2. Installation & Dépendances

1. Installez le plugin depuis le Market Jeedom ou déposez-le dans le dossier `plugins/cliproxyapi/`.
2. Activez le plugin sur la page de gestion des plugins Jeedom.
3. Rendez-vous dans **Configuration du plugin** et lancez l'installation des dépendances. Le script va :
   - Détecter automatiquement l'architecture de votre box.
   - Télécharger la dernière version stable du binaire `cli-proxy-api`.
   - Extraire et sécuriser le binaire avec les permissions appropriées (`chmod 755`, utilisateur `www-data`).
   - Créer le dossier sécurisé `data/auths/` avec des permissions strictes (`0700`).

---

## 3. Configuration Réseau & Sécurité

### Exposition et Bind Réseau
- **Localhost uniquement (`127.0.0.1`)** *(Recommandé si seuls Jeedom et les services locaux utilisent l'IA)* :
  Le service n'est accessible que depuis la machine Jeedom elle-même.
- **Écoute sur tout le LAN (`0.0.0.0`)** *(Pour connecter Open WebUI, Cline, Home Assistant...)* :
  Le service répond sur l'adresse IP de votre box Jeedom (ex: `http://192.168.1.50:8317/v1`).
  > [!IMPORTANT]
  > **Obligation de sécurité :** Si vous choisissez l'écoute LAN (`0.0.0.0`), la présence d'au moins une clé d'API cliente dans la configuration est **strictement obligatoire**. Le plugin bloquera tout démarrage sans clé.
  >
  > **Ne JAMAIS exposer ce port directement sur Internet** via une redirection de port NAT. Pour un accès depuis l'extérieur, utilisez exclusivement un VPN sécurisé (WireGuard, Tailscale, OpenVPN).

### Isolation de la Management API
- La **Management API v8** de CLIProxyAPI est bridée en dur avec `allow-remote: false` et protégée par un secret interne de 64 caractères aléatoires.
- Elle n'est accessible **que** par le backend PHP Jeedom en local (`127.0.0.1`).
- Aucune machine distante du LAN ne peut accéder aux routes d'administration, ni télécharger vos jetons OAuth.

### Pare-feu (UFW / Iptables)
Si un pare-feu est actif sur votre serveur Jeedom et que vous utilisez l'écoute LAN, pensez à autoriser le port TCP configuré :
```bash
sudo ufw allow 8317/tcp
```
Le plugin respecte votre système et ne modifie **jamais** automatiquement vos règles de pare-feu.

---

## 4. Premier Login OAuth (Connexion d'un compte IA)

1. Rendez-vous dans **Plugins > Communication > CLIProxyAPI**.
2. Cliquez sur le bouton **Ajouter un compte (OAuth)**.
3. Choisissez le fournisseur désiré :
   - **Anthropic Claude** (Claude Code, Opus, Sonnet)
   - **OpenAI Codex / GPT** (Comptes ChatGPT Plus, Team, Pro)
   - **Google Antigravity / Gemini** (Gemini Pro, Flash)
   - **xAI / Grok** (Grok 3, Composer)
   - **Moonshot Kimi** (Kimi K3, Code)
4. Cliquez sur **Continuer vers l'authentification**.
5. Cliquez sur **Ouvrir la page de connexion** : votre navigateur s'ouvre sur la page d'authentification officielle du fournisseur.
6. Connectez-vous et autorisez l'accès.
7. Dès confirmation, le wizard Jeedom détecte la validation par polling automatique et affiche **Authentification réussie !**.
8. Un équipement Jeedom dédié au compte est automatiquement créé, et les modèles associés apparaissent instantanément dans la liste des modèles disponibles.

---

## 5. Utilisation dans vos clients IA (Open WebUI, Cline, etc.)

Pour connecter un outil tiers compatible OpenAI à votre proxy Jeedom :

- **Base URL / Endpoint** : `http://<IP_JEEDOM>:8317/v1`
- **API Key** : Renseignez l'une de vos clés clientes générées (ex: `sk-cpa-...`)
- **Modèles** : Utilisez les noms de modèles listés sur la page du plugin (ex: `claude-3-5-sonnet-20241022`, `gpt-4o`, `gemini-1.5-pro`, etc.).

---

## 6. Utilisation en Scénarios Jeedom

L'équipement principal **Service** expose les commandes suivantes :
- `#[IA][CLIProxyAPI Service][État]#` : Valeur binaire (1 = service en cours d'exécution, 0 = arrêté).
- `#[IA][CLIProxyAPI Service][Nombre de modèles]#` : Nombre total de modèles opérationnels.
- `#[IA][CLIProxyAPI Service][Nombre de comptes]#` : Nombre de comptes connectés.
- `#[IA][CLIProxyAPI Service][Tester un modèle]#` : Commande message permettant d'interroger directement une IA en scénario :
  - **Titre** : Nom du modèle (ex: `gpt-4o-mini` ou `claude-3-5-sonnet`)
  - **Message** : Votre prompt (ex: `Analyse cette température de 24°C et indique si la clim doit s'allumer`)
  - La réponse est enregistrée dans l'info `#[IA][CLIProxyAPI Service][Résultat dernier test]#`.

---

## 7. Dépannage & Maintenance

- **Le service ne démarre pas ?**
  Vérifiez dans la configuration que le port choisi n'est pas déjà utilisé par un autre service et consultez le log `cliproxyapi` en niveau *Debug*.
- **Rollback vers la version précédente :**
  Lors de chaque mise à jour du binaire via le script de dépendances, l'ancienne version est conservée sous `resources/bin/cli-proxy-api.bak`. En cas de régression de l'éditeur, un bouton dans la configuration permet de rétablir instantanément la version antérieure.
