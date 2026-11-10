---
layout: default
title: Wyoming Bridge — Changelog
lang: fr_FR
pluginId: wyoming_bridge
---

# Changelog Wyoming Bridge

## 2026-11-10

- Heartbeat du core : un démon actif est désormais vu comme communiquant (sinon, avec un heartbeat réglé, le core l'aurait déclaré muet).
- Commandes de suivi : dernière phrase, dernière réponse, traitée ou non, durée, dernier satellite (mises à jour après l'envoi de la réponse).
- Documentation et changelog, publiés sur le site de documentation.
- Paquet `wyoming` 1.11.0.

## 2026-11-09

- Page du plugin au format Jeedom : Configuration, Tester, Santé, équipement et commandes.
- Fenêtre **Tester** : envoi d'une phrase par le protocole Wyoming, comme un satellite.
- Fenêtre **Santé** : état général, versions installées et dernières versions disponibles.
- Valeurs par défaut affichées dans la configuration (port, bind, délai, message d'échec).
- Limite de débit et historique propres à chaque satellite.
- Version du service annoncée d'après le plugin.
- Délai transmis à AI Assistant : plus aucune action lancée après une réponse « non traité ».
- Message d'échec dit au satellite au lieu d'un silence ; langue annoncée = langue de Jeedom.
- Sécurité : liste IPs autorisées obligatoire hors `127.0.0.1`, jeton hors de la ligne de commande, lectures réseau bornées, arrêt du démon limité à ses propres processus.
- Démarrage automatique du démon confié au core ; librairies d'installation embarquées.

## 2026-10-13

- `handle.php` répond 401 quand le plugin est inactif.

## 2026-09-20

- Jeton chiffré en base, licence AGPL-3.0.

## 2026-09-08

- Première version.
