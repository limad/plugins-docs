---
layout: default
title: "jee2alexa — Services AWS"
lang: fr_FR
pluginId: jee2alexa
---
# Services AWS — Pertinence pour Jeedom

> Ce plugin n'utilise directement que **Lambda** et **IAM** (voir
> [configuration-aws.md](configuration-aws.html) pour le rôle d'exécution et
> l'utilisateur de déploiement). Le reste de ce document est une note de
> synthèse plus large, à destination d'un utilisateur qui voudrait explorer
> d'autres services AWS en complément de son installation Jeedom — aucun de
> ces services additionnels n'est requis par `jee2alexa`.

## Calcul

**Services** : EC2, Lightsail, Lambda, Batch, Elastic Beanstalk, Serverless Application Repository, AWS Outposts, EC2 Image Builder, AWS App Runner, Parallel Computing Service, AWS Global View

Lambda est le service clé de cette catégorie — il permet de déporter des traitements (reconnaissance d'image, calculs lourds, webhooks) hors du serveur local sans gérer d'infrastructure, avec un free tier généreux. Lightsail peut servir à héberger une instance Jeedom cloud à moindre coût (serveur secondaire, relais, ou instance de test) si vous ne voulez pas gérer un VPC complet. Le reste (EC2, conteneurs, Beanstalk, Outposts, Batch...) est disproportionné pour un usage domotique domestique.

## Conteneurs

**Services** : Elastic Container Service, Elastic Kubernetes Service, Red Hat OpenShift Service on AWS, Elastic Container Registry

Aucune en pratique — orchestration de conteneurs à l'échelle, largement au-delà des besoins d'une installation domotique, même multi-sites.

## Stockage

**Services** : S3, EFS, FSx, S3 Glacier, Storage Gateway, AWS Backup, Recycle Bin, Reprise après sinistre d'AWS Elastic

S3 est le service le plus directement utile — sauvegardes externalisées, snapshots caméra, archivage de l'historique. S3 Glacier complète bien pour l'archivage à très long terme et coût minimal. AWS Backup peut orchestrer des règles de rétention automatiques sur ces sauvegardes. Le reste (EFS, FSx, Storage Gateway, DR Elastic) vise des besoins d'entreprise sans rapport avec Jeedom.

## Base de données

**Services** : Aurora and RDS, ElastiCache, Neptune, Amazon DocumentDB, Amazon Keyspaces, Amazon Timestream, DynamoDB, Aurora DSQL, Amazon MemoryDB, Oracle Database@AWS

Amazon Timestream est particulièrement adapté puisqu'il s'agit d'une base conçue pour les séries temporelles — exactement le type de données que Jeedom historise (température, consommation...). DynamoDB est une alternative NoSQL généraliste si vous voulez consolider des données entre plusieurs installations. Les bases relationnelles/graphe/documentaires (RDS, Neptune, DocumentDB, Keyspaces...) n'apportent rien de plus que le MySQL déjà utilisé par Jeedom.

## Migration et Transfert

**Services** : AWS Migration Hub, AWS Transform MGN, Application Discovery Service, Database Migration Service, AWS Transfer Family, AWS Snow Family, DataSync, AWS Transform, AWS Mainframe Modernization, Amazon Elastic VMware Service

Aucune — ces services concernent la migration d'infrastructures d'entreprise vers le cloud, sans lien avec un usage domotique.

## Mise en réseau et diffusion de contenu

**Services** : VPC, CloudFront, API Gateway, Direct Connect, AWS App Mesh, Global Accelerator, Route 53, AWS Data Transfer Terminal, Amazon Route 53 Global Resolver, AWS Cloud Map, RTB Fabric, Application Recovery Controller

API Gateway permet d'exposer un webhook déclenchant une Lambda depuis un scénario Jeedom (ou l'inverse). CloudFront est utile si vous hébergez un dashboard statique sur S3 (HTTPS, cache, performance). Route 53 peut fournir un nom de domaine propre pour accéder à Jeedom depuis l'extérieur. Le reste (VPC avancé, Direct Connect, App Mesh, Global Accelerator...) relève d'architectures réseau d'entreprise.

## Outils pour développeurs

**Services** : CodeCommit, CodeBuild, CodeDeploy, CodePipeline, Cloud9, CloudShell, X-Ray, AWS FIS, Infrastructure Composer, AWS App Studio, AWS DevOps Agent, AWS AppConfig, CodeArtifact, Amazon Q Developer, Amazon CodeCatalyst, Kiro

Marginale — utile uniquement si vous développez un plugin Jeedom de façon structurée avec CI/CD (CodePipeline/CodeBuild) ou codez dans un IDE cloud (Cloud9/CloudShell), mais un usage local (VS Code + Git) suffit largement pour ce cas de figure.

## Habiliter le client

**Services** : AWS IQ, Managed Services, Activate for Startups, AWS re:Post Private, Support

Aucune — services de support et d'accompagnement client, sans lien technique avec Jeedom.

## Blockchain

**Services** : Amazon Managed Blockchain

Aucune pertinence.

## Satellite

**Services** : Ground Station

