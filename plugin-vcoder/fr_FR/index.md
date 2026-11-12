---
layout: default
title: vCoder — Documentation
lang: fr_FR
pluginId: vcoder
---

# Description

**vCoder** est un explorateur et éditeur de fichiers intégré à Jeedom. Il ouvre dans le navigateur un environnement de développement inspiré de VS Code : arborescence des fichiers, onglets, éditeur Monaco avec coloration syntaxique, recherche dans les fichiers, et un panneau d'assistant IA (Gemini, Claude, ChatGPT, Mistral ou le plugin Code Assistant).

> **Important**
>
> vCoder lit, modifie, déplace et supprime des fichiers de votre installation Jeedom. Une erreur dans un fichier du core ou d'un plugin peut rendre Jeedom inutilisable : faites une sauvegarde avant toute modification importante.

# Prérequis

| Composant | Version / remarque |
|---|---|
| Jeedom | ≥ 4.0 |
| Compte | administrateur (la page et toutes les actions sont réservées aux administrateurs) |
| Plugin Code Assistant | facultatif : nécessaire pour le fournisseur « Code Assistant », le mode Agent, les suggestions inline et l'aperçu / application de patchs |
| Clé d'API Gemini, OpenAI, Claude ou Mistral | facultative : seulement pour utiliser ce fournisseur dans l'assistant IA |

Aucune dépendance à installer, pas de démon.

# Installation et configuration

1. Installer le plugin depuis le Market, puis l'activer.
2. Ouvrir la page de configuration du plugin pour renseigner, si besoin, les clés d'API des fournisseurs IA.
3. Ouvrir vCoder depuis le menu **Plugins → Programmation → vCoder**.

## Page de configuration

**Options IA**

| Option | Rôle |
|---|---|
| Gemini Api Key | clé du fournisseur Gemini |
| OpenAi Api Key | clé du fournisseur ChatGPT |
| Claude Api Key | clé du fournisseur Claude |
| Mistral Api Key | clé du fournisseur Mistral |
| Autoriser l'exécution PHP | désactivé par défaut ; à laisser désactivé sauf besoin précis |

Chaque clé dispose d'un bouton **Test** qui envoie une courte requête au fournisseur et affiche « Provider AI opérationnel » ou l'erreur renvoyée. Le bouton en forme d'œil affiche ou masque la clé. Les clés sont enregistrées chiffrées.

**Options globales** : la langue de l'explorateur, l'ouverture sur le dernier répertoire et le repli du code à l'ouverture concernent l'ancien explorateur elFinder ; elles n'agissent pas sur l'éditeur vCoder décrit ci-dessous.

# Utilisation

La page se compose de cinq zones :

- **barre d'outils** (en haut) : actions sur les fichiers et l'éditeur ;
- **barre d'activité** (à gauche) : Explorateur, Rechercher, Assistant IA, affichage du menu Jeedom, Mise en page ;
- **panneau latéral** : contenu de la vue choisie (arborescence, recherche, réglages) ;
- **zone d'édition** : onglets, fil d'Ariane du fichier, éditeur (un ou deux panneaux côte à côte) ;
- **barre d'état** (en bas) : ligne et colonne du curseur, chemin du fichier, langage (cliquer dessus pour le changer).

## Accès aux fichiers

- L'explorateur part par défaut de `/var/www/html/plugins`. La vue **Mise en page** permet de choisir `/var/www/html` ou `/` comme racine.
- Seuls les fichiers situés sous `/var/www/html` sont accessibles.
- Certains fichiers sont toujours refusés, en lecture comme en écriture : fichiers de configuration (`.env`, `config.php`, `common.config.php`, dossier `core/config`), dossier `backup`, certificats et clés (`.pem`, `.key`, `.crt`…), bases de données et archives (`.sql`, `.db`, `.zip`, `.tar`, `.gz`, `.bak`…). Ils sont aussi ignorés lors du téléchargement d'un dossier.

## Explorateur

Un clic ouvre un fichier dans un onglet. Le clic droit affiche un menu contextuel :

| Sur un dossier | Sur un fichier |
|---|---|
| Ouvrir tous les fichiers | Ouvrir |
| Nouveau dossier, Nouveau fichier | Sauvegarder, Formater le code (si le fichier est ouvert) |
| Copier, Couper, Coller | Copier, Couper, Coller |
| Renommer, Copier le chemin | Révéler dans l'explorateur, Renommer, Dupliquer, Copier le chemin |
| Télécharger le dossier (archive ZIP) | Télécharger |
| Propriétés | Informations |
| Supprimer | Supprimer |

La suppression demande une confirmation ; un dossier est supprimé avec tout son contenu.

**Glisser-déposer** : faire glisser un fichier ou un dossier sur un autre dossier de l'arborescence le déplace. Des fichiers déposés depuis l'ordinateur sur la zone d'édition sont envoyés sur la box. Les boutons **Uploader un fichier** et **Uploader un dossier** de la barre d'outils font de même.

## Éditeur

