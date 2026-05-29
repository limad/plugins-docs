# Plugin Alexa Premium — Skill ASK, VoiceQuery et JeeViewer

---

## Sommaire

- [1. Présentation](#1-présentation)
- [2. Prérequis](#2-prérequis)
- [3. Déployer le Skill ASK depuis Jeedom](#3-déployer-le-skill-ask-depuis-jeedom)
- [4. Activer et tester le Skill](#4-activer-et-tester-le-skill)
- [5. VoiceQuery](#5-voicequery)
- [6. Assistant d'interactions vocales](#6-assistant-dinteractions-vocales)
- [7. JeeViewer](#7-jeeviewer)
- [8. Consulter les logs](#8-consulter-les-logs)
- [9. Exemples d'utilisation](#9-exemples-dutilisation)

---

## 1. Présentation

Le Skill ASK (Alexa Skills Kit) permet d'ajouter de l'**interactivité vocale conditionnelle** entre Alexa et Jeedom.

Sans Skill ASK, Alexa exécute des actions de façon silencieuse. Avec le Skill ASK, Alexa peut **poser des questions** et **attendre une réponse vocale** avant d'agir. Exemples :

- « Voulez-vous allumer la lumière du salon ? » → Oui / Non
- « Quelle température souhaitez-vous ? » → Réponse vocale → Action dans Jeedom
- « Confirmez-vous l'extinction de tous les volets ? » → Confirmation vocale requise

Le plugin sait maintenant déployer le Skill ASK directement depuis Jeedom. La création manuelle dans la console Amazon n'est plus la méthode recommandée.

---

## 2. Prérequis

- Un compte développeur Amazon connecté au **même compte Amazon** que vos appareils Alexa.
- Une URL externe Jeedom fonctionnelle, idéalement en HTTPS avec certificat valide.
- Le cookie Amazon du plugin Alexa Premium opérationnel.
- Une clé API plugin valide.

> Le Skill reste en mode **Development**. C'est suffisant pour une utilisation personnelle avec Jeedom.

---

## 3. Déployer le Skill ASK depuis Jeedom

Ouvrez la configuration du plugin Alexa Premium puis utilisez l'écran de **déploiement du Skill ASK**.

Le déploiement automatisé effectue les étapes suivantes :

1. recherche d'un Skill existant du même nom ;
2. création du Skill si nécessaire ;
3. synchronisation du code Lambda ;
4. injection de l'URL Jeedom et de la clé API ;
5. déploiement du code ;
6. import des modèles d'interaction ;
7. build du Skill ;
8. activation du Skill ;
9. enregistrement automatique de l'ID du Skill dans la configuration Jeedom.

L'opération peut prendre plusieurs minutes. Laissez la fenêtre de déploiement ouverte jusqu'au message de réussite.

> Si l'URL externe Jeedom est en HTTPS, le Skill vérifie le certificat SSL. Si elle est en HTTP, la vérification SSL est désactivée automatiquement.

---

## 4. Activer et tester le Skill

Après un déploiement réussi :

1. ouvrez l'application Alexa ;
2. allez dans **Skills et jeux** puis **Vos Skills** ;
3. cherchez les **Skills développeur** ;
4. vérifiez que le Skill Alexa Premium est activé ;
5. testez à la voix avec son nom d'invocation.

Exemple :

```
Alexa, ouvre Jeedom
```

Alexa doit répondre et attendre votre demande.

---

## 5. VoiceQuery

VoiceQuery permet de poser une question en langage naturel au Skill ASK, puis de laisser Jeedom chercher la commande ou l'interaction correspondante.

Exemples :

```
Quelle est la température du salon ?
Allume la lumière du séjour
Lance la routine cinéma
```

Important : VoiceQuery s'appuie sur les commandes et interactions visibles par Jeedom. Une réponse native Alexa peut donc fonctionner hors Skill, alors que la même phrase dans le Skill ASK peut répondre **Aucune commande trouvée** si l'équipement n'est pas visible côté Jeedom ou interactions.

---

## 6. Assistant d'interactions vocales

L'assistant d'interactions vocales aide à créer des phrases Jeedom exploitables par le Skill ASK.

Il peut notamment :

- détecter les commandes info utiles ;
- proposer des phrases pour les capteurs ;
- grouper les commandes par pièce ;
- créer ou régénérer les interactions nécessaires.

Après génération, testez toujours quelques phrases depuis l'application Alexa ou un Echo.

---

## 7. JeeViewer

JeeViewer est un Skill séparé permettant d'afficher Jeedom sur un appareil Alexa avec écran compatible : Echo Show, Fire TV ou écran Alexa équivalent.

Le déploiement se fait depuis l'écran dédié du plugin. Comme pour le Skill ASK, laissez le déploiement aller jusqu'au build final avant de tester.

---

## 8. Consulter les logs

En cas de problème, consultez :

- le log Jeedom du plugin Alexa Premium ;
- la fenêtre de progression du déploiement ;
- le panneau Santé Skill ;
- les logs CloudWatch du Skill dans la console ASK.

Les erreurs fréquentes sont :

- URL externe Jeedom inaccessible depuis Internet ;
- clé API invalide ;
- Skill non activé dans l'application Alexa ;
- build non terminé ;
- modèle vocal pas encore propagé par Amazon ;
- phrase captée par Alexa comme un autre intent que VoiceQuery.

---

## 9. Exemples d'utilisation

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
