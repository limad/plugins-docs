---
layout: default
title: vCoder — Changelog
lang: fr_FR
pluginId: vcoder
---

# Changelog vCoder

## Version en cours de développement

### 23/10/2026

- **Nouveautés et corrections** : traductions mises à jour.

### 17/10/2026

- **Nouveautés et corrections** : menus contextuels de l'explorateur et des onglets plus fiables ; la page de configuration (bouton **Test** des clés d'API) fonctionne sur les installations Jeedom récentes.

### 15/10/2026

- **Nouveautés et corrections** : les confirmations et saisies (fermeture d'un onglet modifié, création, renommage, suppression, déplacement, vidage de la conversation IA, plan de l'agent, application d'un patch) utilisent les fenêtres de dialogue de Jeedom ; l'ancien explorateur affiche un message clair au lieu de rester bloqué quand il ne peut pas démarrer.

### 20/09/2026

- **Sécurité et fiabilité** : les clés d'API des fournisseurs IA sont enregistrées chiffrées (les clés existantes sont chiffrées lors de la mise à jour) ; les fichiers internes du plugin ne sont plus accessibles depuis le web.

### 04/06/2026

- **Nouveautés et corrections** : l'explorateur peut partir de `/var/www/html` ou de `/` en plus du dossier des plugins.

### 19/05/2026

- **Nouveautés et corrections** : glisser-déposer plus fiable (plus de double envoi, onglets mis à jour après le déplacement d'un dossier, déplacement d'un dossier dans lui-même refusé) ; l'envoi d'un dossier conserve son arborescence et signale les fichiers en erreur ; un onglet reste ouvert si sa sauvegarde échoue ; les onglets d'un fichier supprimé se ferment proprement ; la recherche ignore les résultats périmés ; le bouton de l'assistant IA est toujours cliquable ; meilleur affichage des blocs de code dans la conversation ; copie possible même sans accès au presse-papiers ; les réponses IA en cours sont arrêtées en quittant la page ; la barre d'outils ne déborde plus quand le panneau IA est ouvert ; réglages mémorisés réparés s'ils étaient invalides.
- **Sécurité et fiabilité** : contrôles renforcés sur l'envoi, la copie, le renommage, la suppression et le téléchargement de dossiers ; les fichiers sensibles sont exclus des dossiers téléchargés et masqués dans l'explorateur ; les liens symboliques ne sont plus suivis ; aucune suggestion inline n'est demandée pour les fichiers sensibles ; messages d'erreur clairs en cas d'échec d'un envoi, d'une lecture ou d'un téléchargement.

### 18/05/2026

- **Nouveautés et corrections** : le panneau IA s'adapte à la largeur de l'écran et replie l'explorateur au besoin ; les panneaux latéraux ne recouvrent plus l'éditeur ; les boutons **Aperçu** et **Appliquer** des patchs sont utilisables.

### 17/05/2026

- **Nouveautés et corrections** : éditeur complet façon VS Code (onglets, vue scindée, Quick Open, recherche dans les fichiers, Command Palette, raccourcis clavier) ; assistant IA avec Gemini, Claude, ChatGPT, Mistral et le plugin Code Assistant (mode Agent, plan à approuver, suggestions inline, aperçu et application de patchs).

### 04/01/2026

- **Nouveautés et corrections** : première version de l'explorateur / éditeur de fichiers.
