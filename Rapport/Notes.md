[Module 1: Introduction to the Cloud](https://skillbuilder.aws/cds/42a9221b-ff91-4cea-8023-62953205342d/index.html?endpoint=https%3A%2F%2Fskillbuilder.aws%2Flrs&actor=%7B%22name%22%3A%22332b4f2a-3e65-4609-8e42-5c7f60a9c361%22%2C%22openid%22%3A%22https%3A%2F%2Fgandalf-prod.auth.us-east-1.amazoncognito.com%2Fus-east-1_KcXGNtlRY%7C332b4f2a-3e65-4609-8e42-5c7f60a9c361%22%2C%22objectType%22%3A%22Agent%22%7D&module_id=ZWYR2AX7VG%3A001.000.000&registration_id=db1fdb10-fca8-5627-b1c3-4148dbfea559&registration=db1fdb10-fca8-5627-b1c3-4148dbfea559&product_id=8D79F3AVR7%3A002.002.003&activity_id=http%3A%2F%2FYH7XhsyCjD_L0JQit9x_dQJfo3E1RR5l_rise&_cb=1786705520556#/)
https://skillbuilder.aws/learn/94T2BEN85A/aws-cloud-practitioner-essentials/8D79F3AVR7

Client-server model
- un client vient et demande quelque chose à une personne de la boutique ==
  un client fait une requete au server pour avoir accès à un document en fonction de ses permissions
- et le server renvoit une information status okk or not au client 

More complicated

###### Immuabilité

L'**immuabilité** est un paradigme selon lequel une fois qu'une ressource d'infrastructure (comme un serveur ou un conteneur) est déployée, elle ne doit jamais être modifiée directement.  Si une mise à jour ou un correctif est nécessaire, l'ancienne ressource est détruite et une nouvelle instance, incluant les modifications, est provisionnée >

mais si on fait une nouvelle instance comme par exemple un server et qu'on décide de le changer sa veut dire on shutdown le premier server pour le remplacer mais sa va pas paralyser l'infra ? >

Non, cela ne paralyse pas l'infrastructure. C'est précisément pour éviter l'interruption de service que l'immuabilité est couplée à des **stratégies de déploiement spécifiques** et à un **équilibreur de charge**. >

Lors d'une mise à jour, le processus suit ces étapes strictes :

1. **Provisionnement parallèle** : L'outil d'IaC (Terraform, CloudFormation, etc.) lance la nouvelle instance avec la mise à jour, tandis que l'ancienne continue de traiter le trafic. 
    
2. **Validation** : Le système attend que la nouvelle instance soit totalement prête et passe ses tests de santé (_health checks_). 
    
3. **Bascule du trafic** : Une fois la nouvelle instance jugée saine, l'équilibreur de charge commence à lui envoyer du trafic.  Selon la stratégie choisie, cela peut être immédiat ou progressif.
    
4. **Désactivation** : Seulement après que le trafic a été transféré, l'ancienne instance est retirée de l'équilibreur puis détruite.
## Concepts

### Cloud Computing

#### Cloud 

C'est ton service qui tourne chez quelqu'un d'autre comme AWS, tu loues une machine ( serveurs) qui tournes chez eux, des fournisseurs cloud.
Exemple :
- Une entreprise a une application.
  Au lieu d’acheter 10 serveurs physiques, elle loue des serveurs dans le cloud.
Conclusion :
- Cloud = ressources informatiques hébergées chez un fournisseur externe.

![](../Img/Pasted%20image%2020260820132845.png)

![](../Img/Pasted%20image%2020260820133000.png)

![](../Img/Pasted%20image%2020260820133059.png)

![](../Img/Pasted%20image%2020260820133307.png)

#### On-premises

C'est ton service/ serveur qui tourne chez toi dans tes locaux, tu passes pas par un fournisseur cloud. L’avantage est que l’entreprise contrôle directement ses machines donc ses données à 100%. Mais ça coûte cher : il faut acheter les serveurs, les maintenir, les refroidir, gérer l’électricité.
Exemple :
- Une banque possède une salle remplie de serveurs dans ses propres bâtiments.  
  Ses applications et ses bases de données fonctionnent sur ces serveurs.
Conclusion :
- On-premises = les ressources sont physiquement chez l’entreprise.

#### Hybrid

C'est un mélange de serveurs dans le cloud et dans les locaux.
Sa permet de prendre le meilleur des 2.
Exemple:
- Données sensibles comme des données médicaux ou bancaires dans ses propres locaux pour plus de confidentialité.
  Et la puissance de Cloud ainsi que des machines mise à disposition pour faire tourner l'appli à pleine puissances.
Conclusion :
- **Hybride** = Un peu chez soi + un peu sur Internet


### Responsability

![](../Img/Pasted%20image%2020260923215205.png)

### EC2

Les instances des servers que Amazon propose.
like les machines virtuels >> on peut choisir ses puissances, et tout prix non fix
the concept of multi-tenancy. > ressource sharing and isolation fait par un hyperviseur 

Pour communiquer avec on le fait via API requests :
- 
Launch an instance
- selectionner une AMI (Amazon Machine Image) > - Region,OS, logiciels , Processor architecture, Virtualization type, root volume type.
AMI == sont des images vm pre-built comme docker vulgairement
Avec une seule AMI on peut lancer plusieurs mêmes instances
Il y a 3 facons de utiliser une AMI :
- on fait le notre 
- utiliser une préconfig
- acheter des Amis sur le worksplace amazon
Le point fort des AMi est la répétition c'est le fait qu'avec la même configurations on peut ducoup automatiser tout un processus avec les mêmes environnement pout ensuite le scaler



- Hardware ressources, CPU, memory, network
Connect
- ssh ou rdp
Use
- on peut commencer à lancer les commandes 

#### Types

Amazon propose plusieurs EC2 : ce qui changent sont :
- CPU
- Memory
- Espace
- Network capacibilities
en fonction de ce que je veux faire

Différenctes familles :
- general purpose
un peu de tout c bien equilibrer au niveau de tout les fonctionnalités 
on peut faire des servers webs et du code
- compute optimized
des tâches puissantes comme des servers de jeux et même de la modélisation scientifique 
- mémoire optimized
memoire ++ > fast data processing
- acceleration optimized
calculs, data pattern, maths
ils utilients hardware accelerators
- storage optimized
stocker énormement de données

### How to Provision AWS Resources
to interact AWS services
AWS Management Console
- interface normale ou on peut cliquer et selectionner voir différentes choses == like monitoring === ez peace

AWS Command Line Interface
- APi calls with ur terminal

AWS SDK
- interact avec d'autres langages comme python ( appels comme Snowflake)
Customer et AWS responsabilitées

 managed and unmanaged services
 
### Scaling and Load balancing

-  Scaling up : On peut scaler en ajoutant plus de puissances et ressources à une machine 
- Scaling out : On peut scaler en ajoutant plus de machines 
![[Pasted image 20260924134445.png]]

Elasticity >> on peut automatiquement augmenter les ressources et les baisser en fonction du traffic sur le moment , c'est efficace si tu veux economiser et que le système s'adapte en temps réel  
![[Pasted image 20260924134739.png]]

#### ELB (Elastic Load Balancing)

pour continuer dans l'optique de l'elasticity pour scaler et eviter que sa soit qu'un server qui recoit malgré la présence des autres servers on met un load balancer entre les users et les servers.
C'est lui qui va envoyer les requetes sur les différents servers.

Routings methods :
- round robin >> le traffic se distribue dans le server dans un movement circulaire
![[Pasted image 20260924143002.png|162]]
- least connections >> le traffic se distribue à celui qui a le moins de connections 
![[Pasted image 20260924143026.png|340]]
- ip hash >> utilise l'adresse ip du client pour essayez de use la même route au même server
- least Response Time >> redirige le traffic en fonction du temps de reponse le plus rapide.

#### Messaging and Queuing

Imaginons qu'un server reçoit plusieurs requêtes mais que le server down pdnt un moment, les requetes ne sont pas forcement perdues si on les met sous une forme de file d'attente
Amazon Simple Queue Service (Amazon SQS) 
- send messages/ store et receive sous une forme de payload (ou toute la requete est contenue dedans dans la file d'attente avant qu'elle soit traitée)
- 
 Amazon Simple Notification Service (Amazon SNS)
- envoit la requetes à tout les services en fonction de ce que tu veux et de l'urgence et peut être perdue r
tightly coupled and loosely coupled architectures
- tightly coupled c'est quand un composant fail dans l'infra alors tout l'infra est paralyze
- loosely coupled est quand il y a un système de requêtes en attente
![[Pasted image 20260924163958.png]]

Monolithic applications >> une application qui contient plusieurs composant qui tourne sur un service
![](../Img/Pasted%20image%2020260924221442.png)

Microservices architecture >> une application qui contient plusieurs composants qui tourne sur plusieurs services 
![](../Img/Pasted%20image%2020260924221652.png)

Amazon EventBridge >> bus d'énvènement qui permet de connecter différents applications entre elles ainsi que des services internes AWS, il se charge de distribuer les informations à toute les applications/services qui ont besoin de savoir

### Compute services

 unmanaged, managed, and serverless compute services in AWS.
- managed service est quand tu delaisses certaines responsabilitées que t'as à AWS comme l'infrastructure >> ce qui te donne moins de champs  de controles que si c'etait unmanaged service est que tout soit sur ta tête 
- serveless compute == fully managed services  est quand tu ne peux pas voir les caracteristics du hardware ou des instances spécifiques qui sont sur tes applications 

![](../Img/Pasted%20image%2020260925122549.png)

#### AWS LAMBDA

 AWS Lambda est un serveless compute, c'est un puissant service qui permet d'executer du code sans gérer l'infrastucture dont les servers. 

Par exemple on fait une application et il faut ensuite la deployer, voir les ressources, etc mais grace à AWS Lambda pas besoin de ça on fait juste une lambda function ( your code ) > que le trigger va executer cette function ( une function lambda doit être activé par un declencheur == trigger)
- pour ensuite configurer un trigger pour quoi ? et comment
Trigger >> fait la liaison entre le code et un evenement que l'utilisateur a provoqué :
- ex : l'utilisateur envoit un mail , le trigger lance la lambda function qui permet d'envoyer le mail à son destinataire
- step : configuration de la liasion dans la console aws lambda  > event se prduit > le service envoit un json event avec toute les infos pour activer la function lambda et celle-ci s'execute puis s'artt ( elle ne tourne pas h24 d'ou le trigger)

C bien pour des taches rapides ( runtime) limite d'une fonction lambda est max 15min

On peut coder le sien ( event == infos sur un fichier ou l'mail recu , context information== temps restant et id de la requete,... entre lambda et la function )


### Containers

Un conteneur est un ensemble de code, config, runtime et dependances

how containers create a consistent and portable runtime environment across different systems.
=> container start, stop and run à travers un cluster (= groupe)
- sa scale automatiquement en fonction du traffic
![410](../Img/Pasted%20image%2020260925185816.png)
 Amazon Elastic Container Registry (Amazon ECR)
- C'est là où sont stocker les images des containers pour les environnement docker 
Amazon ECS and Amazon EKS orchestrate containers to deploy, scale, and manage applications.
- ECS(Elastic Container Service) == simple , definit quelques paramètres and fully managed service is like a docker == scalable container orchestration service for running and managing containers on AWS( utilise des images docker)

- EKS(Elastic Kubernetes Service) == open-source, plus complex et plus de control donc plus de flexibility like ECS mais on utilise kubernetes avc du langage .yaml 

AWS Fargate runs containers without the need to provision or manage servers
- endroit où on peut run les dockers
**Fargate vs EC2 :** Choisir entre EC2 et Fargate détermine la gestion de la couche sous-jacente. Avec EC2, tu gères les serveurs ; avec Fargate, AWS gère les serveurs à ta place (mode _serverless_).

Step : Commencer à mettre une image docker/container dans l'ECR >> choisir un service qui permet d'orchestrer et d'utiliser des dockers ( ECS ou EKS) > mtn on choisit où il va run EC2 ou AWS Fargate

![398](../Img/Pasted%20image%2020260925185550.png)
Le conteneur isole l'application et ses dépendances il ne recree pas l'OS comme la VM, il partage le kernel de l'Host

![415](../Img/Pasted%20image%2020260925185744.png)

![438](../Img/Pasted%20image%2020260925215157.png)

#### additional compute services

 Elastic Beanstalk streamlines environment provisioning and management.
 => service qui va permettre de mieux manager et rendre le deploiement plus facile des applications dans les instances EC2.
Tu lui donnes ton code et des fichiers de configuration , et il s'occupe de provisionner les instances EC2, de configurer le _Load Balancer_, l' _Auto Scaling_ et la surveillance.

AWS Batch manages large-scale computing tasks and automatically adjusts resources
=> pour des grosses utilités comme des gros calculs avec des gros processeurs pour.
Il prend soin de l'infrastructure pour toi et te permet de te concentrer sur le dev de ton application ou faire tes analyzes, il scale automatiquement aussi 
L'élément clé de **Batch** est l'exécution de traitements **par lots** (_batch processing_), c'est-à-dire lancer des centaines ou milliers de tâches asynchrones/en arrière-plan (ex: analyse de données, rendu vidéo, calculs financiers) qui s'exécutent, scalent puis s'arrêtent une fois terminées.

Amazon Lightsail streamlines web application setup and management without the need for complex infrastructure.
=> simple et efficace pas chère et fait la même chose qu'en haut, manage l'infrastructure
Lightsail est une solution **VPS (Virtual Private Server) clé en main** à prix fixe prévisible (ex: un petit serveur web, un WordPress), conçue pour les débutants, projets personnels ou petites entreprises. Contrairement à Elastic Beanstalk qui gère le scaling automatique complexe sur plusieurs instances EC2, Lightsail vous donne un serveur virtuel préconfiguré très simple avec un tarif mensuel fixe.


AWS Outposts extends AWS services to on-premises environments, supporting hybrid cloud architectures.
=> Pour les entreprises qui ont une architecture hybrid ou qui veulent
pour moins de latences et de la data chez toi 
