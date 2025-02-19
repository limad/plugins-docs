[Sigalou](https://www.sigalou-domotique.fr/author/sigalou) [Domotique](https://www.sigalou-domotique.fr/category/domotique), [Jeedom](https://www.sigalou-domotique.fr/category/domotique/jeedom-2)

Alexa-Spotify Documentation
---------------------------

![alexaamazonmusic icon](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/alexaspotify_icon.png)

*   **Documentation**
*   [Todo-List / Changelog](https://www.sigalou-domotique.fr/alexa-api-changelog)
*   Forum dédié
*   [Chat beta-testeurs](https://alexaapi.slack.com)

Présentation du plugin
----------------------

![alexaspotify screenshot1](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/alexaspotify_screenshot1.png)

Plugin qui vient compléter les possibilités d’Alexa en apportant le lien avec Spotify.

Ce plugin totalement orienté multimédia est en cours de développement, **à ce jour,** le lancement de la lecture des playlists et des pistes musicales n’est pas encore opérationnelle, il faudra demander à Alexa de les lancer. Par contre, la lecture des radios fonctionne.

Les widgets Player permettent de rendre l’utilisation de Spotify très simple et conviviale.

Le multiroom est pris en compte et la diffusion peut être simultanée sur plusieurs Alexa

La [TODOList et le ChangeLog](https://jeedom.sigalou-domotique.fr/alexa-api-changelog) font l’ojet d’une page spéciale.

Une [comparaison entre Alexa-AmazonMusic, Alexa-Spotify et Alexa-Deezer](https://jeedom.sigalou-domotique.fr/comparaison-alexa-amazonmusic-alexa-spotify-alexa-deezer) est disponible.

Installation du Plugin Alexa-Spotify
------------------------------------

Très important, Alexa-Spotify a besoin du [plugin Alexa-API](https://jeedom.sigalou-domotique.fr/alexa-api-documentation) _(gratuit)_ pour dialoguer avec vos Alexa donc :

### Étape 1 : Installer le [plugin Alexa-API](https://jeedom.sigalou-domotique.fr/alexa-api-documentation)

### Étape 2 : Installer le Plugin Alexa-Spotify

#### depuis le Market, catégorie Multimédia ou en tapant son nom dans le champ de recherche

**Note sur les versions :**

Vous avez le choix entre la version **Stable** ou la version **Beta**.

Beaucoup de nouvelles fonctionnalités sont toujours plus présentes sur la Beta que sur la Stable mais elles sont en test.

Si vous êtes joueur et curieux, vous pouvez installer la version Béta.

Nota  Vous n’avez pas besoin d’installer Jeedom en Beta (c’est plutôt déconseillé d’ailleurs) pour installer le plugin en Béta.

Vous pouvez assez facilement passer d’une version Beta à une version Stable et réciproquement, il suffir de réinstaller par dessus l’autre version.

### Étape 3 : Activer le Plugin

###  ![Screenshot 2020 02 04 Alexa API Jeedom](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexa_-_API_-_Jeedom.png)

### Étape 4 : Retourner dans le [plugin Alexa-API](https://jeedom.sigalou-domotique.fr/alexa-api-documentation)

### ![Screenshot 2020 02 04 Alexaamazonmusic Jeedom](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexaamazonmusic_-_Jeedom.png)

### Étape 5 : Relancer le SCAN

![Screenshot 2020 02 04 Alexaapi Jeedom](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexaapi_-_Jeedom.png)

Les devices Multilmédia vont être générés automatiquement

Allez dans Alexa-Spotify pour les visualiser

![Screenshot 2020 04 19 Alexaapi Jeedom1](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-04-19_Alexaapi_-_Jeedom1.png)

Mise à jour ou Changement de version ([de Beta à Stable ou de Stable à Beta](#versions))
----------------------------------------------------------------------------------------

### Que faire ?

Le plugin et son API étant vivants (Amazon n’ayant pas documenté l’API se permet de modifier au fil de l’eau ses protocoles), les mises à jour permettent d’apporter des corrections dans les liens entre le plugin et le serveur Amazon.

Notez qu’il est toujours conseillé de mettre à jour [Alexa-API](https://jeedom.sigalou-domotique.fr/alexa-api-documentation) quand on met à jour les plugins multimedia ([Alexa-AmazonMusic](https://jeedom.sigalou-domotique.fr/alexa-amazon-music-documentation), Alexa-Deezer, Alexa-Spotify ou encore Alexa-smartHome)

**Deux solutions pour avoir une installation opérationnelle :**

*   **Supprimer tous les équipements Amazon et leurs commandes et les recréer**
*   **Forcer la mise à jour de toutes les commandes**
*   **Lancer un SCAN qui détecte les nouveaux équipements ou les nouvelles commandes**

**Le choix entre ces trois solutions dépend du nombre de scénarios que vous avez développé grâce aux. En effet, la première solution supprime tous les devices et toutes les commandes, elle supprimera donc celles ci dans vos scénarios. La seconde solution est plus respectueuse de vos scénarios car elle mettra à jour vos commandes sans les supprimer et donc vos scénarios seront intacts.**

### Solution 1 : Supprimer tous les équipements et leurs commandes et les recréer

C’est le mode le plus **propre** et le plus **optimisé** puisque vous repartez avec une installation comme neuve des devices et de leurs commandes.

Pour se faire, il faut utiliser le bouton (dans l’écran de configuration du plugin Alexa-API)![boutonalexaapi1](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/boutonalexaapi1.png)

**Attention**, cette fonction supprime tous les équipements et leurs commandes, vous perdez donc tous les liens dans vos scénarios.

### Solution 2 : Forcer la mise à jour de toutes les commandes

C’est le mode le plus **simple** et **sans risque** puisque vos équipements et leurs commandes ne sont pas supprimés. Ce forçage n’impacte donc pas vos scénarios.

Pour se faire, il faut utiliser le bouton ![boutonalexaapi2](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/boutonalexaapi2.png)

Si vous ne souhaitez pas lancer le foçage de mise à jour sur **toutes** les commandes de **tous** les équipements, vous pouvez le lancer sur un seul équipement (et donc sur toutes ses commandes). Pour cela, rendez vous sur l’équipement concerné et cliquez sur :

![boutonalexaapi3](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/boutonalexaapi3.png)

### Solution 3 : Le SCAN

![Screenshot 2020 02 04 Alexaapi Jeedom](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-02-04_Alexaapi_-_Jeedom.png)

Notez enfin que le scan peut être lancé à tout moment, il n’impacte pas les équipements déja détectés ni les commandes existantes, par contre, il recrée tous les **nouveaux** devices ou les devices **supprimés**. Il recrée également toutes les **nouvelles** commandes ou les **commandes** supprimées.

Les tuiles
----------

### La tuile du player multimédia

![Screenshot 2020 04 18 Dashboard Jeedom21](https://jeedom.sigalou-domotique.fr/wp-content/uploads/2019/03/Screenshot_2020-04-18_Dashboard_-_Jeedom21.png)