# jee2alexa ou Matter (jee2matter) ?

Deux plugins exposent vos équipements Jeedom à Alexa :

- **jee2alexa** : un skill Alexa Smart Home qui vous appartient, relayé par une Lambda dans votre compte AWS
  (cette documentation).
- **jee2matter** : un pont **Matter local**. L'Echo parle directement à Jeedom sur le réseau de la maison,
  sans AWS ni Internet entre les deux.

Les deux se complètent : Matter pour les équipements courants, le skill pour ce que Matter ne sait pas
exposer à Alexa ou quand le réseau ne permet pas Matter.

## Ce que Matter fait mieux

- **Local** : pas de compte AWS, pas de Lambda, pas de droits IAM, aucun coût à surveiller.
- **Sécurité** : Jeedom n'a pas besoin d'être joignable depuis Internet en HTTPS. Pas d'OAuth, pas de jeton
  Amazon qui contient la clé du plugin.
- **États toujours à jour** : l'Echo est prévenu directement des changements. Pas besoin du
  [push proactif](#push-proactif-états-à-jour-sonnette) ni d'une redécouverte après un changement de réglage.
- **Plusieurs écosystèmes** : un même pont sert Alexa, Apple Maison et Google Home.
- **Mise en route** : un QR code à scanner dans l'application Alexa.

## Ce que le skill jee2alexa fait en plus

| | jee2alexa (skill) | jee2matter (Matter) |
|---|---|---|
| Lumières, prises, volets, ouvertures, présence, température, humidité, serrure, ventilateur | oui | oui |
| Thermostat et climatisation | oui, avec les modes Eco et Auto | arrêt, chauffage, froid |
| Scènes (« Alexa, lance… ») | oui | non |
| TV et multimédia (lecture, volume, chaînes) | oui | non |
| Caméra, alarme, sonnette | oui | non |
| Modes nommés : fil pilote, porte de garage, mode de ventilation | oui | non |
| Aspirateur, consigne d'humidité, oscillation | oui | non |
| Fuite, fumée / CO, qualité de l'air, pression, luminosité | non | oui |
| Nom et catégorie des appareils | choisis dans Jeedom, 6 langues | nommés dans l'application Alexa |
| Réseau | Jeedom joignable en HTTPS, où qu'il soit | IPv6 et mDNS sur le réseau local (voir plus bas) |
| Ressources sur la box | aucune | démon Node.js, environ 150 Mo |

> **N'exposez pas un même équipement par les deux plugins.** Alexa verrait deux appareils du même nom
> (« Deux éléments ont le même nom ») et ne saurait pas lequel piloter.

## Matter et le réseau : IPv6 et mDNS

Matter n'a **pas de repli en IPv4**. Sans IPv6 ou sans mDNS entre Jeedom et l'Echo, il ne fonctionne pas du
tout : le skill jee2alexa reste alors la seule solution.

- **mDNS** : l'Echo trouve le pont grâce à des annonces sur le réseau local (`_matterc._udp` pendant
  l'appairage, `_matter._tcp` ensuite). Sans elles, l'appairage échoue, et un pont déjà appairé devient
  injoignable.
- **IPv6** : tous les échanges Matter passent en UDP sur IPv6. Une adresse « lien local » (`fe80::`) suffit :
  ni la box Internet ni le fournisseur d'accès n'ont besoin de proposer l'IPv6.

| Situation | Que faire |
|---|---|
| IPv6 désactivé sur la box Jeedom (`disable_ipv6=1`, certaines images ou VM) | Le réactiver : l'adresse `fe80::` apparaît seule |
| Jeedom sous Docker en réseau « bridge » | Passer le conteneur en réseau `host` |
| Jeedom en VM derrière un NAT | Passer la carte réseau de la VM en mode pont |
| Echo et Jeedom sur des VLAN ou sous-réseaux différents | Le plus simple : les mettre sur le même réseau. Sinon il faut un relais mDNS (réflecteur Avahi) **et** de l'IPv6 routé entre les VLAN, car une adresse `fe80::` ne traverse pas un routeur |
| Wi-Fi invité, isolation des clients, filtrage multicast sur la borne | Désactiver ces options pour l'Echo |
| Jeedom distant (VPS, autre maison) | Impossible avec Matter : utiliser jee2alexa |

La page Santé de jee2matter vérifie l'interface réseau utilisée et la présence d'IPv6. Pour mDNS, le vrai
test reste l'appairage : si l'application Alexa ne trouve pas le pont, c'est presque toujours lui.
