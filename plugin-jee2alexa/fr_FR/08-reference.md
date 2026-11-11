# Référence technique

## Valeurs fixes

| Élément | Valeur |
|---|---|
| Fonction Lambda | `premiumSmartHome` |
| Exécution | Python 3.12, gestionnaire `lambda_function.lambda_handler`, 128 Mo, délai 8 s, aucune dépendance |
| Rôle d'exécution créé | `premiumSmartHome-lambda-role` (confiance `lambda.amazonaws.com`, politique `AWSLambdaBasicExecutionRole`) |
| Principal autorisé à invoquer | `alexa-connectedhome.amazon.com` |
| Autorisation « ouverte » (étape 1) | `StatementId` = `AlexaSmartHomeInvoke` |
| Autorisation limitée au skill (étape 2) | `StatementId` = `AlexaSmartHomeInvokeSkill`, condition `lambda:EventSourceToken` = identifiant du skill |
| Nom du skill | `jee2alexa` (ancien nom reconnu : `AlexaPremium SmartHome`) |
| API Amazon des skills | `https://api.amazonalexa.com/v1/skills` (SMAPI) |
| Protocole Alexa | Smart Home, `payloadVersion` 3 |
| Permission du skill | `alexa::async_event:write` (événements proactifs) |
| Équipement d'état | Un seul eqLogic `jee2alexa`, logicalId `aws_status` ; budget AWS `jee2alexa-monthly` ; journaux de la Lambda gardés 30 jours |
| Langues déclarées | `fr-FR`, `en-US` |
| Régions Alexa reconnues | `us-east-1` (Amérique du Nord), `eu-west-1` (Europe, Inde), `us-west-2` (Extrême-Orient, Australie) ; passerelle d'événements : `api.amazonalexa.com`, `api.eu.amazonalexa.com`, `api.fe.amazonalexa.com` |
| Connexion AWS | Client `arn:aws:signin:::devtools/cross-device`, PKCE S256, jeton DPoP ES256 ; lien valable 10 min, identifiants 15 min (renouvelés dès qu'il reste moins de 5 min), session 12 h maximum |

## Clés de configuration

Enregistrées dans la configuration du plugin `jee2alexa`. Les clés marquées *chiffré* sont chiffrées dans la base Jeedom.

| Clé | Contenu |
|---|---|
| `aws_auth_mode` | `keys` (défaut) ou `login` |
| `aws_region` | Région de la Lambda (défaut `eu-west-1`) ; détermine aussi la passerelle du push proactif |
| `aws_access_key`, `aws_secret_key` *(chiffré)* | Clés IAM (mode clés) |
| `aws_login_session` *(chiffré)* | Session « Se connecter à AWS » (identifiants temporaires, jeton de renouvellement, clé DPoP) |
| `aws_iam_role_arn` | ARN du rôle d'exécution ; vide = créé au déploiement |
| `lambda_function_arn`, `lambda_last_deploy` | ARN de la Lambda, date du dernier déploiement |
| `smarthome_skill_id` | Identifiant du skill |
| `smarthome_skill_devices` | Sélection d'équipements : par identifiant d'équipement : `enabled`, `friendlyName` (nom affiché), `displayCategory`, `endpointId` (alias numérique, avancé) |
| `smarthome_skill_scenes` | Scènes (nom, type scénario/actions, commandes) |
| `smarthome_oauth_client_id`, `smarthome_oauth_client_secret` *(chiffré)* | Client OAuth du plugin pour l'Account Linking (générés aléatoirement au premier usage ; modifiables ou régénérables dans la configuration, avec validation et confirmation) |
| `smarthome_event_client_id`, `smarthome_event_client_secret` *(chiffré)* | Identifiants « Send Alexa Events » (push proactif) |
| `smarthome_event_refresh_token` *(chiffré)* | Jeton de renouvellement de l'Event Gateway (reçu à la liaison du compte) |
| `cost_alerts_disabled` | `1` = pas de contrôle horaire de consommation ni de message (défaut : actif) |
| `cost_autosuspend` | `1` = suspendre la Lambda quand la limite gratuite du mois est dépassée (défaut : non) |
| `cost_alert_last` | Dernier seuil annoncé, `AAAA-MM:niveau` (un message par seuil et par mois) |
| `cost_budget_amount`, `cost_budget_email` | Montant (USD) et e-mail du budget AWS `jee2alexa-monthly` |
| `status_eq_removed` | `1` = équipement d'état supprimé à la main : pas recréé avant le prochain déploiement |

Lues dans la configuration d'**`alexaapiv2`** : `vendorId`, `askClientId`, `askClientSecret`, et l'authentification développeur (cookie de session).

Entrées de cache temporaires : `jee2alexa::aws_login_pending` (connexion en cours, 10 min), `jee2alexa::event_gateway_token` (jeton d'accès de l'Event Gateway).

## Directives Alexa prises en charge par le proxy

| Espace de noms | Usage |
|---|---|
| `Alexa.Discovery` | Annonce des équipements et scènes cochés |
| `Alexa` (`ReportState`) | Lecture de l'état |
| `Alexa.PowerController` | Marche / arrêt |
| `Alexa.BrightnessController` | Luminosité |
| `Alexa.ColorController` | Couleur |
| `Alexa.ColorTemperatureController` | Température de couleur |
| `Alexa.ThermostatController` | Consigne et mode |
| `Alexa.LockController` | Verrouiller / déverrouiller |
| `Alexa.RangeController` | Volets (`Blind.Lift`), vitesse de ventilateur, consigne d'humidité |
| `Alexa.ModeController` | Porte de garage, fil pilote, mode de ventilation de la clim |
| `Alexa.ToggleController` | Oscillation d'un ventilateur |
| `Alexa.ActionController` | Aspirateur (`Robot.Clean`) |
| `Alexa.PlaybackController` | Lecture, pause, arrêt, suivant, précédent |
| `Alexa.ChannelController` | Changement de chaîne |
| `Alexa.Speaker` | Volume, sourdine |
| `Alexa.CameraStreamController` | Flux caméra (RTSP/RTSPS public) |
| `Alexa.SecurityPanelController` | Armer (`ARMED_AWAY`) / désarmer |
| `Alexa.SceneController` | Activer / désactiver une scène |
| `Alexa.Authorization` (`AcceptGrant`) | Réception du code de liaison (push proactif) |

Interfaces de lecture annoncées : `ContactSensor`, `MotionSensor`, `TemperatureSensor`, `HumiditySensor`, `PlaybackStateReporter`, `EndpointHealth` (sur tous les équipements), `DoorbellEventSource` (sonnette).

## Actions AJAX (`core/ajax/jee2alexa.ajax.php`)

Toutes réservées à un administrateur Jeedom.

| Action | Rôle |
|---|---|
| `ping` | Test de disponibilité |
| `getSmarthomeDevices`, `saveSmarthomeDevices` | Lire / enregistrer la sélection d'équipements |
| `detectSplitCandidates`, `createSplitVirtuals` | Détecter et créer des équipements virtuels (sorties multiples) |
| `getSmarthomeScenes`, `saveSmarthomeScenes` | Lire / enregistrer les scènes |
| `triggerSmartHomeDiscovery` | Lancer la découverte côté Amazon |
| `awsLoginStart`, `awsLoginComplete`, `awsLoginStatus`, `awsLogout` | Connexion AWS interactive |
| `testAwsCredentials`, `fetchAwsAccountId` | Tester la connexion, suggérer l'ARN du rôle |
| `detectAlexaRegion` | Région du compte Alexa (lue via `alexaapiv2`) : `region`, `website`, `error` |
| `deployLambda` | Étape 1 : alignement de la région, rôle, Lambda, permission Alexa |
| `deploySkill` | Étape 2 : skill, fiche, Account Linking à la création, permission limitée |
| `republishAccountLinking` | Republier l'Account Linking (sensible) |
| `fetchEventGatewayCredentials`, `testEventGateway` | Push proactif : récupérer les identifiants, tester le jeton |
| `healthCheck` | Diagnostic de la fenêtre Santé |
| `lambdaStatus`, `setLambdaSuspended` | État de la Lambda côté AWS ; suspendre (concurrence réservée à 0) ou réactiver |
| `skillMetrics` | Mesures Amazon de la fenêtre Santé (console développeur via `alexaapiv2`) |
| `awsUsage` | Consommation AWS du mois (CloudWatch, journaux, budget) |
| `saveAwsBudget` | Créer / recréer le budget AWS `jee2alexa-monthly` |
| `applyLogRetention` | Limiter à 30 jours la conservation des journaux de la Lambda |

## Organisation des fichiers

| Chemin | Rôle |
|---|---|
| `core/class/jee2alexa_awslogin.class.php` | Connexion AWS (PKCE, DPoP, jetons temporaires) |
| `core/class/jee2alexa_aws.class.php` | Rôle IAM, Lambda, permission Alexa, package de déploiement |
| `core/class/jee2alexa_skill.class.php` | Skill (SMAPI), fiche, Account Linking, permission limitée au skill |
| `core/class/jee2alexa_metrics.class.php` | Mesures du skill lues dans la console développeur (réussite, latence, alertes) |
| `core/class/jee2alexa_events.class.php` | Push proactif (Event Gateway, `ChangeReport`) |
| `core/class/jee2alexa_virtualize.class.php` | Équipements virtuels pour sorties multiples |
| `core/class/jee2alexa.class.php` | Classe du plugin, clés chiffrées, plugins exclus |
| `core/php/smartHomeProxy.php`, `smartHomeProxy_lib.php` | Proxy Smart Home : découverte, contrôle, lecture d'état |
| `core/php/oauth/authorize.php`, `token.php`, `core/php/_oauth_helper.php` | OAuth de l'Account Linking |
| `core/php/_apikey_helper.php` | Gestion de la clé API embarquée dans les jetons |
| `data/skill_smarthome/skill.json` | Modèle de la fiche du skill (`{% raw %}{{LAMBDA_ARN}}{% endraw %}`, `{% raw %}{{PRIVACY_URL}}{% endraw %}`, `{% raw %}{{TOS_URL}}{% endraw %}`) |
| `data/skill_smarthome/lambda/lambda_function.py` | Code de la Lambda (`{% raw %}{{JEEDOM_URL}}{% endraw %}` injecté au déploiement) |
| `desktop/modal/*.php`, `desktop/js/jee2alexa.js` | Fenêtres : déploiement, équipements, scènes, santé |
| `plugin_info/configuration.php`, `install.php`, `info.json` | Configuration, installation (Composer), métadonnées |
| `tests/php/` | Suites de tests unitaires |

Langues de l'interface : `fr_FR`, `en_US`, `de_DE`, `es_ES`, `it_IT`, `pt_PT`.
