---
layout: default
title: Passerelle OAuth IA — Documentation
lang: fr_FR
pluginId: ai_proxy
---

# Description

Le plugin **Passerelle OAuth IA** (`ai_proxy`) installe sur la box un service local qui se connecte à vos **abonnements IA** existants (connexion OAuth, comme les outils officiels) et les expose sous forme d'une **API locale standard**, compatible OpenAI et Anthropic.

Les autres plugins IA de la box (par exemple `code_assistant`) et vos outils sur le réseau local peuvent ainsi utiliser les quotas inclus dans vos abonnements, sans clé API facturée à l'usage.

Fournisseurs pris en charge :

- **Claude** (Anthropic)
- **Codex / ChatGPT** (OpenAI)
- **Cursor**
- **Antigravity** (Google)
- **Gemini CLI** (Google)
- **GitHub Copilot**
- **Mistral Vibe** (clé du CLI Vibe, enveloppe mensuelle « Vibe »)

Formats d'API exposés :

| Format | Adresses |
|---|---|
| OpenAI | `/v1/chat/completions`, `/v1/responses`, `/v1/responses/compact`, `/v1/models` |
| Anthropic | `/v1/messages`, `/v1/messages/count_tokens` |
| Gemini natif | `/v1beta/models/<modèle>:generateContent`, `:streamGenerateContent`, `:countTokens`, `/v1beta/models` |
| Ollama (sur option) | `/api/chat`, `/api/generate`, `/api/tags`, `/api/show`, `/api/version` |
| Propres à Mistral | `/v1/fim/completions`, `/v1/embeddings`, `/v1/ocr`, `/v1/audio/transcriptions` |

Chaque format peut être utilisé avec n'importe quel compte connecté : le service fait la traduction.

> **Important**
>
> - Le plugin ne fournit **aucun abonnement** : il faut au moins un compte actif chez un fournisseur pris en charge.
> - Ce n'est ni une interface de discussion ni un agent IA : c'est une passerelle technique. Il ne crée aucun équipement ni aucune commande de scénario.
> - Utiliser un abonnement depuis un logiciel tiers peut être contraire aux conditions de certains fournisseurs (notamment Antigravity et Gemini CLI) : il existe un risque de suspension du compte. À vous d'en juger.

# Prérequis

| Composant | Version |
|---|---|
| Jeedom | ≥ 4.5 |
| Debian | ≥ 11 |
| Node.js | ≥ 22 (installé automatiquement par les dépendances si absent ou trop ancien) |

# Installation

1. Installez et activez le plugin.
2. Dans la page de configuration du plugin (**Plugins > Gestion des plugins > Passerelle OAuth IA**), lancez les **Dépendances** si l'installation n'a pas démarré seule.
3. L'installation vérifie Node.js, télécharge la version vérifiée du service, crée sa configuration et génère la **clé API** de la passerelle.

Lors d'une mise à jour, la version précédente du service est conservée : si la nouvelle ne démarre pas, l'ancienne est restaurée automatiquement.

## Démarrage du démon

Tant qu'aucun compte n'est connecté, le démon ne démarre pas et affiche « Aucun compte connecté : le daemon démarrera après une première connexion ». C'est normal.

Après la connexion d'un premier compte (voir [Ajouter un compte](#ajouter-un-compte)), le démon se démarre depuis la page de configuration, la fenêtre **Comptes** ou la fenêtre **Santé**. La gestion automatique de Jeedom le maintient ensuite en marche.

Par défaut, il écoute uniquement sur la box elle-même : `http://127.0.0.1:8318`.

# Configuration

La page de configuration compte quatre sections. La plupart des réglages demandent de **sauvegarder puis redémarrer le démon**.

## Daemon (service local)

| Option | Défaut | Effet |
|---|---|---|
| Port du daemon | `8318` | Port local du service (1024 à 65535, différent du port HTTPS LAN). Les plugins Jeedom le trouvent seuls. |
| Clé API | générée à l'installation | Exigée à chaque appel. Boutons **Afficher / masquer**, **Copier** et **Régénérer**. Régénérer invalide l'ancienne clé tout de suite et redémarre le démon : mettez à jour vos clients externes ; les plugins Jeedom suivent seuls. |

