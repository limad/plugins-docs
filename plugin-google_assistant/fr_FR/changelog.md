---
layout: default
title: Google Assistant — Changelog
lang: fr_FR
pluginId: google_assistant
---

# Changelog Google Assistant

## Version en cours de développement

### 09/11/2026

- **Sécurité et fiabilité** : le pack de widgets est installé dans une version figée et vérifiée ; s'il est déjà à jour, aucun téléchargement n'a lieu ; un pack géré par le plugin Widgets iso est laissé en place.

### 08/11/2026

- **Nouveautés et corrections** : installation des widgets plus fiable lors d'une mise à jour de plusieurs plugins en même temps ; un seul téléchargement au lieu d'un par plugin ; solution de secours si le téléchargement échoue, avec la cause exacte dans le log ; vos widgets personnalisés ne sont jamais écrasés.

### 06/11/2026

- **Nouveautés et corrections** : les messages d'erreur réseau ne sont plus répétés deux fois dans le log.

### 29/10/2026

- **Nouveautés et corrections** : page du plugin réorganisée (titre « Gestion - Google Assistant », tuiles Documentation et Community en dernier).

### 23/10/2026

- **Nouveautés et corrections** : traductions mises à jour (allemand, anglais, espagnol, italien, portugais).

### 14/10/2026

- **Nouveautés et corrections** : nouvelle icône ; la bascule entre le mode compact et le mode édition du tableau des commandes fonctionne.
- **Sécurité et fiabilité** : les fichiers audio des réponses ne sont plus accessibles en devinant leur nom ; la session Google capturée est désormais enregistrée chiffrée ; le proxy de capture de session s'arrête seul après 10 minutes ; le téléchargement des lanceurs Windows est refusé à un visiteur non connecté ; le lanceur Windows ne conserve plus la clé API Jeedom en clair.

### 20/09/2026

- **Nouveautés et corrections** : première version. Envoi de commandes texte à Google Assistant et récupération de sa réponse, y compris pour les commandes domotiques ; liaison du compte Google en un clic depuis la page Configuration ; lecture à voix haute de la réponse par le plugin TTS Cast, avec option « Silencieux » dans les scénarios ; reformulation optionnelle par Gemini des phrases non comprises ; tuile de dialogue sur le dashboard ; création automatique d'un équipement par appareil TTS Cast ; fenêtre Santé ; fonctions expérimentales Google Home (session Google, automatisations) ; guide illustré de création de l'application Google.
