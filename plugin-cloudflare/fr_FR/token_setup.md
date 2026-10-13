---
layout: default
title: Cloudflare Tunnel — Créer le token API
lang: fr_FR
pluginId: cloudflare
---

# Créer le token API Cloudflare

Le plugin a besoin d'un **jeton API personnalisé** (pas la clé Global API), avec des
permissions strictement limitées. Il n'a besoin d'aucun droit sur votre compte
Cloudflare lui-même, uniquement sur une zone (domaine) et sur les tunnels.

## 1. Créer le jeton

1. Allez sur [dash.cloudflare.com/profile/api-tokens](https://dash.cloudflare.com/profile/api-tokens).
2. Cliquez sur **Créer un jeton** → **Créer un jeton personnalisé**.
3. Donnez-lui un nom explicite, par exemple `jeedom-cloudflare-plugin`.
4. Sous **Permissions**, ajoutez ces lignes (certains comptes affichent Lecture
   et Modification comme deux entrées séparées pour Cloudflare Tunnel — dans ce
   cas cochez les deux, cela reste dans la liste des 4 scopes minimaux) :

   | Ressource | Permission |
   |---|---|
   | Zone → Zone | Lire (Read) |
   | Zone → DNS | Modifier (Edit) |
   | Compte → Cloudflare Tunnel | Lire (Read) |
   | Compte → Cloudflare Tunnel | Modifier (Edit) |

5. Sous **Ressources de zone**, restreignez à la zone (domaine) que vous comptez
   exposer via Jeedom — évitez « Toutes les zones » si possible.
6. Sous **Ressources de compte**, sélectionnez votre compte pour la permission
   Cloudflare Tunnel.
7. **N'ajoutez rien d'autre** : pas de droit « Account Settings », pas de droit
   « Access », pas de droit « API Tokens ». Le plugin refuse volontairement tout
   token portant des droits `Access:*` ou d'écriture sur les paramètres du compte.
8. Validez, puis copiez le token affiché (il ne sera plus jamais visible en clair
   ensuite).

## 2. Renseigner le token dans Jeedom

Dans **Plugins > Cloudflare Tunnel > Configuration**, collez le token dans le
champ *Token API Cloudflare*, puis enregistrez.

Le plugin appelle immédiatement Cloudflare pour vérifier :
- que le token est actif (`/user/tokens/verify`) ;
- qu'il donne bien accès à au moins une zone (`GET /zones`) ;
- que les scopes DNS et Tunnel répondent correctement ;
- si le token peut relire ses propres permissions, qu'aucun droit hors liste
  n'est présent.

**En cas d'échec, rien n'est enregistré.** Le message affiché distingue
toujours un token invalide/expiré d'un problème de permissions.

## 3. Créer le tunnel

Depuis l'onglet **Tunnel et daemon** de l'équipement :
1. Installez `cloudflared` (bouton dédié — privilèges élevés, confirmation requise).
2. Chargez les zones disponibles, choisissez la vôtre.
3. Renseignez un nom de tunnel et le hostname public souhaité (doit se terminer
   par le nom de la zone choisie).
4. Cliquez sur **Créer le tunnel** : le plugin crée le tunnel côté Cloudflare,
   pousse la règle d'ingress (votre hostname → l'accès local Jeedom), crée
   l'enregistrement DNS, puis installe le token du tunnel dans le fichier
   root-only lu par le service.
5. Démarrez le daemon, puis lancez **Tester bout-en-bout**.

## Messages d'erreur possibles

| Message | Cause probable | Action |
|---|---|---|
| Token invalide ou expiré (401) | Token mal collé, révoqué, ou expiré | Recréez un token |
| Permissions insuffisantes (403) | Un des 4 scopes manque | Relisez l'étape 1 |
| Aucune zone active accessible | Ressources de zone du token mal ciblées | Ajoutez la zone au token |
| Token refusé : permissions interdites en V1 | Le token a un droit `Access:*` ou `Account Settings` | Recréez un token minimal |
| Cloudflare limite le débit des requêtes (429) | Trop d'appels rapprochés | Patientez quelques instants |
| Cloudflare injoignable | Panne réseau locale ou côté Cloudflare | Vérifiez la connectivité sortante du serveur |
| Hostname invalide : il doit se terminer par « … » | Hostname hors de la zone sélectionnée | Corrigez le hostname |
| sudoers ne autorise pas cette action | Installation des dépendances jamais lancée / sudoers absent | Relancez l'installation de cloudflared |

## Ce que le plugin ne fait jamais

- Il ne modifie jamais l'accès LAN direct (IP locale) de Jeedom.
- Il ne supprime jamais un tunnel ou un enregistrement DNS sans confirmation
  explicite, nom de la ressource affiché en toutes lettres.
- Il n'installe jamais de `NOPASSWD: ALL` : uniquement `systemctl
  {start,stop,restart,status} cloudflared-jeedom.service` et le script
  d'installation signé.

## Hors périmètre du plugin

### Cloudflare Pages
Cloudflare Pages est une solution d'hébergement statique autonome (type Vercel, Netlify ou GitHub Pages).
Le plugin Jeedom ne propose aucune interface pour déployer des sites statiques, ni intégration Git, ni API Cloudflare Pages.
La gestion de Cloudflare Pages se fait directement via l'interface web de Cloudflare, par liaison avec un dépôt Git (GitHub/GitLab), ou en local via l'outil CLI officiel Cloudflare (`wrangler`).

