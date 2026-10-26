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

## 26/10/2026

**Nouveau modèle de rôles des tokens : Opérateur, Administrateur, Super-administrateur**

- Les tokens ont désormais 3 rôles : **Opérateur** (consulter et piloter la maison), **Administrateur** (en plus : scénarios, fichiers, système, mises à jour) et **Super-administrateur** (en plus : shell sur le serveur et redémarrages). Voir la documentation, section « Tokens et rôles ».
- Les anciens rôles « invité » et « lecture seule » sont supprimés. À la mise à jour, les tokens concernés passent Opérateur et sont **désactivés** : réactivez-les si besoin. Un token administrateur qui avait le shell devient Super-administrateur, sa liste de commandes autorisées est conservée.
- Nouveau réglage **Niveau maximum autorisé** (État seulement, Pilotage, Avancé, Complet) : il plafonne ce que les tokens peuvent faire sur la box, quel que soit leur rôle. Les capacités restent réglables une par une (niveau « Personnalisé »).
- Un changement de rôle ou la désactivation d'un token s'applique immédiatement, y compris aux clients connectés par OAuth, sans redémarrage.
- Chaque client IA ne voit plus que les outils autorisés pour son rôle.
- Une requête sans token valide est désormais refusée, même en direct sur le serveur MCP.
- Gestion des tokens repensée : tableau des droits par rôle, interrupteur activer/désactiver sur chaque carte, filtre Tous/Actifs/Désactivés, « Utilisé il y a » à jour pour les sessions OAuth.
- Page de configuration réorganisée : onglets « Capacités globales » (niveau maximum) et « Capacités des rôles » ; la bannière « redémarrer le daemon » ne s'affiche plus à tort à l'ouverture.

**Sécurité : chaque rôle reste strictement dans ses limites**

- Seul un administrateur Jeedom peut désormais autoriser un client IA par OAuth (Claude, ChatGPT, Le Chat…). Un compte Jeedom simple ne peut plus accorder un accès administrateur ou superadmin.
- Les commandes shell limitées à une liste (sur un token ou dans la configuration) ne peuvent plus être détournées pour lancer une autre commande : les caractères d'enchaînement (`;`, `&&`, `|`, `$(…)`…) sont refusés.
- Un token « operator » ne peut plus lancer d'actions de maintenance réservées aux administrateurs (nettoyage de la base, droits des fichiers, vidage du cache…) en contournant les outils prévus.
- Le mode lecture seule bloque aussi ces actions de maintenance et l'acquittement des messages.
- L'outil d'appel avancé (`rpc`) respecte maintenant les options du plugin (caméra, journaux, écriture de fichiers) et ne permet plus de redémarrer le serveur ni de créer des scénarios sans les contrôles habituels.
- Les scénarios contenant du code PHP ou une action de redémarrage/arrêt de la box sont réservés aux tokens superadmin. Les autres scénarios se créent comme avant.
- Un token « operator » ne gère plus que les équipements de la whitelist (activer, renommer, déplacer…), et la simulation de présence n'allume que les lumières autorisées. Les administrateurs gardent la main sur tous les équipements.
- Correction : l'alerte « token inutilisé depuis 30 jours » n'était jamais envoyée.
- Correction : la suppression d'un équipement par l'IA échouait toujours, même confirmée ; à l'inverse, la suppression d'un objet passait même avec `confirm=false`.
- Explorer les dossiers du serveur est maintenant réservé aux administrateurs, comme la lecture de fichiers.
- Correction : après un redémarrage demandé par l'IA, le serveur refusait toutes les connexions.
- Correction : le filtre « actifs seulement » de la liste des scénarios, et les identifiants inconnus dans les mises à jour groupées.

## 25/10/2026

**Fichiers de secrets protégés et journaux plus lisibles**

