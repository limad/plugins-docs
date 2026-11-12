---
layout: default
title: SMART Control — Documentation
lang: fr_FR
pluginId: smartctrl
---

# Description

Surveillance SMART des disques (SATA, NVMe, pont USB) de la box Jeedom **ou** d'une machine distante par SSH : santé, usure, température, écritures, self-tests et alertes sur seuils. Le plugin sait aussi mesurer **ce qui écrit sur le disque** (logs, base de données, VM…), produire un **rapport hebdomadaire** avec des recommandations, et afficher les processus actifs d'une cible.

Organisation des équipements :

- une **cible** = une machine surveillée (la box en local, ou un hôte SSH) ;
- un **disque** = un équipement enfant créé automatiquement pour chaque disque sélectionné, nommé `<cible>_<disque>` ;
- une **VM** = un équipement enfant optionnel par machine virtuelle, sur un hôte Proxmox/KVM.

La page du plugin range les équipements en trois panneaux : **Cibles**, **Disques**, **Machines virtuelles**.

> **Important**
>
> Le SMART n'est **pas disponible dans une VM** (disque virtuel) ni dans la plupart des conteneurs : le plugin le détecte et ne crée alors aucune commande SMART. Pour surveiller le disque physique d'un hôte de virtualisation (ex. Proxmox), créez une cible **SSH** vers cet hôte. La mesure des écritures reste utile sur une VM.

# Prérequis

| Composant | Version |
|---|---|
| Jeedom | ≥ 4.3 |
| Debian | ≥ 11 |
| Cible | `smartmontools` (commande `smartctl`) |

- **Box Jeedom (mode local)** : `smartmontools` s'installe via le cadre **Dépendances** de la page de configuration du plugin.
- **Cible SSH** : bouton **Installer smartmontools** dans la fiche de la cible (apt, dnf, yum, zypper, pacman, apk, opkg ou pkg selon la machine), ou à la main. Nécessite un compte root ou un sudo sans mot de passe.
- **Optionnel** : le paquet `fatrace` sur la cible donne le détail le plus précis de « qui écrit quoi ».

# Configuration du plugin

Page **Configuration** du plugin :

- **Seuils d'alerte par défaut** : **Seuil d'usure (%)** (90 par défaut) et **Seuil de température (°C)** (60 par défaut), utilisés quand l'équipement n'a pas de valeur propre.
- **Rapport hebdomadaire (défauts)** : **Commande de notification** (Telegram, mail, push…, choisie avec le sélecteur de commande) et **Planning (cron)** (par défaut samedi 03:00).
- **Fraîcheur des données (min)** : au-delà de ce délai sans nouvelle mesure SMART, la cible passe en alerte « données périmées » (180 min par défaut).
- **Actions groupées** sur toutes les cibles : **Installer smartmontools (cibles SSH)**, **Rafraîchir le SMART**, **Sonder les écritures**. Le résultat s'affiche cible par cible.

# Configurer une cible

1. Cliquer sur **Ajouter**, nommer l'équipement.
2. **Mode** :
   - **Local** : disques de la box Jeedom ;
   - **SSH** : renseigner **Hôte**, **Port** (22 par défaut), **Nom d'utilisateur** et **Authentification** :
     - **Mot de passe** : simple, le mot de passe sert aussi à sudo ;
     - **Clé privée** (recommandé) : coller la clé privée OpenSSH/PEM (RSA, ECDSA, Ed25519) et, si besoin, sa **passphrase**. La clé publique doit figurer dans `~/.ssh/authorized_keys` de l'utilisateur sur la cible. Sans mot de passe, l'utilisateur doit être root ou disposer d'un sudo sans mot de passe.
