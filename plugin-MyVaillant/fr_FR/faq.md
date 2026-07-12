# FAQ

## Quelle est la fréquence de rafraîchissement ?

Le système récupère les informations toutes les **5** ou **10 minutes** au choix. Les données de consommation (EMF) sont synchronisées une fois par heure, et le COP glissant est recalculé chaque heure à partir de cet historique.

## Le prix affiché dans la vue Consommations ne correspond plus à mon tarif actuel

Mettez à jour le champ **Prix du kWh** dans la configuration du plugin. Une alerte s'affiche automatiquement si ce prix n'a pas été modifié depuis plus d'un an.

## Une donnée de mon installation n'apparaît pas

Toutes les installations Vaillant ne fournissent pas les mêmes données : cela dépend du matériel réellement installé (pompe à chaleur, chaudière gaz, appoint électrique, ventilation...). Si vous venez d'installer l'équipement, certaines informations (consommations, COP) ne seront disponibles qu'après le premier cycle quotidien de synchronisation.

## Ma connexion échoue après un fonctionnement normal

Utilisez d'abord **Synchroniser sans reconnexion** : ce bouton échoue proprement (sans casser une session déjà valide) si une reconnexion complète est nécessaire. Si le problème persiste, utilisez **Se déconnecter** puis **Synchroniser** pour forcer une connexion complète.
