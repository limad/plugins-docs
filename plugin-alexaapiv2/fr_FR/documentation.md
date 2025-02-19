![alexaapiv2 icon](	https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

*   [Présentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#presentation)
*   Documentation
*   [Todo-List / Changelog](https://limad.github.io/plugins-docs/plugin-alexaapiv2#changelog)
*   [Forum dédié](https://community.jeedom.com/tags/plugin-alexaapiv2)

[![alexaamazonmusic icon](https://market.jeedom.com/filestore/market/plugin/images/alexaamazonmusic_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation) [![alexaspotify icon](https://market.jeedom.com/filestore/market/plugin/images/alexaspotify_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-spotify-documentation) [![alexadeezer icon](https://market.jeedom.com/filestore/market/plugin/images/alexadeezer_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation)

[Limad44 Jeedom](https://limad.github.io/plugins-docs)

Alexa Premium Documentation
==========
Toggle 
*   [Installation du Plugin Alexa Premium](#Installation_du_Plugin_Alexa Premium "Installation du Plugin Alexa Premium")  
    *   [Installer le Plugin depuis le Market](#Installer_le_Plugin_depuis_le_Market "Installer le Plugin depuis le Market")  
    *   [Activer le Plugin](#Activer_le_Plugin "Activer le Plugin")  
    *   [Recharger les dépendances](#Recharger_les_dependances "Recharger les dépendances")  
    *   [Générer manuellement le cookie Amazon](#Generer_manuellement_le_cookie_Amazon "Générer manuellement le cookie Amazon")  
    *   [S’identifier sur la pop-up d’Amazon](#Sidentifier_sur_la_pop-up_dAmazon "S’identifier sur la pop-up d’Amazon")  
    *   [Lancer le Daemon s’il ne se lance pas tout seul](#Lancer_le_Daemon_sil_ne_se_lance_pas_tout_seul "Lancer le Daemon s’il ne se lance pas tout seul")  
    *    [](#i " ")  
    *   [Lancer le SCAN](#Lancer_le_SCAN "Lancer le SCAN")  
*   [Mise à jour ou Changement de version](#Mise_a_jour_ou_Changement_de_version "Mise à jour ou Changement de version")
    *   [Solution 1 : Supprimer tous les équipements et leurs commandes et les recréer](#Solution_1_Supprimer_tous_les_equipements_et_leurs_commandes_et_les_recreer "Solution 1 : Supprimer tous les équipements et leurs commandes et les recréer")  
    *   [Solution 2 : Forcer la mise à jour de toutes les commandes](#Solution_2_Forcer_la_mise_a_jour_de_toutes_les_commandes "Solution 2 : Forcer la mise à jour de toutes les commandes")  
    *   [Solution 3 : Le SCAN](#Solution_3_Le_SCAN "Solution 3 : Le SCAN")
*   [Les écrans de gestion](#Les_ecrans_de_gestion "Les écrans de gestion")
    *   [Scan](#Scan "Scan")  
    *   [Configuration](#Configuration "Configuration")  
    *   [Santé](#Sante "Santé")  
    *   [Routines](#Routines "Routines")  
    *   [Rappels/Alarmes](#RappelsAlarmes "Rappels/Alarmes")  
    *   [Historique](#Historique "Historique")  
    *   [Requêteur Info](#Requeteur_Info "Requêteur Info")  
    *   [Requêteur Action](#Requeteur_Action "Requêteur Action")  
*   [Les tuiles](#Les_tuiles "Les tuiles")
    *   [La tuile de l’équipement principal](#La_tuile_de_lequipement_principal "La tuile de l’équipement principal")  
    *   [La tuile du player multimédia](#La_tuile_du_player_multimedia "La tuile du player multimédia")  
    *   [La tuile de la playlist en cours](#La_tuile_de_la_playlist_en_cours "La tuile de la playlist en cours")
*   [Commandes simples](#Commandes_simples "Commandes simples")
    *   [Principe](#Principe "Principe")  
    *   [Prochaine Alarme](#Prochaine_Alarme "Prochaine Alarme")  
    *   [Prochaine Alarme Musicale](#Prochaine_Alarme_Musicale "Prochaine Alarme Musicale")  
    *   [Prochain Minuteur](#Prochain_Minuteur "Prochain Minuteur")  
    *   [Prochain Rappel](#Prochain_Rappel "Prochain Rappel")  
    *   [Faire parler Alexa en SSML](#Faire_parler_Alexa_en_SSML "Faire parler Alexa en SSML")  
    *   [Lancer une annonce (donc sur tous les appareils)](#Lancer_une_annonce_donc_sur_tous_les_appareils "Lancer une annonce (donc sur tous les appareils)")  
*   [Commandes complexes](#Commandes_complexes "Commandes complexes")
    *   [Principe](#Principe-2 "Principe")  
    *   [alarm?when=#when#&recurring=#recurring#&sound=#sound#](#alarmwhenwhen_recurringrecurring_soundsound "alarm?when=#when#&recurring=#recurring#&sound=#sound#")
    *   [reminder?text=#message#&when=#when](#remindertextmessage_whenwhen "reminder?text=#message#&when=#when")
    *   [whennextalarm?position=1&status=ON&format=hour](#whennextalarmposition1_statusON_formathour "whennextalarm?position=1&status=ON&format=hour")
    *   [Création de la commande INFO qui affichera le résultat de la commande whenNextAlarm](#Creation_de_la_commande_INFO_qui_affichera_le_resultat_de_la_commande_whenNextAlarm "Création de la commande INFO qui affichera le résultat de la commande whenNextAlarm")
    *   [Explication de l’interaction entre la commande ACTION et la commande INFO](#Explication_de_linteraction_entre_la_commande_ACTION_et_la_commande_INFO "Explication de l’interaction entre la commande ACTION et la commande INFO")  
    *   [whennextmusicalalarm?position=1&status=ON&format=hour](#whennextmusicalalarmposition1_statusON_formathour "whennextmusicalalarm?position=1&status=ON&format=hour")
    *   [musicalalarmmusicentity?position=1&status=ON](#musicalalarmmusicentityposition1_statusON "musicalalarmmusicentity?position=1&status=ON")
    *   [whennextreminder?position=1&status=ON](#whennextreminderposition1_statusON "whennextreminder?position=1&status=ON")
    *   [deleteallalarms?type=alarm&status=all](#deleteallalarmstypealarm_statusall "deleteallalarms?type=alarm&status=all")
    *   [history?maxRecordSize=50&recordType = ‘VOICE\_HISTORY’](#historymaxRecordSize50_recordType_%E2%80%98VOICE_HISTORY "history?maxRecordSize=50&recordType = ‘VOICE_HISTORY’")  
    *   [command?command=#command#](#commandcommandcommand "command?command=#command#")  
    *   [radio?station=#select#](#radiostationselect "radio?station=#select#")  
    *   [routine?routine=#select#](#routineroutineselect "routine?routine=#select#")  
    *   [Pour trouver l’ID Routine :](#Pour_trouver_lID_Routine "Pour trouver l’ID Routine :")  
    *   [playmusictrack?trackId=#select#](#playmusictracktrackIdselect "playmusictrack?trackId=#select#")  
    *   [Autres fonctionnalités](#Autres_fonctionnalites "Autres fonctionnalités")
    *   [Modifier l’icone des players](#Modifier_licone_des_players "Modifier l’icone des players")  
*   [Utilisation de balises pour les interjections et les sons](#Utilisation_de_balises_pour_les_interjections_et_les_sons "Utilisation de balises pour les interjections et les sons")
    *   [Les sons de la bibliothèque Amazon](#Les_sons_de_la_bibliotheque_Amazon "Les sons de la bibliothèque Amazon")  
    *   [Les interjections](#Les_interjections "Les interjections")  
    *   [Enchaînement texte et interjection](#Enchainement_texte_et_interjection "Enchaînement texte et interjection")  
*   [Slider du Volume](#Slider_du_Volume "Slider du Volume")
    *   [Personnaliser le widget](#Personnaliser_le_widget "Personnaliser le widget")  
    *   [Revenir au précédent Widget](#Revenir_au_precedent_Widget "Revenir au précédent Widget")  
    *   [Amélioration de la disposition du widget](#Amelioration_de_la_disposition_du_widget "Amélioration de la disposition du widget")  
    *   [Supprimer le logo haut-parleur](#Supprimer_le_logo_haut-parleur "Supprimer le logo haut-parleur")  
*   [Information Mute](#Information_Mute "Information Mute")  


Installation du Plugin Alexa Premium
------------------------------------

### Installer le Plugin depuis le Market

![installationalexaapi1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi1.png)

![installationalexaapi3](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi3.png)

**Note sur les versions :**

Vous avez le choix entre la version **Stable** ou la version **Beta**.

Beaucoup de nouvelles fonctionnalités sont toujours plus présentes sur la Beta que sur la Stable mais elles sont en test.
Si vous êtes joueur et curieux, vous pouvez installer la version Béta.
Nota  Vous n’avez pas besoin d’installer Jeedom en Beta (c’est plutôt déconseillé d’ailleurs) pour installer le plugin en Béta.
Vous pouvez assez facilement passer d’une version Beta à une version Stable et réciproquement, il suffit de réinstaller par dessus l’autre version.

![installationalexaapi4](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi4.png)

### Activer le Plugin

![installationalexaapi5](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi5.png)

### Recharger les dépendances

![installationalexaapi6](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi6.png)

### Générer manuellement le cookie Amazon

![installationalexaapi7](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi7.png)

### S’identifier sur la pop-up d’Amazon

![installationalexaapi8](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi8.png)

Fermer la fenêtre dès que le Cookie Amazon est créé.

### Lancer le Daemon s’il ne se lance pas tout seul

![installationalexaapi9](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi9.png)

### Lancer le SCAN

![installationalexaapi10](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi10.png)

Les devices apparaissent, aller dans un device et dans **Commandes** lancer un **Speak** pour tester

Et voilà, Alexa parle en moins de 5 min !!

Mise à jour ou Changement de version
------------------------------------

Que faire ?

Le plugin et son API étant vivants (Amazon n’ayant pas documenté l’API se permet de modifier au fil de l’eau ses protocoles), les mises à jour permettent d’apporter des corrections dans les liens entre le plugin et le serveur Amazon.

**Trois solutions pour avoir une installation opérationnelle :**

*   **Supprimer tous les équipements Amazon et leurs commandes et les recréer**
*   **Forcer la mise à jour de toutes les commandes**
*   **Lancer un SCAN qui détecte les nouveaux équipements ou les nouvelles commandes**

**Le choix entre ces trois solutions dépend du nombre de scénarios que vous avez développés grâce au Plugin Alexa Premium. En effet, la première solution supprime tous les devices et toutes les commandes, elle supprimera donc celles-ci dans vos scénarios. La seconde solution est plus respectueuse de vos scénarios car elle mettra à jour vos commandes sans les supprimer et donc vos scénarios seront intacts mais si elle ne fonctionne pas, vous devrez utiliser la solution 1.  
**

### Solution 1 : Supprimer tous les équipements et leurs commandes et les recréer
C’est le mode le plus **propre** et le plus **optimisé** puisque vous repartez avec une installation comme neuve des devices et de leurs commandes.
Pour se faire, il faut utiliser le bouton ![boutonalexaapiv21](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi1.png)
**Attention**, cette fonction supprime tous les équipements et leurs commandes, vous perdez donc tous les liens dans vos scénarios.

### Solution 2 : Forcer la mise à jour de toutes les commandes
C’est le mode le plus **simple** et **sans risque** puisque vos équipements et leurs commandes ne sont pas supprimés. Ce forçage n’impacte donc pas vos scénarios.
Pour se faire, il faut utiliser le bouton ![boutonalexaapiv22](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi2.png)
Si vous ne souhaitez pas lancer le forçage de mise à jour sur **toutes** les commandes de **tous** les équipements, vous pouvez le lancer sur un seul équipement (et donc sur toutes ses commandes). Pour cela, rendez vous sur l’équipement concerné et cliquez sur :

![boutonalexaapiv23](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi3.png)

### Solution 3 : Le SCAN
![boutonalexaapiv24](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi4.png)
Notez enfin que le scan peut être lancé à tout moment, il n’impacte pas les équipements déjà détectés ni les commandes existantes, par contre, il recrée tous les **nouveaux** devices ou les devices **supprimés**. Il recrée également toutes les **nouvelles** commandes ou les **commandes** supprimées.

Les écrans de gestion
---------------------
![Screenshot 2019 10 27 Alexaapi Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-10-27_Alexaapi_-_Jeedom.png)

### Scan
Permet de lancer automatiquement la détection de tous vos devices, vous pouvez le lancer quand vous le souhaitez, il ne supprime jamais de device ou de commande, pas de risque.

### Configuration
C’est tout le moteur de paramétrage. si quelque chose ne semble pas assez intuitif, merci de nous le signaler, nous le documenterons ou le rendrons plus simple.

### Santé
Donne des indications sur la santé de vos équipements

### Routines
Donne la liste des routines enregistrées sur votre compte Amazon et permet de les lancer manuellement

### Rappels/Alarmes
Donne la liste de vos alarmes ou rappels, permet de les supprimer. La désactivation manuelle ne fonctionne pour l’instant plus.

### Historique
C’est tout l’historique de l’activité de vos équipements Amazon, donne l’indication de succès le cas échéant.

### Requêteur Info
Réservé aux utilisateurs avertis, il permet de questionner le serveur Amazon

### Requêteur Action
Réservé aux utilisateurs très avertis, il permet de lancer des requêtes brutes au serveur Amazon

Les tuiles
----------

A ce jour, chaque équipement peut générer 3 tuiles.

*   La tuile principale de l’équipement avec ses intéractions avec vous, vos ordres de speak, les alarmes/rappels, le volume et la possibilité de lancer les routines
*   La tuile du player multimédia
*   La tuile de la playlist en cours

### La tuile de l’équipement principal
![widgetprincipal](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetprincipal.jpg)

**A** : C’est la dernière intéraction avec vous, notez que vous pouvez récupérer cette information et l’utiliser dans un scénario.
**B** : Vous pouvez lancer une routine en la sélectionnant dans la liste déroulante
**C** : Le volume, notez qu’il se met à jour si vous modifiez le volume sur l’appareil. (Le volume d’un groupe est imposé à tous les devices du groupe)
**D/E/F** : C’est la prochaine alarme, alarme musicale ou rappel.
**G** : C’est un formulaire qui permet de faire parler Alexa.

### La tuile du player multimédia
![widgetplayer](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetplayer.jpg)

### La tuile de la playlist en cours
![widgetplaylist](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetplaylist.jpg)

Commandes simples
-----------------

### Principe
Les commandes simples sont préinstallées à la détection des devices, vous disposez ainsi de commandes immédiatement utilisables.
Les commandes préinstallées peuvent être utilisées en l’état dans des scénarios.
Sachez que toutes les commandes peuvent faire l’objet d’une adaptation personnelle, les utilisateurs avertis pourront créer leurs commandes et les personnaliser grâce aux paramètres possibles de chaque commande.
Cette documentation ne s’attarde que peu sur les commandes simples car leur utilisation est réfléchie pour être intuitive, par contre, les commandes complexes sont détaillées dans le prochain chapitre.

### Prochaine Alarme

### Prochaine Alarme Musicale

### Prochain Minuteur

### Prochain Rappel
Ces 4 commandes INFO fonctionnent de la même manière.
Elles sont mises à jour automatiquement par le plugin (par MQTT et par CRON)
Le résultat est donné au format suivant : **2019-12-02 21:10:00**
Si vous le voulez dans un autre format **2110** par exemple, [un tuto explique comment faire.](nextHtml.html)

### Faire parler Alexa en SSML
Amazon a intégré le SSML à ses équipements et cela permet de rendre extrêmement naturel la manière de parler. Vous pouvez personnaliser davantage les phrases en fournissant des détails sur les pauses, ainsi que la mise en forme audio des acronymes, des dates, des heures, des abréviations…, vous pouvez également choisir la langue de lecture, une citation ou une expression en langue étrangère pourra ainsi être lue avec l’accent étranger dans un texte de votre langue d’origine.

Contrairement aux autres commandes permettant de faire parler Alexa, sur cette commande, le choix a été fait de respecter scrupuleusement la syntaxe du protocole SSML, balises comprises. Il faudra donc utiliser les balises d’ouverture et de fermeture et être rigoureux dans la manière de coder ces phrases.

#### Voici des exemples :

<speak>
<voice name="Conchita">
<prosody  rate="medium" pitch="high">
Yé m'appel Conchita. yé fé lé ménache partou dans la maichon.</prosody></voice>
</speak>

ou

<speak>
Bonjour je peux lire du <say-as interpret-as="characters">SSML</say-as>.
Je peux faire une pause <break time="3s"/>.
Un chiffre cardinal <say-as interpret-as="cardinal">10</say-as>.
en ordinal <say-as interpret-as="ordinal">10</say-as>.
ou digit <say-as interpret-as="characters">10</say-as>.
</speak>

ou encore

<speak><amazon:effect name="whispered">Bonjour, je suis un fantôme</amazon:effect></speak>

#### Quelques liens intéressants :

*   [La référence W3C sur le SSML](https://www.w3.org/TR/speech-synthesis11/)  
*   [La documentation d’Amazon sur le SSML (en anglais)](https://developer.amazon.com/fr-FR/docs/alexa/custom-skills/speech-synthesis-markup-language-ssml-reference.html)  
    
*   [La documentation de Google sur le SSML (en français)](https://cloud.google.com/text-to-speech/docs/ssml)  
*   [Les **interjections** françaises programmées sur les devices Amazon (intéressant !!!)](https://developer.amazon.com/fr-FR/docs/alexa/custom-skills/speechcon-reference-interjections-french.html)  
    

### Lancer une annonce (donc sur tous les appareils)

A ce jour, nous ne savons pas encore exploiter la commande « Annonce » présente dans l’appli smartphone Alexa, mais nous pouvons faire la même chose grâce à la la commande « Parler à Alexa »

Ainsi, pour faire une annonce « Le facteur est passé, il faut faire (dans un scénario) :

*   Commande **Parler à Alexa**
*   Mettre le **message** dans Message **Alexa annonce le facteur est passé**

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FireShot-Capture-068-Boite-aux-lettres-Cote-Facteur-Jeedom-192.168.1.100.png)

Commandes complexes
-------------------

### Principe

Les commandes simples (paragraphe précédent) sont préinstallées à la détection des devices, vous disposez ainsi de commandes immédiatement utilisables.
Les commandes complexes sont accessibles aux utilisateurs expérimentés et leur utilisation est bien plus difficile mais elles sont bien plus puissantes.
Notez que les commandes simples peuvent être personnalisées. Elles deviendront des commandes complexes.
Pour cela, utilisez le bouton (sous le tableau des commandes) :
![boutonajoutercommandeaction](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonajoutercommandeaction.png)

Vous pouvez vous aider des commandes préinstallées pour en copier la syntaxe et utilisez la documentation ci dessous pour connaitre toutes les options possibles. Si vous souhaitez une autre fonction, un autre format ou que vous ne trouvez pas votre bonheur, contacter l’équipe de création du plugin, il y aura toujours une solution pour vous.

**Nota** : Pour que la commande « **Ajouter une commande action** » soit active, il faut cocher cette case dans la configuration du plugin :
![Screenshot 2019 12 14 Alexa API Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-12-14_Alexa_-_API_-_Jeedom.png)

### alarm?when=#when#&recurring=#recurring#&sound=#sound#
Cette commande permet d’ajouter une alarme au device dans lequel est créée la commande.

#### Voici les options :
*   **when=YYYY-MM-DD HH24:MI:SS**
exemple : 2019-12-31 21:36:00
Notez que les alarmes sont différentes des rappels et doivent être dans un créneau de 24h (une alarme pour le 31/12 au mois d’avril est impossible contrairement aux Rappels)
Ainsi, si aucune récurrence n’est programmée (par le paramètre recuring), seule l’heure est prise en compte, le jour est ignoré par Amazon.

*   **recurring=#recurring#**

La programmation de ce paramètre est bien plus aisé par un scénario puisqu’une liste déroulante vous permet de facilement choisir la récurrence. Mais cela peut être fait manuellement dans une commande action avec le codage suivant :

P1D=Tous les jours  
XXXX-WD=En semaine  
XXXX-WE=Week-ends  
XXXX-WXX-1=Chaque lundi  
XXXX-WXX-2=Chaque mardi  
XXXX-WXX-3=Chaque mercredi  
XXXX-WXX-4=Chaque jeudi  
XXXX-WXX-5=Chaque vendredi  
XXXX-WXX-6=Chaque samedi  
XXXX-WXX-7=Chaque dimanche

*   **sound=#sound#**

Il s’agit du son de l’alarme, #sound# peut être remplacé par :

**system\_alerts\_melodic\_01** pour Alarme simple  
**system\_alerts\_melodic\_01** pour Timer simple  
**system\_alerts\_melodic\_02** pour A la dérive  
**system\_alerts\_atonal\_02** pour Métallique  
**system\_alerts\_melodic\_05** pour Clarté  
**system\_alerts\_repetitive\_04** pour Comptoir  
**system\_alerts\_melodic\_03** pour Focus  
**system\_alerts\_melodic\_06** pour Lueur  
**system\_alerts\_repetitive\_01** pour Table de chevet  
**system\_alerts\_melodic\_07** pour Vif  
**system\_alerts\_soothing\_05** pour Orque  
**system\_alerts\_atonal\_03** pour Lumière du porche  
**system\_alerts\_rhythmic\_02** pour Pulsar  
**system\_alerts\_musical\_02** pour Pluvieux  
**system\_alerts\_alarming\_03** pour Ondes carrées

### reminder?text=#message#&when=#when

Cette commande permet d’ajouter un rappel au device dans lequel est créée la commande.

#### Voici les options :

*   **when=YYYY-MM-DD HH24:MI:SS**

exemple : 2019-12-31 21:36:00

*   **text=#message#**
Vous avez la possibilité de donner un titre à votre rappel.

### whennextalarm?position=1&status=ON&format=hour
_**Nota : Cette commande est masquée, c’est elle qui donne le résultat dans la commande info : Next Alarm Hour**_

Cette commande permet de renvoyer la prochaine alarme du device dans lequel est créée la commande.
Attention, cette commande est une commande **ACTION**, elle doit être reliée à une commande **INFO** qui affichera le résultat, regardez l’explication en dessous de la description des options.

#### Voici les options :

#### position=x
*   Mettre **1** pour la prochaine alarme
*   **2** pour la suivante
*   et ainsi de suite

_Par défaut, position=1 si non spécifié_
#### status=x

*   Mettre **ON** pour prendre en compte uniquement  les alarmes actives
*   Mettre **OFF** pour prendre en compte uniquement les alarmes inactives
*   Mettre **ALL** pour prendre en compte toutes les alarmes

_Par défaut, status=ON si non spécifié_

#### format=x
*   Mettre **hour** pour avoir un résultat au format **HH:MM** _(Attention, cet affichage est dangereux dans le cas où vous programmez des alarmes au dela de 24h, cela est possible avec les répétitions)_
*   Mettre **hhmm** pour avoir un résultat au format **HHMM**
*   Mettre **full** pour avoir un affichage détaillé **yyyy-MM-dd’T’HH:mm:ss.SSS**
_Par défaut, format=hhmm si non spécifié_
Nota : Si vous avez besoin d’un autre format, n’hésitez pas à me le demander, je l’ajouterai dans la prochaine version.

### Création de la commande **INFO** qui affichera le résultat de la commande **whenNextAlarm**
La commande INFO qui vous donnera le résultat de le la commande WhenNextAlarm sera créée automatiquement dès que le champ **Nom de la commande Info** se trouvant dans la colonne **Résultat dans** sera rempli.

### Explication de l’interaction entre la commande ACTION et la commande INFO
*   Quand vous lancez la commande ACTION, le serveur Amazon est interrogé et la résultat est affecté à la commande INFO
*   Quand vous lancez la commande INFO, Jeedom vous donnera donc le résultat de la commande ACTION correspondante

_(Tout cela est conçu dans la même logique que le plugin Virtual)_

Nota : S’il n’y aura pas d’alarme prochaine, le serveur répond « none ».

### whennextmusicalalarm?position=1&status=ON&format=hour

_**Fonctionne comme whennextalarm mais pour les alarmes musicales.**_

### musicalalarmmusicentity?position=1&status=ON

_****Fonctionne comme whennextmusicalalarm mais fournit l’information** MusicEntity **qui correspond à ce qui va être joué à l’heure de l’alarme.****_

### whennextreminder?position=1&status=ON

**Nota : Cette commande est masquée, c’est elle qui donne le résultat dans la commande info : Next Reminder Hour**

Cette commande donne le prochain rappel, elle fonctionne exactement comme WhenNextAlarm.

### deleteallalarms?type=alarm&status=all

Cette commande supprime tous les rappels et/ou alarmes du device dans lequel est créée la commande.

#### Voici les options :

#### type=x
*   Mettre **alarm** pour ne supprimer que les alarmes
*   Mettre **reminder** pour ne supprimer que les rappels
*   Mettre **all** pour supprimer les alarmes et les rappels

_Par défaut, type=alarm si non spécifié_

#### status=x
*   Mettre **ON** pour ne supprimer que les alarmes et/ou rappels actifs
*   Mettre **OFF** pour ne supprimer que les alarmes et/ou rappels inactifs
*   Mettre **ALL** pour supprimer toutes les alarmes et/ou rappels

_Par défaut, status=ON si non spécifié_

**Nota** : Pour que la suppression fonctionne, il faut que l’Alexa soit connecté !!

### history?maxRecordSize=50&recordType = ‘VOICE\_HISTORY’
Toute nouvelle commande en test qui va chercher l’historique.

*   maxRecordSize indique le nb d’enregistrement à remonter (50 sur le plugin)
*   recordType est probablement le type d’enregistrement, VOICE\_HISTORY est la valeur par défaut, on ne connait pas les autres valeurs possibles.

### command?command=#command#
Cette commande envoie une commande au device dans lequel est créée la commande.

#### Deux manières d’utiliser cette commande :

*   **Avec un scénario**
En passant par un scénario, vous laissez **command?command=#command#** comme commande action et vous aurez une liste déroulante dans le scénario, la liste déroulante vous propose toutes les commandes possibles.

*   **Avec une commande directe**
Dans ce cas, c’est au niveau des commandes du device que vous allez créer une commande action par commande à envoyer à Alexa.
Vous utiliserez ainsi la syntaxe suivante : **command?command=play** pour lancer un **play** et **command?command=pause** pour faire une **pause** et ainsi de suite avec les commandes : **pause play next prev fwd rwd shuffle repeat**

_Nota_ : STOP n’existe pas chez Amazon, il faut utiliser PAUSE

### radio?station=#select#
Cette commande lance une station de radio sur le device dans lequel est créée la commande.

Pour une meilleure utilisation en Dashboard, cette commande a été simplifiée. On peut maintenant sélectionner la radio souhaitée plutôt que de connaitre par cœur le code (s0000) de la radio.
Ainsi, il faut dans un premier temps « configurer » ses stations de radio dans la partie commandes du player qui va la lire.
![Screenshot 2019 11 08 Alexaapi Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-11-08_Alexaapi_-_Jeedom.png)
Par défaut, sont configurés : s2960|Nostalgie;s6617|RTL;s6566|Europe1
Il suffit de respecter le format idStation1|Nomstation1;idStation2|Nomstation2
Une fois vos stations configurées, vous pourrez les choisir sur le widget de la radio :
![radios](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/radios.jpg)

#### Les stations :
Pour trouver les id des stations, allez sur le site [https://tunein.com/radio/home/](https://tunein.com/radio/home/)
Vous choisissez votre radio, et pour avoir l’id, cliquez sur partager, vous verrez dans le lien quelque chose qui commence par un s suivi de chiffres, c’est l’id.
**Notez que le plugin est capable de vous donner l’id de la station en cours de lecture,** la procédure est identique à TrackID, [regardez ici](#playmusictrack)

#### Utilisation d’une commande radio dans un scénario
Pour utiliser une commande radio dans un scénario, il faut être un utilisateur expérimenté (dans la config) et savoir créer une nouvelle commande (dans le device player) :

![Screenshot 2019 11 08 Alexa API Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-11-08_Alexa_-_API_-_Jeedom.png)
![Screenshot 2019 11 08 Alexa API Jeedom1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-11-08_Alexa_-_API_-_Jeedom1.png)
Sur cette nouvelle commande, on configure de manière très simple en figeant l’id de la station (ou en utilisant une variable), par exemple :
![Screenshot 2019 11 08 Alexaapi Jeedom1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-11-08_Alexaapi_-_Jeedom1.png)

### routine?routine=#select#
Cette commande lance la routine spécifiée.

#### Deux manières d’utiliser cette commande :
*   **Avec un scénario**
En passant par un scénario, vous laissez **routine?routine=#select#** comme commande action et dans le scénario, dans le champ « ID routine », spécifiez l’identifiant de la routine, cf. paragraphe ci dessous pour trouver cet identifiant.

*   **Avec une commande directe**
Dans ce cas, c’est au niveau des commandes du device que vous allez créer une commande action.
Vous utiliserez ainsi la synthaxe suivante : **routine?routine=xxxxx** pour lancer la routine dont l’ID est **xxxx**

### Pour trouver l’ID Routine :
Vous pouvez trouver facilement l’ID des routines en consultant l’écran « Routines » du plugin, dernière colonne de droite.

### playmusictrack?trackId=#select#

Cette commande lance la lecture de la piste de lecture Amazon music par son numéro de trackID.
Les trackID se configurent dans la commande action **Ecouter une piste musicale** dans votre équipement device, il s’agit d’une liste déroulante, donc avec la syntaxe suivante :
_53bfa26d-f24c-4b13-97a8-8c3debdf06f0|Piste1;7b12ee4f-5a69-4390-ad07-00618f32f110|Piste2_
Vous pouvez donc modifier vos pistes et leurs noms.

![Screenshot 2019 11 03 Alexaapi Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2019-11-03_Alexaapi_-_Jeedom.png)
Une fois la commande configurée, vous n’aurez plus qu’à utiliser la liste déroulante qui sera proposée, autant sur le Dashboard que dans les scénarios

#### Comment trouver le trackID d’une piste Amazon-Music ?
Le plugine Alexa Premium est capable de vous donner le trackID de la piste qui est en cours de lecture.
Pour cela, suivez ces étapes :
*   Allez dans les commandes de l’équipement que vous utilisez et cochez la case **Afficher** de la commande **Amazon Music Id**

![amazonmusicidtrack](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/amazonmusicidtrack.png)
*   Une note de musique va apparaitre sur le Dashboard, sur la tuile de votre équipement, c’est ici qu’apparaitra l’ID
*   Lancez la musique et relevez l’information ainsi affichée

![amazonmusicidtrack2](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/amazonmusicidtrack2.png)
*   Vous gardez ou pas l’information sur votre dashboard, pour la supprimer, décochez **Afficher** de la commande **Amazon Music ID**
Notez que cela fonctionne également pour trouver l’ID d’une station de musique lançable avec **radio?station=#select#**
_Il a été constaté par contre que pour certaines playlist, l’ID ne remontait pas. Pour être certain de l’avoir, lancer uniquement la piste que vous souhaitez (et non dans une playlist)._

Autres fonctionnalités
----------------------

### Modifier l’icone des players

Les images des tuiles des players sont les images envoyées par les serveurs des fournisseurs de musique.
Ces images sont des liens **temporaires** et donc vous pouvez vous retrouver avec des images vides. Cela donne cela :

![tuile](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/tuile.png)
Pour éviter cela, les players ont été modifiés et en cas d’absence d’image, la miniature du lecteur est affichée, cela donne :

![tuile2](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/tuile2.png)
Si vous souhaitez modifier l’image, il suffit de remplacer le fichier **logourl.png** qui se trouve dans :

**plugins/alexaamazonmusic/core/config** (par exemple, modifiez amazonmusic pour les autres players)

Utilisation de balises pour les interjections et les sons
---------------------------------------------------------

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa.png)

Pour la fonctionnalité « Faire parler Alexa » mais cela fonctionne également pour les autres méthodes pour faire parler Alexa, il est mis en place deux nouveautés. Les interjections et les sons de la bibliothèque.

*   Les interjections FR sont décrites **[ici](https://developer.amazon.com/en-US/docs/alexa/custom-skills/speechcon-reference-interjections-french.html)**, les autres pays ont aussi leur page.
*   Les sons de la bibliothèque sont décrits **[ici](https://developer.amazon.com/en-US/docs/alexa/custom-skills/ask-soundlibrary.html)**

Pour faciliter l’envoi de commandes, il a été imaginé dans Alexa Premium un système de balises.

### Les sons de la bibliothèque Amazon

Par exemple, au lieu d’envoyer

> <audio src= »soundbank://soundlibrary/animals/amzn\_sfx\_lion\_roar\_01″/>

on peut tout simplement ajouter

> #animals/amzn\_sfx\_lion\_roar\_01#

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa2.png)

Lancera le rugissement d’un lion.

Beaucoup d’ autres sons sont dans [la bibliothèque de sons d’Amazon](https://developer.amazon.com/en-US/docs/alexa/custom-skills/ask-soundlibrary.html).

### Les interjections

Sur un principe similaire aux sons de la bibliothèque, les interjections sont à mettre entre balise #

Exemple :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa3.png)

### Enchaînement texte et interjection

Attention, contrairement aux sons qui peuvent être noyés dans les phrases, les interjections doivent être dans des phrases séparées, ainsi cet exemple ne fonctionnera pas :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa4.png)

Pour que l’interjection soit prise en compte, il faut la mettre dans une phrase séparée, donc ajouter un point :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa5.png)

Slider du Volume
----------------

Dans la version Avril 2021, le slider du volume a totalement été revu.
Le widget de _Noodom_ (un très grand merci à lui) a été refondu et intégré dans les widgets.
Le widget ressemblait à :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume1.png)

(Utilisez _Alexaapi/Volume\_legacy_ maintenant pour avoir ce widget)

et il devient :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume2.png)

_(Correspond maintenant à Alexaapi/Volume)_
L’encombrement est le même pour être compatible avec les designs personnalisés de chacun.

### Personnaliser le widget

Le widget est totalement personnalisable, il suffit d’utiliser les variables du widget [NooSlider,](https://github.com/noodom/jeedom_widgets/tree/master/nooSlider) une doc est disponible avec tous les [paramètres facultatifs](https://github.com/noodom/jeedom_widgets/tree/master/nooSlider#facultatif-param%C3%A8tres-de-la-commande-associ%C3%A9e-au-widget).

Par défaut, les paramètres envoyés sont :

*   displayedValues = « 0,20,40,60,80,100 »
*   step = 10
*   width = 200
*   height = 50
*   handleSize =15
*   cursorLeftPos =50
*   cursorTopPos = 88
*   paramètre top non définit par défaut, cf un peu plus bas.

Pour personnaliser votre widget, vous pouvez ajouter le paramètre en _Paramètres optionnels widget_ de la commande action « Volume », par exemple :

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume3.png)

### Revenir au précédent Widget

Dans l’hypothèse où vous souhaitiez conserver l’ancien widget, pas de panique, il est toujours dans le plugin. Sélectionner Volume\_legacy 😉

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume4.png)

### Amélioration de la disposition du widget
Un paramètre top est disponible pour caler l’**espacement haut du widget**, il suffit de le spécifier ainsi :
![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume7.png)

### Supprimer le logo haut-parleur
Si le petit logo du haut-parleur qui indique le son, hérité de la version précédente du plugin vous gène :
![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume5.png)

Pas de panique, il est tout simple de le supprimer, allez sur votre équipement, puis dans commandes, cliquez sur ce petit logo en question tout à gauche, il va disparaitre et sauvegardez. Il n’est plus là. (si « Volume » apparait, décocher « Afficher le nom » dans les options du Widget)

![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetvolume6.png)

Information Mute
----------------
Une nouvelle information arrive sur le widget des équipements Alexa.
C’est l’information **Mute** qui apparait quand on dit à Alexa : _Alexa coupe le son_.
![](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetmute.png)

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

Alexa Premium (et AmazonMusic/Deezer/Spotify/FireTv/Todo-List) Changelog
------------------------------------------------------------------------

![alexaapiv2 icon](https://market.jeedom.com/filestore/market/plugin/images/alexaapiv2_icon.png)

*   [Présentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#presentation)
*   [Documentation](https://limad.github.io/plugins-docs/plugin-alexaapiv2#documentation)
*   Changelog
*   [Forum dédié](https://community.jeedom.com/tags/plugin-alexaapiv2)

[![alexaamazonmusic icon](https://market.jeedom.com/filestore/market/plugin/images/alexaamazonmusic_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation) [![alexaspotify icon](https://market.jeedom.com/filestore/market/plugin/images/alexaspotify_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-spotify-documentation) [![alexadeezer icon](https://market.jeedom.com/filestore/market/plugin/images/alexadeezer_icon.png)](http://jeedom.sigalou-domotique.fr/alexa-deezer-documentation)

Créer un skill Amazon
---------------------


Créer un compte développeur sur Amazon : [https://developer.amazon.com/](https://developer.amazon.com/)
---------------------
Une fois connecté, cliquez sur « Alexa »

[![](https://youdom.net/wp-content/uploads/2024/04/image-1.png)](https://youdom.net/wp-content/uploads/2024/04/image-1.png)

Cliquez sur « Ajouter des Skills à Alexa »
[![](https://youdom.net/wp-content/uploads/2024/04/image-2.png)](https://youdom.net/wp-content/uploads/2024/04/image-2.png)

Cliquez sur « Développez votre Skill »
[![](https://youdom.net/wp-content/uploads/2024/04/image-3.png)](https://youdom.net/wp-content/uploads/2024/04/image-3.png)

Cliquez sur « Créer un Skill »
[![](https://youdom.net/wp-content/uploads/2024/04/image.png)](https://youdom.net/wp-content/uploads/2024/04/image.png)

Définissez un nom (ex: ask Jeedom) et la langue FR, puis cliquez sur « Next » en haut à droite de votre écran
[![](https://youdom.net/wp-content/uploads/2024/04/image-4.png)](https://youdom.net/wp-content/uploads/2024/04/image-4.png)

Cliquez sur « Other », Custom et cliquez sur « Sync Locales », puis sur Next en haut à gauche de votre écran.
[![](https://youdom.net/wp-content/uploads/2024/04/image-5.png)](https://youdom.net/wp-content/uploads/2024/04/image-5.png)

Cliquez sur « Start from Scratch » puis sur « Next » en haut à gauche de votre écran
[![](https://youdom.net/wp-content/uploads/2024/04/image-8.png)](https://youdom.net/wp-content/uploads/2024/04/image-8.png)

Cliquez sur « Create Skill »
[![](https://youdom.net/wp-content/uploads/2024/04/image-10.png)](https://youdom.net/wp-content/uploads/2024/04/image-10.png)

Cliquez sur « Next », puis à nouveau sur « Next » et pour finir sur « Done »
[![](https://youdom.net/wp-content/uploads/2024/04/image-11.png)](https://youdom.net/wp-content/uploads/2024/04/image-11.png)

Cliquez sur « 1. Invocation Name > »
[![](https://youdom.net/wp-content/uploads/2024/04/image-13.png)](https://youdom.net/wp-content/uploads/2024/04/image-13.png)

Renseignez « pose question » dans le champ Skill Invocation Name (phrase de déclenchement pour les tests), puis sur « Enregistrer »
[![](https://youdom.net/wp-content/uploads/2024/04/image-14.png)](https://youdom.net/wp-content/uploads/2024/04/image-14.png)

Cliquez sur code, puis sur « Importer Code » (télécharger le fichier zip du Skill)
[![](https://youdom.net/wp-content/uploads/2024/04/image-15.png)](https://youdom.net/wp-content/uploads/2024/04/image-15.png)

Importer le fichier zip télécharger précédemment et cliquez sur « Next ».
[![](https://youdom.net/wp-content/uploads/2024/04/image-17.png)](https://youdom.net/wp-content/uploads/2024/04/image-17.png)

Cochez les cases comme ci-dessous, puis sur « Next » et pour finir sur « Import » dans la fenêtre suivante.
[![](https://youdom.net/wp-content/uploads/2024/04/image-16.png)](https://youdom.net/wp-content/uploads/2024/04/image-16.png)

Renseignez les 3 champs avec vos informations (urlJeedom, apiKey et lang), cliquez sur « Save », puis sur « Build » dans le menu du haut
[![](https://youdom.net/wp-content/uploads/2024/04/image-18.png)](https://youdom.net/wp-content/uploads/2024/04/image-18.png)

Cliquez sur « Interaction model » puis sur « JSON Editor »
[![](https://youdom.net/wp-content/uploads/2024/04/image-20.png)](https://youdom.net/wp-content/uploads/2024/04/image-20.png)

Uploadez le fichier « skill.json » qui se trouve le fichier zip que vous avez téléchargé précédemment
[![](https://youdom.net/wp-content/uploads/2024/04/image-21.png)](https://youdom.net/wp-content/uploads/2024/04/image-21.png)

Une fois vote fichier uploadé, cliquez sur « Enregistrer », puis sur « Développez vos compétences »
[![](https://youdom.net/wp-content/uploads/2024/04/image-22.png)](https://youdom.net/wp-content/uploads/2024/04/image-22.png)

Félicitations, vous venez de créer votre Skill Ask pour Alexa Premium sur Jeedom !!!

Vous pouvez afficher les logs des scripts en cliquant sur « Code » dans le menu du haut puis sur « CloudWatch Logs »
[![](https://youdom.net/wp-content/uploads/2024/04/image-23.png)](https://youdom.net/wp-content/uploads/2024/04/image-23.png)

Le Skill doit être appelé par le plugin Alexa Premium pour qu’il fonctionne correctement. Voir documentation Alexa API Premium



#### Dev et collaborateurs
Toutes les bonnes volontées sont les bienvenues, travail collectif sur ce plugin.
Que vous soyez programmeur, développeur, utilisateur ou plein de bonne voloonté, il y a des choses à faire.

**Nous aurions besoin de traducteurs pour rendre international ce plugin.**
La documentation est à réaliser, des tutos probablement utiles …