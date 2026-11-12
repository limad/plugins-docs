---
layout: default
title: ai_assistant — Démarrage rapide
lang: fr_FR
pluginId: ai_assistant
---

# 🚀 Démarrage rapide

Le plugin a beaucoup d'options, mais **vous n'en avez besoin d'aucune pour commencer**. Cette page vous emmène, en deux étapes, d'un plugin tout juste installé à un assistant qui pilote votre maison. Le reste viendra plus tard.

---

## Comprendre en 30 secondes

Trois pièces travaillent ensemble :

| Pièce | Son rôle | Obligatoire ? |
|---|---|---|
| **Un fournisseur d'IA** (Mistral, Gemini, OpenAI, Claude, Ollama…) | Le « cerveau » qui comprend vos phrases et rédige les réponses | Oui |
| **Ai Assistant** (ce plugin) | Le lien entre vous, le cerveau et Jeedom : panel, mini-chat, application mobile | Oui |
| **MCP AI Server** (plugin `mcp_jeedom`) | Les « mains » : il donne à l'IA les outils pour lire vos capteurs et commander vos équipements | Conseillé |

Sans **MCP AI Server**, l'IA discute mais ne voit pas votre maison.

---

## Étape 1 : discuter avec l'IA (5 minutes)

### 1. Obtenir une clé API

Une clé API est un mot de passe qui permet au plugin d'utiliser le service d'IA. Créez un compte chez un fournisseur et générez une clé :

- **Gemini** (badge « Gratuit ») ou **Mistral** proposent une offre gratuite, limitée en volume, suffisante pour tester.
- **Ollama** tourne sur votre propre machine et ne demande aucune clé (mais un ordinateur assez puissant).

La création guidée affiche, sous le champ de la clé, le lien vers la page où la créer. Si vous avez déjà saisi la clé dans la configuration du plugin, laissez le champ vide : elle sera reprise.

### 2. Lancer la création guidée

Dans **Plugins → Communication → Ai Assistant**, cliquez sur **Création guidée** (l'icône baguette magique en haut de la page). Elle s'ouvre toute seule si vous n'avez encore aucun équipement. Elle pose trois questions :

1. **Fournisseur IA** : cliquez sur son logo et collez votre clé API.
2. **Configuration** : donnez un nom, puis choisissez le type :
   - **Assistant Jeedom** (recommandé) : il connaît vos pièces et vos équipements, et ne touche qu'à ceux que vous autorisez ;
   - **Assistant général** : un chat libre, sans accès à la maison.

   Avec « Assistant Jeedom », elle indique si **MCP AI Server** est installé, et sinon conseille de l'installer (voir l'étape 2). Laissez le **Modèle** vide : le plugin prend le modèle par défaut du fournisseur.
3. **Création** : vérifiez le résumé et cliquez sur **Créer l'assistant**.

Elle teste aussitôt la connexion au fournisseur : ✅ la clé fonctionne, ❌ elle est refusée (corrigez-la dans la fiche du fournisseur).

Avec « Assistant Jeedom », elle crée **deux équipements** : le fournisseur (qui porte la clé) et l'assistant qui lui est lié. C'est normal.

### 3. Poser une première question

Cliquez sur **Essayer maintenant** à la fin de la création guidée : le panel s'ouvre sur votre nouvel assistant. Ensuite, deux façons de discuter :

- **Le panel** : menu **Accueil → Ai Assistant**. C'est l'interface complète (historique, pièces jointes, micro).
- **Le mini-chat** : une icône dans la barre du haut de Jeedom, pour une question rapide. Activez-la dans **Configuration du plugin → Navbar → Activer**, puis rechargez la page.

Demandez par exemple : *« Bonjour, que sais-tu faire ? »*. Si l'IA répond, l'étape 1 est réussie.

---

## Étape 2 : piloter la maison (10 minutes)

### 1. Installer MCP AI Server

Installez le plugin **MCP AI Server** (`mcp_jeedom`) depuis le Market, activez-le, installez ses dépendances et vérifiez que son démon est **OK**.

### 2. Choisir ce que l'IA a le droit de toucher

Dans **MCP AI Server**, ouvrez la **whitelist** (liste blanche) et cochez les équipements que l'IA peut voir et commander. **Rien n'est coché par défaut** : une IA sans équipement coché ne peut rien faire, c'est voulu.

Commencez petit : deux ou trois lumières et un capteur de température suffisent pour tester.

### 3. Relier les deux plugins

Il n'y a rien à faire dans la plupart des cas : dès que MCP AI Server est actif, Ai Assistant crée tout seul le serveur **« MCP Jeedom (local) »** et un équipement **« MCP Jeedom »** (client MCP) lié à votre fournisseur. Le jeton d'accès local est géré automatiquement.

Si l'équipement « MCP Jeedom » n'apparaît pas (MCP AI Server installé après coup, par exemple) :

- ouvrez votre équipement **fournisseur** et **sauvegardez-le** : la création se relance ;
- ou cliquez sur **Créer un Client MCP lié** dans la fiche du fournisseur.

### 4. Tester

Dans le panel, choisissez l'équipement « MCP Jeedom » (ou votre « Assistant Jeedom ») et demandez :

- *« Quelle est la température du salon ? »*
- *« Allume la lumière de la cuisine. »*

Si l'IA répond qu'elle ne trouve pas l'équipement, vérifiez d'abord qu'il est coché dans la whitelist.

---

## Ça ne marche pas ?

| Symptôme | Piste |
|---|---|
| Erreur 401 / « clé invalide » | Clé API mal copiée (espace en trop) ou clé désactivée chez le fournisseur. |
| Erreur 429 / quota | L'offre gratuite est épuisée pour la période : attendez ou changez de modèle. |
| L'IA ne connaît aucun équipement | Whitelist vide dans MCP AI Server. |
| Pas d'équipement « MCP Jeedom » | Démon de MCP AI Server arrêté, ou sauvegardez l'équipement fournisseur. |
| Pas d'icône dans la barre Jeedom | Navbar non activée dans la configuration du plugin, ou page non rechargée. |
| Le micro ne marche pas | Il faut du HTTPS : voir le [tuto micro](tuto_micro.md). |

Pour comprendre une erreur, ouvrez le log **ai_assistant** (Analyse → Logs) et joignez-le à votre question sur le [forum](https://community.jeedom.com/tag/plugin-ai_assistant).

---

## Et ensuite ?

Une fois l'étape 2 en place, explorez à votre rythme :

- **Application mobile (PWA)** : le panel sur votre téléphone, avec notifications.
- **Vigie** : un agent qui surveille la maison et vous prévient (piles faibles, équipements muets…).
- **Commandes rapides** : « allume la lumière du salon » exécuté sans appeler l'IA (gratuit et instantané).
- **Mode conversation** : parler à l'assistant et l'entendre répondre.

Tout est détaillé dans la [documentation complète](index.md).
