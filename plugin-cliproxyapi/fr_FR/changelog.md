---
layout: default
title: CLIProxyAPI — Changelog
lang: fr_FR
pluginId: cliproxyapi
---

# Changelog CLIProxyAPI

Toutes les modifications notables apportées à ce projet sont documentées dans ce fichier.

## [1.1.0] - 2026-09-30

### Ajouté
- **Gestionnaire natif des dépendances Jeedom** : Implémentation de `dependancy_info()`, `dependancy_install()` pour piloter `install_apt.sh` directement depuis le centre de dépendances Jeedom.
- **Exclusion de sauvegarde (`backupExclude`)** : Exclusion du dossier `resources/bin` des archives de sauvegarde Jeedom afin d'économiser ~30-50 Mo d'espace par backup.
- **Chiffrement au repos des secrets (`$_encryptConfigKey`)** : Chiffrement automatique en base de données par le Core Jeedom pour le secret de Management API et les clés clientes.
- **Protection Apache `.htaccess`** : Blocage de l'accès HTTP direct aux scripts PHP dans `plugin_info/` et `core/class/` avec conservation de l'accès public à l'icône du plugin.
- **Icône officielle 256x256** : Ajout de `plugin_info/cliproxyapi_icon.png`.
- **Suivi de la consommation de tokens (Usage Keeper)** : Consommation périodique de l'endpoint d'observabilité (`/observability/usage/queue?count=100`) et cumul sur les commandes `tokens_input`, `tokens_output`, `tokens_total` de chaque compte et du service global.
- **Alertes proactives sur dégradation de compte** : Notification `message::add()` et log `warning` sur transition d'état d'un compte de `ok` vers `désactivé` ou `indisponible` (anti-boucle sur front montant).

### Amélioré
- **Sécurisation AJAX** : Appel strict de `ajax::init()` en tête de fichier pour validation CSRF préalable.
- **Vanilla JS & Préservation des commandes** : Stockage de `_cmdData` et inclusion de champs masqués (`logicalId`, `type`, `subType`, `unite`) dans le tableau des commandes pour éviter toute altération lors de la sauvegarde.
- **Affichage dynamique des équipements** : Handler `printEqLogic()` affichant conditionnellement les champs propres aux comptes (fournisseur, index).
- **Protection XSS** : Rendu sécurisé des messages d'erreur dans les modales et la page de configuration sans injection brute dans `innerHTML`.

## [1.0.0] - 2026-09-29

### Ajouté
- Version initiale du plugin CLIProxyAPI pour Jeedom Core 4.4+.
- Supervision du daemon binaire Go avec gestion PID et contrôle des ports.
- Support multi-fournisseurs (Claude, Codex, Antigravity, xAI, Kimi).
- Assistant de connexion OAuth multi-étapes avec polling automatique et saisie manuelle de secours.
- Génération automatique de `config.yaml` v8 avec secret de management et clés d'API clientes.
- Fonctionnalité de rollback en un clic vers la version précédente du binaire.
