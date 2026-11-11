# Sécurité

Le plugin ouvre Jeedom à Internet (une URL HTTPS) et vous donne un accès AWS : prenez dix minutes pour comprendre ce qui est exposé et comment le limiter.

## Ce qui est exposé

Trois adresses de votre Jeedom doivent être joignables depuis Internet :

| Adresse | Protégée par |
|---|---|
| `/plugins/jee2alexa/core/php/smartHomeProxy.php` | Un **jeton** signé (en-tête `Authorization: Bearer …`) ; refus sans jeton valide |
| `/plugins/jee2alexa/core/php/oauth/authorize.php` | Une **connexion Jeedom** (formulaire intégré) puis une page de **consentement** ; adresses de redirection limitées à Amazon |
| `/plugins/jee2alexa/core/php/oauth/token.php` | Un code d'autorisation à usage court (10 minutes) et le secret client |

Le proxy ne manipule que des commandes **présentes dans la liste que vous avez cochée** : une directive qui référencerait un équipement non exposé est rejetée.

## Les jetons OAuth : à traiter comme un mot de passe

Le jeton d'accès émis à Alexa est de la forme `base64(clé_API:expiration).signature` (HMAC-SHA256, signé avec le secret client OAuth) :

- il est **valable 1 an** (renouvelé par un jeton de renouvellement) ;
- il **embarque la clé API Jeedom du plugin sous forme encodée (base64), non chiffrée** : quiconque détient un jeton peut en extraire la clé. **Ne le publiez jamais** (captures d'écran, journaux, tickets de support).

Pour cette raison, la Lambda **masque** les champs `token` **et** `code` (code d'autorisation de l'`AcceptGrant`) avant d'écrire une directive dans ses journaux CloudWatch. **Redéployez la Lambda** (bouton 1) après chaque mise à jour du plugin pour disposer de ce masquage.

Le secret client OAuth et l'identifiant sont générés automatiquement au premier usage (aléatoires, 32 octets). Ils sont visibles dans la configuration du plugin, section **Client OAuth (liaison du compte Alexa)** : le secret est masqué, affichable et copiable. **Modifier** permet de restaurer un ancien couple (après une réinstallation) ; la saisie est validée (identifiant de 16 caractères minimum, secret de 32 caractères minimum avec au moins 10 caractères différents) et demande une confirmation. Ne partagez jamais ce secret avec une box qui n'est pas à vous. Le secret est **chiffré** dans la base Jeedom (comme le secret AWS, le secret de l'Event Gateway et le jeton de renouvellement).

### Révoquer l'accès d'Alexa

- **Désactiver le skill** dans l'app Alexa coupe l'usage côté Alexa.
- Pour **invalider tous les jetons déjà émis** (fuite suspectée), il faut changer le **secret client OAuth** du plugin (bouton **Régénérer** de la section Client OAuth), puis **Republier l'Account Linking** (proposé juste après) et relier à nouveau le compte. Cette opération est sensible : elle coupe le contrôle vocal le temps de la reliaison.

## AWS : droits minimaux

| Bonne pratique | Pourquoi |
|---|---|
| **Éviter le compte root** | Le compte root ne peut pas être restreint par une politique IAM. Si la session est compromise, tout le compte AWS l'est (facturation, suppression…). Le plugin affiche un badge « compte root : déconseillé » |
| Utiliser un **utilisateur IAM dédié** avec la politique gérée `SignInLocalDevelopmentAccess` + des droits IAM/Lambda limités | Voir [Configuration AWS](configuration-aws.html) |
| Préférer le **mode « Se connecter à AWS »** | Identifiants **temporaires** : 15 minutes, renouvelés automatiquement, 12 h de session maximum. Aucune clé permanente à protéger. La session est chiffrée dans la base Jeedom |
| Dans le mode clés, révoquer la clé au moindre doute | La clé ne sert qu'au déploiement, jamais au fonctionnement vocal |

### Permission d'invocation limitée à votre skill

À l'étape 1, la Lambda reçoit une autorisation « ouverte » pour `alexa-connectedhome.amazon.com`, sans condition. Tant qu'elle reste ouverte, **n'importe quel skill Smart Home** connaissant l'ARN pourrait invoquer votre fonction.

À l'étape 2, le plugin **ajoute une autorisation liée à l'identifiant de votre skill** (`EventSourceToken`) **puis retire l'ouverte**, sans interruption. Relancer l'étape 2 ne change rien d'autre (opération idempotente). Une autorisation liée à un *autre* skill n'est jamais supprimée, sauf celle que le plugin avait créée pour un ancien skill (même nom d'autorisation), remplacée par celle du skill actuel.

Si le skill doit être **recréé** (supprimé dans la console Amazon), le plugin rouvre l'autorisation le temps de la création, car Amazon vérifie que la Lambda accepte le nouveau skill avant de valider sa fiche, puis la limite de nouveau au nouvel identifiant. Le redéploiement de la Lambda applique la même règle (limitée au skill connu, sinon ouverte).

### Journaux CloudWatch

La Lambda écrit dans le groupe `/aws/lambda/premiumSmartHome`. Par défaut AWS **conserve les journaux sans limite de durée** : réglez une **rétention** (par exemple 14 ou 30 jours) dans la console CloudWatch. Les jetons et codes y sont masqués, mais les noms d'appareils et les actions y figurent.

## Équipements sensibles

Soyez prudent avec les **serrures**, l'**alarme** et les **portes de garage** : une phrase prononcée près d'un Echo (ou entendue à travers une porte) peut suffire.

- Le plugin ne gère **pas** de code PIN vocal : l'alarme ne propose que `ARMED_AWAY` / `DISARMED`, et la serrure verrouille/déverrouille sans code côté plugin.
- Alexa peut de son côté exiger un **code vocal** pour déverrouiller (réglage de l'app Alexa).
- Si vous exposez ces équipements, limitez-le à ce que vous êtes prêt à voir déclencher à la voix.

## Liste de contrôle

- [ ] Certificat HTTPS valide sur l'adresse externe
- [ ] Mode « Se connecter à AWS » avec un utilisateur IAM restreint (pas root)
- [ ] Étape 2 relancée : la permission de la Lambda est limitée à votre skill
- [ ] Lambda redéployée depuis la dernière version du plugin (masquage des journaux)
- [ ] Rétention des journaux CloudWatch réglée
- [ ] Seuls les équipements utiles sont cochés
- [ ] Aucun jeton, clé ou identifiant de compte dans vos captures et tickets
