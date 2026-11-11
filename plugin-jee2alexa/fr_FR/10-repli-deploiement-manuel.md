# Repli : déployer la Lambda à la main

Ce document sert quand le bouton **Déployer / Mettre à jour le code Lambda** échoue et que vous ne parvenez pas à le débloquer avec le [dépannage](#dépannage). Il explique comment obtenir **exactement le même résultat** sans l'automatisation, puis comment rebrancher le plugin dessus.

> Vous n'avez besoin de ce document que si l'automatisation est bloquée. Si l'erreur est un simple prérequis (URL non HTTPS, session AWS expirée, compte AWS pas encore activé), corrigez-le : c'est plus rapide.

## Quelle voie choisir ?

| Situation | Voie |
|---|---|
| Le déploiement échoue sur **une** étape précise (rôle, création de la fonction, permission) mais l'accès AWS du plugin fonctionne | **Voie A** : créer à la main seulement ce qui bloque, puis relancer le bouton 1 qui « adopte » l'existant |
| Le plugin **n'arrive pas du tout à joindre AWS** (connexion refusée, droits insuffisants impossibles à corriger, session impossible) | **Voie B** : tout à la main, puis renseigner les valeurs dans le plugin en ligne de commande |
| Vous préférez ne pas utiliser la console AWS au clic | **Voie C** : les mêmes opérations en ligne de commande (AWS CloudShell, sans rien installer) |

## Ce que le bouton 1 fait (donc ce qu'il faut reproduire)

| # | Opération automatique | Équivalent manuel |
|---|---|---|
| 1 | Créer le rôle `premiumSmartHome-lambda-role` + politique `AWSLambdaBasicExecutionRole` | [§ 2](#2-créer-le-rôle-iam) |
| 2 | Construire le zip (`lambda_function.py` + URL externe injectée) | [§ 1](#1-construire-le-zip) |
| 3 | Créer la fonction `premiumSmartHome` (Python 3.12, 128 Mo, 8 s) | [§ 3](#3-créer-la-fonction-lambda) |
| 4 | Attendre l'état `Active` | Contrôle visuel dans la console |
| 5 | Autoriser Alexa à invoquer la fonction | [§ 4](#4-autoriser-alexa-à-invoquer-la-fonction) |
| 6 | Enregistrer l'ARN dans le plugin | [§ 5](#5-rebrancher-le-plugin) |

> **Validé en conditions réelles.** Les étapes de ce document ont été déroulées sur une fonction de test (`premiumSmartHome-test`, rôle `premiumSmartHome-test-role`) dans la console AWS **en français**, région `eu-west-1` : rôle, fonction, zip, délai, événement de test, et les commandes CloudShell du § 4. Les libellés de la console évoluent : si un nom a changé, cherchez le même réglage.

## Informations à rassembler

| Valeur | Où la trouver |
|---|---|
| **Région** | Celle du **compte Alexa**, pas celle de votre langue : lisez-la dans la fenêtre *Déployer Skill SmartHome* (ligne *Région du compte Alexa*). `eu-west-1` pour un compte européen, `us-east-1` pour un compte nord-américain ([prérequis](#5-région-aws-et-langue--à-choisir-ensemble)) |
| **ID de compte AWS** (12 chiffres) | Console AWS, menu du compte en haut à droite |
| **URL externe de Jeedom** | Réglages > Système > Configuration > Réseaux. Doit être en `https://`, sans `/` final |
| **ID du skill** (`amzn1.ask.skill.…`) | Fenêtre *Déployer Skill SmartHome* (ligne *Skill ID*), une fois le skill créé |

## 1. Construire le zip

La fonction est **un seul fichier Python** sans dépendance. Il faut remplacer le marqueur `{% raw %}{{JEEDOM_URL}}{% endraw %}` par l'URL externe de Jeedom et placer le fichier **à la racine** d'une archive zip, sous le nom `lambda_function.py`.

Sur la box Jeedom (PHP est toujours disponible) :

```bash
php -r '$z = new ZipArchive; $z->open($argv[2], ZipArchive::CREATE | ZipArchive::OVERWRITE);
$src = file_get_contents("/var/www/html/plugins/jee2alexa/data/skill_smarthome/lambda/lambda_function.py");
$z->addFromString("lambda_function.py", str_replace("{% raw %}{{JEEDOM_URL}}{% endraw %}", rtrim($argv[1], "/"), $src)); $z->close();' \
  "https://jeedom.example.com" /tmp/jee2alexa-lambda.zip
```

Remplacez `https://jeedom.example.com` par votre URL externe. Le fichier `/tmp/jee2alexa-lambda.zip` (environ 3 Ko) est à transférer sur l'ordinateur qui utilise la console AWS.

Contrôle (le marqueur ne doit plus apparaître, et la ligne `JEEDOM_URL` doit porter votre adresse) :

```bash
unzip -p /tmp/jee2alexa-lambda.zip lambda_function.py | grep -n "^JEEDOM_URL"
```

> Sans PHP en ligne de commande, copiez `lambda_function.py`, remplacez à la main `{% raw %}{{JEEDOM_URL}}{% endraw %}` par votre URL (une seule occurrence), puis compressez le fichier avec n'importe quel outil. Le fichier doit s'appeler `lambda_function.py` et se trouver **à la racine** de l'archive, pas dans un dossier.

## 2. Créer le rôle IAM

Si le rôle `premiumSmartHome-lambda-role` existe déjà, passez au § 3 et notez son ARN.

1. Console AWS → **IAM** → **Rôles** → **Créer un rôle**.
2. Étape *Sélectionner une entité de confiance* : type **Service AWS**, puis dans **Service ou cas d'utilisation** choisissez **Lambda** (le cas d'utilisation *Lambda* est alors coché) → **Suivant**.
3. Étape *Ajouter des autorisations* : cherchez **`AWSLambdaBasicExecutionRole`**, cochez la ligne (écriture des journaux uniquement) → **Suivant**.
4. Étape *Nommer, vérifier et créer* : nom `premiumSmartHome-lambda-role`. La politique d'approbation affichée doit désigner `lambda.amazonaws.com` → **Créer un rôle**.
5. Ouvrez le rôle et notez son **ARN** : `arn:aws:iam::ID_COMPTE:role/premiumSmartHome-lambda-role`.

Un rôle neuf met quelques secondes à être utilisable : en cas d'erreur « the role defined for the function cannot be assumed by Lambda », attendez une minute et réessayez.

## 3. Créer la fonction Lambda

1. Console AWS, **région choisie** (sélecteur en haut à droite), **Lambda** → **Créer une fonction** (la page s'intitule *Create Event Function*) → **Créer à partir de zéro**.
2. Renseignez :

| Champ | Valeur |
|---|---|
| **Nom de la fonction** | `premiumSmartHome` (le plugin l'attend sous ce nom exact) |
| **Exécution** | **Python 3.12**. Il n'est pas en tête de liste (Python 3.14 y figure) : tapez `Python 3.12` dans le champ de recherche du menu, la valeur est classée dans *Autre prise en charge* |

3. Dépliez **Paramètres supplémentaires**, activez l'interrupteur **Rôle d'exécution personnalisé** : un panneau s'ouvre. Dans **Rôle d'exécution**, choisissez le rôle `premiumSmartHome-lambda-role`, puis **Enregistrer**. (Sans ce réglage, Lambda créerait un rôle automatique à la place.)
4. Cliquez **Create Event Function** (bouton orange en bas de page). Fermez la fenêtre *Getting started* qui s'affiche.
5. Onglet **Code** → bouton **Mettre à jour** (au-dessus de l'éditeur) → **Mettre à jour à partir d'un fichier .zip** → **Choisir un fichier** → `jee2alexa-lambda.zip` → **Mettre à jour**. L'éditeur doit alors afficher `lambda_function.py` avec votre URL à la ligne `JEEDOM_URL`.
6. Onglet **Configuration** → **Configuration générale** → **Modifier** : la **mémoire** est déjà à **128 Mo** (valeur par défaut) ; réglez **Expiration** à **0 min 8 secondes** (la valeur par défaut est de 3 secondes) → **Enregistrer**.
7. Le **gestionnaire** est `lambda_function.lambda_handler`, valeur par défaut d'une fonction Python : rien à changer.
8. Notez l'**ARN de la fonction** (en haut de la page) : `arn:aws:lambda:eu-west-1:ID_COMPTE:function:premiumSmartHome`.

Il n'y a **aucune variable d'environnement** à définir : l'URL de Jeedom est écrite dans le code.

## 4. Autoriser Alexa à invoquer la fonction

Sans cette autorisation, le skill s'active mais Alexa répond que l'appareil « ne répond pas ».

La console ne permet d'ajouter le déclencheur *Alexa Smart Home* **qu'avec l'identifiant du skill**, que vous n'avez pas encore avant d'avoir créé le skill (le skill demande l'ARN de la fonction, l'autorisation demande le skill : le plugin résout cela en deux temps). Procédez de même :

**Temps 1 — autorisation « ouverte »** (avant de créer le skill). Le plus simple est **AWS CloudShell** (icône `>_` en bas à gauche de la console AWS, aucune installation) :

```bash
aws lambda add-permission --region eu-west-1 \
  --function-name premiumSmartHome \
  --statement-id AlexaSmartHomeInvoke \
  --action lambda:InvokeFunction \
  --principal alexa-connectedhome.amazon.com
```

**Temps 2 — restriction à votre skill** (après l'étape 2 du plugin, qui donne l'ID du skill). Le plugin le fait seul si son accès AWS fonctionne (ligne *Permission Lambda limitée au skill* de l'étape 2). Sinon, à la main :

```bash
aws lambda add-permission --region eu-west-1 \
  --function-name premiumSmartHome \
  --statement-id AlexaSmartHomeInvokeSkill \
  --action lambda:InvokeFunction \
  --principal alexa-connectedhome.amazon.com \
  --event-source-token amzn1.ask.skill.VOTRE-ID-DE-SKILL

aws lambda remove-permission --region eu-west-1 \
  --function-name premiumSmartHome \
  --statement-id AlexaSmartHomeInvoke
```

L'ordre (ajouter la restreinte, **puis** retirer l'ouverte) évite toute coupure. Pourquoi la restreindre : [sécurité](#permission-dinvocation-limitée-à-votre-skill).

## 5. Rebrancher le plugin

Le plugin retient l'ARN de la Lambda dans sa configuration ; l'étape 2 (skill) en dépend. Deux façons de le renseigner :

### Option 1 — laisser le plugin « adopter » la Lambda (si son accès AWS fonctionne)

Dans la fenêtre *Déployer Skill SmartHome*, cliquez à nouveau **Déployer / Mettre à jour le code Lambda**. La fonction existant déjà, le plugin se contente d'**envoyer le code**, d'attendre l'état `Active`, de vérifier la permission Alexa et d'**enregistrer l'ARN**. Aucune ressource n'est recréée.

### Option 2 — renseigner l'ARN en ligne de commande (si le plugin n'a aucun accès AWS)

À exécuter **en tant que `www-data`** sur la box :

```bash
sudo -u www-data php -r 'require "/var/www/html/core/php/core.inc.php";
config::save("lambda_function_arn", $argv[1], "jee2alexa");
config::save("lambda_last_deploy", date("Y-m-d H:i:s"), "jee2alexa");' \
  "arn:aws:lambda:eu-west-1:ID_COMPTE:function:premiumSmartHome"
```

Le rôle se renseigne, lui, dans l'interface : configuration du plugin → **ARN du rôle IAM d'exécution** (ou laissez-le vide si le plugin n'a pas accès à IAM et que vous n'en avez pas besoin).

Cliquez ensuite **Créer / Mettre à jour le skill** (étape 2) : elle n'a besoin que de l'ARN enregistré et d'`alexaapiv2`. Si l'accès AWS du plugin est inopérant, la ligne *Permission Lambda limitée au skill* s'affichera en **rouge** sans empêcher la création du skill : faites alors le **temps 2** du § 4.

## 6. Vérifier

### Dans la console AWS

La page de la fonction doit afficher **État : Active** et **Dernière mise à jour : réussie**.

### Test sans Alexa

Console Lambda → onglet **Test** → créez un événement (par exemple `discovery`) :

```json
{
  "directive": {
    "header": { "namespace": "Alexa.Discovery", "name": "Discover", "payloadVersion": "3", "messageId": "test-1" },
    "payload": { "scope": { "type": "BearerToken", "token": "test" } }
  }
}
```

Lancez-le (bouton orange **Tester**). La console affiche **« Exécution de la fonction : réussite »** : cela signifie seulement que la fonction s'est exécutée sans planter, **pas** que l'authentification a abouti. Dépliez **Détails** et lisez la **réponse** : le jeton `test` est volontairement invalide, donc **une réponse d'erreur est le résultat normal**. Ce test prouve que la Lambda joint votre Jeedom :

| Réponse (champ *Response*) | Journal CloudWatch | Interprétation |
|---|---|---|
| `ErrorResponse` avec `INVALID_AUTHORIZATION_CREDENTIAL` et le message `Token rejected` | Aucune ligne d'erreur (la ligne `Received:` du journal masque le jeton) | **Tout va bien** : la Lambda a joint Jeedom, qui a refusé le faux jeton. L'exécution dure plus d'une demi-seconde, signe d'un vrai appel réseau |
| `ErrorResponse` avec `BRIDGE_UNREACHABLE` (`Jeedom unreachable`) | `Unreachable contacting Jeedom` | La Lambda ne joint pas Jeedom : URL externe fausse ou non résolue, certificat invalide, pare-feu |
| `ErrorResponse` avec `INTERNAL_ERROR` (`JEEDOM_URL not configured`) | `JEEDOM_URL not configured` | Le marqueur `{% raw %}{{JEEDOM_URL}}{% endraw %}` est resté dans le code : refaire le § 1 |
| `ErrorResponse` avec `INTERNAL_ERROR` (`Jeedom HTTP 5xx` ou `HTTP …`) | `Jeedom returned HTTP …` | Jeedom répond mais en erreur : consulter `log/http.error` et `log/jee2alexa` |

Vous pouvez faire le même contrôle depuis n'importe quelle machine, sans AWS :

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST \
  "https://jeedom.example.com/plugins/jee2alexa/core/php/smartHomeProxy.php" \
  -H "Authorization: Bearer test" -d '{}'
```

Le code `401` signifie que le proxy Jeedom est joignable et protégé.

### Dans le plugin

Fenêtre **Santé** → **Lancer un diagnostic** : *Fonction Lambda déployée (vérifié côté AWS)* doit passer à **OK** si le plugin a un accès AWS.

## Mettre à jour la Lambda à la main

Refaites le § 1 (zip), puis dans la console : fonction → onglet **Code** → **Mettre à jour** → **Mettre à jour à partir d'un fichier .zip** → **Choisir un fichier** → **Mettre à jour**. Nécessaire :

- après une mise à jour du plugin qui modifie `lambda_function.py` (par exemple le masquage des jetons dans les journaux) ;
- après un **changement d'URL externe** (l'URL est dans le code ; pensez aussi à *Republier l'Account Linking*, voir [déploiement](#changer-dadresse-ou-mettre-à-jour)).

## Pièges fréquents

| Piège | Conséquence | Remède |
|---|---|---|
| Mauvaise **région** (hors `eu-west-1` pour fr-FR) | Skill actif mais appareils introuvables ou « ne répondent pas » | Créer la fonction dans la bonne région |
| Nom de fonction différent de `premiumSmartHome` | Le plugin ne la retrouve pas, ses vérifications échouent | Respecter le nom exact |
| Fichier non nommé `lambda_function.py`, ou placé dans un dossier du zip | `Runtime.ImportModuleError` | Zip à plat, bon nom, gestionnaire `lambda_function.lambda_handler` |
| URL en `http://` ou avec un `/` final | L'Account Linking et les appels sont refusés ou mal formés | URL `https://…` sans `/` final |
| Délai d'expiration par défaut (3 s) | Les commandes lentes échouent | Régler 8 s |
| Autorisation absente | « L'appareil ne répond pas » | § 4 |
| Autorisation laissée « ouverte » | Tout skill Smart Home connaissant l'ARN peut appeler la fonction | § 4, temps 2 |

## Revenir à l'automatique

Quand le blocage d'origine est levé (droits, connexion, compte), **rien n'est à défaire** : le bouton 1 reconnaît la fonction et le rôle existants et ne fait que les mettre à jour. Les ressources créées à la main (même noms) sont donc reprises par le plugin sans doublon.
