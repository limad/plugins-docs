---
layout: default
title: CrowdSec — Changelog
lang: fr_FR
pluginId: crowdsec
---

# Changelog CrowdSec

## Version en cours de développement

### 09/11/2026

- **Nouveautés et corrections** : nouvelle option « Certificat CA (fichier PEM) » pour une LAPI en https avec un certificat auto-signé ; la blocklist communautaire n'est plus téléchargée à chaque relevé (une fois par heure), ce qui allège la box et la LAPI ; « Détections locales (24 h) » n'est plus plafonnée ; « IP bloquées localement » ne contient plus que des adresses (plus de plages ni de pays) ; saisir `off` dans Auto-actualisation désactive la lecture automatique ; messages d'erreur plus clairs (clé bouncer ou compte machine en cause, explication donnée par CrowdSec) ; une erreur s'affiche si la réinitialisation des widgets échoue ; traductions mises à jour.
- **Installation locale** : clé bouncer et compte machine dédiés `jeedom-local` (ceux nommés `jeedom` ne sont plus touchés) ; l'adresse d'écoute d'un CrowdSec déjà installé est respectée ; une configuration locale existante est sauvegardée avant d'être remplacée ; correction d'une réinstallation après une désinstallation incomplète ; l'avertissement de mise à niveau de CrowdSec s'affiche quelle que soit la langue de la box.
- **Sécurité et fiabilité** : bannir le loopback ou une adresse de la box Jeedom est refusé, pour ne pas s'enfermer dehors ; délai maximal d'une requête vers la LAPI ramené à 15 s ; le pack de widgets n'est installé que s'il correspond exactement à la version attendue.

### 08/11/2026

- **Sécurité et fiabilité** : le pack de widgets n'est téléchargé que s'il a changé ; les widgets déjà installés sont conservés en cas d'échec du téléchargement.

### 24/10/2026

- **Nouveautés et corrections** : nouvelles commandes « Principaux scénarios », « Détections locales (24 h) », « Dernière détection locale » et « Dernière mise à jour des listes » (les trois dernières nécessitent le compte machine) ; onglet Commandes repensé (mode compact / mode édition, informations brutes).
- **Surveillance** : message dans le centre de messages Jeedom quand la LAPI est injoignable (3 relevés de suite, avec l'état du service pour l'équipement local) ou quand la blocklist communautaire n'est plus mise à jour depuis plus de 6 h ; une ligne par équipement dans la page Santé de Jeedom.

### 22/10/2026

- **Nouveautés et corrections** : installation optionnelle de CrowdSec sur la box via les dépendances (dépôt officiel, port de la LAPI au choix, bouncer pare-feu en option) avec création automatique de l'équipement « CrowdSec (box) » ; correction de bannir / débannir, qui échouait avec CrowdSec 1.8.
- **Sécurité et fiabilité** : le mot de passe du compte machine n'apparaît plus dans le log d'installation.

### 15/10/2026

- **Nouveautés et corrections** : affichage homogène des commandes sur le dashboard (widgets iso).

### 14/10/2026

- **Nouveautés et corrections** : nouvelle commande « LAPI joignable » ; la liste des IP bloquées localement est limitée aux 50 plus récentes ; champs secrets avec bouton afficher / masquer.
- **Sécurité et fiabilité** : une URL de LAPI, une expression cron ou une durée de bannissement invalides sont refusées à la sauvegarde ; une configuration invalide ne bloque plus la mise à jour du plugin.

### 28/09/2026

- **Nouveautés et corrections** : première version ; lecture des décisions actives via la LAPI (clé bouncer) ; bannir / débannir une IP (compte machine, durée configurable) ; bouton de test de la connexion ; traduction anglaise.
