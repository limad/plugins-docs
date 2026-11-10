---
layout: default
title: Wyoming Bridge — Commande vocale Jeedom (Wyoming)
lang: fr_FR
pluginId: wyoming_bridge
---

# Wyoming Bridge

Le plugin **Wyoming Bridge** permet de piloter Jeedom à la voix depuis un pipeline vocal [Wyoming](https://github.com/rhasspy/wyoming) (satellites, Whisper, Piper).

Il expose un service Wyoming de type **handle** (« gestion des intentions ») : le pipeline lui envoie le texte transcrit, le plugin le transmet au plugin **AI Assistant**, qui tente d'abord une interaction Jeedom puis, à défaut, son LLM avec les outils MCP de Jeedom. La réponse texte revient au pipeline, qui la fait dire par Piper.

Le plugin ne remplace rien dans le pipeline vocal existant : il s'y ajoute comme un service Wyoming de plus.

## Prérequis

- Jeedom 4.5 ou supérieur, Debian 11 ou supérieur.
- Le plugin **AI Assistant**, actif, avec un **client MCP par défaut** configuré.
- Un pipeline vocal Wyoming (par exemple Home Assistant Assist, ou un orchestrateur Wyoming) capable d'utiliser un service « handle ».

## Installation

1. Installer et activer le plugin.
2. Lancer l'installation des **dépendances** (environnement Python avec le paquet `wyoming`).
3. Renseigner la configuration (ci-dessous), en particulier **IPs autorisées**.
4. Le démon démarre automatiquement (gestion automatique du démon dans la page de configuration).

## Configuration du plugin

| Paramètre | Rôle | Par défaut |
|---|---|---|
| Port TCP | Port du service Wyoming | 10500 |
| Bind | Adresse d'écoute ; `0.0.0.0` = toutes les interfaces | 0.0.0.0 |
| Timeout (s) | Délai maximal laissé à AI Assistant pour répondre (5 à 120 s) | 20 |
| Message d'échec | Phrase dite par Piper quand aucune réponse n'a pu être produite | « Désolé, je n'ai pas pu répondre. » |
| IPs autorisées | IPs ou réseaux (CIDR) autorisés à se connecter, séparés par des virgules | — |
| Token interne | Jeton qui protège l'échange entre le démon et Jeedom ; bouton pour le régénérer | généré |

**IPs autorisées est obligatoire** dès que l'adresse d'écoute n'est pas `127.0.0.1` : le protocole Wyoming n'a pas d'authentification, cette liste est la seule protection. Sans elle, le démon refuse de démarrer.

Exemple : `192.168.1.0/24` pour tout le réseau local, ou `192.168.1.50, 192.168.1.51` pour deux appareils précis.

## Raccorder le pipeline vocal

Déclarer, dans le logiciel qui orchestre le pipeline, un service Wyoming à l'adresse `IP_de_Jeedom:10500`, de type **handle** / gestion des intentions. Le service s'annonce sous le nom `jeedom`, avec la langue de Jeedom.

## Page du plugin

- **Configuration** : ouvre la configuration du plugin.
- **Tester** : envoie une phrase au démon par le protocole Wyoming, exactement comme un satellite. La fenêtre affiche l'état du démon, la réponse, si elle a été traitée, la durée et le profil AI Assistant utilisé. Les actions demandées sont réellement exécutées et chaque test peut consommer un appel au LLM.
- **Santé** : dépendances, démon, adresse d'écoute, IPs autorisées, délai, versions (Python, paquet `wyoming` installé et dernière version disponible, librairies d'installation), AI Assistant et client MCP par défaut.
- **Équipement Wyoming Bridge** : créé automatiquement, avec ces commandes info, utilisables dans les scénarios :

| Commande | Contenu | Historisée par défaut |
|---|---|---|
| État du démon | 1 = actif (vérifié toutes les 5 minutes) | non |
| Dernière phrase | Texte reçu du satellite | non |
| Dernière réponse | Texte renvoyé (vide si non traitée) | non |
| Dernière requête traitée | 1 = réponse obtenue, 0 = échec | oui |
| Durée de la dernière requête | En millisecondes, côté Jeedom | oui |
| Dernier satellite | Adresse IP du satellite, ou `local` | non |

Les commandes sont mises à jour après l'envoi de la réponse : elles n'ajoutent aucun délai. La dernière phrase et la dernière réponse sont visibles par les utilisateurs de Jeedom ; ne pas historiser ces deux commandes si l'on ne veut pas conserver les conversations.

## Fonctionnement

- Chaque satellite (identifié par son adresse IP) a sa propre limite de débit et son propre historique de conversation dans AI Assistant. Si un orchestrateur unique relaie tous les satellites, ils partagent sa limite et son historique.
- AI Assistant accepte une requête toutes les 2 secondes par appelant : une phrase envoyée trop tôt reçoit le message d'échec.
- En cas de délai dépassé, d'erreur ou d'AI Assistant indisponible, le pipeline reçoit « non traité » avec le message d'échec.
- Les phrases et les réponses ne sont écrites dans les logs qu'en niveau Debug.

## Dépannage

| Symptôme | Vérification |
|---|---|
| Le démon ne démarre pas | Fenêtre Santé : IPs autorisées vide alors que Bind n'est pas `127.0.0.1`, ou dépendances à installer |
| Le satellite n'obtient jamais de réponse | IP du satellite (ou de l'orchestrateur) absente de IPs autorisées ; log `wyoming_bridge_daemon` en niveau Warning |
| Réponse « Désolé… » | Délai trop court pour le LLM, limite des 2 secondes, ou pas de client MCP par défaut dans AI Assistant |
| Le bouton Tester indique « IP refusée » | Ajouter `127.0.0.1` ou l'IP de Jeedom dans IPs autorisées |

Logs : `wyoming_bridge` (partie PHP) et `wyoming_bridge_daemon` (démon Python).
