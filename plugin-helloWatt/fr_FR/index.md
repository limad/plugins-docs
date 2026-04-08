---
layout: default
title: helloWatt documentation
lang: fr_FR
pluginId: helloWatt
---

# Plugin helloWatt

Plugin permettant la récupération des consommations d'énergie (Gaz et Electricité) par l'interrogation du compte-client **Hello Watt**. Les données n'étant pas mises à disposition en temps réel, le plugin récupère chaque jour les dernières données de consommation disponibles.

> **Important**
>
> Il est nécessaire d'être en possession d'un compte-client Hello Watt. Le plugin récupère les informations à partir de la partie *mes-données* du <a href="https://www.hellowatt.fr/mon-compte/ma-consommation/mes-donnees" target="_blank">site Hello Watt</a>. Vérifiez que vous y avez bien accès avec vos identifiants habituels (email/mot de passe) et que vos données y sont visibles. Dans le cas contraire, le plugin ne fonctionnera pas.

# Prérequis

- Jeedom version **4.4** minimum
- Un compte Hello Watt actif avec au moins un compteur Linky (Enedis) ou Gazpar (GRDF) rattaché

# Configuration du plugin

Sur la page de configuration du plugin (**Plugins > Gestion des plugins > helloWatt**), renseignez :

| Paramètre | Description |
|-----------|-------------|
| **Email** | L'adresse email de votre compte Hello Watt |
| **Mot de passe** | Le mot de passe de votre compte Hello Watt (stocké chiffré en base) |
| **Heure de début de récupération** | Heure à partir de laquelle le plugin commence à récupérer les données (entre 15 et 23, défaut : 15). Les données de comptage remontent généralement à des heures régulières ; ce réglage permet de limiter les requêtes inutiles |
| **Objet par défaut** | Objet Jeedom dans lequel les équipements seront créés automatiquement |

Cliquez sur **Synchroniser** pour vérifier la connexion et créer automatiquement vos équipements.

# Équipements et commandes

Pour accéder aux équipements, dirigez-vous vers **Plugins > Energie > helloWatt**.

Le plugin crée automatiquement un équipement par compteur détecté sur votre compte Hello Watt. Chaque équipement est identifié par son type (`elec` ou `gaz`), l'identifiant du domicile et le numéro de point de comptage (PDL ou PCE).

## Commandes disponibles

### Commandes communes (électricité et gaz)

| Commande | Description | Unité |
|----------|-------------|-------|
| **Conso jour** | Consommation journalière | kWh |
| **Conso mois** | Consommation mensuelle (agrégée) | kWh |
| **Conso annuelle** | Consommation annuelle (agrégée) | kWh |
| **Coût jour** | Coût de la consommation journalière | € |
| **Coût mois** | Coût mensuel (agrégé) | € |
| **Coût annuel** | Coût annuel (agrégé) | € |
| **CO₂ jour** | Émissions CO₂ journalières | kgCO₂ |
| **CO₂ mensuelle** | Émissions CO₂ mensuelles (agrégées) | kgCO₂ |
| **CO₂ temps réel** | Intensité carbone actuelle du réseau | gCO₂/kWh |
| **Température** | Température extérieure journalière | °C |
| **EcoWatt** | Signal EcoWatt du jour | - |
| **EcoWatt demain** | Signal EcoWatt du lendemain | - |
| **Fournisseur** | Nom du fournisseur d'énergie | - |
| **Offre** | Nom de l'offre souscrite | - |
| **Rafraichir** | Action de mise à jour manuelle | - |

### Commandes injection solaire (si contrat d'injection Enedis détecté)

| Commande | Description | Unité |
|----------|-------------|-------|
| **Production jour** | Production injectée journalière | kWh |
| **Production mois** | Production mensuelle (agrégée) | kWh |
| **Production année** | Production annuelle (agrégée) | kWh |
| **Rémunération jour** | Rémunération journalière | € |
| **Rémunération mois** | Rémunération mensuelle (agrégée) | € |
| **Rémunération année** | Rémunération annuelle (agrégée) | € |

