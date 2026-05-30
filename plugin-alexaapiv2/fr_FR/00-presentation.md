![Alexa Premium icon](https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

# Plugin Alexa Premium pour Jeedom

![Présentation Alexa Premium](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Pr%C3%A9sentationAlexaAPI.jpg)

> Alexa Premium commande principalement **Alexa depuis Jeedom**. Les fonctions Skill ASK, VoiceQuery et JeeViewer ajoutent aussi des échanges vocaux ou visuels entre Alexa et Jeedom, sans remplacer le plugin Alexa officiel pour exposer tous les équipements Jeedom à Alexa.

---

## Documentation

| # | Fichier | Contenu |
|---|---|---|
| 01 | [Installation & Mise à jour](01-installation.md) | Market, cookie/Auth, Daemon, Scan, widgets, MAJ |
| 02 | [Commandes](02-commandes.md) | Commandes simples, complexes, SSML, expressions |
| 03 | [AlexaSys & SmartHome](03-alexasys-smarthome.md) | Alexa Chat, TTS, scénarios, gestion SmartHome |
| 04 | [Skills Alexa](06-skills.md) | Déploiement automatique, fonctionnement, VoiceQuery, JeeViewer |
| 04 bis | [Skill ASK Amazon manuel](04-skill-ask.md) | Alternative manuelle de création du Skill ASK |
| 05 | [Bonnes pratiques & Dépannage](05-depannage.md) | Astuces, guide de dépannage, forum |

---

## Fonctionnalités

**Ce que vous pouvez faire :**

- Scanner automatiquement les équipements du compte Alexa (Echo, groupes, SmartHome...) par type ou en masse
- Récupérer les informations sur les équipements scannés (statut, volume, état, consigne, température...)
- Faire parler les Amazon Echo (texte libre, SSML, annonces multi-appareils)
- Régler et récupérer le volume de chaque équipement ou d'un groupe
- Programmer des alarmes et des rappels, les supprimer
- Récupérer l'heure de la prochaine alarme ou du prochain rappel pour l'utiliser dans des scénarios
- Exécuter des routines Alexa
- Envoyer un message Push à des utilisateurs du compte
- Récupérer le texte de la dernière interaction vocale (Pas 100% fiable)
- Scanner et commander les équipements SmartHome via les capacités Alexa disponibles (marche/arrêt, consigne, mode, robot, égaliseur, etc.)
- Discuter avec Alexa et exploiter la réponse vocale (MP3) via **Alexa Chat**
- Exécuter n'importe quelle commande via **Alexa Chat** (lance l'aspirateur, allume la télé,...)
- Convertir du texte en MP3 via la commande **TTS** (éventuellement, exploiter le fichier)
- Exécuter directement certains skills (ayant le bouton 'Lancer' dans la config du skill)
- Déployer automatiquement le Skill ASK Alexa Premium et enregistrer son identifiant dans Jeedom
- Interroger Jeedom en langage naturel via VoiceQuery et les interactions vocales
- Exécuter la skill JeeViewer pour afficher Jeedom sur un Echo Show, Fire TV ou écran Alexa compatible

En conjonction avec les plugins supplémentaires
- Lancer des stations de radio sur vos Echo
- Afficher la playlist en cours et lancer des pistes Amazon Music
- Envoyer des commandes au player : `pause`, `play`, `next`, `prev`, `fwd`, `rwd`, `shuffle`, `repeat`

**Ce que ce plugin ne permet pas (pour l'instant):**

- Faire la vaisselle à votre place !
- Remplacer totalement le plugin Alexa officiel pour l'exposition native de tous les équipements Jeedom dans Alexa.

---

## Plugins annexes

| Plugin | Usage |
|---|---|
| Amazon Music | Fonctions avancées de lecture et suivi des pistes |
| Deezer / Spotify | Fonctions avancées de lecture musicale |
| Fire TV | Contrôle des appareils Fire TV |

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)  
🔗 Changelog : [limad.github.io/plugins-docs](https://limad.github.io/plugins-docs/plugin-alexaapiv2#changelog)
