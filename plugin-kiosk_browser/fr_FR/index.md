---
layout: default
title: Kiosk Browser — Documentation
lang: fr_FR
pluginId: kiosk_browser
---

# Plugin Kiosk Browser — Documentation de Configuration

Le plugin **Kiosk Browser** permet de piloter dynamiquement une ou plusieurs fenêtres Chromium exécutées en mode plein écran (`--kiosk`) sur des écrans ou tablettes physiques connectés au serveur Jeedom (ou un Raspberry Pi lié), tout en intégrant un module dédié **OAuth Relay** pour autoriser des intégrations distantes exigeant un callback sur `http://localhost`.

---

## 1. Architecture technique

- **Moteur Chromium natif** : aucun composant lourd ou instable de type Playwright/Puppeteer n'est utilisé. Chromium est lancé directement en mode `--kiosk` avec un profil dédié (`/tmp/jeedom-kiosk-<eqLogicId>`) et un port de débogage à distance (`--remote-debugging-port=<cdp_port>`).
- **Pilotage via Chrome DevTools Protocol (CDP)** : le démon Node.js utilise la librairie standard `chrome-remote-interface` pour se connecter au port CDP et maintenir une session persistante (domaines `Page`, `Runtime`, `Input`, `Network`).
- **Communication PHP → Démon** : requêtes synchrones en HTTP JSON local (`POST http://127.0.0.1:<daemon_port>/cmd`).
- **Communication Démon → PHP** : notifications et captures asynchrones émises en HTTP POST vers `core/php/jeekiosk_browser.php`, authentifié obligatoirement par la clé API du plugin (`jeedom::apiAccess()`).
- **Sécurité réseau stricte** : le serveur HTTP du démon Node.js est lié exclusivement sur l'interface loopback `127.0.0.1` (refus immédiat de toute adresse IP non-loopback).

---

## 2. Multi-écrans & Attribution des ports

Chaque équipement Jeedom (`eqLogic`) correspond à un écran physique distinct (`DISPLAY`, ex: `:0`, `:1`).
Afin d'éviter tout conflit de ports en multi-écrans, une formule déterministe est appliquée lors de la sauvegarde :

- `cdp_port = 9222 + (eqLogic_id % 1000)`
- `daemon_port = 8766 + (eqLogic_id % 1000)`

Ces ports sont stockés dans la configuration de chaque équipement. Une vérification automatique d'unicité est exécutée au `preSave()` et au `postSave()` : si un autre écran actif utilise déjà ce couple de ports, une exception explicite bloque la sauvegarde.

---

## 3. Commandes disponibles

### Commandes Action
- **Aller à (`goto`)** : Navigue vers l'URL transmise en paramètre.
- **Cliquer (`click`)** : Calcule les coordonnées de l'élément correspondant au sélecteur CSS via `getBoundingClientRect` et simule un clic souris réel (`Input.dispatchMouseEvent`).
- **Remplir champ (`fill`)** : Prend en paramètre une chaîne JSON `{"selector":"...","value":"..."}`. Le démon focalise le champ cible puis injecte la chaîne caractère par caractère via `Input.dispatchKeyEvent` (type `char`), assurant une compatibilité totale avec les frameworks modernes (React, Vue, Angular).
- **Précédent (`back`)** : Retourne à la page précédente de l'historique.
- **Suivant (`forward`)** : Avance à la page suivante de l'historique.
- **Recharger (`reload`)** : Recharge la page courante en ignorant le cache.
- **Redémarrer navigateur (`restart_browser`)** : Termine le processus Chromium associé et le relance avec la dernière URL connue.
- **Faire une capture (`screenshot`)** : Capture l'écran courant en PNG via CDP et enregistre le fichier dans Jeedom.
- **Démarrer OAuth Relay (`start_oauth_relay`)** : Lance une session éphémère de capture OAuth.
- **Arrêter OAuth Relay (`stop_oauth_relay`)** : Clôture immédiatement la session de relay active.
- **Coller callback manuel (`paste_manual_callback`)** : Enregistre manuellement l'URL ou le code d'autorisation en cas d'échec du flux automatique.

### Commandes Info
- **URL courante (`current_url`)** : Chaîne contenant l'URL actuelle du navigateur.
- **Statut démon (`daemon_status`)** : Binaire (`1` = actif, `0` = inactif).
- **Dernière capture (`last_screenshot`)** : Chemin absolu vers la dernière capture PNG générée.
- **Statut relay (`relay_status`)** : État de la session relay (`idle`, `running`, `waiting_callback`, `success`, `timeout`).
- **Dernière URL callback (`last_callback_url`)** : Dernière URL de callback OAuth interceptée ou saisie.

---

## 4. Module OAuth Relay

### Problématique résolue
Certains services tiers (Google, Spotify, Philips Hue, etc.) exigent une redirection OAuth vers `http://localhost:<port>`. Lorsque l'administrateur configure l'intégration depuis son PC distant, ce `localhost` doit pointer sur la machine Jeedom et non sur son poste local.

### Fonctionnement du flux
1. L'administrateur saisit l'URL d'autorisation et démarre le relay depuis la modale Jeedom.
2. Le démon lance une instance Chromium éphémère (via Xvfb ou `--headless=new`) avec un profil temporaire jetable (`/tmp/jeedom-kiosk-relay-<relay_id>`).
3. Le démon diffuse un screencast interactif JPEG en temps réel dans la modale Jeedom.
4. L'utilisateur interagit directement sur le canevas de la modale (les clics et frappes clavier sont traduits et injectés fidèlement dans le navigateur headless).
5. Dès que Chromium navigue vers une URL correspondant au pattern de callback (ex. `http://localhost:*`), le démon intercepte la requête, transmet l'URL complète avec le code d'autorisation à Jeedom (`core/php/jeekiosk_browser.php`), puis détruit immédiatement le profil temporaire.
6. En cas d'échec ou d'expiration du délai imparti (timeout paramétrable), un champ de saisie de repli permet de coller manuellement l'URL de redirection ou le code.

---

## 5. Sécurité & Confidentialité

- **Secrets & URL sensibles masqués** : Toutes les URL contenant des clés d'autorisation (`code=***`, `token=***`, `apikey=***`) sont automatiquement masquées avant d'être écrites dans les fichiers journaux (`log::add`).
- **Nettoyage automatique** : Les profils Chromium temporaires utilisés pour l'authentification OAuth sont systématiquement supprimés du disque à la fin de chaque session.
- **Authentification forte du démon** : L'accès au callback Jeedom requiert une clé API valide. Les requêtes dépourvues de clé valide reçoivent immédiatement une réponse HTTP `401 Unauthorized`.
- **Échappement systématique** : Toutes les invocations shell (`exec`, `spawn`) appliquent `escapeshellarg()` sur chaque paramètre dynamique.
