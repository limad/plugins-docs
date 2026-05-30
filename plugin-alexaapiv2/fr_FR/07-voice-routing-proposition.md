# Alexa Premium — Routage VoiceControl / VoiceQuery

Note technique sur le fonctionnement actuel du Skill ASK et sur une proposition d'amélioration pour déplacer l'arbitrage côté Jeedom.

---

## 1. Fonctionnement actuel

Le Skill ASK peut envoyer une commande vocale directe vers Jeedom.

Aujourd'hui, le choix entre `voiceControl.php` et `voiceQuery.php` est fait dans la Lambda Alexa-hosted, via la variable `USE_LLM_VOICE`.

### Flow actuel

```text
Utilisateur
  ↓
Alexa Skill ASK
  ↓
Lambda premiumAsk
  ↓
Lecture de USE_LLM_VOICE dans config.py / user_config.py
  ↓
Si USE_LLM_VOICE = True
  → POST /plugins/alexaapiv2/core/php/voiceQuery.php
Sinon
  → POST /plugins/alexaapiv2/core/php/voiceControl.php
```

### Source de la décision

La case Jeedom `alexavoice_llm_voice` est lue uniquement pendant le déploiement du skill.

Au déploiement, Jeedom écrit dans la configuration Lambda :

```python
USE_LLM_VOICE = True
```

ou :

```python
USE_LLM_VOICE = False
```

Conséquence : si l'utilisateur change la case dans Jeedom, la Lambda ne le sait pas tant que le Skill ASK n'est pas redéployé.

### Comportement des deux endpoints

| Endpoint | Rôle | Moteur |
|---|---|---|
| `voiceControl.php` | commande vocale directe déterministe | interactions Jeedom |
| `voiceQuery.php` | commande/question vocale en langage naturel | `ai_assistant` + contexte Jeedom |

`voiceControl.php` gère aussi la désambiguïsation avec `forceExact`.

`voiceQuery.php` peut revenir vers les interactions Jeedom si l'IA est indisponible ou ne produit pas de réponse exploitable.

---

## 2. Limites du fonctionnement actuel

### Redéploiement obligatoire

Changer le mode VoiceQuery dans Jeedom ne modifie pas immédiatement le comportement du skill.

Le redéploiement est nécessaire car la décision est figée dans le fichier de configuration Lambda.

### Logique métier côté Lambda

La Lambda ne devrait idéalement pas décider du moteur Jeedom à utiliser.

Elle devrait rester un pont Alexa → Jeedom :

- réception de l'intention Alexa ;
- extraction de la phrase utilisateur ;
- appel HTTP vers Jeedom ;
- formatage de la réponse vocale.

### Configuration éclatée

La configuration visible dans Jeedom donne l'impression que le changement est immédiat, mais l'état réellement utilisé est dans la Lambda déployée.

Cela peut créer une incohérence :

```text
Jeedom affiche VoiceQuery activé
Lambda encore déployée avec USE_LLM_VOICE = False
Résultat réel : voiceControl.php
```

---

## 3. Proposition d'amélioration

Créer un routeur vocal côté Jeedom.

Nom possible :

```text
core/php/voiceRouter.php
```

La Lambda n'appellerait plus directement `voiceControl.php` ou `voiceQuery.php`.

Elle appellerait toujours un endpoint unique :

```text
POST /plugins/alexaapiv2/core/php/voiceRouter.php
```

### Flow proposé

```text
Utilisateur
  ↓
Alexa Skill ASK
  ↓
Lambda premiumAsk
  ↓
POST /plugins/alexaapiv2/core/php/voiceRouter.php
  ↓
Jeedom lit config::byKey('alexavoice_llm_voice', 'alexaapiv2', 0)
  ↓
Si forceExact = true
  → voiceControl
Sinon si alexavoice_llm_voice = 1
  → voiceQuery
Sinon
  → voiceControl
```

### Règle de routage recommandée

```text
forceExact = true
  voiceControl

sinon si alexavoice_llm_voice = 1
  voiceQuery

sinon
  voiceControl
```

Justification :

- `forceExact` correspond à un choix utilisateur après désambiguïsation ;
- la désambiguïsation est portée par `voiceControl.php` ;
- VoiceQuery reste le mode IA normal quand il est activé ;
- VoiceControl reste le mode stable par défaut.

---

## 4. Impact sur la Lambda

La Lambda n'aurait plus besoin de choisir le moteur vocal.

Avant :

```python
if USE_LLM_VOICE:
    return self._post_voice_query(query.strip())

return self._post_voice_control(query.strip(), force_exact)
```

Après :

```python
return self._post_voice_router(query.strip(), force_exact)
```

`USE_LLM_VOICE` pourrait être supprimé de la configuration Lambda après migration.

Un redéploiement resterait nécessaire une seule fois pour installer cette nouvelle logique Lambda.

Après ce redéploiement, changer la case Jeedom serait immédiat.

---

## 5. Impact côté Jeedom

Le routeur doit :

- vérifier la clé API comme les endpoints actuels ;
- lire le JSON reçu ;
- conserver les champs actuels : `query`, `deviceSerialNumber`, `personId`, `forceExact` ;
- lire `alexavoice_llm_voice` côté Jeedom ;
- router vers le bon moteur ;
- retourner une réponse JSON compatible avec la Lambda actuelle.

Format attendu en sortie :

```json
{
  "reply": "Réponse vocale",
  "matched": true,
  "ambiguous": false,
  "options": [],
  "http_error": false
}
```

Le routeur doit aussi ajouter un champ de diagnostic non bloquant :

```json
{
  "route": "voiceControl"
}
```

ou :

```json
{
  "route": "voiceQuery"
}
```

---

## 6. Logs recommandés

Log Jeedom dans `alexaapiv2_push` :

```text
voiceRouter: route=voiceControl query="..."
voiceRouter: route=voiceQuery query="..."
voiceRouter: forceExact=1 → voiceControl
voiceRouter: voiceQuery disabled → voiceControl
```

Ces logs permettent de vérifier immédiatement le moteur réellement utilisé, sans aller dans CloudWatch.

---

## 7. Migration progressive

Étape 1 : créer `voiceRouter.php`.

Étape 2 : ajouter un test manuel HTTP local :

```text
POST /plugins/alexaapiv2/core/php/voiceRouter.php?apikey=...
```

Étape 3 : modifier la Lambda pour appeler uniquement `voiceRouter.php`.

Étape 4 : redéployer le Skill ASK une seule fois.

Étape 5 : vérifier que la case `alexavoice_llm_voice` agit sans redéploiement.

Étape 6 : mettre à jour la documentation utilisateur.

---

## 8. Bénéfices

- changement VoiceControl / VoiceQuery immédiat depuis Jeedom ;
- suppression du redéploiement pour un simple changement de mode ;
- logique métier centralisée côté plugin ;
- logs Jeedom plus lisibles ;
- Lambda plus simple et plus stable ;
- compatibilité conservée avec `forceExact` et la désambiguïsation.

---

## 9. Point d'attention

`voiceQuery.php` utilise `ai_assistant`.

Si `ai_assistant` est absent, désactivé ou mal configuré, le routeur ne doit pas échouer brutalement. Il doit laisser `voiceQuery.php` appliquer son fallback ou router directement vers `voiceControl.php` si un contrôle préalable est ajouté.

Le comportement le plus simple reste :

```text
routeur → voiceQuery.php
voiceQuery.php → fallback interactions si IA KO
```

Cela évite de dupliquer la logique de fallback dans le routeur.
