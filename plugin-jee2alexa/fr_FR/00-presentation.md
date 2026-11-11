# jee2alexa — Skill Alexa Smart Home pour Jeedom

`jee2alexa` expose vos équipements Jeedom à Alexa **comme n'importe quel appareil connecté** : « Alexa, allume la lumière du salon », « Alexa, mets le chauffage à 20 degrés », « Alexa, ferme la porte du garage ». Pas de nom de skill à prononcer, pas d'invocation : les appareils apparaissent dans l'app Alexa, peuvent être placés dans des groupes et des routines, et répondent à la voix sur tous vos Echo.

![Écran d'accueil du plugin](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/01-accueil-plugin.jpg)

## Ce que le plugin fait pour vous

Mettre en place un skill Smart Home « à la main » demande une dizaine d'opérations dans trois consoles différentes (développeur Alexa, AWS IAM, AWS Lambda). Le plugin les automatise :

| Étape | Manuelle (sans plugin) | Avec jee2alexa |
|---|---|---|
| Se connecter à AWS | Créer un utilisateur IAM, générer des clés d'accès permanentes | « Se connecter à AWS » : lien + code, identifiants temporaires (voir [installation](#installation-et-configuration)) |
| Rôle d'exécution de la Lambda | Console IAM | Créé automatiquement (`premiumSmartHome-lambda-role`) |
| Fonction Lambda | Console Lambda, copier-coller du code | Créée et mise à jour par un bouton |
| Permission d'invocation Alexa | Console Lambda, ajout d'un déclencheur avec l'ID du skill | Ajoutée, puis limitée à **votre** skill |
| Skill Alexa | Console développeur Alexa | Créé par l'API Amazon (SMAPI) |
| Endpoint du skill | Coller l'ARN de la Lambda | Renseigné automatiquement |
| Account Linking (OAuth) | Saisir URLs, client ID/secret, portée | Publié et vérifié automatiquement |
| Choix des équipements | Filtres en YAML (autres systèmes) | Cases à cocher par pièce, dans Jeedom |

**Reste manuel** (inévitable) : se connecter à votre compte AWS, activer le skill dans l'app Alexa et relier votre compte Jeedom, puis lancer la découverte des appareils.

## Ce que le plugin n'est pas

- **Pas un skill public** : le skill est en phase *développement*, il n'est visible que sur le compte Amazon développeur qui l'a créé. Il n'y a pas de fiche sur le store Alexa.
- **Pas un skill « Alexa-hosted » ni un service cloud tiers** : la Lambda et le skill vous appartiennent, dans votre compte AWS et votre compte développeur Amazon. Aucune donnée ne passe par un serveur du plugin.
- **Pas un remplacement d'`alexaapiv2`** : `alexaapiv2` pilote vos Echo (annonces, musique, routines) ; `jee2alexa` fait l'inverse, Alexa pilote Jeedom. Les deux sont complémentaires, et `jee2alexa` **dépend** d'`alexaapiv2` pour l'authentification développeur Amazon.

## Comment ça marche

```
 Vous ──voix──▶ Echo ──▶ Service Alexa Smart Home
                              │  (directive JSON, jeton de votre compte)
                              ▼
                  AWS Lambda « premiumSmartHome »        ← région eu-west-1 pour fr-FR
                  (Python, sans dépendance, ~3 Ko)
                              │  HTTPS POST + « Authorization: Bearer <jeton> »
                              ▼
        https://votre-jeedom/plugins/jee2alexa/core/php/smartHomeProxy.php
                              │  exécute les commandes Jeedom, lit les états
                              ▼
                       Vos équipements Jeedom
```

Les briques :

| Brique | Rôle | Où elle vit |
|---|---|---|
| **Skill Alexa** | Déclare à Alexa « je gère des appareils » et pointe sur la Lambda | Compte développeur Amazon |
| **Lambda `premiumSmartHome`** | Relais minimal : reçoit la directive d'Alexa, la transmet à Jeedom, renvoie la réponse | Votre compte AWS |
| **Proxy Jeedom** (`smartHomeProxy.php`) | Traduit les directives Alexa en commandes Jeedom (découverte, allumer, régler, lire un état…) | Votre Jeedom |
| **OAuth Jeedom** (`oauth/authorize.php`, `oauth/token.php`) | Lie votre compte Alexa à votre Jeedom (page de connexion + consentement) | Votre Jeedom |
| **Push proactif** (optionnel) | Prévient Alexa quand un état change dans Jeedom | Votre Jeedom → serveurs Alexa |

Tout le trafic entrant vers Jeedom passe par **une seule URL HTTPS publique** : c'est donc le point à soigner (certificat reconnu, voir [prérequis](#prérequis)) et à protéger (voir [sécurité](#sécurité)).

## Plan de la documentation

| Document | Contenu |
|---|---|
| [01 — Prérequis](#prérequis) | Comptes, URL publique, régions et langues, dépendances |
| [02 — Installation et configuration](#installation-et-configuration) | Installer le plugin, se connecter à AWS, réglages |
| [Configuration AWS (IAM)](configuration-aws.html) | Utilisateur IAM restreint, politique JSON, mode clés d'accès |
| [03 — Déploiement pas à pas](#déploiement-pas-à-pas) | Lambda, skill, activation dans l'app Alexa, découverte |
| [04 — Équipements et scènes](#équipements-et-scènes) | Exposer des équipements, types génériques, catégories, scènes |
| [05 — Push proactif](#push-proactif-états-à-jour-sonnette) | États à jour sans interroger, sonnette |
| [06 — Sécurité](#sécurité) | Modèle de menace, droits minimaux, secrets |
| [07 — Dépannage](#dépannage) | Symptômes, causes, vérifications |
| [10 — Repli : déploiement manuel](#repli--déployer-la-lambda-à-la-main) | Déployer la Lambda à la main si l'automatisation échoue |
| [08 — Référence](#référence-technique) | Clés de configuration, fichiers, directives Alexa prises en charge |
| [09 — Comparaison avec Home Assistant](#comparaison-avec-home-assistant) | Ce qui est repris, ce qui diffère |
| [11 — jee2alexa ou Matter ?](#jee2alexa-ou-matter-jee2matter-) | Skill AWS ou pont Matter local (jee2matter), contraintes réseau de Matter |
| [Camera RTSP](camera-rtsp-relay.html) | Limite des caméras Alexa et contournement |
| [Services AWS](aws-services-pertinence.html) | Note sur les autres services AWS (non requis) |
