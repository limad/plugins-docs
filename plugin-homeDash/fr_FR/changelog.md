---
layout: default
title: homeDash — Changelog
lang: fr_FR
pluginId: homeDash
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — homeDash

## Version bêta

### 18/11/2026

- **Ordinateur** : jusqu'à 6 colonnes de tuiles sur un grand écran (4 dès 1024 px), contenu centré ; Réglages, Alertes, Analyse, Diagnostic et Agendas dans une colonne de lecture ; boutons de l'en-tête toujours à la même place, même sur une page courte
- Scénarios sur deux colonnes dès 980 px ; tuiles à leur propre hauteur (plus étirées par une caméra voisine)
- Chat en pleine hauteur sur ordinateur ; survol à la souris sur les boutons, pastilles et tuiles
- Menu latéral replié : info-bulle à droite de l'icône ; liste des pièces estompée en bas quand il en reste à faire défiler
- **« / » ou Ctrl + K** (⌘ + K) place le curseur dans la recherche
- Info-bulles qu'on peut survoler pour les lire
- Un double clic sur un interrupteur ou un scénario n'envoie qu'une commande
- Messages en bas de l'écran selon leur niveau (info, avertissement, erreur), avec icône ; ils restent affichés sous la souris, se ferment d'un toucher
- Petits boutons d'icône (repli d'une pièce, caméra, suppression d'une alerte) de nouveau carrés
- Une lampe éteinte puis rallumée par On / Off garde son niveau (jamais 100 % deviné) ; une valeur arrivée pendant l'envoi n'est plus perdue
- Relais à plusieurs voies : allumé tant qu'une voie l'est, suivi en direct
- Filtre « lumières allumées » (et les autres résumés) suivi en direct ; température suivie même quand ce n'est pas une mesure de la carte
- Alertes : blocs dépliés et textes développés gardés à la relecture
- Session expirée : les préférences du compte ne passent plus au compte suivant ; la saisie de l'écran de connexion est gardée
- Fiche équipement : texte d'un message gardé pendant le repeint ; Diagnostic : pièce, type et recherche remis après la mesure
- Caméras : vidéo en direct d'un flux placé à la racine de la caméra ; identifiants du flux gardés par la box ; image ou segment incomplet refusé
- Agendas : une plage de nuit modifiée garde la bonne durée (une plage de 08:00 à 10:00 était enregistrée sur 26 h)
- La maison est relue quand un autre appareil du même compte l'a relue après un changement de la configuration (plugin ou pièce exclus)

### 17/11/2026

