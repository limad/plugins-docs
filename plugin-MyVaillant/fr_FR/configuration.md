# Configuration

## Configuration des équipements

Préalable : les paramètres d'authentification sont renseignés et la synchronisation a été effectuée (voir la section "Comment installer ce plugin ?" ci-dessus).

La liste des équipements est accessible depuis le menu **Plugins => Confort => MyVaillant** :

![configuration3](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc3.PNG)

En cliquant sur un équipement, vous retrouvez sa configuration standard Jeedom :

- **Nom de l'équipement** : librement modifiable.
- **Objet parent** : l'objet Jeedom auquel appartient l'équipement (emplacement de la tuile).
- **Activer** : active l'équipement et la synchronisation périodique de ses informations.
- **Visible** : affiche l'équipement sur le dashboard.
- **Identifiant** : identifiant unique de l'équipement.
- **Type** : type de l'équipement (**Home**, **Zone** ou **ECS**).

![configuration3](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc4.PNG)

## Configuration des commandes (infos et actions)

Les commandes disponibles sont accessibles depuis l'onglet **Commandes** de l'équipement, classées en **infos** et **actions**.

- **Afficher** : affiche la commande sélectionnée (tuiles "core" uniquement).
- **Historiser** : conserve l'historique de la commande "info" sélectionnée.
- **Tester** : exécute la commande "action" sélectionnée.
- La roue crantée ouvre la configuration avancée de la commande (méthode d'historisation, widget...).

## Paramètres additionnels

- **Prix du kWh** : prix TTC de votre électricité, utilisé pour estimer le coût affiché dans la vue **Consommations** (laisser vide pour ne pas afficher de coût). Une alerte signale si ce prix n'a pas été mis à jour depuis plus d'un an.

## Alerte COP (scénario)

Le plugin calcule un **COP glissant** chaque heure (commande "COP live (chauffage + ECS)" sur l'équipement Home) — utile pour détecter un entartrage, un filtre encrassé ou du givre mal résorbé. Pour être notifié en cas de COP anormalement bas, importez le modèle de scénario fourni (menu **Scénarios**, bloc d'action "Code", coller le contenu de `core/config/scenario_cop_optimisation.php`) et adaptez-le à votre commande de notification.
