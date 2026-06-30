---
layout: default
title: Documentation Modbus Monitor
lang: fr_FR
pluginId: ModbusMonitor
---

# Description

Le plugin **ModbusMonitor** permet à Jeedom de communiquer avec vos équipements compatibles Modbus (TCP-IP ou RTU), tels que des compteurs d'énergie (ex: Socomec, SDM), des onduleurs solaires, des automates industriels ou tout autre appareil utilisant ce protocole de communication standard.

Il vous offre la possibilité de :
- Lire en temps réel les registres de vos appareils (Holding Registers, Input Registers, Coils, etc.).
- Envoyer des commandes d'écriture pour piloter ou configurer vos équipements.
- Traiter automatiquement la conversion des formats complexes (Float, Entiers 16/32 bits, Signés ou Non-Signés).
- Gérer les problèmes d'inversion d'octets (Endianness) très fréquents en Modbus.

# Installation

Afin d’utiliser le plugin, vous devez le télécharger depuis le Market Jeedom, l’installer et l’activer comme tout plugin standard. L'installation des dépendances se lancera automatiquement. 

Une fois l'installation terminée, vérifiez que le **Démon** (le service d'arrière-plan du plugin chargé de la communication Modbus) est bien au statut "OK".

# Configuration du plugin

Dans la page principale de configuration du plugin, vous pouvez modifier les options globales suivantes :

- **Le port d'écoute du démon** : Utilisé pour la communication interne de Jeedom. Il est recommandé de ne pas le modifier, sauf si ce port précis est déjà utilisé par un autre service sur votre système.
- **Niveau de log** : N'hésitez pas à le passer temporairement en mode "Debug" si vous rencontrez des problèmes pour analyser les trames brutes échangées avec votre équipement.

# Configuration des équipements

Pour ajouter un nouvel appareil, rendez-vous dans le menu **Plugins → Protocole domotique → ModbusMonitor** et cliquez sur **Ajouter**.

## Paramètres de connexion de l'équipement

Une fois votre équipement virtuel créé dans Jeedom, vous devez renseigner ses paramètres de connexion physiques :

- **Type de connexion (Connect Type)** : `TCP` (via votre réseau IP local) ou `RTU` (via un adaptateur série RS485).
- **Paramètres TCP** :
  - *IP du Gateway (Gatway Ip)* : L'adresse réseau IP de votre équipement ou de votre passerelle.
  - *Port* : Le port standard Modbus TCP est généralement le `502`.
- **Slave ID (Unit ID)** : L'identifiant de l'esclave sur le bus. Ce paramètre est **indispensable** même en TCP si vous passez par une passerelle Ethernet/RS485.
- **Offset (Décalage)** : La documentation des fabricants Modbus donne parfois des adresses en base 1 au lieu de la base 0 informatique. Si toutes vos valeurs lues semblent appartenir au registre d'à côté, testez en appliquant un offset de `-1` ou `+1`.

## Création des Commandes (Registres)

L'onglet **Commandes** est le cœur du plugin. C'est ici que vous allez définir les adresses Modbus exactes à interroger.
Pour chaque valeur que vous souhaitez récupérer, ajoutez une commande "Info / Numérique" (ou "Action" pour de l'écriture) avec les paramètres suivants :

- **Adresse (Start register)** : L'adresse décimale du registre (ex: `50946`).
- **Fonction (Function Code)** : Le type de requête Modbus. Par exemple, utilisez `fc03` (Read Holding Registers) ou `fc04` (Read Input Registers) pour lire des données de mesure, et `fc06` ou `fc16` pour écrire des valeurs.
- **Taille (Nb registers)** : Le nombre de mots (Words) de 16 bits à lire pour constituer la valeur. Par exemple, une valeur 32 bits nécessite `2` registres, tandis qu'une valeur 16 bits n'en nécessite qu'`1`.
- **Format attendu** : Permet d'indiquer au plugin comment interpréter la donnée brute (`longformat` pour les entiers standards, `floatformat` pour les nombres à virgule flottante).
- **Inversion Mots / Octets (Endianness)** : 
  - *Word Reverse / Byte Reverse* : Le protocole Modbus ne fixe pas de norme stricte sur l'ordre des octets (Big-Endian vs Little-Endian). Si les valeurs remontées par le plugin semblent totalement fantaisistes (ex: 1.5 milliards au lieu de 230), cochez l'option **Word Reverse**. C'est le correctif le plus courant !
- **Non signé (Unsigned)** : À cocher si la valeur est toujours positive (ex: une énergie cumulée en kWh).
- **Opération** : Très utile pour ajuster l'échelle à la volée ! 
  - Si votre équipement envoie la tension en dixièmes de volts (ex: la valeur brute `2325` pour `232.5 V`), renseignez `/10` dans ce champ.
  - S'il envoie une puissance de `2500 W` sous la forme de la valeur `25`, renseignez `*100`.

# Outils intégrés : Scan et Test

Le plugin intègre des outils de diagnostic très puissants, accessibles directement depuis la configuration de votre équipement :

- **Le Scanner Modbus** : Vous ne connaissez pas l'adresse exacte d'une valeur ou vous cherchez des registres cachés ? Le scanner vous permet de lancer une recherche automatique sur une plage d'adresses (ex: de 50000 à 51000). Il interroge le périphérique et vous affiche sous forme de tableau tous les registres qui répondent, avec leur valeur brute décodée selon plusieurs formats (Int32, Float, etc.). Il vous indique même si une commande existe déjà pour ce registre !
- **L'outil de Test** : Accessible depuis les résultats du scan ou directement depuis l'équipement, cet outil vous permet de tester une adresse spécifique en temps réel et de jouer avec les cases "Word Reverse" ou "Byte Reverse". C'est la méthode idéale pour trouver la bonne combinaison de décodage *avant* de créer la commande définitive.

# Dépannage fréquent

- **Le démon crashe ou ne se connecte pas** : Vérifiez scrupuleusement l'adresse IP et surtout le `Slave ID`. Un mauvais Slave ID provoquera l'absence de réponse de l'équipement et une déconnexion.
- **Les valeurs lues ne veulent rien dire** : Essayez de cocher/décocher `Word Reverse`. Dans de très rares cas, `Byte Reverse` peut également être nécessaire.
- **La valeur est légèrement fausse** : Vérifiez que l'adresse (Start Register) est bien la bonne et qu'elle n'est pas décalée d'un cran (voir le paramètre `Offset` de l'équipement). 

# Changelog

[Voir le changelog](./changelog)

# Support

Si malgré cette documentation et après voir lu les sujets en rapport avec le plugin sur [community]({{site.forum}}/tags/plugin-{{page.pluginId}}) vous ne trouvez pas de réponse à votre question, n'hésitez pas à créer un nouveau sujet en n'oubliant pas de mettre le tag du plugin ([plugin-{{page.pluginId}}]({{site.forum}}/tags/plugin-{{page.pluginId}})).
