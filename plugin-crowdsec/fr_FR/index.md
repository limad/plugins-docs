---
layout: default
title: CrowdSec — Documentation
lang: fr_FR
pluginId: crowdsec
---

# Description

Le plugin supervise une instance [CrowdSec](https://www.crowdsec.net/) via son API locale (LAPI). Il remonte le nombre de décisions actives, en distinguant les détections locales de la blocklist communautaire, ainsi que la liste et la dernière adresse IP bloquée localement. Il permet aussi de **bannir** et de **débannir** une adresse IP depuis Jeedom (scénarios, widget).

La lecture utilise une clé *bouncer*, qui ne permet que de lire les décisions. Pour bannir et débannir, ainsi que pour les compteurs de détections, il faut en plus un compte *machine*.

CrowdSec peut tourner sur la box Jeedom elle-même (le plugin sait l'installer) ou sur une autre machine du réseau. Un équipement Jeedom correspond à une LAPI : on peut donc superviser plusieurs instances.

# Prérequis

| Composant | Version |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | ≥ 12 |
| CrowdSec | une instance joignable (sur la box ou ailleurs) |

Pour la seule supervision d'une instance distante, le plugin n'a **aucune dépendance**.

# Installer CrowdSec sur la box (optionnel)

Si CrowdSec tourne déjà ailleurs et que vous voulez seulement le superviser, passez directement à la section suivante.

Pour installer CrowdSec sur la box Jeedom elle-même, dans la configuration du plugin (cadre **Installation locale (optionnelle)**) :

1. Cocher **Installer CrowdSec sur cette box**. En option :
   - **Port de la LAPI** : 8080 par défaut (entre 1024 et 65535). S'il est déjà pris, le port libre suivant est utilisé. Ignoré si CrowdSec est déjà installé ;
   - **Installer le bouncer pare-feu** (nftables) : sans lui, CrowdSec détecte et décide mais ne bloque rien sur la box.
2. Sauvegarder, puis cliquer sur **Relancer** dans le cadre *Dépendances*.

L'installation ajoute le dépôt officiel CrowdSec (celui de Debian est trop ancien), installe le paquet, crée une clé bouncer et un compte machine `jeedom-local`, puis crée l'équipement **CrowdSec (box)** déjà configuré. Le déroulement est visible dans le log `crowdsec_update`.

Si CrowdSec est déjà installé, sa configuration est conservée : seuls la clé et le compte `jeedom-local` sont recréés (un bouncer ou une machine `jeedom` créés à la main ne sont pas touchés). Le log signale une éventuelle mise à niveau de CrowdSec vers la version du dépôt officiel.

**Docker** : cette installation repose sur `systemd` et `nftables`, généralement absents d'un conteneur. Dans Docker, seule la supervision d'une instance CrowdSec distante est utilisable.

Décocher l'option ne désinstalle pas CrowdSec (`sudo apt remove crowdsec`).

# Préparer CrowdSec (installation manuelle ou distante)

Sur la machine qui héberge CrowdSec, créer une clé bouncer :

```
sudo cscli bouncers add jeedom
```

Copier la clé affichée : elle n'est plus visible ensuite.

Optionnel, pour bannir / débannir et obtenir les détections depuis Jeedom, créer un compte machine :

```
sudo cscli machines add jeedom --password '<mot de passe>'
```

# Configuration de l'équipement

Ajouter un équipement depuis **Plugins > Monitoring > CrowdSec**, puis renseigner :

**Connexion à la LAPI**

- **URL de la LAPI** : `http://127.0.0.1:8080` si CrowdSec tourne sur la box Jeedom. Si la LAPI est sur une autre machine, elle doit écouter sur une interface joignable (`api.server.listen_uri` dans `/etc/crowdsec/config.yaml`) ; préférer alors https, car la clé est envoyée à chaque requête.
- **Certificat CA (fichier PEM)** : optionnel, pour une LAPI en https dont le certificat est auto-signé ou émis par une autorité interne. Chemin d'un fichier PEM lisible par `www-data`. Vide : autorités du système. La vérification TLS reste stricte (nom d'hôte compris).
- **Clé bouncer** : la clé créée ci-dessus (stockée chiffrée).

**Bannir / débannir (optionnel)**

- **Compte machine** / **Mot de passe machine** : le compte créé ci-dessus (mot de passe stocké chiffré). Laisser vide pour un usage en lecture seule : les commandes Bannir / Débannir échouent alors, et les commandes de détection restent vides.
- **Durée de bannissement** : durée des bannissements faits depuis Jeedom, au format CrowdSec (`4h`, `30m`, `1h30m`...), `4h` par défaut.

**Paramètres spécifiques**

- **Auto-actualisation** : fréquence de lecture (expression cron, assistant disponible). Vide : toutes les 10 minutes. `off` : pas d'actualisation automatique (la commande Rafraîchir et le bouton **Actualiser maintenant** restent utilisables). La blocklist communautaire (milliers d'IP) n'est relue en entier qu'une fois par heure ; entre deux, les compteurs et scénarios communautaires viennent de la dernière lecture complète et seules les décisions locales sont relues.

