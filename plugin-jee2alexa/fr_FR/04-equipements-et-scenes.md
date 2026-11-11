# Équipements et scènes

Le plugin n'expose **que ce que vous cochez**. Ouvrez la tuile **Équipements SmartHome** pour choisir les équipements, la tuile **Scènes SmartHome** pour les scènes.

## Choisir les équipements

![Liste des équipements, par pièce](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/04-equipements-liste.jpg)

La fenêtre liste tous vos équipements Jeedom **groupés par pièce** (le compteur `0 / 21` indique combien sont cochés sur combien).

| Élément | Rôle |
|---|---|
| Case à cocher d'un équipement | Expose l'équipement à Alexa |
| Cases de la pièce (✓✓ / ▬) | Cocher ou décocher **toute la pièce** |
| Barre de recherche, ✖ | Filtrer par nom, effacer le filtre |
| ✓✓ / ▬ / 📂 / 📁 (barre d'outils) | Tout cocher, tout décocher, tout ouvrir, tout fermer |
| **Nom affiché dans Alexa** | Nom que dira Alexa. **Laissé vide = nom de l'équipement Jeedom** |
| **Catégorie** (liste « Auto ») | Type d'appareil affiché dans l'app Alexa (voir plus bas) |
| Pastilles de capacités | Ce que le plugin a reconnu sur les commandes (allumage, luminosité, thermostat…) |
| **Sauvegarder** | Enregistre le choix (le bouton n'est pas automatique) |
| **Découvrir via Alexa** | Demande à Alexa de relire la liste |

Les équipements des **plugins Alexa** (`alexaapi`, `alexaapiv2`, `alexaamazonmusic`…) ne sont jamais proposés, pour éviter des doublons avec vos vrais appareils.

### « Non pris en charge »

![Équipements sans type générique](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/05-equipements-sans-piece.jpg)

L'étiquette **Non pris en charge** apparaît quand **aucune commande** de l'équipement n'a un **type générique** reconnu par le plugin (liste ci-dessous). La case est alors inutilisable : l'équipement n'aurait aucune capacité à annoncer à Alexa.

Pour y remédier, ouvrez l'équipement dans Jeedom, onglet **Commandes**, et attribuez le bon **type générique** à chaque commande (par exemple *Lumière — Bouton ON*, *Lumière — État*, *Lumière — Slider*).

### Plusieurs sorties sur un même équipement

Un équipement qui porte **plusieurs sorties indépendantes** (par exemple plusieurs relais) n'est exposé qu'avec **une seule** à Alexa. Le plugin le détecte et propose de **découper en équipements virtuels** : un équipement virtuel par sortie, chacun avec son nom.

### Noms en double

Si deux équipements portent le même nom, Alexa ne saurait pas les distinguer : le plugin ajoute un suffixe `(2)`, `(3)`… au nom annoncé et l'écrit dans le journal en `warning`. Donnez de préférence un **nom distinct** dans la colonne « Nom affiché dans Alexa ».

## Types génériques pris en charge

Le plugin lit le **type générique** des commandes de chaque équipement, jamais leur nom. Une commande *action* (bouton) et une commande *info* (état) se complètent : l'état permet à Alexa d'afficher la valeur réelle (« retrievable »).

### Allumage, lumière

| Commandes (type générique) | Interface Alexa | Exemples de phrases |
|---|---|---|
| `ENERGY_ON`, `ENERGY_OFF`, `ENERGY_STATE` ou `LIGHT_ON`, `LIGHT_OFF`, `LIGHT_STATE` / `LIGHT_STATE_BOOL` | Marche/arrêt (`PowerController`) | « Alexa, allume la prise ». `LIGHT_STATE_BOOL` prime sur `LIGHT_STATE` |
| `LIGHT_TOGGLE` + un état (`LIGHT_STATE`…) | Marche/arrêt | Lumière à bascule seule : le plugin ne bascule que si l'état lu diffère de la demande. Sans état lisible, non exposée |
| `LIGHT_SLIDER` / `ENERGY_SLIDER` (marche/arrêt implicite), `LIGHT_SET_BRIGHTNESS`, `LIGHT_BRIGHTNESS` | Luminosité (`BrightnessController`) | « Alexa, mets le salon à 40 % » ; `ENERGY_SLIDER` = prise variable |
| `LIGHT_SET_COLOR`, `LIGHT_COLOR` | Couleur (`ColorController`) | « Alexa, mets la lumière en rouge » |
| `LIGHT_SET_COLOR_TEMP`, `LIGHT_COLOR_TEMP` | Température de couleur (`ColorTemperatureController`) | « Alexa, lumière blanc chaud » |

### Chauffage, climatisation, humidité

| Commandes | Interface Alexa | Remarques |
|---|---|---|
| `THERMOSTAT_SET_SETPOINT`, `THERMOSTAT_SETPOINT`, `THERMOSTAT_MODE`, `THERMOSTAT_SET_MODE` | Thermostat (`ThermostatController`) | Alexa n'accepte que `AUTO`, `HEAT`, `COOL`, `ECO`, `OFF` : les libellés Jeedom courants (*confort*, *chauffage*, *clim*, *planning*, *hors-gel*, *nuit*, *arrêt*…) sont traduits ; un libellé inconnu est **omis** plutôt qu'envoyé tel quel |
| `AC_ON`, `AC_OFF`, `AC_STATE`, `AC_SET_TEMPERATURE`, `AC_SET_MODE`, `AC_MODE`, `AC_TEMPERATURE`, `AC_INDOOR_TEMPERATURE` | Même interfaces que le thermostat + capteur de température + marche/arrêt | Famille « climatisation » distincte de `THERMOSTAT_*`. Ces types sont ajoutés par le plugin **JeedomConnect**, pas par le core : ils ne sont proposés que si JeedomConnect est actif |
| `AC_SET_FAN_MODE` (+ `AC_FAN_MODE`) | Mode de ventilation (`ModeController`, `HVAC.FanMode`) | Modes lus depuis la **liste de valeurs** de la commande de type *select* (types JeedomConnect, voir ligne précédente) |
| `HEATING_ON`, `HEATING_OFF`, `HEATING_STATE` | Marche/arrêt | Fil pilote simple |
| `HEATING_OTHER` (commande *select*) | Modes du fil pilote (`ModeController`, `PilotWire.Mode`) | Modes lus depuis la liste de valeurs (Confort, Éco, Hors-gel, Arrêt…), jamais devinés |
| `HUMIDITY_SET_SETPOINT`, `HUMIDITY_SETPOINT` | Consigne d'humidité 0-100 % (`RangeController`) | Humidificateur |
| `THERMOSTAT_TEMPERATURE`, `THERMOSTAT_HUMIDITY`, `WEATHER_TEMPERATURE`, `WEATHER_HUMIDITY` | Capteurs température / humidité | Repli si aucune commande `TEMPERATURE` / `HUMIDITY` n'existe. La météo n'est pas reprise sur un compteur (température extérieure d'un Gazpar, par exemple) |

### Ouvertures, volets, serrures

| Commandes | Interface Alexa | Remarques |
|---|---|---|
| `FLAP_SLIDER`, `FLAP_STATE` | Position 0-100 % (`RangeController`, `Blind.Lift`) | « Ouvre / ferme / monte / descends le volet », ou une valeur. Catégorie par défaut : volet extérieur |
| `LOCK_OPEN`, `LOCK_CLOSE`, `LOCK_STATE` | Serrure (`LockController`) | « Alexa, verrouille la porte » |
| `GB_OPEN`, `GB_CLOSE`, `GARAGE_STATE` | Porte de garage (`ModeController`, `GarageDoor.Position`) | Ouvert / fermé |
| `OPENING`, `OPENING_WINDOW`, `DOOR` | Capteur d'ouverture (`ContactSensor`) | Lecture seule |

### Capteurs

| Commandes | Interface Alexa |
|---|---|
| `PRESENCE` (ou `MOTION`) | Capteur de mouvement (`MotionSensor`) |
| `TEMPERATURE` | Capteur de température (`TemperatureSensor`) |
| `HUMIDITY` | Capteur d'humidité (`HumiditySensor`) |
| `ONLINE` | Alimente la connectivité de l'appareil (« ne répond pas ») |
| `CO2`, `AIR_QUALITY` (numérique), `BRIGHTNESS` (lux), `WEATHER_PRESSURE` / `PRESSURE` | Capteurs en lecture seule : « CO2 » (ou « Dioxyde de carbone »), « Qualité de l'air », « Luminosité », « Pression » (`RangeController`). Avec du CO2 ou une qualité d'air, l'appareil est classé « moniteur de qualité de l'air ». La pression météo (niveau de la mer) prime sur la pression absolue |
| `POWER`, `CONSUMPTION`, `DAILY_CONSUMPTION`, `PRODUCTION`, `DAILY_PRODUCTION` | Compteur en lecture seule : réglages « Puissance », « Consommation », « Consommation du jour », « Production », « Production du jour » (`RangeController`) |

Tout équipement annonce aussi `EndpointHealth` : Alexa le signale « Ne répond pas » s'il est **désactivé dans Jeedom** (ou, si une commande `ONLINE` existe, hors ligne).

**Capteurs sans équivalent Alexa** (CO2, qualité de l'air, luminosité, pression) : même principe que le
compteur ci-dessous, sans unité dite. Exemple vérifié : « Alexa, quel est le CO2 du capteur cuisine ? » →
« Le paramètre CO2 de l'appareil capteur cuisine est 1421 ».

**Compteur électrique ou de gaz** : « Alexa, quelle est la puissance du compteur séjour ? » → « Le paramètre
Puissance de l'appareil compteur séjour est 20 ». Valeurs ramenées en **W** (puissance) et **kWh**
(consommation) quelle que soit l'unité de la commande (kW, Wh…). Alexa **ne dit pas l'unité** : sa liste
d'unités n'a ni watt ni kWh, et l'interface « énergie » d'Amazon n'existe qu'en anglais américain, sans voix.
Alexa répond depuis le dernier état envoyé : le [push proactif](#push-proactif-états-à-jour-sonnette) doit être actif, et la
valeur est envoyée au plus toutes les 10 minutes (tout de suite si la puissance varie de 200 W ou plus).

### Ventilation, aspirateur, multimédia, sécurité

| Commandes | Interface Alexa | Remarques |
|---|---|---|
| `FAN_SPEED`, `FAN_SPEED_STATE` | Vitesse (`RangeController`) | La plage reprend `minValue` / `maxValue` de la commande |
| `ROTATION`, `ROTATION_STATE` | Oscillation (`ToggleController`, `Fan.Oscillate`) | Une seule commande action : marche = arrêt |
| `DOCK` (+ `ENERGY_ON`/`OFF`) | Aspirateur (`ActionController`, `Robot.Clean`) | « Lance le nettoyage » ; marche/arrêt via `PowerController` |
| `MEDIA_RESUME`, `MEDIA_PAUSE`, `MEDIA_STOP`, `MEDIA_NEXT`, `MEDIA_PREVIOUS`, `MEDIA_STATE` | Lecture (`PlaybackController`, état de lecture) | |
| `SET_CHANNEL`, `CHANNEL` | Chaîne (`ChannelController`) | Catégorie TV |
| `SET_VOLUME`, `VOLUME`, `MEDIA_MUTE`, `MEDIA_UNMUTE` | Volume et sourdine (`Speaker`) | |
| `CAMERA_URL` | Flux caméra (`CameraStreamController`) | **Flux RTSP/RTSPS public uniquement** — voir [caméras](camera-rtsp-relay.html) |
| `ALARM_ARMED`, `ALARM_RELEASED`, `ALARM_STATE` / `ALARM_MODE` | Alarme (`SecurityPanelController`) | Deux états seulement : `ARMED_AWAY` et `DISARMED` (pas de mode nuit/présence, pas de code PIN) |
| `BUTTON` (Bouton) + catégorie **Sonnette** | Sonnette (`DoorbellEventSource`) | Voir [Push proactif](#sonnette) |

> Un équipement n'est annoncé que s'il a **au moins une** de ces capacités. Plusieurs peuvent se cumuler sur un même équipement (une lampe variable = marche/arrêt + luminosité + couleur).

## Catégorie (icône dans l'app Alexa)

La liste **Catégorie** vaut **Auto** par défaut : le plugin choisit lui-même, dans cet ordre de priorité, d'après les capacités détectées :

`THERMOSTAT` → `SMARTLOCK` → `SECURITY_SYSTEM` → `CAMERA` → `GARAGE_DOOR` → `EXTERIOR_BLIND` → `VACUUM_CLEANER` → `TV` → `SPEAKER` → `LIGHT` → `FAN` → `THERMOSTAT` (fil pilote multi-modes) → `SWITCH` → `CONTACT_SENSOR` → `MOTION_SENSOR` → `TEMPERATURE_SENSOR` (valeur de repli recommandée par Amazon).

Vous pouvez imposer une catégorie dans la liste : `LIGHT`, `SWITCH`, `SMARTPLUG`, `FAN`, `AIR_PURIFIER`, `AIR_FRESHENER`, `AIR_QUALITY_MONITOR`, `THERMOSTAT`, `SMARTLOCK`, `DOOR`, `GARAGE_DOOR`, `INTERIOR_BLIND`, `EXTERIOR_BLIND`, `SPEAKER`, `BLUETOOTH_SPEAKER`, `SCREEN`, `DOORBELL`, `OVEN`, `MICROWAVE`, `COFFEE_MAKER`, `SLOW_COOKER`, `WASHER`, `COMPUTER`, `LAPTOP`, `PRINTER`, `ROUTER`, `NETWORK_HARDWARE`, `HUB`, `VEHICLE`, `WEARABLE`, `OTHER`.

La catégorie **change l'icône et le regroupement** dans l'app Alexa, **pas les capacités**. Une seule exception : `DOORBELL`, qui active l'annonce de sonnette si l'équipement a une commande `BUTTON` (Bouton).

## Scènes

![Ajout d'une scène](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/06-scenes-ajout.jpg)

Une **scène** est exposée à Alexa comme un appareil activable : « Alexa, lance *la scène* ». Deux types, au choix dans la liste à droite du nom :

| Type | Comportement |
|---|---|
| **Scénario existant** | « Activer » lance un scénario Jeedom ; « Désactiver (optionnel) » peut en lancer un autre |
| **Liste d'actions** | Exécute directement une liste de commandes *action* Jeedom, chacune avec une valeur facultative |

Renseignez le **nom vu par Alexa**, la commande d'activation (et éventuellement de désactivation), puis **Sauvegardez** : le badge orange « Modifications non sauvegardées » disparaît. Les scènes sont découvertes comme les équipements : relancez **Découvrir via Alexa** après toute modification.

Sans commande de désactivation, « Alexa, désactive… » répond par une action neutre (aucune erreur).

## Après un changement

Tout ajout, retrait ou renommage d'équipement ou de scène demande une **nouvelle découverte** : **Sauvegarder**, puis **Découvrir via Alexa** (ou « Alexa, découvre les appareils »). Un appareil retiré de la sélection disparaît de l'app Alexa à la découverte suivante.
