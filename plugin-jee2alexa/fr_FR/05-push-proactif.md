# Push proactif (états à jour, sonnette)

## À quoi ça sert

Sans le push, Alexa ne connaît l'état d'un équipement **qu'en le redemandant** (`ReportState`) : si vous allumez une lampe depuis Jeedom, l'app Alexa peut afficher l'ancien état jusqu'à la prochaine interrogation.

Avec le **push proactif**, Jeedom prévient Alexa **dès qu'une commande d'état change** (`ChangeReport` vers l'Event Gateway d'Alexa). Il est **désactivé par défaut** et **facultatif** : le contrôle vocal fonctionne sans.

## Ce que le push apporte

- États toujours à jour dans l'app Alexa.
- **Annonce de sonnette** (voir plus bas).
- Les capteurs (ouverture, mouvement) ne peuvent déclencher des routines Alexa que s'ils rapportent leurs changements de façon proactive.

## Mise en place

Faites-la **après** avoir déployé et activé le skill ([déploiement](#déploiement-pas-à-pas)).

1. **Console développeur Amazon** → votre skill → onglet **Permissions** → activer **« Send Alexa Events »**.
2. Dans la configuration du plugin, bloc **Push proactif (ChangeReport)** :
   - cliquez **Récupérer depuis la console** pour remplir automatiquement **Alexa Client Id** et **Alexa Client Secret** (nécessite `alexaapiv2` et le skill déjà créé) ;
   - ou copiez-les à la main depuis la console. Ce **ne sont pas** les identifiants de l'Account Linking : ce sont ceux de la permission « Send Alexa Events ».
3. **Sauvegardez**.
4. **Reliez à nouveau le compte** : désactivez puis réactivez le skill dans l'app Alexa. À la liaison, Alexa envoie un `AcceptGrant` contenant un code ; Jeedom l'échange contre un jeton d'accès et un **jeton de renouvellement** (stocké chiffré). Sans cette étape, le push ne peut pas s'authentifier.
5. Cliquez **Tester le token Event Gateway** : le test doit réussir.

![Configuration : push proactif](https://limad.github.io/plugins-docs/plugin-jee2alexa/images/09-configuration-push.jpg)

La fenêtre **Santé** (diagnostic) affiche ensuite *Event Gateway (push proactif)* en **OK** au lieu de *Non configuré*.

## Fonctionnement

- À chaque **sauvegarde** de la sélection d'équipements, le plugin (re)crée un **écouteur Jeedom** sur chaque commande *état* des équipements exposés, et supprime les écouteurs devenus inutiles. Une tâche **horaire** refait cette synchronisation par sécurité.
- Les changements de plusieurs commandes d'un même équipement survenant ensemble (allumage + luminosité + couleur) sont **regroupés** en un seul `ChangeReport`.
- Quand le push est actif, la découverte annonce à Alexa que ces états lui sont **poussés** (`proactivelyReported`). C'est indispensable : Alexa répond désormais aux questions (« quelle température dans le salon ? ») et déclenche les routines **depuis son cache**, sans redemander l'état à Jeedom. Sans cette annonce, elle ignore les mises à jour et garde une valeur ancienne, parfois de plusieurs heures.
- Sont poussés : marche/arrêt, luminosité, température de couleur, thermostat (consigne, mode), serrure, volet, ouverture, mouvement, température, humidité, connectivité (commande `ONLINE`), ventilateur (vitesse, oscillation), consigne d'humidité, fil pilote, mode de ventilation de la clim, porte de garage, lecture, volume, chaîne, alarme.
- Pour ne pas saturer Alexa : une valeur identique n'est pas renvoyée, une variation de température de moins de 0,5 °C (2 % pour l'humidité) attend 15 min, au moins 2 s séparent deux envois d'un même équipement, 120 envois par heure au plus. Un mouvement n'est déclaré terminé que 30 s après avoir été détecté (règle Amazon).
- Si Alexa refuse le push (skill désactivé, permission « Send Alexa Events » retirée), le plugin l'arrête et l'écrit une fois dans le journal ; réactivez le skill pour le rétablir.

> **Après activation du push, ou après la mise à jour qui ajoute cette annonce : cliquez Découvrir via Alexa** (fenêtre **Équipements SmartHome**). Alexa ne relit l'annonce qu'à la découverte. Avec le push actif, ce bouton et chaque sauvegarde des équipements ou des scènes envoient directement à Alexa la description à jour des appareils (`AddOrUpdateReport`), sans découverte vocale. Un appareil **retiré** de la sélection reste dans Alexa jusqu'à une découverte vocale (« Alexa, découvre mes appareils ») ou sa suppression dans l'app.
- Le push est **au mieux** : en cas d'échec, il est écrit dans le journal et abandonné, sans jamais bloquer Jeedom.

### Passerelle utilisée

Le plugin choisit la passerelle d'après la **région AWS** configurée :

| Région AWS | Passerelle |
|---|---|
| `eu-*` | `api.eu.amazonalexa.com` (Europe) |
| toute autre | `api.amazonalexa.com` (Amérique du Nord) |

> Limite connue : la passerelle **Extrême-Orient** (`api.fe.amazonalexa.com`) n'est pas utilisée, y compris pour `us-west-2`. Le push proactif n'est donc fiable que pour l'Europe et l'Amérique du Nord.

## Sonnette

1. Choisissez la catégorie **DOORBELL** (« Sonnette ») pour l'équipement dans **Équipements SmartHome**.
2. L'équipement doit avoir une commande de type générique **Bouton** (`BUTTON`) : chaque passage à une valeur non nulle est annoncé à Alexa comme un appui de sonnette (`DoorbellEventSource`).
3. Activez le push proactif (ci-dessus) : l'annonce est un événement proactif. Sur les Echo, vérifiez que les **annonces** sont autorisées et que **Ne pas déranger** est désactivé.
4. Dans l'app Alexa, sur l'appareil sonnette : activez les **annonces de sonnette** et choisissez les Echo qui les diffusent.
5. Si rien n'est annoncé alors que tout ce qui précède est en place : **désactivez puis réactivez le skill** dans l'app Alexa, relancez une découverte et refaites l'étape 4. Constaté le 2026-11-05 : Amazon acceptait les appuis (HTTP 204) sans aucune annonce jusqu'à cette réactivation.
