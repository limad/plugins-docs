---
layout: default
title: shelly_control — Changelog
lang: fr_FR
---

[Limad44 Domotique Jeedom](https://limad.github.io/plugins-docs)

# Changelog — shelly_control

<a href="https://limad.github.io/plugins-docs/plugin-shelly_control">
  <img src="https://market.jeedom.com/filestore/market/plugin/images/shelly_control_icon.png" alt="shelly_control icon" width="120px">
</a>

>**IMPORTANT**
>
>S'il n'y a pas d'information sur la mise à jour, c'est que celle-ci concerne uniquement de la
>mise à jour de documentation, de traduction ou de texte.

# 19/08/2026

- Réduction de l'empreinte ressources du démon (usage interne, sans effet visible pour
  l'utilisateur) : contexte CoIoT initialisé seulement si un appareil Gen1 est présent, session
  réseau dédiée au scan LAN au lieu d'être dimensionnée en permanence sur son pic.

# 18/08/2026

- Ajout de la gestion des **webhooks** (URLs d'action) depuis la fiche équipement : lecture et
  modification directes des URLs de l'appareil, pour les deux générations (emplacements fixes
  `/settings/actions` en Gen1, objets `Webhook.*` en Gen2+).
- Ajout du branchement en un clic des **appuis bouton d'un appareil Gen1** vers Jeedom, seule
  façon d'obtenir cette information sur cette génération. Les commandes créées utilisent le même
  vocabulaire de valeurs que les appareils Gen2 et supérieurs (`single_push`, `long_push`…), ce
  qui permet d'écrire un scénario unique pour un parc mixte. Les URLs déjà configurées sur
  l'appareil sont conservées.
- **Correction de sécurité** : le callback HTTP du démon ne vérifiait pas réellement la clé d'API
  (`jeedom::apiAccess()` retourne un booléen sans interrompre l'exécution). L'endpoint était donc
  ouvert à tout le réseau local : états d'appareils forgés, scénarios déclenchés à volonté, et
  inventaire des appareils exposé avec leurs identifiants. Mise à jour recommandée.
- Renommage du callback du démon `jeeshelly_pro.php` → `jeeshelly_control.php` : « pro » était un
  vestige sans rapport avec ce plugin, alors que la convention Jeedom est `jee<plugin>.php`. Sans
  effet visible — le démon est relancé avec la nouvelle adresse lors de la mise à jour.

# 14/08/2026

- Mise à disposition initiale : contrôle local (sans cloud) des appareils Shelly Gen1 à Gen4 via
  `aioshelly`, découverte mDNS automatique, création dynamique des commandes selon les composants
  exposés par chaque appareil, gestion des scripts natifs (Gen2+), modale de diagnostic Santé.