## Accès depuis le réseau local (LAN)

Par défaut, seule la box peut joindre la passerelle. Cette section l'ouvre à d'autres machines (Claude Desktop, Open WebUI, éditeurs de code…). La clé API reste obligatoire dans tous les cas.

| Option | Défaut | Effet |
|---|---|---|
| Exposition sur le réseau local | Non exposé (cette box uniquement) | Mode d'ouverture, voir ci-dessous |
| Port HTTPS LAN | `30129` | Port d'écoute en mode HTTPS (différent du port du daemon) |
| IP autorisées (liste blanche) | vide (aucune restriction) | Adresses IPv4/IPv6 ou plages CIDR autorisées, une par ligne. La box elle-même est toujours autorisée ; les autres adresses reçoivent une erreur 403. |

Les trois modes :

- **Non exposé (cette box uniquement)** : aucune autre machine ne peut joindre la passerelle.
- **HTTPS (via Apache)**, recommandé : à l'enregistrement, et après confirmation, le plugin ajoute une configuration au serveur web Apache de la box, la teste puis recharge Apache. Le certificat de la box Jeedom est utilisé. En cas d'échec du test, l'état précédent est restauré. Cette configuration est retirée si vous changez de mode ou désinstallez le plugin.
- **HTTP direct (non chiffré)** : le démon écoute directement sur l'adresse de la box ; la clé API circule en clair sur le réseau. Renseignez la liste blanche d'IP.

Ne jamais exposer la passerelle directement sur Internet.

## Choix du compte

Utile quand un fournisseur a plusieurs comptes connectés.

| Option | Défaut | Effet |
|---|---|---|
| Stratégie | Rotation collante | **Rotation collante** : le même compte pendant 20 à 60 minutes, puis le suivant. **Premier compte d'abord** : toujours le premier compte disponible (ordre alphabétique de l'adresse), les autres restent intacts tant qu'il n'est pas en pause. |
| Exceptions par fournisseur | vide | Une ligne `fournisseur=stratégie`, par exemple `codex=fill-first`. Fournisseurs : `anthropic` (ou `claude`), `codex`, `copilot`, `cursor`, `gemini`, `mistral` ; stratégies : `sticky` ou `fill-first`. |

## Modèles et compatibilité

| Option | Défaut | Effet |
|---|---|---|
| Alias de modèles | vide | Une ligne `alias=modèle` (exemple : `claude-gpt-5.5=gpt-5.5`). L'alias apparaît dans la liste des modèles et la réponse le reprend. Utile pour les clients qui n'acceptent que des noms commençant par `claude-`. |
| Modèles listés (/v1/models) | vide (tous) | Un motif par ligne : `*` remplace n'importe quelle suite de caractères, `!` en tête exclut. Le bouton **Choisir dans la liste** ouvre une sélection par fournisseur (démon démarré). Masquer un modèle ne l'interdit pas : il reste utilisable si un client le demande par son nom. |
| API compatible Ollama | désactivée | Active les adresses au format Ollama. La clé API reste obligatoire. |

# Utilisation

La page du plugin (**Plugins > Programmation > Passerelle OAuth IA**) propose les tuiles **Configuration**, **Santé**, **Comptes**, **Modèles**, **Capacité**, **Aide Clients** et **Documentation**.

## Ajouter un compte

Ouvrez **Comptes**. En haut, l'état du démon et les boutons **Rafraîchir**, **Démarrer** et **Arrêter**.

Dans **Connecter un compte**, choisissez le fournisseur puis cliquez sur **Connecter**. La suite dépend du fournisseur.

**Claude, Codex, Antigravity, Gemini CLI** (adresse de retour à recopier)

1. Cliquez sur **Ouvrir** (ou **Copier** le lien) et connectez-vous chez le fournisseur.
2. Après l'autorisation, le navigateur tente d'ouvrir une adresse du type `http://localhost:<port>/callback?code=...`. S'il n'est pas sur la box, il affiche une erreur de connexion : c'est normal.
3. Copiez l'adresse complète de la barre du navigateur, collez-la dans le champ prévu de la fenêtre Jeedom, puis cliquez sur **Valider**.

**Cursor** (attente automatique)

