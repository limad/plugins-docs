---
layout: default
title: "jee2alexa — Caméra RTSP"
lang: fr_FR
pluginId: jee2alexa
---
# Caméra Alexa (`Alexa.CameraStreamController`) — contourner la contrainte RTSP public

`Alexa.CameraStreamController` exige un flux **RTSP ou RTSPS accessible publiquement**
("Local camera URIs (LAN-only, non-HTTPS) are not viable for Alexa integration" — doc
officielle Alexa). `shp_cameraStreamController()` (`core/php/smartHomeProxy_lib.php`)
vérifie le schéma de l'URL du cmd `CAMERA_URL` à l'exécution et renvoie explicitement
`ENDPOINT_UNREACHABLE` si ce n'est pas du `rtsp(s)://`, plutôt que de laisser Alexa
échouer silencieusement sur une URL qui ne marchera jamais.

Une `CAMERA_URL` Jeedom typique ne satisfait pas cette contrainte : c'est le plus
souvent un flux LAN (RTSP privé) ou un simple snapshot HTTP (ex. caméras
`netatmoSecurity` en `http://192.168.x.x`). Le plugin ne peut pas garantir cette
capacité automatiquement — c'est une limitation d'infrastructure, pas de code.

## Pourquoi un simple proxy PHP ne suffit pas

Un endpoint PHP qui reforwarderait l'URL LAN telle quelle vers une URL publique ne
change rien : Alexa continuerait de recevoir un flux HTTP/snapshot, pas un flux RTSP.
Il faut un composant qui **produit réellement un flux RTSP en sortie**, quel que soit
le format d'entrée.

## Solution recommandée : un relais RTSP dédié

Des outils comme [go2rtc](https://github.com/AlexxIT/go2rtc) ou
[MediaMTX](https://github.com/bluenviron/mediamtx) remplissent exactement ce rôle :

1. **Ingestion** : ils se connectent à la caméra dans son protocole natif (RTSP privé,
   MJPEG, HTTP snapshot via FFmpeg en entrée, ONVIF...).
2. **Republication** : ils réexposent le flux en RTSP(S) sur un port dédié, avec ou
   sans transcodage (H264/AAC — codecs déclarés par `shp_capCameraStream()`).
3. **Exposition publique** : reverse-proxy TLS (RTSPS) ou port-forwarding direct selon
   le niveau de sécurité souhaité. `shp_capCameraStream()` déclare
   `authorizationTypes: NONE` (aucune authentification au niveau du flux RTSP
   lui-même) — l'accès public à ce flux dépend donc entièrement du contrôle réseau mis
   en place autour du relais (pare-feu, VPN, RTSPS avec certificat, IP allowlist...).

## Configuration côté plugin

Aucune modification du plugin nécessaire : une fois le relais en place, changer
simplement la valeur retournée par le cmd `CAMERA_URL` pour qu'elle pointe vers l'URL
publique `rtsp://` (ou `rtsps://`) du relais, au lieu de l'URL LAN/HTTP d'origine.
`shp_cameraStreamController()` acceptera alors la directive Alexa normalement.

## Avertissement sécurité

Exposer un flux caméra domestique sur Internet est un arbitrage sécurité/vie privée
que l'utilisateur doit faire en connaissance de cause — ce n'est pas automatisable
par le plugin. Privilégier RTSPS (TLS) plutôt que RTSP en clair, et restreindre l'accès
réseau au strict nécessaire (le relais n'a besoin d'être joignable que par les serveurs
Alexa au moment où une session de streaming est active, pas en permanence par
n'importe qui).
