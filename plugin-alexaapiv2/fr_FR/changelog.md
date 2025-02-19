[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

Alexa Premium (et AmazonMusic/Deezer/Spotify/FireTv/Todo-List) Changelog
------------------------------------------------------------------------

![alexaapiv2 icon](https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

*   [Présentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#presentation)
*   [Documentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#documentation)
*   Changelog
*   [Forum dédié](https://community.jeedom.com/tags/plugin-alexaapiv2)

[![alexaamazonmusic icon](https://market.jeedom.com/filestore/market/plugin/images/alexaamazonmusic_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation) [![alexaspotify icon](https://market.jeedom.com/filestore/market/plugin/images/alexaspotify_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-spotify-documentation) [![alexadeezer icon](https://market.jeedom.com/filestore/market/plugin/images/alexadeezer_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation)

Change Log
==========

**Version en cours de dev**

*   Changement de la méthode de définition des logs au démarrage et quelques ajustement de messages (merci Neurall)

_**Fusion des Versions Beta et Stable au 2022-12-23**_

_**Version Bêta 2022-12-11**_

*   Ajout icones smartHome : Bridge / FireStick (Merci Skillix)
*   Correction d’un blocage sur l’enregistrement des devices smartHome à désactiver

_**Version Bêta 2022-12-08**_

*   Ajout de la création du double équipement des Bridge/Echo quand la duplicité existe (Merci Skillix)
*   Ajout icones smartHome : Bridge / FireStick (Merci Skillix)
*   Correction d’un blocage sur l’enregistrement des devices smartHome à désactiver

_**Version Bêta 2022-10-02**_

*   Grosse modification de la librairie cookies/remote, passage en v5.8.2
*   Dans le requeteur info, ajout de PlayerQueue qui permettra de retrouver les playlists, en effet, visiblement Amazon ne diffuse plus ces infos par le MQTT (function getPlayerQueue)
*   Ajout d’une fenêtre proposant le rafraichissement de l’écran après un forcage à valeur par défaut (Alexa Premium, AmazonMusic et smartHome)

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/image2.png)

*   Correction d’un souci d’actualisation des alarmes/rappels/minuteurs…
*   Ajout d’un bouton « mute » pour mettre en sourdine
*   Ajout d’un bouton « unmute » pour désactiver la sourdine

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/image.png)

*   Correction de soucis potentiels sur les remontées d’info des alarmes/rappels/timers
*   Correction de soucis potentiels pour envoyer une alarme en instantané
*   Embellir le nom du module dans la page de santé (Merci noodom)
*   Refonte de la gestion du Mute et de la connectivité Bluetooth (nouveaux icones) :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/image-2.png)

*   Intégration du Moniteur de qualité d’Air d’Amazon dans smartHome

**Versi****o****n** _**2022-06-04 (stable&bêta)****:**_

*   Le log alexaapiv2\_scan passe en info au lieu de debug
*   Ajout d’infos dans le log scan en cas de suppression de device par la config
*   NodeJs passe en v16 (Merci Nebz)
*   Modification librairie alexaapiv2.js pour prise en compte des réponses  : undefined, ajout de l’info dans le log
*   Correction d’un souci d’affichage sur Desktop, les listes déroulantes ne déroulaient plus

**Versio****n** _**2022-04-16 (stable&bêta)****:**_

*   Ajout support commandes actions smartHome pour serrures (Merci Skillix)
*   Correction d’un souci sur le widget player sur changement de volume d’un groupe
*   Amélioration des notifs d’info lors du scan (possible grâce à la présentation V4.2)
*   Amélioration du Refresh Cookie et suppression Cron dans config (calé à 30 15 \* \* \*/7)
*   Amélioration champ de recherche (Merci JAG)![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot-2022-04-03-at-18-19-05-Alexasmarthome-Jeedom.png)

*   Ajout du mode tableau sur le desktop (Merci JAG)![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot-2022-04-03-at-18-20-04-Alexasmarthome-Jeedom.png)

**Version 2021-11-27 (stable&bêta)**

*   Fusion des deux versions
*   Correction du gros bug MQTT engendré par un renforcement de la sécurité d’Amazon
*   Ajout des images des nouveaux Echo

**Version 2021-09-12 19:02 (stable&bêta)**

*   Correction risque d’erreur à l’exécution, merci Eric !!

**Version 2021-09-03 09:03 (stable&bêta)**

*   _Beta et stable **fusionnées** le 03/09/2021_

*   ressources/lib/formerDataStore.json ignoré et supprimé du paquet

**Version 2021-09-02 21:47 (beta)**

*   Mise à jour pour debian bulleye et nouvelles versions nodejs (Merci Nebz)

**Version 2021-06-05 09:45 (stable&beta)**

*   _Beta et stable fusionnées le 05/06/2021_
*   Modification requeteur info pour récupérer les « lists »
*   Modification requeteur info pour tester la fonction allDeviceVolumes
*   Correction « Erreur sur la fonction cron du plugin : 6 is not a valid position »
*   Correction du changement de lien vers l’API des routines (v2)
*   Correction changement d’API pour l’historique, modification de la librairie et modal Historique.
*   Correction d’un bug de perte de liste des routines et des alarmes en cas de save d’un Device.
*   Modification de la commande Speak via un scénario pour ignorer #volume#
*   Ajout des interjections  et des sons via balises #, [doc](https://limad.github.io/plugins-docs/plugin-alexaapiv2/alexa-api-documentation#Utilisation_de_balises_pour_les_interjections_et_les_sons) mise à jour
*   Suppression de la condition du log en Debug pour activer les requeteurs sur desktop
*   Relance automatique du serveur désactivé sauf pour « utilisateurs expérimentés » (devenue inutile)
*   Ajout d’une option « Protection du sommeil » qui désactive toutes les commandes de xxh à xxh
*   Mise à jour de NodeJS 12 vers 14 pour s’aligner avec les autres plugins
*   Ajout du paramétrage Display/showNameOndashboard lors de la création de commandes
*   Travail sur les commandes « Volume » des devices et des groups
*   Mise à jour proxy.js (Alexa-cookie 3.4.3) : handle potential crash case
*   Adjust automatic Cookie Refresh interval from 7 to 4 days et recalage à 1 jour sur tokensValidSince  
    
*   Ajout de la prise en compte des commande setThermostatMode, Fan.Speed (sélection de vitesse numeroté) et Blind.Lift (selection de vitesse/ouverture en %) Merci **Skillix** et **Didier3L**
*   Passage de http-proxy-middleware from 1.3.1 to 2.0.0 in /resources
*   Correction dernière modif acceptation Cookie d’Amazon: Remote 3.8.1 (2021-06-04) (bbindreiter) Set missing Accept Header

_Beta et Stable fusionnés le 17-01-2021_

**Version 2021-01-17 11:04 (stable&beta)**

*   **ÉNORME découverte**, c’était la génération du csrf qui posait souci lors du renouvellement du cookie, Amazon semble changer régulièrement de page et cela a été corrigé. Donc le Daemon ne devrait plus tomber et le cookie se renouveler tout seul sans aucune intervention de l’utilisateur.
*   _Nouvelle fonctionnalité_ : Ajout de la possibilité d’envoyer une phrase à Alexa (fonctionnement identique à une phrase parlée)
*   Petites Modifs de Alexa-remote 3.3.2 en 3.3.3 (fix potential crash case)
*   Ajout TextCommand Alexa-remote2 3.3.3 en 3.4.0
*   jeeObject::buildTree à la place de jeeObject::all (merci Nebz)
*   Gros travail sur les logs de alexaapiv2\_node pour suivre la génération du cookie et son renouvellement
*   Modification mineures sur la génération du cookies et son renouvellement
*   Essai de régénération du cookie après 23h pour test
*   Correction mineure d’une ligne en log Error au lieu d’être en log Info
*   Rectangles arrondis (merci JAG)
*   Corrections et suppressions de « Notices » (merci EricG)

_Beta et Stable fusionnés le 21-11-2020_

**Version 2020-11-21 21:50 (stable)**

*   Ajout du futur plugin Alexa-FireTV, intégration icone et scan
*   Ajout de la commande pour l’affichage de l’heure (merci Alexandre)
*   Ajout des boutons sur le Widget et des commandes pour afficher/masquer l’heure des Echo Dot Horloge ![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/horlogeonoff.png)
*   Correction install des dépendances, petit fix pour une commande + pour les vieux raspberry et enfin un fix pour un problème de prefix (merci Nebz)

**Version 2020-09-30 (stable)  
**

*   Optimisation du code et corrections mineures (merci Thibaut)
*   Ajout d’une commande « Volume » pour les groupes
*   Correction d’un bug sur le retour de la dernière interaction
*   **Nouvelle correction du plugin pour s’adapter à une encore nouvelle modification d’Amazon dans l’identification**
*   Ajout des badges sur les players et smartHome
*   Modification des bagdes OffLine
*   Ajout d’un bagde quand le device vient d’être découvert
*   Migration font en version 5 (Merci JAG)
*   Modification génération Playlist (Bug apparu)
*   Ajout d’une nouvelle cmd info : **En ligne** (attention mise à jour lente, 15 min environ)
*   Correction d’un bug dans les touches du volume qui provoquait un _« private fields are not currently supported »_
*   Ajout de fichiers .htaccess dans les dossiers core/class de tous les plugins
*   Modification de querySmarthomeDevices pour éviter qu’une perte de connexion au serveur Amazon ne fasse tomber le démon
*   Modification des logs, formats et suppression de traces inutiles
*   Correction d’un bug sur la commande **volume**, elle était recréée à chaque enregistrement du device. (suite à l’ajout du volume dans les groupes)
*   Ajout des **Fire TV Cube,** image et prise en compte des commandes

**Version 2020-07-10 13:25**

*    Modification du Timeout sur tous les lecteurs, passage de 3s à 10s (soucis sur les envois de commandes « radio » chez certains)
*   Correction Installation nodejs

**Version 2020-06-26 01:24 (beta et stable) d’Alexa Premium :**

*   **Correction du plugin pour s’adapter à une nouvelle modification d’Amazon dans l’identification**
*   Ajout d’un template/scénario pour pouvoir spécifier un ID quand on utilise la commande deletereminder
*   Correction d’un bug sur la commande deleteallalarms, le type et le status ne changeaient pas, il suffit de supprimer la commande et forcer la création des commandes pour avoir la mise à jour.
*   Ajout d’un calendrier au template sécnéario, c’est  dire qu’on peut choisir la date/heure avec un petit assistant lors de la création d’un scénario (Merci Aidom)
*   Ajout de la possibilité d’ajouter un rappel avec une récurrence (Merci Aidom)
*   Ajout de la possibilité de faire une « test » avec la commande « Faire parler Alexa en SSML »
*   Ajout d’un contrôle de la présence de la balise <speak> en SSML sinon le signaler en le disant.
*   Modification librairie Alexa mqtt pour corriger le problème du constructeur Buffer qui est déprécié
*   Ajout des commandes de couleur et de luminosité pour les smartHome (de Alexa-smartHome)
*   Modification du contrôle du cookies, le serveur ne sera pas lancé si le cookie n’est pas bon (modif du log également)
*   Ajout d’un message (message:add) pour informer l’utilisateur que le cookie Amazon doit être régénéré.
*   Ajout d’un contrôle de « doublon »de Name lors de l’import (arrive quand on change de playeurMusique par exemple), ça ajoute « doublon xxxx » derrière pour ne pas bloquer le scan.
*   Ajout d’un message qui prévient l’utilisateur s’il a deux players activés en même temps (cela ne fonctionnera pas)
*   Modification du ScanDevices pour n’ajouter les playlists qu’aux Device d’AmazonMusic (pour l’instant)
*   Correction de Warnings
*   Ajout du logo de application Alexa pour Windws 10
*   Modification de la récupération des routines (dans Alexa Premium) et des playlists (dans Alexa-amazonMusic) pour pouvoir diminuer le nombre de sollicitation du serveur Amazon, ajout de ces informations dans la config, possibilité de forcer la récupération des infos plus rapidement.

**Version 20-05-009 (beta et stable) des players :**

*   Correction d’une erreur de synthaxe sur jeealexaapiv2

**Version 2020-02-27 20:01**

*   Ajout de la détection des équipements smartHome
*   Ajout de la commande **Announcement** (identique à Speak avec une musique d’acceuil)
*   Ajout de la case à cocher « Activer les fonctions Domotique des Amazon SmartHome » dans la config
*   Ajout de la case à cocher « Activer les fonctions Multimedia (Player/Playlist) » dans la config
*   Ajout des commandes turnOn et turnOff pour les équipements smartHome
*   Correction petit bug sur l’utilisation de la commande Play Music Track via un scénario
*   Correction petit bug si les cases de config sont pas initialisées
*   La gestion des radio est passé d’un string à un select, donc on sélectionne dans une liste déroulante **(attention, vérifiez vos scénarios #station# est devenu #select#)**
*   La gestion des MusicTrack est passé d’un string à un select, donc on sélectionne dans une liste déroulante **(attention, vérifiez vos scénarios #trackId# est devenu #select#)**
*   La commande Radio sur un multiroom fonctionne avec une seule commande, plus de boucle
*   Les commandes Play/pause/Next/Previous sur le multiroom fonctionne avec une seule commande, plus de boucle
*   Les commandes action qui sont des listes ont maintenant leur champ avec les données de la liste déroulante, donc modifiable (Playlist, Radio, IdTrack)
*   Refonte du Widget des radios pour intégrer une liste déroulante
*   Refonte du Widget des pistes musicales pour intégrer une liste déroulante
*   Ajout de la commande info : **providerName**
*   Ajout de la commande info : **contentId** qui donne le TrackID d’une piste en cours de lecture ou d’une radio
*   Correction d’un bug qui ajoutait « Last interaction » aux smartHome
*   Correction d’un bug sur la commande **Volume**, il doit y avoir **volume?value=#slider#** et non **volume?value=#volume#**
*   Correction d’un bug sur la commande **Volume**, il doit y avoir **command?command=#select#** et non **command?command=#command#**
*   Ajout de notificationSounds dans le requeteur info
*   Ajout de « Reminders » dans le requeteur info
*   Ajout de la possibilité de choisir un sound à chaque alarme lors de sa programmation
*   Affichage du nom de la musique d’alarme sur l’onglet « Rappels/Alarmes »
*   Correction d’un bug sur la liste des devices qui peuvent lancer les routines, limitation aux Alexa
*   Bouton « Recharger configuration par défaut » déplacé à côté du bouton Sauvegarder
*   Refonte des widgets des commandes Speak et Annoucement (et changement des titres)
*   Refonte du widget de Last intéraction et changement de titre
*   Correction d’un bug sur la commande **Reminder**, il doit y avoir **text=#message#** et non **text=#text#**
*   Template pour scénario pour la commande Reminder refait
*   Ajout d’un son par défaut sur la commande Alarm pour ne pas que le test par le bouton Tester ne bug
*   Ajout d’une nouvelle commande info **bluetoothDevice** qui va donner le nom du device bluetooth connecté à votre Alexa
*   Ajout d’un paramètre **Volume** aux commandes Speak et Annoucement
*   Modification du template scenario de Speak et de Announcement pour ajouter le volume
*   Ajout du niveau de log dans la commande de lancement du serveur pour transmettre au nodejs cette info (pour la gestion du log du node)
*   Ajout d’une case à cocher « Activer le client MQTT Amazon (conseillé pour les fonctions multimédia) » dans la config
*   **Ajout d’une détection d’erreur d’envoi de commande (dans remote), puis pause 8s et re-envoi de la même commande**
*   Suppression de : whennextalarm, whennextmusicalalarm, musicalalarmmusicentity, whennexttimer, whennextreminder, whennextreminderlabel
*   Ajout de **updateallalarms** qui vient remplacer 6 autres commandes (1 requête vers Amazon au lieu de 6)
*   Refonte des infos : whennextmusicalalarminfo, whennextreminderinfo, whennextmusicalalarminfo, whennexttimerinfo au format CRON (et non plus hhmm)
*   Modification des templates qui correspondent aux 4 commandes ci-dessus pour adaptation format de résultat
*   Ajout de « Routines » au requèteur Infos
*   Corrections de petits soucis de refresh des routines

**Version** **2019-10-27 18:05:00**

*   Ajout d’un cron de relance du lien avec le serveur (par défaut à 03h33 du matin)
*   Le changement de volume d’un groupe « Multiroom » ne boucle plus, en une seule commande, il change le volume de tous les équipements du groupe
*   Ajout d’un requêteur de développement (pour tester les requêtes directes vers le serveur Amazon, pour utilisateurs avertis)
*   Intégration du lien MQTT avec ajout des commandes info : VoluleInfo InteractionInfo

*   Ajout des images de la Freebox Delta et de Echo Input
*   Ajout d’un lien entre le volume remonté de l’Echo et le curseur « volume » du widget
*   Ajout de la possibilité de choisir dans quelle pièce ajouter les nouveaux équipements détectés au scan
*   Les requêteurs sont disponibles pour les utilisateurs expérimentés (option dans la config) etant en mode debug
*   Ajout de la case à cocher « Activer les fonctions Domotique des Amazon SmartHome » dans la config
*   Ajout de la case à cocher « Activer les fonctions Multimedia (Player/Playlist) » dans la config
*   Augmentation du timeout de 2s à 3s pour les requetes http
*   Ajout de la fonctionnalité « Recharge configuration par défaut » pour chaque Device
*   Ajout de la fonctionnalité « Supprimer tous les devices !! et relancer un Scan » dans la config du plugin
*   Les routines sont mise à jour lors du scan
*   Les playlists sont mise à jour lors du scan
*   Correction d’un souci de majscule qui bloquait la commande DeleteAllAlarm
*   Ajout de l’information MusicEntity quand on a une alarme musicale
*   Ajout de la commande Play Music Track pour lire un Track Amazon Music grace à son Id
*   Refonte du template scénario pour la commande Radio
*   Refonte du template scénario pour la commande Play Music Track
*   Le logo d’un Device Player sur le desktop passe sur « Play » vert quand il est en lecture (ça s’actualisera au prochain chargement de page)
*   La playlist affiche une animation pour savoir quelle piste est en lecture et se décale progressivement.

**Version** **2019-09-22 09:41:38**

*   Mise en place d’un test d’authentification lors du Refresh et d’une relance du serveur en cas de perte d’autorisation

**Version 2019-09-15 08:22:00  
**

*   Correctif des {{}} qui s’affiche en V4  
    
*   Mise à jour de la librairie

**Version 2019-09-08 15:01:32  
**

*   Correctif pour simplifier les requetes au serveur dont les routines (une fois à chaque refresh, pas à chaque device)  
    

**Version 2019-09-05 18:44:02  
**

*   Correctif pour ceux qui ont des vm’s

**Version 2019-09-03 13:18:15  
**

*   Correction d’un bug sur ouverture des screen Historique, Routines, Rappels, Santé …
*   Remplacement de tous les .on(‘click’ par .off(‘click’).on(‘click’
*   Ajout requêteur
*   Ajout de WhenNextMusicalAlarm
*   Ajout d’un contrôle transparent qui vérifie si la connexion est ok lors du CRON sinon relance le serveur (pour éviter Connexion Close)
*   Refonte général de l’affectation des commandes, elles sont affectées en fonction de la capacité affichée de chaque équipement
*   Recodage de la partie Multiroom, tout fonctionne !
*   Correction du bug **« Alexa Premium: Error: no csrf found »**
*   Support nodejs v12 (prévision pour debian Buster)

**Version : 2019-04-12 18:32:27**

*   Ajout de l’écran Routines.
*   Lancement possible des routines dans l’écran Routines grace au bouton « play » tout à droite
*   Lancement des routines par scénario ou par commande action
*   Ajout d’une commande Refresh pour lancer la mise à jour de la liste des routines (utile pour le template des routines) et pour actualiser les valeur des WhenNextAlarm/Reminder…
*   Ajout un CRON15 pour vérifier la connexion avec Alexa et pour lancer le Refresh
*   Possibilité de ranger les lignes des Commandes action/info des devices (drag & drop)
*   Suppression des Speak+volume et Radio+Volume qui n’apporte rien puisque deux commandes lancées une derrière l’autre mais pose des soucis. Sera remplacé par de nouvelles commandes quand on saura récupérer le volume « en cours » d’Alexa.
*   Ajout d’une file des commandes et d’un controle de bonne execution, sinon au prochain lancement de serveur, les commandes sont executées.
*   Correction dans un souci sur la commande Push qui indiquait que le sdevice n’était pas spécifié
*   Sur l’écran Historique, identification des commandes envoyées via Jeedom

**Version : 2019-03-19 19:43:34**

*   Modification du script alexa-remote.js pour prise en compte des **autres serveurs Amazon** (.es .de …)
*   Modification mineure du message Alexa Premium: \* Server listening on port 3456 \* dans alexaapiv2.js
*   Correction d’un bug mineur sur le contrôle d’erreur d’envoi de la commande Volume
*   Ajout de la commande action : command qui permet de lancer **pause|play|next|prev|fwd|rwd|shuffle|repeat**
*   Ajout de la commande **Radio**
*   Correction d’un bug à la création des commandes sur isVisible
*   Correction souci mineur sur les évènements contenant Radio+Volume
*   Ajout du format HHMM pour WhenNextAlarm
*   Intégration de setDisplay et setconfiguration/request dans la boucle de création automatique de la commande et non dans la mise à jour
*   Ajout des paramètres type et status à la commande DeleteAllAlarms

**Version : 2019-03-11 18:36:14**

*   Ajout dans la configuration d’options pour définir le serveur Amazon et le serveur Alexa, et ainsi rendre international le plugin
*   Augmentation du temps d’attente avant l’ouverture de la fenetre de chargement du cookie Amazon
*   Agrandissement (en hauteur) de la fenetre de chargement du cookie Amazon

**Version : 2019-03-11 12:41:20**

*   Création **Cookie Alexa**, changement de couleur du bouton qui informe de l’ouverture de la fenetre d’identification (Vert->bleu) avec cercle qui tourne
*   Création **Cookie Alexa**, diminution du temps attente ouverture popup 2000ms->1500ms
*   Création **Cookie Alexa**, augmentation du temps de génération du Cookie avant lancement du démon (3=>4 clignotements)
*   Création de la commande **WhenNextAlarm** qui dit quand aura lieu la prochaine alarme ([explications](https://limad.github.io/plugins-docs/plugin-alexaapiv2))
*   Création de la commande **WhenNextReminder** qui dit quand aura lieu le prochain rappel ([explications](https://limad.github.io/plugins-docs/plugin-alexaapiv2))
*   Création d’une commande **DeleteAllAlarms** pour supprimer toutes les alarmes et tous les rappels d’un device

**Version : 2019-03-07 19:09:17**

*   Recalage de la largeur des colonnes des Commandes des équipements
*   Verrouillage des Commandes (Action ou Info)
*   Refonte de la grille des Commandes, possibilité d’avoir des commandes qui envoient des résultats dans des Commandes Info
*   Correction de la commande alarm?**&**when=#when#&recurring=#recurring# par alarm?when=#when#&recurring=#recurring#

**Version Stable : 2019-03-05 20:05:48  
**

Elle permet à ce stade de :

*   Scanner automatiquement tous les Echo du compte Amazon
*   Faire parler les Amazon Echo
*   Régler le volume
*   Programmer des alarmes et les supprimer
*   Programmer des rappels et les supprimer

* * *

**Plein de Version Beta xx-02-2019**

*   Correction mineure sur l’affichage des boutons permettant de générer le cookie Amazon
*   Beaucoup d’autres choses avant sortie de la première version stable

**Version Beta 14-02-2019**

*   Ajout de la génération automatique des commandes **Speak** et **Volume**
*   Refonte complète de la **génération du cookie Amazon**
*   Blocage du lancement du Daemon tant que le cookie n’est pas présent

**Version Beta 12-02-2019**

*   Ajout du **volet de gauche** (panneau latéral)
*   Ajout d’un CSS pour améliorer l’affichage des équipements
*   Bug : Kill initCookie.js remplace Kill Cookie.js

**Version Beta 09-02-2019**

*   **Ajout automatique des équipements** Amazon Echo
*   Détection du **type de chaque équipement** ainsi que de sa Présence

 Todo List
---------------------

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