Aucune pertinence.

## Quantum Technologies

**Services** : Amazon Braket

Aucune pertinence.

## Gestion et gouvernance

**Services** : AWS Organizations, CloudWatch, AWS Auto Scaling, CloudFormation, AWS Config, Service Catalog, Systems Manager, Trusted Advisor, Control Tower, AWS Well-Architected Tool, Amazon Q Developer in chat applications, Launch Wizard, AWS Compute Optimizer, Resource Groups & Tag Editor, Amazon Grafana, Amazon Prometheus, AWS Resilience Hub, Incident Manager, AWS pour SAP, AWS Telco Network Builder, AWS Health Dashboard, AWS Proton, Durabilité AWS, AWS User Notifications, AWS Partner Central, CloudTrail, AWS License Manager, AWS Resource Explorer, Service Quotas

CloudWatch permet de centraliser des métriques Jeedom (température, consommation, uptime) avec dashboards et alarmes — c'est aussi le service que ce plugin utilise déjà indirectement via `AWSLambdaBasicExecutionRole` pour les logs du Lambda (voir [configuration-aws.md](configuration-aws.html)). Amazon Grafana peut afficher ces mêmes données de façon plus poussée que les graphiques natifs Jeedom. AWS User Notifications est utile pour surveiller votre propre facturation AWS si vous multipliez les services. Le reste (Organizations, Auto Scaling, CloudFormation, Config, Control Tower...) vise la gestion d'infrastructures d'entreprise à grande échelle.

## Services multimédias

**Services** : Kinesis Video Streams, MediaConvert, MediaLive, MediaPackage, MediaStore, MediaTailor, Elemental Appliances & Software, Amazon Interactive Video Service, Elemental Inference, AWS Deadline Cloud, MediaConnect

Kinesis Video Streams pourrait servir à ingérer et stocker des flux vidéo de caméras IP de façon managée, en alternative au stockage local. Le reste (MediaConvert, MediaLive, MediaPackage...) cible la diffusion vidéo professionnelle (streaming broadcast), hors de portée d'un usage domotique.

## Machine Learning

**Services** : Amazon SageMaker AI, Amazon Augmented AI, Amazon CodeGuru, Amazon DevOps Guru, Amazon Comprehend, Amazon Forecast, Amazon Fraud Detector, Amazon Kendra, Amazon Personalize, Amazon Polly, Amazon Rekognition, Amazon Textract, Amazon Transcribe, Amazon Translate, Amazon Monitron, AWS HealthLake, Amazon Lookout for Equipment, Amazon Q Business, Amazon Bedrock, AWS Agent Registry, Plateforme Claude sur AWS, AWS HealthOmics, Amazon Nova Act, Amazon Bedrock AgentCore, Amazon Q, Amazon Comprehend Medical, Amazon Lex, Amazon Bio Discovery, AWS HealthImaging

Amazon Rekognition est le plus directement utile, couplé aux caméras Jeedom pour de la détection intelligente (personnes, véhicules) plutôt qu'une simple détection de mouvement. Amazon Polly offre une synthèse vocale de qualité pour les annonces sur enceintes connectées. Amazon Transcribe et Amazon Lex permettent de construire un assistant vocal maison. Amazon Forecast pourrait, en théorie, prédire des besoins de consommation énergétique à partir de l'historique Jeedom. Les autres services (SageMaker, HealthLake, Bio Discovery, Fraud Detector...) sont hors sujet pour un usage domotique.

## Analyse

**Services** : Athena, Amazon Redshift, CloudSearch, Amazon OpenSearch Service, Kinesis, QuickSight, AWS Data Exchange, AWS Lake Formation, MSK, AWS Glue DataBrew, Amazon FinSpace, Managed Apache Flink, EMR, AWS Clean Rooms, Amazon SageMaker (DataZone), AWS Entity Resolution, AWS Glue, Amazon Data Firehose, Amazon DataZone, Amazon Quick

QuickSight permet de créer des dashboards de visualisation plus poussés que les graphiques natifs Jeedom, à partir de données exportées vers S3/Timestream. Kinesis (Data Streams/Firehose) peut ingérer en flux continu des données de capteurs. Le reste (Redshift, EMR, Glue, Lake Formation...) cible du traitement de données à grande échelle, sans rapport avec le volume généré par une installation domotique.

## Sécurité, identité et conformité

**Services** : Resource Access Manager, Cognito, Secrets Manager, GuardDuty, Amazon Inspector, Amazon Macie, IAM Identity Center, Certificate Manager, Key Management Service, CloudHSM, Directory Service, AWS Firewall Manager, AWS Artifact, Detective, AWS Signer, Security Lake, AWS Security Agent, Amazon Verified Permissions, AWS Audit Manager, Security Hub CSPM, IAM, WAF & Shield, Security Hub, AWS Private Certificate Authority, AWS Payment Cryptography, AWS Security Incident Response