1. Cliquez sur **Ouvrir** et validez la connexion sur le site de Cursor.
2. La fenêtre affiche « En attente du token… » puis enregistre le compte seule.

**Mistral Vibe** (attente automatique)

1. Cliquez sur **Ouvrir** : la page Mistral « Autoriser Mistral Vibe Code ? » s'affiche.
2. Cliquez sur **Autoriser**. La fenêtre Jeedom enregistre le compte seule.
3. Mistral fournit une clé longue durée, sans renouvellement : la colonne **Expire** affiche une date lointaine.

Si la connexion par le navigateur échoue, le champ **… ou colle une clé Vibe** permet d'enregistrer la clé obtenue avec la commande `vibe --setup` du CLI Mistral. Elle est vérifiée auprès de Mistral avant d'être enregistrée.

**GitHub Copilot** (code d'appareil)

1. La fenêtre affiche une adresse GitHub et un code à usage unique.
2. Cliquez sur **Copier le code**, puis sur **Ouvrir**, et saisissez le code sur la page GitHub.
3. Le compte est ajouté dès que GitHub confirme.

Vous pouvez connecter **plusieurs comptes** d'un même fournisseur : la passerelle les répartit selon la [stratégie choisie](#choix-du-compte) et bascule sur un autre compte si l'un est temporairement limité. Pour retirer un compte, cliquez sur la corbeille de sa ligne. Le démon recharge la liste sans coupure.

## Utiliser la passerelle depuis un client

Copiez la clé API depuis la page de configuration (bouton **Copier**). Dans les exemples, elle est remplacée par `sk-xxxx`.

| Emplacement du client | Format OpenAI | Format Anthropic |
|---|---|---|
| Sur la box Jeedom | `http://127.0.0.1:8318/v1` | `http://127.0.0.1:8318` |
| Réseau local, mode HTTPS | `https://<IP_DE_LA_BOX>:30129/v1` | `https://<IP_DE_LA_BOX>:30129` |
| Réseau local, mode HTTP direct | `http://<IP_DE_LA_BOX>:8318/v1` | `http://<IP_DE_LA_BOX>:8318` |

Les clients OpenAI attendent `/v1` à la fin de l'adresse ; les clients Anthropic et Gemini utilisent l'adresse sans `/v1`.

Les plugins Jeedom compatibles de la même box récupèrent l'adresse et la clé seuls.

Lister les modèles disponibles pour vos comptes :

```bash
curl -s http://127.0.0.1:8318/v1/models \
  -H "Authorization: Bearer sk-xxxx"
```

Appel au format OpenAI :

```bash
curl -s http://127.0.0.1:8318/v1/chat/completions \
  -H "Authorization: Bearer sk-xxxx" \
  -H "Content-Type: application/json" \
  -d '{"model":"gemini-2.5-flash","messages":[{"role":"user","content":"Bonjour"}]}'
```

Appel au format Anthropic :

```bash
curl -s http://127.0.0.1:8318/v1/messages \
  -H "x-api-key: sk-xxxx" \
  -H "anthropic-version: 2023-06-01" \
  -H "Content-Type: application/json" \
  -d '{"model":"ag/claude-sonnet-5-5-medium","max_tokens":64,"messages":[{"role":"user","content":"Bonjour"}]}'
```

Les noms de modèles sont des exemples : utilisez ceux que renvoie `/v1/models` pour vos comptes.

### Préfixes de modèles

Quand plusieurs fournisseurs servent un modèle du même nom, un préfixe impose le fournisseur :

- `ag/<modèle>` : **Antigravity**. Obligatoire pour les modèles Claude et GPT-OSS d'Antigravity ; les modèles Gemini fonctionnent avec ou sans.
- `at/<modèle>` : **Claude** (Anthropic) natif.
- `cr/<modèle>` : **Cursor**.
- `mistral/<modèle>` : modèles Mistral hors familles principales (`labs-*`, modèles partenaires). Les familles `mistral-*`, `codestral-*`, `magistral-*`, `ministral-*`, `devstral-*`, `pixtral-*`, `voxtral-*` s'appellent sans préfixe.

### Aide Clients

La fenêtre **Aide Clients** fournit des blocs de configuration prêts à copier pour : plugins Jeedom, Claude Code, Claude Desktop (mode Gateway), SDK Anthropic, Codex CLI, Cline / Roo Code / Continue, Open WebUI / LibreChat / LobeChat, Aider, SDK OpenAI, clients Ollama et tests rapides avec `curl`.

La plupart des outils qui acceptent une « API compatible OpenAI » avec une adresse personnalisée devraient fonctionner, mais tous n'ont pas été testés. Un client Ollama qui ne sait pas envoyer de clé API sera refusé : utilisez plutôt son mode compatible OpenAI.

### Fonctions particulières

- **Embeddings** : avec un compte Mistral Vibe (`mistral-embed`, `codestral-embed`) ou GitHub Copilot (`text-embedding-3-small`, `text-embedding-ada-002`). Pas d'embeddings pour Claude, Codex, Cursor ni Gemini, ni au format Ollama.
- **Mistral** : complétion de code (`codestral-latest`), lecture de documents (`mistral-ocr-latest`) et transcription audio (`voxtral-mini-latest`), au format de l'API Mistral.
- **Modèles Mistral à raisonnement** (`mistral-large-4`, `magistral-*`) : la réflexion est renvoyée à part de la réponse.
- **Cache de prompt** : pour Claude, la passerelle active elle-même le cache quand un prompt revient, même si le client ne le demande pas.
- La gestion de modèles locaux Ollama (téléchargement, suppression…) n'est pas prise en charge.

## Quotas

La fenêtre **Comptes** affiche pour chaque compte son offre (**Plan**), sa disponibilité, l'expiration de son jeton et la colonne **Quota utilisé**. Les quotas sont relus chez les fournisseurs, en lecture seule, au plus une fois par minute (**Rafraîchir** force la relecture). Le survol d'une barre indique le temps restant avant sa remise à zéro.

| Fournisseur | Plan affiché | Fenêtres de quota |
|---|---|---|
| Claude | Pro, Max 5x, Max 20x, Team… | 5 h et 7 jours (et selon le compte 7 j Opus, 7 j Sonnet) |
| Codex | Free, Plus, Pro, Team… | fenêtre principale (souvent 5 h) et secondaire (7 jours, absente en gratuit) |
| Antigravity / Gemini CLI | Google AI Pro, Free… | par groupe de modèles (Gemini, Claude and GPT, Gemini 4 Argon sur Google AI Pro), fenêtres 5 h et 7 jours, avec un **Détail par modèle** |
| GitHub Copilot | Free, individual… | compteurs mensuels (Chat, Completions, Premium) |
| Mistral Vibe | Free, Pro… | enveloppe mensuelle **Vibe (mois)** |
| Cursor | selon le compte | non disponible |

Sur Antigravity, la fenêtre de 7 jours d'un groupe démarre au premier appel à ce groupe ; quand elle est épuisée, la fenêtre de 5 h est bloquée jusqu'à sa remise à zéro.

Ordres de grandeur mesurés sur un compte **Google AI Pro** (indicatifs, Google n'affiche que des pourcentages) : modèles Claude via Antigravity, environ 330 000 tokens par fenêtre de 5 h et 650 000 par semaine ; **Gemini 4 Argon**, environ 5 millions de tokens par semaine, sans fenêtre de 5 h. Pour les tâches longues, Gemini 4 Argon offre donc beaucoup plus de marge.

## Capacité

La fenêtre **Capacité** estime le **travail restant** sur chaque compte. Les fournisseurs n'affichent qu'un pourcentage : la passerelle mesure combien de travail représente 100 % d'une fenêtre (coût des requêtes passées par elle, rapporté à la hausse du pourcentage), puis en déduit ce qu'il reste.

- Affichage en **$ équivalent API** (prix publics) ou en **tokens bruts**, échelle linéaire ou logarithmique.
- Barre pleine : capacité mesurée ; hachurée : estimée d'après l'offre ; pointillée : valeur minimale ; grise : pas encore mesurée.
- Cochez **tout passe par ai_proxy** sur un compte que vous n'utilisez que via la passerelle : ses mesures deviennent exactes. Sinon, l'usage direct (site web, outils officiels) fausse la mesure, qui devient une valeur minimale.
- **Relire les quotas** force une relecture ; **Importer des relevés** ajoute des mesures faites en dehors de la passerelle.
- Les mesures se refont seules à chaque fenêtre ; une baisse nette du quota est signalée.

## Santé et journaux

La fenêtre **Santé** résume l'état du démon (processus, port, adresses OpenAI et Anthropic, clé API), des dépendances (Node.js, version du service) et du réseau (mode d'écoute, liste blanche).

Journaux (**Analyse > Logs**) :

- `ai_proxy` : gestion du plugin (démarrage, arrêt, clé API, accès LAN, comptes).
- `ai_proxy_daemon` : sortie du service et des connexions de comptes ; en niveau Debug, le détail de chaque appel.
- `ai_proxy_update` : installation et mise à jour des dépendances.

Les jetons et clés sont masqués avant l'écriture dans les journaux.

# FAQ / Dépannage

**Le démon ne démarre pas.**

- Aucun compte connecté : connectez un premier compte dans **Comptes**.
- Dépendances manquantes : ouvrez **Santé** ; si Node.js ou le service est en erreur, relancez les dépendances.
- Liste blanche d'IP mal saisie (aucune adresse valide) : le démon refuse de démarrer par sécurité. Corrigez ou videz le champ, sauvegardez, redémarrez.
- Port déjà utilisé : consultez le journal `ai_proxy_daemon` ou changez le **Port du daemon**.

**Erreur 401 ou 403.**

- 401 dès l'appel : le client n'envoie pas la clé, ou une ancienne clé. Vérifiez l'en-tête `Authorization: Bearer ...` ou `x-api-key: ...`.
- 403 dès l'appel : l'adresse du client n'est pas dans la liste blanche d'IP.
- 401 ou 403 renvoyé par le fournisseur : le compte a été déconnecté ou révoqué. Reconnectez-le dans **Comptes**. Un jeton simplement expiré est renouvelé automatiquement à l'appel suivant.
- Ne copiez jamais les comptes Codex ou Cursor vers une autre installation : leur renouvellement à deux endroits invalide le compte.

**Erreur 429.**

- Quota ou limite de débit atteint : vérifiez les barres dans **Comptes**. Le compte est mis en pause quelques instants (60 secondes, jusqu'à 15 minutes si la limite se répète) et un autre compte du même fournisseur prend le relais s'il existe.
- Comptes Google : un 429 sans détail correspond souvent à une requête refusée par les filtres de Google. Il ne consomme pas de quota et ne met pas le compte en pause.

**Erreur 404 ou 400 sur un modèle.**

- Le modèle n'est pas inclus dans l'offre du compte (par exemple Gemini 4 Argon ou les modèles Claude 5.5 d'Antigravity sur un compte Google gratuit).
- Préfixe manquant : les modèles Claude et GPT-OSS d'Antigravity exigent `ag/`.
- Un modèle refusé par l'offre Cursor renvoie une erreur 400 claire, sans pause du compte.
- Les adresses propres à Mistral refusent un modèle non Mistral (400).
- Interrogez `/v1/models` pour voir les modèles réellement disponibles.

**Comptes Google gratuits.**
Ils apparaissent avec le plan `Free`. Ils n'ont que des limites hebdomadaires et n'accèdent ni à Gemini 4 Argon ni aux modèles Claude 5.5 d'Antigravity (seulement des modèles plus anciens et les modèles Gemini standards).

**Mistral.**

- Modèles `labs-*` refusés (403) : un administrateur doit les activer dans les réglages de l'organisation Mistral, page « Confidentialité » ([admin.mistral.ai/plateforme/privacy](https://admin.mistral.ai/plateforme/privacy)). Les autres modèles restent utilisables.
- « Clé Vibe refusée par Mistral (révoquée ?) » : le compte est mis en pause 24 h. Refaites la connexion Mistral ou collez une nouvelle clé. Mistral n'attribue qu'une clé Vibe par compte : la révoquer coupe tous les outils qui l'utilisent.

**Audio vers Gemini.**
Le format audio OpenAI (`input_audio`) n'est pas transmis à Gemini : la requête est refusée (400) sans consommer de quota. Pour la transcription, utilisez `/v1/audio/transcriptions` avec un compte Mistral Vibe.
