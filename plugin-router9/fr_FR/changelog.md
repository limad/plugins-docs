---
layout: default
title: Changelog 9Router
lang: fr_FR
pluginId: router9
---

# Changelog

## 2026-10-07

- Correction : le handler AJAX ne se chargeait pas (chemin de `core.inc.php` erroné), ce qui rendait inopérants le scan des providers, le bouton *Forcer template Iso* et le panel *Live Monitor*
- Correction : les erreurs AJAX sont de nouveau renvoyées en JSON (`displayException`) ; le contrôle CSRF `ajax::init()` s'exécute avant le contrôle d'accès
- Correction : la sauvegarde d'un équipement n'écrase plus le nom, l'unité, l'historisation, le type générique ni l'ordre des commandes existantes (posés uniquement à la création)
- Sécurité : les valeurs renvoyées par l'API 9Router (provider, modèle, statut, nom/email) sont échappées dans le panel et la modale de scan (XSS)
- Sécurité : `plugin_info/.htaccess` bloque l'accès HTTP direct aux fichiers PHP de `plugin_info/`
- Sécurité : la page de configuration est réservée aux administrateurs ; liste blanche des types d'équipement
- Les champs *Mot de passe* et *Clé API* sont affichables (bouton œil)
- Le panel *Live Monitor* ne scrute plus l'API quand l'onglet est caché et n'empile plus les requêtes
- Le scan tolère un provider sans nom, email ou identifiant
- Ajout de la documentation (`docs/fr_FR`), du changelog et du README
- Ajout des liens `documentation` et `changelog` dans `info.json`
