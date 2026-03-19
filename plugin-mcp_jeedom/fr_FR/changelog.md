---
layout: default
title: mcp_jeedom — Changelog
lang: fr_FR
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — mcp_jeedom

<a href="https://limad.github.io/plugins-docs/plugin-mcp_jeedom">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/mcp_jeedom_icon.png" alt="mcp_jeedom icon" width="120px">
</a>

- [Présentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#presentation)
- [Documentation](https://limad.github.io/plugins-docs/plugin-mcp_jeedom#documentation)
- Changelog
- [Forum dédié](https://community.jeedom.com/tags/plugin-mcp_jeedom)

---

## 19/03/2026

**L'IA peut créer, modifier et gérer vos scénarios Jeedom** *(option à activer)*
Décrivez ce que vous voulez, l'IA s'occupe de tout : créer un scénario de présence, une alerte de température, un programme planifié avec code PHP… Il peut aussi dupliquer, exporter, importer et supprimer des scénarios existants.
Exemples : *« Crée un scénario qui éteint les lumières 30 minutes après le coucher »*, *« Duplique mon scénario Réveil et adapte-le pour le week-end »*, *« Exporte mon scénario Chauffage pour que je puisse le sauvegarder »*.

**L'IA détecte maintenant vos enceintes Alexa et autres assistants vocaux**
`list_notification_commands` reconnaît automatiquement le plugin `alexaapiv2` et les autres assistants vocaux (Google Home, Sonos…). L'IA peut donc faire parler vos enceintes directement depuis un scénario ou en réponse à une question.
Exemple : *« Crée un scénario qui annonce l'heure du dîner sur l'Echo 8 »*.

**Équipement de supervision `MCP System`**
Un nouvel équipement apparaît dans Jeedom, créé automatiquement au premier démarrage du daemon. Il expose en temps réel :
- L'état du daemon (actif / arrêté), historisé
- Le client actuellement connecté (oui/non), son IP et son nom
- Le type d'accès (local, LAN ou externe)
- Le compteur de connexions du jour, historisé
Vous pouvez utiliser ces informations dans vos scénarios Jeedom (ex. : action si Claude se connecte, alerte si le daemon s'arrête).

**Nommer vos clients IA**
Éditez le fichier `resources/data/client_names.json` pour associer une IP à un nom lisible.
Exemple : `"192.168.1.10": "Claude Desktop — Bureau"`. La modification est prise en compte immédiatement, sans redémarrer le daemon.

**La whitelist se synchronise automatiquement**
À chaque ouverture de la fenêtre de whitelist, les équipements sont automatiquement mis en adéquation avec l'état réel de Jeedom : nouveaux équipements ajoutés, équipements supprimés retirés, commandes nouvelles intégrées. Vos choix (équipements autorisés, labels, aliases) sont toujours préservés. Le bouton *Synchroniser depuis Jeedom* reste disponible pour forcer un rafraîchissement complet.

**Corrections**
- Les blocs *Commentaire* dans les scénarios créés par L'IA ne bloquent plus l'exécution.
- Le log d'exécution d'un scénario (`get_scenario_log`) est maintenant correctement lu via l'API Jeedom officielle.

---

## 17/03/2026

**Nouvel outil `jeedom_api` — opérations composites PHP**
L'IA dispose d'un accès direct à des opérations avancées impossibles via l'API standard.
Exemples d'utilisation : *« Donne-moi un bilan de santé complet »*, *« Duplique mon scénario Réveil »*, *« Crée un équipement virtuel avec une commande température »*, *« Exécute cette séquence de 5 actions avec 2 secondes entre chaque »*.

Actions disponibles :
- `healthReport` — bilan de santé global en 1 appel (équipements en erreur, batteries faibles, messages système, daemons NOK, mises à jour disponibles)
- `fullStateOptimized` — état complet de la maison avec valeurs temps réel, format optimisé pour l'IA
- `getCommandValues` — valeurs de plusieurs commandes en un seul appel
- `duplicateScenario` — copie complète d'un scénario existant
- `bulkScenarioAction` — activer, désactiver, déclencher ou arrêter tous les scénarios d'un groupe
- `saveVirtualDevice` — créer ou mettre à jour un équipement virtuel avec ses commandes
- `saveCommand` — créer ou mettre à jour une commande sur un équipement
- `removeDevice` — supprimer un équipement (confirmation obligatoire)
- `objectSummary` — résumés Jeedom agrégés (lumières allumées, volets ouverts…)
- `sequenceActions` — séquence d'actions avec délai configurable entre chacune

**Correction : `get_full_state` affiche maintenant les vraies valeurs**
Les valeurs des commandes (température, état, volume…) s'affichaient en tirets. Corrigé : le champ `state` de l'API Jeedom 4.5 est maintenant correctement lu.

**Correction : `get_plugin_status` fonctionnel**
L'état des daemons et dépendances de chaque plugin s'affiche correctement. La méthode utilisée est désormais robuste (PHP natif au lieu du RPC instable pour ces informations).

**Correction : `delete_scenario` fonctionne**
La suppression d'un scénario s'effectue maintenant correctement via PHP natif (la méthode RPC correspondante n'existe pas dans Jeedom 4.5).

**Correction : `update_scenario` ne corrompt plus le nom**
La modification d'un scénario utilise désormais `scenario::save` au lieu de `scenario::import`, évitant la corruption du nom.

**Nouveaux outils disponibles**
- `get_cron_list` — liste les 27 tâches planifiées internes Jeedom avec leur état et dernière exécution
- `list_variables` / `get_variable` / `set_variable` — les variables globales Jeedom sont maintenant accessibles (corrigé : les méthodes RPC correspondantes n'existent pas dans Jeedom 4.5)

**Autres corrections**
- `find_command` avec `generic_type` ne plante plus
- `list_logs` filtre les résultats correctement
- `list_notification_commands` ne plante plus sur les commandes Alexa TTS
- `get_dead_devices` retourne les équipements en danger/warning avec détails
- Les logs sont affichés avec les sauts de ligne corrects

---

## 16/03/2026

**L'IA peut maintenant créer et modifier vos scénarios** *(option à activer)*
Demandez à L'IA de créer un scénario de réveil progressif, une alerte présence, un programme de thermostat… Il dispose de 8 templates prêts à l'emploi, valide la structure avant d'enregistrer et demande toujours confirmation avant de supprimer.

**L'IA peut lire les pages de votre interface Jeedom** *(option à activer)*
Utile pour analyser le rendu d'un dashboard, inspecter un widget ou déboguer une page de configuration. Les accès API restent bloqués.

**Page de santé complète**
Un nouveau bouton *Santé* dans la gestion du plugin affiche en un coup d'œil : état du daemon, dépendances, transport, accès externe, permissions actives et profil maison.

**L'endpoint MCP se met à jour en direct**
Changer le transport ou le port dans la configuration met immédiatement à jour l'URL à copier dans L'IA — plus besoin de recalculer manuellement.

---

## 15/03/2026

**L'IA comprend mieux votre maison**
L'IA peut désormais voir l'état complet de la maison en un seul appel, trouver n'importe quelle commande par son rôle (`lumière`, `température`, `volet`…) sans connaître son ID, et détecter ce qui a changé depuis ce matin.

**L'IA peut voir vos caméras** *(option à activer)*
Demandez à L'IA ce qui se passe dans le salon ou si quelqu'un est dans le garage — il analyse le snapshot en direct.

**L'IA peut vous envoyer des notifications** *(option à activer)*
Via Telegram, Mail, Slack, Pushover… ou tout autre plugin notification déjà configuré dans Jeedom.

**Mode lecture seule**
Donnez à L'IA un accès consultation uniquement, sans aucun risque d'action accidentelle.

**Journal d'audit**
Chaque action réalisée par L'IA est tracée (outil, commande, résultat) dans un fichier consultable depuis la configuration.

**Profil maison**
Renseignez les prénoms des habitants, les horaires habituels et le canal de notification préféré — L'IA adapte ses réponses à votre installation.

**Transport Streamable HTTP**
Nouvelle méthode de connexion plus moderne et plus stable, activée par défaut. L'ancien mode SSE reste disponible.


- Correction des logs au démarrage du daemon.

---

## 14/03/2026

- Mise en ligne de la version bêta.