---
layout: default
title: Google Assistant — Documentation
lang: fr_FR
pluginId: google_assistant
---

# Description

Le plugin **Google Assistant** envoie des commandes texte au **vrai Google Assistant** de votre compte Google et récupère sa réponse comme valeur de commande Jeedom. Il fonctionne aussi pour les commandes domotiques (« éteins le salon »), pour lesquelles l'API officielle de Google ne renvoie souvent aucun texte : le plugin contourne ce défaut en lisant aussi la réponse « écran » de l'Assistant.

C'est un portage du composant Home Assistant [google_assistant_sdk_custom](https://github.com/tronikos/google_assistant_sdk_custom).

Le plugin est un **cerveau**, pas un haut-parleur : pour entendre la réponse, il s'appuie sur le plugin [TTS Cast](https://community.jeedom.com/tag/plugin-ttscast) s'il est installé (voir [Restitution audio](#restitution-audio-optionnel)).

> **Important**
>
> Le plugin n'écoute jamais : pas de mot-clé de réveil, pas de micro. Tout part d'un scénario ou d'une exécution manuelle de la commande.

# Prérequis

| Élément | Détail |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | ≥ 11 |
| Compte Google | celui qui utilise déjà Google Assistant / Google Home |
| Projet Google Cloud | le vôtre, gratuit : voir [Créer son application Google et lier son compte](creation-app-google) |
| Accès externe Jeedom | configuré et joignable depuis Internet (Réglages > Système > Configuration, onglet Réseaux) pour la liaison du compte en un clic ; sinon, procédure de secours ci-dessous |
| TTS Cast | optionnel, pour la lecture à voix haute |

# Installation

1. Installez le plugin depuis le Market, puis activez-le.
2. Lancez l'installation des **dépendances** (environnement Python) depuis la page de configuration du plugin.
3. Le **démon** se gère depuis la même page (bloc Démon standard de Jeedom). Un seul démon sert tous les équipements.

# Configuration

## Lier votre compte Google

Chaque utilisateur crée sa **propre application Google**, dans son propre projet Google Cloud. L'auteur du plugin n'y a jamais accès.

> **Guide pas à pas illustré** : [Créer son application Google et lier son compte](creation-app-google).
>
> Dans **Plugins > Communication > Google Assistant > Configuration**, le bouton **Assistant de configuration Google** reprend ces étapes avec les valeurs réelles de votre Jeedom, chacune avec un bouton **Copier**.

En résumé :

1. Dans la [console Google Cloud](https://console.cloud.google.com/), créez un projet et activez l'**API Google Assistant**.
2. Configurez Google Auth Platform (audience **Externe**) et **publiez** l'application. Sinon, l'autorisation expire tous les 7 jours.
3. Créez un client OAuth de type **Application Web**. Dans **URI de redirection autorisés**, collez la valeur du champ **URI de redirection** de la page Configuration du plugin.
4. Dans la page Configuration du plugin, collez le **Client ID** et le **Client Secret**, puis **sauvegardez**.
5. Ouvrez Jeedom **par son accès externe**, connecté en administrateur, puis cliquez sur **Se connecter avec Google**. Acceptez les autorisations demandées, puis cliquez sur **Lier le compte** sur la page affichée par votre Jeedom.
6. Vérifiez avec **Tester la connexion**.

Le jeton d'accès est récupéré et enregistré par Jeedom : vous n'avez rien à recopier. Il reste valable tant qu'il n'est pas révoqué (changement de mot de passe Google, retrait de l'accès, ou application restée « En test »). En cas d'erreur d'identifiants, relancez simplement **Se connecter avec Google**.

## Options de la page Configuration

| Option | Rôle |
|---|---|
| Client ID / Client Secret | Identifiants du client OAuth « Application Web » |
| URI de redirection | Calculée à partir de l'accès externe, à déclarer telle quelle chez Google (bouton **Copier**) |
| URL générée | Affichée après un clic sur « Se connecter » : utile pour diagnostiquer une erreur `redirect_uri_mismatch` |
| Refresh Token | Rempli automatiquement ; à saisir à la main uniquement avec la procédure de secours |
| Langue | Langue des échanges avec l'Assistant : de-DE, en-AU, en-CA, en-GB, en-IN, en-US, es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR |
| Gemini (reformulation) | Voir [Reformulation Gemini](#reformulation-gemini-optionnel) |
| Port socket interne | Port local du démon (55231 par défaut) ; redémarrer le démon après modification |

## Procédure de secours (sans accès externe)

Si votre Jeedom n'a pas d'accès externe joignable, le bouton **Se connecter avec Google** ne peut pas fonctionner : Google exige une adresse de retour HTTPS déclarée à l'avance.

À faire **une fois, depuis votre PC** (pas depuis la box) :

1. Créez dans votre projet un client OAuth de type **Application de bureau** (*Desktop app*) et téléchargez son fichier `client_secret.json`.
2. Lancez l'outil officiel de Google :

```bash
python -m pip install --upgrade "google-auth-oauthlib[tool]"
google-oauthlib-tool \
  --scope https://www.googleapis.com/auth/assistant-sdk-prototype \
  --scope https://www.googleapis.com/auth/gcm \
  --save --client-secrets client_secret.json
```

3. Validez les autorisations dans la page Google qui s'ouvre. Le terminal indique le chemin du fichier `credentials.json` généré : il contient le Client ID, le Client Secret et le Refresh Token.
4. Copiez ces trois valeurs dans la page Configuration du plugin, sauvegardez, puis cliquez sur **Tester la connexion**.

Pour la reformulation Gemini, ajoutez ces deux autorisations à la commande :

```bash
  --scope https://www.googleapis.com/auth/cloud-platform \
  --scope https://www.googleapis.com/auth/generative-language.retriever \
```

# Utilisation

## Créer les équipements

Depuis **Plugins > Communication > Google Assistant** :

- **Ajouter** : crée un équipement.
- **Synchroniser** : crée un équipement Google Assistant pour chaque appareil TTS Cast qui n'en a pas encore, déjà relié à cet appareil. Les équipements existants ne sont jamais supprimés.
- **Santé** : état du compte Google, de Gemini, des dépendances, du démon et dernière réponse de chaque équipement.

Le compte Google est commun à tous les équipements. Créer **un équipement par pièce** a du sens : chacun peut faire lire sa réponse sur une enceinte différente (« Google Assistant Salon », « Google Assistant Cuisine »…).

La liaison du compte lance aussi une synchronisation TTS Cast : une notification indique le nombre d'équipements créés.

## Envoyer une commande

La commande action **Envoyer une commande** (type message) attend un texte libre, comme une commande de notification. Dans un scénario, bloc **Action**, choisissez cette commande et tapez le texte (ou une variable, un tag) à envoyer à Google Assistant.

La réponse met à jour la commande info **Dernière réponse**, ce qui déclenche les scénarios qui l'utilisent. Si Google ne renvoie aucun texte, la commande prend la valeur « Réponse vocale uniquement (pas de texte fourni par Google) » ou « Non compris par Google Assistant ».

Sur le dashboard, la tuile affiche l'échange sous forme de dialogue (question, réponse) et peut lire l'audio de la réponse de Google.

## Commandes

| Commande | Type | Rôle |
|---|---|---|
| Envoyer une commande | action (message) | Texte envoyé à Google Assistant |
| Dernière commande envoyée | info texte | Dernier texte envoyé |
| Dernière réponse | info texte | Réponse de Google Assistant, à utiliser dans les scénarios |
| Dernière commande comprise | info binaire (historisée) | 1 si Google a compris la demande, 0 sinon |
| Dernier dialogue (tuile) | info texte | Contenu de la tuile de dialogue (texte, audio, reformulation Gemini éventuelle) |

Les commandes info sont créées masquées (leur contenu est déjà dans la tuile), mais restent utilisables dans les scénarios. Vous pouvez les afficher si besoin.

Sur la fiche d'un équipement, le bouton **Défaut config** recrée les commandes manquantes et réapplique le widget de dialogue.

Exemple de scénario : action `#[Salon][Google Assistant Salon][Envoyer une commande]#` avec le message « allume la lumière du salon », puis condition sur `#[Salon][Google Assistant Salon][Dernière commande comprise]# == 1`.

## Restitution audio (optionnel)

Si le plugin **TTS Cast** est installé avec au moins un appareil Google Cast, le champ **Appareil TTS Cast** apparaît sur la fiche de chaque équipement (onglet Équipement). Choisissez l'appareil : la réponse de cet équipement y sera lue à voix haute, avec le moteur de synthèse vocale de TTS Cast. Sans appareil choisi, l'équipement reste en texte seul.

Pour qu'une exécution précise ne soit pas lue à voix haute, cochez **Silencieux** sur le bloc d'action du scénario. La commande **Dernière réponse** est mise à jour normalement ; seule la lecture est sautée.

## Reformulation Gemini (optionnel)

Quand Google Assistant ne comprend pas une phrase indirecte (« il fait sombre dans le séjour »), le plugin peut demander à **Gemini** de la reformuler en commande directe (« allume la lumière du séjour »), puis la renvoyer **une seule fois** à l'Assistant. Désactivé par défaut. Gemini utilise le même compte Google, sans clé API.

**Mise en place**

1. Activez l'API **Generative Language** sur votre projet Google Cloud : [console.cloud.google.com/apis/library/generativelanguage.googleapis.com](https://console.cloud.google.com/apis/library/generativelanguage.googleapis.com). Le lien est aussi dans l'assistant de configuration du plugin.
2. Si votre compte a été lié avant l'ajout de Gemini, **reliez-le à nouveau** avec **Se connecter avec Google** : l'ancienne autorisation ne comprend pas Gemini. La page Configuration et la fenêtre Santé signalent ce cas.
3. Page Configuration : cochez **Activer la reformulation Gemini**. Réglages facultatifs :
   - **Modèles (ordre de repli)** : par défaut `gemini-3.1-flash-lite` puis `gemini-3.5-flash` ; le suivant est essayé si le précédent est saturé ou hors quota.
   - **Appareils Google Home** : noms de vos appareils et pièces, un par ligne, pour que Gemini n'en invente pas.
   - Bouton **Tester Gemini** pour valider la configuration.
4. Sur **chaque équipement** concerné, cochez **Reformuler via Gemini si non compris** (onglet Équipement). Les deux interrupteurs, plugin et équipement, doivent être activés.

**À savoir**

- Google dit rarement « je n'ai pas compris ». Pour les phrases qui reçoivent seulement une réponse audio, sans texte, une seconde case existe : **Aussi tenter Gemini si Google ne renvoie aucun texte**.
- **Ne cochez pas cette seconde case** sur un équipement qui pilote un portail, une serrure ou tout appareil à bascule : Google renvoie aussi une réponse audio seule pour une commande **déjà réussie**, et la commande serait alors rejouée une seconde fois (état inversé).
- Une reformulation ajoute environ 2 à 5 secondes.
- Sans facturation activée sur le projet, le quota gratuit de Gemini est limité (de l'ordre de 10 requêtes par minute) ; le modèle suivant prend le relais en cas de blocage.
- **Confidentialité** : sur l'offre gratuite de l'API Gemini, Google peut utiliser les échanges pour entraîner ses modèles. Si c'est un problème, laissez l'option désactivée ou activez la facturation sur le projet.
- La reformulation utilisée apparaît dans la commande **Dernier dialogue (tuile)**.

## Fonctions avancées (expérimentales)

La page Configuration contient aussi des sections réservées à un usage averti, indépendantes de la liaison OAuth et non nécessaires au fonctionnement normal :

- **Session Google (API privée Home Graph)** : capture de la session de votre navigateur sur home.google.com, via un proxy temporaire (arrêt automatique après 10 minutes), pour interroger l'API non officielle de Google Home. Des lanceurs Windows sont téléchargeables pour automatiser la capture. Boutons de test en lecture seule (**Tester Home Graph**, **Tester GetTraits**).
- **Login Google automatisé (gpsoauth)** : connexion par e-mail et **mot de passe d'application** Google (jamais votre mot de passe principal). Risque de verrouillage du compte plus élevé. Nécessite l'installation manuelle d'un module Python optionnel, indiquée sur la page.
- **Automatisations Google Home (script)** : génération et validation d'un script d'automatisation Google Home, en lecture seule (rien n'est enregistré ni exécuté).

Ces fonctions reposent sur des interfaces Google non documentées : elles peuvent cesser de fonctionner sans préavis.

# Portée et limites

- Pas de liste de commandes prédéfinie : le plugin relaie du texte. Ce qui fonctionne dépend du compte Google (appareils Google Home / Nest liés, questions générales, minuteurs, météo…).
- Un seul compte Google par Jeedom.
- Le texte de réponse des commandes domotiques est obtenu par contournement : il peut se dégrader si Google change ses pages.
- Limites de l'API Google Assistant elle-même (constatées aussi par l'intégration Home Assistant) : les routines Google ne se déclenchent pas ; la lecture de médias ne fonctionne presque pas ; les commandes qui exigent Voice Match échouent ; les annonces vers une pièce précise marchent mal hors anglais.
- Google a annoncé l'arrêt de l'application grand public Google Assistant, remplacée par Gemini. Rien n'indique officiellement si l'API utilisée par le plugin est concernée.

# FAQ / Dépannage

**« Tester la connexion » échoue avec des identifiants invalides ou révoqués.**
Relancez **Se connecter avec Google**. Si la liaison est à refaire chaque semaine, l'application Google est restée « En test » : publiez-la.

**Erreur Google `redirect_uri_mismatch`.**
L'URI déclarée chez Google diffère de celle du plugin. Comparez-la, caractère par caractère, avec le champ **URL générée** de la page Configuration. Voir le tableau de dépannage complet en fin de page [Créer son application Google](creation-app-google).

**La page « Lier ce compte » demande une session administrateur.**
Connectez-vous à Jeedom sur l'adresse de l'accès externe dans un autre onglet, puis recliquez sur **Lier le compte** dans les 5 minutes.

**La commande n'a aucun effet.**
Vérifiez dans [Mon activité Google](https://myactivity.google.com/myactivity) que la requête arrive. Simplifiez la phrase (« allume lumière salon »).

**La réponse n'est pas lue à voix haute.**
Vérifiez le champ **Appareil TTS Cast** de l'équipement et la case **Silencieux** du bloc de scénario.

**La reformulation Gemini ne se déclenche jamais.**
Vérifiez les deux interrupteurs (plugin et équipement) et la fenêtre **Santé** : si l'autorisation Gemini manque, reliez à nouveau le compte.

**Où voir ce qui se passe ?**
Analyse > Logs, journal `google_assistant`.