À la sauvegarde, une URL, une expression cron, une durée de bannissement ou un certificat invalides sont refusés avec un message explicite.

Boutons de la fiche :

- **Tester la connexion** : vérifie que la LAPI répond et accepte la clé bouncer, puis le compte machine s'il est renseigné. Il teste la configuration **enregistrée** : sauvegarder d'abord.
- **Actualiser maintenant** : relit immédiatement les décisions.
- **Informations brutes**, **Mode édition des commandes**, **Défaut config** (réapplique le widget par défaut sur les commandes de l'équipement).

# Commandes

| Commande | Type | Description |
|---|---|---|
| Décisions actives | info numérique | Toutes les décisions actives |
| Décisions locales | info numérique | Décisions issues de la machine (détections, `cscli`...) |
| Décisions communautaires | info numérique | Décisions de la blocklist communautaire (CAPI) et des listes abonnées |
| IP bloquées localement | info texte | IP des décisions locales, la plus récente en premier (50 maximum). Les décisions portant sur une plage ou un pays sont comptées mais pas listées |
| Dernière IP bloquée localement | info texte | IP de la décision locale la plus récente |
| LAPI joignable | info binaire | 1 si la dernière lecture de la LAPI a réussi, 0 sinon (les autres valeurs restent alors figées) |
| Principaux scénarios | info texte | Les 3 scénarios les plus fréquents parmi les décisions actives (ex. `http:scan 7772, ssh:bruteforce 4357`) |
| Détections locales (24 h) | info numérique | Alertes levées par la machine elle-même sur les dernières 24 h (hors mises à jour des listes communautaires, bannissements manuels et alertes simulées). Nécessite le compte machine |
| Dernière détection locale | info texte | Scénario, IP et date de la dernière détection locale. Nécessite le compte machine |
| Dernière mise à jour des listes | info texte | Date (heure de la box) de la dernière mise à jour de la blocklist communautaire, normalement toutes les 2 h environ. Si elle n'avance plus, la protection communautaire est périmée. Nécessite le compte machine |
| Rafraîchir | action | Relit immédiatement les décisions |
| Bannir une IP | action message | Ajoute une décision `ban` locale sur l'IP saisie, pour la durée configurée (équivalent de `cscli decisions add --ip`). Refusé pour le loopback et les adresses de la box Jeedom, afin de ne pas s'enfermer dehors |
| Débannir une IP | action message | Supprime toutes les décisions actives sur l'IP saisie (équivalent de `cscli decisions delete --ip`) |

Exemple de scénario : action `Bannir une IP` avec, dans le champ message, l'adresse à bloquer (ex. `203.0.113.10`).

# Surveillance

- **Panne de la LAPI** : après 3 relevés consécutifs en échec, un message est ajouté au centre de messages de Jeedom (une seule fois), avec la cause. Pour l'équipement « CrowdSec (box) », il indique si le service `crowdsec` est arrêté ou si la LAPI ne répond pas alors que le service tourne. Le message disparaît quand la LAPI répond de nouveau. La commande `LAPI joignable` passe à 0 dès le premier échec.
- **Blocklist communautaire périmée** : un message est ajouté si la dernière mise à jour a plus de 6 h (elle a lieu environ toutes les 2 h). Nécessite le compte machine.
- **Page Santé** de Jeedom : une ligne par équipement actif avec l'état de la LAPI et la dernière erreur.

Le plugin ne redémarre pas CrowdSec : lancer `sudo systemctl restart crowdsec` sur la machine concernée.

# FAQ / dépannage

**« Clé bouncer refusée par la LAPI (HTTP 403) »**
La clé a été supprimée ou mal copiée. En recréer une (`cscli bouncers add`) et la coller dans l'équipement.

**« Compte machine refusé par la LAPI (HTTP 403) »**
Le compte machine n'est pas validé : `sudo cscli machines validate <nom>`.

**« Identifiants du compte machine refusés (HTTP 401) »**
Nom de compte ou mot de passe incorrect.

**« LAPI injoignable »**
Vérifier l'URL et le port, que le service tourne (`systemctl status crowdsec`) et, pour une LAPI distante, qu'elle écoute sur une interface joignable depuis Jeedom.

**La date « Dernière mise à jour des listes » n'avance plus**
CrowdSec ne reçoit plus la blocklist communautaire : vérifier sa connexion au service central avec `sudo cscli capi status`.

**Les IP bannies ne sont pas bloquées sur la box**
Une décision ne bloque rien sans bouncer de remédiation. Sur la box, cocher **Installer le bouncer pare-feu** dans la configuration du plugin puis relancer les dépendances.

**Où voir les erreurs ?**
Log `crowdsec` (lectures, bannissements) et log `crowdsec_update` (installation locale).
