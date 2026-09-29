# Création de comptes

il existe un compte root et un compte iam, ne pas utiliser le compte root en production.
Evitez/voir jamais utiliser un compte à forts privilèges.
On l'utilise juste pour créer un compte IAM (Identity & Access Management)

## Compte Root
![](../Img/Pasted%20image%2020260929184057.png)
## Compte IAM

Un compte IAM sert à la gestion des users, grp, permissions,...

2 facons d'utiliser aws:
- Accéder à l'intaraface graphique 
- Utiliser api en utilsant le terminale/cli (interface en ligne de commande ) qui permet de faire des actions via API
  
Faire l'auth multi-facteurs