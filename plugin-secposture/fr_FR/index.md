---
layout: default
title: Posture Sécurité — Plugin secposture pour Jeedom
lang: fr_FR
pluginId: secposture
---

# Description

Audit **en lecture seule** de la posture de sécurité de la box Jeedom. Le plugin vérifie la configuration chaque jour et à la demande, affiche les constats avec un correctif proposé, et expose un score et des compteurs en commandes (scénarios, notifications, historique).

> **Important**
>
> Le plugin ne modifie rien : il ne touche ni à SSH, ni au pare-feu, ni à MariaDB. Il détecte la dérive de configuration, pas un attaquant ayant déjà les droits root (qui peut le désactiver).

# Prérequis

| Composant | Version |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | 11 (Bullseye) ou 12 (Bookworm) |

Aucune dépendance à installer. Le plugin utilise les droits `sudo` de `www-data` présents sur une installation Jeedom standard ; sans eux, les contrôles concernés passent en **Inconnu** (jamais en OK).

Installation Docker non prise en charge : les contrôles système porteraient sur le conteneur, pas sur l'hôte.

# Contrôles

| Contrôle | Critique / Avertissement si… |
|---|---|
| Compte admin par défaut | le compte `admin` a le mot de passe `admin` (comparaison du hash, sans tentative de connexion) |
| Double authentification | un administrateur actif n'a pas la 2FA |
| Bannissement | la liste blanche contient toutes les adresses (critique) ; bannissement désactivé |
| Accès API | une API est « Activée » sans restriction d'IP |
| Mises à jour Jeedom | des mises à jour core/plugins sont en attente |
| Source des plugins | *(info)* plugins hors Market ou en bêta |
| sudo de www-data | *(info)* sudo illimité, standard sur Jeedom : rappel des conséquences |
| Mises à jour Debian automatiques | `unattended-upgrades` absent ou inactif |
| Exposition MariaDB | écoute hors loopback (critique) ; `local_infile` actif |
| Privilèges SQL | l'utilisateur SQL de Jeedom a un privilège global |
| Âge de la sauvegarde | aucune sauvegarde, ou plus ancienne que le seuil (48 h par défaut) |
| Copie hors box | aucun envoi Samba/Market configuré (normal si un NAS récupère les sauvegardes) |
| Ports en écoute | un port TCP hors loopback n'est pas dans la liste autorisée |
| Pare-feu | pas de chaîne `input` en politique `drop` (nftables), ou IPv4/IPv6 non couvert |
| SSH | root + mot de passe (critique) ; root ou mot de passe autorisé (valeurs effectives `sshd -T`) |
| Dossiers servis | `backup/`, `log/`, `data/` listés ou `.git/` lisible par HTTP (critique) |
| En-têtes HTTP | *(info)* version d'Apache visible, `X-Content-Type-Options` / `X-Frame-Options` absents |
| PHP du serveur web | `display_errors` ou `expose_php` actif (lu dans le php.ini Apache/FPM, pas celui du CLI) |
| Détection | ni fail2ban ni CrowdSec actif ; *(info)* plugin f2ban sans équipement |
| Intégrité des fichiers | fichier ajouté/modifié/supprimé hors mise à jour ; critique si code (`.php`, `.sh`, `.py`…), `sudoers`, `cron` ou `authorized_keys` |
| Exécutables temporaires | fichier exécutable dans `/tmp`, `/var/tmp` ou `/dev/shm` |

Un contrôle qui ne peut pas s'exécuter est marqué **Inconnu**, jamais OK.

# Score

`100 − 25 × critiques − 5 × avertissements` (minimum 0). Les constats *Info* et *Inconnu* ne comptent pas.

# Commandes

| Commande | Type |
|---|---|
| Score | info numérique (historisée) |
| Critiques | info numérique (historisée) |
| Avertissements | info numérique (historisée) |
| Âge dernière sauvegarde | info numérique (h) |
| Dernier audit | info texte |
| Résumé | info texte (pour une notification) |
| Fichiers modifiés (inattendus) | info numérique (historisée) |
| Lancer l'audit | action |
| Accepter les changements de fichiers | action |

Exemple de scénario : déclencheur `#[Posture sécurité][Critiques]# > 0` → envoyer `#[Posture sécurité][Résumé]#` par notification.

# Intégrité des fichiers

Au premier audit, le plugin enregistre une **référence** (empreinte SHA-256) des fichiers du core Jeedom (`core/`, `desktop/`, `mobile/`, `install/`), des plugins, et de fichiers système sensibles lus via sudo : `sudoers`, crontabs, unités systemd locales, `authorized_keys` de root et des utilisateurs.

À chaque audit :

- un changement dans le core ou un plugin **mis à jour depuis la référence** (date de mise à jour Jeedom différente) est **attendu** : la référence de ce groupe est remplacée automatiquement ;
- tout autre changement est **inattendu** et reste signalé jusqu'à ce que vous cliquiez sur **Accepter les changements de fichiers** (page du plugin ou commande action) ;
- un changement de fichier système est toujours inattendu (y compris lors d'une mise à jour Debian : à accepter après vérification).

Toujours exclus : `data/`, `venv/`, `node_modules/`, `.git/`, `__pycache__/`. Ajouter d'autres motifs dans la configuration.

Limites :

- un fichier remplacé en conservant exactement sa taille et sa date de modification n'est pas re-haché ;
- la référence est stockée sur la box (`plugins/secposture/data/baseline.json`) : un attaquant root peut la modifier ;
- le premier audit hache tous les fichiers et peut prendre plus d'une minute sur une carte SD.

# Commandes système exécutées

Toutes les commandes sont **fixes** (aucune donnée saisie n'y est insérée) et **en lecture seule** :

| Commande | Contrôle |
|---|---|
| `sudo -n -l` | droits sudo de www-data |
| `dpkg-query -W unattended-upgrades` | mises à jour automatiques |
| `ss -H -lnt` | ports en écoute |
| `sudo -n nft -j list ruleset` | pare-feu |
| `sudo -n sshd -T` | configuration SSH effective |
| `systemctl is-active fail2ban` / `crowdsec` | détection |
| `sudo -n find … -exec sha256sum` sur `sudoers`, crontabs, `/etc/systemd/system`, `authorized_keys` | intégrité des fichiers système |
| `find /tmp /var/tmp /dev/shm -type f -perm -u+x` | exécutables temporaires |

Requêtes MariaDB : `SHOW VARIABLES` et `SHOW GRANTS` uniquement. Requêtes HTTP : uniquement vers l'accès interne de Jeedom lui-même.

Le plugin n'écrit que dans son propre dossier (`plugins/secposture/data/`). **Aucune donnée n'est envoyée à l'extérieur.**

# Configuration

- **Âge maximal de la dernière sauvegarde (h)** : défaut 48.
- **Ports TCP autorisés en écoute** : défaut `22,80,443`. Ajouter les services volontairement ouverts (MQTT 1883, etc.).
- **Contrôles web** : requêtes HTTP vers l'accès interne de Jeedom (dossiers servis, en-têtes). À désactiver si cet accès n'est pas joignable depuis la box.
- **Exclusions de l'intégrité des fichiers** : un motif par ligne, comparé au chemin depuis la racine Jeedom (ex. `plugins/camera/captures/`).

L'équipement « Posture sécurité » est créé à l'installation. L'audit s'exécute via le cron quotidien de Jeedom.

---

*secposture — Documentation | Plugin open-source pour Jeedom*
