# Plugin Alexa Premium — Commandes

---

## Sommaire

- [1. Commandes simples](#1-commandes-simples)
- [2. Commandes complexes](#2-commandes-complexes)
- [3. Commandes vocales — Évaluation d'expressions](#3-commandes-vocales--évaluation-dexpressions)
- [4. Balises SSML enrichies](#4-balises-ssml-enrichies)

---

## 1. Commandes simples

Les commandes simples sont **préinstallées automatiquement** lors de la détection des équipements. Elles sont immédiatement utilisables dans les scénarios sans configuration particulière.

Les commandes préinstallées peuvent être utilisées en l'état ou personnalisées par les utilisateurs expérimentés. Pour ajouter une commande personnalisée, utilisez le bouton :

![Bouton ajouter commande action](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/boutonajoutercommandeaction.png)

> Pour que ce bouton soit actif, cochez la case **Utilisateurs expérimentés** dans la configuration du plugin :

![Activation utilisateurs expérimentés](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi2.png)

---

### Alarmes et rappels

Le modal de gestion des alarmes a été **entièrement revu**. Les commandes INFO et ACTION associées ont été mises à jour — pensez à vérifier vos scénarios existants après une mise à jour majeure.

Les quatre commandes INFO retournent leur valeur au format `2024-12-31 21:10:00` et sont mises à jour automatiquement via MQTT et CRON :

| Commande | Description |
|---|---|
| **Prochaine Alarme** | Heure de la prochaine alarme |
| **Prochaine Alarme Musicale** | Heure de la prochaine alarme musicale |
| **Prochain Minuteur** | Heure du prochain minuteur |
| **Prochain Rappel** | Heure du prochain rappel |

> Si vous avez besoin d'un autre format (ex. `2110` pour HHmm), consultez le fichier **05 — Astuces & Dépannage**.

---

### Faire parler Alexa

Le champ texte de toutes les commandes vocales est **évalué comme une expression Jeedom** avant d'être envoyé à Alexa. Voir la section [3. Commandes vocales](#3-commandes-vocales--évaluation-dexpressions).

---

### Faire une annonce sur tous les appareils

Pour simuler une annonce multi-appareils, utilisez la commande **Parler à Alexa** avec le message préfixé par `Alexa annonce` :

![Exemple annonce multi-appareils](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FireShot-Capture-068-Boite-aux-lettres-Cote-Facteur-Jeedom-192.168.1.100.png)

```
Message : Alexa annonce le facteur est passé
```

---

### Information Mute

Lorsque vous dites « Alexa coupe le son », l'information **Mute** apparaît sur le widget de l'équipement concerné.

![Widget Mute](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/widgetmute.png)

---

## 2. Commandes complexes

Les commandes complexes sont accessibles aux utilisateurs expérimentés. Elles permettent un contrôle plus fin d'Alexa et de ses fonctionnalités.

---

### `alarm?when=#when#&recurring=#recurring#&sound=#sound#`

Ajoute une alarme sur l'équipement.

**Options :**

- `when=YYYY-MM-DD HH:MM:SS` — Si aucune récurrence n'est définie, seule l'heure est prise en compte par Amazon (le jour est ignoré).

- `recurring=` — Récurrence :

| Code | Signification |
|---|---|
| `P1D` | Tous les jours |
| `XXXX-WD` | En semaine |
| `XXXX-WE` | Week-ends |
| `XXXX-WXX-1` | Chaque lundi |
| `XXXX-WXX-2` | Chaque mardi |
| `XXXX-WXX-3` | Chaque mercredi |
| `XXXX-WXX-4` | Chaque jeudi |
| `XXXX-WXX-5` | Chaque vendredi |
| `XXXX-WXX-6` | Chaque samedi |
| `XXXX-WXX-7` | Chaque dimanche |

- `sound=` — Son de l'alarme :

| Code | Nom |
|---|---|
| `system_alerts_melodic_01` | Alarme simple / Timer simple |
| `system_alerts_melodic_02` | À la dérive |
| `system_alerts_atonal_02` | Métallique |
| `system_alerts_melodic_05` | Clarté |
| `system_alerts_repetitive_04` | Comptoir |
| `system_alerts_melodic_03` | Focus |
| `system_alerts_melodic_06` | Lueur |
| `system_alerts_repetitive_01` | Table de chevet |
| `system_alerts_melodic_07` | Vif |
| `system_alerts_soothing_05` | Orque |
| `system_alerts_atonal_03` | Lumière du porche |
| `system_alerts_rhythmic_02` | Pulsar |
| `system_alerts_musical_02` | Pluvieux |
| `system_alerts_alarming_03` | Ondes carrées |

---

### `reminder?text=#message#&when=#when#`

Ajoute un rappel sur l'équipement. Contrairement aux alarmes, les rappels peuvent être programmés **plusieurs jours à l'avance**.

- `when=YYYY-MM-DD HH:MM:SS`
- `text=#message#` — Titre/texte du rappel

---

### `whennextalarm?position=1&status=ON&format=hour`

Retourne l'heure de la prochaine alarme. Cette commande **ACTION** doit être associée à une commande **INFO** pour afficher le résultat.

La commande INFO se crée automatiquement dès que vous renseignez le champ **Nom de la commande Info** dans la colonne **Résultat dans**.

**Options :**

| Paramètre | Valeurs | Défaut | Description |
|---|---|---|---|
| `position` | `1`, `2`, `3`… | `1` | Position dans la liste (1 = prochaine) |
| `status` | `ON`, `OFF`, `ALL` | `ON` | Filtre sur l'état des alarmes |
| `format` | `hour`, `hhmm`, `full` | `hhmm` | Format du résultat retourné |

**Formats de sortie :**
- `hour` → `HH:MM`
- `hhmm` → `HHMM`
- `full` → `yyyy-MM-dd'T'HH:mm:ss.SSS`

> Si aucune alarme n'est trouvée, le serveur répond `none`.

---

### `whennextmusicalalarm?position=1&status=ON&format=hour`

Fonctionne comme `whennextalarm` mais pour les **alarmes musicales**.

---

### `musicalalarmmusicentity?position=1&status=ON`

Retourne l'information **MusicEntity** — ce qui sera joué à l'heure de l'alarme musicale.

---

### `whennextreminder?position=1&status=ON`

Retourne le prochain rappel. Fonctionne exactement comme `whennextalarm`.

---

### `deleteallalarms?type=alarm&status=all`

Supprime des alarmes et/ou rappels sur l'équipement.

> **Important :** L'équipement Alexa doit être **connecté** pour que la suppression fonctionne.

| Paramètre | Valeurs | Défaut |
|---|---|---|
| `type` | `alarm`, `reminder`, `all` | `alarm` |
| `status` | `ON`, `OFF`, `ALL` | `ON` |

---

### `command?command=#command#`

Envoie une commande au player de l'équipement.

Commandes disponibles : `play`, `pause`, `next`, `prev`, `fwd`, `rwd`, `shuffle`, `repeat`

> **Note :** `STOP` n'existe pas chez Amazon — utilisez `pause`.

- **Via scénario** : laissez `command?command=#command#`, une liste déroulante apparaît automatiquement dans le scénario.
- **Via commande directe** : syntaxe figée, ex. `command?command=play`.

---

### `radio?station=#select#`

Lance une station de radio sur l'équipement.

**Configuration des stations** dans les commandes du player :

![Configuration des stations radio](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi3.png)

Format : `idStation1|NomStation1;idStation2|NomStation2`

Par défaut : `s2960|Nostalgie;s6617|RTL;s6566|Europe1`

Une fois configurées, les stations sont sélectionnables sur le widget :

![Widget radio](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/radios.jpg)

**Trouver l'ID d'une station :** rendez-vous sur [tunein.com](https://tunein.com/radio/home/), sélectionnez votre radio, cliquez sur **Partager** et repérez l'identifiant commençant par `s`.

**Utiliser une commande radio dans un scénario** (mode utilisateur expérimenté) :

![Création commande radio scénario 1](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi4.png)

![Création commande radio scénario 2](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi5.png)

Configurez la commande en figeant l'ID de la station souhaitée :

![Configuration ID station figé](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi6.png)

---

### `routine?routine=#select#`

Lance une routine Alexa.

- **Via scénario** : laissez `routine?routine=#select#` et renseignez l'ID dans le champ prévu.
- **Via commande directe** : `routine?routine=VOTRE_ID_ROUTINE`

**Trouver l'ID d'une routine :** consultez l'écran **Routines** du plugin, colonne de droite.

---

### `playmusictrack?trackId=#select#`

Lance une piste Amazon Music par son TrackID. Les pistes se configurent dans la commande **Écouter une piste musicale** :

![Configuration TrackID](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/Screenshot_Alexaapi7.png)

Format : `53bfa26d-xxxx|Ma Piste 1;7b12ee4f-xxxx|Ma Piste 2`

**Comment trouver un TrackID :**

1. Dans les commandes de l'équipement, cochez **Afficher** sur la commande **Amazon Music Id** :

![Commande Amazon Music Id](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/amazonmusicidtrack.png)

2. Une icône note de musique apparaît sur le dashboard — lancez la piste souhaitée et relevez l'ID affiché :

![Affichage TrackID sur dashboard](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/amazonmusicidtrack2.png)

3. Décochez **Afficher** une fois l'ID récupéré.

> Cette procédure fonctionne également pour trouver l'ID d'une **station radio**. Pour certaines playlists, l'ID ne remonte pas — lancez la piste seule (hors playlist) pour être certain de l'obtenir.

---

### `history?maxRecordSize=50&recordType=VOICE_HISTORY`

Récupère l'historique d'activité de l'équipement.

- `maxRecordSize` — Nombre d'enregistrements à remonter
- `recordType` — Type d'enregistrement (`VOICE_HISTORY` par défaut)

---

### Modifier l'icone des players

Les images des tuiles des players sont des liens temporaires envoyés par les fournisseurs de musique. En cas d'image vide :

![Tuile sans image](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/tuile.png)

Le plugin affiche automatiquement la miniature par défaut du lecteur :

![Tuile avec miniature par défaut](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/tuile2.png)

Pour personnaliser l'image, remplacez le fichier `logourl.png` dans :
```
plugins/alexaamazonmusic/core/config/
```
(remplacez `alexaamazonmusic` par le nom du plugin player concerné)

---

## 3. Commandes vocales — Évaluation d'expressions

> **Nouveauté importante**

Pour toutes les commandes vocales (**Faire parler Alexa**, **Annonce**, **QuestionAsk**, etc.), le champ texte est désormais **évalué comme une expression Jeedom** avant d'être envoyé à Alexa, exactement comme le ferait le testeur d'expression.

Cela signifie que vous pouvez envoyer :

- Une **expression mathématique** — Alexa annoncera directement le résultat calculé :
  ```
  round(avg(10,15,18))
  ```
- La **valeur d'une commande info** via son ID ou sa syntaxe complète :
  ```
  #cmdId#
  #[Objet][Équipement][Commande]#
  ```

---

## 4. Balises SSML enrichies

La bibliothèque SSML Amazon est intégrée de manière exhaustive via un système de **balises simplifié**, utilisable dans toutes les commandes vocales du plugin.

### Syntaxe générale

```
#tagName::param1::param2#
```

---

### `audio` — Son de la bibliothèque Amazon

![Exemple balise audio](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa2.png)

```
#audio::alarms/buzzers/buzzers_09#
#audio::animals/amzn_sfx_lion_roar_01#
```

> [Bibliothèque de sons Amazon](https://developer.amazon.com/en-US/docs/alexa/custom-skills/ask-soundlibrary.html)

---

### `voice` — Voix Amazon

```
#voice::Mathieu::Salut, c'est Mathieu qui parle !#
#voice::Celine::Bonjour, je suis Céline.#
```

---

### `say-as` — Interprétation du texte

| Valeur `param1` | Effet |
|---|---|
| `interjection` | Prononce une interjection naturelle |
| `cardinal` | Lit un nombre comme un chiffre cardinal |
| `ordinal` | Lit un nombre en ordinal (premier, deuxième…) |
| `characters` | Épelle lettre par lettre |
| `date` | Lit une date formatée |
| `time` | Lit une durée |
| `telephone` | Lit un numéro de téléphone |
| `address` | Lit une adresse |
| `expletive` | Bip de censure sur le mot |

```
#say-as::interjection::Bonjour#
#say-as::cardinal::42#
#say-as::characters::SSML#
```

**Utilisation des interjections :**

![Exemple interjection](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa3.png)

> **Important :** Les interjections doivent être dans des **phrases séparées** — entourées de points — sinon elles ne sont pas prises en compte.

![Exemple interjection incorrecte](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa4.png)
*❌ Ne fonctionne pas — l'interjection est noyée dans la phrase*

![Exemple interjection correcte](https://limad.github.io/plugins-docs/plugin-alexaapiv2/images/FaireParlerAlexa5.png)
*✅ Correct — l'interjection est isolée par un point*

---

### `amazon:effect` — Effets vocaux

| Valeur `param1` | Effet |
|---|---|
| `whispered` | Chuchotement |

```
#amazon:effect::whispered::Je suis un fantôme...#
```

---

### `amazon:emotion` — Émotion

```
#amazon:emotion::excited::medium::Incroyable, ça fonctionne !#
#amazon:emotion::disappointed::low::Ce n'est pas ce que j'espérais.#
```

---

### `amazon:domain` — Domaine de lecture

| Valeur | Contexte |
|---|---|
| `news` | Style journalistique |
| `music` | Présentation musicale |
| `conversational` | Ton conversationnel |
| `long-form` | Lecture longue |

```
#amazon:domain::news::Et voici les titres de l'actualité.#
```

---

### `prosody` — Rythme, ton et volume

```
#prosody::rate="fast" pitch="high"::Je parle vite et aigu !#
#prosody::rate="slow" volume="loud"::Je parle lentement et fort.#
```

| Attribut | Valeurs possibles |
|---|---|
| `rate` | `x-slow`, `slow`, `medium`, `fast`, `x-fast`, ou `n%` |
| `pitch` | `x-low`, `low`, `medium`, `high`, `x-high`, ou `+n%` / `-n%` |
| `volume` | `silent`, `x-soft`, `soft`, `medium`, `loud`, `x-loud`, ou `+ndB` |

---

### `emphasis` — Accentuation

```
#emphasis::strong::absolument#
```

| Valeur | Intensité |
|---|---|
| `strong` | Forte |
| `moderate` | Modérée |
| `reduced` | Réduite |

---

### `break` — Pause

```
#break::2s#
#break::500ms#
```

---

### `lang` — Langue

```
#lang::en-US::Hello, how are you?#
#lang::es-ES::Hola, buenos días.#
```

---

### `phoneme` — Prononciation phonétique

```
#phoneme::ipa::pɛ.ʁi#
```

---

### `sub` — Substitution

Alexa prononce le texte de remplacement à la place de l'original :

```
#sub::World Wide Web Consortium::W3C#
```

---

### `p` et `s` — Structure du discours

```
#p::Voici le premier paragraphe.#
#s::Et voici une phrase bien délimitée.#
```

---

### `w` — Rôle grammatical

```
#w::VB::Lire#
```

---

### SSML complet en XML (usage avancé)

Pour les cas complexes ou les combinaisons imbriquées, la commande **Speak SSML** accepte toujours la syntaxe XML complète :

```xml
<speak>
  <voice name="Mathieu">
    <prosody rate="slow" pitch="low">
      Attention, message important.
      <break time="1s"/>
      La température est de <say-as interpret-as="cardinal">12</say-as> degrés.
    </prosody>
  </voice>
</speak>
```

**Ressources utiles :**
- [Référence W3C sur le SSML](https://www.w3.org/TR/speech-synthesis11/)
- [Documentation Amazon SSML (EN)](https://developer.amazon.com/fr-FR/docs/alexa/custom-skills/speech-synthesis-markup-language-ssml-reference.html)
- [Interjections françaises Amazon](https://developer.amazon.com/fr-FR/docs/alexa/custom-skills/speechcon-reference-interjections-french.html)
- [Bibliothèque de sons Amazon](https://developer.amazon.com/en-US/docs/alexa/custom-skills/ask-soundlibrary.html)

---

📌 Forum dédié : [community.jeedom.com/tags/plugin-alexaapiv2](https://community.jeedom.com/tags/plugin-alexaapiv2)
