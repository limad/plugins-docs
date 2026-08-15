---
layout: default
title: Shelly Control — Contrôle local des appareils Shelly
lang: fr_FR
pluginId: shelly_control
---

# Présentation

**Shelly Control** permet de piloter les appareils **Shelly** (relais, volets, lumières, capteurs)
**en local, sans passer par le cloud Shelly**. La communication se fait directement sur le réseau
local avec chaque appareil :

- **CoIoT / HTTP** pour les appareils **Gen1**,
- **WebSocket RPC** pour les appareils **Gen2, Gen3 et Gen4**.

Les appareils présents sur le réseau sont détectés automatiquement par **découverte mDNS**, sans
configuration manuelle de leur adresse IP.

>**INFORMATION**
>
>Aucun compte Shelly Cloud, aucune clé API externe et aucune connexion Internet ne sont
>nécessaires : tout le contrôle transite en direct entre Jeedom et l'appareil, sur le réseau
>local.

## Fonctionnement général

Le plugin s'appuie sur un **démon Python** (bibliothèque `aioshelly`) qui maintient une connexion
permanente à chaque appareil ajouté :

- il reçoit en temps réel les changements d'état (relais actionné physiquement, volet en
  mouvement, capteur qui remonte une nouvelle valeur…) et les répercute immédiatement dans
  Jeedom ;
- il exécute les commandes envoyées depuis Jeedom (allumer, éteindre, ouvrir un volet, régler une
  couleur…) ;
- il scanne périodiquement le réseau local en mDNS pour découvrir les nouveaux appareils Shelly.

Les **commandes Jeedom sont créées automatiquement** à partir des composants réellement exposés
par l'appareil (un relais n'aura pas les mêmes commandes qu'un volet ou qu'une lumière RGB) : il
n'y a rien à créer manuellement.

## Compatibilité

- Appareils Shelly **Gen1** (CoIoT), **Gen2**, **Gen3** et **Gen4** (WebSocket RPC).
- Relais (switch), volets/stores (cover), lumières (blanches, RGB, RGBW, blanc réglable CCT),
  entrées, capteurs (température, humidité, alimentation, compteur d'énergie, fumée…).
- Les composants non reconnus spécifiquement par le plugin remontent tout de même en Jeedom sous
  forme de commandes d'information génériques : aucune valeur exposée par l'appareil n'est perdue.

# Installation

Comme tout plugin Jeedom, **Shelly Control** doit être installé puis activé.

L'installation met en place un environnement Python dédié (venv) contenant la bibliothèque
`aioshelly` : c'est cette dépendance qui est vérifiée sur la page de gestion du plugin
(bloc **Dépendances**). Si elle n'est pas au vert, cliquez sur **Relancer** pour relancer son
installation.

![Page de gestion du plugin : état, dépendances et démon](../images/shelly_control_gestion_plugin.png)

Une fois les dépendances en place, le **démon** doit être démarré (bloc **Démon**, bouton
**(Re)Démarrer**) : c'est lui qui communique avec les appareils Shelly. Sans démon actif, aucune
découverte ni aucune commande n'est possible.

# Configuration du plugin

La configuration générale se trouve dans **Plugins → Protocole domotique → Shelly Control →
Configuration**, ou directement depuis le bandeau de gestion du plugin (bouton
**Configuration**, icône clé à molette, sur la page principale du plugin).

![Configuration générale du plugin : découverte réseau et démon](../images/shelly_control_config_plugin.png)

- **Découverte automatique (mDNS)** : active le scan périodique du réseau local à la recherche de
  nouveaux appareils Shelly. Activée par défaut.
- **Intervalle de découverte (secondes)** : fréquence des scans mDNS. Minimum 60 secondes, valeur
  par défaut 300 secondes (5 minutes).
- **Port socket interne** : port TCP local (`127.0.0.1` uniquement, jamais exposé sur le réseau)
  utilisé pour la communication entre Jeedom et le démon. Valeur par défaut `55116`.
  >**IMPORTANT**
  >
  >Le démon doit être **redémarré manuellement** après toute modification de ce port (bloc
  >**Démon** de la page de gestion du plugin).
- **Rafraîchissement Gen1 (secondes)** : les appareils Gen1 communiquent leurs changements d'état
  en CoIoT (multicast), qui peut occasionnellement manquer une notification. Ce cycle HTTP
  complémentaire interroge périodiquement chaque appareil Gen1 pour rattraper un état manqué et
  détecter sa perte de connexion. Minimum 30 secondes, valeur par défaut 60 secondes. Sans effet
  sur les appareils Gen2 et supérieurs, qui utilisent une connexion WebSocket permanente.

# Ajout des appareils

La page principale du plugin (**Plugins → Protocole domotique → Shelly Control**) centralise la
découverte et la gestion des appareils.

![Page principale du plugin : bandeau de gestion, appareils découverts et liste des équipements](../images/shelly_control_liste.png)

Le bandeau **Gestion** en haut de page donne accès à :

- **Configuration** : réglages généraux du plugin (voir plus haut) ;
- **Santé** : diagnostic complet de l'installation (voir plus bas) ;
- **Rechercher** : force un scan mDNS immédiat sans attendre le prochain cycle automatique ;
- **Synchronisation** : resynchronise l'état de tous les appareils déjà ajoutés (utile après un
  redémarrage du démon, par exemple) ;
