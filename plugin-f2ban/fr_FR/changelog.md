---
layout: default
title: f2ban — Changelog
lang: fr_FR
pluginId: f2ban
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — f2ban

## 2026-09-20

- Toutes les commandes utilisent un widget du jeu de widgets iso, téléchargé depuis GitHub à l'installation et à la mise à jour du plugin (widget par défaut de Jeedom s'il est absent): **iso_line** pour les infos, **iso_btn** pour *Rafraichir*, *Banip* et *Unbanip*. Les commandes existantes qui utilisent encore le widget par défaut, l'ancien widget *line*, un widget qui n'existe pas ou *iso_msg_dialogue* (réservé aux commandes de type « chat » avec une info liée) sont migrées une fois à la mise à jour; les autres widgets ne sont pas modifiés
- Les commandes sont maintenant décrites dans `core/config/commands.json`: noms traduisibles (langue de Jeedom à la création) et type générique renseigné (*Connecté* pour *En ligne*, *Générique info/action* pour les autres)
- Les commandes *Banip* et *Unbanip* sont liées (commande info liée) à l'info *Dernière IP bannie* et *Liste des IP bannies* du même jail, visible et modifiable dans la colonne *Etat* du tableau de commandes
- Nouveau bouton **Défaut config** sur la page de l'équipement: recrée les commandes manquantes (même sans connexion à la cible) et réapplique le widget iso, le type générique et la commande info liée par défaut, sans écraser un autre widget existant que vous avez choisi
- Robustesse: si `commands.json` est illisible, l'erreur est journalisée une seule fois et les IP en attente de géolocalisation sont conservées au lieu de provoquer une erreur fatale

## 2026-09-19

- Le plugin est renommé **f2ban** (l'identifiant passe de `fail2ban` à `f2ban`). À l'activation, les équipements, commandes et paramètres existants sont repris automatiquement
- Nouvelle icône
- Nouvelle interface inspirée du plugin smartctrl: équipements regroupés par mode, fenêtre *Santé* avec mise à jour en direct, bannissement/débannissement d'une IP depuis la fenêtre, bouton *Assistance*, tableau de commandes avec mode compact/édition
- Authentification SSH par clé privée (en plus du mot de passe)
- Bouton *Tester la connexion* avec diagnostic de la cause en cas d'échec, et bouton *Actualiser maintenant*
- Le plugin n'utilise plus les paquets `mips/httpclient` et `mips/jeedom-tools` (la géolocalisation utilise désormais `connector.php` avec des délais d'attente)
- Mise à jour de phpseclib (3.0.57, version minimale imposée)
- Licence AGPL v3
- Correction : le compteur par pays provoquait une erreur (TypeError) en PHP 8 à la première IP d'un nouveau pays, ce qui interrompait l'actualisation
- Compteurs par pays : une IP bannie dans plusieurs jails n'est comptée qu'une fois, et une IP dont la géolocalisation a échoué (limite d'ip-api, réseau) est retentée à l'actualisation suivante au lieu d'être perdue. 30 requêtes maximum par passage, pays mis en cache par IP
- SSH : une seule connexion par actualisation au lieu d'une par jail, et la détection des jails n'est plus perturbée par les messages d'erreur de sudo (ex. `unable to resolve host`)
- Sécurité : les fichiers PHP de `plugin_info/`, `core/php/` et `core/class/` ne sont plus accessibles directement par HTTP
- Sécurité SSH : l'empreinte de la clé du serveur est enregistrée à la première connexion puis vérifiée avant d'envoyer le mot de passe ou la clé privée (bouton *Réinitialiser* dans la fiche de l'équipement)
- Nouvelle commande info **En ligne** (1 si fail2ban a répondu à la dernière actualisation, 0 sinon)
- Les jails ajoutés sur la cible sont détectés à chaque actualisation, et non plus seulement à la sauvegarde de l'équipement
- Un équipement en échec n'interrompt plus l'actualisation des suivants
- Un jail supprimé sur la cible n'est plus actualisé indéfiniment avec des valeurs figées: ses commandes sont masquées (historique conservé) et un message est ajouté; elles redeviennent visibles si le jail réapparaît
- Nouvelle option *Désactiver la géolocalisation des IP bannies* (configuration du plugin): aucune adresse IP n'est alors envoyée à ip-api.com

## 2026-06-12

- Mise en place d'un nouveau flux de déploiement pour la documentation

## 2026-05-12

- Ajout des dépendances pour installer fail2ban-client
- Mise à jour de dépendances
- Jeedom v4.5 requis

## 2025-08-11

- Mise à jour de dépendances

## 2024-12-25

- Mise à jour de dépendances
- Mise à jour de l'icône

## 2024-10-17

- Mise à jour de dépendances
- Jeedom v4.4 requis

## 2024-09-16

- Mise à jour de dépendances
- Traduction du plugin en anglais, allemand, espagnol, italien, portugais
- Version Debian 11 minimum requise

## 2024-04-10

- Mise à jour de dépendances

## 2023-11-01

- Correction sur les commandes **Liste des IP bannies** qui n'étaient pas correctement vidées lorsque plus aucune IP n'étaient bannies
- Modification du template par défaut pour les commandes info/numérique

## 2023-10-23

- Mise à jour de dépendances

## 2023-10-21

- Changement: cron par défaut à 10min

## 2023-10-06

- Ajout d'une commande **Dernière IP bannie** par jail

## 2023-10-01

Première version

# Documentation

[Voir la documentation]({{site.baseurl}}/plugin-{{page.pluginId}}/{{page.lang}})
