# Prérequis et Décisions Fondamentales

> **Pour qui ?** Ce guide est pour les organisations qui démarrent leur premier projet, ou qui souhaitent structurer leur approche DevOps de manière cohérente dès le départ.

Avant même de créer votre premier repository ou d'écrire la première ligne de code, certaines décisions fondamentales doivent être prises. Ces choix structurent l'ensemble de votre organisation DevOps et ont un impact à long terme sur tous vos projets.

## 🗺️ Votre Parcours DevOps

```
┌─────────────────────────────────────────────────────────────┐
│  VOUS ÊTES ICI : Premier Jour, Organisation Nouvelle        │
│  📍 PREREQUISITES.md - Décisions fondamentales               │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  Décisions Organisationnelles (Une seule fois)               │
│  • Git Provider (GitHub, GitLab, etc.)                       │
│  • Plateforme CI/CD (GitHub Actions, GitLab CI, etc.)       │
│  • Infrastructure (AWS, Azure, GCP, on-premise)              │
│  • Agents CI/CD (SaaS vs self-hosted)                        │
│  • Secret Management (Vault, cloud native, etc.)             │
│  • Monitoring (Prometheus, Datadog, etc.)                    │
│  • Conventions de nommage                                     │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  Configuration de Base (CHECKLIST.md - Phase 0)             │
│  • Créer organisation sur Git provider                       │
│  • Configurer IAM et permissions                             │
│  • Activer MFA pour tous                                     │
│  • Setup compte cloud et billing                             │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│  ✅ Prêt pour Vos Projets !                                  │
│  → CHECKLIST.md (Phases 1-12) pour chaque nouveau projet    │
│  → GUIDE/TOOLS.md pour approfondir les choix                │
│  → GUIDE/CICD.md, ARCHITECTURE.md, etc. pour l'implémentation│
└─────────────────────────────────────────────────────────────┘
```

## Table des Matières

