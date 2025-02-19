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
<a href="https://limad.github.io/plugins-docs/plugin-alexaapiv2"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.pngg" alt="alexatodolist icon" width="150px"></a>

**Version en cours de dev**

*   Changement de la méthode de définition des logs au démarrage et quelques ajustement de messages (merci Neurall)


Todo List
---------------------
<a href="http://jeedom.sigalou-domotique.fr/alexatodolist-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexatodolist_icon.png" alt="alexatodolist icon" width="100px"></a>

#### Bugs

*   Souci : A la génération du cookie, si le fichier n’est pas bien récupéré, il y a quand même le message Bravo
*   Annonces sur le multiroom font plante le démon. D’une manière générale tester toutes les fonctions multiroom (volume est OK)
*   Corriger l’affichage des commandes play/pause/next … dans les scénario
*   Corriger les ‘ dans les widgets Player sur le nom de l’album ou l’artiste.
*   Programmer les template widgets mobile en v4
*   La répétition ne fonctionne pas sur les rappels
*   Amazon a modifié le fonctionnement des alarmes et on peut maintenant programmer dans plusieurs jours les alarmes, à modifier
*   Quand on exporte JSON depuis le requeteur info, il y a un souci sur bluetooth par exemple
*   Regarder pourquoi le nom ne change pas quand on modifie le nom Amazon et qu’on lance un scan

#### Améliorations

*   Toiletter les logs et reclasser en info/debug/…
*   Faire en sorte que les devices ajoutés par Amazon soient désactivés à la détection ( xx Alexa Apps, This Device, Tous les appareils)
*   Pour le Scan ou Santé, ajouter un message qui dit de générer le cookie quand il n’existe pas encore
*   Ajouter un bouton de Refresh pour Santé, pour avoir le « Présent » actualisé
*   Contrôler la présence des dépendances avant de pouvoir lancer le controleur de l’API Cookie-Alexa
*   A la génération du cookie, rallonger le clignotement
*   A la génération du cookie, changer la couleur du message « ouverture de la fenetre… » mettre bleu au lieu de vert et si possible avec le cercle qui tourne
*   Supprimer la colonne « Commande envoyée » dans le tableau des commandes (non utile)
*   Supprimer la colonne « ID » dans l’écran Rappels/Alarmes (non utile)
*   Remettre les boutons Tester pour les commandes Reminder et Alarm en mettant des données test dans le code
*   Corriger la commande alarm?**&**when=#when#&recurring=#recurring# par alarm?when=#when#&recurring=#recurring#
*   Trouver comment fonctionne table\_cmd et comment sont classées les commandes dans le tableau
*   Voir pourquoi on ne peut pas déplacer les commandes dans le tableau des commandes
*   Agrandir un peu vers le bas la fenetre d’identification du cookie Amazon, quand il demande le controle captcha, on n’a pas le bouton de validation
*   Dans la commande whennextalarm, permettre d’ecrire l’option hour en majuscules ou minuscules
*   Trier les routines par ordre alphabétique.
*   Ajouter la prochaine alarme musicale
*   Supprimer musicalalarmmusicentity pour récupérer la musique par whennextmusicalalarm
*   Regarder pourquoi pas de bouton Refresh pour le player d’un groupe
*   Il faudrait que la liste déroulante des sons des alarmes puisse se mettre à jour toute seule (elle est dans un template pour les scenarios et dans une commande action)
*   Si un device est offline, mettre widget en veille ou auy moins couper l’icone de playing
*   Ajouter shuffle et repeat  pour les Multirooms
*   Ne prendre en compte pour les dernières interactions que les phrases qui sont en success
*   Si le device existe au scan, ne pas modifier la visibilité (case visible) Demande de JAG

**Evolutions**

*   Ajouter aux devices uniquement les commandes qui sont supportées par chacun
*   Permettre d’avoir d’autres serveur que amazon.fr
*   Récupérer « ‘the last spoken voice command »
*   Permettre d’activer/désactiver les alarmes
*   Ajouter date/heure dans les logs
*   Ajouter un WhenNextTimer
*   Gestion des routines
*   Lancement de son via MP3 ou autre (pour générer une alarme intrusion)
*   Récupérer le volume « en cours »
*   Programmer des fonctions telles que Speak qui enregistrent le volume en cours, lance la commande Speak à un volume précis et remette l’ancien volume
*   Ajouter une case à cocher pour Utilisateurs avertis
*   Mieux gérer les devices déconnectés
*   La commande action Delete All Alarms pourrait permettre de supprimer les rappels
*   Aller récupérer l’information « Do Not Distrub » pour l’affecter aux devices (requete /api/dnd/device-status-list)
*   Aller récupérer l’information « bluetoothStates » (requete /api/bluetooth?)

Changelog AmazonMusic/Deezer/Spotify/FireTv
---------------------

<a href="http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaamazonmusic_icon.png" alt="alexaamazonmusic icon" width="100px"></a>
<a href="http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexaspotify_icon.png" alt="alexa-deezer icon" width="100px"></a>
<a href="http://jeedom.sigalou-domotique.fr/alexafiretv-documentation"><img src="https://market.jeedom.com/filestore/market/plugin/images/alexafiretv_icon.png" alt="alexafiretv icon" width="100px"></a>

**Evolutions**

