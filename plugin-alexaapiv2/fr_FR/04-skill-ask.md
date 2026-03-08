# Plugin Alexa Premium — Créer un Skill ASK Amazon

---

## Sommaire

- [1. Présentation](#1-présentation)
- [2. Créer un compte développeur Amazon](#2-créer-un-compte-développeur-amazon)
- [3. Créer le Skill](#3-créer-le-skill)
- [4. Importer le code](#4-importer-le-code)
- [5. Configurer les paramètres Jeedom](#5-configurer-les-paramètres-jeedom)
- [6. Importer le modèle d'interaction](#6-importer-le-modèle-dinteraction)
- [7. Déployer et activer le Skill](#7-déployer-et-activer-le-skill)
- [8. Renseigner l'ID du Skill dans le plugin](#8-renseigner-lid-du-skill-dans-le-plugin)
- [9. Consulter les logs du Skill](#9-consulter-les-logs-du-skill)
- [10. Exemples d'utilisation](#10-exemples-dutilisation)

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

## 3. Créer le Skill

Accédez à la console ASK : [developer.amazon.com/alexa/console/ask](https://developer.amazon.com/alexa/console/ask)

![Console ASK — écran d'accueil](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/0.png)

Cliquez sur **Créer un Skill** :

![Bouton Créer un Skill](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/1.png)

Renseignez les informations du Skill :
- **Nom** : `ask Jeedom`
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

Cliquez sur **Next** puis sélectionnez **Start from Scratch**.

![Sélection Start from Scratch](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/5.png)

### Nom d'invocation

Dans la section **Invocation Name**, saisissez le mot ou la phrase qui permettra d'activer votre Skill à la voix :

```
pose question
```

> Vous pouvez choisir un autre nom d'invocation, mais il doit être **simple et mémorisable**. C'est ce que vous direz à Alexa : *« Alexa, ouvre pose question »*.

Cliquez sur **Enregistrer**.

---

## 4. Importer le code

Cliquez sur l'onglet **Code** puis sur **Import skill** en haut à droite :

![Import du code](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.png)

Dans le formulaire qui s'ouvre, renseignez l'URL GitHub :

```
https://github.com/limad/alexaPremium_Skill_Ask.git
```

Validez et **patientez** pendant la génération du Skill (cela peut prendre plusieurs minutes).

---

## 5. Configurer les paramètres Jeedom

Une fois le code importé, ouvrez le fichier **`lambda_function.py`** dans l'onglet Code et renseignez les deux paramètres suivants :

![Configuration lambda_function.py](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.1.png)

| Paramètre | Description |
|---|---|
| `JEEDOM_URL` | URL externe de votre Jeedom, **avec `/` à la fin** (ex. : `https://votre-jeedom.fr/`) |
| `APIKEY` | Clé API du plugin Alexa Premium |
| `lang` | Langue souhaitée (ex. : `fr-FR`) |

**Où trouver ces paramètres ?**  
Dans la configuration du plugin Alexa Premium, cliquez sur le bouton **Params** :

![Bouton Params dans la configuration](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.1.png)

Une fois les paramètres renseignés, cliquez sur **Save** puis sur **Deploy** :

![Boutons Save et Deploy](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/ask/6.2.png)

---

## 6. Importer le modèle d'interaction

1. Cliquez sur **Interaction Model** dans le menu de gauche
2. Ouvrez **JSON Editor**
3. Importez le fichier `skill.json` fourni avec le code
4. Cliquez sur **Enregistrer**
5. Cliquez sur **Build Model** et attendez la fin de la compilation

> Le Build est indispensable — sans lui, le Skill ne fonctionnera pas même si le code est déployé.

---

## 7. Déployer et activer le Skill

Une fois le Build terminé :

1. **Activez le Skill** depuis l'application **Alexa** sur votre smartphone (section *Vos Skills* → *Skills développeur*)
2. Vérifiez que le Skill apparaît bien en mode **Development** dans la console ASK

> Le Skill restera en mode Development tant qu'il n'est pas soumis à la certification Amazon — ce mode est **suffisant** pour une utilisation personnelle avec Jeedom.

---

## 8. Renseigner l'ID du Skill dans le plugin

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

## 9. Consulter les logs du Skill

En cas de problème, les logs du Skill sont disponibles via :

```
Onglet Code → CloudWatch Logs
```

Les erreurs les plus fréquentes sont :
- `JEEDOM_URL` incorrecte (slash manquant, HTTP au lieu de HTTPS)
- `APIKEY` invalide ou expirée
- Jeedom non accessible depuis Internet
- Build non effectué après une modification du code

---

## 10. Exemples d'utilisation

### Conditionner une action à une réponse vocale

Sans Skill ASK, un scénario allume la lumière automatiquement à une heure précise.  
**Avec Skill ASK**, Alexa peut d'abord vous demander confirmation :

```
Alexa : « Voulez-vous allumer la lumière du salon ? »
Vous  : « Oui »
→ Jeedom allume la lumière
```

### Utilisation dans un scénario Jeedom

Dans votre scénario, utilisez la commande **QuestionAsk** d'AlexaSys pour poser une question et récupérer la réponse dans une variable :

```
[AlexaSys][QuestionAsk] → "Confirmez-vous l'extinction de tous les volets ?"

SI #variable(reponse)# = "oui"
ALORS
  Fermer tous les volets
```

> Cette approche permet de conditionner n'importe quelle action Jeedom à une **confirmation vocale**, rendant vos automatisations plus sûres et interactives.

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)
