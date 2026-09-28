---
layout: default
title: secposture — Changelog
lang: fr_FR
pluginId: secposture
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — secposture

## 0.4

- Traductions : anglais, allemand, espagnol, italien, portugais.
- Icône au format Market (309 × 348).
- Documentation : prérequis, liste des commandes système exécutées.

## 0.3

- Intégrité des fichiers : référence SHA-256 du core, des plugins et de fichiers système sensibles ; changements corrélés aux mises à jour Jeedom (attendus) ou signalés jusqu'à acceptation (inattendus).
- Exécutables dans `/tmp`, `/var/tmp`, `/dev/shm`.
- Commandes « Fichiers modifiés (inattendus) » et « Accepter les changements de fichiers » ; exclusions configurables.

## 0.2

- Nouveaux contrôles : ports TCP en écoute, pare-feu nftables (IPv4/IPv6), configuration SSH effective, dossiers sensibles servis par Apache, en-têtes HTTP, PHP du serveur web, fail2ban/CrowdSec et plugin f2ban.
- Configuration : ports autorisés, activation des contrôles web.
- Valeurs par défaut de la configuration dans `core/config/secposture.config.ini`.

## 0.1

- Première version : 12 contrôles (Jeedom, système, MariaDB, sauvegardes), score et commandes.
