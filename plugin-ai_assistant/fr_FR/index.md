---
layout: default
title: ai_assistant — Assistant IA pour Jeedom
lang: fr_FR
pluginId: ai_assistant
---

# Presentation

**ai_assistant** est le plugin Jeedom qui apporte une interface de chat IA et des assistants specialises pour piloter votre domotique. Il peut fonctionner **en mode API direct** (sans daemon) ou se connecter a un **serveur MCP** via le plugin `mcp_jeedom`.

> ## Un plugin, multi roles
>
> Couteau suisse de l intelligence artificielle, ce plugin se decline en trois piliers :
>
> - **Agent conversationnel classique :** un dialogue fluide avec l IA pour toutes vos questions generales.
> - **Assistant specialise Jeedom :** un expert qui connait votre installation sur le bout des doigts.
> - **Pont MCP (Model Context Protocol) :** le lien direct entre votre serveur MCP et votre modele d IA.
>
> Ce dernier usage est une petite revolution. Au dela de l aspect pratique, les possibilites deviennent illimitees : imaginez un scenario Jeedom demandant a l IA de generer et d integrer un script complexe, le tout en interne et de maniere autonome.

---

# Pourquoi ajouter mcp_jeedom ?

Le plugin **mcp_jeedom** transforme Jeedom en serveur MCP. Associe a **ai_assistant**, cela apporte :

- **Outils avances** (MCP) : lecture d etats, statistiques, snapshots camera, actions composites.
- **Mode Jeedom specialist** : l IA est alimentee par une connaissance riche et structuree de votre maison.
- **Whitelists et securite** : controle fin des equipements et commandes exposes.

Sans MCP, ai_assistant reste pleinement utilisable en **mode API direct** (Gemini, OpenAI, Claude, Mistral, Groq, Ollama, etc.).

---

# Interfaces utilisateur

## 1) Panel (interface complete)

Le panel est l interface principale :

- **Selection d equipement IA** (provider, assistant ou client MCP)
- **Streaming des reponses**
- **Options avancees** : modele, temperature, theme
- **Pieces jointes** (image / pdf / txt / docx)
- **Bouton flottant "Defiler vers le bas"** (affichage automatique si vous remontez dans l historique)

## 1bis) Pan Light (vue provider)

Interface legere orientee provider direct :

- **Selection provider + modele**
- **Streaming**
- **Pieces jointes**
- **Bouton flottant "Defiler vers le bas"**
- **Toggle menu Jeedom**

## 2) Mini chat (navbar)

Un chat rapide integre a la barre Jeedom :

- Simple et discret
- Selection d equipement
- Reponse streaming
- Pas d options avancees

---

# Types d equipements

## Assistant general (Provider)
- Connexion directe a votre fournisseur IA
- Sert de base a d autres equipements

## Assistant Jeedom
- **Lie a un Provider**
- Specialise pour Jeedom
- Recommande pour piloter la maison

## Client MCP
- **Lie a un Provider**
- Sert d interface entre ai_assistant et un serveur MCP
- Associe a `mcp_jeedom` pour un controle domotique avance

---

# Mise en place rapide

1. **Obtenir une cle API** (Gemini, OpenAI, Claude, etc.)
2. **Renseigner la cle** dans la configuration du plugin
3. **Creer un equipement** "Assistant general"
4. **Creer un equipement** "Assistant Jeedom" lie au precedent
5. (Optionnel) **Installer `mcp_jeedom`** pour activer le pont MCP

Le client MCP se cree automatiquement si le serveur MCP est detecte.

---

# Mode API only (sans daemon)

Pour Gemini, vous pouvez activer le **mode API only** :

- Pas de daemon
- Pas de dependances Node
- Connexion directe API

Idem pour les autres providers : le plugin fonctionne en mode API direct.

---

# Securite

- **Whitelists** : controle fin des commandes autorisees
- **Mode lecture seule** recommande pour tests
- **Confirmations d actions sensibles** (selon configuration)

---

# Modeles compatibles

Le plugin supporte les fournisseurs suivants :

- Gemini
- Claude (Anthropic)
- OpenAI
- Mistral
- Groq
- Ollama (local)
- DeepSeek
- xAI / Grok
- OpenRouter
- Perplexity

Cette liste est amennee a evoluer.

---

# FAQ

**Puis je utiliser ai_assistant sans mcp_jeedom ?**
Oui. Le plugin fonctionne en mode API direct (sans MCP).

**Pourquoi ajouter mcp_jeedom ?**
Pour les fonctions avancees : outils domotiques riches, analyses, controle multi equipements.

**Est ce securise ?**
Le plugin propose des gardes fous (whitelist, confirmations). L utilisateur reste responsable de la configuration.

---

# Guides pratiques

- [Tuto micro (HTTP local)](tuto_micro.md)

---

*Documentation ai_assistant — Plugin IA pour Jeedom*
