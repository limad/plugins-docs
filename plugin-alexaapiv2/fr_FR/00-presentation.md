![Alexa Premium icon](https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

# Plugin Alexa Premium pour Jeedom

![Présentation Alexa Premium](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Pr%C3%A9sentationAlexaAPI.jpg)

> Ce plugin ne commande **pas** Jeedom depuis Alexa (contrairement au plugin Alexa officiel) mais **commande Alexa depuis Jeedom**. Il peut toutefois interagir avec Alexa de façon bidirectionnelle.

---

## Documentation

| # | Fichier | Contenu |
|---|---|---|
| 01 | [Installation & Mise à jour](01-installation.md) | Market, cookie/Auth, Daemon, Scan, widgets, MAJ |
| 02 | [Commandes](02-commandes.md) | Commandes simples, complexes, SSML, expressions |
| 03 | [AlexaSys & SmartHome](03-alexasys-smarthome.md) | Alexa Chat, TTS, scénarios, gestion SmartHome |
| 04 | [Skill ASK Amazon](04-skill-ask.md) | Création pas à pas du Skill interactif |
| 05 | [Bonnes pratiques & Dépannage](05-depannage.md) | Astuces, guide de dépannage, forum |

---

## Fonctionnalités

**Ce que vous pouvez faire :**

- Scanner automatiquement tous les Équipements (Echo, smartHome...) du compte Alexa (par type ou en masse)
- Récupérer les informations sur les équipements scannés (status, volumes, état,...)
- Faire parler les Amazon Echo (texte libre, SSML, annonces multi-appareils)
- Régler et récupérer le volume de chaque équipement ou d'un groupe
- Programmer des alarmes et des rappels, les supprimer
- Récupérer l'heure de la prochaine alarme ou du prochain rappel pour l'utiliser dans des scénarios
- Exécuter des routines Alexa
- Envoyer un message Push à des utilisateurs du compte
- Récupérer le texte de la dernière interaction vocale (Pas 100% fiable)
- Scanner et commander les équipements SmartHome (turnOn / turnOff, reglage consigne,...)
- Discuter avec Alexa et exploiter la réponse vocale (MP3) via **Alexa Chat**
- Exécuter n'importe quelle commande via **Alexa Chat** (lance l'aspirateur, allume la télé,...)
- Convertir du texte en MP3 via la commande **TTS** (éventuellement, exploiter le fichier)
- Exécuter directement certains skills (ayant le bouton 'Lancer' dans la config du skill)
- Déployer de manière automatisée la skill Ask
- Exécuter des commandes via la skill Ask
- Exécuter la skill JeeViewer (afficher Jeedom sur un Echo muni d'un écran)

En conjonction avec les plugins supplémentaires
- Lancer des stations de radio sur vos Echo
- Afficher la playlist en cours et lancer des pistes Amazon Music
- Envoyer des commandes au player : `pause`, `play`, `next`, `prev`, `fwd`, `rwd`, `shuffle`, `repeat`
- Lire et modifier le contenu des listes

**Ce que ce plugin ne permet pas (pour l'instant):**

- Faire la vaisselle à votre place !
- Commander des équipements Jeedom non-intégrés à Alexa (il faut passer par le plugin Alexa officiel ou IFTTT...) ou jouer avec le skill Ask et les utterances Jeedom. Détecter dans un scénario une phrase courte avec un mot clé (ex. : «Commande Jeedom ouvre volets» ou «Commande Jeedom ouvre portail») et d'en déclencher l'action correspondante via les utterances Jeedom.

---

## Plugins annexes

| Plugin | Lien |
|---|---|
| Amazon Music | [Documentation](http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation) |
| Deezer / Spotify | [Documentation](http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation) |
| Fire TV | [Documentation](http://jeedom.sigalou-domotique.fr/alexafiretv-documentation) |
| Todo List | [Documentation](http://jeedom.sigalou-domotique.fr/alexatodolist-documentation) |

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)  
🔗 Changelog : [limad.github.io/plugins-docs](https://limad.github.io/plugins-docs/plugin-alexaapiv2#changelog)
