---
layout: default
title: helloWatt changelog
lang: fr_FR
pluginId: helloWatt
---
# Changelog

>**IMPORTANT**
>
>Pour rappel s'il n'y a pas d'information sur la mise à jour, c'est que celle-ci concerne probablement la mise à jour de documentation, de bugs mineurs ou alors j'ai pas le temps !!!.

# 06/11/2026
- Types génériques : consommation et production du jour en « consommation / production journalière », plus de type sur les cumuls mensuels et annuels ; température en °C, de type météo ; plus de famille « Électricité » sur les compteurs gaz
- Un type ou une unité choisis à la main ne sont plus écrasés. Correction automatique à la mise à jour

# 08/04/2026
- Correction du cron qui ne fonctionnait qu'une heure sur deux au lieu d'alterner correctement
- Correction du widget custom qui ne s'affichait pas (dead code)
- Sécurisation du mot de passe (chiffrement automatique en base)
- Amélioration des performances de synchronisation (réduction significative des requêtes SQL)
- Reconnexion automatique en cas d'expiration de session Hello Watt
- Ajout de la protection contre la suppression accidentelle des commandes
- Ajout de nouveaux endpoints API (contrats, ecowatt, analyse tarifaire)
- Gestion automatique du fuseau horaire Europe/Paris pour les requêtes API
- Définitions des commandes externalisées dans `helloWatt.commands.json` avec templates iso-widget
- Compatibilité Jeedom 4.4+ : migration complète jQuery vers vanilla JS (domUtils, jeedomUtils, Sortable.js)
- Nettoyage du code : suppression des commentaires obsolètes, console.log, code mort
- Nettoyage automatique des données à la désinstallation du plugin
- Nombreuses corrections de bugs et optimisations

# 11/03/2024
- Optimisations diverses.

# 21/02/2023
- Mise à disposition du plugin en version beta
