---
layout: default
title: Pulse Counter — Documentation
lang: fr_FR
pluginId: pulseCounter
---

# Description

**Pulse Counter** transforme les impulsions d'un compteur d'**électricité**, de **gaz** ou d'**eau** en données de consommation : index, consommation par heure, jour, semaine et mois, débit et puissance instantanés. Un **panneau de consommation** regroupe tous vos compteurs.

> **Important**
>
> Le plugin ne lit pas directement un capteur : il **écoute une commande Jeedom existante** qui remonte les impulsions (module Zigbee ou Z-Wave, ESP, contact sec compté par un autre plugin, scénario…).

# Prérequis

| Élément | Détail |
|---|---|
| Jeedom | 4.4 ou plus récent |
| Source | une **commande info numérique** qui compte les impulsions du compteur |

Aucun démon ni dépendance : tout est traité par Jeedom (écoute de la commande source et tâches planifiées).

# Installation et configuration

1. Installez le plugin depuis le Market, puis activez-le.
2. Ouvrez **Plugins → Énergie → Pulse Counter** pour créer vos compteurs.

La page de configuration du plugin propose un champ **Surface habitable du logement (m²)** : il n'est pas utilisé pour l'instant.

Les calculs par heure, jour, semaine et mois s'appuient sur les tâches planifiées horaire et quotidienne de Jeedom : le moteur de tâches doit fonctionner.

# Équipements

Sur la page du plugin, cliquez sur **Ajouter**, nommez le compteur, puis renseignez l'onglet **Équipement**.

| Champ | Rôle |
|---|---|
| Type | `Gaz`, `Eau` ou `Electricité` : définit les unités et les commandes créées. **Non modifiable** une fois sauvegardé. |
| Type Comptage | `Total` : la commande source donne le cumul des impulsions (un index qui ne fait qu'augmenter). `Instantané` : chaque mise à jour de la source compte pour une impulsion. |
| Compteur impulsions | la commande source (bouton de sélection). **Obligatoire** pour sauvegarder ; **une seule** commande. |
| Poids par impulsion | quantité représentée par une impulsion, avec son unité (`Litre`, `m³`, `Wh`, `kWh`). Ex. `10` + `Litre` = 10 litres par impulsion. Défaut : 10. |
| Index correcteur | index réel lu sur votre compteur : à la sauvegarde, le plugin calcule les impulsions à ajouter pour s'aligner dessus. |
| Coef de conversion | `Personalisé` (valeur fixe) ou `Commande` (valeur lue dans une commande). Gaz : énergie par m³, ex. 11 pour 1 m³ = 11 kWh (défaut 10,91). Eau et électricité : laisser 1 (défaut). |

Le champ **Type impulsion** n'est pas utilisé pour l'instant. Le champ **Prix unitaire** (paramètres avancés) n'est pas utilisé non plus : le plugin ne calcule pas de coût.

## Paramètres avancés

- **Corriger** : ouvre une fenêtre pour ajuster l'index jusqu'à ce qu'il corresponde au compteur réel. Les impulsions à ajouter sont calculées automatiquement.
- **Renseigner** : saisie manuelle de l'historique sur une plage de dates (volume jour, consommation jour, coefficient de conversion, consommation). La valeur saisie est répartie de façon égale sur chaque jour de la plage et **remplace** l'historique existant de ces jours.
- **Push-urls** : affiche l'adresse de l'API Jeedom permettant de mettre à jour une commande depuis l'extérieur. Elle contient la clé API du plugin : ne la partagez pas.
- **Régénérer** : recrée les commandes manquantes et remet le type générique et l'unité des commandes existantes aux valeurs du plugin. À n'utiliser qu'en cas de besoin.

## Barre de l'équipement et page du plugin

- **Défaut config** : réapplique le widget par défaut sur toutes les commandes de l'équipement.
- **Santé** (page du plugin) : état de chaque compteur (statut, dernière communication, date de création).
- **Supprimer tous** (page du plugin) : supprime tous les compteurs, après confirmation.

# Commandes

Les commandes sont créées automatiquement à la sauvegarde.

| Commande | Compteurs | Contenu |
|---|---|---|
| Impulsions in | tous | impulsions reçues de la source |
| Totale impulsions | tous | impulsions reçues + correction d'index |
| Index_conso | tous | index d'énergie en kWh (eau : dans l'unité du volume, m³) |
| Conso Instantané, Conso Horaire, Conso Jour, Conso Hier | tous | consommation en Wh |
| Conso Semaine, Conso Mois | tous | consommation en kWh |
| Last pulse | tous | date de la dernière impulsion |
| Rafraichir | tous | action : recalcule les index |
| Puissance_inst | électricité, gaz | puissance en W |
| Index_vol | gaz, eau | index de volume en m³ |
| Volume Instantané, Volume Horaire, Volume Jour, Volume Hier | gaz, eau | volume en litres |
| Volume Semaine, Volume Mois | gaz, eau | volume en m³ |
| Dernier debit | gaz, eau | débit (l/h pour le gaz) |
| Coef Conversion Gaz | gaz, eau | coefficient de conversion utilisé |

Types génériques, pour l'analyse énergie de Jeedom :

- électricité : **Index_conso** en « Consommation », **Conso Jour** en « Consommation journalière », **Puissance_inst** en « Puissance » ;
- gaz et eau : pas de type « Consommation » ni « Puissance ».

Un type générique ou une unité modifiés à la main ne sont pas écrasés lors d'une mise à jour du plugin (mais le bouton **Régénérer** les remet par défaut).

# Panneau de consommation

Le plugin ajoute un panneau accessible depuis le menu **Accueil** de Jeedom.

- **Sélecteur de période** : `J`, `S`, `M`, `A` (jour, semaine, mois, année en cours) et `J-1`, `S-1`, `M-1`, `A-1` (période précédente).
- **Total énergétique** en kWh (électricité et gaz) avec sa **répartition**.
- **Une carte par compteur** : consommation de la période (kWh, ou m³ pour l'eau), volume pour le gaz, valeur instantanée (débit et/ou puissance) et **graphe d'historique** dépliable.

L'eau est affichée en m³ et n'entre pas dans le total en kWh.

# FAQ et dépannage

**« Total » ou « Instantané » ?**
`Total` si la commande source est un totalisateur (sa valeur augmente à chaque impulsion). `Instantané` si chaque mise à jour de la source représente une impulsion.

**L'index du plugin ne correspond pas à mon compteur.**
Utilisez **Corriger** (ou **Index correcteur**) avec la valeur lue sur le compteur.

**Erreur « le nouvel index d'impulsions est inférieur à la valeur actuelle ».**
La source a été remise à zéro (changement de module, réinitialisation). Recalez l'index avec **Corriger**.

**Rien ne se calcule, avertissement « ratio d'impulsions non configuré » dans le log.**
Renseignez le **Poids par impulsion** et sauvegardez l'équipement.

**Les consommations d'hier, de la semaine ou du mois ne bougent pas.**
Elles sont calculées par les tâches planifiées de Jeedom : vérifiez que le moteur de tâches fonctionne.

**Puis-je importer un historique ancien ?**
Oui, avec **Renseigner**, sur une plage de dates.

# Support

Forum : [community Jeedom, sujet du plugin](https://community.jeedom.com/t/plugin-pulseCounter-documentation-et-actualites/).

[Voir le changelog](./changelog)
