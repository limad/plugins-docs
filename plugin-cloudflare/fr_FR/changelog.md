---
layout: default
title: Cloudflare Tunnel — Changelog
lang: fr_FR
pluginId: cloudflare
---

# Changelog — plugin cloudflare

## V1

### Décision d'architecture : daemon `cloudflared`

Choix retenu : **unit systemd dédiée (`cloudflared-jeedom.service`), pilotée par
`sudo` avec un fichier sudoers scopé à l'exact nécessaire** — ni pattern
"plugin à daemon" Python/socket classique, ni `exec()` synchrone direct dans
l'AJAX.

Justification :
- `cloudflared` est un binaire Go autonome qui gère déjà tout son cycle de vie
  (reconnexion, health-check interne) : un wrapper Python asyncio n'apporterait
  rien, ce pattern sert à faire dialoguer PHP avec un *daemon métier* qui
  produit des données, pas à superviser un binaire tiers déjà persistant.
- Appeler `systemctl` en direct et de façon synchrone depuis l'AJAX est exclu :
  cela bloquerait le rendu d'une page sur une opération qui peut prendre
  plusieurs secondes (attente de connexion au edge Cloudflare). Toutes les
  actions de service sont donc **asynchrones** (lancées en arrière-plan,
  statut relu ensuite par cron ou bouton "rafraîchir").
- Le statut affiché (running/stopped/erreur, connexions, version) est
  rafraîchi par **cron5** (léger : `systemctl is-active` sans sudo + appel API
  Cloudflare mis en cache implicite par la fréquence du cron) et **cronDaily**
  pour l'IP publique (peu volatile). Rien n'est recalculé de façon
  synchrone dans une page qui bloquerait le rendu.
- L'installation du binaire n'utilise **pas** de `curl | bash` : elle passe
  par le dépôt APT officiellement publié et signé par Cloudflare
  (`pkg.cloudflare.com`, clé GPG vérifiée par `apt` lui-même). C'est le
  mécanisme d'authenticité recommandé par Cloudflare pour Debian/Ubuntu ; il
  a été préféré à une vérification manuelle de somme SHA256 d'un binaire
  téléchargé une fois, car il couvre aussi nativement les mises à jour de
  sécurité ultérieures du paquet. Le script d'installation est un fichier
  livré avec le plugin, jamais une commande construite dynamiquement par
  l'AJAX.
- **Piège de sécurité évité** : le fichier `resources/scripts/*.sh` du plugin
  vit dans un répertoire appartenant à `www-data` (comme tout le reste du
  plugin). Le faire pointer directement en cible `sudo` aurait été une faille
  d'élévation de privilèges (un compromis applicatif aurait pu modifier le
  script puis le faire exécuter en root). Le script se **déploie donc
  lui-même** en copie root-owned (`install -o root -g root -m 0755`) vers
  `/usr/local/bin/cloudflared-jeedom-install.sh` — c'est cette copie, et
  uniquement elle, que `sudoers` autorise à exécuter.
- Le fichier sudoers lui-même n'est jamais écrit à la main par l'AJAX : il est
  copié depuis `resources/sudoers/jeedom_cloudflare`, validé par
  `visudo -cf` avant d'être installé, avec permissions `0440`.
- Le secret du tunnel (token cloudflared) ne transite jamais en argument de
  ligne de commande (`ps aux` resterait propre) ni en base Jeedom : il est
  écrit une seule fois, via le script privilégié (`--set-token`), dans
  `/etc/cloudflared-jeedom/tunnel.token` (root:root, 0600), référencé par
  `--token-file` dans l'unit systemd.

### Autres choix notables

- Un seul équipement `cloudflare` autorisé (`preInsert()`), conformément au
  positionnement "compte + tunnel unique" de la V1.
- Zone et hostname viennent uniquement de sélections UI contraintes
  (dropdown alimenté par `getZones()`), jamais d'un champ libre non revalidé
  côté serveur — le hostname est en plus revalidé (`validHostname()`) contre
  le nom de la zone sélectionnée.
- Toute méthode `cloudflareApi` timeout (5–10 s), gère 429 avec un retry
  court, distingue 401 (token invalide) de 403 (scope insuffisant), et ne
  lève jamais d'exception non catchée jusqu'au cron.
- Catégorie `security` retenue dans `info.json` (accès distant), plutôt que
  `communication`.
- Suppression de tunnel/DNS uniquement après confirmation modale affichant le
  nom exact de la ressource ; `plugin_info/install.php::cloudflare_remove()`
  ne supprime volontairement rien côté Cloudflare ni le service systemd à la
  désinstallation du plugin (évite une coupure d'accès distant surprise).

### Hors périmètre V1 (rappel)

Supervision multi-services, Cloudflare Access/MFA, Service Tokens M2M, WAF,
statistiques avancées, multi-tunnels, DNS dynamique généralisé, intégration
MCP via Access.

## V1.1

### Commandes d'action Démarrer/Arrêter
- Ajout des commandes d'action `start` et `stop` sur l'équipement, utilisables directement depuis les scénarios Jeedom (indépendamment de l'interface AJAX).
- Conformément à la V1, l'exécution (`cloudflareCmd::execute()`) déclenche un appel asynchrone (`cloudflaredShell::serviceAction()`) pour ne pas bloquer le système, l'état réel sera rafraîchi par le `cron5`.

### Alerte proactive
- Ajout d'une détection de panne prolongée via `cron5()` (et `refreshTunnelStatus()`). 
- **Mécanisme anti-flap** : le passage à l'état `degraded`, `down` ou `unknown` n'entraîne une alerte dans le Centre de messages Jeedom qu'au bout de **15 minutes** continues. Le suivi d'état (`down_since` et `down_alerted`) est stocké dans la configuration de l'équipement `eqLogic`.
- **Anti-spam et auto-résolution** : une seule alerte par panne. Dès que le statut repasse à `healthy`, l'alerte est retirée et remplacée par un message de retour à la normale pour un bon suivi utilisateur.
