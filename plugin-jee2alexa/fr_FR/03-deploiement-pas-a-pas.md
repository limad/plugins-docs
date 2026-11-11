# Déploiement pas à pas

Tout se fait depuis la tuile **Déployer Skill SmartHome**, dans l'ordre affiché en haut de la fenêtre :
**1 · Lambda AWS → 2 · Skill Alexa → 3 · activer le skill dans l'app Alexa → 4 · découvrir les équipements.**

## 0. Lire les prérequis affichés

La fenêtre commence par un bloc **Prérequis** qui reprend l'état réel de votre installation.

![Fenêtre de déploiement : prérequis et état actuel](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/02-modale-deploiement-etat.jpg)

| Ligne | Ce qu'elle vérifie |
|---|---|
| **Connexion AWS** | Connecté (mode connexion) ou clés saisies (mode clés). Badge orange si le compte est **root** |
| **Région AWS** | Région choisie dans la configuration |
| **Région du compte Alexa** | Lue chez Alexa. Coche verte si elle correspond à la région du plugin ; avertissement orange sinon (la Lambda sera placée dans la région du compte Alexa au prochain déploiement) |
| **URL externe Jeedom** | Coche verte si l'adresse est en HTTPS ; croix rouge avec le message d'erreur sinon |
| **Rôle IAM d'exécution** | ARN du rôle, ou « sera créé automatiquement » |

Le bouton **1** reste **grisé** tant que la connexion AWS ou l'URL externe n'est pas valide, et le bouton **2** tant que la Lambda n'a jamais été déployée.

Le bloc **État actuel** affiche la fonction (`premiumSmartHome`), son ARN, la date du dernier déploiement et l'identifiant du skill.

## 1. Déployer la Lambda

Cliquez **Déployer / Mettre à jour le code Lambda**.

Au **premier** déploiement, une fenêtre de confirmation rappelle ce qui va être créé dans votre compte AWS : le rôle IAM `premiumSmartHome-lambda-role` (s'il n'existe pas) et la fonction Lambda `premiumSmartHome`. Ces ressources peuvent être facturées selon votre compte.

Le plugin enchaîne, en affichant chaque étape (✓ ou ✗) :

| Étape affichée | Ce qui se passe |
|---|---|
| **Région du compte Alexa** | Lit la région du compte Alexa et **aligne la région du plugin** dessus avant de déployer. Si la région ne peut pas être lue, l'étape s'affiche en rouge et la région du plugin est conservée |
| **Rôle IAM d'exécution** | Crée `premiumSmartHome-lambda-role` (confiance `lambda.amazonaws.com`, politique `AWSLambdaBasicExecutionRole`) ou réutilise l'existant. Ignorée si vous avez saisi un ARN |
| **Package construit** | Zip d'un seul fichier Python (`lambda_function.py`, environ 3 Ko, sans dépendance) avec l'**URL externe** de Jeedom injectée |
| **Lambda créé** / **Code Lambda envoyé** | `createFunction` (Python 3.12, 128 Mo, délai 8 s) la première fois ; `updateFunctionCode` ensuite. Après création d'un rôle neuf, le plugin attend sa propagation (quelques secondes à une minute) |
| **Lambda opérationnel** | Attend l'état `Active` et `LastUpdateStatus=Successful` **côté AWS** avant d'annoncer le succès |
| **Permission d'invocation Alexa** | Autorise `alexa-connectedhome.amazon.com` à invoquer la fonction (créée si absente, jamais dupliquée) |

À la fin, l'**ARN** de la Lambda est enregistré et la ligne *État actuel* se met à jour.

L'opération est **idempotente** : relancez-la sans risque après une mise à jour du plugin. Elle ne change ni l'ARN ni les permissions.

