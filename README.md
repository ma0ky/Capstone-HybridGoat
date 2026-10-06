# Cahier des Charges — Cloud Web Mapper (Capstone HybridGoat)

### Pentest d'identité hybride multi-cloud — AWS + Azure/Entra ID

**Version** : 1.1 — 13 août 2026 (maj 01/10/2026) **Porteur du projet** : Maoky **Statut** : Active — à optimiser/réviser avant lancement

---

## 1. Contexte

Je suis en L2 informatique, objectif pentester avec une expatriation US à terme. Ma roadmap actuelle : certifications eJPT (automne 2026) → CPTS (HTB Academy) → OSCP (financée employeur), et un portfolio de projets organisé en 4 phases — Recon, Exploit, AD/Post-exploit, Evasion & C2 (AD Web Mapper, OSINT Web-Mapper, CVE Radar, PoC Vault, Web Slinger, Spider-Sense IDS, Rapport-o-matic).

Aucun de ces éléments ne couvre le cloud, alors que c'est la compétence technique la plus recherchée par les recruteurs cyber en 2026. Ce capstone comble ce trou, sur le thème le plus actuel et le plus solide identifié : l'escalade de privilèges IAM et les chemins d'attaque identité hybrides AD↔Entra ID — avec une approche "construire puis casser" pour garantir une compréhension réelle plutôt que superficielle.

Origine du projet : rapport de recherche du 13 août 2026 (à uploader dans ce projet en référence).

## 2. Objectifs

**2.1 Objectifs pédagogiques**

- Comprendre en profondeur IAM AWS et Entra ID/Azure AD, et la mécanique de synchronisation hybride (Entra Connect).
- Maîtriser l'IaC avec Terraform, y compris l'introduction volontaire et documentée de vulnérabilités.
- Savoir scanner et auditer une infra cloud (IaC scanning + config auditing).
- Savoir cartographier des chemins d'attaque identité et écrire un outil qui interopère avec un standard de l'industrie (BloodHound).

**2.2 Objectifs portfolio / CV**

- Obtenir un projet différenciant par rapport à la trajectoire "cert-only" (eJPT/CPTS/OSCP), qui ne couvre pas le cloud.
- Démontrer une compétence alignée avec les attentes Thales/Airbus (Terraform, Docker, Python, DevSecOps, offensif) et avec le chemin AWS/Microsoft (AWS Security Specialty, AZ-500).
- Produire un livrable montrable : rapport de pentest pro + repo GitHub + arguments concrets d'entretien.

**2.3 Niveau de difficulté visé** Bac+4/5, délibérément difficile — pensé pour pousser au-delà du niveau actuel, pas pour rejouer un tutoriel.

## 3. Périmètre

**3.1 Dans le scope**

- AWS + Azure/Entra ID, avec un lien hybride simulé (sync façon Entra Connect).
- Terraform écrit à la main (pas de simple déploiement d'un template existant).
- Minimum 6 mauvaises configurations documentées (catalogue en Section 6).
- Outil "Cloud Web Mapper" en Python alimentant BloodHound via OpenGraph.
- Scan IaC (Checkov + Trivy) et audit de configuration (ScoutSuite/Prowler).
- Exécution complète d'au moins 3 chaînes d'attaque de bout en bout.
- Rapport de pentest professionnel final.

**3.2 Hors scope**

- GCP.
- Tout environnement de prod ou partagé.
- Toute cible réelle non autorisée.
- Dépenses cloud non maîtrisées.
- Développement d'un visualiseur de graphe maison — on s'appuie sur BloodHound existant.
- La phase détection/purple-team (Phase 5) reste optionnelle si le temps manque.

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

**4.4 Rapport final** Structure attendue : résumé exécutif, méthodologie, findings notés (style CVSS) avec impact business, étapes de reproduction, remédiation, annexe sur l'outil.

## 5. Spécifications techniques (baseline prescrite)

|Composant|Choix prescrit|Rôle|
|---|---|---|
|IaC|Terraform (providers `aws` + `azurerm`/`azuread`)|Construction de l'environnement|
|Langage outil|Python 3.x|Cloud Web Mapper|
|Environnement pentest|VM Kali Linux dédiée|Plateforme d'exécution des outils offensifs et d'énumération, séparée de la machine personnelle|
|SDK AWS|boto3|Auth + extraction IAM|
|SDK Azure|azure-identity + MS Graph SDK (ou appels REST directs)|Auth + extraction Entra|
|Graphe attack path|BloodHound CE + OpenGraph|Visualisation & analyse des chemins|
|Collecteur Entra|AzureHound|Enum Entra/Azure|
|Enum Entra (fallback)|ROADtools|Si Graph API pose souci|
|Scanners IaC|Checkov + Trivy|Détection des mauvaises configs dans le code|
|Audit de config|ScoutSuite, Prowler|Audit post-déploiement|
|Exploitation AWS|Pacu, CloudFox|Exploitation & situational awareness|
|Références de scénarios|CloudGoat, AWSGoat, AzureGoat, TerraGoat|Inspiration de design, jamais copiés tels quels|
|Purple team (optionnel)|Stratus Red Team|Phase 5|
|Versioning|Git|Historique + honnêteté du build|
|Comptes cloud|AWS free-tier isolé + tenant Azure free/dev isolé|Jamais de prod|

## 6. Catalogue des mauvaises configurations (baseline)

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
6. (Optionnel) Notes de détection purple-team.
7. Rapport de pentest professionnel final.

## 8. Planning & jalons (rythme parallèle — pas de semaines calendaires figées)

|Phase|Contenu|Effort estimé|
|---|---|---|
|0|Design & threat model|~5-8h|
|1|Build IaC|~20-30h|
|2|Injection de vulnérabilités + scan IaC|~8-12h|
|3|Recon & énumération + début du mapper|~20-25h|
|4|Exploitation & escalade de privilèges|~25-35h|
|5|Détection/purple-team (optionnel)|~8-10h|
|6|Rapport professionnel|~10-15h|

**Total** : ~95-135h hors phase optionnelle (~105-145h avec). À 4-6h/semaine en rythme de croisière, ça s'étale sur environ 4 à 7 mois.

**Jalon MVP (minimum montrable)** : si le rythme ralentit trop, la version réduite reste : un seul cloud approfondi + une chaîne d'attaque hybride complète (AWS IMDS→privesc OU pivot Entra Connect→Global Admin) + version basique du mapper (export JSON/CSV en fallback si OpenGraph n'est pas prêt) + mini-rapport.

