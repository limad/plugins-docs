---
layout: default
title: Documentation du plugin Proteo
lang: fr_FR
pluginId: proteo
---

# Description

Le plugin **proteo** permet de suivre une station de traitement d'eau **Proteo V2** depuis Jeedom.

Il lit l'état de la station sur le réseau local : alertes, défauts, débits, usure des filtres et de la lampe UV, consommations et réglages.

Le plugin ne nécessite pas de démon.

# Prérequis

- Jeedom 4.0.1 ou plus récent.
- Une station Proteo V2 connectée au réseau local, avec une adresse IP fixe.
- La box Jeedom doit pouvoir joindre la station (port 23 par défaut).

# Configuration du plugin

Dans **Plugins > Gestion des plugins > proteo** :

- **Adresse IP Proteo** : adresse IP de la station.
- **Port** : port de la station (23 par défaut).
- **Synchroniser...** : enregistre la configuration puis interroge la station. En cas de succès, actualisez la page (F5).

Les deux champs doivent être remplis avant de synchroniser.

# Équipements

Ouvrez **Plugins > Monitoring > proteo**.

La page propose les boutons **Ajouter**, **Configuration**, **Supprimer tous** et **Santé**.

Pour créer un équipement, cliquez sur **Ajouter** et donnez-lui un nom. Puis renseignez :

- **Nom de l'équipement proteo**.
- **Objet parent**.
- **Catégorie**.
- **Activer** et **Visible**.
- **Adresse Ip** : adresse IP de la station.
- **Port** : port de la station.

À la sauvegarde, le plugin contacte la station et récupère son numéro de série et son modèle. Les commandes sont alors créées.

Le bouton **Défaut config** remet les widgets par défaut sur toutes les commandes de l'équipement.

La fenêtre **Santé** liste les équipements avec leur statut et leurs dates de dernière communication.

Les données sont mises à jour automatiquement toutes les minutes.

# Commandes

**Commandes info**

- Nombre Alertes
- Nombre defauts
- Etat lampe_uvc
- status_mode
- Débit instantané
- Débit moyen
- Débit max
- Usure prefiltre
- Usure filtre_1
- Usure filtre_2
- Usure lamp_uvc
- consumption_day, consumption_week, consumption_month, consumption_year (consommations jour, semaine, mois, année)
- config_lang (langue de la station)
- Dureté de l'eau
- config_prefilt
- Type d'eau à traiter (eau du réseau ou autre source)
- Infos Tech
- Time Proteo (heure de la station)
- Horaire uvc

**Commandes action**

- Rafraichir : relit immédiatement les données de la station.
- Synchroniser date et heure.

# FAQ / Dépannage

**Aucune commande n'est créée.**
Vérifiez l'adresse IP saisie dans l'équipement. Elle doit être une adresse IP valide. Sauvegardez à nouveau l'équipement.

**Message « pas eu de mise à jour des données depuis... ».**
La station n'a pas répondu depuis plus de 15 minutes. Vérifiez qu'elle est allumée et joignable depuis Jeedom.

**La synchronisation échoue.**
Vérifiez l'adresse IP et le port dans la configuration du plugin. Consultez le log `proteo`.

**Où trouver les détails d'une erreur ?**
Dans **Analyse > Logs**, log `proteo`. Passez le niveau de log en Debug pour plus de détails.
