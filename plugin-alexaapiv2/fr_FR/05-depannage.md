# Plugin Alexa Premium — Bonnes pratiques, Astuces & Dépannage

---

## Sommaire

- [1. Bonnes pratiques](#1-bonnes-pratiques)
- [2. Astuce : Formater l'heure de la prochaine alarme](#2-astuce--formater-lheure-de-la-prochaine-alarme)
- [3. En cas de problèmes](#3-en-cas-de-problèmes)
  - [3.1 Problèmes d'authentification](#31-problèmes-dauthentification)
  - [3.2 Problèmes de cookie](#32-problèmes-de-cookie)
  - [3.3 Appareils absents ou non remontés](#33-appareils-absents-ou-non-remontés)
  - [3.4 Commande envoyée sans effet](#34-commande-envoyée-sans-effet)
  - [3.5 Problèmes Amazon Music](#35-problèmes-amazon-music)
  - [3.6 Problèmes de Skill ASK](#36-problèmes-de-skill-ask)
  - [3.7 Cas réseau spécifiques](#37-cas-réseau-spécifiques)
  - [3.8 Procédure de reset complet](#38-procédure-de-reset-complet)
- [4. Ouvrir un sujet sur le forum](#4-ouvrir-un-sujet-sur-le-forum)
- [5. Plugins annexes](#5-plugins-annexes)

---

## 1. Bonnes pratiques

Quelques règles simples pour une installation stable et durable.

### Authentification

- **Utilisez toujours la méthode Auth** pour générer et renouveler le cookie — n'utilisez pas le proxy.
- **Activez la double authentification (2FA/MFA)** sur votre compte Amazon. Contrairement à ce que l'on pourrait penser, le 2FA améliore la fiabilité de la génération du cookie.
- **Évitez les VPN** pendant l'utilisation du plugin : ils peuvent invalider le cookie prématurément en changeant l'IP apparente.
- **Ne partagez jamais votre cookie** — il donne accès à votre compte Amazon.

### Équipements

- **Désactivez les équipements inutilisés** plutôt que de les laisser actifs — ils consomment des ressources inutilement.
- **Faites le ménage** régulièrement dans vos équipements SmartHome, côté Jeedom et côté Amazon.
- **Nettoyez vos appareils Amazon enregistrés** pour éviter les doublons et les fantômes :  
  👉 [amazon.fr — Gestion des appareils Alexa](https://www.amazon.fr/hz/mycd/digital-console/devicedetails?deviceFamily=ALEXA_APP)

### Scénarios

- **Centralisez vos scénarios via AlexaSys** plutôt que de cibler directement des équipements Echo physiques pour les traitements génériques.
- **Vérifiez les logs** régulièrement depuis la page de configuration du plugin pour anticiper les problèmes.

### Architecture recommandée

```
Compte Amazon
      ↓
Plugin Alexa Premium
      ↓
AlexaSys (équipement central)
      ↓
Scénarios Jeedom
      ↓
Appareils Echo / SmartHome
```

---

## 2. Astuce : Formater l'heure de la prochaine alarme

Par défaut, le plugin retourne les alarmes au format `2024-12-31 21:10:00`. Pour obtenir le format `HHmm` (ex. `2110`), créez un **équipement virtuel** avec le plugin Virtuel.

![Créer un virtuel](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom.png)

Nommez-le par exemple `Chambre HHmm`, puis dans l'onglet **Commandes**, ajoutez une **info virtuelle** :

![Ajouter une info virtuelle](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom1.png)

- **Nom** : `Prochaine Alarme HHmm`
- **Sous-type** : `Autre`

Dans le champ **Valeur**, saisissez la formule suivante en remplaçant `#[Etage][La chambre][Prochaine Alarme]#` par votre commande :

![Formule dans le champ Valeur](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom2.png)

```
sprintf("%02d",trim(substr(#[Etage][La chambre][Prochaine Alarme]#, 11, 2)))sprintf("%02d",trim(substr(#[Etage][La chambre][Prochaine Alarme]#, 14, 2)))
```

Cliquez sur **Tester** pour vérifier le résultat :

![Test du résultat HHmm](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_2020-02-29_Virtual_-_Jeedom3.png)

Puis **Sauvegardez**. Utilisez désormais cette commande `Prochaine Alarme HHmm` dans vos scénarios à la place de la commande d'origine.

> Cette technique s'applique également aux **alarmes musicales**, **minuteurs** et **rappels**.

![Exemple d'utilisation horloge Alexa](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/alexaformathorloge.png)

---

## 3. En cas de problèmes

> **Rappel :** 90 % des problèmes sont liés au cookie. La solution est quasi systématiquement de **relancer l'Auth proprement**.

---

### 3.1 Problèmes d'authentification

#### Le bouton Auth ne fonctionne pas / la pop-up ne s'ouvre pas

- Vérifiez que les **pop-ups ne sont pas bloquées** par votre navigateur ou un AdBlock.
- Vérifiez que votre Jeedom est bien accessible en **HTTPS** avec un certificat valide.
- Testez dans un **autre navigateur** ou en **navigation privée**.
- Vérifiez la console du navigateur (F12) pour identifier une éventuelle erreur JavaScript.

#### Boucle après connexion Amazon

- Déconnectez-vous d'Amazon dans votre navigateur.
- Supprimez les cookies du navigateur.
- Relancez l'Auth depuis une fenêtre de navigation privée.

#### Échec de la double authentification (MFA)

- Vérifiez que l'**heure système de votre serveur Jeedom est correcte** (synchronisation NTP).
- Validez le code MFA **immédiatement** après sa génération — il expire en quelques secondes.

#### Erreur Login unsuccessful

- Testez la connexion à votre compte Amazon directement depuis un navigateur.
- Vérifiez que vous n'avez pas reçu un mail de sécurité Amazon signalant un blocage.
- Assurez-vous que le **pays de votre compte Amazon** correspond à la région configurée dans le plugin.

---

### 3.2 Problèmes de cookie

#### Cookie expiré rapidement ou invalide

Les logs afficheront typiquement `Authentication failed` ou `cookie invalid`.

Causes fréquentes :
- Changement d'IP (redémarrage box, IP dynamique)
- VPN actif
- Connexions simultanées multiples
- Sécurité Amazon renforcée

**Solutions :**
- Relancez l'**Auth** depuis la configuration du plugin.
- Désactivez tout VPN pendant l'authentification et l'utilisation du plugin.
- Si votre accès Internet utilise le **CG-NAT** (IP partagée entre plusieurs abonnés), envisagez de demander une IP fixe à votre FAI — le CG-NAT peut invalider le cookie régulièrement.

#### CAPTCHA récurrent

Cause probable : Amazon détecte une activité automatisée.

- Attendez **24 heures** avant de retenter.
- Utilisez **uniquement la méthode Auth** (jamais le proxy).

---

### 3.3 Appareils absents ou non remontés

- Le cookie est peut-être expiré — relancez l'Auth.
- Vérifiez que la **région Amazon** configurée dans le plugin correspond à celle de votre compte.
- Nettoyez les appareils enregistrés côté Amazon :  
  👉 [amazon.fr — Gestion des appareils Alexa](https://www.amazon.fr/hz/mycd/digital-console/devicedetails?deviceFamily=ALEXA_APP)
- Relancez un scan (général ou par type) depuis l'écran Scan du plugin.

---

### 3.4 Commande envoyée sans effet

- Vérifiez que l'appareil est bien **en ligne** (page Santé du plugin).
- Vérifiez que le **deviceId** utilisé dans la commande est correct.
- Activez les logs en mode **Debug** et analysez les entrées `alexaapi` et `alexapremium` pour identifier l'erreur.

---

### 3.5 Problèmes Amazon Music

#### La lecture ne démarre pas

- Vérifiez que vous disposez d'un **abonnement Amazon Music actif**.
- Vérifiez qu'**Amazon Music est défini comme service de musique par défaut** dans les paramètres Alexa.

#### La lecture s'arrête

Causes possibles :
- Restrictions DRM liées à l'abonnement
- Compte multi-utilisateur avec conflit de session
- Changement d'IP en cours de lecture

---

### 3.6 Problèmes de Skill ASK

#### Le Skill ne répond pas

- Vérifiez que le Skill est en mode **Development** dans la console ASK.
- Vérifiez qu'un **Build a bien été effectué** après chaque modification du code ou du modèle d'interaction.
- Vérifiez que le fichier `skill.json` a bien été importé et sauvegardé.

#### Erreur 500

Causes fréquentes :
- `JEEDOM_URL` incorrecte (slash final manquant, HTTP au lieu de HTTPS)
- `APIKEY` invalide ou expirée
- Jeedom non accessible depuis Internet

**Vérification :** testez l'URL de votre Jeedom depuis un navigateur externe (depuis un réseau mobile par exemple).

#### Consulter les logs du Skill

```
Console ASK → Onglet Code → CloudWatch Logs
```

---

### 3.7 Cas réseau spécifiques

#### Reverse Proxy

Vérifiez que les en-têtes **`X-Forwarded-For`** sont correctement transmis et que le **HTTPS est forcé** sur toutes les routes.

#### Hébergement Cloud (VPS, serveur dédié)

Certaines IP de datacenters peuvent être bloquées par Amazon. Si vous constatez des refus systématiques lors de la génération du cookie, c'est peut-être la cause.

#### CG-NAT

L'IP partagée du CG-NAT peut invalider le cookie de façon régulière et imprévisible. Si vous êtes dans cette situation, demandez une **IP fixe** à votre fournisseur d'accès Internet.

---

### 3.8 Procédure de reset complet

En dernier recours, si rien ne fonctionne, effectuez un reset propre dans cet ordre :

1. Supprimer le cookie depuis la configuration du plugin
2. Faire une **sauvegarde Jeedom**
3. Supprimer l'équipement AlexaSys
4. Redémarrer Jeedom
5. Réinstaller le plugin ou forcer la mise à jour des dépendances
6. Relancer l'**Auth**
7. Relancer le **SCAN** pour recréer AlexaSys et les équipements

---

## 4. Ouvrir un sujet sur le forum

Si vous rencontrez un problème non résolu après avoir consulté cette documentation, ouvrez un sujet sur le forum dédié.

Pour qu'une aide efficace soit possible, joignez **systématiquement** les éléments suivants :

| Élément | Comment l'obtenir |
|---|---|
| **Version du plugin** | Page de configuration du plugin |
| **Page Santé** | Écran Santé du plugin (capture d'écran) |
| **Logs** | Activer le niveau Debug, reproduire le problème, copier les lignes `alexaapi` et `alexapremium` concernées |
| **Description du problème** | Ce qui est attendu, ce qui se passe réellement, depuis quand |
| **Configuration réseau** | Reverse proxy ? VPS ? CG-NAT ? VPN ? |

> Plus votre rapport sera complet, plus la réponse sera rapide et pertinente.

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)

---

## 5. Plugins annexes

| Plugin | Description |
|---|---|
| **Amazon Music** | Lancement de playlists Amazon Music |
| **Deezer / Spotify** | Intégration des plateformes de streaming |
| **Fire TV** | Contrôle des appareils Fire TV |
| **Todo List** | Gestion des listes de tâches Alexa |
