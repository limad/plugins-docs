---
layout: default
title: "jee2alexa — Configuration AWS"
lang: fr_FR
pluginId: jee2alexa
---
# Configuration AWS — rôle d'exécution Lambda & utilisateur de déploiement

> Cette intégration s'appuie sur un **Lambda AWS réel et un skill Amazon vous appartenant** (voir [présentation](./)) — pas sur un skill Alexa-hosted ni sur un service tiers. Elle ne fonctionne qu'avec un compte **Amazon Web Services** classique.

## Deux modes de connexion

Le plugin sait se connecter à AWS de deux façons (réglage **Mode de connexion** de la configuration, voir [installation](./#4-configurer-le-plugin)) :

| Mode | Principe | Quand l'utiliser |
|---|---|---|
| **Se connecter à AWS** (recommandé) | Lien + code, comme `aws login --remote` ; identifiants **temporaires** (15 min, renouvelés automatiquement, session de 12 h). Le rôle d'exécution est **créé automatiquement** | Cas général. Aucune clé permanente. Voir [identité du mode connexion](#identité-du-mode-connexion) plus bas |
| **Clés IAM** | Un utilisateur IAM de déploiement avec une clé d'accès permanente, limité à la fonction `premiumSmartHome` | Si vous refusez toute connexion interactive. Procédure ci-dessous |

La suite de ce document décrit le **mode clés IAM** en détail (rôle d'exécution créé à la main, utilisateur de déploiement, politique JSON).

---

Deux identités IAM sont nécessaires, avec des rôles différents :

| Identité | Rôle | Utilisée par |
|---|---|---|
| **Rôle d'exécution** | Identité que le Lambda *endosse à l'exécution* pour écrire ses logs CloudWatch | AWS Lambda, au runtime |
| **Utilisateur de déploiement** | Identité que **Jeedom** utilise pour créer/mettre à jour le code du Lambda | Le plugin, au moment du clic sur *Déployer* |

Ne les confondez pas : le rôle n'a jamais besoin d'agir depuis Jeedom, et l'utilisateur de déploiement n'a jamais besoin d'écrire dans CloudWatch.

Comme pour tout accès programmatique, il est fortement recommandé de restreindre chaque politique IAM au strict nécessaire plutôt que d'utiliser des permissions larges (`AdministratorAccess`, `AWSLambda_FullAccess`...) ou les identifiants du compte root.

---

## Prérequis

| Composant | Nécessaire |
|---|---|
| Compte AWS actif | Oui |
| Droits console sur IAM (créer rôles, utilisateurs, politiques) | Oui |
| Région AWS de déploiement | À choisir — pour un skill Smart Home, Amazon associe les locales à `eu-west-1` (Europe, fr-FR), `us-east-1` (Amérique du Nord) ou `us-west-2` (Extrême-Orient / Australie) ; `eu-west-2` et `eu-central-1` restent sélectionnables mais ne sont pas listés par Amazon. Doit être la même pour le rôle, l'utilisateur et la config du plugin |


### Vérifier que le service Lambda est actif sur le compte

Ouvrez la [console Lambda (eu-west-1)](https://eu-west-1.console.aws.amazon.com/lambda/home?region=eu-west-1#/begin) : si la page d'accueil du service s'affiche, Lambda est utilisable. Si AWS signale que le compte n'est pas encore activé pour ce service, ou si le déploiement du plugin échoue avec `SubscriptionRequiredException`, terminez l'inscription du compte (moyen de paiement, vérification téléphone et identité) dans **Compte** ; l'activation peut prendre jusqu'à 24 h.

---

## 1. Créer le rôle d'exécution du Lambda

1. Connectez-vous à la [console de gestion AWS](https://console.aws.amazon.com/) et accédez à **IAM > Rôles**.
2. Cliquez sur **Créer un rôle**.
3. Type d'entité de confiance : **Service AWS** — cas d'utilisation : **Lambda**.
4. Dans la liste des politiques, attachez la politique managée **`AWSLambdaBasicExecutionRole`** (droits d'écriture CloudWatch Logs uniquement — c'est tout ce dont le Lambda a besoin).
5. Nommez le rôle, par exemple `premiumSmartHome-lambda-role`.
6. Créez le rôle et **notez son ARN** (`arn:aws:iam::VOTRE_ID_COMPTE:role/premiumSmartHome-lambda-role`) — il sera collé dans la config du plugin à l'étape 3.

> **Étape facultative.** Si vous laissez l'**ARN du rôle** vide dans la configuration, le plugin crée (ou réutilise) lui-même `premiumSmartHome-lambda-role` au premier déploiement, à condition que l'identité AWS ait les droits IAM nécessaires. La création à la main ci-dessus sert quand vous voulez **imposer un rôle existant** ou **retirer tout droit IAM** à l'utilisateur de déploiement (dans ce cas l'ARN doit être saisi).

---

## 2. Créer l'utilisateur IAM de déploiement

1. Accédez à **IAM > Utilisateurs > Créer un utilisateur**.
2. Donnez-lui un nom, par exemple `jeedom-jee2alexa-deploy`. Ne cochez **pas** d'accès à la console de gestion — cet utilisateur n'est utilisé qu'en accès programmatique (access key), jamais pour se connecter à la console.
3. Créez maintenant une politique dédiée plutôt que d'attacher une politique large : **Créer une politique**, onglet **JSON**, collez ceci en remplaçant `VOTRE_REGION`, `VOTRE_ID_COMPTE` et `ARN_DU_ROLE_ETAPE_1` :

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowConnectionTest",
      "Effect": "Allow",
      "Action": "lambda:ListFunctions",
      "Resource": "*"
    },
    {
      "Sid": "AllowSmartHomeLambdaDeploy",
      "Effect": "Allow",
      "Action": [
        "lambda:GetFunction",
        "lambda:GetFunctionConfiguration",
        "lambda:CreateFunction",
        "lambda:UpdateFunctionCode",
        "lambda:GetPolicy",
        "lambda:AddPermission",
        "lambda:RemovePermission",
        "lambda:PutFunctionConcurrency",
        "lambda:DeleteFunctionConcurrency"
      ],
      "Resource": "arn:aws:lambda:VOTRE_REGION:VOTRE_ID_COMPTE:function:premiumSmartHome"
    },
    {
      "Sid": "AllowCostMonitoring",
      "Effect": "Allow",
      "Action": [
        "cloudwatch:GetMetricStatistics",
        "logs:DescribeLogGroups"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowLambdaLogRetention",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:PutRetentionPolicy"
      ],
      "Resource": "arn:aws:logs:VOTRE_REGION:VOTRE_ID_COMPTE:log-group:/aws/lambda/premiumSmartHome:*"
    },
    {
      "Sid": "AllowCostBudget",
      "Effect": "Allow",
      "Action": [
        "budgets:ViewBudget",
        "budgets:ModifyBudget"
      ],
      "Resource": "arn:aws:budgets::VOTRE_ID_COMPTE:budget/jee2alexa-monthly"
    },
    {
      "Sid": "AllowPassExecutionRoleToLambdaOnly",
      "Effect": "Allow",
      "Action": "iam:PassRole",
      "Resource": "ARN_DU_ROLE_ETAPE_1",
      "Condition": {
        "StringEquals": { "iam:PassedToService": "lambda.amazonaws.com" }
      }
    }
  ]
}
```

Le détail de chaque bloc, pour ne pas coller une politique à l'aveugle :
- **`AllowConnectionTest`** — `lambda:ListFunctions` sert uniquement au bouton *Tester la connexion AWS* de la config. AWS n'autorise pas de restriction par ressource sur cette action (elle doit rester en `Resource: "*"`), mais elle ne permet ni lecture ni modification d'aucune fonction.
- **`AllowSmartHomeLambdaDeploy`** — restreint à la fonction `premiumSmartHome` (nom fixe utilisé par le plugin, voir `jee2alexa_aws::FUNCTION_NAME`) : lecture (`GetFunction` pour détecter si elle existe déjà, `GetFunctionConfiguration` pour attendre qu'elle soit opérationnelle), création, mise à jour du code, et l'autorisation donnée à Alexa d'invoquer la fonction (`GetPolicy`, `AddPermission`). `RemovePermission` sert à limiter cette autorisation au seul skill du plugin : à la création du skill, l'autorisation « ouverte » ajoutée avec la Lambda est remplacée par une autorisation liée à l'identifiant du skill. `PutFunctionConcurrency`/`DeleteFunctionConcurrency` servent au bouton **Suspendre / Réactiver la Lambda** (concurrence réservée à 0, puis retirée) ; sans eux, seul ce bouton échoue.
- **`AllowCostMonitoring`** — lecture seule, pour la consommation (fenêtre **Santé > Consommation AWS** et contrôle horaire) : nombre d'appels et durée de la Lambda (CloudWatch), taille et durée de conservation de ses journaux. Ces deux actions n'acceptent pas de restriction par ressource. Sans elles, seule la consommation est indisponible.
- **`AllowLambdaLogRetention`** — limite à 30 jours la conservation des journaux de la Lambda, au déploiement (ou via le bouton *Limiter à 30 jours* de la fenêtre Santé). Par défaut, AWS les garde sans limite. Sans ce droit, le déploiement réussit quand même, avec un avertissement.
- **`AllowCostBudget`** — budget AWS `jee2alexa-monthly` (bouton *Créer / mettre à jour le budget AWS*, section **Coûts AWS** de la configuration). Optionnel.
- **`AllowPassExecutionRoleToLambdaOnly`** — AWS exige explicitement que l'appelant ait le droit de « passer » le rôle d'exécution au service Lambda lors de `CreateFunction`. La condition `iam:PassedToService` empêche que cet utilisateur puisse faire passer ce rôle à autre chose qu'AWS Lambda.

4. Nommez la politique, par exemple `AlexaSmartHomeLambdaDeployPolicy`, créez-la, puis attachez-la à l'utilisateur créé en étape 2.
5. Terminez la création de l'utilisateur, puis ouvrez son onglet **Informations d'identification de sécurité > Créer une clé d'accès** — cas d'utilisation **Interface de ligne de commande (CLI)**.
6. **Notez immédiatement** l'ID de clé d'accès et la clé secrète : cette dernière ne sera plus jamais affichée en clair par la suite.

---

## 3. Renseigner la configuration du plugin

Dans **Configuration du plugin > Compte AWS (déploiement du Lambda)** :

| Champ | Valeur |
|---|---|
| **AWS Access Key ID** | Celle générée à l'étape 2.5 |
| **AWS Secret Access Key** | Celle générée à l'étape 2.5 |
| **Région AWS** | La même région que celle utilisée dans l'ARN de la politique et du rôle |
| **ARN du rôle IAM d'exécution** | L'ARN noté à l'étape 1.6 |

Cliquez sur **Tester la connexion AWS** pour valider les identifiants avant de déployer.

Une fois le test réussi, retournez sur l'écran principal du plugin, ouvrez **Déployer Skill SmartHome**, puis **Déployer / Mettre à jour le code Lambda**. Cette action est idempotente : elle crée le Lambda s'il n'existe pas encore, ou se contente de mettre à jour son code sinon (l'ARN ne change jamais après la création).

---

## Identité du mode connexion

En mode **Se connecter à AWS**, l'identité qui se connecte (utilisateur IAM, de préférence) a besoin de :

1. la politique gérée **`SignInLocalDevelopmentAccess`**, qui autorise la connexion de développement ;
2. les droits de la **politique de déploiement** ci-dessus (Lambda, `PassRole`) ;
3. si l'**ARN du rôle est laissé vide**, de quoi créer le rôle d'exécution, limité à ce seul rôle :

```json
{
  "Sid": "AllowExecutionRoleManagement",
  "Effect": "Allow",
  "Action": ["iam:GetRole", "iam:CreateRole"],
  "Resource": "arn:aws:iam::VOTRE_ID_COMPTE:role/premiumSmartHome-lambda-role"
},
{
  "Sid": "AllowAttachBasicExecutionPolicyOnly",
  "Effect": "Allow",
  "Action": "iam:AttachRolePolicy",
  "Resource": "arn:aws:iam::VOTRE_ID_COMPTE:role/premiumSmartHome-lambda-role",
  "Condition": {
    "ArnEquals": { "iam:PolicyARN": "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole" }
  }
}
```

> Ces droits IAM supplémentaires n'ont été validés qu'avec un compte à pleins pouvoirs. Avec une identité restreinte, un `AccessDenied` affiché dans la fenêtre de déploiement nomme l'action manquante : ajoutez-la à la politique et relancez (l'opération est idempotente). Une fois le rôle créé, vous pouvez retirer ces deux déclarations : elles ne servent qu'à la création.

La vérification de la connexion utilise `sts:GetCallerIdentity`, qui n'exige aucun droit particulier.

---

## Remarques

- N'utilisez jamais les identifiants du compte **root** AWS pour cet utilisateur de déploiement.
- La politique ci-dessus est volontairement restreinte à la seule fonction `premiumSmartHome` : si vous renommez la fonction ou tentez de réutiliser cet utilisateur pour un autre Lambda, l'appel échouera en `AccessDenied` — c'est le comportement attendu, pas un bug.
- En cas de doute sur une fuite de la clé secrète, révoquez-la immédiatement depuis **IAM > Utilisateurs > *nom* > Informations d'identification de sécurité** et générez-en une nouvelle : aucune autre partie du plugin n'en dépend (elle n'est utilisée que pour le déploiement, jamais pour le contrôle vocal au quotidien).