> En cas d'échec, les étapes déjà réussies restent affichées avec le message AWS lisible (`AccessDenied`, jeton expiré, rôle non propagé…). Voir [dépannage](#dépannage). Si l'automatisation reste bloquée, [déployez la Lambda à la main](#repli--déployer-la-lambda-à-la-main) : le plugin reprend ensuite la fonction existante sans doublon.

## 2. Créer ou mettre à jour le skill

Cliquez **Créer / Mettre à jour le skill**.

![Fenêtre de déploiement : étapes 2 et 3](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/03-modale-deploiement-etapes.jpg)

| Étape affichée | Ce qui se passe |
|---|---|
| **Skill créé** ou **Fiche du skill envoyée** | Le plugin cherche d'abord un skill nommé `jee2alexa` (ou l'ancien nom *AlexaPremium SmartHome*) : s'il existe, il met sa fiche à jour ; sinon il le **crée**. Pas de doublon possible en cas d'erreur passagère. L'identifiant (`amzn1.ask.skill.…`) est mémorisé |
| **Fiche validée par Amazon** | Attend la fin du traitement asynchrone (`SUCCEEDED`). La fiche contient l'ARN de la Lambda, le nom, les textes fr-FR et en-US, les icônes |
| **Account Linking** | **À la création** : publie les URLs OAuth de votre Jeedom (autorisation, jeton), l'identifiant et le secret client, puis **relit** ce qu'Amazon a enregistré pour vérifier |
| **Account Linking (relu chez Amazon)** | **Skill existant** : relit l'Account Linking et le compare à cette box (identifiant client, URLs). En cas d'écart (réinstallation, nouvelle adresse…), la ligne est en avertissement et la fenêtre **propose de le republier** — jamais sans votre accord, car republier délie les comptes. Le secret n'est pas renvoyé par Amazon : un secret changé sans republication ne se détecte pas ici |
| **Permission Lambda limitée au skill** | Remplace l'autorisation « ouverte » de l'étape 1 par une autorisation liée à **l'identifiant de votre skill** : seul votre skill peut invoquer la Lambda. Faite **avant** l'envoi de la fiche d'un skill existant (Amazon la refuse si la Lambda n'accepte pas ce skill) et **juste après** la création d'un nouveau skill (l'autorisation ouverte est alors assurée avant la création). Idempotent |

Le panneau de résultat annonce **« Skill créé — reliez le compte depuis l'app Alexa »** à la première création, **« Fiche du skill mise à jour »** ensuite.

> L'**Account Linking** n'est volontairement **pas** republié lors d'une mise à jour : le republier invalide les comptes déjà liés. Utilisez le bouton orange *Republier l'Account Linking* uniquement si les URLs ont changé (voir plus bas).

## 3. Activer le skill dans l'app Alexa