3. **Sauvegarder**.
4. **Tester la connexion** : distingue hôte injoignable, identifiants refusés, `smartctl` absent, ou succès avec la version de `smartctl`.
5. **Scanner les disques** : la liste des disques détectés s'affiche, avec un badge indiquant si le SMART est disponible. Le scan détecte aussi la plateforme de la cible (Proxmox, Debian, Raspberry Pi, appliance Jeedom, Docker, LXC…).
6. Cocher les disques à suivre puis **Appliquer la sélection** : un équipement « disque » est créé par disque sélectionné. Un disque décoché est **désactivé**, pas supprimé : son historique est conservé.

Mots de passe, clé privée et passphrase sont stockés chiffrés.

**Empreinte du serveur SSH** : à la première connexion, le plugin enregistre l'empreinte de la clé du serveur, puis la vérifie à chaque connexion. Si elle change (serveur réinstallé, ou machine qui usurpe la cible), la connexion est refusée **avant** l'envoi du mot de passe ou de la clé, et une erreur indiquant l'ancienne et la nouvelle empreinte est enregistrée dans le log. Après une réinstallation légitime, le bouton **Réinitialiser** à côté de l'empreinte permet de la réapprendre. Changer l'hôte, le port ou le mode la réinitialise aussi, ainsi que la duplication de l'équipement.

**Équipement parent** : chaque équipement peut être rattaché sous un autre (ex. une VM sous son hôte Proxmox). C'est purement organisationnel ; un équipement ne peut pas être rattaché à lui-même ni à l'un de ses descendants.

## Droits root (sudo)

`smartctl` nécessite les droits root.

- **Mode local** : le plugin utilise les droits sudo standard de Jeedom, rien à faire.
- **Mode SSH, utilisateur root** : rien à faire.
- **Mode SSH, autre utilisateur** : avec un mot de passe, celui-ci est fourni à sudo ; avec une clé privée, il faut un sudo sans mot de passe. Le plugin fournit un modèle limité à `smartctl` et `lsblk` : `plugins/smartctrl/resources/sudoers.smartctrl`, à copier dans `/etc/sudoers.d/` de la cible en adaptant le nom d'utilisateur.

> La **mesure des écritures** en SSH demande davantage : un compte root, ou un sudo étendu au shell (ce qui revient à un accès root complet). Le SMART fonctionne sans. Le mode **push** (plus bas) n'est pas concerné.

# Surveillance

Section **Surveillance** de la cible :

- **Auto-actualisation** : expression cron de relecture du SMART, `*/15 * * * *` par défaut. Le plugin la vérifie toutes les 15 minutes : une fréquence plus élevée n'est pas prise en compte. Vide : pas de relecture automatique.
- **Seuil usure (%)** / **Seuil température (°C)** : vide = défaut du plugin.
- **Self-test court quotidien** : lance un self-test court sur chaque disque suivi, une fois par jour.

Alertes ajoutées au **centre de messages** de Jeedom à chaque relevé :

- santé SMART différente de PASSED ;
- usure ≥ seuil ; température ≥ seuil ;
- hausse des erreurs CRC ou des « command timeout » ;
- self-test en échec.

La cible porte aussi deux commandes de synthèse, **Alertes SMART** (texte, `RAS` si tout va bien) et **Nombre d'alertes** (historisée, utilisable dans un scénario). Elles signalent en plus les secteurs réalloués ou en attente, les données SMART périmées et l'absence de remontée en mode push.

# Commandes

## Disque

| Commande | Type | Historisée |
|---|---|---|
| Santé (PASSED) | info binaire (1 = PASSED) | |
| Usure | info numérique (%) | oui |
| Température | info numérique (°C) | oui |
| Température max | info numérique (°C) | |
| Écrit (hôte) | info numérique (Go) | oui |
| Heures allumé | info numérique (h) | |
| Cycles démarrage | info numérique | |
| Secteurs réalloués / Secteurs en attente | info numérique | |
| Erreurs CRC / Command timeout | info numérique | |
| Arrêts brutaux | info numérique | |
| Indicateur d'usure | info numérique | |
| Dernier self-test | info texte | |
| Modèle / Numéro de série / Capacité (Go) | info | |
| Dernier relevé | info texte | |
| Écritures/jour | info numérique (Go/j), après une sonde | oui |
| Rafraichir | action | |
| Self-test court / Self-test long | action | |

