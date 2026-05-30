# Plugin Alexa Premium — Skills Alexa

Ce fichier regroupe le fonctionnement des skills liés au plugin : déploiement, sélection du skill actif, commandes vocales Jeedom et affichage JeeViewer.

---

## 1. Vue d'ensemble

Les skills Alexa Premium ajoutent des usages Alexa vers Jeedom.

Ils ne remplacent pas le plugin Alexa officiel pour exposer tous les équipements Jeedom à Alexa. Alexa Premium reste d'abord un plugin pour piloter Alexa depuis Jeedom.

| Skill / module | Rôle |
|---|---|
| Skill ASK Alexa Premium | Dialogue vocal entre Alexa et Jeedom |
| VoiceControl | Mode vocal direct basé sur les interactions Jeedom |
| VoiceQuery | Mode vocal IA via le plugin `ai_assistant` |
| JeeViewer | Affichage de Jeedom sur Echo Show, Fire TV ou écran Alexa compatible |

---

## 2. Déploiement automatique du Skill ASK

Depuis la page du plugin, l'écran **Déploiement Skill ASK** permet de créer ou mettre à jour le skill Alexa-hosted.

Le déploiement automatique :

- crée ou récupère le skill développeur ;
- pousse le code Lambda du skill ;
- injecte la configuration Jeedom dans le code du skill ;
- lance le build et le déploiement côté Amazon ;
- enregistre l'identifiant du skill actif dans Jeedom quand le déploiement est réussi.

Configuration injectée dans le skill :

| Clé | Utilisation |
|---|---|
| `JEEDOM_URL` | URL externe de Jeedom |
| `APIKEY` | clé API utilisée par le skill pour appeler Jeedom |
| `DEBUG` | niveau de logs côté Lambda |
| `VERIFY_SSL` | `True` si l'URL externe est en HTTPS, sinon `False` |
| `USE_LLM_VOICE` | `True` si le mode IA VoiceQuery est activé, sinon `False` |

Après modification du mode vocal IA ou de l'URL externe, relancez le déploiement pour mettre à jour la Lambda.

---

## 3. Sélection du skill actif

L'écran **Gérer les skills** liste les skills développeur du compte Amazon.

Il sert à choisir le Skill ASK utilisé par Jeedom. Le skill sélectionné est enregistré dans la configuration du plugin avec son identifiant Amazon.

Si le déploiement est réussi mais que le skill ne répond pas, vérifiez que le bon skill est marqué comme utilisé.

---

## 4. Fonctionnement du Skill ASK

Flux simplifié :

```text
Utilisateur Alexa
      ↓
Skill ASK Alexa Premium
      ↓
Lambda Alexa-hosted
      ↓
Endpoint Jeedom
      ↓
Réponse vocale Alexa
```

Le skill gère deux familles d'usages :

- les questions/réponses en attente depuis Jeedom, par exemple une confirmation vocale demandée par un scénario ;
- les commandes vocales directes vers Jeedom, via `voiceControl.php` ou `voiceQuery.php`.

Exemple :

```text
Alexa, ouvre Jeedom
Quelle est la température du thermostat ?
```

---

## 5. VoiceControl et VoiceQuery

| Mode | Endpoint Jeedom | Moteur | À utiliser quand |
|---|---|---|---|
| VoiceControl | `core/php/voiceControl.php` | interactions Jeedom (`interactQuery`) | vous voulez un comportement déterministe, sans IA |
| VoiceQuery | `core/php/voiceQuery.php` | plugin `ai_assistant` + contexte Jeedom | vous voulez des questions plus naturelles ou des actions pilotées par IA |

### VoiceControl

VoiceControl est le mode standard.

Il envoie la phrase reconnue par Alexa vers le moteur d'interactions Jeedom. Il peut proposer une désambiguïsation quand plusieurs commandes proches existent.

Si Alexa répond **Aucune commande trouvée pour cette demande**, cela signifie généralement que l'interaction Jeedom correspondante n'existe pas encore ou n'est pas assez précise.

### VoiceQuery

VoiceQuery utilise le plugin `ai_assistant`.

Il construit un contexte court de la maison Jeedom : objets, équipements visibles, commandes info/action, valeurs utiles. Ce contexte est envoyé au fournisseur IA configuré par défaut dans `ai_assistant`.

VoiceQuery peut :

- répondre à une question sur l'état Jeedom ;
- proposer ou exécuter une action Jeedom ;
- revenir automatiquement vers les interactions Jeedom si l'IA n'est pas disponible.

VoiceQuery dépend uniquement du plugin `ai_assistant`. Le libellé correct est **IA via ai_assistant**.

---

## 6. Assistant d'interactions vocales

L'assistant d'interactions prépare les phrases Jeedom utilisables par VoiceControl.

Il reste utile même si VoiceQuery est activé, car les interactions Jeedom servent de comportement de secours quand l'IA n'est pas disponible ou ne trouve pas de réponse fiable.

À régénérer après ajout ou renommage d'équipements et de commandes Jeedom.

---

## 7. Santé Skill ASK et logs

L'écran **Santé Skill ASK** aide à contrôler :

- l'identifiant du skill actif ;
- l'état du build ;
- l'état du déploiement ;
- l'accès aux logs CloudWatch.

Logs utiles côté Jeedom :

| Log | Usage |
|---|---|
| `alexaapiv2` | déploiement, configuration, erreurs générales |
| `alexaapiv2_push` | appels vocaux Jeedom, VoiceControl, VoiceQuery |

Repères de diagnostic :

| Symptôme | Piste |
|---|---|
| `Aucune commande trouvée` | interaction Jeedom absente ou trop imprécise |
| `Désolé, j'ai quelques problèmes` | erreur Lambda, URL externe, clé API ou configuration skill |
| `source=fallback` | VoiceQuery a basculé vers les interactions Jeedom |
| `source=llm_action` | une action Jeedom a été exécutée via VoiceQuery |

---

## 8. JeeViewer

JeeViewer est un skill séparé du Skill ASK.

Il sert à afficher Jeedom sur un Echo Show, Fire TV ou écran Alexa compatible. Il ne sert pas aux commandes VoiceControl ou VoiceQuery.

Son écran de déploiement injecte aussi l'URL Jeedom et la clé API nécessaires à l'affichage.

---

## 9. Alternative manuelle

Le déploiement automatique est recommandé.

La création manuelle reste documentée ici : [Skill ASK Amazon manuel](04-skill-ask.md).
