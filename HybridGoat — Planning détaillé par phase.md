# HybridGoat — Planning détaillé par phase

> Complète la section 8 du cahier des charges v2 (qui garde les estimations d'heures par phase). Ici : ce qu'il y a concrètement à faire dans chaque phase, sans heures ni commandes. Phase 0 et début de Phase 1 : voir `semaine1-recette-hybridgoat.md` pour le détail complet (objectifs/étapes/sous-étapes) — condensé ici pour éviter la duplication.

## Phase 0 — Design & threat model

**Objectif** : poser l'architecture cible et les fondations documentaires avant de toucher au code.

**Étapes**

- Schéma d'architecture de l'environnement hybride (AWS + Azure + lien hybride)
- Choix des 6 misconfigs + mapping vers des ressources Terraform précises — déjà fait, cf. section 6 du cahier des charges
- Doc de design + scope + règles d'engagement (livrable #1 officiel)

## Phase 1 — Build IaC

**Objectif** : avoir l'infrastructure complète (AWS + Azure + lien hybride) qui se déploie proprement via `terraform apply`, encore sans vulnérabilités injectées.

**Étapes**

- Setup repo Git + structure modules Terraform + provider AWS
- Ressources AWS : VPC, EC2 + instance profile, S3, users/roles IAM
- Ressources AWS : Lambda, API Gateway
- Provider Azure + ressources : VM, Storage Account, Key Vault
- Ressources Azure : App/Function, utilisateurs Entra, groupes, service principals
- Lien hybride simulé : utilisateur synchronisé (PHS) + identité privilégiée (DSA)

## Phase 2 — Injection de vulnérabilités + scan IaC

**Objectif** : transformer l'infra "propre" de la Phase 1 en infra volontairement vulnérable, puis vérifier ce que les scanners automatiques en détectent.

**Étapes**

- Implémenter les 6 misconfigs dans le Terraform, une par une, en te basant sur le tableau de mapping posé en Phase 0
- Installer et lancer Checkov sur le repo
- Installer et lancer Trivy sur le repo
- Documenter, misconfig par misconfig, si Checkov et/ou Trivy l'a détectée — le comparatif de couverture (livrable #3)

## Phase 3 — Recon & énumération + début du mapper

**Objectif** : déployer réellement l'infra, l'auditer depuis l'extérieur comme un attaquant le ferait, et démarrer le Cloud Web Mapper.

**Étapes**

- Déployer l'environnement (`terraform apply`) et valider que tout fonctionne
- Audit de configuration avec ScoutSuite
- Audit de configuration avec Prowler
- Énumération Entra avec AzureHound (depuis la VM Kali)
- Énumération Entra avec ROADtools si AzureHound pose souci (fallback)
- Énumération AWS avec Pacu (depuis la VM Kali)
- Énumération AWS avec CloudFox (depuis la VM Kali)
- Cloud Web Mapper : authentification sur AWS (boto3) et sur Entra/Azure (azure-identity)
- Cloud Web Mapper : extraction des principals/policies IAM et des users/rôles/service principals/état de sync côté Entra

## Phase 4 — Exploitation & escalade de privilèges

**Objectif** : transformer les données brutes de la Phase 3 en chemins d'attaque exploitables, puis valider ces chemins en les exécutant réellement.

**Étapes**

- Cloud Web Mapper : parser les relations de confiance/permissions pour calculer les chemins d'escalade et de mouvement latéral
- Cloud Web Mapper : exporter ces chemins vers BloodHound via OpenGraph (fallback JSON/CSV si OpenGraph pose souci)
- Exécuter la chaîne AWS : SSRF → IMDS → privesc IAM
- Exécuter la chaîne : trust OIDC GitHub→AWS trop large → CloudFormation → admin
- Exécuter la chaîne hybride : AD → Entra Connect → Global Admin
- Documenter chaque chaîne avec des preuves (captures, logs) et vérifier qu'elle correspond au chemin prédit par le mapper

## Phase 5 — Détection/purple-team

_Core depuis le recadrage blue team (section 12 du cahier des charges v2) — plus optionnelle._

**Objectif** : comprendre, pour chaque attaque exécutée en Phase 4, ce qui est détectable dans les logs cloud et ce qui ne l'est pas — la partie qui parle directement à un profil blue team/RSSI.

**Étapes**

- Rejouer les techniques clés avec Stratus Red Team (depuis la VM Kali)
- Chasser ces techniques dans les logs CloudTrail (AWS) et les diagnostic settings (Azure)
- Noter, technique par technique, ce qui était détectable et ce qui ne l'était pas — et pourquoi (logging désactivé ? alerte absente ? signal noyé ?)
- Documenter des pistes de remédiation/détection pour chaque trou identifié

## Phase 6 — Rapport professionnel

**Objectif** : produire le livrable final montrable — celui que tu enverras vraiment en entretien.

**Étapes**

- Rédiger le résumé exécutif et la méthodologie
- Rédiger les findings détaillés (notation style CVSS, impact business, étapes de reproduction), un par misconfig/chaîne d'attaque
- Rédiger la section couverture de détection (nouveau depuis le recadrage blue team) — ce qui était détectable ou pas, par chaîne
- Rédiger la remédiation pour chaque finding
- Rédiger l'annexe sur l'outil (Cloud Web Mapper)
- Relecture et mise en forme (skill rapport-maoky pour le PDF/DOCX final)