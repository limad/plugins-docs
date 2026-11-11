# Dépannage

## Où regarder

| Source | Contenu |
|---|---|
| Fenêtre **Santé** → *Lancer un diagnostic* | Teste réellement : connexion AWS, Lambda active, Event Gateway, découverte |
| Fenêtre **Santé** → *Charger les mesures des 7 derniers jours* | Ce qu'**Amazon** a constaté (page *Analytics* de la console développeur) : nombre d'appels, taux de réussite et latence de la **découverte** et du **contrôle**, alertes graves. Une découverte sous 90 % de réussite explique des équipements absents de l'app Alexa. Nécessite `alexaapiv2` ; données publiées avec quelques heures de retard |
| Fenêtre **Santé** → *Lire la consommation* | Appels et durée de la Lambda du mois (CloudWatch), part de la limite gratuite, projection, coût estimé, erreurs / appels refusés, journaux, budget AWS. « Non lu » + `AccessDenied` : droits `AllowCostMonitoring` / `AllowCostBudget` manquants (voir [Configuration AWS](configuration-aws.html)) |
| `log/jee2alexa` (Analyse > Logs > `Jee2alexa`) | Journal du plugin ; passez en niveau **Debug** pour le détail |
| `log/http.error` | Erreurs PHP du serveur web |
| CloudWatch → groupe `/aws/lambda/premiumSmartHome` | Journal de la Lambda, gardé 30 jours. Le groupe est créé au déploiement (pour fixer cette durée) ou, à défaut, au premier appel de la Lambda |
| Console AWS → Lambda → *Surveillance* | Nombre d'invocations (métrique `Invocations`) |

> **Tester depuis un terminal** : exécutez vos scripts PHP **en tant que `www-data`** (par exemple `sudo -u www-data php …`). Sous un autre utilisateur, des fichiers comme ceux d'`alexaapiv2` (`resources/data/`) sont illisibles et donnent de **faux** « authentification vide ».

## Déploiement de la Lambda

| Message / symptôme | Cause probable | Que faire |
|---|---|---|
| Bouton **1 grisé** | Connexion AWS absente, ou URL externe invalide | Lire le bloc *Prérequis* de la fenêtre |
| `URL externe Jeedom absente ou non HTTPS (valeur actuelle : « http: »)` | Accès externe non renseigné | Réglages > Système > Configuration > Réseaux : `https://`, adresse, port `443` |
| `Non connecté à AWS — cliquez sur « Se connecter à AWS »` | Session absente ou expirée (12 h) | Se reconnecter dans la configuration |
| `Identifiants AWS expirés ou invalides` (`ExpiredToken`, `InvalidClientTokenId`) | Session expirée, clés révoquées | Reconnexion, ou vérifier les clés |
| `Permissions AWS insuffisantes` (`AccessDenied`) | L'identité n'a pas les droits IAM/Lambda requis | Ajouter l'action indiquée dans la politique ([Configuration AWS](configuration-aws.html)) |
| `SubscriptionRequiredException` | Compte AWS pas encore activé pour Lambda | Terminer l'inscription (paiement, vérification), attendre jusqu'à 24 h, ouvrir la console Lambda pour contrôle |
| `Région AWS invalide :` | Région vide au lancement de la connexion | Choisir une région dans la liste, puis reconnecter |
| `Rôle … cannot be assumed` | Rôle IAM tout juste créé, pas encore propagé | Le plugin réessaie ; relancer au besoin après une minute |
| `Lambda non opérationnel` (`State`/`LastUpdateStatus`) | Code ou rôle refusé par Lambda | Lire le détail affiché (`StateReason`) |
| `Lambda suspendue` (avertissement) ou Alexa : « l'appareil ne répond pas » pour tout | Lambda suspendue (concurrence réservée à 0) | Fenêtre de déploiement → **Réactiver la Lambda** |
| *Suspendre/Réactiver la Lambda* : `Permissions AWS insuffisantes` | Droits `PutFunctionConcurrency`/`DeleteFunctionConcurrency` absents (mode clés IAM) | Les ajouter à la politique ([Configuration AWS](configuration-aws.html)) |
| `Aucune connexion en cours (ou lien expiré après 10 min)` | Le lien de connexion AWS a expiré | Recliquer *Se connecter à AWS* |
| `Le code ne correspond pas à cette demande de connexion` | Code d'une ancienne tentative | Relancer la connexion et utiliser le nouveau code |

