Récupérer la prochaine alarme Alexa au format HHmm
--------------------------------------------------

![alexaformathorloge](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/alexaformathorloge.png)

Ce mini-tuto va vous permettre de récupérer l’alarme (ou l’alarme musicale, ou le minuteur ou le rappel) au format **HHmm**.

Le plugin renvoie toujours l’information au format CRON qui correspond à : **2020-02-29 10:55:00**

En fonction de l’utilisation que l’on souhaite faire de cette information, il faut parfois changer le format.

HHmm serait **1055** dans notre exemple.Cela pourrait être adapté pour d’autres formats.

Dans cet exemple, on va  créer une nouvelle Commande Info qui va se nommer _**Prochaine Alarme HHmm**_, elle va donner l’heure de la prochaine Alarme de mon device _**Chambre**_

Pour cela, créer un nouveau virtuel (grace au plugin Virtuel) qui s’appelle _**Chambre HHmm**_

![Screenshot 2020 02 29 Virtual Jeedom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom.png)

*   Aller dans l’onglet Commandes
*   Ajouter une info virtuelle

![Screenshot 2020 02 29 Virtual Jeedom1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom1.png)

*   Dans nom, mettre **Prochaine Alarme HHmm**
*   Dans sous-type, mettre **Autre**

Pour mon exemple, je vais utiliser **Prochaine Alarme** qui se trouve dans mon équipement **La chambre** qui se trouve dans l’objet **Etage**

![Screenshot 2020 02 29 Virtual Jeedom2](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom2.png)

*   Dans Valeur, mettre **sprintf(« %02d »,trim(substr(#\[Etage\]\[La chambre\]\[Prochaine Alarme\]#, 11, 2)))sprintf(« %02d »,trim(substr(#\[Etage\]\[La chambre\]\[Prochaine Alarme\]#,14, 2)))** en remplaçant _**#\[Etage\]\[La chambre\]\[Prochaine Alarme\]#**_ par votre commande.
*   Cliquer sur **Sauvegarder**

 Pour vérifier que cela fonctionne, cliquer sur le bouton **Tester**, si tout se passe bien, vous aurez votre prochaine alarme au format **HHmm**

![Screenshot 2020 02 29 Virtual Jeedom3](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom3.png)

Utiliser donc cette nouvelle commande **Prochaine Alarme HHmm** à la place de la commande **Prochaine Alarme** de votre device.