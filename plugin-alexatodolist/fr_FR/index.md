---
layout: default
title: Alexa - Todo List — Documentation
lang: fr_FR
pluginId: alexatodolist
---

# Description

**Alexa - Todo List** affiche et gère dans Jeedom les listes de votre compte Alexa : liste de courses, liste de tâches et listes personnalisées. Chaque liste devient un équipement Jeedom ; vous pouvez y ajouter, renommer, cocher ou supprimer des éléments, depuis la page du plugin ou par commandes (scénarios).

> **Important**
>
> Le plugin est en version bêta. Il n'a pas de connexion Amazon propre : tout passe par le plugin **Alexa Premium**.

# Prérequis

| Composant | Version / remarque |
|---|---|
| Jeedom | ≥ 4.4 |
| Plugin Alexa Premium | installé, actif et connecté à votre compte Amazon |

Sans Alexa Premium, la page du plugin affiche « Le plugin Alexa-Premium est introuvable » et ne s'ouvre pas. Voir la [documentation d'Alexa Premium](../../plugin-alexaapiv2/) pour la connexion au compte Amazon.

Pas de démon ni de dépendance propres.

# Installation

1. Installer et configurer **Alexa Premium** (connexion au compte Amazon).
2. Installer **Alexa - Todo List** depuis le Market, puis l'activer.
3. Ouvrir la page du plugin et cliquer sur **Scan**.

À l'installation et à chaque mise à jour, le plugin installe le pack de widgets « iso » utilisé par ses commandes. Vos widgets personnalisés (`custom.*`) ne sont jamais écrasés.

# Page du plugin

| Tuile | Rôle |
|---|---|
| Lien Alexa-Premium | ouvre la page du plugin Alexa Premium |
| Configuration | page de configuration du plugin |
| Scan | récupère toutes les listes du compte Alexa : crée un équipement par nouvelle liste et met à jour les éléments de toutes les listes |
| Ajouter une liste | demande un nom, crée la liste sur le compte Alexa puis relance le scan |
| Supprimer tout | supprime, après confirmation, tous les équipements du plugin dans Jeedom (les listes restent sur le compte Alexa) |

Les listes apparaissent sous **Mes listes**. Les listes par défaut d'Alexa sont créées sous les noms `SHOPPING_LIST` (courses) et `TO_DO` (tâches) : vous pouvez renommer l'équipement.

# Équipement (une liste)

Barre de boutons :

- **i** : informations brutes de l'équipement ;
- **mode édition** (crayon) : affichage détaillé du tableau des commandes ;
- **Défaut config** : réapplique les widgets et retours à la ligne par défaut à toutes les commandes de l'équipement ;
- **Configuration avancée**, **Sauvegarder**, **Supprimer** : fonctions standard de Jeedom.

Onglets :

- **Equipement** : nom, objet parent, catégorie, Activer / Visible ; identifiant et type de la liste Alexa (lecture seule).
- **Commandes** : commandes infos et actions de la liste.
- **Elements de la liste** : tableau des éléments avec, pour chacun, la date d'ajout, la date de modification, l'utilisateur Alexa qui l'a ajouté, la case **Accompli** et le bouton **Supprimer**. L'icône engrenage renomme l'élément. Le bouton **Ajouter un élément à la liste** demande un nom et l'ajoute.

Chaque modification est envoyée à Amazon, puis la liste est relue.

# Commandes

| Commande | Type | Rôle |
|---|---|---|
| Refresh | action | relit la liste depuis le compte Alexa |
| Date mise à jour | info texte | date et heure de la dernière lecture de la liste |
| Liste des éléments | info texte (masquée) | tous les éléments, au format `id\|nom`, séparés par `;` |
| AddItem | action message | ajoute un élément ; le nom est le titre du message |
| deleteItem | action liste | supprime l'élément choisi dans la liste déroulante |
| Archiver | action | archive la liste sur le compte Alexa |
| *une commande par élément* | info texte (masquée) | nom de l'élément ; valeur `true` si accompli, `false` sinon |

Exemple de scénario : action `#[Cuisine][SHOPPING_LIST][AddItem]#` avec le titre `lait`.

# FAQ / dépannage

**Un élément ajouté dans l'application Alexa n'apparaît pas dans Jeedom.**
Le plugin ne relit pas les listes automatiquement. Cliquer sur **Scan** (toutes les listes) ou exécuter la commande **Refresh** de la liste (par exemple depuis un scénario programmé).

**Une liste a disparu de la page.**
Les listes archivées ne sont pas affichées sous **Mes listes** et leur équipement est désactivé.

**Un élément porte le suffixe `::Doublon_…`.**
Deux éléments de la même liste ont le même nom : le second reçoit ce suffixe pour que les commandes restent distinctes.

**Une action renvoie une erreur Amazon (code et message).**
Vérifier dans Alexa Premium que la connexion au compte Amazon est active, puis relancer **Scan**. Le détail est dans le log `alexatodolist`.

---

*Alexa - Todo List — Documentation | Plugin open-source pour Jeedom*
