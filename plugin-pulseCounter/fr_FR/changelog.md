---
layout: default
title: Pulse Counter — Changelog
lang: fr_FR
pluginId: pulseCounter
---

# Changelog Pulse Counter

## Version en cours de développement

### 09/11/2026

- **Sécurité et fiabilité** : les widgets installés avec le plugin proviennent d'une version figée et sont vérifiés avant installation.

### 08/11/2026

- **Sécurité et fiabilité** : les widgets ne sont téléchargés que s'ils ont changé ; vos widgets personnalisés ne sont jamais écrasés ; message plus clair dans le log en cas d'échec du téléchargement.

### 06/11/2026

- **Nouveautés et corrections** : type « Consommation » réservé à l'index des compteurs électriques ; « Conso Jour » d'un compteur électrique en « Consommation journalière » ; plus de type « Puissance » sur le compteur de gaz ; eau : index exprimé en m³ au lieu de kWh ; la mise à jour corrige les commandes existantes sans toucher un type ou une unité modifiés à la main.
- **Sécurité et fiabilité** : « Rafraichir » et le calcul des index ne plantent plus sur un compteur qui n'a encore reçu aucune impulsion ; un poids par impulsion manquant est signalé dans le log.

### 29/10/2026

- **Interface** : titre « Gestion - Pulse Counter » sur la page du plugin.

### 23/10/2026

- **Nouveautés et corrections** : traductions mises à jour.

### 15/10/2026

- **Nouveautés et corrections** : widget par défaut appliqué aux nouvelles commandes ; bouton « Défaut config » pour le réappliquer à toutes les commandes d'un équipement.

### 20/09/2026

- **Sécurité et fiabilité** : suppression d'un ancien fichier de connexion automatique ; la fenêtre « Push-urls » ne propose plus que l'adresse de l'API Jeedom ; pages de recalcul manuel réservées aux administrateurs.

### 19/07/2026

- **Nouveautés et corrections** : Jeedom 4.4 minimum ; nouvelle documentation.

### 18/07/2026

- **Nouveautés et corrections** : nouveau panneau de consommation (sélecteur de période, total énergétique, répartition, carte et graphe par compteur) ; prise en charge des trois fluides avec les bonnes unités (électricité en kWh et W, gaz en kWh et m³, eau en m³) ; intégration à l'analyse énergie de Jeedom ; fenêtre « URL à appeler » de nouveau remplie ; textes traduits en anglais ; bouton « Supprimer tous » fonctionnel.
- **Sécurité et fiabilité** : les actions qui modifient la configuration ou l'historique sont réservées aux administrateurs ; un compteur en erreur ne bloque plus le calcul des autres ; le coefficient de conversion est correctement enregistré ; protection de la correction d'index rétablie.

## Versions précédentes

### 08/10/2022

- **Nouveautés et corrections** : première version du plugin.

[Voir la documentation](./)
