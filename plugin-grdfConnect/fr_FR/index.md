# Plugin Grdf-Connect

Plugin permettant la récupération des consommations du compteur communicant *Gazpar* par l'interrogation du compte-client *GRDF*. Les données n'étant pas mises à disposition en temps réel, le plugin récupère chaque jour les données de consommation de gaz de la veille. 

types de consommation accessibles :
- la **consommation journalière** *(en kWh)*.
- la **consommation mensuelle** *(en kWh)*.
- la **consommation annuelle** *(en kWh)*.

>**Important**      
>Il est nécessaire d'être en possession d'un compte-client GRDF. Le plugin récupère les informations à partir de la partie *mon espace* <a href="https://monespace.grdf.fr/monespace/particulier/accueil" target="_blank">du site GRDF</a>, il faut donc vérifier que vous y avez bien accès avec vos identifiants habituels(email/mot de passe) et que les données y sont visibles. Dans le cas contraire, le plugin ne fonctionnera pas.

# Configuration

## Configuration du plugin

Le plugin **Grdf-Connect** ne nécessite aucune configuration spécifique mais doit être activé après l'installation.

Deux options sont disponibles dans la configuration du plugin pour gérer la réaction en cas de détecion de captcha à la connexion:
- Ajouter une entrée dans le centre de message (cochée par défaut)
- Désactiver l'équipement 

Vous pouvez également spécifier le nombre de jours de retard généralement constaté afin d'éviter au plugin d'interroger le site pour rien.

Le site GRDF vous permet de spécifier des seuils menusels que le plugin récupère et stocke dans la commande dédiée.
Si vous préférez utiliser les valeurs mensuelles de l'année précédente comme seuils, décochez l'option correspondante.

Les données sont vérifiées toutes les heures entre 4h et 22h et mises à jour uniquement si non disponibles dans Jeedom.

## Configuration des équipements

Pour accéder aux différents équipements **Grdf-Connect**, dirigez-vous vers le menu **Plugins → Energie → Grdf-Connect**.

> **A savoir**    
> Le bouton **+ Ajouter** permet d'ajouter un nouveau compte **Grdf-Connect**.

Sur la page de l'équipement, renseignez l'**identifiant** ainsi que le **mot de passe** de votre compte-client *GRDF* puis cliquez sur le bouton **Sauvegarder**.

Vous pouvez également spécifier le numéro PCE de votre compteur si vous en avez plusieurs liés à votre compte.

Le plugin va alors vérifier la bonne connexion au site *GRDF* et récupérer et insérer en historique :
- **conso journalière** : consommation journalière,
- **conso mensuelle** : consommation mensuelle des 12 derniers mois,
- **conso annuelle** : consommation annuelle les 12 derniers mois ou depuis le 1er Janvier,
- **conso publiée** : les 4 dernieres années(avec ou sans compteur communicant),
- **conso (max/min/moy) locale** : consommation mensuelle comparative (maximum/minimum/médiane) des foyers similaires  des 12 derniers mois
- **seuils mensuels** : optionnelle, s'ils sont définis dans votre espace GRDF
Autre commandes information:
- **coeff de Conversion** : Coefficient de Conversion gaz kWh/m3,
- **delai MAJ** : delai moyen de disponibilité des donées sur votre compte(en jour), le plugin se sert de cette donée pour évité des requetes inutiles.
- **Index** : l'index de votre compteur en m3.
- **meteo** : temperature journaliere moyenne relevé sur la station méteo la plus proche.


Deux tuiles sont disponibles: 
- tuile pérsonalisé; permettant l'affichage des informations comparatives(des 12 derniers mois) et les informations de consommation.
- tuile core; cette tuile est géré par le Core Jeedom et vous laisse la possibilité d'apporter vos propres pérsonalisations.

# Problèmes potentiels

De temps en temps, il se peut que le site demande une résolution de captcha pour se connecter.
Ce sera indiqué dans les logs du plugin, dans ce cas, vous devez vous connecter "manuellement" au site GRDF afin de résoudre la captcha.

En l'abscence d'Api officielle, des changements coté serveur peuvent rendre l'ole plugin innoperant. Je ferais mon maximum pour résoudre ce genre de probleme mais sans garantie.

# Remarques

Le plugin se repose sur la manière dont le site GRDF est structuré. Tout changement sur le site entrainera vraisemblablement une erreur sur le plugin et nécessitera une adaptation de celui-ci plus ou moins difficile à faire.


# Crédits

Ce plugin s'est inspiré des travaux réalisés sur le plugin Jazpar :


# Disclaimer

-   En l'abscence d'Api officielle, des changements coté serveur peuvent rendre le plugin innoperant. Je ferais mon maximum pour résoudre ce genre de probleme mais sans garantie.
-   Ce plugin vous est fourni sans aucune garantie. Bien que peu probable, si il venait à corrompre votre installation Jeedom, l'auteur ne pourrait en être tenu pour responsable.

# ChangeLog
Disponible [ici](./changelog.html).
