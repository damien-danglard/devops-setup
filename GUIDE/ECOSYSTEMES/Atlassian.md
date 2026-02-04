# Écosystème Atlassian

## Vue d'ensemble

Atlassian est un éditeur de logiciels australien fondé en 2002, spécialisé dans les outils de collaboration, gestion de projet et développement logiciel. L'entreprise propose un écosystème complet et intégré d'outils pour les équipes de développement et IT, avec une forte adoption dans les grandes entreprises.

## Outils de l'écosystème

### Outils principaux

#### Jira
- **Description**: Plateforme de gestion de projet et issue tracking
- **Variantes**:
  - **Jira Software**: Pour équipes agiles (Scrum, Kanban)
  - **Jira Service Management**: ITSM et service desk
  - **Jira Work Management**: Gestion projet business (non-tech)
  - **Jira Align**: Portfolio et enterprise agile planning
- **Fonctionnalités**: Issues, sprints, boards, workflows, automation, roadmaps, reporting
- **Type**: SaaS (Cloud) ou self-hosted (Data Center)

#### Confluence
- **Description**: Wiki et plateforme de documentation collaborative
- **Fonctionnalités**:
  - Pages et espaces
  - Templates
  - Collaboration temps réel
  - Intégration Jira
  - Knowledge base
  - Meeting notes
- **Type**: SaaS (Cloud) ou self-hosted (Data Center)

#### Bitbucket
- **Description**: Plateforme Git repository hosting et CI/CD
- **Fonctionnalités**:
  - Git repositories
  - Pull requests et code review
  - Bitbucket Pipelines (CI/CD)
  - Branch permissions
  - Merge checks
- **Type**: SaaS (Cloud) ou self-hosted (Data Center)

#### Trello
- **Description**: Outil de gestion visuelle type Kanban
- **Fonctionnalités**: Boards, listes, cartes, power-ups, automation
- **Type**: SaaS uniquement (acquis par Atlassian en 2017)
- **Usage**: Projets simples, personal productivity

#### Bamboo
- **Description**: Serveur CI/CD (alternative à Bitbucket Pipelines)
- **Fonctionnalités**:
  - Build automation
  - Deployment automation
  - Intégration Jira et Bitbucket
  - Agents distribués
- **Type**: Self-hosted uniquement (Data Center)
- **Note**: Atlassian recommande Bitbucket Pipelines pour nouveau projets Cloud

### Outils complémentaires

#### Opsgenie
- **Description**: Incident management et on-call scheduling
- **Fonctionnalités**:
  - Alerting et escalation
  - On-call rotations
  - Incident response
  - Intégration monitoring tools
- **Type**: SaaS (Cloud)

#### Statuspage
- **Description**: Communication de statut et incidents
- **Fonctionnalités**: Status pages publiques, notifications incidents
- **Type**: SaaS (Cloud)

#### Compass
- **Description**: Developer experience platform
- **Fonctionnalités**: Service catalog, scorecards, health monitoring
- **Type**: SaaS (Cloud)

#### Atlas
- **Description**: Teamwork directory
- **Fonctionnalités**: Team directory, goals, projects overview
- **Type**: SaaS (Cloud)

#### Halp
- **Description**: Ticketing dans Slack/Teams
- **Fonctionnalités**: Convertir messages en tickets
- **Type**: SaaS (Cloud)

### Intégrations et Marketplace

**Atlassian Marketplace**
- 5,000+ apps et intégrations
- Pour Jira, Confluence, Bitbucket, etc.
- Apps gratuites et payantes

**Apps populaires:**
- Tempo (time tracking)
- ScriptRunner (automation avancée)
- Zephyr (test management)
- BigPicture (portfolio management)
- Draw.io (diagrammes)

## Niveau d'ouverture

### Interconnexion et intégrations