- Aucun client IA, même administrateur, ne peut plus lire ni modifier les fichiers de secrets de la box (identifiants de base de données, jetons OAuth, clés privées…).
- Le journal du serveur n'affiche plus une « connexion » à chaque appel d'outil, et suit le niveau de log réglé dans Jeedom.
- Une ligne du journal d'audit correspond toujours à une seule action.
- Correction : en mode « outils unitaires », les instructions envoyées à l'IA citaient des outils qui n'existent pas dans ce mode.

## 23/10/2026

**Sécurité renforcée : accès direct, lecture de fichiers, journaux, et nouveau guide de diagnostic**

- Une connexion directe au serveur MCP (sans passer par le proxy sécurisé) n'a plus un accès large par défaut : elle est maintenant limitée à la lecture, sans exécution.
- La lecture de fichiers reste réservée aux comptes administrateurs ; lister un dossier reste accessible à tous.
- Les connexions internes (depuis la box elle-même) ne sont plus limitées en fréquence comme les connexions externes.
- La recherche dans les journaux ne restitue plus les mots de passe, jetons ou cookies qu'elle croiserait — ils sont masqués avant d'être affichés.
- Nouveau guide interne pour produire un rapport de sécurité fiable, sans dépendre d'extensions optionnelles qui ne seraient pas installées.
- Correction : le redémarrage du serveur manquait dans l'un des deux modes de fonctionnement des outils.

## 25/09/2026

**Guide de bonnes pratiques MCP et publication d'un skill public**

- Nouvelle resource `jeedom://best_practices` : économie de tokens (`state find` vs `full`, `format=summary|compact`, réutilisation d'`etag`, lecture/exécution en masse, éviter `full_map` sauf besoin réel) et pièges de syntaxe des scénarios (`trigger_tags`, guillemets, mise en garde sur l'ancienne notation `#cmdId#` dépréciée depuis Jeedom ≥ 4.5). Contenu servi aussi via `mcp_jeedom::getMcpBestPracticesGuide()` (consommé par `ai_assistant`) et publié comme skill public pour Claude Code / Codex : [github.com/limad/mcp_jeedom_skills](https://github.com/limad/mcp_jeedom_skills)
- Suppression de `resources/data/ai_skills/*.md` (4 fichiers) : code mort jamais référencé, contenu obsolète (mentionnait PHP 8.1+, ignorait les trigger_tags)
- Correction de `EXTENSION_GUIDE.md` : le chemin documenté pour les extensions MCP tierces (`resources/mcp_extension/`) ne correspondait pas au chemin réellement scanné par le code (`plugin_info/mcp_extension/`) — un développeur tiers suivant la doc à la lettre n'était jamais découvert
- CI : plancher PHP relevé à 8.2 (Debian 12, désormais la version préconisée par Jeedom) — la matrice de lint n'inclut plus PHP 7.4
- **Alerte cron si les dépendances Python dérivent** : `requirements.txt` peut déclarer une version qu'un `pip install` n'a jamais réellement appliquée au venv (constaté : `mcp==2.1.0` déclaré depuis le 29/08 sans jamais réinstaller, resté en 2.0.0 pendant 4 semaines sans que personne ne le remarque). La détection existait déjà (`pythonRequirementsInstalled()`) mais n'était recalculée que si un admin ouvrait la modale santé/assistant ; `cronDaily()` pousse désormais un message Jeedom quotidien si l'état est `nok`
- **SDK Python `mcp` mis à jour 2.0.0 → 2.2.0**, déployé et testé en direct (pas seulement en venv jetable) : `initialize`, `tools/list`, `resources/read` vérifiés fonctionnels après redémarrage du daemon, aucune régression. Conséquence positive : le daemon négocie désormais la révision de spec MCP **2025-11-25** (vérifié empiriquement), ce qui répondait à un point resté en attente dans les notes d'intégration Alexa+ (`ALEXAPLUS_MCP.md`)

## 29/08/2026

**Analyse d'historique sur plusieurs commandes à la fois**
Les outils d'historique et de statistiques acceptent désormais une liste de commandes en un seul appel. Vous pouvez filtrer les points sur un seuil de valeur, ne garder que les changements d'état, ou agréger par heure ou par jour. Les statistiques renvoient en plus l'écart-type, la somme, la dernière valeur et l'horodatage des extrêmes.
Exemple : *« Compare les températures du salon, de la chambre et de l'extérieur sur les 7 derniers jours, en moyenne journalière »*.

**Détection des références de commandes supprimées**
Un nouvel outil de diagnostic liste les références `#cmdId#` restées dans vos scénarios, objets, vues, designs et interactions après la suppression d'une commande ou d'un équipement. Le nombre de références mortes est également remonté dans le diagnostic système. Un second outil réécrit ces références vers une commande valide, en lot, avec une simulation préalable avant d'appliquer (réservé à l'administrateur).
Exemple : *« Quelles commandes supprimées sont encore référencées quelque part ? »*.

