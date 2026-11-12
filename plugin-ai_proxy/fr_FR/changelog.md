---
layout: default
title: Passerelle OAuth IA — Changelog
lang: fr_FR
pluginId: ai_proxy
---

# Changelog Passerelle OAuth IA

## Version en cours de développement

### 09/11/2026

- **Nouveautés et corrections** : choix de la stratégie quand un fournisseur a plusieurs comptes (rotation collante ou premier compte d'abord), avec des exceptions par fournisseur ; le cache Claude de 5 minutes et d'une heure est pris en charge et compté à son tarif dans la fenêtre Capacité ; arguments des outils Codex corrigés.
- **Sécurité et fiabilité** : une réponse en flux se termine proprement dès la fin annoncée par le fournisseur.

### 08/11/2026

- **Nouveautés et corrections** : nouvelle fenêtre **Capacité** qui estime le travail restant sur chaque compte, avec import de relevés faits hors de la passerelle ; tous les formats d'API (OpenAI, Anthropic, Gemini natif) fonctionnent avec tous les comptes ; cache de prompt activé automatiquement pour Claude ; Cursor : les outils fonctionnent de bout en bout, la consommation de tokens est comptée, un modèle hors forfait renvoie une erreur claire ; Cursor fonctionne de nouveau après le changement de service côté Cursor ; l'API Responses accepte une entrée en simple texte ; la connexion d'un nouveau compte Claude fonctionne de nouveau et les modèles Claude récents sont acceptés ; la consommation affichée pour Gemini ne compte plus deux fois le cache et inclut la réflexion ; traductions mises à jour.
- **Sécurité et fiabilité** : une réponse ChatGPT/Codex coupée en cours renvoie une erreur au lieu d'une réponse incomplète ; un audio que Gemini ne peut pas recevoir est refusé avec une erreur claire au lieu d'être ignoré, sans consommer de quota.

### 07/11/2026

- **Nouveautés et corrections** : nouveau fournisseur **Mistral Vibe** (connexion par le navigateur ou clé collée, quota mensuel affiché) ; Mistral : complétion de code, embeddings, lecture de documents et transcription audio ; réflexion des modèles Mistral à raisonnement renvoyée à part ; message clair si la clé Vibe est révoquée ; embeddings avec un compte GitHub Copilot ; quota du groupe Gemini 4 Argon affiché pour les comptes Google AI Pro ; documentation utilisateur ; traductions mises à jour.
- **Sécurité et fiabilité** : un modèle Mistral Labs non activé ne met plus le compte en pause ; un refus Google sans détail n'est plus compté comme un quota atteint dans les statistiques.

### 06/11/2026

- **Nouveautés et corrections** : les modèles Gemini ne sont envoyés qu'aux comptes Google qui y ont accès (fin des erreurs 404 sur les comptes gratuits) ; traductions mises à jour.
- **Sécurité et fiabilité** : un refus Google sans détail de quota ne met plus les comptes en pause.

### 03/11/2026

- **Nouveautés et corrections** : connexion d'un compte : le lien d'autorisation s'affiche toujours et la raison d'un échec est plus claire.
- **Sécurité et fiabilité** : en mode HTTPS, la liste blanche d'IP s'applique à l'adresse réelle du client et ne peut plus être contournée.

### 31/10/2026

- **Nouveautés et corrections** : consommation de tokens exacte ; le cache demandé par le client est conservé.

### 29/10/2026

- **Nouveautés et corrections** : page du plugin réorganisée (titre « Gestion - Passerelle OAuth IA », tuiles Documentation et Community en dernier).
- **Sécurité et fiabilité** : le dossier de données du plugin (clé API, comptes) n'est plus accessible depuis le web.

### 27/10/2026

- **Nouveautés et corrections** : le plugin change d'identifiant (ancien `ai_auth2api`, désormais `ai_proxy`) ; accès depuis le réseau local au choix : non exposé, HTTP direct ou HTTPS via Apache (avec avertissement et confirmation), configuration Apache retirée à la désinstallation ; Node.js installé automatiquement s'il est absent ou trop ancien.
- **Sécurité et fiabilité** : corrections issues d'un audit de sécurité.

### 25/10/2026

- **Nouveautés et corrections** : le journal du démon suit le niveau de log choisi dans Jeedom ; modèles présentés avec des libellés lisibles.

### 23/10/2026

- **Nouveautés et corrections** : nouvelles fenêtres **Aide Clients** et **Santé** ; fenêtres Comptes et Modèles repensées ; traductions automatiques.

### 14/10/2026

- **Nouveautés et corrections** : guide d'utilisation avec Claude Desktop ; le résultat d'une connexion de compte est fiable.
- **Sécurité et fiabilité** : fichiers internes du plugin inaccessibles depuis le web ; corrections issues d'un audit (droits des fichiers, port, enregistrement de la configuration).

### 06/10/2026

- **Sécurité et fiabilité** : le démon est retrouvé même si son fichier de suivi a disparu.

### 05/10/2026

- **Nouveautés et corrections** : choix des modèles affichés dans la liste des modèles, avec une fenêtre de sélection ; fenêtre Comptes améliorée.

### 04/10/2026

- **Nouveautés et corrections** : option d'API compatible Ollama ; nom affiché « Passerelle OAuth IA » ; nouvelle icône.
- **Sécurité et fiabilité** : le service est installé dans une version figée et vérifiée.

### 01/10/2026

- **Nouveautés et corrections** : fenêtre de gestion des comptes, avec GitHub Copilot ; icônes des fournisseurs.

### 28/09/2026

- **Nouveautés et corrections** : première version. Comptes Claude, Codex, Cursor, Antigravity, Gemini CLI et GitHub Copilot exposés en API locale compatible OpenAI et Anthropic.