**Points forts:**
- ✅ **APIs REST complètes**: Toutes les fonctionnalités accessibles
- ✅ **Webhooks**: Événements en temps réel
- ✅ **Intégrations natives**: Slack, Microsoft Teams, GitHub, GitLab
- ✅ **Marketplace riche**: 5,000+ apps tierces
- ✅ **Atlassian Connect**: Framework pour apps cloud
- ✅ **Forge**: Nouvelle plateforme apps cloud (serverless)

**Intégrations entre produits Atlassian:**
- ✅ Excellente: Jira ↔ Confluence ↔ Bitbucket
- ✅ Automation cross-product
- ✅ Données partagées (utilisateurs, groupes)
- ✅ SSO unifié

**Limitations:**
- ⚠️ **Cloud vs Data Center**: APIs parfois différentes
- ⚠️ **Vendor lock-in modéré**: Migration sortante complexe
- ⚠️ **Jira workflows**: Configuration propriétaire
- ❌ **Bitbucket Pipelines**: Syntaxe spécifique

### Ouverture du code

**Propriétaire:**
- ❌ Tous les produits Atlassian sont closed-source
- ❌ Pas d'option self-hosted open source
- ❌ Code non auditable

**Mais:**
- ✅ APIs ouvertes et documentées
- ✅ SDKs open source
- ✅ Plugins/apps peuvent être open source

### Formats et standards

- ✅ **Git standard**: Bitbucket utilise Git pur
- ✅ **REST APIs**: Standards HTTP/JSON
- ✅ **Markdown**: Supporté (avec extensions propriétaires)
- ⚠️ **Storage format**: Confluence storage format propriétaire
- ⚠️ **Jira workflows**: Format spécifique

## Avantages

### Points forts

1. **Intégration écosystème**
   - Suite cohérente d'outils
   - Jira + Confluence + Bitbucket = stack complet
   - Données partagées entre produits
   - SSO et gestion utilisateurs centralisée

2. **Jira: Standard de l'industrie**
   - Référence pour gestion de projet agile
   - Workflows hautement personnalisables
   - Reporting et dashboards puissants
   - Adoption massive (facilite recrutement)

3. **Confluence: Documentation collaborative**
   - Templates riches
   - Collaboration temps réel
   - Intégration parfaite avec Jira
   - Knowledge base structurée

4. **Marketplace étendu**
   - 5,000+ apps disponibles
   - Extensions pour tous besoins
   - Apps enterprise de qualité

5. **Options déploiement**
   - Cloud (SaaS) simple
   - Data Center (self-hosted) pour compliance
   - Migration Cloud ↔ Data Center possible

6. **Support enterprise**
   - Support 24/7 disponible
   - SLAs stricts
   - Success programs
   - Training et certification

7. **Scalabilité**
   - Supporte très grandes organisations
   - Data Center: haute disponibilité
   - Performance pour milliers d'utilisateurs

## Inconvénients

### Limitations

1. **Complexité**
   - Jira très complexe à configurer
   - Courbe d'apprentissage élevée
   - Administration demande expertise
   - Peut être over-engineered pour petites équipes

2. **Performance**
   - Jira peut être lent (surtout Cloud)
   - Confluence gourmand en ressources
   - Temps de chargement parfois longs

3. **Coût**
   - Prix élevé à l'échelle
   - Apps Marketplace en supplément
   - Data Center très cher
   - Accumulation rapide si plusieurs produits

4. **Interface utilisateur**
   - Jira: interface datée et chargée
   - Navigation complexe
   - UX moins moderne que concurrents

5. **Bitbucket**
   - Moins populaire que GitHub/GitLab
   - Communauté plus petite
   - Fonctionnalités en retard
   - Bitbucket Pipelines moins mature

6. **Vendor lock-in**
   - Migration sortante très complexe
   - Workflows Jira difficiles à exporter
   - Données Confluence en format propriétaire
   - Dépendance forte si adoption profonde

7. **Cloud limitations**
   - Moins de contrôle que Data Center
   - Personnalisation limitée
   - Certains apps non disponibles en Cloud

## SaaS vs Auto-hébergé

### Atlassian Cloud (SaaS)