IAM est indispensable dès qu'on utilise n'importe quel autre service AWS — c'est déjà le cas pour ce plugin (rôle d'exécution Lambda + utilisateur de déploiement scopé, voir [configuration-aws.md](configuration-aws.html)). Secrets Manager permet de stocker vos clés API/identifiants de façon chiffrée plutôt qu'en clair dans la config Jeedom. Key Management Service (KMS) peut chiffrer les sauvegardes S3 avant envoi. Cognito est utile si vous développez une app custom pour piloter Jeedom avec gestion d'utilisateurs. Le reste (GuardDuty, Macie, Firewall Manager, Security Hub, CloudHSM...) relève de la sécurité d'entreprise à grande échelle.

## Gestion financière dans le cloud

**Services** : AWS Marketplace, AWS Billing Conductor, AWS FinOps Agent, Gestion de la facturation et des coûts

Sans lien direct, mais utile en tant qu'utilisateur AWS pour surveiller les coûts générés par les services ci-dessus.

## Mobile

**Services** : AWS Amplify, AWS AppSync, Device Farm, Amazon Location Service

Amazon Location Service peut servir à des scénarios de géolocalisation/geofencing (déclenchement à l'arrivée/départ) sans dépendre d'un service tiers comme Life360. AWS Amplify permet de créer une interface web/mobile custom pour piloter Jeedom, hébergée sur AWS. AppSync et Device Farm sont trop spécifiques au développement d'applications mobiles professionnelles pour être pertinents ici.

## Intégration des applications

**Services** : Step Functions, Amazon AppFlow, Amazon MQ, Simple Notification Service, Simple Queue Service, SWF, Apache Airflow géré, AWS B2B Data Interchange, Amazon EventBridge

Catégorie très pertinente. Amazon EventBridge peut centraliser tous les événements Jeedom (changements d'état, alertes) et les router automatiquement vers Lambda, SNS, S3, etc., sans code de routage à écrire à la main. Simple Notification Service (SNS) et Simple Queue Service (SQS) couvrent respectivement les notifications push/SMS/email et la mise en file d'attente fiable des événements. Step Functions permet d'orchestrer des workflows multi-étapes complexes. Le reste (AppFlow, Amazon MQ, SWF, Airflow, B2B Data Interchange) cible des intégrations d'entreprise sans rapport avec un usage domotique.

## Applications d'entreprise

**Services** : Amazon Connect Customer, Amazon Chime, Amazon Simple Email Service, Amazon WorkDocs, Amazon WorkMail, Amazon Connect Health, AWS Supply Chain, Amazon Pinpoint, AWS Wickr, AWS AppFabric, AWS End User Messaging, Amazon Chime SDK

Amazon Simple Email Service (SES) est utile pour l'envoi fiable et peu coûteux de rapports/alertes par email depuis un scénario Jeedom. Le reste (Connect, Chime, WorkDocs, WorkMail, Supply Chain, Pinpoint...) vise des besoins de communication et de collaboration d'entreprise sans lien avec la domotique.

## Computing de l'utilisateur final

**Services** : WorkSpaces, WorkSpaces Applications, WorkSpaces Thin Client, WorkSpaces Secure Browser

Aucune — postes de travail virtuels d'entreprise.

## Internet des objets

**Services** : IoT Device Defender, IoT Device Management, IoT Greengrass, IoT SiteWise, IoT Core, IoT TwinMaker, IoT Events, AWS IoT FleetWise

Catégorie la plus naturelle pour un usage domotique. IoT Core offre une passerelle MQTT managée et sécurisée, utile pour fédérer plusieurs installations Jeedom distantes via des certificats par device plutôt que d'exposer directement un serveur. IoT Greengrass permet de faire tourner des fonctions Lambda localement sur le serveur Jeedom (edge computing), avec synchronisation cloud, réduisant la dépendance à la latence réseau. IoT Events permet de détecter des patterns/anomalies sur des flux de capteurs (ex : porte restée ouverte anormalement longtemps), en complément des scénarios Jeedom. IoT SiteWise, IoT TwinMaker et IoT FleetWise restent orientés vers des usages industriels ou automobiles, hors de portée domestique.

## Développement de jeux

**Services** : Amazon GameLift Servers, Amazon GameLift Streams

Aucune pertinence.

---

## Synthèse — sélection resserrée pour un usage domestique

Pour une installation Jeedom classique, les services les plus rentables (gratuits ou quasi gratuits en usage domestique typique, faciles à mettre en œuvre) sont :

- **S3** — sauvegardes, snapshots caméra
- **Lambda** — traitement événementiel serverless (déjà utilisé par `jee2alexa`)
- **EventBridge** — routage centralisé des événements Jeedom vers d'autres services AWS
- **SNS / SES** — notifications et emails
- **Rekognition** — détection intelligente sur les flux caméra
- **IAM + Secrets Manager** — gestion sécurisée des accès et des identifiants (le principe de moindre privilège IAM est déjà appliqué par ce plugin, voir [configuration-aws.md](configuration-aws.html))

Les services plus avancés (Timestream, Kinesis, QuickSight, IoT Core/Greengrass) prennent tout leur sens dans un contexte multi-sites ou pour une architecture domotique plus ambitieuse, mais impliquent un coût récurrent et une complexité additionnelle à mettre en balance avec le besoin réel.
