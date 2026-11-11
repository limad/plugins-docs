# Prérequis

Avant de lancer le moindre déploiement, vérifiez ces cinq points. La plupart des échecs viennent d'un prérequis manquant, pas d'un défaut du plugin.

## 1. Un Jeedom joignable en HTTPS depuis Internet

Alexa doit pouvoir appeler votre Jeedom (via la Lambda) et rediriger votre navigateur vers sa page de connexion (liaison de compte).

- **HTTPS obligatoire**, avec un **certificat reconnu publiquement** (un certificat auto-signé est refusé par Alexa). Port 443 recommandé.
- Le service **DNS Jeedom** (adresse `xxxx.eu.jeedom.link`) convient : il fournit un nom et un certificat valides sans ouvrir de port chez vous. Un nom de domaine à vous avec Let's Encrypt convient aussi.
- Renseignez l'adresse dans **Réglages > Système > Configuration > Réseaux** (accès externe : protocole `https://`, adresse, port `443`).

Le plugin **vérifie** cette adresse avant de déployer et la refuse si elle est vide ou en `http` :

> URL externe Jeedom absente ou non HTTPS (valeur actuelle : « http: ») — renseignez-la dans Réglages > Système > Configuration > Réseaux

> **Important** : l'URL externe est **écrite dans le code de la Lambda** au moment du déploiement et dans la fiche d'Account Linking du skill. Si vous changez d'adresse plus tard, il faut **redéployer la Lambda** puis **republier l'Account Linking** (voir [déploiement](#changer-dadresse-ou-mettre-à-jour)).

## 2. Un compte Amazon, le même partout

Le skill est en phase *développement* : il n'apparaît que pour le **compte Amazon développeur** qui l'a créé.

- Le compte utilisé dans l'**app Alexa** (téléphone) doit être **le même** que celui du compte développeur configuré dans `alexaapiv2`.
- Si les deux diffèrent, le skill reste introuvable dans l'app, et Amazon répond « Enablement not found » (voir [dépannage](#dépannage)).

## 3. `alexaapiv2` installé et authentifié

`jee2alexa` s'appuie sur le plugin **`alexaapiv2`** pour parler à l'API Amazon des skills (SMAPI) : création du skill, mise à jour de la fiche, Account Linking.

- `alexaapiv2` doit être installé, activé, et **authentifié auprès d'Amazon** (cookie de session, compte développeur, identifiant vendeur).
- Sans cela, « Créer / Mettre à jour le skill » échoue (messages : *Plugin alexaapiv2 introuvable*, *Authentification développeur Amazon absente*).

## 4. Un compte AWS avec Lambda activé

- Compte **Amazon Web Services** (distinct du compte Amazon.fr, même s'il peut utiliser la même adresse e-mail).
- Un **moyen de paiement valide** et l'inscription terminée (vérification téléphone et identité). Tant que l'inscription n'est pas finalisée, Lambda peut répondre `SubscriptionRequiredException`.
- Vérifiez que le service est actif : ouvrez la [console Lambda (eu-west-1)](https://eu-west-1.console.aws.amazon.com/lambda/home?region=eu-west-1#/begin). Si la page d'accueil du service s'affiche, Lambda est utilisable. L'activation d'un compte récent peut prendre jusqu'à 24 h.
- **Coût** : un usage domestique reste très en deçà du niveau gratuit de Lambda, qui est permanent : 1 million d'appels et 400 000 Go-s par mois, partagés entre toutes les fonctions Lambda du compte. Vérifiez les conditions en vigueur sur la [page tarifaire AWS](https://aws.amazon.com/lambda/pricing/). Seuls le rôle IAM et une fonction de 128 Mo sont créés. Mesuré sur une installation réelle : environ 1 000 appels par mois, soit 0,1 % du gratuit. Au-delà, comptez environ 1 USD par million d'appels supplémentaires. Garde-fous du plugin :
  - fenêtre **Santé > Consommation AWS** : appels du mois, part de la limite gratuite, projection de fin de mois, coût estimé, taille des journaux, budget AWS ;
  - **message Jeedom** à 50, 80 et 100 % de la limite gratuite (contrôle horaire, projection incluse après 3 jours), désactivable dans la section **Coûts AWS** de la configuration ;
  - en option, **suspension automatique** de la Lambda à 100 % (Alexa ne pilote plus rien jusqu'à sa réactivation) ;
  - **budget AWS** créé par le plugin (gratuit) : AWS vous écrit lui-même si la dépense réelle de **tout** le compte dépasse 80 % du montant choisi, ou si la dépense prévue le dépasse ;
  - journaux CloudWatch de la Lambda gardés **30 jours** au lieu d'indéfiniment.

## 5. Région AWS et langue : à choisir ensemble

Pour un skill Smart Home, Amazon associe chaque langue du skill à **une région Lambda précise** (tableau de la documentation Amazon sur les skills Smart Home multilingues) :

| Langues du skill | Région Lambda | Région Alexa |
|---|---|---|
| fr-FR, de-DE, en-GB, es-ES, it-IT, nl-NL, en-IN, hi-IN | **eu-west-1** (Irlande) | Europe et Inde |
| en-US, en-CA, fr-CA, es-MX, es-US, pt-BR, ar-SA | **us-east-1** (Virginie) | Amérique du Nord |
| ja-JP, en-AU | **us-west-2** (Oregon) | Extrême-Orient et Australie |

Un décalage entre la région du compte Alexa et la région de la Lambda échoue **silencieusement** : le skill s'active et se lie, mais les appareils restent introuvables ou « ne répondent pas ».

### La région du compte Alexa prime sur la langue et sur l'adresse Amazon

Le tableau ci-dessus donne le cas général. Ce qui décide est la **région du compte Alexa**, telle qu'Alexa la connaît, et elle peut différer de ce que laisse croire votre compte : un compte connecté par `amazon.fr`, avec une app en français, peut être rattaché à l'**Amérique du Nord** (app installée avec le marché américain). Dans ce cas, la Lambda doit être en **us-east-1**, même en France.

Le plugin la **lit chez Alexa avant tout déploiement** (appel `GET /api/endpoints`, clé `awsRegion`, avec la session d'`alexaapiv2`) :

- la fenêtre *Déployer Skill SmartHome* affiche une ligne **Région du compte Alexa** et signale tout écart avec la région du plugin ;
- le déploiement de la Lambda **aligne la région du plugin** sur celle du compte Alexa, après une confirmation qui nomme les deux régions ;
- « Créer / Mettre à jour le skill » **refuse** de continuer si la Lambda n'est pas dans cette région ;
- la page de configuration propose **Détecter la région du compte Alexa**.

Vérification manuelle : dans l'app Alexa, la région est celle du serveur utilisé ; dans la console développeur, la page *Analytics → Smart Home → Discovery* du skill, filtrée par région (NA, EU, FE), montre dans quelle région arrivent les découvertes (voir [dépannage](#découverte-qui-échoue-sans-appeler-la-lambda)).

- Le plugin déclare les langues **fr-FR** et **en-US** (fichier `data/skill_smarthome/skill.json`) mais ne déploie **qu'une** Lambda. Avec `eu-west-1`, seul **fr-FR** est joignable. Pour un compte Alexa américain, choisissez `us-east-1` et adaptez la langue du skill.
- Le sélecteur de région propose aussi `eu-west-2` et `eu-central-1` ; ils ne sont **pas listés par Amazon** pour Smart Home. Restez sur `eu-west-1` pour la France.

## 6. Des équipements Jeedom exploitables par Alexa

Le plugin ne devine pas le type d'un équipement : il lit le **type générique** de ses commandes (Lumière, Énergie, Thermostat, Volet…). Un équipement dont les commandes n'ont **aucun type générique reconnu** apparaît « Non pris en charge » et ne peut pas être exposé.

Voir [Équipements et scènes](#équipements-et-scènes) pour la liste des types pris en charge.

## Dépendances techniques

| Élément | Détail |
|---|---|
| Jeedom | ≥ 4.4 |
| SDK AWS pour PHP | Installé automatiquement par Composer à l'installation/mise à jour du plugin (dossier `vendor/`, environ 55 Mo) |
| Droits fichiers | Les fichiers du plugin appartiennent à `www-data` (utilisateur du serveur web) |
| AWS CLI / Python sur la box | **Non requis** : la connexion AWS est implémentée en PHP natif |

## Liste de contrôle

- [ ] Jeedom répond en HTTPS sur son adresse externe, certificat valide
- [ ] Adresse externe saisie dans Jeedom (`https://…`)
- [ ] `alexaapiv2` installé et authentifié (compte développeur)
- [ ] Même compte Amazon dans l'app Alexa et dans `alexaapiv2`
- [ ] Compte AWS actif, console Lambda accessible
- [ ] Région `eu-west-1` (pour fr-FR)
- [ ] Au moins un équipement avec des types génériques configurés
