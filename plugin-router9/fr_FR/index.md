---
layout: default
title: Documentation 9Router
lang: fr_FR
pluginId: router9
---

# Description

Plugin d'intégration du routeur/proxy IA local **9Router**. Il permet de surveiller l'usage, les quotas et l'état des providers (Gemini/Antigravity, etc.) depuis Jeedom.

> **Important**
>
> Ce plugin ne installe ni ne configure 9Router : l'instance doit déjà fonctionner et être joignable depuis la box.

# Versions supportées

| Composant | Version |
|-----------|---------|
| Jeedom    | >= 4.4  |

# Installation

Téléchargez, installez et activez le plugin comme tout plugin Jeedom. À l'installation et à la mise à jour, le jeu de widgets *iso* est téléchargé depuis GitHub (widgets par défaut de Jeedom s'il est indisponible).

# Configuration du plugin

| Paramètre | Description |
|-----------|-------------|
| URL de 9Router | Adresse de l'API (défaut : `http://127.0.0.1:20128`) |
| Mot de passe dashboard | Mot de passe du dashboard 9Router, utilisé pour se connecter automatiquement (stocké chiffré) |
| Clé API (optionnel) | Clé envoyée en `Authorization: Bearer` (stockée chiffrée) |

# Équipements

Deux types d'équipement :

- **Routeur global** : statistiques globales (statut, requêtes totales, tokens totaux, coût total).
- **Compte Gemini/Antigravity** : un équipement par compte provider (statut, email, tier, quota restant, dernière erreur).

Le bouton **Scanner 9Router** de la page du plugin liste les providers détectés et indique s'ils existent déjà dans Jeedom. Les valeurs sont rafraîchies toutes les 5 minutes (cron) ou via la commande *Rafraîchir*.

Le bouton **Forcer template Iso** réapplique les widgets iso aux commandes de l'équipement.

# Panel Live Monitor

Le panel affiche en temps réel les dernières requêtes passant par 9Router (heure, provider, modèle, tokens, coût, statut). Il est réservé aux administrateurs.

# Limitations connues

- Les commandes *Reset stats*, *Toggle* et *Choix du modèle* ne déclenchent pas encore d'action côté 9Router.

# FAQ

**Aucune donnée ne remonte ?** Vérifiez l'URL, le mot de passe/la clé API, puis le log `router9`.
