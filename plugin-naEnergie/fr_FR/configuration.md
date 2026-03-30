# Configuration

## Configuration des équipements

Préalable : vous avez déjà renseigné les paramètres d'authentification et synchronisé le plugin avec les serveurs Netatmo. (Voir section 2 "Comment installer ce plugin ?")

La configuration des équipements Netatmo Energie est accessible à partir du menu Plugin => Confort => naEnergie :

![configuration3](https://limad.github.io/plugins-docs/plugin-naEnergie/images/naEnergie_screenshot4.PNG)

Une fois que vous cliquez sur un équipement, vous obtenez :

Vous retrouvez ici toute la configuration de votre équipement :

- **Nom de l'équipement** : nom de votre équipement (vous pouvez le modifier librement).
- **Objet parent** : objet parent auquel appartient l’équipement; c'est là que la tuile apparait.
- **Activer** : active l'équipement et synchronise ses informations périodiquement.
- **Visible** : rend l'équipement visible sur le dashboard.
- **Identifiant** : identifiant unique de l'équipement.
- **Type** : type d'équipement (Thermostat pour pièces avec thermostat / Valve pour vannes seulement).

![configuration3](https://limad.github.io/plugins-docs/plugin-naEnergie/images/naEnergie_doc4.PNG)

## Configuration des commandes infos et actions

Les commandes disponibles pour votre équipement sont accessibles depuis l'onglet **Commandes**. Elles sont classées par type : infos ou actions.

![configuration3](https://limad.github.io/plugins-docs/plugin-naEnergie/images/naEnergie_doc3.PNG)

- **Afficher** : active l’affichage de la commande sélectionnée (uniquement pour tuiles core).
- **Historiser** : historise la donnée de la commande info sélectionnée.
- Configuration avancée (petite roue crantée) : affiche la configuration avancée de la commande (méthode d’historisation, widget…)
- **Evaluer** : retourne la valeur de la commande info sélectionnée.
- **Tester** : teste la commande action sélectionnée.

Ce paramètre est disponible dans la page de l'équipement "Type de la tuile" :
- Default : le plugin utilise la tuile personnalisée (conseillé).
- Core : la tuile est gérée par le core Jeedom.