**Recherche « où est-ce utilisé ? »**
Demandez à l'IA où une commande ou un équipement est employé : elle parcourt les scénarios (déclencheurs et expressions), les vues, les designs et la configuration des autres commandes. Une recherche par texte libre dans les expressions de scénario est aussi possible.
Exemple : *« Dans quels scénarios la température de la chaudière est-elle utilisée ? »*.

**Redémarrage automatique du daemon à la mise à jour**
Après une mise à jour du plugin, le daemon est relancé automatiquement pour charger le nouveau code, à condition qu'il tournait déjà (ou que le démarrage automatique soit activé) et que les dépendances soient prêtes. Plus besoin de le redémarrer à la main dans la plupart des cas.

**Correctifs**
Les outils d'analyse des logs, de nettoyage des logs et d'occupation disque ciblaient un mauvais dossier et ne trouvaient aucun fichier : ils sont de nouveau fonctionnels. Les valeurs d'historique à forte précision (`49.000000000`) sont affichées proprement (`49`).

---

## 19/08/2026

**Migration vers le SDK MCP 2.0**
Le daemon Python s'appuie désormais sur la version 2.0 du SDK officiel MCP. Le bridge `uvx mcp-proxy` utilisé par certains clients ne supportant pas encore le HTTP natif (JetBrains, Google Antigravity) a été corrigé par épinglage de version.

**Nettoyage de l'authentification externe**
L'ancien jeton d'accès externe unique a été supprimé : seuls subsistent les jetons multi-clients (avec rôles) et OAuth 2.0. L'ancien stockage des jetons sous forme de fichier est migré automatiquement et définitivement vers la base de données Jeedom lors de la mise à jour, de façon transparente.

---

## 08/07/2026

**Scénarios multi-déclencheurs avec les tags Jeedom ≥ 4.5**
L'IA peut désormais générer des conditions de scénario basées sur `#trigger_id#`, `#trigger_name#`, `#trigger_value#` et `#trigger#`, qui remplacent les fonctions dépréciées `trigger()`/`triggerId()`/`triggerValue()`. Deux nouvelles actions dans `scenario_write` : une condition unique prête à coller, ou un squelette if/elseif complet routant automatiquement sur plusieurs commandes déclenchantes. Une syntaxe dépréciée détectée dans un scénario importé ou modifié déclenche désormais un avertissement (sans bloquer l'opération).
Exemple : *« Crée un scénario qui réagit différemment selon que ce soit l'interrupteur du salon ou celui de la cuisine qui a déclenché »*.

**Nouvel Assistant de configuration**
Un assistant en 5 étapes s'ouvre automatiquement à la première utilisation du plugin : vérification de la santé du daemon, choix d'un profil d'usage (Consultation / Pilotage / Avancé), génération de la whitelist, création du premier jeton d'accès et configuration du client IA. Les réglages à risque (exécution locale, écriture de fichiers, redémarrage) restent toujours à activer manuellement, quel que soit le profil choisi.