- Onglets avec indicateur de modification (`*`) ; clic droit sur un onglet : fermer, fermer les autres, fermer à droite, fermer tous, épingler, rouvrir l'onglet fermé.
- Fermer un onglet modifié demande une confirmation ; quitter la page avec des fichiers non enregistrés déclenche l'avertissement du navigateur.
- Vue scindée en deux éditeurs, minimap, retour à la ligne, taille de police, formatage du document, thème clair / sombre, plein écran.
- **Quick Open** : recherche d'un fichier par son nom.
- **Rechercher dans les fichiers** : recherche dans le contenu, limitée au **Plugin courant** ou à toute la **Racine Explorer**.
- **Command Palette** : accès par le clavier à toutes les commandes ci-dessus.
- La vue **Rechercher** de la barre d'activité cherche un fichier par son nom (2 caractères minimum).

Les réglages d'affichage (thème, largeur du panneau, vue scindée, minimap, police, racine, fichiers cachés, onglets ouverts, fichiers récents) sont mémorisés dans le navigateur.

### Raccourcis clavier

| Raccourci | Action |
|---|---|
| Ctrl+S | Sauvegarder |
| Ctrl+Maj+S | Sauvegarder tout |
| Ctrl+W | Fermer l'onglet |
| Ctrl+Maj+T | Rouvrir l'onglet fermé |
| Ctrl+Tab / Ctrl+Maj+Tab | Onglet suivant / précédent |
| Ctrl+Page suivante / Page précédente | Onglet suivant / précédent |
| Ctrl+1 … Ctrl+9 | Aller à l'onglet n |
| Ctrl+P | Quick Open |
| Ctrl+Maj+P | Command Palette |
| Ctrl+Maj+F | Rechercher dans les fichiers |
| Ctrl+B | Afficher / masquer le panneau latéral |
| Ctrl+\ | Vue scindée |
| Ctrl++ / Ctrl+- | Agrandir / réduire la police |
| Alt+Z | Retour à la ligne |
| Maj+Alt+F | Formater le document |

Sur Mac, Cmd remplace Ctrl.

## Mise en page

La vue **Mise en page** (icône engrenage de la barre d'outils ou icône grille de la barre d'activité) regroupe : dossier racine, affichage des fichiers cachés, affichage du menu Jeedom, plein écran.

## Assistant IA

Le bouton robot de la barre d'activité ouvre le panneau **AI Assistant** en choisissant le fournisseur : Code Assistant, Gemini, Claude, ChatGPT ou Mistral. Si du code est sélectionné dans l'éditeur, la zone de saisie est pré-remplie avec ce code (500 premiers caractères).

Le bouton engrenage du panneau affiche les options :

| Option | Rôle |
|---|---|
| AI Provider | fournisseur utilisé |
| Agent | utilise le mode Agent de Code Assistant (outils, demandes d'approbation) |
| Plan Mode | l'agent propose d'abord un plan : **Approuver**, **Modifier** ou **Rejeter** |
| Mode lecture seule | l'agent ne modifie aucun fichier |
| Budget Agent | budget de tokens de l'agent (12 000 par défaut) |
| Context Mode | contexte envoyé : code sélectionné, fichier courant ou projet |
| Temperature, Max Tokens | réglages de génération |
| CA Provider, CA Model | fournisseur et modèle utilisés par Code Assistant (« Auto » = configuration du plugin Code Assistant) |
| Quick Actions | Explain Code, Refactor, Optimize, Add Docs, Debug, Generate Tests |

La réponse s'affiche au fil de l'eau ; le bouton **Stop** l'interrompt. Une icône ouvre la conversation complète dans le plugin Code Assistant.

**Patchs** : quand une réponse contient un patch (bloc `diff`), les boutons **Aperçu** (comparaison côte à côte avant / après) et **Appliquer** apparaissent. L'application demande une confirmation. Nécessite Code Assistant.

**Suggestions inline** : le bouton ampoule de la barre d'outils active les suggestions de code pendant la frappe (PHP, JavaScript, Python, CSS, HTML), fournies par Code Assistant. La barre d'état indique le temps de réponse moyen et le coût du jour. Aucune suggestion n'est demandée pour les fichiers sensibles (`.env`, clés, certificats…).

# FAQ / dépannage

**Accès refusé à ce chemin / fichier.**
Le fichier est hors de `/var/www/html` ou fait partie des fichiers protégés (voir *Accès aux fichiers*). Ce blocage ne peut pas être désactivé.

**« Plugin code_assistant non disponible ».**
Le fournisseur Code Assistant, le mode Agent, les suggestions inline et les patchs exigent le plugin Code Assistant installé. Les autres fournisseurs fonctionnent avec leur seule clé d'API.

**Le bouton Test d'une clé renvoie une erreur.**
Vérifier la clé, sauvegarder la configuration, puis relancer le test. Le message affiché est celui renvoyé par le fournisseur.

**L'upload d'un gros fichier échoue.**
La taille maximale d'un envoi est fixée par la configuration PHP du serveur web (`upload_max_filesize`), pas par vCoder.

**Les vues « Contrôle de version », « Extensions » et le terminal sont vides.**
Ces éléments de l'interface ne sont pas encore fonctionnels.

---

*vCoder — Documentation | Plugin open-source pour Jeedom*