**Caractéristiques:**
- Hébergé et géré par Atlassian
- Mises à jour automatiques continues
- Nouvelles fonctionnalités en premier

**Avantages:**
- ✅ Maintenance zéro
- ✅ Scaling automatique
- ✅ Disponibilité garantie (99.9% SLA)
- ✅ Nouvelles features rapidement
- ✅ Setup simple et rapide
- ✅ Coût prévisible

**Inconvénients:**
- ❌ Données chez Atlassian (AWS)
- ❌ Personnalisation limitée
- ❌ Certaines apps Marketplace non disponibles
- ❌ Performance variable
- ❌ Pas de contrôle infrastructure

### Atlassian Data Center (Auto-hébergé)

**Caractéristiques:**
- Installation on-premise ou cloud privé
- Haute disponibilité et clustering
- Contrôle total

**Avantages:**
- ✅ Contrôle total données
- ✅ Compliance facilitée
- ✅ Performance dédiée et optimisable
- ✅ Personnalisation complète
- ✅ Tous les apps Marketplace
- ✅ Haute disponibilité native

**Inconvénients:**
- ❌ Maintenance complexe (DB, clustering, upgrades)
- ❌ Infrastructure coûteuse
- ❌ Expertise DevOps requise
- ❌ Nouvelles fonctionnalités en retard
- ❌ Licence très chère

**Recommandations Data Center:**
- Minimum 500-1000 utilisateurs pour justifier coût
- PostgreSQL ou Oracle en haute disponibilité
- NFS partagé ou object storage
- Load balancer pour clustering
- Monitoring robuste

**Note importante:** Atlassian a arrêté les licences Server (version simple self-hosted) en 2024, ne reste que Data Center pour self-hosting.

## Prix

### Atlassian Cloud (SaaS)

