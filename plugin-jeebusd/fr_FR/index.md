---
layout: default
title: Plugin Ebusd
lang: fr_FR
pluginId: jeebusd
---

# Description

Le plugin **Ebusd** permet de piloter dans Jeedom les équipements de chauffage reliés au bus eBUS (chaudières, régulateurs…).

Il s'appuie sur le logiciel `ebusd` et sait découvrir automatiquement les équipements présents sur le bus.

# Prérequis

- Un adaptateur eBUS dédié (eBUS coupler, eBUS adapter, adaptateur Shield/ESP32…).
- Cet adaptateur peut être :
  - branché en USB ou en série sur la box ;
  - branché sur les GPIO d'un Raspberry Pi (module `ttyebus`) ;
  - accessible sur le réseau ;
  - un adaptateur avec micro-ebusd embarqué (API HTTP).

# Installation

1. Installez et activez le plugin.
2. Lancez l'installation des dépendances. Elle installe le paquet `ebusd`.
3. Choisissez l'emplacement de l'adaptateur dans la configuration du plugin, puis sauvegardez.
4. Démarrez le démon.

La version d'ebusd installée s'affiche en haut de la page de configuration (« Non installé » si le paquet est absent).

En mode **micro-ebusd**, aucun démon ebusd local n'est utilisé : Jeedom interroge directement l'adaptateur.

# Configuration du plugin

## Général

- **Emplacement gateway eBus** : comment Jeedom accède au bus.
  - *Réseau* : démon ebusd local et adaptateur distant.
  - *micro-ebusd (API HTTP)* : API embarquée sur l'adaptateur.
  - un port série ou USB local, `ttyebus`, *Auto* ou *Perso*.
- **Adresse du gateway** (mode Réseau) : adresse IP et port de l'adaptateur.
- **Adresse micro-ebusd** (mode micro-ebusd) : adresse IP et port de l'API HTTP. Le bouton **Scanner micro-ebusd** crée les équipements.
- **Emplacement Config** : dossier des définitions eBUS. *Auto* utilise la configuration officielle ebusd en ligne.
- **Mode avancé (ens)** : protocole « enhanced », requis pour les adaptateurs récents dans ce mode.
- **Latence du gateway (ms)** : 0 pour un adaptateur USB local, 10 pour un adaptateur réseau.

## Démon ebusd

- **Port du Serveur HTTP** (défaut 8083) et **Port du Serveur TCP** (défaut 8888).
- **Intervalle de scrutation** (secondes, défaut 30).
- **Timeout de réception (ms)** (défaut 1000).
- **Lecture seule** : le démon n'écrit jamais sur le bus. Aucune commande de contrôle possible.
- **Ignorer le test du périphérique** : ne vérifie pas que le port est bien un adaptateur eBUS.
- **Niveau de log du démon** : zone (Main, Network, Bus, Update, All) et niveau (Error, Notice, Info, Debug). Option **Log données brutes**.

## MQTT

Optionnel : le démon ebusd publie aussi les valeurs vers un broker MQTT externe.

- **Activer Mqtt**, **Hôte Mqtt**, **Port broker** (défaut 1883).
- **Utilisateur Mqtt** et **Mot de passe Mqtt** (stocké chiffré).

## Actions avancées

- **Backups** : liste et restaure les sauvegardes.
- **Configs modules** : retélécharge les définitions eBUS officielles.
- **Synchroniser** : scanne le bus et crée ou met à jour les équipements (modes Réseau et série).
- **ttyebus** : installe le module pour un adaptateur branché sur les GPIO d'un Raspberry Pi.

# Équipements

Les équipements sont créés automatiquement :

- par le bouton **Synchroniser** (démon démarré et bus détecté) ;
- ou par **Scanner micro-ebusd** en mode micro-ebusd.

Chaque circuit eBUS détecté devient un équipement.

La page d'accueil du plugin propose aussi : **Configuration**, **Santé**, **Supprimer tout**, et les outils **Testeur**, **Scan**, **Decode** et **Sys**.

Sur la page d'un équipement :

- paramètres Jeedom habituels : nom, objet parent, catégorie, activer, visible ;
- **Identifiant**, **Scan Id** et **Home** : informations en lecture seule issues du scan ;
- **Auto-actualisation (cron)** : fréquence de lecture des valeurs, de 1 minute à 1 fois par semaine (10 minutes par défaut) ;
- dates de **Création** et de dernière communication ;
- boutons **Défaut config**, **Import JSON** et **Exporter des commandes…**.

# Commandes

- **Commandes info** : les valeurs lues sur le bus (températures, états, identification de l'appareil…). Numériques ou texte selon la donnée.
- **Commandes action** : écriture d'une valeur sur le bus (curseur, liste ou valeur fixe), sauf en mode Lecture seule.
- **Rafraîchir** : relit les valeurs de l'équipement.

Les commandes utilisent par défaut les widgets du pack « iso » lorsqu'il est installé.

# FAQ / Dépannage

**La synchronisation échoue avec « démon non démarré ».**
Démarrez le démon depuis la page de configuration et vérifiez l'emplacement de l'adaptateur.

**Message « Signal ebus not acquired ».**
Le démon tourne mais ne reçoit rien du bus. Vérifiez le câblage, l'adresse du gateway, le mode avancé (ens) et la latence.

**Aucune commande action ne fonctionne.**
Vérifiez que l'option **Lecture seule** est décochée.

**Où trouver plus de détails ?**
Activez le niveau Debug dans les logs Jeedom (log `jeebusd`) et, si besoin, le niveau de log du démon ebusd.
