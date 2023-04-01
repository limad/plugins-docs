---
layout: default
title: helloWatt documentation
lang: fr_FR
pluginId: helloWatt
--
![logo](https://limad.github.io/plugins-docs/plugin-hellowatt/images/logo.PNG)
# Plugin helloWatt

Plugin permettant la récupération des consommations(Gaz/Elec) par l'interrogation du compte-client *helloWatt*. Les données n'étant pas mises à disposition en temps réel, le plugin récupère chaque jour les dernieres données de consommations disponibles. 

types de consommation accessibles :
- la **consommation journalière** *(en kWh)*.
- la **consommation mensuelle** *(en kWh)*.
- la **consommation annuelle** *(en kWh)*.
- le cout associé aux consommations.
Si vous disposer d'une production solaire en contrat avec ENEDIS
- la **production journalière** *(en kWh)*.
- la **production mensuelle** *(en kWh)*.
- la **production annuelle** *(en kWh)*.
- le cout associé aux productions.
Si vous disposer d'un abonnement TEMPO
- la **couleur du jour**
- la **couleur du lendemain**
Et d'autres...
![Commandes](https://limad.github.io/plugins-docs/plugin-hellowatt/images/helloWatt_screenshot2.png)

>**Important**      
>Il est nécessaire d'être en possession d'un compte-client helloWatt. Le plugin récupère les informations à partir de la partie *mes-donnees* <a href="https://www.hellowatt.fr/mon-compte/ma-consommation/mes-donnees" target="_blank">du site helloWatt</a>, il faut donc vérifier que vous y avez bien accès avec vos identifiants habituels (email/mot de passe) et que les données y sont visibles. Dans le cas contraire, le plugin ne fonctionnera pas.

# Configuration

## Configuration du plugin

Sur la page de configuration du plugin, renseignez l'**identifiant** ainsi que le **mot de passe** de votre compte-client *helloWatt* puis cliquez sur le bouton **Sauvegarder**.

![Configuration](https://limad.github.io/plugins-docs/plugin-hellowatt/images/helloWatt_Eqconfig.png)

## Configuration des équipements

Pour accéder aux différents équipements **helloWatt**, dirigez-vous vers le menu **Plugins → Energie → helloWatt**.

> **A savoir**    

Le plugin va alors vérifier la bonne connexion au site *helloWatt* créer les équipements, récupérer et insérer les informations dans l'historique Jeedom.

![Eq_Configuration](https://limad.github.io/plugins-docs/plugin-hellowatt/images/helloWatt_config.png)

# Affichage des informations
Deux tuiles sont disponibles : 
- tuile pérsonalisée : l'affichage des informations de consommation, il est possible de cacher les informations non souhaitées par la procédure habituelle.
- tuile *core* : cette tuile est gérée par le *Core Jeedom* et vous laisse la possibilité d'apporter vos propres pérsonalisations.

- Le plugin dispose également d'un panneau affichant la tuile et des graphiques.

# Recommandations d'usage
- Il faut laisser les commandes en mode "Lissage historique => aucun"
- Éviter d'exécuter les commandes refresh et veiller à limiter le nombre de requêtes.
- En cas du discussion sur le forum, **Ne pas poster vos logs sur un fil public, uniquement en MP si nécessaire**

# En cas de dysfonctionnements importants
commencer par vérifier vos identifiants (email, mdp)
que votre compte sur le site helloWatt est accessibles en vous connectant dessus.
que votre mdp est conforme aux exigences récentes de hellowatt (12 caractères, caractères spéciaux…)
que votre version Jeedom est en adéquation avec la version minimale requise par le plugin.

Si des erreurs persistent :
1- Activer les logs en début
2- Faire un refresh ou "sauvegarder l’équipement si les commandes ne sont pas crées, me transmettre les logs complets de cette séquence en MP. 
3- En cas d’erreur 500 joindre les logs http.error aussi.

Rappel : !!! Ne pas poster vos logs sur un fil public, **uniquement en MP**, les logs pourraient contenir des informations personnelles directement visibles mais parfois codée !


# Problèmes potentiels

Comme tous les serveurs, celui de helloWatt n'est pas infaible, il peut être momentanement inaccéssible notament pour maintenance…
Le plugin réessaie à intervals réguliers.

En l'abscence d'API officielle, des changements coté serveur peuvent rendre le plugin inopérant. Je ferais mon maximum pour résoudre ce genre de probleme mais sans garantie.

# Remarques

Le plugin se repose sur la manière dont le site helloWatt est structuré. Tout changement sur le site entrainera vraisemblablement une erreur sur le plugin et nécessitera une adaptation de celui-ci plus ou moins difficile à faire.


# Disclaimer

-   En l'abscence d'API officielle, des changements coté serveur peuvent rendre le plugin inoperant. Je ferais mon maximum pour résoudre ce genre de probleme mais sans garantie.
-   Ce plugin vous est fourni sans aucune garantie. Bien que peu probable, si il venait à corrompre votre installation Jeedom, l'auteur ne pourrait en être tenu pour responsable.

# ChangeLog
Disponible [ici](./changelog.html).
