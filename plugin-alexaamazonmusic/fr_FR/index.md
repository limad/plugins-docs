---
layout: default
title: Alexa Amazon Music
lang: fr_FR
pluginId: alexaamazonmusic
---

# Alexa Amazon Music

## Présentation

Le plugin **Alexa Amazon Music** permet de contrôler la lecture d'Amazon Music sur vos appareils Alexa directement depuis Jeedom. Il s'appuie sur le plugin **alexaapiv2** (Alexa Premium) dont il est dépendant, et ne nécessite aucun daemon propre.

Grâce à ce plugin, vous pouvez :

- Lire, mettre en pause, arrêter, passer à la piste suivante ou précédente
- Contrôler le volume de chaque appareil Alexa
- Lancer une playlist Amazon Music ou une station TuneIn
- Jouer un titre spécifique par son nom
- Transférer la lecture vers un autre appareil Alexa (Cast)
- Gérer les modes répétition et lecture aléatoire
- Régler l'égaliseur (basses, médiums, aigus)
- Créer des groupes multi-pièces (WHA — Whole Home Audio)

---

## Prérequis

Avant d'installer le plugin Alexa Amazon Music, les conditions suivantes doivent être réunies :

1. **Plugin alexaapiv2** installé, activé et correctement configuré sur votre Jeedom.
2. Un **cookie Alexa valide** renseigné dans la configuration du plugin alexaapiv2.
3. Le **daemon alexaapiv2** doit être en cours d'exécution (statut « OK » dans la page du plugin).
4. Vos appareils Alexa doivent être associés au même compte Amazon que celui utilisé pour le cookie.

> **Remarque :** Le plugin alexaamazonmusic ne dispose pas de son propre daemon. Toutes les communications avec les serveurs Amazon passent par alexaapiv2.

---

## Installation

1. Depuis le **Market Jeedom**, recherchez « Alexa Amazon Music » et installez le plugin.
2. Activez le plugin depuis **Plugins > Gestion des plugins**.
3. Vérifiez que le plugin **alexaapiv2** est bien actif et que son daemon fonctionne.

---

## Configuration générale

Accédez à **Plugins > Multimédia > Alexa Amazon Music**, puis cliquez sur la roue dentée (Configuration).

| Paramètre | Description |
|---|---|
| Liste des playlists | Inventaire de vos playlists Amazon Music, rafraîchi automatiquement toutes les ~17 minutes. |
| Rafraîchir les playlists | Bouton permettant de forcer la mise à jour immédiate de la liste des playlists. |

---

## Équipements

### Scan automatique

Les équipements sont créés automatiquement via le **scan** proposé dans le plugin alexaapiv2. Tous les appareils Alexa compatibles (Echo, Echo Dot, Echo Show, Fire TV…) sont importés.

Les groupes **WHA (Whole Home Audio)** sont également pris en charge et apparaissent comme des équipements distincts permettant de diffuser du son sur plusieurs appareils simultanément.

### Paramètres d'un équipement

| Paramètre | Description |
|---|---|
| Nom | Nom de l'appareil tel qu'il apparaît dans Jeedom |
| Objet parent | Objet Jeedom auquel l'équipement est rattaché |
| Activé | Active ou désactive l'équipement |
| Visible | Rend l'équipement visible sur le dashboard |

---

## Commandes

### Commandes action

| Commande | Type | Description |
|---|---|---|
| `textCommand` | Action / Message | Envoie n'importe quelle commande textuelle à Alexa (ex. : « joue du jazz ») |
| `volume` | Action / Curseur (0–100) | Règle le volume de l'appareil |
| `command` | Action / Liste | Contrôles média : `play`, `pause`, `stop`, `next`, `previous`, `forward`, `rewind`, `repeat` (on/off), `shuffle` (on/off) |
| `playlist` | Action / Liste | Lance une playlist Amazon Music choisie dans la liste scannée |
| `playmusictrack` | Action / Message | Lance un titre Amazon Music par son nom ou son identifiant |
| `radio` | Action / Message | Lance une station TuneIn par son nom |
| `castMediaSession` | Action / Message | Transfère la lecture en cours vers un autre appareil Alexa |
| `seekPlayer` | Action / Curseur (0–100) | Se déplace dans la piste en cours (position en pourcentage) |
| `eqBass` | Action / Curseur (-6 à +6) | Règle le niveau des basses de l'égaliseur |
| `eqMid` | Action / Curseur (-6 à +6) | Règle le niveau des médiums de l'égaliseur |
| `eqTreble` | Action / Curseur (-6 à +6) | Règle le niveau des aigus de l'égaliseur |

### Commandes info

| Commande | Type | Description |
|---|---|---|
| `volumeinfo` | Info / Numérique | Volume actuel de l'appareil |
| `repeat` | Info / Binaire | État du mode répétition (1 = activé, 0 = désactivé) |
| `shuffle` | Info / Binaire | État de la lecture aléatoire (1 = activée, 0 = désactivée) |
| `eqBassInfo` | Info / Numérique | Valeur actuelle des basses de l'égaliseur |
| `eqMidInfo` | Info / Numérique | Valeur actuelle des médiums de l'égaliseur |
| `eqTrebleInfo` | Info / Numérique | Valeur actuelle des aigus de l'égaliseur |
| `mediaLength` | Info / Numérique | Durée totale du titre en cours (en secondes) |

---

## Exemples d'utilisation

### Lancer une playlist le matin

Dans un scénario Jeedom déclenché à 7h00 :

1. Commande `volume` → valeur `30`
2. Commande `playlist` → sélectionnez « Ma playlist matin »

### Mettre en pause lors d'une alarme

Créez un scénario déclenché par votre alarme Jeedom :

1. Commande `command` → valeur `pause`

### Diffuser sur tout le domicile (WHA)

1. Sélectionnez l'équipement correspondant à votre groupe WHA.
2. Commande `playlist` → choisissez la playlist souhaitée.
3. Commande `volume` → ajustez le volume global.

### Jouer un titre spécifique

Commande `playmusictrack` → saisissez le nom du titre, par exemple : `Bohemian Rhapsody Queen`.

---

## Dépannage

| Problème | Solution |
|---|---|
| Les équipements n'apparaissent pas après le scan | Vérifiez que le daemon alexaapiv2 est actif et que le cookie est valide. |
| La playlist ne se lance pas | Rafraîchissez manuellement la liste des playlists depuis la configuration du plugin. |
| Le volume ne se met pas à jour | Attendez le prochain cycle de rafraîchissement ou vérifiez la connexion réseau de l'appareil Alexa. |
| Erreur « cookie expiré » | Renouvelez le cookie dans la configuration du plugin alexaapiv2. |
| L'égaliseur n'est pas disponible | Certains modèles d'appareils Alexa ne prennent pas en charge l'égaliseur (ex. : Echo Dot 2e génération). |

---

> Pour tout signalement de bug ou demande de fonctionnalité, rendez-vous sur la page du plugin dans le Market Jeedom.
