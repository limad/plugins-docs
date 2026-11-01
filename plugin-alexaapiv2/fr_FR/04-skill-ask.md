# Plugin Alexa Premium — Créer un Skill ASK Amazon

> Cette page décrit l'**alternative manuelle**. La méthode recommandée reste le déploiement automatisé depuis la configuration du plugin Alexa Premium.

---

## Sommaire

- [1. Présentation](#1-présentation)
- [2. Créer un compte développeur Amazon](#2-créer-un-compte-développeur-amazon)
- [3. Créer le Skill par import GitHub](#3-créer-le-skill-par-import-github)
- [4. Configurer les paramètres Jeedom](#4-configurer-les-paramètres-jeedom)
- [5. Activer le Skill](#5-activer-le-skill)
- [6. Renseigner l'ID du Skill dans le plugin](#6-renseigner-lid-du-skill-dans-le-plugin)
- [7. Consulter les logs du Skill](#7-consulter-les-logs-du-skill)
- [8. Exemples d'utilisation](#8-exemples-dutilisation)

---

## 1. Présentation

Le Skill ASK (Alexa Skills Kit) permet d'ajouter de l'**interactivité vocale conditionnelle** entre Alexa et Jeedom.

Sans Skill ASK, Alexa exécute des actions de façon silencieuse. Avec le Skill ASK, Alexa peut **poser des questions** et **attendre une réponse vocale** avant d'agir. Exemples :

- « Voulez-vous allumer la lumière du salon ? » → Oui / Non
- « Quelle température souhaitez-vous ? » → Réponse vocale → Action dans Jeedom
- « Confirmez-vous l'extinction de tous les volets ? » → Confirmation vocale requise

> Le Skill doit être **appelé par le plugin Alexa Premium** pour fonctionner correctement — il ne fonctionne pas de façon autonome.

---

## 2. Créer un compte développeur Amazon

Rendez-vous sur [developer.amazon.com](https://developer.amazon.com/) et connectez-vous avec le **même compte Amazon** que celui utilisé pour vos appareils Alexa.

---

## 3. Créer le Skill par import GitHub

Accédez à la console ASK : [developer.amazon.com/alexa/console/ask](https://developer.amazon.com/alexa/console/ask)

![Console ASK — écran d'accueil](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/0.png)

Cliquez sur **Créer un Skill** :

![Bouton Créer un Skill](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/1.png)

Renseignez les informations du Skill :
- **Nom** : `ask Jeedom` (au choix : c'est le nom affiché dans l'application Alexa)
- **Langue** : Français (FR)

Cliquez sur **Next** en haut à droite.

![Nom et langue du Skill](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/2.png)

Sélectionnez les options suivantes :

![Sélection type Other et Custom](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/3.png)

![Sélection hébergement et région](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/4.png)

- **Type d'expérience** → `Other`
- **Modèle** → `Custom` avec **Sync Locales** activé
- **Service d'hébergement** → `Alexa-hosted (Python)`
- **Région d'hébergement** → `EU (Ireland)`

Cliquez sur **Next**, choisissez **Import skill** (et non *Start from Scratch*), puis renseignez l'URL GitHub :

```
https://github.com/limad/alexaSkill_premiumAsk.git
```

Validez et **patientez** environ 3 minutes. Amazon crée le Skill et importe tout depuis GitHub : le code, la fiche, les icônes et le modèle vocal de la langue choisie (Français (FR), avec Français (CA) recopié automatiquement), puis le compile. Il n'y a **rien à coller** dans le *JSON Editor*.

### Nom d'invocation

Le nom d'invocation importé est :

```
ma jeebox
```

C'est ce que vous direz à Alexa : *« Alexa, demande à ma jeebox… »*. Pour le changer : onglet **Build** → **Invocations** → **Skill Invocation Name**, puis **Save** et **Build skill**.

---

## 4. Configurer les paramètres Jeedom

Ouvrez l'onglet **Code**, puis le fichier **`lambda/config.py`**, et renseignez les paramètres suivants :

![Configuration lambda/config.py](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.1.png)

| Paramètre | Description |
|---|---|
| `JEEDOM_URL` | URL externe de votre Jeedom (ex. : `"https://votre-jeedom.fr"`) |
| `JEEDOM_APIKEY` | Clé API du plugin Alexa Premium (jamais celle du core Jeedom) |
| `VERIFY_SSL` | `True` si votre URL externe est en HTTPS avec certificat valide, sinon `False` |
| `DEBUG` | `False` en usage normal ; `True` pour des journaux détaillés |

Les textes s'écrivent entre guillemets, `True` / `False` sans guillemets.

**Où trouver ces paramètres ?**
Dans la configuration du plugin Alexa Premium, cliquez sur le bouton **Params** :

![Bouton Params dans la configuration](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.1.png)

Une fois les paramètres renseignés, cliquez sur **Save** puis sur **Deploy** (une vingtaine de secondes) :

![Boutons Save et Deploy](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.2.png)

---

## 5. Activer le Skill

1. Onglet **Test** de la console : passez de *Off* à **Development**.
2. Dans l'application **Alexa** sur votre smartphone, le Skill apparaît dans *Vos Skills* → *Skills développeur*.

> Le Skill restera en mode Development tant qu'il n'est pas soumis à la certification Amazon — ce mode est **suffisant** pour une utilisation personnelle avec Jeedom.

**Autre langue que le français** : l'import ne crée que la langue choisie à la création. Pour en ajouter une : onglet **Build** → **Language settings** → ajoutez-la, collez le fichier `skill-package/interactionModels/custom/<langue>.json` du dépôt GitHub dans le *JSON Editor*, puis **Build skill**.

---

## 6. Renseigner l'ID du Skill dans le plugin

La dernière étape consiste à informer le plugin Alexa Premium de l'ID de votre Skill.

**Où trouver l'ID du Skill ?**

- Depuis la page [developer.amazon.com/alexa/console/ask](https://developer.amazon.com/alexa/console/ask), cliquez sur **Copier l'identifiant de la Skill**
- Ou directement dans l'**URL** lorsque vous êtes sur la page du code du Skill

L'ID ressemble à :
```
amzn1.ask.skill.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Collez cet ID dans la page de **Configuration** du plugin Alexa Premium, dans le champ dédié au Skill ASK.

---

## 7. Consulter les logs du Skill

En cas de problème, les logs du Skill sont disponibles via :

```
Onglet Code → CloudWatch Logs
```

Les erreurs les plus fréquentes sont :
- `JEEDOM_URL` incorrecte ou inaccessible depuis Internet
- `JEEDOM_APIKEY` invalide (clé du plugin Alexa Premium)
- `VERIFY_SSL` à `True` avec un certificat HTTPS invalide
- Jeedom non accessible depuis Internet
- **Deploy** non fait après une modification de `config.py`, ou **Build skill** non fait après une modification du modèle vocal

---

## 8. Exemples d'utilisation

### Conditionner une action à une réponse vocale

Sans Skill ASK, un scénario allume la lumière automatiquement à une heure précise.  
**Avec Skill ASK**, Alexa peut d'abord vous demander confirmation :

```
Alexa : « Voulez-vous allumer la lumière du salon ? »
Vous  : « Oui »
→ Jeedom allume la lumière
```

### Utilisation dans un scénario Jeedom

La réponse à une question posée via **Ask** arrive de façon **asynchrone** (Alexa répond à Jeedom après coup). Elle n'est jamais stockée dans une variable de scénario, mais dans les commandes info de l'**équipement Echo qui a posé la question** :

- `Réponse à la question (Ask)` — la valeur lisible ("oui", "non", un nombre, du texte…)
- `Type de réponse (Ask)` — le type détecté (`yes`, `no`, `numeric`, `string`…)
- `Dialogue (Ask)` — événement combiné au format `question::réponse`, utilisable comme déclencheur

Il ne faut donc **jamais enchaîner directement** `Poser une question (Ask)` puis tester la réponse dans la même foulée : à cet instant elle est encore vide. Deux approches fonctionnent :

**Option A — un seul scénario avec un bloc Attente (Wait)**

```
Action    : #[Salon][Echo salon][Poser une question (Ask)]# → "Confirmez-vous l'extinction de tous les volets ?"
Attente   : #[Salon][Echo salon][Réponse à la question (Ask)]# != ""   (timeout 60s)
SI #[Salon][Echo salon][Réponse à la question (Ask)]# = "oui"
ALORS
  Fermer tous les volets
```

Le bloc **Attente** (natif Jeedom) patiente jusqu'à ce que la réponse soit non vide ou que le délai expire. Simple, mais le scénario reste bloqué pendant l'attente.

**Option B — deux scénarios, déclenchés par l'événement**

```
Scénario "Question" :
  Action : #[Salon][Echo salon][Poser une question (Ask)]# → "Confirmez-vous l'extinction de tous les volets ?"

Scénario "Réponse" (déclenché par) : #[Salon][Echo salon][Dialogue (Ask)]#
  SI #[Salon][Echo salon][Type de réponse (Ask)]# = "yes"
  ALORS
    Fermer tous les volets
```

Plus robuste (pas de blocage, pas de timeout à régler), au prix de deux scénarios à maintenir.

> Dans les deux cas, remplacez `[Salon][Echo salon]` par le nom réel de votre équipement Echo — la commande n'existe que si l'équipement a la capability nécessaire (visible après un scan).

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)
