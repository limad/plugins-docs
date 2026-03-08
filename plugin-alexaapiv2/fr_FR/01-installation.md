# Plugin Alexa Premium — Installation & Mise à jour

> **Commandez Alexa depuis Jeedom** — Ce plugin ne commande **pas** Jeedom depuis Alexa (contrairement au plugin Alexa officiel) mais **commande Alexa depuis Jeedom**.

---

## Ce que permet de faire ce plugin

- Scanner automatiquement tous les Echo du compte Amazon (scan général ou par type)
- Faire parler les Amazon Echo (texte libre, SSML, annonces multi-appareils)
- Régler et récupérer le volume de chaque équipement ou d'un groupe
- Programmer, supprimer et consulter des alarmes et des rappels
- Récupérer l'heure de la prochaine alarme/rappel pour l'utiliser dans des scénarios
- Envoyer des commandes au player : `pause`, `play`, `next`, `prev`, `fwd`, `rwd`, `shuffle`, `repeat`
- Afficher la playlist en cours et lancer des pistes Amazon Music
- Lancer des routines Alexa et des stations de radio
- Récupérer le texte de la dernière interaction vocale
- Scanner et commander les équipements SmartHome (turnOn / turnOff)
- Discuter avec Alexa et exploiter la réponse vocale (MP3) via **Alexa Chat**
- Convertir du texte en MP3 via la commande **TTS**

**Ce que ce plugin ne fait pas :**  
Envoyer un ordre vocal à exécuter par Alexa (il faut passer par le plugin Alexa officiel ou IFTTT).

> **Astuce :** Il est toutefois possible de détecter via un scénario une phrase courte dite à Alexa (ex. : « Alexa volets ») et d'en déclencher l'action dans Jeedom.

---

## 1. Installer le plugin depuis le Market

Recherchez **Alexa Premium** dans le Market Jeedom.

![Installation étape 1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi1.png)

![Installation étape 3](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi3.png)

Vous avez le choix entre deux versions :
- **Stable** — recommandée pour la plupart des utilisateurs
- **Beta** — contient les dernières fonctionnalités en cours de test

![Choix de version](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi4.png)

> Il n'est pas nécessaire de passer Jeedom en mode Beta pour installer le plugin en Beta. Vous pouvez basculer d'une version à l'autre à tout moment en réinstallant par-dessus.

---

## 2. Activer le plugin

![Activation du plugin](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi5.png)

---

## 3. Recharger les dépendances

![Chargement des dépendances](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi6.png)

Attendez que l'installation des dépendances se termine avant de passer à l'étape suivante.

---

## 4. Générer le cookie Amazon — Méthode Auth (recommandée)

Le plugin communique avec les serveurs Amazon via un cookie d'authentification. **La méthode recommandée est le bouton Auth**, sans proxy.

**Pourquoi privilégier le bouton Auth ?**
- Méthode officielle, plus stable
- Compatible avec la double authentification (MFA/2FA)
- Moins de CAPTCHA
- Pas de sensibilité aux changements d'IP

> **Évitez d'utiliser le proxy** : il génère plus de CAPTCHAs, est sensible aux changements d'IP et nécessite une maintenance complexe.

![Génération du cookie](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi7.png)

### Procédure

1. Ouvrez la configuration du plugin Alexa Premium.
2. Cliquez sur le bouton **Auth** (ou **Générer le cookie Amazon**).
3. Une fenêtre pop-up Amazon s'ouvre — identifiez-vous avec votre compte Amazon.

![Identification Amazon](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi8.png)

4. Validez la double authentification (MFA) si elle est demandée.
5. **Attendez la confirmation** puis fermez la fenêtre.

> Si Amazon affiche un CAPTCHA, agrandissez la fenêtre vers le bas pour accéder au bouton de validation.

---

## 5. Lancer le Daemon

