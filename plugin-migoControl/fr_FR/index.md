---
layout: default
title: Migo Control — Documentation
lang: fr_FR
pluginId: migoControl
---

# Description

Le plugin **Migo Control** intègre dans Jeedom les équipements de chauffage **Migo Energie** : thermostats (dont OpenTherm), vannes thermostatiques et relais de chaudière. Il fonctionne avec le compte cloud de l'application Migo (MiGo Saunier Duval, eRelax / vSmart Vaillant).

Le plugin permet de :

- lire les informations du logement et de chaque pièce : température, consigne, mode, planning en cours, profil suivant, état de la chaudière, batterie, signal radio ;
- changer la consigne d'une pièce, avec ou sans durée ;
- changer le mode du logement (Planning, Absent, Hors-gel) et le planning actif ;
- régler l'eau chaude sanitaire (ECS) et la courbe de chauffe, selon la chaudière ;
- consulter un panneau de suivi avec les durées d'allumage du chauffage.

> **Important**
>
> Les fonctions disponibles dépendent de votre installation. Certaines commandes ne sont créées que si votre matériel les gère (ECS, pression d'eau, courbe de chauffe…).

# Prérequis

| Composant | Exigence |
|---|---|
| Jeedom | 4.4 ou plus récent |
| Compte | un compte Migo actif (e-mail et mot de passe de l'application) |
| Réseau | accès Internet depuis la box Jeedom (le plugin dialogue avec le cloud Migo) |

Aucune dépendance à installer et aucun démon à lancer.

# Installation et configuration du plugin

1. Installez le plugin depuis le Market Jeedom, puis activez-le.
2. Dans la page de configuration du plugin, renseignez :
   - **Email** : l'adresse e-mail de votre compte Migo ;
   - **Mot de passe** : le mot de passe de votre compte Migo (le bouton en forme d'œil l'affiche ou le masque) ;
   - **Type du Thermostat** : **MiGo** ou **eRelax/vSmart**, selon l'application que vous utilisez ;
   - **Objets par défaut** *(facultatif)* : l'objet Jeedom dans lequel les nouveaux équipements seront rangés.
3. Cliquez sur **Synchroniser mes équipements**. La configuration est enregistrée, puis le plugin se connecte à votre compte et crée les équipements. En cas de succès, vous êtes redirigé vers la page des équipements.

Le mot de passe est enregistré chiffré dans Jeedom.

En cas d'échec, un message s'affiche :

- « Vérifier que les informations saisies sont correctes » : e-mail, mot de passe ou type de thermostat erroné ;
- « Vérifier la connectivité internet » : la box n'a pas pu joindre le service Migo.

## Fréquence d'actualisation

Les informations sont relues **toutes les 10 minutes** (option *cron10* activée à l'installation). Vous pouvez passer à **5 minutes** en activant *cron5* dans la section **Fonctionnalités** de la page de configuration du plugin : les deux options sont exclusives, activer l'une désactive l'autre.

Après une action (consigne, mode…), l'équipement est relu automatiquement quelques secondes plus tard.

## Panneau

Si l'affichage du panneau est activé dans la configuration du plugin, le menu **Accueil** donne accès au panneau Migo Control :

- la tuile du logement et un bouton par pièce ;
- les graphiques **Durée d'allumage** (par jour) et **Répartition d'allumage** (par heure), avec navigation par date ;
- une page de statistiques (flèche en haut du panneau).

Les statistiques sont calculées une fois par jour.

# Équipements

## Création

Les équipements sont créés par la synchronisation, jamais à la main :

- **un équipement « logement »** par maison du compte Migo : mode du chauffage, plannings, chaudière, eau chaude ;
- **un équipement par pièce** équipée d'au moins un module (thermostat ou vanne).

Relancez **Synchroniser mes équipements** après tout changement dans l'application Migo (nouvelle pièce, nouveau module, planning renommé…) : les équipements existants sont mis à jour et leurs commandes complétées.

La page du plugin propose aussi :

- **Santé** : tableau par pièce (identifiant, Wifi, signal radio, batterie, statut, dernière communication) ;
- **Supprimer tous** : supprime tous les équipements du plugin, après confirmation.

## Configuration d'un équipement

Onglet **Equipement**, partie **Général** :

- **Nom de la pièce**, **Objet parent**, **Catégorie**, **Activer**, **Visible** : réglages Jeedom habituels.

Partie **Informations** (lecture seule) : identifiant, type de pièce, logement, relais associé, type d'équipement, et la liste des modules de la pièce.

Partie **Configuration durées par défaut (min)**, modifiable sur l'équipement logement :

| Champ | Rôle |
|---|---|
| Mode Manuel | durée d'une consigne manuelle (valeur reprise du compte Migo, 60 min par défaut) |
| Mode absent | durée du mode Absent si aucune fin n'est donnée ; vide = jusqu'à nouvel ordre |
| Mode H.gel | durée du mode Hors-gel si aucune fin n'est donnée ; vide = jusqu'à nouvel ordre |

Partie **Configuration autres...** :

| Option | Rôle |
|---|---|
| Appartient au groupe Boost | *(pièce)* la pièce est concernée par le boost du logement |
| Type de la tuile Dashboard / Mobile | **Default** : tuile du plugin ; **Core** : tuile standard de Jeedom |
| Dupliquer les boutons Mode | *(pièce)* affiche les boutons de mode du logement sur la tuile de la pièce |
| Afficher le planning | *(pièce)* affiche le planning sur la tuile de la pièce |
| Température extérieure | *(logement)* commande info Jeedom à utiliser comme température extérieure, à la place de celle fournie par Migo |

Le bouton **Défaut config** remet les widgets par défaut sur toutes les commandes de l'équipement.

# Commandes principales

Seules les commandes adaptées à votre matériel sont créées. Les noms ci-dessous sont ceux affichés dans l'onglet **Commandes**.

## Équipement logement

| Commande | Type | Description |
|---|---|---|
| ModeTech Home | info | mode du chauffage du logement |
| Planning en cours | info | nom du planning actif |
| Fin Mode en Cours | info | fin du mode en cours |
| Etat Chaudiere | info binaire | chaudière en chauffe |
| Anticipation Chaudiere(Home) | info binaire | anticipation en cours |
| Température extérieure | info (°C) | historisée |
| T° Hors-gel / T° Absence | info (°C) | températures des modes Hors-gel et Absent |
| Signal Wifi | info | qualité du Wifi |
| Pression | info (bar) | pression d'eau, si la chaudière la transmet |
| Algorithme de chauffe | info | chaudière pilotée en eBus |
| Courbe de chauffe | info | si disponible |
| Consigne Ecs / Ecs Active / Ecs-Boost | info | eau chaude sanitaire, si gérée |
| Set-Mode et ou Fin de mode | action | mode **Planning**, **Absent** ou **Hors-gel**, avec fin facultative |
| Changer Planning | action liste | choisit le planning actif |
| Changer Priorité chauffage | action liste | **ECO** ou **CONFORT** |
| Réglage Courbe de chauffe | action curseur | 0 à 5 |
| Réglage Consigne Ecs | action curseur | consigne d'eau chaude |
| Allumer Ecs / Ecs-Boost | action | marche/arrêt de l'ECS et du boost ECS |
| Rafraichir | action | relit le logement |

Dans **Set-Mode et ou Fin de mode**, le champ de fin accepte une durée en minutes ou une date de fin (timestamp). Sans fin, la durée par défaut de l'équipement logement s'applique. Le changement de mode est refusé si le chauffage est éteint.

## Équipement pièce

| Commande | Type | Description |
|---|---|---|
| Température | info (°C) | historisée |
| Consigne | info (°C) | historisée |
| ModeTech | info | mode de la pièce |
| Actif | info binaire | chauffe en cours dans la pièce ; sert aux graphiques du panneau |
| Profile de température / Profile suivant | info | profil du planning en cours et suivant |
| Délai prochain profile | info (min) | temps avant le profil suivant |
| Fin Mode en Cours(Room) | info | fin du mode manuel de la pièce |
| Anticipation en cours(Room) | info binaire | |
| Batterie | info (%) | niveau de pile du module |
| Signal RF(Room) | info (%) | signal radio du module |
| Ouverture vanne | info (%) | vannes uniquement |
| Réglage Consigne | action message | consigne (titre) et durée en minutes (message), 24 h maximum |
| Réglage Consigne Mobile | action curseur | consigne de 7 à 30 °C, pour la durée « Mode Manuel » |
| Set-Mode to Home | action | retour au planning |
| Set-RoomMode | action liste | **Home**, **Hors-gel** ou **Max** |
| Mode Forcé (Max) | action message | chauffe au maximum, durée en minutes facultative |
| Eteindre | action | éteint le chauffage (le rallume s'il est déjà éteint) |
| Réglage offset (Piéce) | action curseur | correction de la mesure, -10 à +10 °C |
| True Temperature | action message | indique la température réelle mesurée dans la pièce |
| Rafraichir | action | relit la pièce |

Les commandes du thermostat ont un type générique Jeedom : elles sont reconnues par l'application mobile et les ponts vers les assistants vocaux.

# FAQ et dépannage

**La synchronisation échoue.**
Vérifiez l'e-mail, le mot de passe et le **Type du Thermostat** (MiGo ou eRelax/vSmart) : ils doivent correspondre à l'application que vous utilisez. Vérifiez ensuite que la box accède à Internet.

**Une pièce n'apparaît pas.**
Seules les pièces avec au moins un module sont créées. Ajoutez le module dans l'application Migo, puis relancez la synchronisation.

**Un équipement est signalé « semble invalide ».**
La pièce n'est plus renvoyée par votre compte Migo (pièce supprimée ou module retiré). Désactivez ou supprimez l'équipement.

**Une commande attendue est absente.**
Elle n'est créée que si votre matériel la gère. Après un changement d'installation, relancez la synchronisation.

**Les valeurs ne bougent pas.**
Attendez le prochain rafraîchissement (5 ou 10 minutes) ou utilisez **Rafraichir**. Sinon, consultez le log `migoControl` (niveau Debug) dans la configuration du plugin.

**Que fait l'option « na_Live_update » ?**
Cette option est expérimentale et n'est pas active dans cette version : laissez-la décochée.

---

*Migo Control — Documentation | Plugin pour Jeedom*
