# Cahier des Charges — Identité hybride multi-cloud : construire, attaquer, détecter (Capstone HybridGoat)

### AWS + Azure/Entra ID — orientation sysadmin/RSSI (blue team)

**Version** : 2.0 — 1er octobre 2026 (révision majeure ; v1.0 du 13 août 2026 conservée comme historique dans le Journal, section 12) 
**Porteur du projet** : Maoky **Statut** : Draft — à optimiser/réviser avant lancement

---

## 1. Contexte

Je suis en L2 informatique à Guardia Cybersecurity School. Mon objectif professionnel est administrateur systèmes et réseaux (sysadmin), avec une évolution visée vers des postes à plus grandes responsabilités jusqu'à RSSI (Responsable de la Sécurité des Systèmes d'Information) — avec un intérêt particulier pour le secteur finance (banques, assurances). Mon orientation est blue team : administration, durcissement, supervision, défense et gouvernance de la sécurité. Je garde un intérêt pour la cybersécurité offensive, mais uniquement comme moyen de mieux comprendre les attaques pour mieux défendre.

Aucun projet de mon parcours actuel ne couvre en profondeur l'identité cloud hybride (AWS IAM + Azure/Entra ID), alors que c'est une surface de risque majeure en 2026 et une compétence très recherchée côté sécurité cloud/sysadmin — y compris dans le secteur finance, très réglementé sur ces sujets. Ce capstone comble ce trou : comprendre en profondeur comment ces identités cassent (escalade de privilèges IAM, chemins d'attaque hybrides AD↔Entra ID) pour savoir ensuite les défendre, les superviser et les auditer — avec une approche "construire, casser, détecter" pour garantir une compréhension réelle plutôt que superficielle.

Origine du projet : rapport de recherche du 13 août 2026 (uploadé dans ce projet en référence), rédigé à l'origine sous un angle pentest pur. Projet recadré en octobre 2026 vers l'angle sysadmin/RSSI/blue team — voir Journal des décisions (section 12).

## 2. Objectifs

**2.1 Objectifs pédagogiques**

- Comprendre en profondeur IAM AWS et Entra ID/Azure AD, et la mécanique de synchronisation hybride (Entra Connect).
- Maîtriser l'IaC avec Terraform, y compris l'introduction volontaire et documentée de vulnérabilités.
- Savoir scanner et auditer une infra cloud (IaC scanning + config auditing).
- Savoir cartographier des chemins d'attaque identité et écrire un outil qui interopère avec un standard de l'industrie (BloodHound).
- Savoir détecter ces mêmes attaques dans les logs cloud (CloudTrail/Azure diagnostics) et documenter ce qui est détectable ou pas — la boucle attaque→détection complète.

**2.2 Objectifs portfolio / CV**

- Obtenir un projet différenciant pour une trajectoire sysadmin → RSSI, particulièrement lisible pour un secteur réglementé comme la finance où la gouvernance identité/cloud est centrale.
- Démontrer le triptyque attendu d'un futur RSSI : construire une infra (Terraform), comprendre comment elle casse (attaque), savoir la superviser et la durcir (détection, remédiation).
- Produire un livrable montrable : rapport de sécurité complet (attaque + détection + remédiation) + repo GitHub + arguments concrets d'entretien.

**2.3 Niveau de difficulté visé** Bac+4/5, délibérément difficile — pensé pour pousser au-delà du niveau actuel, pas pour rejouer un tutoriel.

## 3. Périmètre

**3.1 Dans le scope**

- AWS + Azure/Entra ID, avec un lien hybride simulé (sync façon Entra Connect).
- Terraform écrit à la main (pas de simple déploiement d'un template existant).
- Minimum 6 mauvaises configurations documentées (catalogue en Section 6, inchangé).
- Outil "Cloud Web Mapper" en Python alimentant BloodHound via OpenGraph.
- Scan IaC (Checkov + Trivy) et audit de configuration (ScoutSuite/Prowler).
- Exécution complète d'au moins 3 chaînes d'attaque de bout en bout, depuis une VM Kali dédiée.
- Détection/purple-team (Phase 5) — passée d'optionnelle à **core**, cohérent avec l'orientation blue team.
- Rapport de sécurité professionnel final (attaque + détection + remédiation).

**3.2 Hors scope**

- GCP.
- Tout environnement de prod ou partagé.
- Toute cible réelle non autorisée.
- Dépenses cloud non maîtrisées.
- Développement d'un visualiseur de graphe maison — on s'appuie sur BloodHound existant.
- Maîtrise individuelle approfondie de chaque outil offensif (Pacu, CloudFox, etc.) — c'est la VM Kali qui les héberge, utilisés pragmatiquement pour générer les attaques, pas un objectif de compétence en soi.

## 4. Spécifications fonctionnelles

**4.1 Environnement cible**

- Côté AWS : VPC, EC2 (avec instance profile), S3, Lambda, API Gateway, users/roles IAM.
- Côté Azure : VM, Storage Account, Key Vault, App/Function, utilisateurs Entra, groupes, service principals.
- Lien hybride : un utilisateur synchronisé façon PHS, et une identité privilégiée façon compte de synchronisation d'annuaire (DSA).

**4.2 Outil "Cloud Web Mapper"** Doit :

- S'authentifier sur AWS (boto3) et sur Entra/Azure (azure-identity / MS Graph).
- Récupérer les principals/policies IAM côté AWS, et users/rôles/service principals/état de sync côté Entra.
- Parser les relations de confiance et de permissions pour calculer les chemins d'escalade de privilèges et de mouvement latéral.
- Exporter ces chemins vers BloodHound via OpenGraph (fallback JSON/CSV accepté si OpenGraph pose souci technique).

**4.3 Documentation des vulnérabilités** Chaque mauvaise configuration doit être documentée avec : le pattern réel qu'elle reflète (incident de référence) et la technique MITRE ATT&CK qu'elle permet.

**4.4 Rapport final** Structure attendue : résumé exécutif, méthodologie, findings notés (style CVSS) avec impact business, étapes de reproduction, **couverture de détection** (ce qui a été détecté ou raté, en lien avec la Phase 5), remédiation, annexe sur l'outil.

## 5. Spécifications techniques (baseline prescrite)

|Composant|Choix prescrit|Rôle|
|---|---|---|
|IaC|Terraform (providers `aws` + `azurerm`/`azuread`)|Construction de l'environnement|
|Langage outil|Python 3.x|Cloud Web Mapper|
|SDK AWS|boto3|Auth + extraction IAM|
|SDK Azure|azure-identity + MS Graph SDK (ou appels REST directs)|Auth + extraction Entra|
|Graphe attack path|BloodHound CE + OpenGraph|Visualisation & analyse des chemins|
|Plateforme offensive|VM Kali Linux dédiée|Héberge les outils offensifs ci-dessous, utilisés pragmatiquement — pas un objectif de maîtrise individuelle|
|Collecteur Entra|AzureHound (sur la VM Kali)|Enum Entra/Azure|
|Enum Entra (fallback)|ROADtools|Si Graph API pose souci|
|Scanners IaC|Checkov + Trivy|Détection des mauvaises configs dans le code|
|Audit de config|ScoutSuite, Prowler|Audit post-déploiement|
|Exploitation AWS|Pacu, CloudFox (sur la VM Kali)|Génère les attaques à détecter en Phase 5|
|Références de scénarios|CloudGoat, AWSGoat, AzureGoat, TerraGoat|Inspiration de design, jamais copiés tels quels|
|Détection/purple-team|Stratus Red Team|Phase 5 — désormais core, pas optionnelle|
|Versioning|Git|Historique + honnêteté du build|
|Comptes cloud|AWS free-tier isolé + tenant Azure free/dev isolé|Jamais de prod|

## 6. Catalogue des mauvaises configurations (baseline)

_Inchangé — confirmé explicitement conservé lors du recadrage d'octobre 2026._

|#|Mauvaise configuration|Pattern réel associé|Technique ATT&CK|
|---|---|---|---|
|1|Policy IAM trop permissive (`iam:CreatePolicyVersion`/`PassRole`)|Escalade de privilège IAM classique|T1548|
|2|EC2 avec IMDS accessible via SSRF|Vol de credentials via l'endpoint de métadonnées|T1552 / T1078.004|
|3|Trust OIDC GitHub Actions→AWS trop large + capacités IAM CloudFormation|Chaîne nx/UNC6426 (août 2025)|T1098 / T1550.001|
|4|Service principal Entra avec permissions Graph excessives|Scattered Spider / Storm-0501|T1098|
|5|Identité synchronisée éligible Global Admin sans MFA|Storm-0501 (reset mot de passe puis sync)|T1078.004|
|6|Logging CloudTrail/diagnostic Azure désactivé ou mal configuré|Évasion de défense|T1562|

## 7. Livrables

1. Doc de design + threat model.
2. Repo Terraform versionné + journal de build.
3. Catalogue de mauvaises configs + comparatif de couverture des scanners.
4. Outil "Cloud Web Mapper" (code + résultats d'énumération + export vers BloodHound).
5. Walkthrough d'exploitation avec preuves (captures, logs) pour chaque chaîne d'attaque.
6. Notes de détection et remédiation (Phase 5) — non optionnel désormais.
7. Rapport de sécurité professionnel final (attaque + détection + remédiation).

## 8. Planning & jalons (rythme parallèle — pas de semaines calendaires figées)

|Phase|Contenu|Effort estimé|
|---|---|---|
|0|Design & threat model|~5-8h|
|1|Build IaC|~20-30h|
|2|Injection de vulnérabilités + scan IaC|~8-12h|
|3|Recon & énumération + début du mapper|~20-25h|
|4|Exploitation & escalade de privilèges|~25-35h|
|5|Détection/purple-team|~8-10h|
|6|Rapport professionnel|~10-15h|

**Total** : ~105-145h (Phase 5 désormais incluse dans le cœur du projet, plus de phase "optionnelle" distincte). À 4-6h/semaine en rythme de croisière, ça s'étale sur environ 5 à 7 mois.

**Jalon MVP (minimum montrable)** : si le rythme ralentit trop, la version réduite reste : un seul cloud approfondi + une chaîne d'attaque hybride complète + version basique du mapper (export JSON/CSV en fallback) + mini-rapport. La Phase 5 reste la première chose qu'on coupe en cas de vrai manque de temps, malgré sa promotion en "core" — un MVP livré sans détection vaut mieux qu'un projet jamais fini.

## 9. Critères d'acceptation / de réussite

- [ ] L'environnement hybride AWS+Azure/Entra se déploie proprement via `terraform apply` sans intervention manuelle.
- [ ] Au moins 6 mauvaises configurations sont implémentées et documentées (pattern réel + technique ATT&CK).
- [ ] Le Cloud Web Mapper s'authentifie sur les deux clouds et exporte au moins un chemin d'attaque exploitable (vers BloodHound ou en fallback JSON).
- [ ] Au moins 3 chaînes d'attaque complètes sont exécutées et documentées avec preuves.
- [ ] Le scan IaC (Checkov+Trivy) tourne sur le repo et le comparatif de couverture est documenté.
- [ ] Au moins une passe de détection est exécutée et documentée (Phase 5) — ce qui était détectable et ce qui ne l'était pas.
- [ ] Un rapport de sécurité professionnel complet est livré (attaque + détection + remédiation).
- [ ] Le repo GitHub est publié avec un avertissement clair "volontairement vulnérable — ne pas déployer en prod".
- [ ] Le Journal des décisions techniques (Section 12) reflète les éventuels changements de stack en cours de route.

## 10. Contraintes

- Comptes cloud isolés uniquement (AWS free-tier + Azure free/dev), jamais de prod ni de compte partagé.
- `terraform destroy` entre les sessions pour maîtriser le coût, alertes de facturation actives.
- Projet solo, sans équipe.
- Temps disponible partagé avec le curriculum Python en cours et la recherche d'alternance (septembre 2027).
- Jamais d'attaque en dehors de l'infra déployée par mes soins sur mes propres comptes.

## 11. Risques & mitigations

|Risque|Impact|Mitigation|
|---|---|---|
|Dérive de scope (feature creep)|Le projet ne finit jamais|Se référer strictement au périmètre (Section 3) ; toute extension part dans une liste "v2" séparée|
|Outil déprécié/cassé en cours de route|Blocage technique|Fallbacks prévus (Section 5) ; documenter la limite plutôt que forcer|
|Rythme qui s'étiole|Projet abandonné à mi-chemin|Jalon MVP (Section 8) comme filet de sécurité|
|Sur-ingénierie du Cloud Web Mapper|Temps perdu sur du polish|Se concentrer d'abord sur les chemins du catalogue (Section 6)|
|Phase 5 "core" grignote le temps du cœur offensif|Rapport final incomplet|Phase 5 reste la première coupée si le temps manque vraiment (Section 8, Jalon MVP)|

## 12. Journal des décisions techniques

|Date|Composant|Ancien choix|Nouveau choix|Raison|
|---|---|---|---|---|
|_(exemple)_ 20/09/2026|Enum Entra fallback|ROADtools|[outil trouvé]|ROADtools cassé par retrait Graph API|
|01/10/2026|Orientation projet|Pentest pur (objectif pentester, roadmap eJPT→CPTS→OSCP)|Sysadmin → RSSI, orientation blue team|Reconversion d'objectif professionnel ; eJPT/CPTS/OSCP et le portfolio pentest associé sont abandonnés|
|01/10/2026|Outillage offensif|Maîtrise individuelle des outils (Pacu, CloudFox, AzureHound, ROADtools)|VM Kali dédiée, outils utilisés pragmatiquement|L'offensif n'est plus l'objectif de compétence central — c'est un moyen de générer des attaques à détecter|
|01/10/2026|Phase 5 (détection)|Optionnelle, première coupée si manque de temps|Core, dans le périmètre standard (reste coupable en MVP uniquement)|Cohérence avec l'orientation blue team — c'est elle qui différencie le projet pour un profil RSSI|