Cette étape se fait **sur votre téléphone**, avec le **compte Amazon développeur** (voir [prérequis](#2-un-compte-amazon-le-même-partout)).

1. App Alexa → **Plus** → **Skills et jeux** → **Vos skills** → onglet **Développement**.
2. Ouvrez **jee2alexa** et appuyez sur **Activer pour utiliser**.
3. Amazon ouvre la page de **connexion à votre Jeedom** (hébergée par le plugin, `oauth/authorize.php`) : connectez-vous avec un compte **administrateur Jeedom**, puis acceptez la page de **consentement**.
4. Le compte est relié. Alexa envoie alors une demande `AcceptGrant` à la Lambda, qui la transmet à Jeedom (utile au [push proactif](#push-proactif-états-à-jour-sonnette)).

Seules les adresses de redirection Amazon officielles (`layla.amazon.com`, `pitangui.amazon.com`, `alexa.amazon.co.jp`) sont acceptées par la page d'autorisation.

## 4. Découvrir les équipements

1. Dans la tuile **Équipements SmartHome**, cochez les équipements à exposer et **Sauvegardez** (voir [Équipements et scènes](#équipements-et-scènes)).
2. Cliquez **Découvrir via Alexa** (équivalent de « Alexa, découvre les appareils »), ou prononcez la phrase à voix haute.
3. Dans l'app Alexa → **Appareils**, les équipements apparaissent après quelques secondes.

> Le bouton **Découvrir via Alexa** ne confirme rien dans l'interface : vérifiez dans l'app Alexa, ou lancez le diagnostic ci-dessous.

Essayez ensuite : « Alexa, allume la lumière du salon ».

## 5. Vérifier avec la fenêtre Santé

La tuile **Santé** donne l'état de la configuration, puis **Lancer un diagnostic** teste réellement la chaîne.

![Fenêtre Santé après diagnostic](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/07-sante-diagnostic.jpg)

| Test | Signification |
|---|---|
| **Connexion AWS** | Les identifiants fonctionnent (appel réel) |
| **Fonction Lambda déployée (vérifié côté AWS)** | La fonction existe et est `Active` |
| **Event Gateway (push proactif)** | *Non configuré* tant que le push n'est pas mis en place |
| **Discovery (équipements valides)** | Nombre d'équipements que le proxy saurait annoncer à Alexa |

« 0 équipement(s) » signifie qu'aucun équipement n'est coché, ou qu'aucun n'est pris en charge.

## 6. L'équipement « Alexa SmartHome (AWS) »

Dès que la Lambda **et** le skill existent, le plugin crée un équipement Jeedom unique, **Alexa SmartHome (AWS)**, visible sur la page du plugin (section *État et consommation AWS*). Placez-le dans une pièce pour l'afficher sur le dashboard. Il est mis à jour chaque heure :

| Commande | Contenu |
|---|---|
| État de la Lambda | `Active`, `Suspendue` ou `Absente` (supprimée côté AWS) |
| Appels du mois, durée moyenne, erreurs, appels refusés | Mesures AWS de la Lambda depuis le 1er du mois (UTC) |
| Limite gratuite consommée / projetée | Part de la limite gratuite Lambda (%), réelle et estimée en fin de mois (après 3 jours) |
| Coût Lambda estimé / projeté | Estimation aux tarifs publics (USD), 0 tant que la limite gratuite n'est pas dépassée |
| Taille des journaux | Journaux CloudWatch de la Lambda (Ko) |
| Dépense AWS réelle / prévue | Montant facturé sur **tout** le compte AWS, lu dans le budget AWS (relu toutes les 6 h). Masquées tant qu'aucun budget n'existe |
| Équipements exposés, Push proactif actif | Nombre d'équipements cochés ; push d'état vers Alexa en service |
| Rafraîchir, Suspendre la Lambda, Réactiver la Lambda | Mise à jour immédiate ; suspension (avec confirmation) et réactivation, utilisables aussi dans un scénario |

Les commandes numériques principales sont historisées. Si vous supprimez l'équipement, il n'est recréé qu'au prochain déploiement.

## Changer d'adresse ou mettre à jour

| Situation | À faire |
|---|---|
| Mise à jour du plugin (nouveau code Lambda) | Bouton **1**. L'ARN ne change pas |
| Modification du nom, des textes ou des icônes du skill | Bouton **2** (fiche seule ; l'Account Linking n'est pas touché) |
| **Nouvelle adresse externe de Jeedom** | Bouton **1** (l'URL est écrite dans la Lambda), puis **Republier l'Account Linking**, puis **désactiver/réactiver le skill** dans l'app Alexa pour relier à nouveau le compte |
| **Réinstallation de Jeedom sans sauvegarde** | Même compte AWS, même session `alexaapiv2` : bouton **1** retrouve la Lambda (`premiumSmartHome`) et le rôle, bouton **2** retrouve le skill par son nom puis signale que l'Account Linking ne correspond plus (nouveau client OAuth) et propose de le republier ; acceptez, puis **désactivez/réactivez le skill** dans l'app Alexa |
| Changement de région AWS | Une nouvelle Lambda est créée dans la nouvelle région : relancez **1** puis **2** |
| Reconnexion AWS (session de 12 h expirée) | « Se connecter à AWS » dans la configuration, puis relancez l'étape voulue |

### Suspendre ou réactiver la Lambda

AWS n'a pas d'interrupteur « désactiver » pour une Lambda. Le bouton **Suspendre la Lambda** (sous le bouton 1, affiché une fois la Lambda déployée) règle sa **concurrence réservée à 0** : AWS refuse alors tous les appels et Alexa répond que l'appareil ne répond pas. Rien n'est supprimé : ARN, code, autorisation d'Alexa, skill et liaison du compte restent en place, et **Réactiver la Lambda** rétablit le contrôle vocal aussitôt. La ligne *État côté AWS* indique *Active* ou *Suspendue*.

Un redéploiement du code (bouton 1) **ne réactive pas** une Lambda suspendue : le déploiement le signale par une ligne *Lambda suspendue* en avertissement. La fenêtre **Santé** l'affiche aussi.

### Republier l'Account Linking

Le bouton orange demande confirmation : Amazon **invalide les jetons déjà émis**, le contrôle vocal est coupé jusqu'à ce que le compte soit relié à nouveau (désactiver puis réactiver le skill dans l'app Alexa). À réserver aux changements d'adresse ou de secret OAuth.

## Retirer l'installation

Le plugin ne supprime pas vos ressources AWS ni votre skill. Pour tout retirer :

Pour seulement couper le contrôle vocal sans rien supprimer, utilisez **Suspendre la Lambda** (ci-dessus).

1. Désactivez le skill dans l'app Alexa.
2. Supprimez le skill dans la console développeur Alexa.
3. Dans la console AWS, supprimez la fonction `premiumSmartHome` puis le rôle `premiumSmartHome-lambda-role`.
4. Dans la configuration du plugin, cliquez **Se déconnecter** (supprime la session AWS locale).