Le Daemon se lance normalement de façon automatique après la génération du cookie. S'il ne démarre pas, cliquez sur **Lancer le Daemon** depuis la page de configuration.

![Lancement du Daemon](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi9.png)

---

## 6. Lancer le SCAN

![Lancement du Scan](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/installationalexaapi10.png)

Cliquez sur **SCAN** pour détecter automatiquement vos équipements Amazon. Vous pouvez choisir entre :
- **Scan général** — détecte tous les types d'équipements en une seule fois
- **Scan par type** — ciblez uniquement les Devices (Echo), les équipements SmartHome, les Groupes, etc.

Les appareils détectés apparaissent dans la liste des équipements. Pour tester, ouvrez un équipement, allez dans **Commandes** et lancez la commande **Speak** — Alexa devrait parler en moins de 5 minutes !

---

## 7. Les écrans de gestion

![Vue générale du plugin](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi1.png)

| Écran | Description |
|---|---|
| **Scan** | Détecte vos appareils Amazon. Scan général ou par type (Devices, SmartHome, Groupes…). Sans risque, ne supprime rien. |
| **Configuration** | Paramétrage général du plugin, dont le CRON SmartHome et les options d'affichage. |
| **Santé** | État de santé de vos équipements (présence, connectivité). |
| **Routines** | Liste des routines de votre compte Amazon, lançables manuellement. |
| **Rappels / Alarmes** | Consultation et suppression de vos alarmes et rappels. Modal entièrement revu. |
| **Historique** | Historique d'activité de vos équipements avec indication de succès. |
| **Requêteur Info** | *(Utilisateurs avertis)* Interroge directement le serveur Amazon. |
| **Requêteur Action** | *(Utilisateurs experts)* Envoie des requêtes brutes au serveur Amazon. |

---

## 8. Mise à jour et changement de version

L'API Amazon n'étant pas documentée officiellement, Amazon peut en modifier le fonctionnement à tout moment. Les mises à jour du plugin permettent de corriger ces écarts.

Après une mise à jour, **trois approches** sont possibles selon l'impact souhaité sur vos scénarios :

---

### Solution 1 — Supprimer et recréer tous les équipements *(la plus propre)*

![Bouton supprimer et recréer](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi1.png)

C'est le mode le plus **propre** et le plus **optimisé** : vous repartez avec une installation comme neuve.

> **Attention :** cette opération supprime tous les équipements et toutes leurs commandes — les liens dans vos scénarios seront perdus.

---

### Solution 2 — Forcer la mise à jour de toutes les commandes *(sans risque)*

![Bouton forcer mise à jour globale](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi2.png)

Vos équipements et leurs commandes ne sont pas supprimés. Vos scénarios restent intacts.

Pour ne lancer le forçage que sur un seul équipement, rendez-vous sur l'équipement concerné et cliquez sur :

![Bouton forcer mise à jour unitaire](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi3.png)

---

### Solution 3 — Lancer un SCAN

![Bouton Scan](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonalexaapi4.png)

Le SCAN peut être lancé à tout moment. Il ne supprime jamais un équipement ou une commande existante, mais recrée les nouveaux équipements ou commandes manquantes. C'est l'option la moins invasive.

---

## 9. Les widgets — isoWidget

Le plugin installe un jeu de widgets nommé **isoWidget**. Ces widgets sont indépendants du plugin Alexa Premium : ils peuvent être utilisés avec n'importe quel autre plugin Jeedom compatible.

L'attribution d'un widget à une commande et sa personnalisation se font via les **outils natifs du core Jeedom** :
- Rendez-vous sur la commande concernée (dans l'équipement).
- Utilisez l'onglet **Affichage** ou les **Paramètres optionnels du widget** pour choisir et configurer le widget isoWidget souhaité.

> Pour toute question sur la gestion des widgets dans Jeedom, référez-vous à la [documentation officielle du core Jeedom](https://doc.jeedom.com).

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)