- Nouveau mode de cartes **Mini** (Réglages > Affichage > Cartes) : tuiles dans le style des réglages rapides d'Android, deux par ligne sur un téléphone en portrait
- Tuiles Mini : contenu toujours en haut quand une tuile voisine plus haute l'étire
- Réglages > Affichage rangés en trois cartes : Cartes, Écran et Général
- **Caméras** : image de l'instantané (Netatmo, Frigate…) sur la carte ; bouton caméra sur les tuiles compactes et Mini, ⋯ > Caméra partout
- Fenêtre caméra : image en grand rafraîchie toutes les 2 s, passage d'une caméra à l'autre, surveillance en interrupteur, accès aux Contrôles
- **Vidéo en direct** dans la fenêtre caméra (bouton Direct) et depuis la fiche (« Voir en direct ») : le flux passe par la box, l'adresse de la caméra n'est jamais envoyée au téléphone
- **Réorganiser** aussi dans Toute la maison et dans une pièce (bouton ⇅) : pièces et appareils par glisser-déposer, ordre propre à cette vue (les favoris gardent le leur), suivi par le menu des pièces
- Sécurité : l'adresse de l'instantané d'une caméra (avec sa clé) n'est plus envoyée au téléphone ; le lien vers le flux vidéo brut est retiré
- **Barre du bas à 4 boutons** sur téléphone (portrait et paysage) : Favoris, Chat IA (Scénarios sans assistant partagé), Équipements et Plus, qui ouvre les autres vues et porte le nombre d'alertes
- **Chat** : choix des assistants partagés dans la configuration du plugin, parmi les assistants MCP et Assist d'Ai Assistant ; le Chat ne propose que ceux-là
- Pastille **« Type ▾ »** en tête des pièces sur téléphone : une rangée de pastilles de moins, plus d'appareils à l'écran
- Téléphone en paysage : nom de la box masqué et pastilles resserrées
- Cadran du thermostat plus compact (température 80 px, consigne 69 px) ; signes + et − lisibles
- Appareil injoignable : « Dernier état : éteint » et icône atténuée ; en cartes Mini, une icône de lien coupé à la place du mot
- Recherche sans résultat : bouton « Effacer la recherche » ; caméra sans image : icône de caméra barrée
- Fiche d'une lumière : blanc actuel affiché et blancs proposés (chaud, neutre, froid)
- Curseur sans valeur connue au milieu, valeur « — » ; scénarios seuls dans leur groupe réunis
- Lecteur en cours de lecture lisible en mode sombre ; cartes de détail qui ne s'étirent plus à la hauteur de leur voisine ; libellés de la barre du bas plus grands
- Zones de toucher agrandies (titres des cartes, noms des tuiles, rond de couleur) ; lignes des menus ⋯ resserrées au doigt
- Nom affiché du plugin : « Home Dash »
- Boutons qui attendent la box (Actualiser, Enregistrer, Se connecter, Envoyer à la box…) : l'icône ou un petit anneau tourne pendant l'attente, un second appui est ignoré
- Après la fermeture d'une fiche ouverte par ⋯ › Contrôles, le focus revient sur le ⋯ de la carte
- Icône « Afficher le mot de passe » de nouveau dans son champ sur l'écran de connexion
- Téléphone en paysage : les tuiles occupent toute la largeur
- Une nouvelle vue s'ouvre en haut ; le retour à Équipements retrouve sa position ; toucher la vue en cours remonte en haut
- **Alertes** : messages semblables réunis sur une ligne (« ×8 depuis… ») et supprimés ensemble ; messages des logs sans erreur récente réunis par log (« Venant des logs ») ; le badge compte les mêmes lignes ; mises à jour comptées comme le core, Alertes et Analyse d'accord
- Commandes « message » qui attendent une valeur (durée, adresse…) : champ simple à côté d'Envoyer, Entrée envoie
- En-tête fixe dans les panneaux (fiche, caméra, chat, agenda) ; Agendas : toute la carte ouvre la plage
- Fiche : commandes de maintenance (firmware, redémarrage, réinitialisation, calibrage) sorties des Contrôles, dans Toutes les commandes > **Maintenance** (replié) ; la mise à jour du firmware demande confirmation
- Fenêtre caméra : âge de l'image (« Image d'il y a 4 s », signalée au-delà de 30 s) ou « En direct »
- Chat : trois exemples de questions qui remplissent le champ sans l'envoyer
- Thèmes : accent plus lisible pour Delprat et Halexa sombre ; l'éditeur prévient quand l'accent est peu lisible sur les cartes, aperçu en direct (lampe allumée / éteinte)
- Téléphone en paysage : les pastilles des pièces se rangent en descendant et reviennent en remontant
- Lien « Aller au contenu » au premier Tab ; noms lisibles par les lecteurs d'écran (repli des pièces, groupes des Réglages)

### 16/11/2026

- Interrupteur pour les paires de commandes On / Off liées à un état
- Menu ⋯ des scénarios : lancer ou arrêter, détails (mode, déclencheurs, prochain lancement, description), ajout aux favoris, masquer
- Scénarios favoris, affichés dans la vue Favoris après les appareils
- Favoris en une seule grille par défaut, réorganisable par glisser-déposer (« Par pièce » au choix dans les Réglages)
- Bouton pour chaque commande « message » sur les cartes en ligne et les cartes média
- Favoris au même niveau qu'Équipements dans le menu latéral et la barre du bas
- Glisser-déposer : une carte lâchée dans un espace vide va à la place la plus proche, la page défile près des bords de l'écran
- En-tête aligné sur la grille des tuiles
- Plugin publié en bêta

### 15/11/2026

- Cadran du thermostat en blocs carrés avec chiffres Starck : la consigne chevauche la température mesurée
- Anneau qui tourne autour de l'icône tant qu'une commande attend sa réponse
- Suppression du bouton › des tuiles, qui faisait doublon avec ⋯ > Contrôles
- Seuil de pile faible du core Jeedom utilisé partout ; corrections d'accessibilité et de repeint en direct (audit)
- Sécurité : accès HTTP direct refusé aux fichiers PHP de `plugin_info` ; certificats TLS des caméras vérifiés, sauf sur le réseau local
- Une commande dont le plugin écrit du texte pendant l'exécution ne fait plus échouer l'appel
- Icônes de l'app et du plugin sur le fond ardoise du logo
- Installation de l'app sur le téléphone toujours possible ; mode hors ligne retiré (bloqué par la configuration de Jeedom)
- Jeedom 4.5 et Debian 12 requis
- Page de configuration : textes plus précis sur l'actualisation et les objets masqués