Seules les commandes que le disque sait fournir sont créées (un SSD NVMe et un disque mécanique n'exposent pas les mêmes compteurs).

## Cible

| Commande | Type | Historisée |
|---|---|---|
| Alertes SMART | info texte | |
| Nombre d'alertes | info numérique | oui |
| Écritures/jour | info numérique (Go/j) | oui |
| Écritures SSD/jour | info numérique (Go/j) | oui |
| Swap out (fenêtre) | info numérique (Mio) | |
| Top catégorie / Top écrivain | info texte | |
| Écritures VM/jour / Écritures hôte/jour | info numérique (Go/j), hôte Proxmox/KVM | oui |
| Top VM | info texte, hôte Proxmox/KVM | |
| Plateforme / Méthode de sonde / Dernière sonde | info texte | |
| Sonder les écritures | action | |
| Envoyer le rapport | action | |
| Rafraichir | action | |

## VM

| Commande | Type | Historisée |
|---|---|---|
| Écritures/jour | info numérique (Go/j) | oui |

# Écritures disque

Section **Écritures disque** de la cible. Le bouton **Sonder les écritures maintenant** mesure, pendant la **Fenêtre de sonde** (120 s par défaut), combien est écrit et **ce qui l'écrit** :

- **Écritures/jour** : volume total extrapolé sur une journée ;
- **Écritures SSD/jour** : la part qui touche réellement un SSD (les écritures en RAM, tmpfs, sont exclues) ;
- **Top catégorie** / **Top écrivain** : catégorie et fichier ou processus qui écrit le plus (logs, sessions, base de données, cache, un plugin précis…).

Le détail par fichier est le plus précis avec `fatrace` ; sans lui, le plugin se rabat sur d'autres méthodes (volume global fiable, détail par processus approximatif). La commande **Méthode de sonde** indique la méthode utilisée.

Le volume est mesuré **par disque physique** : chaque disque reçoit sa commande **Écritures/jour**. Sur un hôte avec LVM (Proxmox), cela capture aussi les écritures des VM réparties sur les autres volumes.

**Sonde horaire auto** : lance une mesure chaque heure.

## Écritures par VM (Proxmox / KVM)

Sur un hôte Proxmox/KVM (cible SSH), la sonde attribue les écritures **par VM**. Le rapport liste les VM classées par débit d'écriture, la somme, et le **reste** (écritures de l'hôte et surcoût du stockage).

