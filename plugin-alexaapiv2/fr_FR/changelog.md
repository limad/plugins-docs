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

### Fiabilité et performance

*   **Plus de daemon zombie au démarrage** : le warning « PID actif mais HTTP non joignable » n'apparaît plus pendant les redémarrages légitimes (grace period 30 s avant signalement)
*   **Fin des erreurs 429 horaires** sur `/homegroups/{id}/devices` : le cache des appareils SIP passe de 1 h à 7 jours, avec invalidation automatique lors d'un scan manuel
*   **Cookie refresh atomique** : plus de double-refresh concurrent en cas de 401 simultanés (évite les cascades d'authentification ratées)
*   **Listeners daemon ciblés** : plus de fuite mémoire de listeners à chaque redémarrage du daemon
*   **Modal « Speak » passe en PHP direct** : le bouton « Parler » utilise désormais le round-robin 4 voies (P1/P2/com/actor) au lieu du daemon, fini les timeouts curl error(28)
*   **Décommissionnement progressif du daemon** : les commandes `addList`, `addItem`, `modifyItem`, `deleteItem`, `getPlayerInfo`, `getMedia` passent désormais directement par PHP — gain de latence et résilience accrue
*   **`comCookie` ne reste plus figé indéfiniment** : renouvellement automatique toutes les heures (auparavant, ça ne se déclenchait que par hasard selon l'exécution des routines)
*   **Reconnexion automatique de la session développeur Amazon expirée** : élimine l'erreur *« Vendor ID is required. »* lors du déploiement de la skill vocale
*   **Scènes Alexa mieux catégorisées** : les scènes tierces (TaHoma, Somfy…) ainsi que les scènes désactivées s'affichent et se rafraîchissent désormais correctement

### Sécurité

*   **`pluginKey` (clé API Jeedom) ne traîne plus dans les logs** : niveau abaissé de `info` à `debug`
*   **Cookies de session (`at-main`) masqués dans les logs de debug**
*   **Forwarder daemon bloque les IP privées** : durcissement contre SSRF
*   **Récursion contrôlée** sur `setCookie` : protection contre cookies malformés à structure infinie

### Compatibilité

*   Compatibilité PHP 7.4.33 : remplacement des expressions `match()` (PHP 8.0+) et de `str_contains()` (PHP 8.0+) par des équivalents PHP 7.4
*   Ajustements de messages de log au démarrage *(merci Neurall)*

### Nettoyage

*   `.gitignore` réécrit (153 → 66 règles, -57 %), réorganisé par catégorie
*   Fichiers Sprint D Skill SmartHome retirés du repo public (parking en attendant l'évolution Amazon Lambda) — code conservé localement
*   Code de déploiement natif « Skill SmartHome » obsolète retiré (déplacé vers un plugin dédié)

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