### 14/11/2026

- Tuiles dessinées de la même façon sur tous les écrans : icône, nom et état sur une ligne
- Contenu centré ; menu latéral replié automatiquement entre 980 et 1099 px
- Ligne de résumés masquée par défaut, affichée par un bouton à côté de la recherche ; chaque résumé filtre exactement ce qu'il compte
- Onglet Agendas affiché seulement si on le choisit dans les Réglages
- Nom du plugin de l'équipement en tête de son menu ⋯
- Les équipements désactivés ne sont plus jamais envoyés à l'app
- Fiche équipement plus rapide : rendue une fois à l'ouverture, seule la section des courbes est refaite ensuite
- Affichage des nombres plus rapide sur téléphone

### 13/11/2026

- Première version : app mobile (PWA) servie par Jeedom, connexion par le compte Jeedom
- Préférences par utilisateur (favoris, masqués, ordre), QR codes des adresses de l'app, exclusion de plugins et d'objets
- Classement des équipements par types génériques, cartes lisibles (boutons en paires, état écrit)
- Tuiles en ligne sur téléphone, comme Google Home, une par ligne en portrait
- Menu latéral avec pièces imbriquées (objets parents Jeedom), repliable en rail d'icônes ; les vues restent visibles, seule la liste des pièces défile
- Icônes Material Symbols Rounded en trait fin, icône « routine » pour les scénarios
- **Thèmes** : thèmes fournis (Google Home par défaut, Delprat, Alexa, Terracotta, Sauge, OLED, Contraste élevé…), thèmes personnels, import / export, partage par un admin ; polices Inter et Roboto Flex ; arrondi des cartes réglable
- Fiche équipement : courbes, commandes brutes, code d'accès, et en tête un panneau de contrôle composé selon ce que l'appareil sait faire (accès par ⋯ > Contrôles, le titre d'une carte en ligne ou un appui long)
- Option **Cartes compactes** : tuiles sans curseur, le reste dans le panneau de contrôle
- Rond de couleur sur les tuiles des lumières colorées : 8 couleurs, blancs chauds à froids, ou autre couleur au choix ; la carte garde la teinte du thème
- Thermostat dans le style de Google Home : états chauffe et arrêt, modes dans une liste à choix unique, grande température en chiffres Starck
- **Agendas** du plugin Agenda : liste compacte, panneau d'édition par plage, état écrit (« En cours · jusqu'à 06:00 », « Prochaine : demain 07:00 »), répétition décrite, interrupteur par agenda, heures en 24 h, jours de la semaine en pastilles rondes
- Onglet **Alertes** : appareils en défaut, messages Jeedom triés, erreurs des logs regroupées, mises à jour
- Onglet **Analyse** : santé de Jeedom et démons des plugins, démarrage ou relance d'un démon
- Vue **Diagnostic** (Réglages) pour mesurer l'app sur un vrai téléphone, rapport copiable ou envoyé à la box
- Filtre par type, « Tout allumer / Tout éteindre » pour les lumières et les prises
- Résumés de la maison et de chaque pièce
- Cartes média et images des caméras (leurs adresses ne sont jamais envoyées à l'app)
- Chat avec les assistants IA
- Personnalisation d'une carte : commandes affichées, taille, pas du curseur
- Interrupteurs marche / arrêt en option ; confirmation demandée quand la commande Off est dangereuse
- Page d'accueil au choix, vue courante gardée dans l'adresse (#)
- Actualisation des mesures toutes les secondes par défaut (1, 2, 5 ou 10 s) ; taille de l'interface, police et plein écran réglables
- Zones de toucher de 44 px sans changer le dessin, curseurs et listes faciles à toucher sur téléphone, menu ⋯ utilisable au clavier, info-bulles sur tous les boutons
- Les menus restent à l'écran : ouverture vers le haut ou défilement quand la place manque
- Équipements sans pièce non chargés par défaut
- Rapide sur téléphone : seuls les appareils touchés par une mise à jour sont redessinés, chargement découpé en étapes, Agendas et Réglages chargés à la demande
- Logo, icônes de l'app et favicon
