Créer un compte développeur sur Amazon : [https://developer.amazon.com/](https://developer.amazon.com/)

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