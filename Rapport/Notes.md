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








