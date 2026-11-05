---
layout: default
title: Jee2Matter — Documentation
lang: fr_FR
pluginId: jee2matter
---

# Jee2Matter

Jee2Matter expose vos équipements Jeedom à **Alexa**, **Apple Maison** et **Google Home** par le protocole
**Matter**, en local : pas de cloud, pas de Docker, pas de MQTT, aucun autre plugin nécessaire.

Le plugin crée un ou plusieurs **hubs** (ponts Matter). Chaque hub a son nom, sa liste d'équipements et
ses propres contrôleurs. Vous l'ajoutez dans l'application Alexa (ou Apple Maison, Google Home) comme
n'importe quel appareil Matter, et les équipements cochés apparaissent.

## Prérequis

- Jeedom 4.4 ou plus récent.
- **IPv6 activé** (au moins une adresse « lien local » `fe80::`) sur l'interface réseau de la box :
  Matter ne fonctionne pas sans. La page Santé du plugin le vérifie.
- Le téléphone qui fait l'appairage et l'enceinte (Echo, HomePod, Nest...) sur le même réseau local que Jeedom.
- Des **types génériques** corrects sur les commandes des équipements à exposer (voir plus bas).

## Installation

1. Installez le plugin, activez-le, puis lancez les **dépendances** (Node.js 20.19 ou plus et matter.js,
   environ 150 Mo).
2. Démarrez le **démon**. Un premier hub nommé « JeeHub » est créé automatiquement.

### Configuration du plugin

| Paramètre | Rôle |
|---|---|
| Interface réseau | Interface sur laquelle les hubs s'annoncent. Vide = celle de l'adresse interne de Jeedom. |
| Port Matter de départ | Port UDP du premier hub (5550 par défaut) ; les hubs suivants prennent les ports libres suivants. |
| Port interne du démon | Port local entre Jeedom et le démon (55095 par défaut). |

## Créer et remplir un hub

Sur la page du plugin, ouvrez un hub (ou **Ajouter un hub**) :

- Onglet **Équipements exposés** : cochez les équipements à exposer, puis **Sauvegarder**. Un même
  équipement peut être exposé par plusieurs hubs.
- Onglet **Hub** : nom vu par les contrôleurs (32 caractères au plus), activation, port.

Un seul hub suffit si tous vos contrôleurs doivent voir les mêmes équipements. Créez un autre hub pour
montrer une liste différente à un contrôleur (par exemple un second compte Alexa).

> Désactiver un hub l'arrête sans perdre l'appairage. Le **supprimer** efface son appairage : ses
> contrôleurs perdent ses équipements.

### Équipements reconnus

| Type Matter | Types génériques nécessaires |
|---|---|
| Lumière | `LIGHT_ON` + `LIGHT_OFF` (état : `LIGHT_STATE` ou `LIGHT_STATE_BOOL`) |
| Lumière variable | les mêmes + `LIGHT_SLIDER` (luminosité : `LIGHT_BRIGHTNESS`) |
| Prise | `ENERGY_ON` + `ENERGY_OFF` (état : `ENERGY_STATE`) |
| Volet | `FLAP_SLIDER`, ou `FLAP_UP` + `FLAP_DOWN` (état : `FLAP_STATE`, arrêt : `FLAP_STOP`) |
| Ouverture (porte, fenêtre) | `OPENING`, `OPENING_WINDOW` ou `DOOR` |
| Présence | `PRESENCE` ou `MOTION` |
| Température | `TEMPERATURE` |
| Humidité | `HUMIDITY` |
| Thermostat | `THERMOSTAT_SET_SETPOINT` (consigne : `THERMOSTAT_SETPOINT`, température : `THERMOSTAT_TEMPERATURE`, mode : `THERMOSTAT_MODE` + `THERMOSTAT_SET_MODE`) |
| Climatisation | `AC_SET_TEMPERATURE` (et `AC_ON`, `AC_OFF`, `AC_STATE`, `AC_MODE`, `AC_SET_MODE`, `AC_TEMPERATURE`) |

Une commande info de type générique `ONLINE` (« Connecté ») est suivie : à 0, l'équipement apparaît
**hors ligne** dans les applications. Un état qui vaut « Hors ligne » a le même effet.

### Thermostat et climatisation

La consigne se règle à la voix (« Alexa, mets Vesta à 21 degrés ») dans les bornes du curseur de
consigne (7 à 30 °C par défaut, 16 à 30 °C pour une clim). Les modes Matter sont **arrêt**, **chauffage**
et, pour une clim, **froid** (« Alexa, règle Vesta sur off », « … sur chauffage »). Ils sont reliés aux
commandes de mode de l'équipement d'après leur nom ou leurs valeurs : Off/Arrêt → arrêt ;
Confort/Chauffage, sinon Auto/Programme, sinon Eco/Hors-gel → chauffage ; Clim/Froid → froid.
Un mode Eco ou Hors-gel actif apparaît comme « chauffage ».

> Un équipement qui a une commande de consigne est exposé comme thermostat, même s'il a aussi une
> température : il devient pilotable depuis les applications.

### Inverser une valeur

Le plugin considère qu'une ouverture vaut **1 quand elle est ouverte** et qu'un volet vaut **100 quand il
est ouvert**. Si votre capteur ou votre volet fait l'inverse, cochez **Inverser** sur sa ligne puis
Sauvegarder. Ce réglage vaut pour tous les hubs. Les commandes Monter et Descendre gardent leur sens.

## Appairer un hub

Bouton **Appairage** sur la page du hub :

1. Application Alexa : Appareils, **+**, Ajouter un appareil, **Matter**.
2. Répondez Oui au logo Matter et scannez le QR code (ou saisissez le code manuel).
3. Alexa indique « appareil non certifié » : c'est normal, continuez.

La fenêtre passe sur « Hub appairé » dès que c'est fait.

### Ajouter un autre contrôleur (Google Home, Apple Maison, second compte Alexa)

Une fois le hub appairé, **Ajouter un contrôleur** ouvre une fenêtre de 15 minutes avec un code
temporaire à usage unique. Les contrôleurs déjà appairés sont conservés.

**Réinitialiser l'appairage** fait oublier tous les contrôleurs du hub (les autres hubs ne sont pas touchés).

## Santé

Le bouton **Santé** affiche :

- les vérifications (Node.js, matter.js, démon, interface, IPv6, chaque hub et ses contrôleurs) ;
- pour chaque équipement exposé, l'état dans Jeedom et la valeur vue par Matter, avec une éventuelle
  erreur (équipement refusé par matter.js), l'indication « hors ligne » ou « inversé » ;
- l'accès aux logs et le redémarrage du démon.

Les changements faits sur un équipement exposé (nom, désactivation, suppression, types génériques) sont
repris automatiquement dans les 5 minutes ; sauvegarder un hub les applique tout de suite.

## Bon à savoir

- **Alexa ignore les mots de type** (« lampe », « volet », « prise ») : deux équipements dont les noms ne
  diffèrent que par ce mot sont confondus. Donnez-leur des noms distincts.
- Le nom du hub ne doit pas contenir « Jeedom », pour ne pas être confondu avec une intégration officielle.
- Le code d'appairage n'est jamais écrit dans les logs : il n'est visible que dans la fenêtre Appairage.
- Le port Matter d'un hub ne doit pas être utilisé par un autre pont (Matterbridge utilise souvent 5540).