> **Toujours bloqué ?** [Déployer la Lambda à la main](#repli--déployer-la-lambda-à-la-main) donne le même résultat sans l'automatisation, puis le plugin reprend la fonction existante.

## Création du skill

| Message / symptôme | Cause probable | Que faire |
|---|---|---|
| Bouton **2 grisé** | Lambda jamais déployée | Faire l'étape 1 |
| `Plugin alexaapiv2 introuvable` | Dépendance absente | Installer `alexaapiv2` |
| `Authentification développeur Amazon absente` | `alexaapiv2` non connecté à Amazon | Refaire l'authentification dans `alexaapiv2` |
| `cookieData vide dans les fichiers auth` | Même cause, **ou** test lancé sous le mauvais utilisateur | Vérifier sous `www-data` ; sinon réauthentifier `alexaapiv2` |
| `Identifiant vendeur Amazon introuvable` | Compte développeur incomplet | Compléter le profil développeur Amazon |
| `Création du skill refusée par Amazon` | Violation de la fiche (HTTP 4xx + message) | Lire le message affiché sous l'étape |
| `Fiche du skill non validée par Amazon` | Traitement asynchrone en échec ou dépassé | Relancer l'étape 2, lire les erreurs affichées |
| `Account Linking … relecture non conforme` | Amazon n'a pas enregistré les URLs attendues | Vérifier dans la console développeur, puis *Republier l'Account Linking* |
| `Permission Lambda limitée au skill` en rouge | Droits `GetPolicy`/`AddPermission`/`RemovePermission` manquants | Compléter la politique IAM ; relancer l'étape 2 |

## Activation et découverte

| Symptôme | Cause probable | Que faire |
|---|---|---|
| Skill **introuvable** dans l'app Alexa (onglet *Développement*) | Compte Amazon de l'app ≠ compte développeur | Se connecter à l'app avec le compte développeur |
| « Découvrir via Alexa » **ne fait rien** de visible | Normal : pas de retour dans l'interface | Contrôler dans l'app Alexa → *Appareils*, ou lancer le diagnostic |
| Aucun appareil trouvé | 0 équipement coché, ou tous **« Non pris en charge »** | [Types génériques](#types-génériques-pris-en-charge) ; cocher et **Sauvegarder** |
| Aucun appareil, et la Lambda n'a **jamais été invoquée** (pas de groupe CloudWatch, 0 invocation) | Skill non activé/relié, mauvais compte, ou **région différente** (voir [ci-dessous](#découverte-qui-échoue-sans-appeler-la-lambda)) | Activer et relier dans l'app ; Amazon répond *Enablement not found* tant que le skill n'est pas activé |
| Appareils trouvés mais **« ne répond pas »** | Région Lambda ≠ région du compte Alexa ; URL externe obsolète dans la Lambda ; permission d'invocation absente | [Région et langue](#5-région-aws-et-langue--à-choisir-ensemble) ; redéployer la Lambda |
| Les appareils **disparaissent** ou doublonnent | Noms en double, équipement désactivé dans Jeedom | Noms distincts ; réactiver l'équipement |
| Étape 2 : `Account Linking (relu chez Amazon)` en avertissement, « différent de cette box » | Jeedom réinstallée, adresse externe ou client OAuth changés : Amazon garde l'ancien | Accepter la proposition de republier (ou bouton *Republier l'Account Linking*), puis désactiver/réactiver le skill dans l'app Alexa |
| `Token invalid and no refresh token available` / liaison perdue | Jetons invalidés (secret changé, Account Linking republié) | Désactiver puis réactiver le skill dans l'app Alexa |
| Page de connexion Jeedom **en erreur** pendant la liaison | URL d'autorisation ou certificat HTTPS invalide | Vérifier l'accès `https://…/plugins/jee2alexa/core/php/oauth/authorize.php` depuis un navigateur externe ; republier l'Account Linking si l'adresse a changé |

### Découverte qui échoue sans appeler la Lambda

Symptômes : le skill est actif et lié, l'app répond « Aucun appareil trouvé » (`ZERO_ENDPOINTS_REPORTED`), et la Lambda ne reçoit **aucun `Discover`** (seulement des `AcceptGrant`). C'est le signe d'un **décalage de région** : Alexa tente d'appeler le skill dans sa région et échoue avant d'atteindre la Lambda.

Pour le confirmer, ouvrez la console développeur Alexa → votre skill → **Analytics → Smart Home → Discovery**, choisissez l'étape *Development* et la région (**NA**, **EU**, **FE**) :

| Ce que vous voyez | Interprétation |
|---|---|
| Des requêtes dans **une région** avec un taux de réussite de **0 %** et des latences de quelques millisecondes, aucune dans les autres | Alexa traite votre compte dans cette région, et la Lambda n'y est pas (ou n'y est pas joignable). Déployez la Lambda dans la région du compte Alexa |
| Aucune requête nulle part | Alexa n'a pas lancé de découverte : refaire la découverte depuis l'app |
| Taux de réussite élevé mais appareils absents | La Lambda répond : vérifier les équipements cochés ([Équipements](#équipements-et-scènes)) |

Remède : la fenêtre *Déployer Skill SmartHome* affiche la **région du compte Alexa** ; relancez l'étape 1, qui y place la Lambda, puis l'étape 2. L'ancienne Lambda n'est pas supprimée (à retirer à la main si elle ne sert plus).

## Commandes et états

| Symptôme | Cause probable | Que faire |
|---|---|---|
| « Alexa ne répond pas » à une commande vocale | Jeedom injoignable depuis la Lambda (HTTPS, pare-feu, DNS) | Journal CloudWatch : `Unreachable contacting Jeedom` ; tester l'URL externe |
| `INVALID_AUTHORIZATION_CREDENTIAL` | Jeton absent/invalide | Relier à nouveau le compte |
| `ENDPOINT_UNREACHABLE` sur une caméra | `CAMERA_URL` n'est pas un flux `rtsp(s)://` public | [Caméras](camera-rtsp-relay.html) |
| État **périmé** dans l'app Alexa, température ancienne à la voix, routine sur capteur qui ne se déclenche pas | Push proactif non configuré, ou découverte pas refaite depuis son activation (Alexa répond depuis son cache) | [Push proactif](#push-proactif-états-à-jour-sonnette), puis relancer une découverte |
| Sonnette muette | Push non actif, catégorie ≠ DOORBELL, pas de commande `BUTTON` (Bouton), annonces de sonnette non activées sur l'appareil dans l'app Alexa, Echo en « Ne pas déranger ». Si tout est en place : désactiver puis réactiver le skill | [Sonnette](#sonnette) |
| Thermostat sans affichage de mode | Libellé de mode inconnu d'Alexa | Libellés reconnus dans [Équipements](#chauffage-climatisation-humidité) |

## Tests automatiques

Les suites du dossier `tests/php/` s'exécutent sans Jeedom ni AWS :

```bash
for f in tests/php/test_*.php; do php "$f"; done
```

Elles couvrent la connexion AWS, la fiche du skill, la limitation de la permission Lambda, le proxy Smart Home, l'OAuth, les événements et la virtualisation. Les appels réseau réels (AWS, Amazon) ne sont volontairement pas testés.
