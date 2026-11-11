Changelog jee2alexa
-------------------

<a href="https://limad.github.io/plugins-docs/plugin-jee2alexa"><img src="https://limad.github.io/plugins-docs/plugin-jee2alexa/images/jee2alexa_icon.png" alt="jee2alexa icon" width="150px"></a>

## Version en cours de développement

### 11/11/2026

*   **Nouveautés** : compteurs lus à la voix (« Alexa, quelle est la puissance du compteur séjour ? ») : puissance, consommation, consommation et production du jour, en W et kWh quelle que soit l'unité du plugin ; capteurs CO2, qualité de l'air, luminosité et pression ; appareils classés « moniteur de qualité de l'air » quand ils mesurent le CO2
*   **Nouveautés** : prises variables, lumières à bouton bascule, état booléen des lumières et stations météo reconnus (types génériques déjà gérés par Matter)

### 07/11/2026

*   **Nouveautés** : une consigne de thermostat hors de la plage de l'appareil est refusée par Alexa (« je peux uniquement régler la température entre 7 et 30 ») au lieu d'être envoyée ; test de bout en bout depuis AWS dans la fenêtre Santé ; état des dépendances et bouton « Relancer » natifs de Jeedom ; bouton « Révoquer la liaison Alexa » dans la configuration

### 05/11/2026 et 06/11/2026 — audit complet du plugin

*   **Corrections** : modes du thermostat (« éco » n'exécute plus un autre mode, les modes de la clim sont annoncés) ; température de couleur plus chaude / plus froide dans le bon sens et en mireds pour les lampes Zigbee ; valeurs des scènes « actions » transmises selon le type de commande ; volets sans position (montée, descente, stop) découverts ; sonnette annoncée à chaque appui ; détection de mouvement brève annoncée
*   **Sécurité** : seule la clé API du plugin est acceptée par le proxy ; état et commandes toujours reconstruits côté Jeedom (un appel forgé ne peut plus viser une autre commande) ; jetons OAuth chiffrés et code d'autorisation à usage unique ; pages d'essai AVS retirées
*   **Exploitation** : envois d'état vers Alexa moins nombreux (un capteur qui oscille n'envoie plus des centaines de messages par jour) ; délais réseau bornés vers AWS ; suspension automatique de la Lambda une fois par mois au plus ; protection quand deux box partagent le même compte AWS ; liste de mise en route sur la page du plugin et politique IAM minimale à copier ; push d'état activé dès la création du skill ; skill disponible en anglais, allemand, espagnol et italien

### 04/11/2026

*   **Nouveautés** : suivi de la consommation et du coût AWS (alertes à 50, 80 et 100 % de la limite gratuite, budget AWS, suspension automatique optionnelle) ; équipement d'état « Alexa SmartHome (AWS) » ; relecture de l'Account Linking d'un skill existant

### 02/11/2026 et 03/11/2026

*   **Nouveautés** : connexion « Se connecter à AWS » sans clé IAM permanente et rôle d'exécution créé automatiquement ; Lambda limitée au skill et placée dans la région du compte Alexa ; client OAuth modifiable ; mesures d'Amazon dans la fenêtre Santé ; suspension / réactivation de la Lambda

### 31/10/2026 et 01/11/2026

*   **Nouveautés** : plugin renommé `jee2alexa` ; création et mise à jour du skill Smart Home depuis le plugin ; push d'état via l'autorisation donnée par Alexa à la liaison du compte

### Octobre 2026

*   **Première version** : skill Alexa Smart Home sur votre propre Lambda AWS, liaison du compte, découverte et pilotage des équipements Jeedom par types génériques, scènes Alexa
