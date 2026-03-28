# 🎙️ Tuto : Autoriser le Micro sur un Réseau Local (HTTP)

Par défaut, les navigateurs modernes appliquent une politique de **"Secure Context"**. Cela signifie que l'accès au microphone, à la caméra ou à la géolocalisation est systématiquement bloqué si la page n'est pas en **HTTPS**.

Ce guide explique comment lever cette restriction pour vos serveurs de test ou vos installations domotiques (Jeedom, Home Assistant, etc.) accessibles via une IP locale.

---

## 🔵 Google Chrome & Microsoft Edge
*Méthode la plus sûre : permet d'autoriser des adresses spécifiques.*

1. Copiez-collez l'adresse suivante dans votre barre de navigation :
   * **Chrome :** `chrome://flags/#unsafely-treat-insecure-origin-as-secure`
   * **Edge :** `edge://flags/#unsafely-treat-insecure-origin-as-secure`
2. Localisez l'option **"Insecure origins treated as secure"**.
3. Changez le statut de `Disabled` à **Enabled**.
4. Dans la zone de texte, saisissez votre adresse IP locale complète (ex: `http://192.168.1.50:8080`).
   > *Note : Pour ajouter plusieurs adresses, séparez-les par une virgule.*
5. Cliquez sur le bouton **Relaunch** en bas à droite pour redémarrer le navigateur.

---

## 🦊 Mozilla Firefox
*Méthode globale : modifie le comportement du moteur de rendu.*

1. Tapez `about:config` dans la barre d'adresse et validez l'avertissement.
2. Recherchez les deux préférences suivantes et passez-les à **true** (double-clic) :
   * `media.devices.enumerate.allowInsecure`
   * `media.getusermedia.insecure.enabled`
3. **Redémarrez Firefox** complètement.
4. ⚠️ **Attention :** Ce réglage est global. Pensez à le désactiver (remettre sur `false`) après vos tests pour garantir votre sécurité sur le web public.

---

## 🍏 Safari (macOS)
*Méthode temporaire : via le menu de développement.*

1. Allez dans **Réglages** (ou Préférences) > onglet **Avancé**.
2. Cochez **"Afficher les fonctionnalités pour les développeurs Web"**.
3. Dans la barre des menus (en haut de l'écran), cliquez sur le nouveau menu **Développement**.
4. Cochez l'option **"Autoriser la capture multimédia sur les sites non sécurisés"**.

---

## 📱 Smartphones (iOS & Android)
Le blocage est au niveau du système d'exploitation et ne peut généralement pas être contourné via des réglages.

* **La solution :** Utiliser un tunnel HTTPS.
* **Outil recommandé :** [ngrok](https://ngrok.com/) ou [Localtunnel](https://localtunnel.github.io/www/).
* **Commande rapide (si Node.js est installé) :**
  ```bash
  npx localtunnel --port 80
  ```

Une URL publique HTTPS est ensuite fournie (ex: `https://xxxxx.loca.lt`) et peut etre utilisee pour acceder au panel avec autorisation micro.