- **Un équipement par VM** : crée un équipement enfant par VM avec son historique **Écritures/jour**, pour suivre la tendance. Une VM disparue est désactivée, pas supprimée.
- **VM à exclure** : VMID exacts ou fragments de nom (sans tenir compte des majuscules). Ces VM n'ont pas d'équipement dédié (l'équipement existant est désactivé) mais restent comptées dans le rapport.

> La somme par VM est inférieure au total du disque physique : l'écart correspond à l'hôte et au surcoût du stockage (copie sur écriture, journalisation…). VMware ESXi n'est pas pris en charge.

## Mode push

Utile quand Jeedom ne peut pas joindre la cible en SSH, ou pour ne pas faire tourner la sonde longue depuis Jeedom : la cible lance elle-même la sonde chaque heure (cron en root) et envoie le résultat à Jeedom, SMART compris.

- **Sonde en push** : active le mode. Tant qu'une remontée récente existe, Jeedom ne relit pas la cible par SSH ; si les remontées s'arrêtent au-delà de la **Fraîcheur des données**, la lecture SSH reprend automatiquement et une alerte est levée.
- **Déployer la sonde push (SSH)** : installe la sonde et la tâche planifiée sur la cible (root ou sudo sans mot de passe).
- **Retirer le cron push** : supprime la tâche planifiée de la cible.
- **Voir la ligne cron à copier** : pour une installation manuelle.
- **Accepter un certificat TLS non vérifiable** : à cocher seulement si l'adresse interne de Jeedom est en https avec un certificat auto-signé, puis redéployer la sonde.

Prérequis : l'**adresse interne** de Jeedom doit être renseignée (Réglages > Système > Configuration > Réseau). La ligne cron contient une clé d'accès à Jeedom : ne la partagez pas.

# Rapport hebdomadaire

Section **Rapport** de la cible :

- **Rapport hebdomadaire** : active l'envoi automatique ;
- **Notification (surcharge)** : commande d'envoi propre à cette cible (vide = défaut du plugin) ;
- **Planning (cron)** : vide = défaut du plugin (samedi 03:00). Le planning est vérifié une fois par heure : les minutes sont ignorées ;
- **Envoyer le rapport maintenant** : aperçu immédiat.

Le rapport contient : tableau SMART par disque, **tendance d'usure sur 7 jours**, écritures (Go/jour, part SSD, top écrivain), écritures par VM, état tmpfs et **recommandations** (remplacer un disque en échec, surveiller les secteurs réalloués, refroidissement, réduire les logs…). Il est toujours ajouté au centre de messages et, si une commande de notification est choisie, envoyé aussi par elle. Avant l'envoi planifié, une mesure d'écritures longue (300 s) est faite.

# Page de gestion

Boutons du haut de la page du plugin :

- **Santé** : vue d'ensemble de toutes les cibles (plateforme, SMART, dernière sonde, alertes) avec leurs disques et VM ; santé, usure et température colorées selon les seuils et mises à jour en direct ; boutons rafraîchir, sonder, rapport et self-tests.
- **Processus** : liste en direct des processus d'une cible (locale ou SSH) : CPU, mémoire, threads, durée, sessions SSH. Mode **Standard** (tous) ou **Jeedom** (processus liés à Jeedom), regroupement par nom, recherche, export CSV/JSON.
- **Supprimer tous** : supprime toutes les cibles et leurs disques/VM (après confirmation). Les disques pourront être ré-ajoutés par un nouveau scan.
- **Assistance** : prépare une demande d'aide sur le forum avec un diagnostic du plugin (Jeedom ≥ 4.4).

Barre de la fiche équipement : **Informations brutes**, **Mode édition des commandes**, **Défaut config** (nouveau scan et relecture des commandes), **Dupliquer**, **Sauvegarder**, **Supprimer**. Supprimer une cible supprime aussi ses disques et VM.

# FAQ / dépannage

**Aucune commande SMART n'est créée.**
La cible est probablement une VM ou un conteneur : le SMART n'y est pas accessible. Créez une cible SSH vers l'hôte physique.

**« Connexion OK, mais smartctl est introuvable »**
Installez `smartmontools` : cadre Dépendances pour la box, bouton **Installer smartmontools** pour une cible SSH.

**« sudo a refusé la commande »**
Utilisez un compte root ou installez le modèle sudoers fourni (section Droits root).

**La connexion SSH est refusée après une réinstallation du serveur.**
L'empreinte du serveur a changé : vérifiez qu'il s'agit bien de votre machine, puis cliquez sur **Réinitialiser** à côté de l'empreinte.

**« Sonde sans résultat » en SSH alors que le SMART fonctionne.**
La mesure des écritures exige root sur la cible : connectez-vous en root ou utilisez le mode push.

**Alerte « données SMART périmées » ou « aucune remontée push ».**
La cible n'a pas été relue depuis plus que la fraîcheur configurée : vérifiez qu'elle est joignable, ou que la tâche planifiée push tourne bien sur la cible.

**Où voir les erreurs ?**
Log `smartctrl`. Pour l'installation de `smartmontools` sur la box, le log est accessible depuis le cadre Dépendances.
