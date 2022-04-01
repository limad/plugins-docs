# Configuration

## **Configuration des équipements**

Préalable: Vous avez déja renseiger les parametres d'autentification et synchroniser le plugin avec les serveur Vaillant.
(Voir section2 Comment installer ce plugin ? )


La configuration des équipements Vaillant Energie est accessible à partir du menu Plugin => Confort => VaillantControl:

![configuration3](https://limad.github.io/plugins-docs/plugin-VaillantControl/images/VaillantControl_doc3.PNG)



Une fois que vous cliquez sur un équipement vous obtenez :


Vous retrouvez ici toute la configuration de votre équipement :

**Nom de l'équipement ** : nom de votre équipement (Vous pouver le changer librement).

**Objet parent** : indique l’objet parent auquel appartient l’équipement c'est là que la tuile va apparaitre.

**Activer** : permet d'activer l'équipement et synchroniser ses informations périodiquement

**Visible** : le rend visible sur le dashboard

**Identifiant** : identifiant unique de l’équipement

**Type** : type de votre équipement (Home/Thermostat/Dhw)

![configuration3](https://limad.github.io/plugins-docs/plugin-VaillantControl/images/VaillantControl_doc4.PNG)




## Configuration les Commandes infos et actions

Les Commandes disponibles pour votre équipement sont accéssibles depuis **l'onglet Commandes**
Elles sont classées par type ; infos ou actions

![configuration3](https://limad.github.io/plugins-docs/plugin-VaillantControl/images/VaillantControl_doc3.PNG

**Afficher** : permet d'afficher la commande selectionnée( uniquement pour tuiles 'core'**)
**Historiser** : permet d’historiser la donnée de la commande "Info" selectionnée

configuration avancée (petites roues crantées) : permet d’afficher la configuration avancée de la commande (méthode d’historisation, widget…​)
**Evaluer**: retourne la valeur de la commande "Info" selectionnée
**Tester** : permet de tester la commande "Action" selectionnée

** ce parametre est disponible dans la page de l'équipement "Type de la tuile" 
Default: le plugin va utiliser la tuile pérsonalisé (conseillé)
Core: la tuile sera gérée par le core Jeedom
