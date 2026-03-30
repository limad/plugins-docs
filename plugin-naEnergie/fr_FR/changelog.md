---
layout: default
title: Changelog naEnergie
lang: fr_FR
pluginId: naEnergie
---

# Changelog
---

<img align="right" src="../images/naEnergie_icon.png" width="100">

## Netatmo-Energie - Plugin pour Jeedom (Intégration des thermostats et vannes NETATMO).

---

* Installation du plugin [Netatmo-Energie.](https://limad.github.io/plugin-naEnergie/fr_FR/#tocAnchor-1-3)
* Topic dédié au plugin [Topic community.](https://community.jeedom.com/t/re-plugin-tiers-netatmo-energie/38002/)
* [Documentation du plugin.](https://community.jeedom.com/t/re-plugin-tiers-netatmo-energie/38002/)

### Liste des évolutions majeures de la version courante :
>*Liste non exhaustive. Les changements mineurs et/ou corrections de bugs ne figurent pas forcément ici.*
>

### Version 29/03/2026 (Beta)

#### Architecture/Fiabilité
* Réécriture complète du code
* Extraction de `class naEnergieCmd` dans `naEnergieCmd.php`
* Découpage de `naEnergie` : `naEnergieTherm` (actions thermostat) et `naEnergieSync` (cron/webhook/sync)
*...

#### Webhook
* Correction bug critique `wbhook_last` qui cassait silencieusement la chaîne.
* Nouvel événement `boiler_status` : mise à jour directe de la commande sans refresh complet.
* Événement `schedule` : mise à jour directe de `nowplanid`/`nowplanning` sans attendre le refresh.

#### Gestion des erreurs & logs
* Nouveau helper `naEnergie::logErr()` : bascule les logs `error` en `warning` selon la config.
* Option "Logs discrets" (`log_error_as_warning`) dans la page de configuration.
* Compteur `naEnergie_nbRequest` : incrémenté dans `NA_ApiClient::api()`, alerte à 50 req, reset en `cronDaily`.
* Correction initialisation manquante de `$eqErr_Ar` dans `errMgmt()`.
* Logs warning exhaustifs dans `NA_ApiClient::wRequest()` avec trace de l'appelant.

#### Sécurité
* `naEnergie.ajax.php` : validation du paramètre `type` avant construction du chemin de fichier (prévention path traversal).

#### API Client
* Correction de bugs divers.

#### Frontend
* `naEnergie.js` : remplacement total de jQuery par vanilla JS + `domUtils.ajax` / `jeedomUtils.showAlert`.

---

### Version 25/02/2024 (Beta)
* Ajout des thermostats (Smarther with Netatmo)
* Améliorations de la page de configuration des équipements
* ...

### Version 21/08/2023  (Beta/Stable)
* Améliorations des widgets Météo
* Améliorations de la gestion/déclaration des erreurs liées aux équipements Météo
* Améliorations des alertes (Batterie) liées aux équipements
* Bug-Fix Divers.
* ...

### Version 14/08/2023  (Beta/Stable)
* Intégration Prévisions météo "partie Météo"
* Bug-Fix Divers.

### Version 06/08/2023  (Beta/Stable)
* Bug-Fix Divers sur la partie Météo.

### Version 05/08/2023  (Stable)
* Passage de Beta en Stable (voir 03/08/2023).

### Version 03/08/2023  (Beta)
* Bug-Fix modules « Commande Intelligente de Climatiseur ».
* Widgets personnalisés et personnalisables (isoTile, isoLine) pour les équipements Météo
* ...

### Version 15/07/2023  (Beta)
* Mise à jour majeure.
* Prise en charge des nouveaux modules « Commande Intelligente de Climatiseur ».
* Prise en charge des modules « Netatmo-Météo ».
* ...

### Version 23/12/2022
* Bug-Fix Perte Auto-Adapt.
* Bug-Fix Disparition des tuiles Custom suite à une mise à jour.
* ...

### Version 23/10/2022
* Amélioration du protocole AuthFlow pour pallier aux manquements des utilisateurs
* Conformité de la présentation des commandes pour Jeedom v4.3 (Affichage des valeurs)

### Version 23/09/2022
* Amélioration du protocole AuthFlow pour une meilleure interactivité

### Version 17/09/2022
* Prise en charge du protocole AuthFlow pour l'authentification (nécessite d'obtenir une autorisation depuis le site Netatmo)

### Version 20/10/2021 (Beta)
* !!!! Mise à jour majeure !!!!
* Intégration des thermostats intelligents (Netatmo OpenTherm) y compris gestion ECS
* Importantes modifications dans le code du plugin
* Optimisations globales du traitement des données API
* Optimisations de la gestion des erreurs
* Optimisations des tuiles (dashboard)
* Panel (en version beta)

### Version 01/12/2020
* Optimisations diverses (error logs,...)
* Correction et amélioration des tuiles (dashboard, mobile)
* Modification commande action "consigne" de type slider à type message avec endtime
* Amélioration de la prise en charge de la commande "endtime" saisie en minutes/format date/timestamp

### Version 09/10/2020
* Arrêt du support pour le plugin en Jeedom V3. Donc pour Jeedom V4:
  - Optimisations diverses (error logs,...)
  - Mise à jour des commandes (Type Generic)
  - Compatibilité avec l'app mobile Jeedom
  - Prise en charge des retours Webhook
  - ...

### Version 1.0 27/04/2020
* Correction et amélioration mineures des tuiles (dashboard, mobile)
  - Signalement et affichage des alertes "error" sur la tuile.

### Version 25/03/2020
* Installation de plugins
* Mise à jour de plugins
