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

## 01/11/2026

**Nouveau : digest quotidien des anomalies**

- Onglet « Personnalisation IA » : le plugin peut vous envoyer chaque jour la liste des anomalies de la box (batteries faibles ou critiques, équipements en alerte, valeurs hors plage). Désactivé par défaut.
- Gratuit et sans IA : la liste est calculée par Jeedom. Rien n'est envoyé s'il n'y a aucune anomalie et, si vous le souhaitez, seulement quand la liste a changé depuis le dernier envoi.
- Envoi par la commande de notification de votre choix, ou dans le centre de messages Jeedom.
- En option, une synthèse IA regroupe les anomalies par cause probable (par exemple plusieurs capteurs d'un même plugin hors ligne : passerelle en panne). Un appel par jour au maximum, uniquement s'il y a des anomalies nouvelles (environ 1 à 2 k tokens), via l'intégration AI Assistant. Les noms des pièces et des équipements concernés sont transmis au provider choisi.
- Nouveau réglage **Plafond d'appels IA automatiques par jour** (20 par défaut, 0 = aucun) : il limite toutes les fonctions IA qui se déclenchent sans action de votre part. Les générations lancées depuis la page de configuration ne sont pas comptées.

**Recherche d'équipements plus tolérante**

- Quand une IA cherche une commande par nom d'équipement (`find_command`) et que la recherche exacte ne trouve rien, le plugin tente une correspondance approchée : accents, pluriels et petites fautes de frappe sont tolérés (« lumieres salon » trouve « Lumière Salon »). Sans IA, rien à configurer.
- Une recherche de température trouve aussi les sondes de thermostat, la température extérieure et la météo (même chose pour l'humidité et la pression).
- Si rien ne correspond au nom d'un équipement, les mots sont aussi cherchés dans les noms des commandes et des pièces : « température extérieure » trouve la commande du même nom, même si l'équipement s'appelle autrement.
- La liste des caméras indique le dernier événement de chacune (par exemple la dernière personne vue). Une question sur le passé n'oblige plus l'IA à analyser l'image, qui ne montre que l'instant présent.

**Réponses plus légères pour les IA**

Les réponses des outils coûtent moins de tokens à chaque client IA, sans réponse perdue sur le jeu de 22 questions de référence (11 % de tokens consommés en moins sur ce jeu).

- `get_full_state` : le format `compact` ne garde que les informations utiles (8,7 k → 3,3 k tokens sur une box de test) ; le format `summary` inclut la consigne des thermostats.
- Format texte (état complet, état d'une pièce, fiche d'un équipement, listes d'équipements) : jusqu'à 57 % de texte en moins (40 % pour l'état complet). L'aide d'un type de commande n'apparaît qu'une fois par réponse, et les valeurs très longues (historique caméra, JSON, adresses signées) sont tronquées, avec leur taille et l'appel qui donne la valeur complète.
- Messages système (`get_system_messages`) : les messages répétés d'un même plugin (même texte à la date ou au code d'erreur près) n'apparaissent qu'une fois, avec leur nombre ; près de deux fois moins de texte sur une box de test.
- La description de `get_full_state` oriente les IA vers les outils ciblés (`find_command`, `search_devices`) pour une question précise, au lieu de charger tout l'état de la maison.
- Connexion d'un client plus rapide : la réponse d'ouverture de session passe de 136 Ko à 4 Ko (l'icône du serveur est fournie par une adresse au lieu d'être incluse).
- **Corrigé** : dans `get_full_state` (formats `summary` et `compact`), les IA prenaient parfois l'identifiant d'un équipement pour celui d'une commande, d'où une erreur et un appel d'outil inutile. Il est maintenant étiqueté `eq_id`.

**Corrections de filtres**

Cinq outils perdaient le filtre demandé par l'IA :

- **Statut des équipements** (`get_devices_status`) : renvoyait tous les équipements de la box, y compris ceux hors liste blanche, au lieu de ceux demandés. Il ne renvoie plus que les équipements autorisés, et refuse les autres.
- **Mises à jour par lot** : demandées pour quelques plugins, elles étaient appliquées à **tous** les plugins (après confirmation).
- **Simulation de présence** et **résumé des pièces** : agissaient sur toutes les pièces au lieu de celles demandées.
- **Planning des modes** : le planning envoyé n'était pas transmis au scénario.
- Effet de bord : une question comme « y a-t-il des alertes SMART ? » coûte nettement moins de tokens et ne tourne plus en boucle.

**OAuth MCP**

- **Corrigé** : Claude (`claude.ai`), Le Chat (`callback.mistral.ai`, et `console.mistral.ai` pour le débogueur de connecteurs Mistral) et ChatGPT (`chatgpt.com`) sont toujours acceptés comme adresses de retour OAuth. Une configuration enregistrée avant l'ajout de l'un de ces clients pouvait bloquer son enregistrement. Le champ **Redirect hosts autorises** ne sert plus qu'aux hôtes d'autres clients.

**Tokens**

- Fenêtre de création d'un token : le détail des permissions du rôle est replié par défaut ; un clic sur la description du rôle l'affiche.

**Traductions**

- **Corrigé** : en anglais, l'onglet « Personnalisation IA » affichait la description de l'option shell au lieu de son nom, et trois textes voisins étaient décalés de la même façon.
- Une quarantaine de textes récents (digest des anomalies, plafond d'appels IA, capacités des rôles, avertissements OAuth) sont maintenant traduits dans toutes les langues.

## 30/10/2026

**Préparation pour Alexa+**

- Le plugin est prêt pour les futurs modules complémentaires d'Alexa+ (programme Amazon en avant-première, pour l'instant réservé à des partenaires sélectionnés). Alexa pourra découvrir les outils de votre box avant que vous liiez votre compte, puis vous demander de le lier au premier usage.
- Tant que le compte n'est pas lié, Alexa voit la liste des outils mais ne peut rien lire ni piloter dans la maison.
- **Corrigé** : une des adresses de découverte OAuth utilisées par certains clients répondait « introuvable » sur une box Jeedom standard.
- Rien ne change pour les clients déjà connectés (Claude, ChatGPT, Le Chat…).

## 29/10/2026

**Configuration requise : Jeedom 4.6 et Debian 12**

- Le plugin nécessite désormais **Jeedom 4.6** ou plus récent.
- **Debian 11 n'est plus pris en charge** (fin de vie) : passez à **Debian 12** ou plus récent. Sur Debian 11, l'installation des dépendances peut échouer, car les paquets de sécurité de Debian 11 ne sont plus sur les dépôts habituels. Un message vous prévient à l'installation et à la mise à jour ; le plugin continue de fonctionner.

**Accès externe : codes d'erreur standard**

- Un token invalide, désactivé ou expiré reçoit maintenant une réponse **401** avec `error="invalid_token"` (au lieu de 403), comme le prévoit la norme OAuth. Un client connecté par OAuth (Claude, ChatGPT, Le Chat…) dont le jeton a expiré ou dont le token Jeedom a été désactivé sait ainsi qu'il doit se réautoriser.
- Le 403 reste réservé aux cas où l'accès externe n'est pas configuré (aucun token, ou OAuth désactivé).
- **Corrigé** : sur une box sans accès externe configuré, l'adresse de découverte OAuth envoyée aux clients en réseau local était incomplète (`http:/plugins/…`, sans le nom de la box).

- **Corrigé** : juste après le démarrage de la box, le daemon pouvait ignorer les outils d'extension des autres plugins pendant 30 secondes.

**Page de configuration**

- Les onglets apparaissent dans l'adresse de la page (`#tab-…`) : vous pouvez enregistrer ou partager un lien direct vers un onglet, et l'onglet reste affiché après un rechargement.
- Onglet « Capacités des rôles » : le bouton **Voir les capacités** de chaque rôle ouvre la liste des outils et actions que ce rôle peut utiliser. Elle tient compte de vos réglages : une action bloquée par une option désactivée ou par le mode lecture seule est barrée, avec la raison.
- Le rôle « Lecture seule » devient **Utilisateur limité**, pour ne plus le confondre avec le **mode lecture seule** de la box (qui bloque toutes les actions de tous les tokens). Ses droits ne changent pas.
- L'onglet « Avancé » est fusionné dans « Démarrage » (transport, port, mode d'outils).
- OAuth MCP : un avertissement rappelle qu'un client distant peut accéder à votre maison à tout moment et que son token est stocké chez le fournisseur. À réserver aux clients de confiance et aux vrais besoins.
- Intégration AI Assistant : les options restent visibles mais grisées, avec un message, quand le plugin AI Assistant est absent ou désactivé.
- Fenêtres Tokens, Clients et Whitelist plus larges ; interrupteurs des tokens plus lisibles, y compris à l'état désactivé.
- Page du plugin : les tuiles Documentation et Community passent en dernier.

**Traductions**

- Interface entièrement traduite en anglais, allemand, espagnol, italien et portugais (environ 170 textes s'affichaient en français).
- La durée « il y a … » (dernière utilisation d'un token) et l'aide « Entrée pour ajouter » de la whitelist se traduisent correctement.

## 28/10/2026

**Sécurité : transports SSE (legacy) et STDIO retirés**

- **Corrigé (grave), installations réglées en transport SSE uniquement** : les rôles des tokens n'y étaient pas appliqués, et tout token agissait comme un Super-administrateur. Le transport Streamable HTTP, réglage par défaut, n'était pas concerné.
- **Les transports SSE (legacy) et STDIO sont retirés.** Seul Streamable HTTP reste. À la mise à jour, une configuration en SSE ou en STDIO passe automatiquement en Streamable HTTP et un message vous prévient. Le démon est relancé par Jeedom si sa gestion automatique est activée ; sinon, il reste arrêté comme vous l'avez choisi.
- **Mise à jour du plugin** : c'est Jeedom qui relance le démon, selon votre réglage de gestion automatique. Le plugin ne force plus de redémarrage (ce qu'annonçait l'entrée du 23/10).
- **Clients configurés avec `mcp-proxy --sse-url …`** : remplacez leur configuration par celle de la fenêtre **Clients** du plugin (Streamable HTTP, ou pont `mcp-proxy --transport streamablehttp` / `mcp-remote`).
- Défense en profondeur : une requête dont le rôle n'est pas connu n'a plus aucun droit (aucun outil listé, tout appel refusé).
- Un refus lié à la liste blanche (équipement ou commande non autorisé) est maintenant signalé comme une erreur au client IA. `execute_action_by_name` ne révèle plus l'existence d'un équipement hors liste blanche.

**Auto-diagnostic et corrections trouvées par les nouveaux tests**

- **Nouveau : auto-diagnostic** (bouton « Auto-diagnostic » de la fenêtre Santé, et action `system` → `selftest` pour un token Administrateur). Il vérifie la version du SDK MCP, la cohérence des rôles, les droits, les schémas des outils, les extensions des plugins tiers, le daemon et les dossiers de sauvegarde et de journaux.
- **Corrigé : liste des sauvegardes vide** (`system` → `backup_list`) quand le dossier des sauvegardes est relatif (cas par défaut). La restauration par nom de fichier était refusée pour la même raison.
- **Corrigé : journal d'exécution d'un scénario toujours vide.**
- **Corrigé : la simulation de présence n'éteignait pas les lumières** à l'arrêt.
- **Corrigé : taille du journal d'audit toujours à 0** dans la fenêtre Santé.
- **Sécurité, extensions de plugins tiers** : un outil qui déclare `readOnlyHint` autrement que par le booléen `true` (par exemple la chaîne `"false"`) est traité comme une écriture, donc réservé à l'Administrateur. Les outils mal déclarés sont écartés ou corrigés, avec un avertissement dans le journal.
- Les erreurs internes qui passaient inaperçues sont maintenant journalisées.

**Rôles recentrés : l'Opérateur pilote, l'Administrateur configure**

- **Opérateur** : consulte et pilote la maison (équipements autorisés, scénarios lancer/arrêter, modes, présence, notifications, variables). Il ne modifie plus la configuration de Jeedom.
- Passent **Administrateur** : pièces et équipements (créer, renommer, déplacer, désactiver, supprimer), crons, activer/désactiver un scénario, supprimer une variable, acquitter les messages, vues et designs, planning des modes.
- Passent **Super-administrateur** (équivalents au shell) : écriture de fichiers et nettoyage des journaux, restauration d'une sauvegarde, appels API bruts (`rpc`) en écriture.
- Les variables internes du plugin (`home_mode`, `mcp_…`) ne sont modifiables que par un Administrateur ; l'Opérateur change de mode par `home` → `mode`.
- **Vérifiez vos tokens Opérateur** : s'ils servaient à ces actions de configuration, passez-les Administrateur.

**Retour du rôle « Lecture seule »**

- Nouveau rôle de token **Lecture seule** : le client consulte la maison (états, historiques, journaux et caméras si autorisés) mais ne peut rien piloter ni modifier. Idéal pour un assistant vocal économique à côté d'un assistant Opérateur.
- La restriction est appliquée par le plugin, pas par l'IA, et vaut aussi pour les clients connectés par OAuth (rôle relu à chaque requête).
- Un ancien token « lecture seule » désactivé par la mise à jour du 26/10 peut maintenant repasser en Lecture seule depuis son menu, **avant** d'être réactivé (le changement de rôle est possible sur un token désactivé).

**Chaque client ne voit que ce qu'il peut faire**

- La liste des outils envoyée à l'IA ne contient plus les actions refusées à son rôle (ni en mode lecture seule) : actions, description et paramètres sont réduits. Moins de tokens consommés, plus de tentatives inutiles. Reconnectez le client après un changement de rôle pour actualiser sa liste.

**Sécurité**

- `interactions` → `test` exécute réellement l'interaction : réservé à l'Administrateur et bloqué en lecture seule.
- `notify` → `send` n'accepte plus que les commandes de notification des équipements autorisés (whitelist).
- `camera` → `send`, `execute_action_by_name`, `devices` → `refresh` et les outils d'extension en écriture sont bloqués en lecture seule, côté daemon et côté PHP.

**Outils d'extension (autres plugins, ex. Alexa)**

- Ils n'étaient plus chargés : c'est corrigé.
- Les outils qui ne font que lire (ex. lister les appareils Alexa) sont ouverts à tous les rôles. Ceux qui agissent (faire parler, annoncer, commande vocale, routine…) sont **réservés à l'Administrateur** : ils échappent à la liste des équipements autorisés.

**Intégration AI Assistant : autorisation explicite**

- Nouvelle section « Intégration AI Assistant » (onglet Personnalisation IA) : case **Autoriser le plugin MCP à utiliser AI Assistant**, **désactivée par défaut**, et choix de l'**équipement AI Assistant** (par défaut : le provider par défaut d'AI Assistant).
- Tant qu'elle n'est pas cochée, rien n'est envoyé à AI Assistant : description des caméras, génération du profil maison, des libellés, alias, types génériques et du planning des modes. **Après la mise à jour, cochez-la si vous utilisiez ces générations.**

**Description texte des caméras (mode vocal de Claude)**

- En mode vocal, l'app Claude ne montre pas au modèle les images renvoyées par un outil : Claude reçoit la capture mais n'en voit pas le contenu. Nouvelle action `camera` → `describe` (outil `describe_camera` en mode legacy) : l'image est décrite côté Jeedom et Claude reçoit un texte qu'il peut lire à voix haute.
- Fonctionne pour toutes les caméras déjà prises en charge, Netatmo compris.
- Nécessite le plugin **AI Assistant** et un équipement qui lit les images. Chaque description est facturée par ce provider.
- Réglage « Description texte des caméras », activé par défaut, sous l'option Caméras de la configuration.
- Le mode texte ne change pas : `snapshot` renvoie toujours l'image.
- Les générations via AI Assistant (profil maison, libellés, alias, types génériques, planning des modes) ne gardent plus d'historique entre deux appels et ne peuvent plus déclencher de commande Jeedom.

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
- Journaux allégés : en niveau « info », le serveur n'écrit plus qu'une ligne au démarrage et les événements utiles (shell exécuté, sessions OAuth ouvertes, actions d'administration, erreurs). Le détail des appels d'outils, des requêtes HTTP vers Jeedom et du démarrage passe en « debug ».

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
