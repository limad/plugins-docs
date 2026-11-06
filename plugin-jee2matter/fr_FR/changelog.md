---
layout: default
title: Jee2Matter — Changelog
lang: fr_FR
pluginId: jee2matter
---

# Changelog

## Version en développement

- **Lumières couleur et blanc réglable** (`LIGHT_SET_COLOR` couleur, `LIGHT_SET_COLOR_TEMP` en Kelvin, mireds ou %) : « Alexa, mets Lyra en rouge », « … en blanc chaud ». Lumière à bascule seule (`LIGHT_TOGGLE` + état) reconnue.
- **Ventilateurs** (`FAN_SPEED`), **prises variables** (`ENERGY_SLIDER`), **chauffage marche / arrêt** (`HEATING_ON` / `HEATING_OFF`).
- **Détecteurs de fuite** (`WATER_LEAK`, `FLOOD`), **de fumée** (`SMOKE`) **et de CO** (`CO`). Une mesure de CO en ppm ne déclenche l'alarme qu'à partir de 50 ppm (avant : dès 1 ppm).
- **Capteurs de qualité de l'air** (`AIR_QUALITY`, `CO2`), **de pression** (`PRESSURE`) **et de luminosité** (`BRIGHTNESS`). Les types météo (`WEATHER_TEMPERATURE`, `WEATHER_HUMIDITY`, `WEATHER_PRESSURE`) servent de repli. Ce repli est ignoré sur un compteur d'énergie (consommation, puissance, production) : un compteur Gazpar ou Linky qui affiche la météo du jour n'apparaît plus comme une sonde de température. Les particules fines en µg/m³ (PM2.5, PM10) sont converties selon l'indice européen de qualité de l'air (avant : une faible valeur comme 5 µg/m³ était lue comme « très mauvais »).
- Plusieurs mesures d'un même équipement (température, humidité, CO₂, pression...) regroupées dans un seul appareil.
- **Niveau de batterie** transmis pour tous les types ; **chauffe en cours** des thermostats (`THERMOSTAT_STATE`).
- **Capteurs température + humidité** : un équipement qui a les deux mesures apparaît comme un seul appareil avec les deux valeurs (auparavant, seule la température était exposée). S'il était déjà exposé, l'ancien capteur de température est remplacé.
- **Serrures** (`LOCK_STATE`, `LOCK_CLOSE`, `LOCK_OPEN`) : verrouillage et déverrouillage depuis les applications ; option Inverser pour une serrure dont 1 = déverrouillée.

## 0.2.0 (2026-11-05)

- **Thermostats et climatisations** : consigne, température mesurée et modes (arrêt, chauffage, froid) pilotables depuis Alexa, Apple Maison et Google Home. Les équipements qui ont une commande de consigne (`THERMOSTAT_SET_SETPOINT` ou `AC_SET_TEMPERATURE`) sont désormais exposés comme thermostats ; s'ils étaient exposés comme capteurs de température, l'ancien capteur disparaît des applications et un thermostat le remplace.
- Les changements faits sur un équipement exposé (nom, désactivation, suppression, types génériques) sont repris automatiquement dans les 5 minutes ; auparavant il fallait sauvegarder un hub ou redémarrer le démon.
- Un équipement refusé par Matter (valeur hors bornes, par exemple) n'empêche plus son hub de démarrer : il est signalé dans la page Santé et les autres équipements restent disponibles.
- Température bornée aux limites Matter (-273 à 327 °C).
- États texte reconnus (ON/OFF, true/false, ouvert/fermé...) : une lumière dont l'état vaut « ON » n'apparaît plus éteinte.
- Équipement hors ligne : affiché comme tel dans les applications quand sa commande « Connecté » (type générique ONLINE) vaut 0 ou que son état vaut « Hors ligne ».
- Nouvelle option **Inverser** pour les ouvertures (1 = fermé) et les volets (100 = fermé).
- « Régler à x % » sur une lampe éteinte l'allume aussi, y compris pour les équipements dont le curseur seul n'allume pas.
- Volets : un refus de Jeedom est maintenant signalé à l'assistant vocal au lieu d'être annoncé comme réussi.
- Le code d'appairage et le QR code ne sont plus écrits dans le log du démon ; le niveau de log de matter.js suit celui de Jeedom.
- Sécurité : clé API du démon absente de la liste des processus, stockage Matter lisible par Jeedom seulement, fichiers de `plugin_info` inaccessibles depuis le web.
- La liste des équipements d'un hub est rechargée à chaque ouverture (nouveaux équipements visibles sans recharger la page).
- Documentation.

## 0.1.0

- Première version : plusieurs hubs Matter, chacun avec sa liste d'équipements et ses contrôleurs (Alexa, Apple Maison, Google Home) ; lumières, lumières variables, prises, volets, ouvertures, présence, température et humidité ; ajout d'un contrôleur par fenêtre d'appairage de 15 minutes ; page Santé.