1. [Philosophie et Culture](#philosophie-et-culture)
2. [Choix du Git Provider](#choix-du-git-provider)
3. [Choix de l'Outil de Gestion de Projet](#choix-de-loutil-de-gestion-de-projet)
4. [Choix de l'Outil de Pipeline CI/CD](#choix-de-loutil-de-pipeline-cicd)
5. [Infrastructure des Agents CI/CD](#infrastructure-des-agents-cicd)
6. [Choix du Cloud Provider ou Infrastructure](#choix-du-cloud-provider-ou-infrastructure)
7. [Gestion des Secrets et Credentials](#gestion-des-secrets-et-credentials)
8. [Stratégie de Sécurité Organisationnelle](#stratégie-de-sécurité-organisationnelle)
9. [Observabilité et Monitoring](#observabilité-et-monitoring)
10. [Structure Organisationnelle](#structure-organisationnelle)
11. [Budget et Coûts](#budget-et-coûts)
12. [Checklist de Démarrage](#checklist-de-démarrage)

---

## Philosophie et Culture

### Comprendre DevOps

Avant tout choix technique, il est essentiel de comprendre la philosophie DevOps :

**Principes Fondamentaux :**
- **Culture de collaboration** : Briser les silos entre Dev et Ops
- **Automatisation** : Tout ce qui peut être automatisé doit l'être
- **Mesure** : Ce qui n'est pas mesuré ne peut être amélioré
- **Partage** : Knowledge sharing, blameless post-mortems
- **Amélioration continue** : Itération et feedback loops

**Mindset à adopter :**
- Infrastructure as Code (IaC)
- "You build it, you run it"
- Fail fast, learn faster
- Pets vs Cattle (voir [PET_VS_CATTLE.md](../PET_VS_CATTLE.md))
- Everything as Code (Documentation, Configuration, Policy)

### Définir Votre Approche

**Questions à se poser :**
1. **Niveau de maturité cible** : Où voulez-vous être dans 6 mois, 1 an, 3 ans ?
2. **Taille de l'équipe** : Combien de développeurs ? Combien d'ops ? Équipe mixte ?
3. **Type de projets** : Web apps, microservices, mobile, IoT, ML/AI ?
4. **Contraintes** : Régulation, sécurité, compliance (RGPD, HIPAA, SOC2) ?
5. **Budget** : Quel investissement initial et récurrent est possible ?

---

## Choix du Git Provider

Le Git provider est le cœur de votre infrastructure DevOps. C'est souvent le premier choix à faire.

### Options Principales

| Provider | Type | Avantages | Inconvénients | Idéal pour |
|----------|------|-----------|---------------|------------|
| **GitHub** | SaaS / Self-hosted | • Écosystème le plus riche<br>• GitHub Actions intégré<br>• Copilot AI<br>• Gratuit pour open source | • Coût pour grandes équipes<br>• Moins de features que GitLab | Startups, Open Source, Entreprises moyennes |
| **GitLab** | SaaS / Self-hosted | • Suite complète (CI/CD, registry, etc.)<br>• Excellent self-hosted<br>• Gratuit très généreux | • UI parfois complexe<br>• Moins d'intégrations tierces | Entreprises, DevOps matures, Self-hosted |
| **Bitbucket** | SaaS / Self-hosted | • Intégration Atlassian (Jira)<br>• Pipelines intégrés<br>• Bon pour petites équipes | • Moins de features<br>• Écosystème plus limité | Équipes Atlassian, PME |
| **Azure DevOps** | SaaS | • Intégration Microsoft<br>• Suite complète<br>• Gratuit pour <5 users | • Lock-in Microsoft<br>• UI datée | Shops Microsoft, .NET |
| **Gitea** | Self-hosted | • Léger et rapide<br>• Open source<br>• Facile à déployer | • Moins de features<br>• Communauté plus petite | Self-hosted budget, Homelab |

### Critères de Décision

**1. SaaS vs Self-Hosted ?**

**SaaS (GitHub.com, GitLab.com, etc.) :**
- ✅ Pas de maintenance infrastructure
- ✅ Disponibilité et scalabilité garanties
- ✅ Démarrage immédiat
- ❌ Coûts récurrents par utilisateur
- ❌ Moins de contrôle
- ❌ Dépendance à Internet

**Self-Hosted (GitLab self-hosted, Gitea, etc.) :**
- ✅ Contrôle total des données
- ✅ Pas de limite d'utilisateurs
- ✅ Personnalisation complète
- ❌ Maintenance infrastructure
- ❌ Coûts d'infrastructure
- ❌ Responsabilité sécurité/backups

**2. Fonctionnalités Requises**

Checklist des features importantes :
- [ ] CI/CD intégré natif
- [ ] Container registry intégré
- [ ] Gestion des packages (npm, Maven, etc.)
- [ ] Code review avancé
- [ ] Gestion des issues/boards
- [ ] Wiki / Documentation
- [ ] API complète
- [ ] SSO / SAML
- [ ] Audit logs
- [ ] Branch protection avancée

**3. Intégrations**

Vérifiez la compatibilité avec :
- Votre IDE (VS Code, JetBrains)
- Vos outils de monitoring (Datadog, Prometheus)
- Vos outils de sécurité (Snyk, SonarQube)
- Votre communication (Slack, Teams)

### Recommandations

**Pour démarrer (< 10 personnes) :**
```
GitHub (gratuit) ou GitLab SaaS (gratuit)
→ Simplicité, pas d'infrastructure à gérer
```

**Pour une entreprise (10-100 personnes) :**
```
GitHub Team/Enterprise ou GitLab Premium
→ Features avancées, support
```

**Pour grandes organisations (100+ personnes) :**
```
GitLab self-hosted ou GitHub Enterprise Server
→ Contrôle, compliance, coûts prévisibles
```

**Pour projets open source :**
```
GitHub (gratuit et illimité)
→ Visibilité, communauté
```

---

## Choix de l'Outil de Gestion de Projet

### Options Principales

| Outil | Type | Forces | Faiblesses | Tarif |
|-------|------|--------|-----------|-------|
| **Jira** | SaaS/Self-hosted | Très complet, Agile, Customizable | Complexe, Lourd | 💰💰💰 |
| **GitHub Projects** | Intégré GitHub | Simple, Natif Git, Gratuit | Basique | Gratuit |
| **GitLab Issues/Boards** | Intégré GitLab | Natif Git, Gratuit | Moins features | Gratuit |
| **Linear** | SaaS | Moderne, Rapide, UX excellente | Récent | 💰💰 |
| **Asana** | SaaS | Facile, Visuel, Polyvalent | Moins tech-oriented | 💰 |
| **Trello** | SaaS | Très simple, Kanban | Trop simple pour gros projets | 💰 |
| **Azure Boards** | SaaS | Intégration Microsoft | Lock-in Azure | 💰💰 |
| **ClickUp** | SaaS | Très flexible, Tout-en-un | Peut être overwhelming | 💰 |

### Critères de Décision

**1. Méthodologie**
- **Scrum strict** → Jira, Azure Boards
- **Kanban** → Trello, GitHub Projects, Linear
- **Flexible** → ClickUp, Asana
- **Lean/Simple** → GitHub/GitLab intégré

**2. Intégration avec Git Provider**
- Même plateforme = moins de friction
- GitHub Projects ↔ GitHub
- GitLab Boards ↔ GitLab
- Jira → intégrations disponibles partout

**3. Complexité vs Features**

```
Simple (démarrage rapide)     Complexe (features avancées)
Trello → GitHub Projects → Linear → Jira → Azure Boards
```

### Recommandations

**Pour démarrer :**
```
Utilisez l'outil natif de votre Git provider
GitHub Projects ou GitLab Issues
→ Une seule plateforme, courbe d'apprentissage minimale
```

**Pour croissance :**
```
Linear ou Jira
→ Plus de structure, reporting, scalabilité
```

**Pour entreprises Atlassian :**
```
Jira (suite Atlassian complète)
→ Confluence, Bitbucket, etc.
```

---

## Choix de l'Outil de Pipeline CI/CD

### Options Principales

| Outil | Type | Forces | Faiblesses | Coût |
|-------|------|--------|-----------|------|
| **GitHub Actions** | Intégré GitHub | Native, YAML simple, Marketplace | Limité hors GitHub | Gratuit puis pay-per-use |
| **GitLab CI** | Intégré GitLab | Très puissant, Auto DevOps | Configuration complexe | Gratuit puis tiers |
| **Jenkins** | Self-hosted | Très flexible, Plugins | Maintenance lourde, UI datée | Gratuit (infra à payer) |
| **CircleCI** | SaaS | Rapide, Config simple | Coût, Moins flexible | Pay-per-use |
| **Travis CI** | SaaS | Simple, Open source friendly | Moins de features | Gratuit OSS, paid privé |
| **Azure Pipelines** | SaaS | Intégration Microsoft | Lock-in Azure | Gratuit puis pay-per-use |
| **ArgoCD/Flux** | Self-hosted | GitOps natif, Kubernetes | Spécifique K8s | Gratuit (infra à payer) |

### Critères de Décision

**1. Intégration avec Git Provider**
```
✅ Recommandé : Utilisez le CI/CD de votre Git provider
   - GitHub → GitHub Actions
   - GitLab → GitLab CI
   - Azure DevOps → Azure Pipelines

Avantages :
   - Configuration centralisée
   - Pas de setup supplémentaire
   - Permissions unifiées
   - Meilleure intégration
```

**2. Flexibilité vs Simplicité**

| Besoin | Outil Recommandé |
|--------|------------------|
| Démarrage rapide | GitHub Actions, GitLab CI |
| Maximum de contrôle | Jenkins |
| Kubernetes/GitOps | ArgoCD, Flux CD |
| Multi-cloud | Jenkins, Terraform Cloud |

**3. Self-hosted vs SaaS**

**SaaS CI/CD :**
- ✅ Pas de runners à gérer (ou optionnel)
- ✅ Scalabilité automatique
- ✅ Démarrage immédiat
- ❌ Coûts variables avec usage
- ❌ Minutes limitées (plans gratuits)

**Self-hosted Runners :**
- ✅ Contrôle total
- ✅ Coûts fixes prévisibles
- ✅ Pas de limite de minutes
- ✅ Accès réseau privé
- ❌ Infrastructure à maintenir

### Recommandations

**Pour la plupart des cas :**
```yaml
# GitHub Actions ou GitLab CI
# Exemple: .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm ci
      - run: npm test
```

**Pour grandes entreprises :**
```
GitLab CI self-hosted
+ Runners autoscaling (Kubernetes)
→ Contrôle, sécurité, coûts maîtrisés
```

**Pour Kubernetes :**
```
ArgoCD (GitOps)
→ Déploiements déclaratifs, audit trail
```

---

## Infrastructure des Agents CI/CD

Une décision critique souvent négligée : **où et comment vos pipelines CI/CD s'exécutent**.

### SaaS vs Auto-hébergé

| Critère | SaaS Runners | Self-Hosted Runners |
|---------|--------------|---------------------|
| **Setup** | Immédiat | Configuration requise |
| **Maintenance** | Zéro | Vous gérez |
| **Coût** | Pay-per-minute | Infrastructure fixe |
| **Performance** | Standard | Optimisable |
| **Sécurité** | Réseau public | Réseau privé possible |
| **Accès ressources** | Limité | Accès interne |
| **Scalabilité** | Automatique | À configurer |

### VM vs Containers

**Virtual Machines (VM) :**
```
Exemples: EC2, Azure VMs, GCE
✅ Isolation complète
✅ Supporte Docker-in-Docker
✅ Traditionnel, bien compris
❌ Plus lent à démarrer
❌ Plus coûteux en ressources
```

**Containers :**
```
Exemples: Kubernetes pods, Docker
✅ Démarrage rapide
✅ Économique en ressources
✅ Scalabilité facile
❌ Isolation moins forte
❌ Complexité si Docker-in-Docker
```

### Orchestrateur vs Virtualiseur

**Orchestrateur (Kubernetes, ECS, Nomad) :**
```yaml
# Exemple: GitLab Runner sur Kubernetes
[[runners]]
  [runners.kubernetes]
    namespace = "gitlab-runners"
    cpu_request = "1"
    memory_request = "1Gi"
```
- ✅ Autoscaling natif
- ✅ Haute disponibilité
- ✅ Gestion déclarative
- ❌ Complexité initiale
- ❌ Overhead

**Virtualiseur (VMware, Hyper-V, Proxmox) :**
```
Runners sur VMs traditionnelles
✅ Simple à comprendre
✅ Isolation maximale
❌ Moins flexible
❌ Scalabilité manuelle
```

### Stratégies Recommandées

#### Startup / Petite Équipe (< 10 devs)
```
SaaS Runners (GitHub Actions hosted, GitLab.com shared)
→ Pas d'infrastructure à gérer
→ Gratuit ou très peu coûteux initialement
```

#### Entreprise Moyenne (10-50 devs)
```
Hybride:
- SaaS runners pour builds standards
- Self-hosted runners pour :
  • Builds nécessitant accès réseau interne
  • Builds lourds (économies)
  • Compliance/sécurité
```

#### Grande Entreprise (50+ devs)
```
Self-hosted sur Kubernetes:
- Runners containerisés
- Autoscaling (Karpenter, Cluster Autoscaler)
- Multiple pools (build, test, deploy)
→ Coûts maîtrisés, performance, sécurité
```

### Configuration Détaillée

**GitHub Actions - Self-hosted Runner :**
```bash
# Installation sur VM/serveur
./config.sh --url https://github.com/org/repo --token TOKEN
./run.sh

# Ou sur Kubernetes avec actions-runner-controller
helm install arc \
  --namespace actions-runner-system \
  oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
```

**GitLab Runner - Kubernetes Autoscaling :**
```toml
[[runners]]
  name = "k8s-runner"
  url = "https://gitlab.com"
  executor = "kubernetes"
  [runners.kubernetes]
    namespace = "gitlab-runner"
    poll_timeout = 180
    cpu_request = "500m"
    memory_request = "512Mi"
  [[runners.kubernetes.volumes.empty_dir]]
    name = "docker-certs"
    mount_path = "/certs/client"
```

---

## Choix du Cloud Provider ou Infrastructure

### Cloud Public vs Private vs Hybrid vs On-Premise

| Type | Description | Avantages | Inconvénients |
|------|-------------|-----------|---------------|
| **Cloud Public** | AWS, Azure, GCP | • Scalabilité infinie<br>• Pay-as-you-go<br>• Services managés | • Coûts variables<br>• Vendor lock-in<br>• Complexité |
| **Cloud Private** | OpenStack, VMware Cloud | • Contrôle total<br>• Compliance | • Coûts fixes élevés<br>• Maintenance |
| **Hybrid** | Mix public/private | • Flexibilité<br>• Migration progressive | • Complexité réseau<br>• Gestion double |
| **On-Premise** | Vos datacenters | • Contrôle max<br>• Coûts prévisibles | • CapEx important<br>• Maintenance lourde |

### Principaux Cloud Providers

#### AWS (Amazon Web Services)
```
✅ Leader du marché, le plus mature
✅ Plus large choix de services
✅ Excellent pour startups (credits)
✅ Forte communauté
❌ Complexité (trop de choix)
❌ Coûts peuvent exploser
❌ UI parfois datée

Idéal pour: Startups tech, scale-ups, entreprises innovantes
```

#### Azure (Microsoft)
```
✅ Intégration Microsoft (.NET, AD, Office)
✅ Hybrid cloud fort (Azure Arc)
✅ Bon pour entreprises existantes Microsoft
❌ Moins de services que AWS
❌ Documentation parfois confuse

Idéal pour: Entreprises Microsoft, .NET shops, hybrid cloud
```

#### GCP (Google Cloud Platform)
```
✅ Excellent pour data/ML (BigQuery, Vertex AI)
✅ Kubernetes natif (GKE = référence)
✅ Réseau mondial performant
✅ Tarification plus simple
❌ Moins de services que AWS/Azure
❌ Plus petite part de marché

Idéal pour: Data engineering, ML/AI, Kubernetes
```

#### Alternatives

**DigitalOcean :**
- ✅ Très simple, excellente DX
- ✅ Tarifs prévisibles
- ❌ Moins de services enterprise
- **Idéal pour :** MVPs, side projects, simplicité

**OVHcloud :**
- ✅ Européen (souveraineté)
- ✅ Bon rapport qualité/prix
- ❌ Moins de services managés
- **Idéal pour :** Europe, budgets serrés

**Hetzner :**
- ✅ Très bon marché
- ✅ Performant
- ❌ Features limitées
- **Idéal pour :** Budgets très serrés

### Stratégie Multi-Cloud ?

**⚠️ Attention : Multi-cloud ≠ Bonne idée par défaut**

**Quand faire du multi-cloud :**
- Résilience critique (finance, santé)
- Éviter vendor lock-in absolu
- Besoins spécifiques par cloud (ML sur GCP, .NET sur Azure)

**Pourquoi l'éviter souvent :**
- ❌ Complexité × nombre de clouds
- ❌ Coûts de gestion multipliés
- ❌ Expertise requise sur chaque plateforme
- ❌ Transfert de données coûteux

**Approche recommandée :**
```
Mono-cloud + portable architecture
→ Choisir un cloud principal
→ Utiliser IaC portable (Terraform)
→ Containeriser (Kubernetes portable)
→ Abstraire services (S3 API → compatible avec MinIO, R2, etc.)
```

### Décision Framework

**Questions clés :**

1. **Quelle expertise avez-vous ?**
   - Équipe AWS → AWS
   - Équipe Microsoft → Azure
   - Équipe Google/Data → GCP
   - Pas d'expertise → Simplicité (DigitalOcean, AWS)

2. **Quel budget ?**
   - Serré → Hetzner, DigitalOcean
   - Moyen → AWS, Azure, GCP (avec optimisation)
   - Élevé → N'importe lequel

3. **Quels services critiques ?**
   - ML/Data → GCP
   - .NET → Azure
   - Startup → AWS (ecosystem)
   - Simplicité → DigitalOcean

4. **Contraintes réglementaires ?**
   - Europe souveraineté → OVH, Scaleway
   - Standard → AWS, Azure, GCP
   - Compliance stricte → Private cloud, on-premise

### Recommandation Générale

**Pour 80% des cas :**
```
AWS ou Azure (selon expertise)
+ Kubernetes (EKS/AKS) pour portabilité
+ Terraform pour IaC
→ Bon équilibre features/maturité/écosystème
```

---

## Gestion des Secrets et Credentials

### Pourquoi c'est critique dès le jour 1

```
❌ NE JAMAIS :
- Committer des secrets dans Git
- Partager des credentials par Slack/email
- Hardcoder des mots de passe
- Utiliser le même secret partout

✅ TOUJOURS :
- Utiliser un secret manager
- Rotation automatique
- Principe du moindre privilège
- Audit des accès
```

### Options de Secret Management

| Solution | Type | Forces | Faiblesses | Coût |
|----------|------|--------|-----------|------|
| **HashiCorp Vault** | Self-hosted/SaaS | Standard industrie, très flexible | Configuration complexe | Gratuit/💰💰💰 |
| **AWS Secrets Manager** | Cloud | Intégré AWS, rotation auto | Lock-in AWS | 💰 |
| **Azure Key Vault** | Cloud | Intégré Azure, HSM | Lock-in Azure | 💰 |
| **GCP Secret Manager** | Cloud | Intégré GCP, simple | Lock-in GCP | 💰 |
| **1Password** | SaaS | Excellent UX, CLI, Git | Moins technique | 💰 |
| **Doppler** | SaaS | Simple, moderne, CI/CD | Startup (risque) | 💰💰 |
| **GitHub Secrets** | Intégré GitHub | Gratuit, simple | Basique, GitHub only | Gratuit |
| **GitLab CI Variables** | Intégré GitLab | Gratuit, simple | Basique, GitLab only | Gratuit |

### Recommandations par Cas

**Démarrage (MVP, < 5 personnes) :**
```
GitHub/GitLab Secrets pour CI/CD
+ 1Password pour l'équipe
→ Simple, pas d'infrastructure
```

**Croissance (5-20 personnes) :**
```
Doppler ou Cloud Secret Manager (AWS/Azure/GCP)
→ Centralisation, audit, UI moderne
```

**Entreprise (20+ personnes) :**
```
HashiCorp Vault (self-hosted ou Cloud)
→ Compliance, rotation, fine-grained access
```

### Architecture de Base

```
Développeurs
    ↓
Secret Manager (Vault, AWS SM, etc.)
    ↓
├─→ CI/CD Pipelines (via injection)
├─→ Applications (via API/SDK)
└─→ Infrastructure (Terraform, Ansible)

Principes:
- Jamais de secrets en clair
- Injection à runtime
- Rotation régulière
- Audit de tous les accès
```

---

## Stratégie de Sécurité Organisationnelle

### Security from Day 1

La sécurité doit être intégrée dès le départ, pas ajoutée après coup.

### Fondations à Mettre en Place

**1. Identity & Access Management (IAM)**

```
Principes:
- Principe du moindre privilège
- MFA obligatoire pour tous
- Revue régulière des accès
- Offboarding automatique

Outils:
- SSO (Okta, Auth0, Azure AD)
- RBAC (Role-Based Access Control)
- Just-in-Time Access
```

**2. Shift-Left Security**

```
Intégrer la sécurité le plus tôt possible :

Code → SAST (SonarQube, Semgrep)
Dependencies → SCA (Snyk, Dependabot)
Containers → Trivy, Grype
IaC → Checkov, tfsec
Runtime → DAST, pen testing
```

**3. Compliance Dès le Début**

Si vous avez des contraintes réglementaires :
- RGPD (EU)
- HIPAA (Santé US)
- SOC 2 (Trust)
- PCI DSS (Paiements)

→ Intégrez les requirements dès la conception

**4. Zero Trust Network**

```
Principe: "Never trust, always verify"

- Pas de réseau "interne" de confiance
- Authentification sur chaque requête
- Encryption partout (TLS, at-rest)
- Micro-segmentation
```

### Security Checklist Organisationnelle

- [ ] MFA activé pour tous les services critiques
- [ ] SSO configuré (ou planifié)
- [ ] Secret manager en place
- [ ] Scanning de vulnérabilités automatique
- [ ] Politique de mots de passe forte
- [ ] Backups chiffrés et testés
- [ ] Plan de réponse aux incidents
- [ ] Formation sécurité de l'équipe
- [ ] Revue de code obligatoire
- [ ] Principle of least privilege appliqué

Plus de détails dans [GUIDE/SECURITY.md](SECURITY.md).

---

## Observabilité et Monitoring

### Les Trois Piliers

Dès le début, planifiez votre stratégie d'observabilité :

```
1. Metrics (Prometheus, Datadog)
   → Performance, santé système

2. Logs (ELK, Loki, CloudWatch)
   → Debugging, audit trail

3. Traces (Jaeger, Tempo, Datadog APM)
   → Distributed tracing, latence
```

### Options par Budget

**Gratuit / Très Petit Budget :**
```
Prometheus + Grafana + Loki
→ Open source, self-hosted
⚠️ Vous gérez l'infrastructure
```

**Budget Moyen :**
```
Grafana Cloud ou Cloud native (CloudWatch, Azure Monitor)
→ Managed, facile
```

**Budget Entreprise :**
```
Datadog, New Relic, Dynatrace
→ All-in-one, excellent support, cher
```

### Recommandation

**Pour démarrer :**
```
Utilisez le monitoring natif de votre cloud
AWS CloudWatch, Azure Monitor, GCP Operations
+ Grafana pour dashboards
→ Coût initial bas, facile à setup
```

Plus de détails dans [GUIDE/MONITORING.md](MONITORING.md).

---

## Structure Organisationnelle

### Modèle d'Équipe

**Petite Équipe (< 10 personnes) :**
```
Full-stack DevOps
- Chacun fait un peu de tout
- Pas de silos
- Pair programming pour knowledge sharing
```

**Équipe Moyenne (10-50 personnes) :**
```
Équipes produit + Platform team
- Équipes produit: feature teams
- Platform team: infrastructure, outils, CI/CD
- Platform-as-a-Product mindset
```

**Grande Organisation (50+ personnes) :**
```
Modèle SRE (Site Reliability Engineering)
- SRE teams: fiabilité, performance, ops
- Dev teams: features
- Shared responsibility
- Error budgets
```

### Repositories et Organisation

**Mono-repo vs Multi-repo ?**

**Mono-repo :**
```
Un seul repository pour tout
✅ Refactoring facile
✅ Atomic changes
✅ Tooling unifié
❌ Scaling challenges
❌ CI/CD plus complexe

Idéal pour: Startups, équipes < 50
Outils: Nx, Turborepo, Bazel
```

**Multi-repo :**
```
Un repository par service/composant
✅ Isolation
✅ Ownership clair
✅ CI/CD indépendant
❌ Code sharing difficile
❌ Refactoring cross-repo complexe

Idéal pour: Microservices, grandes orgs
```

**Poly-repo (Hybride) :**
```
Groupes logiques de repos
✅ Équilibre
❌ Complexité de décision

Idéal pour: Cas général
```

### Naming Conventions

Définissez dès le début :
```
Repositories:
  - <org>/<product>-<component>
  - Exemple: acme/shop-api, acme/shop-web

Branches:
  - feature/<ticket>-<description>
  - fix/<ticket>-<description>
  - release/v<version>

Environnements:
  - dev, staging, prod
  (ou development, integration, production)

Tags:
  - v<semver> (v1.2.3)
  - <env>-<timestamp> (prod-20260201)
```

---

## Budget et Coûts

### Estimation Initiale

**Très Petit Budget (< 100€/mois) :**
```
- GitHub Free
- GitHub Actions (minutes gratuites)
- Hetzner VPS (5-20€/mois)
- Maximum d'outils open source gratuits
→ Viable pour side projects, MVP
```

**Petit Budget (100-500€/mois) :**
```
- GitHub Team ou GitLab Premium
- DigitalOcean (50-200€/mois)
- Monitoring basique (Grafana Cloud gratuit)
- Quelques outils SaaS essentiels
→ Viable pour petites startups
```

**Budget Moyen (500-2000€/mois) :**
```
- GitHub Enterprise ou GitLab Ultimate
- AWS/Azure/GCP (200-1000€/mois)
- CI/CD runners dédiés
- Monitoring complet (Datadog, etc.)
- Secret management (Vault, Doppler)
→ Startup en croissance, PME
```

**Budget Entreprise (2000€+/mois) :**
```
- Tout ce qui précède +
- Support premium
- Multi-région
- DR/HA
- Compliance tools
→ Entreprise établie
```

### FinOps : Optimisation Continue

Principes à intégrer dès le début :
- **Tagging rigoureux** : Tous les resources taggés (projet, env, owner)
- **Right-sizing** : Pas de over-provisioning
- **Autoscaling** : Scale down la nuit/weekend
- **Reserved instances** : Commitment pour coûts récurrents
- **Monitoring des coûts** : Alertes sur dépassement

Outils :
- Cloud native : AWS Cost Explorer, Azure Cost Management
- Tiers : CloudHealth, Kubecost (Kubernetes)

---

## Checklist de Démarrage

Utilisez cette checklist pour vos toutes premières décisions :

### Jour 1 : Décisions Stratégiques

- [ ] **Philosophie** : Équipe alignée sur principes DevOps
- [ ] **Cloud Provider** : AWS/Azure/GCP/Autre choisi
- [ ] **Git Provider** : GitHub/GitLab/Bitbucket choisi
- [ ] **Budget** : Enveloppe mensuelle définie

### Semaine 1 : Fondations

- [ ] **Git Provider** : Organisation créée, équipe invitée
- [ ] **SSO** : MFA activé pour tous (minimum)
- [ ] **Secret Manager** : Outil choisi et configuré
- [ ] **Naming conventions** : Documentées et partagées
- [ ] **CI/CD Platform** : Choix fait (natif Git provider recommandé)

### Semaine 2-3 : Infrastructure

- [ ] **Cloud** : Compte créé, billing configuré
- [ ] **IAM** : Roles et permissions définis
- [ ] **Networking** : VPC/réseaux de base créés
- [ ] **CI/CD Runners** : Stratégie choisie (SaaS vs self-hosted)
- [ ] **Monitoring** : Outil choisi, compte créé

### Semaine 4 : Processus

- [ ] **Gestion de projet** : Outil choisi et configuré
- [ ] **Documentation** : Wiki/Confluence initialisé
- [ ] **Runbooks** : Template créé
- [ ] **Incident response** : Process défini
- [ ] **On-call** : Rotation planifiée (si applicable)

### Mois 2 : Sécurité et Compliance

- [ ] **SAST/SCA** : Outils intégrés au CI
- [ ] **Vulnerability scanning** : Automatisé
- [ ] **Compliance** : Requirements mappés
- [ ] **Backups** : Stratégie définie et testée
- [ ] **DR Plan** : Document initial créé

---

## Prochaines Étapes

Une fois ces prérequis en place, vous êtes prêt à :

1. **Créer votre premier projet** → Suivez [CHECKLIST.md](../CHECKLIST.md)
2. **Choisir votre stack technique** → Voir [GUIDE/TOOLS.md](TOOLS.md)
3. **Configurer votre architecture** → Voir [GUIDE/ARCHITECTURE.md](ARCHITECTURE.md)
4. **Mettre en place CI/CD** → Voir [GUIDE/CICD.md](CICD.md)

---

## Résumé : Décisions Minimales Avant de Commencer

**Les 5 décisions absolument obligatoires :**

1. **Git Provider** (GitHub/GitLab/autre)
2. **CI/CD Platform** (idéalement le natif du Git provider)
3. **Cloud/Infrastructure** (AWS/Azure/GCP/on-premise)
4. **Secret Management** (au minimum GitHub/GitLab Secrets)
5. **Monitoring basique** (au minimum cloud native)

**Avec juste ça, vous pouvez démarrer votre premier projet.**

Le reste peut venir progressivement, mais ces fondations sont critiques dès le jour 1.

---

**Document maintenu par:** Votre équipe DevOps
**Dernière révision:** Février 2026
**Prochaine révision:** Tous les 6 mois ou après changements majeurs