- **Ajout manuel** : ajoute un appareil par son adresse IP quand la découverte mDNS ne le trouve
  pas (réseau segmenté, mDNS bloqué…) ;
- **Supprimer tous** : supprime en une fois tous les équipements Shelly de Jeedom ;
- **Documentation** / **Community** : liens vers la documentation en ligne et le forum Jeedom.

## Découverte automatique

Tant que le démon tourne, les appareils Shelly présents sur le réseau local apparaissent
automatiquement dans le bloc **Appareils découverts**, avec leur nom, adresse MAC, adresse IP,
génération et modèle.

![Un appareil détecté en attente d'ajout](../images/shelly_control_decouverte.png)

Pour chaque appareil détecté :

- **Ajouter** crée l'équipement Jeedom correspondant et génère ses commandes ;
- l'icône **✕** l'ignore : il disparaît de la liste des appareils en attente (il réapparaîtra si
  jamais il est de nouveau détecté après un redémarrage du démon).

## Ajout manuel

Si un appareil n'est pas détecté par mDNS (segment réseau différent, mDNS filtré par un
équipement réseau…), utilisez le bouton **Ajout manuel** du bandeau **Gestion** : il suffit de
renseigner son **adresse IP**, puis son **adresse MAC** (12 caractères hexadécimaux, visible dans
l'interface web de l'appareil ou son étiquette).