**Jira Software:**
- **Free**: $0 (jusqu'à 10 utilisateurs)
  - Features de base
  - 2 GB storage
  
- **Standard**: $8.15/utilisateur/mois
  - 250 GB storage
  - Support business hours
  
- **Premium**: $16/utilisateur/mois
  - Unlimited storage
  - Support 24/7
  - Advanced features (sandbox, analytics)
  
- **Enterprise**: Sur devis
  - Unlimited instances
  - HIPAA compliance
  - 99.95% SLA
  - Enterprise support

**Confluence:**
- **Free**: $0 (jusqu'à 10 utilisateurs)
- **Standard**: $6.05/utilisateur/mois
- **Premium**: $11.55/utilisateur/mois
- **Enterprise**: Sur devis

**Bitbucket:**
- **Free**: $0 (jusqu'à 5 utilisateurs)
  - 50 minutes CI/CD/mois
- **Standard**: $3/utilisateur/mois
  - 2,500 minutes CI/CD/mois
- **Premium**: $6/utilisateur/mois
  - 3,500 minutes CI/CD/mois

**Bundles (réductions):**
- Combinaison de produits offre réductions
- Exemple: Jira + Confluence + Bitbucket

### Atlassian Data Center (Self-hosted)

**Tarification annuelle:**

**Jira Software Data Center:**
- 500 utilisateurs: $42,000/an
- 2,000 utilisateurs: $80,000/an
- 10,000 utilisateurs: $160,000/an
- 25,000+ utilisateurs: Sur devis

**Confluence Data Center:**
- 500 utilisateurs: $27,000/an
- 2,000 utilisateurs: $54,000/an

**Bitbucket Data Center:**
- 25 utilisateurs: $1,800/an
- 100 utilisateurs: $3,600/an
- 500 utilisateurs: $18,000/an

**Coûts additionnels:**
- Infrastructure (serveurs, stockage, réseau)
- Base de données (PostgreSQL/Oracle)
- Load balancers
- Monitoring
- Backups
- Personnel DevOps

**Estimation coût total (500 utilisateurs):**
- Licences: ~$87,000/an (Jira + Confluence + Bitbucket)
- Infrastructure: $20,000-50,000/an
- Personnel: $100,000+/an
- **Total**: $200,000-250,000/an minimum

## Cas d'usage recommandés

### Idéal pour

1. **Grandes entreprises**
   - Nombreuses équipes
   - Processus complexes
   - Besoins de conformité
   - Support enterprise critique

2. **Équipes déjà sur Atlassian**
   - Migration coûteuse et complexe
   - Investissement formation important
   - Workflows établis

3. **Organisations ITIL/ITSM**
   - Jira Service Management référence
   - Processus structurés
   - Gestion incidents/changements

4. **Projets nécessitant traçabilité**
   - Automotive, aéronautique, pharma
   - Audit trails complets
   - Documentation exhaustive

5. **Équipes hybrides tech/business**
   - Jira Work Management pour business
   - Jira Software pour dev
   - Confluence pour tous

### À éviter si

1. **Petites équipes (< 20 personnes)**
   - Over-engineering
   - Complexité inutile
   - → GitHub/GitLab Projects, Linear, Notion

2. **Startups agiles**
   - Overhead processus
   - Coût élevé
   - → Tools plus simples et modernes

3. **Besoin de simplicité**
   - Jira trop complexe
   - → Linear, Asana, Monday.com

4. **Budget très limité**
   - Coût prohibitif à l'échelle
   - → GitLab (open source), Plane

5. **Équipe open source**
   - Pas de version gratuite pour OSS
   - → GitHub, GitLab

## Migrations et intégrations

### Migration vers Atlassian

**Depuis GitHub:**
- Import Bitbucket: repositories, issues, PRs
- Sync bidirectionnel Jira ↔ GitHub (via app)

**Depuis GitLab:**
- Migration manuelle ou outils tiers
- Jira ↔ GitLab integration disponible

**Depuis Azure DevOps:**
- Outils de migration disponibles
- Migration data complexe

### Migration depuis Atlassian

**Complexité élevée:**
- ❌ Export Jira: format XML/CSV limité
- ❌ Workflows: recréation manuelle nécessaire
- ❌ Confluence: export PDF/HTML/XML (perte structure)
- ❌ Bitbucket: Git facile, Pipelines à convertir
- ❌ Historique et metadata: perte partielle

**Outils tiers:**
- Exporter pour GitHub, GitLab, Azure DevOps
- Souvent payants
- Rarement 100% fidèle

## Comparaison outils Atlassian

| Produit | Usage principal | Cloud | Data Center | Open Source Alternative |
|---------|----------------|-------|-------------|------------------------|
| Jira Software | Agile project management | ✅ | ✅ | Plane, Taiga |
| Confluence | Documentation | ✅ | ✅ | WikiJS, BookStack |
| Bitbucket | Git + CI/CD | ✅ | ✅ | GitLab, Gitea |
| Trello | Kanban simple | ✅ | ❌ | Wekan, Focalboard |
| Bamboo | CI/CD | ❌ | ✅ | Jenkins, GitLab CI |

## Conclusion

Atlassian est le choix approprié pour:
- Les grandes organisations avec processus établis
- Les équipes nécessitant Jira (standard industrie)
- Les besoins enterprise (support, SLA, compliance)
- Les organisations déjà investies dans l'écosystème

**Cependant**, la complexité, le coût et le vendor lock-in sont des inconvénients majeurs. Pour petites équipes et startups, des alternatives plus modernes et simples sont préférables.

Alternative recommandée si recherche simplicité et prix: **Linear** (gestion projet), **GitLab** (Git + CI/CD + DevOps)

## Ressources

- [Documentation Atlassian](https://confluence.atlassian.com)
- [Atlassian Community](https://community.atlassian.com)
- [Atlassian Marketplace](https://marketplace.atlassian.com)
- [Atlassian University](https://university.atlassian.com) - Training et certification
- [Atlassian Blog](https://www.atlassian.com/blog)
- [Atlassian Status](https://status.atlassian.com)
- [Atlassian Trust](https://www.atlassian.com/trust) - Security & Compliance
