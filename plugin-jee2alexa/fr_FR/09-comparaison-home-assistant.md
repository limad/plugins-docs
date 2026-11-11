# Comparaison avec Home Assistant

La documentation de Home Assistant sur Alexa fait référence. Ce document compare, point par point, la méthode « manuelle » qu'elle décrit avec le fonctionnement de `jee2alexa`, pour comprendre ce que le plugin automatise, ce qu'il reprend à l'identique, et ses limites.

Sources consultées :

- [Alexa Smart Home — Home Assistant](https://www.home-assistant.io/integrations/alexa.smart_home/)
- [Amazon Web Services — Home Assistant](https://www.home-assistant.io/integrations/aws/)

## 1. Deux intégrations, deux objets différents

| Page Home Assistant | Objet | Équivalent dans `jee2alexa` |
|---|---|---|
| **Alexa Smart Home** | Exposer des entités à Alexa par un skill Smart Home (soit via le cloud payant de HA, soit par une Lambda « manuelle ») | **Tout le plugin** : le skill, la Lambda, le proxy |
| **Amazon Web Services** | Intégration *historique* : envoyer des notifications depuis HA vers Lambda, SNS, SQS ou EventBridge | **Aucun** : sans rapport avec le contrôle vocal. `jee2alexa` n'utilise que **Lambda et IAM** (voir [services AWS](aws-services-pertinence.html)) |

Home Assistant propose aussi un service cloud payant qui évite toute cette installation. `jee2alexa` n'en utilise aucun : tout tourne chez vous (Jeedom) et dans **votre** compte AWS. C'est le modèle « manuel » de Home Assistant, automatisé.

## 2. La méthode manuelle de Home Assistant, étape par étape

| Étape Home Assistant | Avec `jee2alexa` |
|---|---|
| HTTPS public sur le port 443, certificat reconnu (DNS dynamique conseillé) | Même exigence, **vérifiée** par le plugin avant tout déploiement ([prérequis](#prérequis)) |
| Compte développeur Amazon, **même compte** que les Echo | Même exigence ; authentification portée par `alexaapiv2` |
| Console développeur : *Create Skill* → **Smart Home** → *Provision your own*, noter le **Skill ID** | Fait par le plugin via l'API SMAPI ; l'identifiant est mémorisé |
| Charge utile **v3** | Identique (`payloadVersion` 3) |
| IAM : créer un rôle Lambda avec `AWSLambdaBasicExecutionRole` | Créé automatiquement (`premiumSmartHome-lambda-role`) |
| Choisir la **région** selon la langue du compte Amazon, pas selon sa position (un décalage échoue silencieusement) | Même règle, rappelée dans les [prérequis](#5-région-aws-et-langue--à-choisir-ensemble) |
| Créer la fonction Lambda (Python, rôle existant) | Créée par le plugin (Python 3.12, 128 Mo, 8 s) |
| Ajouter un **déclencheur « Alexa Smart Home »** avec le Skill ID | `AddPermission` du principal Alexa, **limité à l'identifiant du skill** à l'étape 2 |
| Coller le code de la Lambda, puis définir des **variables d'environnement** (`BASE_URL`, `NOT_VERIFY_SSL`, `DEBUG`, jeton longue durée en secours) | Code injecté avec l'**URL externe** écrite dans le fichier ; **pas de variable d'environnement**, **pas de jeton longue durée de secours** |
| **Tester** la Lambda avec un événement `Discovery` | Fenêtre **Santé** (diagnostic réel) et journaux CloudWatch |
| Renseigner l'**ARN** comme *Default endpoint* du skill | Renseigné automatiquement dans la fiche |
| **Account Linking** : URIs d'autorisation et de jeton, *Client ID* d'Amazon (`pitangui` / `layla` / `alexa.amazon.co.jp`), secret quelconque, schéma *credentials in request body*, portée `smart_home` | Publié et relu automatiquement. Le plugin utilise **son propre client OAuth** (identifiant et secret aléatoires) et n'accepte les redirections que vers ces trois mêmes hôtes Amazon ; portée `smarthome` |
| Activer le skill dans l'app Alexa (Plus → Skills et jeux → Vos skills → Développement) | Identique, manuel |
| Découverte automatique, ou « Alexa, découvre les appareils » | Identique, plus un bouton **Découvrir via Alexa** |

La grande différence : **les opérations des consoles Amazon et AWS disparaissent**, y compris la configuration de l'Account Linking et la permission d'invocation, que Home Assistant laisse à la main.

## 3. Configuration côté Home Assistant et équivalents

| Option Home Assistant | Équivalent `jee2alexa` |
|---|---|
| `locale` (liste de seize locales) | Langues du skill fixées à `fr-FR` et `en-US` dans `skill.json` ; la région Lambda se règle dans la configuration du plugin |
| `endpoint` (passerelle des événements proactifs : Amérique du Nord, Europe, Extrême-Orient) | Déduit de la **région AWS** ; Europe et Amérique du Nord seulement ([push proactif](#passerelle-utilisée)) |
| `client_id` / `client_secret` (« Send Alexa Events », distincts de l'Account Linking) | Mêmes identifiants : bloc **Push proactif**, avec un bouton *Récupérer depuis la console* |
| `filter` (domaines, entités, motifs `*`/`?`, règles d'inclusion/exclusion) | **Cases à cocher par pièce** dans *Équipements SmartHome* : on n'expose que ce qui est coché ; pas de motifs |
| `entity_config` : `name`, `description`, `display_categories` | Colonne **Nom affiché dans Alexa** et liste **Catégorie** (pas de description personnalisée) |
| Catégories d'affichage (LIGHT, SWITCH, CAMERA, DOORBELL…) | Même notion : [liste des catégories](#catégorie-icône-dans-lapp-alexa), choix **Auto** par défaut |
| Journal `homeassistant.components.alexa` en debug | Niveau **Debug** du journal `Jee2alexa` ; journaux CloudWatch de la Lambda |

## 4. Entités Home Assistant et types génériques Jeedom

Home Assistant détecte les capacités par **domaine** d'entité ; Jeedom les détecte par **type générique des commandes** (voir [Équipements et scènes](#types-génériques-pris-en-charge)).

| Domaine Home Assistant | Pris en charge par `jee2alexa` ? | Types génériques Jeedom |
|---|---|---|
| Light | Oui (marche/arrêt, luminosité, couleur, température de couleur) | `LIGHT_*`, `ENERGY_*` |
| Switch, Input boolean, Group, Automation | Marche/arrêt | `ENERGY_*`, `LIGHT_ON/OFF` |
| Climate | Oui (thermostat, clim, fil pilote) | `THERMOSTAT_*`, `AC_*`, `HEATING_*` |
| Cover | Volets (position), porte de garage | `FLAP_*`, `GB_*`, `GARAGE_STATE` |
| Lock | Verrouiller / déverrouiller, **sans code PIN** | `LOCK_*` |
| Fan | Vitesse, oscillation (pas de sens ni de préréglage) | `FAN_SPEED*`, `ROTATION*` |
| Humidifier | Consigne d'humidité (pas de mode) | `HUMIDITY_SET_SETPOINT`, `HUMIDITY_SETPOINT` |
| Binary sensor | Contact, mouvement | `OPENING*`, `DOOR`, `PRESENCE`, `MOTION` |
| Sensor | Température, humidité | `TEMPERATURE`, `HUMIDITY` |
| Media player | Lecture, chaîne, volume, sourdine (pas de changement de source ni de mode sonore) | `MEDIA_*`, `SET_CHANNEL`, `SET_VOLUME` |
| Camera | Flux RTSP/RTSPS **public** uniquement | `CAMERA_URL` ([caméras](camera-rtsp-relay.html)) |
| Alarm control panel | Armer en « absent » et désarmer, **sans PIN** ni modes maison/nuit | `ALARM_*` |
| Scene, Script | Scènes : scénario Jeedom ou liste d'actions, **désactivation possible** | Tuile *Scènes SmartHome* |
| Vacuum | Marche/arrêt, nettoyage, retour base | `DOCK` + `ENERGY_ON/OFF` |
| Button, Event (sonnette) | Sonnette via catégorie **DOORBELL** + `BUTTON` (push proactif requis) | `BUTTON` |
| Number / Input number, Timer, Valve, Water heater, Remote, Image processing | **Non pris en charge** | — |

## 5. Ce que `jee2alexa` ne fait pas (par rapport à Home Assistant)

- **Pas de code PIN vocal** pour la serrure ni l'alarme.
- **Une seule Lambda** : donc une seule région, donc une famille de langues par installation.
- **Pas de filtres par motifs** ni de configuration en YAML : tout passe par l'interface.
- **Passerelle Extrême-Orient** du push proactif non gérée.
- Peu d'entités « utilitaires » (compteurs, minuteries, vannes…).

## 6. Ce qui va plus loin

- **Création et mise à jour automatiques** de la Lambda, du rôle IAM, du skill et de l'Account Linking.
- **Connexion AWS sans clé permanente** (identifiants temporaires), et permission d'invocation **liée à votre skill**.
- Masquage des **jetons et codes d'autorisation** dans les journaux de la Lambda.
- Diagnostic intégré (fenêtre Santé) et messages d'erreur AWS lisibles, avec les étapes déjà réussies.
- Scènes qui lancent un **scénario Jeedom**.

## 7. L'intégration AWS de Home Assistant, pour mémoire

Cette intégration (identifiée comme historique et maintenue par la communauté) envoie des notifications vers Lambda, SNS, SQS ou EventBridge. Quelques idées utiles, adaptées ici :

| Idée Home Assistant | Dans `jee2alexa` |
|---|---|
| Identifiants nommés, ou profil partagé avec l'AWS CLI (`~/.aws`) | Deux modes : connexion interactive ou clés IAM. Aucune dépendance à l'AWS CLI |
| Validation des identifiants (appel `IAM.GetUser`) | Contrôle par `STS GetCallerIdentity`, qui **ne demande aucun droit particulier** |
| Avertissement sur les coûts hors niveau gratuit | Rappelé dans les [prérequis](#4-un-compte-aws-avec-lambda-activé) |
| Droits IAM peu détaillés (SNS en accès complet) | Politique IAM **restreinte** à la fonction et au rôle : [Configuration AWS](configuration-aws.html) |
