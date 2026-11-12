---
layout: default
title: SMART Control — Changelog
lang: fr_FR
pluginId: smartctrl
---

# Changelog SMART Control

## Version en cours de développement

### 09/11/2026

- **Sécurité et fiabilité** : le pack de widgets n'est installé que s'il correspond exactement à la version attendue.

### 08/11/2026

- **Sécurité et fiabilité** : le pack de widgets n'est téléchargé que s'il a changé ; les widgets déjà installés sont conservés en cas d'échec du téléchargement.

### 06/11/2026

- **Nouveautés et corrections** : la température d'un disque n'est plus comptée comme une température de pièce (résumés, assistants vocaux) ; l'usure est bornée de 0 à 100 %. Les commandes existantes sont corrigées à la mise à jour, sauf choix manuel.

### 29/10/2026

- **Nouveautés et corrections** : tuiles Documentation et Community déplacées en fin de barre de gestion.

### 23/10/2026

- **Nouveautés et corrections** : traductions mises à jour.

### 19/10/2026

- **Nouveautés et corrections** : renommer ou déplacer une cible ne provoque plus d'erreur ; ses disques suivent le nouveau nom et la nouvelle pièce.

### 14/10/2026

- **Sécurité et fiabilité** : le mot de passe sudo n'est plus visible dans la liste des processus de la cible ; le mode push n'accepte plus qu'un nombre limité de VM ; les mesures horaires et le rapport hebdomadaire tournent en arrière-plan, en parallèle pour chaque cible, sans bloquer Jeedom.

### 13/10/2026

- **Nouveautés et corrections** : la mesure des écritures fonctionne de nouveau sur Debian quand `fatrace` est installé ; des noms de fichiers ou de processus inhabituels ne font plus échouer la mesure ; correction de l'affichage du champ de recherche de la fenêtre Processus.
- **Sécurité et fiabilité** : les données venant de la cible sont affichées sans risque dans l'interface ; la sonde n'est plus déposée à un emplacement prévisible ; en mode push, le certificat de Jeedom est toujours vérifié, sauf option explicite « Accepter un certificat TLS non vérifiable ».

### 07/10/2026

- **Nouveautés et corrections** : nouvelle fenêtre **Processus** : liste en direct des processus d'une cible (CPU, mémoire, sessions), mode Jeedom, regroupement, recherche et export CSV/JSON.

### 20/09/2026

- **Sécurité et fiabilité** : vérification de l'empreinte du serveur SSH ; si elle change, la connexion est refusée avant l'envoi du mot de passe ou de la clé ; bouton **Réinitialiser** pour la réapprendre.

### 10/09/2026

- **Nouveautés et corrections** : le mode push remonte aussi le SMART ; alerte quand les données SMART sont périmées ou que la cible ne remonte plus rien, avec reprise automatique de la lecture SSH ; nouveau réglage **Fraîcheur des données** ; le pack de widgets est installé avec le plugin ; un équipement ne peut plus être proposé comme parent de l'un de ses descendants.

### 09/09/2026

- **Nouveautés et corrections** : authentification SSH par clé privée ; page de gestion rangée en panneaux Cibles / Disques / Machines virtuelles ; liste de VM à exclure ; fenêtre Santé mise à jour en direct, avec boutons d'action ; commandes de synthèse **Alertes SMART** et **Nombre d'alertes** ; un disque décoché ou une VM disparue est désactivé au lieu d'être supprimé (historique conservé) ; état relu quelques secondes après un self-test ou une mesure ; choix de la commande de notification avec le sélecteur standard de Jeedom.
- **Sécurité et fiabilité** : bibliothèque SSH mise à jour (failles corrigées) ; « Supprimer tous » inclut les équipements désactivés ; les actions groupées longues tournent en arrière-plan ; la tâche planifiée push n'est lisible que par root sur la cible.

### 08/09/2026

- **Nouveautés et corrections** : installation de `smartmontools` sur les cibles SSH en un clic ; actions groupées sur toutes les cibles ; mode push (la cible envoie ses mesures à Jeedom) ; boutons **Santé**, **Supprimer tous** et **Assistance** ; dépôt de la sonde possible même sans SFTP sur la cible ; l'alerte de hausse des erreurs CRC / command timeout fonctionne de nouveau ; l'indicateur d'usure NVMe affiche la bonne valeur ; un nom de VM avec des guillemets ne casse plus la mesure.
- **Sécurité et fiabilité** : le rapport hebdomadaire ne bloque plus les tâches planifiées de Jeedom.

### 07/09/2026

- **Nouveautés et corrections** : rapport hebdomadaire (SMART, tendance d'usure, écritures, recommandations) envoyé au centre de messages et à une commande de notification ; détection de la plateforme de la cible ; écritures mesurées par disque physique ; écritures par VM sur un hôte Proxmox/KVM, avec un équipement par VM en option ; rattachement des équipements à un parent ; icônes par type d'équipement ; nouveau tableau des commandes et barre d'outils de l'équipement ; bouton **Tester la connexion** avec diagnostic détaillé.

### 06/09/2026

- **Nouveautés et corrections** : nouvelle version du plugin ; surveillance SMART des disques SATA, NVMe et USB en local ou par SSH ; découverte et sélection des disques ; seuils d'alerte ; self-tests court et long, self-test court quotidien en option ; détection des VM et conteneurs sans SMART ; mesure de ce qui écrit sur le disque.
- **Sécurité et fiabilité** : mot de passe SSH stocké chiffré.