### Commandes Tempo (si abonnement Tempo détecté)

| Commande | Description |
|----------|-------------|
| **Tempo jour** | Couleur du jour (bleu, blanc, rouge) |
| **Tempo demain** | Couleur du lendemain |

### Commandes puissance (électricité uniquement)

| Commande | Description | Unité |
|----------|-------------|-------|
| **Puissance max** | Puissance maximale atteinte | kVA |
| **Puissance souscrite** | Puissance souscrite au contrat | kVA |

# Fonctionnement du cron

Le plugin utilise le cron horaire de Jeedom. La récupération des données s'effectue :

- **Entre l'heure configurée et 23h59**, une heure sur deux
- Le plugin vérifie si les dernières données collectées ont plus de **18 heures** avant de relancer une récupération
- Un délai de 500 ms est appliqué entre chaque appel API pour éviter les blocages côté Hello Watt
- En cas d'expiration de session, le plugin se ré-authentifie automatiquement et retente la requête

# Affichage

## Tuiles dashboard

Deux tuiles sont disponibles dans la configuration de l'équipement :

- **Tuile personnalisée (helloWatt)** : affichage compact des consommations avec comparaison mensuelle. Les informations non souhaitées peuvent être masquées via la configuration habituelle des commandes
- **Tuile Core** : tuile standard gérée par le core Jeedom, personnalisable selon vos préférences

## Panneau (Panel)

Le plugin dispose d'un panneau dédié accessible via **Accueil > helloWatt**. Il affiche :

- La tuile de l'équipement sélectionné
- Des graphiques de consommation (journalier, mensuel)
- Des graphiques de comparaison entre périodes

# Recommandations d'usage

- **Lissage historique** : laissez les commandes en mode *Aucun* (configuré automatiquement par le plugin)
- **Requêtes** : évitez d'utiliser le bouton *Rafraichir* de manière excessive. Le cron automatique suffit dans la majorité des cas
- **Logs** : en cas de problème, activez les logs en mode *Debug* dans la configuration du plugin
- **Confidentialité** : ne postez **jamais** vos logs sur un fil public du forum. Les logs peuvent contenir des informations personnelles. Transmettez-les uniquement par **message privé**

# Dépannage

## Vérifications préliminaires

1. Vérifiez vos identifiants (email et mot de passe) sur le <a href="https://www.hellowatt.fr/mon-compte/me-connecter" target="_blank">site Hello Watt</a>
2. Vérifiez que vos données de consommation sont bien visibles dans la section *Mes données* du site
3. Vérifiez que votre mot de passe est conforme aux exigences de Hello Watt (12 caractères minimum, caractères spéciaux)
4. Vérifiez que votre version Jeedom est au minimum 4.1

## En cas d'erreur persistante

1. Activez les logs en mode **Debug** dans la configuration du plugin
2. Effectuez un **Rafraichir** ou sauvegardez l'équipement si les commandes ne sont pas créées
3. Transmettez les logs complets par **MP** au développeur
4. En cas d'erreur HTTP 500, joignez également les logs `http.error` de Jeedom

## Problèmes connus

- Le serveur Hello Watt peut être momentanément inaccessible (maintenance, surcharge). Le plugin retente automatiquement lors du prochain cycle cron
- En l'absence d'API officielle, des changements côté Hello Watt peuvent rendre le plugin temporairement inopérant. Les mises à jour du plugin corrigent ces incompatibilités

# Disclaimer

- Ce plugin utilise une **API non officielle** de Hello Watt. Des changements côté serveur peuvent rendre le plugin temporairement inopérant
- Ce plugin vous est fourni **sans aucune garantie**. L'auteur ne pourrait être tenu pour responsable en cas de dysfonctionnement

# Changelog

Disponible [ici](./changelog.html).