## 9. Critères d'acceptation / de réussite

- [ ] L'environnement hybride AWS+Azure/Entra se déploie proprement via `terraform apply` sans intervention manuelle.
- [ ] Au moins 6 mauvaises configurations sont implémentées et documentées (pattern réel + technique ATT&CK).
- [ ] Le Cloud Web Mapper s'authentifie sur les deux clouds et exporte au moins un chemin d'attaque exploitable (vers BloodHound ou en fallback JSON).
- [ ] Au moins 3 chaînes d'attaque complètes sont exécutées et documentées avec preuves.
- [ ] Le scan IaC (Checkov+Trivy) tourne sur le repo et le comparatif de couverture est documenté.
- [ ] Un rapport de pentest professionnel complet est livré.
- [ ] Le repo GitHub est publié avec un avertissement clair "volontairement vulnérable — ne pas déployer en prod".
- [ ] Le Journal des décisions techniques (Section 12) reflète les éventuels changements de stack en cours de route.

## 10. Contraintes

- Comptes cloud isolés uniquement (AWS free-tier + Azure free/dev), jamais de prod ni de compte partagé.
- `terraform destroy` entre les sessions pour maîtriser le coût, alertes de facturation actives.
- Projet solo, sans équipe.
- Temps disponible partagé avec le curriculum Python en cours, la prépa eJPT (automne 2026) et la recherche d'alternance.
- Jamais d'attaque en dehors de l'infra déployée par mes soins sur mes propres comptes.

## 11. Risques & mitigations

|Risque|Impact|Mitigation|
|---|---|---|
|Dérive de scope (feature creep)|Le projet ne finit jamais|Se référer strictement au périmètre (Section 3) ; toute extension part dans une liste "v2" séparée|
|Outil déprécié/cassé en cours de route (ex. ROADtools/Azure AD Graph)|Blocage technique|Fallbacks prévus (Section 5) ; documenter la limite plutôt que forcer|
|Rythme qui s'étiole (étalé sur plusieurs mois)|Projet abandonné à mi-chemin|Jalon MVP (Section 8) comme filet de sécurité, à viser en priorité|
|Conflit calendaire avec la prépa eJPT (automne 2026)|Ralentissement voire arrêt|Anticiper une baisse de rythme plutôt qu'un abandon ; viser le MVP avant si possible|
|Sur-ingénierie du Cloud Web Mapper|Temps perdu sur du polish plutôt que sur la couverture des cas|Se concentrer d'abord sur les chemins du catalogue (Section 6) avant d'étendre|

## 12. Journal des décisions techniques

|Date|Composant|Ancien choix|Nouveau choix|Raison|
|---|---|---|---|---|
|_(exemple)_ 20/09/2026|Enum Entra fallback|ROADtools|[outil trouvé]|ROADtools cassé par retrait Graph API, [outil] fait le job|
|01/10/2026|Environnement pentest|Non spécifié|VM Kali Linux dédiée|Plateforme unique pour héberger tous les outils offensifs/d'énumération (Pacu, CloudFox, AzureHound, ROADtools, etc.), séparée de la machine personnelle|