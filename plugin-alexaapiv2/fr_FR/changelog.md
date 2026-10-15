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

### Voice Query (IA)

*   **Contexte IA adapté aux grosses installations** : le nombre maximum de commandes (350 par défaut) et la taille maximale du contexte (6 000 caractères par défaut) sont désormais réglables dans la configuration du plugin
*   **Équipements exposés** : nouvelle modale « Choisir les équipements » (recherche, cases par objet, compteurs) pour sélectionner précisément ce que Voice Query peut voir et piloter
*   **Pièce de l'Echo prioritaire** : les équipements de la pièce de l'Echo qui reçoit la demande sont traités en premier, ils ne sont plus écartés par la limite de commandes
*   **Troncature propre** : le contexte est coupé en fin de ligne, plus au milieu d'un équipement
*   **Plus de doublon avec ai_assistant** : un prompt système dédié évite que la liste d'équipements d'ai_assistant s'ajoute à celle du plugin, et l'exécution des actions reste validée par le plugin
*   **Sélection obligatoire** : sans équipement sélectionné, Voice Query n'expose plus rien et lève une alerte dans le centre de messages ; la phrase est alors traitée par les interactions Jeedom classiques (plus de panne vocale)
*   **Commandes sensibles jamais exposées** : alarme, serrure, portail, garage et sirène (type générique ou nom de la commande) ne sont plus pilotables par l'IA ; un cadenas les signale dans la modale
*   **Réponses fidèles** : si l'exécution échoue, la réponse le dit au lieu de confirmer « c'est fait »
*   **Valeurs contrôlées** : sliders bornés au min/max de la commande, identifiants de commande stricts, commandes à liste de choix non pilotables
*   **Contexte fiable** : retours ligne et crochets retirés des noms et valeurs envoyés à l'IA, ordre des équipements identique d'une requête à l'autre, et seules les commandes réellement visibles dans le contexte peuvent être exécutées (une commande tronquée ne l'est plus)
*   **Compteur et plafonds** : le badge de configuration n'affiche que les équipements réellement exposables, les ids supprimés sont retirés de la sélection ; plafonds de 1 000 commandes et 20 000 caractères ; un avertissement est écrit dans les logs lorsque le contexte est tronqué

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

### Push temps réel, historique vocal et SmartHome

*   **Push HTTP/2 plus fiable** : plus d'abandon après 100 échecs (nouvelle tentative toutes les 10 min), jeton renouvelé de force sur 401/403, ping applicatif AVS (`/ping`) et endpoint aligné sur la bibliothèque de référence ; une perte de push est désormais visible dans le log (`warning`)
*   **État SmartHome en temps réel** : prises, lumières, capteurs et thermostats se mettent à jour en quelques secondes grâce au push `FDAL_UPDATE`, sans attendre le cron SmartHome
*   **Capacités déclarées à chaque démarrage** : le daemon enregistre 35 interfaces Alexa auprès d'Amazon (14 auparavant)
*   **Historique vocal** : une phrase dispose de 30 s et 3 essais avant d'être abandonnée (18 s et 2 essais auparavant)
*   **Modale Historique** : plus de lignes en double, et jusqu'à 80 enregistrements affichés au lieu de 10
*   **Désactivation automatique des équipements SmartHome** : après x jours consécutifs sans réponse (7 par défaut, 7 minimum, réglable dans la configuration) ; un équipement réactivé à la main est de nouveau surveillé
*   **Playlists** : plus de warning toutes les heures quand les sources Amazon Music sont indisponibles

### Interface et traductions

*   **Account Linking fonctionne sans jQuery** : les boutons « Vérifier l'état », « Configurer » et « Supprimer » ne renvoient plus « $ is not defined » lorsque Jeedom est en mode sans jQuery (`core::jqueryless`)
*   **Modales du skill et assistant d'interactions sans jQuery** : « Déployer le skill », « Gérer les skills », « Santé du skill » et l'assistant d'interactions fonctionnent en mode sans jQuery (`core::jqueryless`) ; une erreur HTTP est maintenant signalée au lieu de laisser le chargement tourner
*   **Widgets du dashboard sans jQuery** : les icônes Bluetooth et muet se mettent à jour et le bouton du widget « speak » fonctionne en mode sans jQuery
*   **Traductions complétées** : les textes de la configuration (Voice Query, panneau d'authentification, déconnexion des sessions, import P2 → P1) et de la modale de sélection des équipements sont traduits en anglais, allemand, espagnol, italien et portugais

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
