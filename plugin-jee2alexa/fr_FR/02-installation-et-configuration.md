# Installation et configuration

## 1. Installer le plugin

1. Installez **jee2alexa** depuis son dépôt GitHub avec la fonction d'installation depuis une source externe de Jeedom (le plugin n'est pas publié sur le Market).
2. À l'installation, le plugin télécharge lui-même le **SDK AWS pour PHP** (Composer, dossier `vendor/`). Si cette étape échoue, un message Jeedom le signale et les fonctions AWS restent indisponibles ; le reste du plugin (proxy Smart Home, OAuth) fonctionne.
3. Activez le plugin. L'écran d'accueil propose six tuiles : **Configuration**, **Santé**, **Équipements SmartHome**, **Scènes SmartHome**, **Déployer Skill SmartHome**, **Documentation**.

![Écran d'accueil du plugin](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/01-accueil-plugin.jpg)

> Le plugin remplace l'ancien plugin `alexa_sh_skill` : à la mise à jour, ses réglages sont migrés (aucune ancienne valeur n'est supprimée).

## 2. Installer et authentifier `alexaapiv2`

`jee2alexa` ne gère pas lui-même l'authentification développeur Amazon : il réutilise celle d'`alexaapiv2` (cookie de session, identifiant vendeur). Installez `alexaapiv2` et connectez-le à **votre compte Amazon développeur** avant de créer le skill (voir [prérequis](#3-alexaapiv2-installé-et-authentifié)).

## 3. Renseigner l'adresse externe de Jeedom

**Réglages > Système > Configuration > Réseaux > Accès externe** : protocole `https://`, adresse (par exemple `jeedom.example.com`), port `443`. Sans adresse HTTPS valide, le plugin refuse de déployer.

## 4. Configurer le plugin

Ouvrez **Plugins > Gestion des plugins > Jee2Alexa**. Le bloc **Configuration** contient deux parties : *Compte AWS* et *Push proactif*.

![Page de configuration du plugin, compte AWS](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/08-configuration-aws.jpg)

### Compte AWS (déploiement de la Lambda)

| Champ | Rôle |
|---|---|
| **Mode de connexion** | `Se connecter à AWS (sans clé permanente)` (recommandé) ou `Clés IAM (Access Key / Secret)` (mode historique) |
| **Session AWS** | État de la connexion (mode connexion) : identité connectée, boutons *Se connecter à AWS* / *Se déconnecter* |
| **Région AWS** | Région de la Lambda : **celle du compte Alexa** (voir [prérequis](#5-région-aws-et-langue--à-choisir-ensemble)). Le bouton **Détecter la région du compte Alexa** la lit chez Alexa ; le déploiement l'aligne aussi automatiquement |
| **ARN du rôle IAM d'exécution** | **Laissez vide** : le plugin crée (ou réutilise) `premiumSmartHome-lambda-role` au déploiement. Renseignez-le seulement pour imposer un rôle existant. Le bouton *Suggérer* complète l'ID de compte via STS |
| **Tester la connexion AWS** | Vérifie que les identifiants fonctionnent (liste des fonctions Lambda, lecture seule) |

### Mode « Se connecter à AWS » (recommandé)

Équivalent de la commande `aws login --remote`, sans installer l'AWS CLI. Aucune clé permanente n'est stockée.

1. Choisissez le mode **Se connecter à AWS (sans clé permanente)**, la région, puis cliquez **Se connecter à AWS**.
2. Un **lien d'autorisation** s'ouvre dans un nouvel onglet. Il est aussi affiché dans un champ pour l'ouvrir sur un autre appareil (ordinateur, téléphone). **Il est valable 10 minutes.**
3. Sur la page AWS, connectez-vous à la console et autorisez la connexion. AWS affiche un **code de vérification**.
4. Collez ce code dans le champ **Code AWS**, puis **Valider**.
5. Le plugin échange le code contre des identifiants temporaires, puis **vérifie l'identité par un appel STS** (lecture seule). Un bandeau indique `Connecté : arn:aws:iam::…`.

Ce qu'il faut savoir :

- Les identifiants durent **15 minutes** et sont **renouvelés automatiquement** ; la session complète dure **12 heures au maximum** (limite AWS). Passé ce délai, reconnectez-vous : c'est nécessaire **uniquement pour (re)déployer** la Lambda. Le contrôle vocal ne dépend pas de cette session.
- La session est stockée **chiffrée** dans la configuration Jeedom.
- **Évitez le compte root** : le plugin affiche un avertissement orange (« compte root : déconseillé »). Préférez un utilisateur IAM avec la politique gérée **`SignInLocalDevelopmentAccess`** plus des droits IAM et Lambda limités (voir [Configuration AWS](configuration-aws.html) et [sécurité](#sécurité)).
- Si le lien expire ou qu'un code est refusé (*Aucune connexion en cours*, *state différent*), recliquez **Se connecter à AWS** pour générer un nouveau lien.

### Mode « Clés IAM » (historique)

Un utilisateur IAM dédié avec une clé d'accès permanente, limité à la fonction `premiumSmartHome`. Procédure complète, politique JSON et rôle d'exécution : [Configuration AWS](configuration-aws.html).

### Push proactif (optionnel)

Voir [Push proactif](#push-proactif-états-à-jour-sonnette) : à configurer **après** le déploiement du skill.

![Configuration : push proactif et fonctionnalités](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/09-configuration-push.jpg)

## 5. Logs et surveillance

Dans le bloc **Logs et surveillance** de la même page :

- **Niveau log** : laissez *Défaut* en usage normal ; passez en **Debug** pour diagnostiquer (le journal du plugin s'appelle `Jee2alexa`, fichier `log/jee2alexa`).
- Le plugin planifie une tâche **horaire** (`cronHourly`) qui resynchronise les écouteurs du push proactif, met à jour l'équipement **Alexa SmartHome (AWS)** et contrôle la consommation AWS (voir [Coûts AWS](#4-un-compte-aws-avec-lambda-activé)).

## Étape suivante

[03 — Déploiement pas à pas](#déploiement-pas-à-pas)
