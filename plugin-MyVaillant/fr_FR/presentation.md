# Présentation

**MyVaillant pour Jeedom** intègre vos équipements Vaillant / Saunier-Duval (gamme senso/MiSet, MiPro Sense, multiMATIC...) pilotés depuis l'application **myVAILLANT** ou **MiGo Link**, via une passerelle sensoNET VR92x/VR7xx ou MiLink V3.

Le plugin crée automatiquement, pour chaque installation synchronisée :

- un équipement **Home** : vue d'ensemble du système (informations et actions communes, codes défaut, rapport annuel) ;
- un équipement **Zone** par pièce/circuit pilotée (température, consigne, mode, programme) ;
- un équipement **ECS** (eau chaude sanitaire) le cas échéant ;
- si votre installation le permet, des commandes de **ventilation** et de **courbe de chauffe** par circuit.

*Certaines commandes sont créées dynamiquement selon les capacités réelles de votre installation : il est normal que deux installations n'affichent pas exactement les mêmes commandes.*

## Ce que permet le plugin

- Consulter la majorité des informations de votre installation (températures, consignes, modes...).
- Choisir le mode de fonctionnement (Programme, Consigne manuelle, Absent, Hors gel, Quick Veto).
- Définir la fin programmée des modes manuel/absent.
- Suivre les **consommations et le COP** (coefficient de performance) de la pompe à chaleur dans une vue dédiée : par période, tendance mensuelle, comparaison à l'année précédente, corrélation avec la température extérieure.
- Consulter les **codes défaut** et télécharger le **rapport annuel de consommation**.
- Piloter, selon votre matériel, le **boost ventilation** et la **courbe de chauffe** par circuit.

*Toutes les installations Vaillant ne fournissent pas les mêmes données (cela dépend du matériel installé : pompe à chaleur, chaudière gaz, appoint électrique, ventilation...).*

## Aperçu du plugin

### Page du plugin

![screenshot1](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc10.PNG)

### Installation du plugin

![screenshot1](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc1.PNG)

### Configuration

![screenshot1](https://limad.github.io/plugins-docs/plugin-MyVaillant/images/MyVaillant_doc2.PNG)
