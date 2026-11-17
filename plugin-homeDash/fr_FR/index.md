---
layout: default
title: Home Dash — Plugin homeDash pour Jeedom
lang: fr_FR
pluginId: homeDash
---

# Description

Interface mobile épurée pour piloter la maison depuis un téléphone, une tablette ou un ordinateur :
favoris, pièces, lumières, prises, volets, thermostats, lecteurs, caméras et scénarios, mis à jour
en temps réel. L'application est servie par Jeedom : aucun port à ouvrir, aucun réglage Apache,
aucun service extérieur.

# Accès

- Réseau local : `http://IP_JEEDOM/plugins/homeDash/app/`
- Extérieur : `https://xxxx.eu.jeedom.link/plugins/homeDash/app/`

La page du plugin (Plugins → Organisation → Home Dash) affiche les deux adresses avec un QR code
à scanner depuis le téléphone.

# Connexion

On se connecte avec un compte Jeedom (double authentification prise en charge).
« Rester connecté sur cet appareil » enregistre l'appareil comme dans Jeedom.
Un compte au profil restreint ne voit et ne commande que les équipements et scénarios autorisés.
Les équipements sans objet (pièce) Jeedom ne sont jamais affichés.

# Installer sur le téléphone

Ouvrir l'adresse extérieure (HTTPS), puis :

- Android (Chrome) : menu ⋮ → « Installer l'application » ;
- iPhone (Safari) : Partager → « Sur l'écran d'accueil ».

# Naviguer

- **Téléphone** : barre du bas à quatre boutons : **Favoris**, **Chat IA** (ou **Scénarios** si aucun
  assistant n'est partagé), **Équipements** et **Plus**, qui ouvre les autres vues (Scénarios, Alertes,
  Analyse, Agendas, Réglages) et affiche le nombre d'alertes.
- **Ordinateur** : menu latéral avec les vues et les pièces (objets Jeedom, sous-pièces en retrait) ;
  il se replie en rail d'icônes.
- **Pièces** : pastilles en haut de la vue Équipements. Sur téléphone, la pastille **Type ▾** en tête
  filtre par type d'appareil (lumières, prises, chauffage…) ; avec Lumières ou Prises, « Tout allumer /
  Tout éteindre » demande confirmation.
- **Recherche** dans le nom des appareils ; bouton ⓘ pour afficher les résumés (lumières allumées,
  ouvertures, température…), qui filtrent d'un toucher.
- **Réorganiser** (⇅) : pièces et appareils se déplacent par glisser-déposer, dans les Favoris comme
  dans Toute la maison.
- **Actualiser** (⟳) relit la maison ; le bouton tourne pendant la lecture.

# Cartes

- Trois dispositions (Réglages > Cartes) : **complètes**, **compactes** (sans curseur, le reste dans
  les Contrôles) et **Mini** (petites tuiles, deux par ligne sur téléphone).