**Vues et tableaux de bord pilotables par l'IA**
Demandez à l'IA de créer un dashboard, d'y ajouter des zones de widgets ou des tableaux de commandes, ou de convertir la tuile d'un équipement en tableau compact. Elle peut aussi lister vos vues et plans 2D (designs) existants.
Exemple : *« Crée un dashboard Salon avec un tableau regroupant les lumières et volets »*.

**Contexte IA allégé**
Le format de la carte complète de la maison (`jeedom://full_map`) a été compacté (près de deux tiers de tokens en moins), et les instructions internes envoyées à l'IA ont été simplifiées, pour une prise en main plus rapide et moins coûteuse en tokens, notamment pour les longues conversations.

---

## 06/06/2026

**Installation plus simple : suppression de la dépendance Composer**
Le plugin n'a plus besoin d'installer des dépendances PHP via Composer. Les éléments nécessaires ont été intégrés directement dans le plugin, ce qui rend l'installation et l'activation plus fiables, notamment sur les Jeedom où Composer n'est pas disponible ou pas maintenu à jour.

**Profil maison généré automatiquement depuis Jeedom**
Le fichier `home_profile.md` peut maintenant être pré-rempli depuis l'installation Jeedom. Le plugin conserve les sections personnelles déjà saisies, puis ajoute la structure des pièces visibles et les plugins actifs pour donner plus de contexte à l'IA.

**Nouveaux prompts MCP intégrés**
Trois nouveaux prompts sont disponibles côté MCP : aide à la création de scénarios Jeedom, rapport complet des batteries et audit des plugins. Ils guident l'IA étape par étape et privilégient les outils Jeedom adaptés.

**Corrections de l'interface de configuration**
La page de configuration charge correctement la classe du plugin, ne dépend plus de jQuery pour ses actions internes récentes et corrige plusieurs cas où les blocs dépendances ou démon pouvaient rester vides côté Jeedom.

**Correctifs daemon et maintenance**
Les informations de dépendances Python sont de nouveau calculées correctement après la suppression de Composer. Le proxy, les prompts MCP et plusieurs fichiers internes ont été synchronisés avec l'état stable du plugin.

---

## 18/04/2026

**L'IA devient experte de votre Jeedom grâce aux "skills"**
Quatre guides spécialisés sont désormais embarqués dans le plugin : scénarios experts, analyse historique, gestion énergie et interaction avec l'utilisateur. Avant de répondre ou d'agir, l'IA consulte automatiquement le guide adapté à votre demande.
Résultat : créations de scénarios plus fiables, analyses de consommation plus pertinentes, moins de réponses "hors-sol".

**D'autres plugins Jeedom peuvent exposer leurs propres outils MCP**
Un mécanisme d'extension permet aux développeurs d'autres plugins d'ajouter leurs outils au client MCP sans modifier `mcp_jeedom`. Il suffit de déposer deux fichiers dans le plugin tiers (`mcp_tools.json` + `McpExtension.php`) et les outils apparaissent automatiquement avec le préfixe `ext__{plugin}__{outil}`.
Exemple d'usage : un plugin météo pourrait exposer `ext__meteo__get_forecast` que l'IA utiliserait directement.

**L'IA connaît les types génériques Jeedom**
Elle dispose d'une documentation intégrée sur les types de commandes (`info`, `action`, `numeric`, `binary`…) et d'équipements (`light`, `heating`, `presence`…). Elle choisit la bonne catégorie lorsqu'elle crée ou modifie des équipements virtuels.

**Refonte complète de l'interface administrateur**
Les pages Configuration, Tokens, Clients, Whitelist et Santé ont été entièrement réécrites pour plus de clarté : meilleur affichage mobile, tableaux plus lisibles, actions mieux regroupées, messages d'erreur explicites.

**Code interne réorganisé (invisible pour vous, mais plus robuste)**
Le cœur du plugin a été découpé en modules spécialisés (scénarios, équipements, santé, administration côté PHP ; références, outils intelligents, extensions côté Python). Les futures évolutions et correctifs seront plus rapides, avec moins de risque de régression.

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
