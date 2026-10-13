---
layout: default
title: Cloudflare Tunnel — Documentation
lang: fr_FR
pluginId: cloudflare
---

# Plugin Cloudflare Tunnel (`cloudflare`)

Le plugin **Cloudflare Tunnel** permet de configurer un accès distant sécurisé à votre box Jeedom sans ouvrir aucun port entrant sur votre box Internet et sans exposer votre adresse IP publique.

Il s'appuie sur la technologie **Cloudflare Tunnel** (`cloudflared`) et l'API v4 de Cloudflare pour établir un tunnel chiffré sortant entre Jeedom et le réseau mondial de Cloudflare.

---

## 1. Fonctionnement

1. Un démon système dédié (`cloudflared`) s'exécute localement sur votre box Jeedom sous la forme d'un service systemd (`cloudflared-jeedom.service`).
2. Ce démon maintient des connexions sécurisées sortantes vers les serveurs Cloudflare (Edge).
3. Le plugin configure automatiquement la règle d'acheminement (*ingress rule*) redirigeant le trafic de votre nom d'hôte personnalisé (ex: `jeedom.mondomaine.fr`) vers votre instance locale Jeedom.
4. Un enregistrement DNS de type CNAME pointant vers le tunnel est créé dans votre zone Cloudflare.

L'accès local direct (IP de votre réseau local) n'est jamais modifié ni perturbé.

---

## 2. Prérequis

- Un compte Cloudflare avec au moins une **zone active** (un nom de domaine dont les serveurs DNS sont délégués à Cloudflare).
- Un **jeton API personnalisé** Cloudflare avec les 4 permissions minimales suivantes (aucun droit sur le compte ou Access n'est requis) :

| Ressource | Permission |
|---|---|
| Zone → Zone | Lire (`Read`) |
| Zone → DNS | Modifier (`Edit`) |
| Compte → Cloudflare Tunnel | Lire (`Read`) |
| Compte → Cloudflare Tunnel | Modifier (`Edit`) |

> [!TIP]
> Consultez le guide détaillé [Créer le token API](token_setup) pour la procédure pas-à-pas sur dash.cloudflare.com.

---

## 3. Configuration du plugin

1. Rendez-vous dans **Plugins → Gestion des plugins → Cloudflare Tunnel**.
2. Dans le champ **Token API Cloudflare**, collez votre jeton puis cliquez sur **Valider et enregistrer**.
3. Le plugin vérifie immédiatement la validité du token, ses scopes et l'accessibilité de vos zones auprès de l'API Cloudflare. En cas d'erreur de permissions, rien n'est sauvegardé.

---

## 4. Gestion de l'équipement

Rendez-vous dans **Plugins → Sécurité → Cloudflare Tunnel** :

1. Ouvrez l'équipement Cloudflare unique.
2. Dans l'onglet **Tunnel et daemon** :
   - **Installer cloudflared** : installe le binaire officiel et met en place le service systemd local ainsi que les règles sudoers restreintes.
   - **Zone Cloudflare** : cliquez sur **Charger** puis sélectionnez votre domaine.
   - **Nom du tunnel** : indiquez un identifiant (ex: `jeedom-maison`).
   - **Hostname public** : saisissez le nom de domaine complet souhaité (ex: `jeedom.mondomaine.fr`, qui doit obligatoirement correspondre à la zone choisie).
   - Cliquez sur **Créer le tunnel** : le plugin génère le tunnel Cloudflare, enregistre le DNS et déploie le token localement.
   - Cliquez sur **Démarrer** pour lancer le service.
   - Utilisez **Tester bout-en-bout** pour confirmer que la résolution DNS et l'accès HTTPS sont opérationnels.

---

## 5. Commandes de l'équipement

L'équipement met à disposition les commandes suivantes :

| Commande | Type | Description |
|---|---|---|
| `Statut du tunnel` | Info (string) | Statut du tunnel côté Cloudflare (`healthy`, `down`, `inactive`, etc.) |
| `Connexions du tunnel` | Info (numérique) | Nombre de connexions actives vers l'Edge Cloudflare |
| `Version de cloudflared` | Info (string) | Version installée du binaire `cloudflared` |
| `IP publique détectée` | Info (string) | IP publique sortante observée |
| `État du daemon` | Info (string) | État du service systemd (`active`, `inactive`, `failed`) |
| `Démarrer le tunnel` | Action | Lance le service `cloudflared-jeedom` |
| `Arrêter le tunnel` | Action | Coupe le service `cloudflared-jeedom` |

---

## 6. Périmètre et fonctionnalités hors scope

### Cloudflare Pages
Cloudflare Pages est une solution d'hébergement statique autonome (type Vercel, Netlify ou GitHub Pages).
Le plugin Jeedom ne propose aucune interface pour déployer des sites statiques, ni intégration Git, ni API Cloudflare Pages.
La gestion de Cloudflare Pages se fait directement via l'interface web de Cloudflare, par liaison avec un dépôt Git (GitHub/GitLab), ou en local via l'outil CLI officiel Cloudflare (`wrangler`).

### Pages Perso Free en HTTPS
Si vous souhaitez passer des Pages Perso Free en HTTPS à l'aide de votre domaine Cloudflare :
Aucun plugin Jeedom n'est nécessaire. La configuration s'effectue directement sur le tableau de bord Cloudflare (via un Worker reverse-proxy ou une règle DNS/redirection pointant vers vos pages Free).

---

## 7. Sécurité & Bonnes pratiques

- **Aucun port ouvert** : Votre box ou routeur ne nécessite aucune redirection de port (NAT).
- **Principe de moindre privilège** :
  - Le token API ne requiert aucun droit d'administration sur votre compte Cloudflare.
  - Le fichier de configuration sensible du tunnel est stocké avec des permissions restreintes (`root:root`, `0600`).
  - Les commandes sudo sont strictement limitées par un fichier de configuration sudoers dédié (`systemctl` sur l'unité `cloudflared-jeedom` et script de déploiement).
- **Protection LAN** : L'accès direct via votre réseau local reste inchangé et indépendant du tunnel.