![Ajout manuel d'un appareil par IP et adresse MAC](../images/shelly_control_ajout_manuel.png)

# Configuration des équipements

Cliquer sur un appareil dans la liste ouvre sa fiche, avec deux onglets : **Équipement** et
**Commandes**.

## Onglet Équipement

![Onglet Équipement : informations générales et connexion locale](../images/shelly_control_fiche_equipement.png)

**Général**

- **Nom** : nom de l'équipement dans Jeedom.
- **Adresse MAC** : identifiant unique de l'appareil, renseigné automatiquement à la création et
  non modifiable ensuite.
- **Objet parent**, **Activer**, **Visible**, **Catégorie** : réglages standards Jeedom.

**Connexion locale**

- **Adresse IP** : adresse de l'appareil sur le réseau local. Mise à jour automatiquement si elle
  change (attribution DHCP) tant que l'appareil reste joignable en mDNS.
- **Statut** : état de connexion en temps réel de l'appareil (connecté / déconnecté / erreur),
  rafraîchi automatiquement toutes les 5 secondes pendant la consultation de la fiche.
- **Authentification** : à activer uniquement si une restriction d'accès a été configurée
  directement sur l'appareil Shelly (interface locale). Une fois activée, elle fait apparaître
  les champs **Utilisateur** et **Mot de passe**.
  >**INFORMATION**
  >
  >Le mot de passe est stocké chiffré par Jeedom (comme tout champ de mot de passe du core) et
  >n'apparaît jamais en clair dans les logs.

**Informations appareil**

Génération, modèle, version de firmware et nom réseau sont renseignés automatiquement par le
démon lors de la connexion à l'appareil — ils ne sont pas modifiables manuellement. Une image de
l'appareil est également affichée ; elle peut être remplacée par une image personnalisée depuis
**Configuration avancée** (bouton en haut de la fiche équipement).

## Onglet Commandes

![Onglet Commandes : tableau des commandes générées automatiquement](../images/shelly_control_fiche_commandes.png)

Les commandes sont **entièrement générées à partir des composants réellement exposés par
l'appareil** : leur nombre et leur nature varient donc d'un modèle à l'autre. Leur identifiant
logique reflète le composant d'origine et n'est pas modifiable.

Chaque appareil dispose au minimum des commandes communes suivantes :

- **En ligne** *(info, binaire)* : reflète l'état de connexion de l'appareil.
- **Signal Wi-Fi (dBm)** *(info, numérique)*.
- **Mise à jour disponible** *(info, binaire)* et **Mettre à jour le firmware** *(action)*.
- **Rafraîchir** *(action)* : force une resynchronisation immédiate de l'état de l'appareil.
- **Redémarrer** *(action)* : redémarre l'appareil.

S'y ajoutent les commandes propres à chaque composant détecté, par exemple :

- **Relais** (switch) : état, puissance (W), tension (V), courant (A), température, énergie
  cumulée (Wh) ; actions Allumer / Éteindre / Basculer.
- **Volet** (cover) : état, position (%), puissance (W) ; actions Ouvrir / Fermer / Stop / Régler
  la position (curseur).
- **Lumière** (blanche simple, RGB, RGBW, blanc réglable CCT) : état, luminosité, couleur, canal
  blanc et/ou température de couleur selon les capacités du modèle ; actions correspondantes
  (Allumer / Éteindre / Basculer, curseur de luminosité, sélecteur de couleur…).
- **Capteurs** (température, humidité, alimentation, compteur d'énergie, fumée…) et tout autre
  composant non modélisé spécifiquement : chaque valeur remontée par l'appareil devient une
  commande d'information dédiée.

>**INFORMATION**
>
>Quand une commande action agit sur un état (allumer/éteindre, ouvrir/fermer…), son champ
>**Etat** est automatiquement lié à la commande info correspondante : c'est ce qui permet
>d'afficher l'état courant directement sur le bouton, comme pour la majorité des plugins Jeedom.

## Résumé sur le tableau de bord

Comme toute commande Jeedom, celles créées par le plugin peuvent être affichées sous forme de
résumé compact (widget « summary ») sur un tableau de bord ou un objet :

![Résumé d'un équipement Shelly sur le tableau de bord](../images/shelly_control_tuile.png)

# Diagnostic (Santé)

Le bouton **Santé** du bandeau **Gestion** ouvre une modale de diagnostic complet de
l'installation : état du plugin, du démon, des dépendances Python (venv, version d'`aioshelly`),
de la découverte automatique, nombre d'appareils détectés en attente, nombre d'équipements
(total, activés, Gen1/Gen2+) et nombre d'équipements réellement connectés. Le détail de chaque
appareil (objet, nom, MAC, IP, génération, modèle, firmware, statut de connexion) est listé
en dessous, ainsi que les appareils détectés en attente d'ajout le cas échéant.

![Modale de diagnostic Santé](../images/shelly_control_sante.png)

C'est le premier réflexe en cas de souci : appareil qui n'apparaît pas, commandes qui ne
remontent pas d'état, etc.

# Scripts natifs (Gen2+)

Les appareils **Gen2, Gen3 et Gen4** embarquent un moteur de script JavaScript natif (absent des
appareils Gen1). Le bouton **Scripts** de la fiche équipement — visible uniquement pour ces
générations — ouvre un éditeur permettant de :

- lister les scripts présents sur l'appareil et leur état (en cours / arrêté) ;
- créer un nouveau script ;
- éditer le code d'un script existant (éditeur CodeMirror, coloration JavaScript) ;
- l'enregistrer, ou l'enregistrer et le démarrer directement ;
- démarrer, arrêter ou supprimer un script.

![Modale de gestion des scripts natifs Shelly (Gen2+)](../images/shelly_control_scripts.png)

>**INFORMATION**
>
>Les sorties console (`print()`) d'un script restent visibles depuis l'interface web native de
>l'appareil ou l'application Shelly, pas depuis Jeedom.

## Exemple : mode « boost » temporisé

Un script natif s'exécute directement sur l'appareil, indépendamment de Jeedom : utile pour des
automatismes qui doivent continuer à fonctionner même si le démon ou Jeedom est éteint. Exemple
pour un relais de VMC : l'activer bascule automatiquement le relais 0 en mode « boost », qui
s'éteint tout seul après un délai fixe.

```js
// Bascule le relais 0 en mode "boost" : à chaque activation, programme son
// extinction automatique après BOOST_DURATION_S secondes.
let BOOST_DURATION_S = 600;

Shelly.addStatusHandler(function (status) {
  if (status.name !== "switch" || status.id !== 0) return;
  if (status.delta.output === true) {
    print("Relais activé, arrêt automatique dans", BOOST_DURATION_S, "s");
    Timer.set(BOOST_DURATION_S * 1000, false, function () {
      Shelly.call("Switch.Set", { id: 0, on: false });
      print("Fin du boost : relais éteint automatiquement");
    });
  }
});
```

# Sécurité et vie privée

- Toute la communication passe en direct entre Jeedom et chaque appareil sur le réseau local :
  aucune donnée ne transite par un service Shelly Cloud.
- Le socket interne entre Jeedom et le démon n'écoute que sur `127.0.0.1` et n'est jamais exposé
  sur le réseau.
- Le mot de passe éventuel d'un appareil Shelly est stocké chiffré par Jeedom et n'est jamais
  journalisé.