- Toucher une tuile allume ou éteint ; le menu **⋯** donne les **Contrôles** (panneau complet de
  l'appareil), l'ajout aux favoris, « Masquer », « Personnaliser la carte » (commandes affichées,
  taille) et, pour une caméra, sa fenêtre. Un appui long ouvre aussi les Contrôles.
- **Thermostat** : température mesurée et consigne côte à côte, + / − pour la consigne, mode forcé
  dans une liste, pastille Chauffe / Arrêt.
- **Lumière colorée** : rond de couleur avec couleurs, blancs (chaud, neutre, froid) et « Autre couleur ».
- **Appareil injoignable** : bordure teintée, dernier état connu en grisé.
- Une commande qui attend la réponse de Jeedom affiche un anneau qui tourne ; en cas d'échec, un
  message s'affiche et seul l'appareil touché revient à son état d'avant.

# Caméras

L'image de la caméra (ou son instantané) s'affiche sur la carte. Un toucher ouvre la fenêtre caméra :
image en grand, passage d'une caméra à l'autre, surveillance en interrupteur, et vidéo **en direct**
pour les caméras qui fournissent un flux. Images et vidéo passent par la box : l'adresse de la caméra
n'est jamais envoyée au téléphone.

# Scénarios

Liste des scénarios actifs : un toucher lance ou arrête. Le menu ⋯ donne les détails (mode,
déclencheurs, prochain lancement, description), l'ajout aux favoris et « Masquer ».

# Chat

Avec le plugin **Ai Assistant**, l'application propose un chat avec les assistants choisis dans la
configuration du plugin (assistants **MCP** et **Assist** seulement). Sans assistant coché, le chat
n'apparaît pas.

# Alertes et Analyse

- **Alertes** : appareils en défaut et messages Jeedom triés ; pour un administrateur, aussi les erreurs
  des logs des dernières 24 h et les mises à jour disponibles, et la suppression des messages.
- **Analyse** : piles faibles, et pour un administrateur la santé de Jeedom et l'état des démons
  des plugins, avec Démarrer / Relancer.

# Agendas

Si le plugin **Agenda** est actif, l'onglet *Agendas* (à choisir dans Réglages > Général) liste ses
agendas et leurs plages : heures, jours de la semaine, jours fériés, plage en cours et prochaine.
Un administrateur Jeedom peut changer les heures, les jours et les jours fériés d'une plage répétée
chaque jour, et activer ou désactiver un agenda ; les autres comptes voient les agendas en lecture seule.
Les actions des plages se règlent toujours dans le plugin Agenda.
Si une plage modifiée couvre l'heure actuelle, ses actions sont lancées tout de suite.

# Favoris, ordre et éléments masqués

Les favoris, l'ordre des pièces et des appareils, la page d'accueil et les éléments masqués sont
enregistrés par compte Jeedom : on les retrouve sur tous ses appareils. Ils ne modifient pas Jeedom.
Les éléments masqués se réaffichent dans Réglages > Éléments masqués.

# Thèmes

Dans **Réglages > Thèmes** :

- **Auto / Clair / Sombre** : propre à chaque appareil (Auto suit le réglage du téléphone).
- **Thèmes fournis** : Ghome (par défaut), Delprat, Halexa, Nord, Contraste élevé, OLED ambre,
  Sauge, Terracotta.
- **Créer un thème** : part du thème affiché. Six couleurs clés (fond, cartes, texte, accent,
  appareil allumé, alerte) ; les autres sont calculées, ou réglables dans « Plus de couleurs ».
  Arrondi, police, ombre, mise en avant et curseurs se choisissent dans des listes. L'aperçu est
  immédiat et les contrastes trop faibles sont signalés.
- **Importer** : un thème est un petit fichier `.json` (couleurs seulement), qui s'ouvre dans
  l'éditeur avant d'être enregistré.
- Le thème choisi et ses propres thèmes (20 au plus) suivent le compte Jeedom sur tous ses appareils.
- **Partager** (administrateurs) : rend un de ses thèmes visible par tous les utilisateurs.

# Réglages de l'appareil

Taille de l'interface, police, horloge, disposition des cartes, interrupteurs à la place des boutons,
arrondi des cartes et délai d'actualisation des mesures se règlent par appareil (Réglages > Écran,
Cartes, Général). Réglages > Diagnostic mesure l'écran et la vitesse de l'appareil.

# Configuration du plugin

- **Actualisation** : délai maximal de regroupement des mesures (1 s par défaut).
- **Plugins exclus de l'application** : leurs équipements n'apparaissent pas, pour aucun utilisateur.
- **Objets exclus de l'application** : les équipements de ces objets et de leurs sous-objets
  n'apparaissent pas et ne peuvent pas y être commandés.
- **Assistants partagés dans le chat** : assistants MCP et Assist d'Ai Assistant proposés dans le chat.

Jeedom n'est jamais modifié par ces choix.

# Changelog

Disponible [ici](./changelog.html).

# Crédits

Style et quelques concepts inspirés du projet « Maison » de Julien Delprat (gitlab.com/jdelprat/domotique).
