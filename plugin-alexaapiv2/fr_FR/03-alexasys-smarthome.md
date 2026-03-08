# Plugin Alexa Premium — AlexaSys & SmartHome

---

## Sommaire

- [1. Équipement AlexaSys](#1-équipement-alexasys)
- [2. Alexa Chat](#2-alexa-chat)
- [3. TTS — Text-To-Speech](#3-tts--text-to-speech)
- [4. Utilisation d'AlexaSys dans les scénarios](#4-utilisation-dalexasys-dans-les-scénarios)
- [5. Gestion SmartHome](#5-gestion-smarthome)

---

## 1. Équipement AlexaSys

**AlexaSys** est l'équipement virtuel central du plugin. Il concentre :
- Les informations communes à tous les appareils
- Les états globaux du compte Amazon
- Les données système du plugin

Il n'est pas lié à un appareil Echo physique en particulier — c'est pourquoi il est le point d'entrée recommandé pour construire des scénarios stables et maintenables.

**Architecture recommandée :**

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

## 2. Alexa Chat

> **Nouveauté**

La commande **Alexa Chat** permet de dialoguer directement avec Alexa, avec plusieurs avantages par rapport aux commandes vocales classiques (Parler à Alexa, Annonce…) :

| | Commandes classiques | Alexa Chat |
|---|---|---|
| Dépendance à l'historique | ✅ Oui | ❌ Non |
| Retour vocal exploitable (MP3) | ❌ Non | ✅ Oui |
| Retour texte lisible | ❌ Non | ✅ Oui |
| Vitesse de réponse | Standard | Immédiate |

### Widget Alexa Chat

Le nouveau widget dédié permet directement depuis le dashboard de :
- Saisir une question ou un message
- **Écouter la réponse vocale** (MP3)
- **Lire la réponse textuelle** retournée par Alexa

### Désactiver la lecture automatique audio

Si vous ne souhaitez pas que l'audio se lance automatiquement sur la tuile du dashboard, ajoutez l'option suivante dans les paramètres optionnels du widget :

```
AutoPlayAudio => 0
```

### Quand utiliser Alexa Chat ?

> Cette commande est à **privilégier dans les scénarios** qui n'ont pas besoin d'interagir avec un équipement Amazon physique spécifique.

❌ **Mauvaise pratique** — cibler directement un Echo physique pour des traitements génériques :
```
[Salon][Echo Salon][Parler à Alexa]
→ "La température est de #[Maison][Capteur][Température]# degrés"
```

✅ **Bonne pratique** — passer par AlexaSys / Alexa Chat pour les traitements centralisés :
```
[AlexaSys][Alexa Chat]
→ "La température est de #[Maison][Capteur][Température]# degrés"
```

---

## 3. TTS — Text-To-Speech

> **Nouveauté**

La commande **TTS** convertit un texte en fichier **MP3**, exploitable directement sur la tuile du dashboard ou dans un scénario.

Elle fonctionne **indépendamment de tout équipement Alexa physique** — utile pour générer des annonces audio personnalisées dans Jeedom, les diffuser sur un lecteur tiers, ou les archiver.

Le MP3 généré est accessible et lisible depuis le widget d'AlexaSys, de la même façon que le retour vocal d'Alexa Chat.

---

## 4. Utilisation d'AlexaSys dans les scénarios

### Récupérer la dernière commande vocale

AlexaSys expose la **dernière interaction vocale** détectée sur le compte Amazon. Cette information est utilisable directement dans les conditions de scénarios :

```
SI
  #[AlexaSys][DernièreCommande]# contient "musique"
ALORS
  Action spécifique
```

### Exploiter le retour textuel d'Alexa Chat

Après un Alexa Chat, la réponse textuelle d'Alexa est stockée dans une commande INFO d'AlexaSys. Vous pouvez l'utiliser dans un scénario pour déclencher des actions en fonction du contenu de la réponse :

```
SI
  #[AlexaSys][Alexa Chat Réponse]# contient "oui"
ALORS
  Allumer la lumière du salon
```

### Bonnes pratiques scénarios

- Utilisez **AlexaSys comme point d'entrée unique** pour toutes les interactions ne nécessitant pas un appareil Echo spécifique.
- Réservez le ciblage d'un Echo physique aux cas où la localisation de la voix a de l'importance (ex. : faire parler l'Echo de la chambre spécifiquement).
- Vérifiez régulièrement que l'équipement AlexaSys est **actif** et que ses commandes INFO sont à jour.

---

## 5. Gestion SmartHome

Le code du démon gérant la partie SmartHome a été **entièrement reconstruit**.

### Faire le ménage dans les équipements SmartHome

Amazon peut remonter un très grand nombre d'appareils SmartHome, y compris des appareils anciens, dupliqués ou inutilisés.

> **Trop d'appareils SmartHome remontent ?** Désactivez dans Jeedom tous les équipements dont vous n'avez pas besoin — ils consomment des ressources inutilement et encombrent l'interface.

Pour nettoyer également côté Amazon, gérez vos appareils enregistrés depuis votre espace Amazon :

👉 [amazon.fr — Gestion des appareils Alexa](https://www.amazon.fr/hz/mycd/digital-console/devicedetails?deviceFamily=ALEXA_APP)

### Scan SmartHome

Vous pouvez lancer un **scan ciblé SmartHome** depuis l'écran Scan du plugin, sans affecter vos autres équipements (Echo, Groupes…).

### CRON centralisé et paramétrable

Les équipements SmartHome sont désormais rafraîchis via un **CRON centralisé**. Sa fréquence est configurable directement depuis la page de **Configuration** du plugin.

### Désactivation automatique des équipements injoignables

Tout équipement SmartHome ne répondant pas pendant **3 jours consécutifs** est automatiquement désactivé par le plugin, afin d'éviter l'accumulation d'équipements fantômes.

> Cette durée est susceptible d'évoluer selon les retours des utilisateurs — n'hésitez pas à partager votre expérience sur le forum.

### Filtrage des équipements sans commandes

Certains équipements détectés par Amazon mais ne proposant aucune commande disponible ne remontent plus dans le plugin, évitant ainsi l'encombrement inutile.

### Sélection multiple — Activer / Désactiver en masse

Le modal de sélection multiple a été revu. Deux boutons permettent désormais d'**Activer** ou de **Désactiver** en masse tous les équipements sélectionnés en une seule action.

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)
