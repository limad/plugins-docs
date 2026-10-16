[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

Changelog Plugins-Alexa
=======================

![alexaapiv2 icon](https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

*   [Présentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#presentation)
*   [Documentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#documentation)
*   Changelog
*   [Forum dédié](https://community.jeedom.com/tags/plugin-alexaapiv2)


Changelog Alexa-Premium
---------------------
<a href="https://limad.github.io/plugins-docs/plugin-alexaapiv2"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png" alt="alexaapiv2 icon" width="150px"></a>

## Version en cours de développement

### 16/10/2026

*   **Nouveautés et corrections** : « Dernier dialogue » et « Dernière réponse » se mettent de nouveau à jour après chaque échange avec Alexa (ils pouvaient rester figés)

### 15/10/2026

*   **Nouveautés et corrections** : états SmartHome (prises, lumières, capteurs, thermostats) mis à jour en temps réel en quelques secondes ; connexion Alexa plus stable (les coupures sont rattrapées automatiquement) ; historique vocal : moins de phrases manquantes ; fenêtre Historique sans doublons et jusqu'à 80 entrées affichées (au lieu de 10) ; fin des alertes répétées quand les playlists Amazon Music sont indisponibles
*   **Sécurité et fiabilité** : équipements injoignables désactivés automatiquement après 7 jours sans réponse (réglable, 7 minimum) et réactivés dès qu'ils répondent de nouveau

### 14/10/2026

*   **Voice Query (IA)** : nouvelle fenêtre « Choisir les équipements » pour décider ce que l'IA peut voir et piloter ; nombre de commandes et taille du contexte réglables pour les grosses installations ; alarme, serrure, portail, garage et sirène jamais pilotables par l'IA ; réponses fidèles (une action qui échoue n'est plus confirmée) ; sans équipement sélectionné, Voice Query n'agit pas et vous prévient dans le centre de messages
*   **Nouveautés et corrections** : scènes tierces (TaHoma, Somfy…) et scènes désactivées correctement affichées ; fonctionnement sur les installations Jeedom sans jQuery (Account Linking, gestion de la skill, assistant d'interactions, widgets) ; traductions complétées (anglais, allemand, espagnol, italien, portugais)
*   **Sécurité et fiabilité** : authentification renouvelée plus sûrement (moins de coupures et d'erreurs « 429 ») ; bouton « Parler » plus fiable ; erreur « Vendor ID is required » corrigée ; plus de fausses alertes au redémarrage *(merci Neurall)* ; clé API et cookies de session masqués dans les journaux

### 06/06/2026

*   Amélioration du flow ASK : état des questions isolé par appareil Echo pour éviter les collisions multi-équipements.
*   Ajout de protections anti-réponses tardives : une réponse ASK est ignorée si elle ne correspond plus à l'événement ou à l'appareil attendu.
*   Ajout de la commande `alexaAskSilent` pour enchaîner des dialogues ASK multi-tours sans relancer une annonce vocale.
*   Nettoyage des anciens chemins `reponseASK` et suppression du code legacy divergent.
*   Correction du modèle Skill ASK sur le slot `VoiceQuery` et amélioration des tests associés.
*   Amélioration de l'internationalisation des templates HTML de scénarios et widgets, avec déclenchement du workflow de traduction sur les fichiers `.html`.
*   Documentation du fonctionnement interne du flow ASK et ajout de tests unitaires sur l'état ASK.

### 30/05/2026

*   Ajout du déploiement automatisé du Skill ASK Alexa Premium depuis Jeedom.
*   Ajout du Skill JeeViewer pour afficher Jeedom sur les écrans Alexa compatibles.
*   Ajout d'un assistant de création d'interactions vocales.
*   Ajout du mode VoiceQuery pour interroger Jeedom en langage naturel.
*   Ajout d'un panneau de santé du Skill Alexa.
*   Ajout d'un écran de gestion des Skills Alexa.
*   Ajout du support OAuth pour le lien de compte Alexa / Jeedom.
*   Amélioration de l'authentification Amazon / Alexa.
*   Amélioration de la gestion des appareils, routines, rappels, alarmes et notifications.
*   Amélioration du scan SmartHome et des commandes GraphQL.
*   Amélioration des fenêtres de gestion : rappels, routines, historique, santé, requêtes et speak.
*   Amélioration des traductions multilingues.
*   Corrections sur les commandes Alexa, les interactions vocales et les retours SmartHome.
*   Corrections de sécurité sur les logs et la protection des informations sensibles.
*   Nettoyage interne du plugin et suppression de fichiers obsolètes.

## Avril 2026 — Refonte majeure

> Cette version représente une réécriture profonde du plugin (~80% du code). Elle pose de nouvelles bases plus stables, plus sûres et mieux adaptées aux évolutions d'Amazon.

### Authentification

*   **Nouvelle méthode d'authentification sans proxy** : la connexion à Amazon ne passe plus par un proxy intermédiaire — l'authentification est désormais directe et indépendante du daemon
*   **Double profil** : un second profil d'authentification peut être créé, offrant une meilleure résilience en cas de limitation Amazon (erreur 429)
*   Le daemon n'est plus sollicité que pour certaines informations d'authentification ; son code est maintenant indépendant des librairies externes habituelles
*   Support de **19 pays Amazon** détecté automatiquement (fr, de, uk, it, es, jp, com…) — plus aucune valeur codée en dur

### SmartHome

*   **Migration complète vers les nouveaux endpoints Amazon** (API GraphQL) : meilleure fiabilité et conformité avec l'infrastructure actuelle d'Amazon
*   **Actualisation groupée** : plusieurs appareils sont mis à jour en une seule requête (4× moins d'appels Amazon, réduction du risque de limitation)
*   Nouveau : support des **robots aspirateurs** — actions Dock, Nettoyage, sélection de pièces
*   Nouveau : support des **interrupteurs à instance** (ToggleController)
*   Scan enrichi : les modes, plages de valeurs et états supportés sont détectés et configurés automatiquement
*   Correction d'une erreur lors du scan d'appareils avec contrôleur de mode (lave-linge, climatiseur…)

### Historique vocal

*   L'acquisition de l'historique est désormais **désactivée par défaut** — il est fortement conseillé de la laisser désactivée si vous n'en avez pas d'usage régulier (impact notable sur les performances)

### Interface

*   **Refonte complète des modales** : Alarmes, Routines, Historique, Speak, Rappels — chacune entièrement réécrite
*   Suppression de jQuery : toute l'interface passe en JavaScript natif (Jeedom 4.4+)
*   Modale Routines : tableau redimensionnable, colonnes réorganisées, bouton de configuration par routine
*   Modale Rappels/Alarmes : filtre par appareil, bouton d'édition, navigation par onglets
*   Modale Historique : types de commandes vocales traduits, affichage optimisé
*   Modale Speak : barre de test fixe, 40+ exemples SSML, copie par double-clic
*   Thème sombre amélioré (variables CSS Jeedom natives)
*   Page de configuration : sélecteur de pays Amazon remplace le champ texte libre

### Sécurité

*   Les logs ne contiennent plus aucun token, cookie ou mot de passe en clair
*   Protection CSRF, correction de failles XSS, durcissement des appels système

### Optimisation

*   Nettoyage massif du code : suppression de fichiers de dev, de dead code, de données de runtime committées par erreur
*   Audit complet PHP, JS et daemon : 80+ bugs corrigés
*   Format de réponse du daemon standardisé sur toutes les routes


Changelog AmazonMusic/Deezer/Spotify/FireTv
---------------------

<a href="http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaamazonmusic_icon.png" alt="alexaamazonmusic icon" width="100px"></a>
<a href="http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaspotify_icon.png" alt="alexa-deezer icon" width="100px"></a>
<a href="http://jeedom.sigalou-domotique.fr/alexafiretv-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexafiretv_icon.png" alt="alexafiretv icon" width="100px"></a>

**Evolutions**
