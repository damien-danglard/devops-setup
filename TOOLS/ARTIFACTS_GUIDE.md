# Guide des Registres d'Artefacts

Guide détaillé pour la sélection et l'utilisation des registres d'artefacts (artifacts/packages) pour différentes technologies et langages de programmation.

## Table des Matières

- [Introduction](#introduction)
- [Registres de Conteneurs (Docker/OCI)](#registres-de-conteneurs-dockeroci)
- [Registres Universels Multi-Format](#registres-universels-multi-format)
- [Registres Spécifiques par Langage](#registres-spécifiques-par-langage)
- [Registres Cloud Provider](#registres-cloud-provider)
- [Comparaison et Matrice de Décision](#comparaison-et-matrice-de-décision)
- [Best Practices](#best-practices)

## Introduction

Les registres d'artefacts (artifact registries) sont des services de stockage centralisés pour vos packages, images de conteneurs, bibliothèques et autres artefacts de build. Ils jouent un rôle crucial dans votre pipeline CI/CD et la distribution de vos applications.

### Pourquoi un Registre d'Artefacts ?

- **Centralisation**: Un point unique pour tous vos artefacts
- **Versioning**: Gestion des versions et historique complet
- **Sécurité**: Scan de vulnérabilités, contrôle d'accès
- **Performance**: Cache et distribution géographique
- **Traçabilité**: Qui a publié quoi et quand
- **Reproductibilité**: Garantie d'avoir toujours les mêmes versions

### Types d'Artefacts

- **Images de conteneurs**: Docker, OCI
- **Packages de langages**: npm, Maven, PyPI, NuGet, etc.
- **Charts Helm**: Pour Kubernetes
- **Binaires**: Exécutables, archives
- **Fichiers génériques**: Documentation, assets

## Registres de Conteneurs (Docker/OCI)

Les registres de conteneurs stockent les images Docker et OCI (Open Container Initiative) utilisées pour déployer vos applications conteneurisées.

### Docker Hub

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Le registre Docker officiel et le plus populaire au monde, avec des millions d'images publiques.

**Avantages:**

- **Standard de facto**: Presque tous les développeurs le connaissent
- **Bibliothèque publique**: Des millions d'images officielles et communautaires
- **Gratuit pour repos publics**: Images publiques illimitées
- **Intégration facile**: Docker CLI utilise Hub par défaut
- **Automated Builds**: Builds automatiques depuis GitHub/Bitbucket
- **Official Images**: Images vérifiées et maintenues

**Inconvénients:**

- **Rate limiting**: 100 pulls/6h pour anonymes, 200 pulls/6h pour gratuits
- **Fonctionnalités limitées**: Version gratuite basique
- **Pas de multi-format**: Uniquement images Docker
- **Coût pour privé**: Repos privés payants après limite
- **Scan limité**: Scan de sécurité limité en version gratuite

**Tarification:**

- **Free**: 1 repo privé
- **Pro**: $5/mois - Repos privés illimités
- **Team**: $7/user/mois - Collaboration équipe
- **Business**: $21/user/mois - SSO, audit logs

**Cas d'usage:**

- Images Docker publiques
- Petits projets avec peu de repos privés
- Prototypage et développement
- Utilisation d'images officielles

**Exemple d'utilisation:**

```bash
# Push vers Docker Hub
docker tag myapp:1.0 username/myapp:1.0
docker push username/myapp:1.0

# Pull depuis Docker Hub
docker pull nginx:latest
```

### GitHub Container Registry (GHCR)

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(GitHub)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Registre de conteneurs intégré à GitHub, supportant les images Docker et OCI.

**Avantages:**

- **Intégration native GitHub**: Parfaite intégration avec repos et Actions
- **Gratuit pour public**: Illimité pour images publiques
- **Pas de rate limiting**: Pour utilisateurs authentifiés
- **Support OCI**: Images et autres artefacts OCI
- **GitHub Actions**: Excellent pour CI/CD GitHub
- **Permissions granulaires**: Gestion fine des accès
- **Anonymous access**: Support du pull anonyme

**Inconvénients:**

- **Écosystème GitHub**: Limité à GitHub
- **Moins de fonctionnalités**: Comparé à Artifactory ou Harbor
- **Pas de UI riche**: Interface basique
- **Scan de sécurité**: Nécessite GitHub Advanced Security (payant)

**Tarification:**

- **Gratuit**: 500MB stockage, 1GB transfert/mois
- **GitHub Pro**: 2GB stockage, 10GB transfert/mois
- **GitHub Team**: 2GB stockage, 10GB transfert/mois par user
- **GitHub Enterprise**: 50GB stockage, 100GB transfert/mois

**Cas d'usage:**

- Projets hébergés sur GitHub
- CI/CD avec GitHub Actions
- Images Docker publiques alternatives à Docker Hub
- Projets open source

**Exemple d'utilisation:**

```bash
# Login
echo $GITHUB_TOKEN | docker login ghcr.io -u USERNAME --password-stdin

# Tag et push
docker tag myapp:1.0 ghcr.io/username/myapp:1.0
docker push ghcr.io/username/myapp:1.0

# Dans GitHub Actions
- uses: docker/login-action@v2
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```

### GitLab Container Registry

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen) ![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/gitlab/license/gitlab-org%2Fgitlab-foss?label=License%20(CE)) ![License](https://img.shields.io/badge/License%20(EE)-Proprietary-red)

**Description:**

Registre de conteneurs intégré à GitLab, disponible en version cloud et self-hosted.

**Avantages:**

- **Intégré à GitLab**: Un seul outil pour tout
- **Gratuit en self-hosted**: Aucun coût pour version CE
- **CI/CD natif**: Excellente intégration GitLab CI
- **Scan de vulnérabilités**: Intégré (version Premium+)
- **Support multi-format**: Docker, Helm, et autres
- **Cleanup policies**: Nettoyage automatique des anciennes images
- **Tag immutability**: Protection des tags

**Inconvénients:**

- **Performances variables**: Selon configuration self-hosted
- **Fonctionnalités avancées**: Nécessitent version Premium/Ultimate
- **UI moins riche**: Comparé à Harbor ou Artifactory

**Tarification (GitLab.com):**

- **Free**: 5GB stockage
- **Premium**: $19/user/mois - 10GB, scan de sécurité
- **Ultimate**: $99/user/mois - 10GB, fonctionnalités avancées

**Cas d'usage:**

- Projets GitLab
- Teams voulant self-hosted
- Intégration GitLab CI/CD
- Besoin de compliance et contrôle

**Exemple d'utilisation:**

```bash
# Login
docker login registry.gitlab.com

# Tag et push
docker tag myapp:1.0 registry.gitlab.com/username/project/myapp:1.0
docker push registry.gitlab.com/username/project/myapp:1.0

# Dans GitLab CI
build:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG
```

### Azure Container Registry (ACR)

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(Azure)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Registre de conteneurs managé par Microsoft Azure, optimisé pour les workloads Azure.

**Avantages:**

- **Intégration Azure native**: AKS, App Service, Container Instances
- **Geo-replication**: Réplication multi-région automatique
- **Scan de sécurité**: Microsoft Defender pour conteneurs
- **ACR Tasks**: Build d'images dans le cloud
- **Support multi-format**: Docker, Helm, OCI
- **Azure AD integration**: Gestion des identités centralisée
- **Private Link**: Accès via réseau privé Azure
- **Webhooks**: Notifications sur push/pull

**Inconvénients:**

- **Azure uniquement**: Lock-in écosystème Azure
- **Coûts**: Stockage et transfert sortant facturés
- **Complexité**: Configuration réseau peut être complexe

**Tarification:**

- **Basic**: ~$5/mois - 10GB stockage, 10GB transfert
- **Standard**: ~$20/mois - 100GB stockage, 100GB transfert
- **Premium**: ~$500/mois - 500GB, geo-replication, Private Link

**Cas d'usage:**

- Applications Azure (AKS, App Service)
- Besoin de geo-replication
- Intégration Azure AD
- Réseaux privés Azure

**Exemple d'utilisation:**

```bash
# Login avec Azure CLI
az acr login --name myregistry

# Tag et push
docker tag myapp:1.0 myregistry.azurecr.io/myapp:1.0
docker push myregistry.azurecr.io/myapp:1.0

# Build dans ACR
az acr build --registry myregistry --image myapp:1.0 .
```

### Amazon Elastic Container Registry (ECR)

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(AWS)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Registre de conteneurs managé AWS, optimisé pour ECS et EKS.

**Avantages:**

- **Intégration AWS native**: ECS, EKS, Lambda, CodeBuild
- **IAM integration**: Contrôle d'accès AWS IAM
- **Scan de vulnérabilités**: Scan automatique intégré (ECR Enhanced Scanning)
- **Réplication cross-region**: Réplication automatique multi-régions
- **ECR Public**: Registre public gratuit
- **Lifecycle policies**: Nettoyage automatique
- **Encryption**: At-rest et in-transit
- **VPC endpoints**: Accès privé sans internet

**Inconvénients:**

- **AWS uniquement**: Lock-in AWS
- **Coûts**: Stockage et transfert sortant facturés
- **Rate limiting**: 200 GetAuthorizationToken/sec

**Tarification:**

- **Stockage**: $0.10/GB/mois
- **Transfert sortant**: Variable selon région ($0.09/GB vers Internet)
- **Transfert AWS**: Gratuit vers même région
- **ECR Public**: Gratuit

**Cas d'usage:**

- Applications AWS (ECS, EKS)
- Lambda avec conteneurs
- Infrastructure multi-région AWS
- Besoin d'intégration IAM forte

**Exemple d'utilisation:**

```bash
# Login
aws ecr get-login-password --region eu-west-1 | \
  docker login --username AWS --password-stdin \
  123456789.dkr.ecr.eu-west-1.amazonaws.com

# Tag et push
docker tag myapp:1.0 123456789.dkr.ecr.eu-west-1.amazonaws.com/myapp:1.0
docker push 123456789.dkr.ecr.eu-west-1.amazonaws.com/myapp:1.0
```

### Google Artifact Registry

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(GCP)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Registre universel GCP supportant Docker, Maven, npm, Python, et plus. Remplace Google Container Registry.

**Avantages:**

- **Support multi-format**: Docker, Maven, npm, Python, apt, yum, Go, Helm
- **Intégration GCP native**: GKE, Cloud Build, Cloud Run
- **IAM integration**: Gestion fine des permissions
- **Scan de vulnérabilités**: Analyse automatique
- **Multi-région**: Réplication automatique
- **CMEK**: Customer-managed encryption keys
- **VPC Service Controls**: Isolation réseau
- **Artifact Analysis**: Métadonnées et provenance

**Inconvénients:**

- **GCP uniquement**: Lock-in Google Cloud
- **Coûts**: Stockage et transfert sortant facturés
- **Migration**: Depuis Container Registry nécessaire

**Tarification:**

- **Stockage**: $0.10/GB/mois
- **Transfert sortant**: Variable ($0.12/GB vers Internet)
- **Transfert GCP**: Gratuit dans même région
- **Gratuit**: 0.5GB stockage/mois

**Cas d'usage:**

- Applications GCP (GKE, Cloud Run)
- Besoin multi-format (Docker + Maven + npm)
- Infrastructure multi-région GCP
- Projets nécessitant plusieurs types d'artefacts

**Exemple d'utilisation:**

```bash
# Login
gcloud auth configure-docker europe-west1-docker.pkg.dev

# Tag et push Docker
docker tag myapp:1.0 europe-west1-docker.pkg.dev/project-id/repo-name/myapp:1.0
docker push europe-west1-docker.pkg.dev/project-id/repo-name/myapp:1.0

# Upload Maven artifact
mvn deploy
```

### Harbor

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen)  
![License](https://img.shields.io/github/license/goharbor/harbor)

**Description:**

Registre de conteneurs open source (CNCF), riche en fonctionnalités de sécurité et orienté Kubernetes.

**Avantages:**

- **Open source**: CNCF graduated project, aucun vendor lock-in
- **Scan de sécurité**: Trivy et Clair intégrés
- **Signature d'images**: Support Notary et Cosign
- **Réplication**: Multi-registry replication
- **RBAC**: Contrôle d'accès granulaire
- **Quotas**: Par projet et par repo
- **Retention policies**: Nettoyage automatique
- **Proxy cache**: Cache d'autres registries (Docker Hub, etc.)
- **Audit logs**: Logging complet
- **Support multi-format**: Docker, Helm, OCI
- **Vulnerability exemptions**: Gestion des exceptions

**Inconvénients:**

- **Setup et maintenance**: Nécessite infrastructure et expertise
- **Haute disponibilité**: Configuration complexe pour HA
- **Principalement conteneurs**: Moins adapté pour autres formats

**Composants:**

- **Harbor Core**: API et UI
- **Harbor Registry**: Stockage des images
- **PostgreSQL**: Métadonnées
- **Redis**: Cache et job queue
- **Trivy/Clair**: Scan de vulnérabilités
- **Notary**: Signature d'images (optionnel)

**Cas d'usage:**

- Kubernetes on-premise ou cloud
- Besoins élevés de sécurité
- Self-hosted avec contrôle total
- Projets nécessitant compliance stricte
- Teams voulant éviter vendor lock-in

**Installation (Docker Compose):**

```bash
# Télécharger Harbor
wget https://github.com/goharbor/harbor/releases/download/v2.9.0/harbor-offline-installer-v2.9.0.tgz
tar xzvf harbor-offline-installer-v2.9.0.tgz
cd harbor

# Configuration
cp harbor.yml.tmpl harbor.yml
# Éditer harbor.yml (hostname, ports, admin password)

# Installation
sudo ./install.sh --with-trivy --with-chartmuseum
```

**Exemple d'utilisation:**

```bash
# Login
docker login harbor.example.com

# Tag et push
docker tag myapp:1.0 harbor.example.com/myproject/myapp:1.0
docker push harbor.example.com/myproject/myapp:1.0

# Scan manuel
curl -X POST "https://harbor.example.com/api/v2.0/projects/myproject/repositories/myapp/artifacts/1.0/scan"
```

## Registres Universels Multi-Format

Les registres universels supportent plusieurs types d'artefacts (Docker, Maven, npm, etc.) dans une seule plateforme.

### JFrog Artifactory

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen) ![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Solution universelle de gestion d'artefacts supportant tous les formats majeurs. Leader du marché enterprise.

**Avantages:**

- **Support universel**: 30+ formats (Docker, Maven, npm, PyPI, NuGet, Helm, etc.)
- **Très mature**: Plus de 15 ans de développement
- **Enterprise-grade**: HA, disaster recovery, SLA
- **JFrog Xray**: Scan de sécurité avancé et analyse de dépendances
- **Geo-replication**: Synchronisation multi-site
- **Advanced features**: Promotion pipeline, build info, metadata
- **REST API riche**: Automation complète
- **Access Federation**: Gestion des permissions centralisée
- **AQL**: Advanced Query Language pour recherche
- **Build integration**: Jenkins, GitLab, GitHub Actions, etc.

**Inconvénients:**

- **Coûteux**: Licence commerciale, tarification par tier
- **Complexe**: Beaucoup de fonctionnalités = courbe d'apprentissage
- **Overkill pour petits projets**: Trop de features pour besoins simples

**Formats supportés:**

Docker, Helm, Maven, Gradle, npm, PyPI, NuGet, RubyGems, Composer, Go, Conan, Debian, RPM, Vagrant, Terraform, GitLFS, et plus.

**Tarification:**

- **Pro**: $98/mois - 1 instance, support basique
- **Pro X**: $270/mois - HA, advanced features
- **Enterprise**: Sur devis - Multi-site, Xray advanced
- **Self-hosted**: Licence annuelle

**Cas d'usage:**

- Grandes entreprises
- Multi-format nécessaire
- Besoins de compliance stricte
- Infrastructure distribuée géographiquement
- Pipelines CI/CD complexes

**Exemple d'utilisation:**

```bash
# Docker
docker login mycompany.jfrog.io
docker tag myapp:1.0 mycompany.jfrog.io/docker-local/myapp:1.0
docker push mycompany.jfrog.io/docker-local/myapp:1.0

# Maven (pom.xml)
<distributionManagement>
  <repository>
    <id>central</id>
    <name>libs-release</name>
    <url>https://mycompany.jfrog.io/artifactory/libs-release</url>
  </repository>
</distributionManagement>

# npm (.npmrc)
registry=https://mycompany.jfrog.io/artifactory/api/npm/npm-local/
```

### Sonatype Nexus Repository

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen) ![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/github/license/sonatype/nexus-public?label=License%20(OSS)) ![License](https://img.shields.io/badge/License%20(Pro)-Proprietary-red)

**Description:**

Gestionnaire universel d'artefacts avec version open source. Alternative populaire à Artifactory.

**Avantages:**

- **Version open source**: Nexus OSS gratuit (formats limités)
- **Support multi-format**: Docker, Maven, npm, PyPI, NuGet, Helm, etc.
- **Mature et stable**: 15+ ans d'existence
- **Bon rapport qualité/prix**: Moins cher qu'Artifactory
- **Cleanup policies**: Automatisation du nettoyage
- **Staging et promotion**: Workflow de release
- **LDAP/SAML**: Authentification enterprise
- **Sonatype Lifecycle**: Analyse de sécurité (Pro)

**Inconvénients:**

- **Interface moins moderne**: UI datée comparée à Artifactory
- **Performances**: Peut être plus lent sur gros volumes
- **Moins de features**: Comparé à Artifactory Pro/Enterprise
- **Configuration complexe**: Setup initial peut être difficile

**Versions:**

- **Nexus OSS**: Gratuit - Maven, npm, NuGet, PyPI, Docker (basique)
- **Nexus Pro**: $100/user/an - Tous formats, staging, RBAC avancé
- **Nexus Cloud**: SaaS managé

**Formats supportés:**

Maven, Gradle, npm, PyPI, NuGet, Docker, Helm, RubyGems, Composer, Bower, Git LFS, Raw, apt, yum, et plus.

**Cas d'usage:**

- Entreprises avec budget limité
- Self-hosted avec version OSS
- Migration depuis Maven Central proxy
- Équipes Java/Maven (historiquement fort)

**Installation (Docker):**

```bash
# Nexus 3
docker run -d -p 8081:8081 \
  -v nexus-data:/nexus-data \
  --name nexus \
  sonatype/nexus3
```

**Exemple d'utilisation:**

```bash
# Docker
docker login nexus.example.com:8082
docker tag myapp:1.0 nexus.example.com:8082/myapp:1.0
docker push nexus.example.com:8082/myapp:1.0

# Maven (settings.xml)
<servers>
  <server>
    <id>nexus</id>
    <username>admin</username>
    <password>password</password>
  </server>
</servers>

# npm
npm config set registry http://nexus.example.com:8081/repository/npm-group/
```

## Registres Spécifiques par Langage

Registres publics et privés spécialisés pour des écosystèmes spécifiques.

### Maven Central et Alternatives

#### Maven Central

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free-green)

**Description:**

Registre public officiel pour packages Maven/Java.

**Avantages:**

- **Standard Java**: Référence pour l'écosystème Java
- **Gratuit**: Pour artefacts open source
- **Fiable**: Haute disponibilité, CDN global
- **Immutable**: Artefacts ne peuvent être supprimés

**Inconvénients:**

- **Public uniquement**: Pas de repos privés
- **Process de publication**: Validation stricte, temps de latence
- **Pas de gestion**: Aucun contrôle après publication

**Cas d'usage:**

- Publication de bibliothèques Java open source
- Consommation de dépendances publiques

**Alternatives privées:**

- **Artifactory** (repos Maven privés)
- **Nexus** (repos Maven privés)
- **GitHub Packages** (Maven repos privés)
- **GitLab Package Registry** (Maven repos)

### npm et Alternatives

#### npm Registry (npmjs.com)

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free%20(public)-green)

**Description:**

Registre officiel pour packages JavaScript/Node.js.

**Avantages:**

- **Standard JavaScript**: Plus grand registre de packages au monde
- **Gratuit pour public**: Packages publics illimités
- **npm Workspaces**: Support monorepos
- **Scopes**: Packages organisés par scope (@org/package)

**Inconvénients:**

- **Payant pour privé**: Packages privés nécessitent abonnement
- **Sécurité**: Historique de packages malveillants
- **Dépendance externe**: Indisponibilité impacte votre build

**Tarification:**

- **Free**: Packages publics illimités
- **Pro**: $7/mois - Packages privés illimités
- **Teams**: $7/user/mois - Collaboration équipe

**Alternatives privées:**

- **GitHub Packages** (npm registry)
- **GitLab Package Registry** (npm registry)
- **Artifactory** (npm repos)
- **Nexus** (npm repos)
- **Verdaccio** (proxy npm self-hosted gratuit)

#### Verdaccio

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen)  
![License](https://img.shields.io/github/license/verdaccio/verdaccio)

**Description:**

Proxy npm léger et privé, open source.

**Avantages:**

- **Gratuit et open source**: Aucun coût
- **Léger**: Facile à installer et maintenir
- **Proxy npm**: Cache des packages publics
- **Packages privés**: Support complet
- **Plugins**: Extensible

**Cas d'usage:**

- Petites équipes
- Besoin de proxy npm on-premise
- Cache npm pour accélérer les builds

```bash
# Installation
npm install -g verdaccio
verdaccio
```

### PyPI et Alternatives

#### Python Package Index (PyPI)

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free-green)

**Description:**

Registre officiel pour packages Python.

**Avantages:**

- **Standard Python**: Tous les packages Python publics
- **Gratuit**: Pour packages publics
- **pip integration**: Utilisation native avec pip

**Inconvénients:**

- **Public uniquement**: Pas de packages privés
- **Pas de gestion d'entreprise**: Pas de RBAC, quotas, etc.

**Alternatives privées:**

- **Artifactory** (PyPI repos)
- **Nexus** (PyPI repos)
- **GitLab Package Registry** (PyPI repos)
- **AWS CodeArtifact** (PyPI repos)
- **devpi** (PyPI server self-hosted)

#### devpi

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen)  
![License](https://img.shields.io/github/license/devpi/devpi)

**Description:**

Serveur PyPI self-hosted, léger.

**Avantages:**

- **Open source**: Gratuit
- **Proxy PyPI**: Cache des packages publics
- **Test index**: Index de test avant publication
- **Staging**: Workflow de promotion

**Cas d'usage:**

- Packages Python privés
- Cache PyPI on-premise
- Test de packages avant publication PyPI

### NuGet et Alternatives

#### NuGet Gallery

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free-green)

**Description:**

Registre officiel pour packages .NET.

**Avantages:**

- **Standard .NET**: Tous les packages .NET publics
- **Gratuit**: Pour packages publics
- **Visual Studio integration**: Intégration native

**Alternatives privées:**

- **Azure Artifacts** (NuGet feeds)
- **GitHub Packages** (NuGet registry)
- **Artifactory** (NuGet repos)
- **Nexus** (NuGet repos)
- **MyGet** (NuGet hosting privé)

#### Azure Artifacts

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(Azure)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Service Azure pour packages privés (NuGet, npm, Maven, Python).

**Avantages:**

- **Multi-format**: NuGet, npm, Maven, Python, Universal Packages
- **Azure DevOps integration**: Excellente intégration
- **Upstream sources**: Proxy vers registres publics
- **Retention policies**: Nettoyage automatique

**Tarification:**

- **Gratuit**: 2GB stockage
- **Additionnel**: $2/GB/mois

**Cas d'usage:**

- Projets .NET sur Azure DevOps
- Multi-format Microsoft stack

### RubyGems

#### RubyGems.org

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free-green)

**Description:**

Registre officiel pour gems Ruby.

**Avantages:**

- **Standard Ruby**: Tous les gems Ruby publics
- **Gratuit**: Pour gems publics

**Alternatives privées:**

- **Gemfury** (gem hosting privé)
- **Artifactory** (RubyGems repos)
- **Nexus** (RubyGems repos)

### Go Modules

#### Go Module Proxy (proxy.golang.org)

![Hosting](https://img.shields.io/badge/Hosting-SaaS-blue)  
![License](https://img.shields.io/badge/License-Free-green)

**Description:**

Proxy officiel Google pour modules Go.

**Avantages:**

- **Gratuit**: Public modules
- **Immutable**: Garantie d'immuabilité
- **Cache**: Performance globale

**Alternatives privées:**

- **Athens** (Go module proxy self-hosted)
- **Artifactory** (Go repos)
- **Google Artifact Registry** (Go modules)
- **GitLab Package Registry** (Go modules)

#### Athens

![Hosting](https://img.shields.io/badge/Hosting-Self--hosted-brightgreen)  
![License](https://img.shields.io/github/license/gomods/athens)

**Description:**

Proxy Go modules self-hosted, open source.

**Cas d'usage:**

- Modules Go privés
- Cache on-premise
- Air-gapped environments

## Registres Cloud Provider

### AWS CodeArtifact

![Hosting](https://img.shields.io/badge/Hosting-SaaS%20(AWS)-blue)  
![License](https://img.shields.io/badge/License-Proprietary-red)

**Description:**

Service AWS managé pour artefacts multi-format.

**Avantages:**

- **Multi-format**: Maven, Gradle, npm, PyPI, NuGet, Generic
- **IAM integration**: Contrôle d'accès AWS natif
- **Upstream repositories**: Proxy vers registres publics
- **Cross-account**: Partage entre comptes AWS
- **VPC endpoints**: Accès privé

**Tarification:**

- **Stockage**: $0.05/GB/mois
- **Requests**: $0.05/10,000 requests
- **Data transfer out**: Variable

**Cas d'usage:**

- Projets AWS
- Multi-format pour applications AWS
- Besoin d'intégration IAM

**Note**: Pour Azure Artifacts, voir la section NuGet ci-dessus. Pour Google Artifact Registry, voir la section Registres de Conteneurs ci-dessus.

## Comparaison et Matrice de Décision

### Tableau Comparatif par Type d'Artefact

| Registre | Docker | Maven | npm | PyPI | NuGet | Helm | Go | Generic | Self-hosted | SaaS |
|----------|--------|-------|-----|------|-------|------|----|---------|-----------|----|
| **Docker Hub** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **GHCR** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ |
| **GitLab CR** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **ACR** | ✅ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ | ❌ | ✅ |
| **ECR** | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| **Google AR** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ | ❌ | ✅ |
| **Harbor** | ✅ | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ | ✅ | ❌ |
| **Artifactory** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Nexus** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **GitHub Pkg** | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ | ✅ | ❌ | ✅ |
| **Azure Artifacts** | ❌ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ | ✅ |
| **AWS CodeArtifact** | ❌ | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | ✅ | ❌ | ✅ |

### Tableau Comparatif par Fonctionnalité

| Registre | Scan Sécurité | Geo-Replication | RBAC | Cleanup Policies | Proxy Cache | Block/Quarantine | API | Prix |
|----------|--------------|-----------------|------|-----------------|-------------|------------------|-----|------|
| **Docker Hub** | ⚠️ Limité | ❌ | ⚠️ Basique | ❌ | ❌ | ❌ | ✅ | $ |
| **GHCR** | ⚠️ Payant | ❌ | ✅ | ❌ | ❌ | ❌ | ✅ | $ |
| **GitLab CR** | ✅ Premium | ❌ | ✅ | ✅ | ⚠️ | ⚠️ | ✅ | $$ |
| **ACR** | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ | $$$ |
| **ECR** | ✅ | ✅ | ✅ (IAM) | ✅ | ❌ | ✅ | ✅ | $$ |
| **Google AR** | ✅ | ✅ | ✅ (IAM) | ✅ | ❌ | ✅ | ✅ | $$ |
| **Harbor** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | Gratuit (self) |
| **Artifactory** | ✅ Xray | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | $$$$ |
| **Nexus** | ✅ Pro | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | $$ (OSS gratuit) |
| **Azure Artifacts** | ❌ | ❌ | ✅ | ✅ | ✅ | ⚠️ | ✅ | $$ |
| **AWS CodeArtifact** | ❌ | ❌ | ✅ (IAM) | ⚠️ | ✅ | ⚠️ | ✅ | $$ |

**Légende:**
- **Proxy Cache**: Capacité à mettre en cache des dépôts publics (npm, Maven Central, PyPI, etc.) pour améliorer les performances et éviter le rate limiting
- **Block/Quarantine**: Capacité à bloquer ou mettre en quarantaine des artefacts spécifiques pour des raisons de sécurité (packages corrompus, vulnérabilités critiques)
- ✅ = Support complet, ⚠️ = Support partiel ou via configuration, ❌ = Non supporté

### Matrice de Sélection par Cas d'Usage

#### Startup / Petit Projet

**Besoins**: Simple, gratuit/pas cher, maintenance minimale

**Docker uniquement**:
1. **GitHub Container Registry** - Si projet GitHub
2. **Docker Hub** - Si public ou peu de repos privés
3. **GitLab Container Registry** - Si projet GitLab

**Multi-format**:
1. **GitHub Packages** - Si projet GitHub (Docker + Maven/npm/NuGet)
2. **GitLab Package Registry** - Si projet GitLab (multi-format)

#### Équipe Moyenne (5-20 devs)

**Besoins**: Multi-format, sécurité basique, self-hosted possible

**Self-hosted**:
1. **Nexus OSS** - Gratuit, multi-format
2. **Harbor** - Si focus conteneurs et Kubernetes
3. **GitLab (self-hosted)** - Si besoin DevOps complet

**SaaS**:
1. **Cloud provider registry** - Si infrastructure cloud dédiée
2. **GitHub/GitLab Packages** - Si déjà sur ces plateformes

#### Grande Entreprise

**Besoins**: Multi-format, sécurité avancée, compliance, HA

**Self-hosted**:
1. **JFrog Artifactory Pro/Enterprise** - Solution complète, mature
2. **Nexus Pro** - Alternative moins chère
3. **Harbor + Nexus** - Combinaison (Harbor pour Docker, Nexus pour reste)

**SaaS**:
1. **JFrog Cloud** - Si budget disponible
2. **Cloud provider** - Si lock-in acceptable (AWS/Azure/GCP)

#### Kubernetes-First

**Besoins**: Focus conteneurs, Helm, sécurité importante

**Recommandations**:
1. **Harbor** - Open source, features sécurité avancées
2. **Cloud provider registry** - Si Kubernetes managé (EKS/GKE/AKS)
3. **Artifactory** - Si besoins enterprise

#### Multi-Cloud

**Besoins**: Éviter vendor lock-in, multi-cloud deployment

**Recommandations**:
1. **JFrog Artifactory** - Multi-cloud, universal
2. **Nexus** - Alternative moins chère
3. **Harbor** - Pour conteneurs uniquement
4. **GitLab self-hosted** - DevOps complet

#### Open Source Project

**Besoins**: Gratuit, public, CDN global

**Docker**:
1. **Docker Hub** - Standard, grande visibilité
2. **GitHub Container Registry** - Si projet GitHub
3. **GitLab Container Registry** - Si projet GitLab

**Autres artefacts**:
- **Maven**: Maven Central
- **npm**: npmjs.com
- **PyPI**: pypi.org
- **NuGet**: nuget.org
- **RubyGems**: rubygems.org

### Critères de Sélection

#### 1. Types d'Artefacts

**Question**: Quels types d'artefacts devez-vous stocker ?

- **Docker uniquement** → Docker Hub, GHCR, GitLab CR, Harbor
- **Multi-format** → Artifactory, Nexus, Google AR, GitLab
- **Un seul langage spécifique** → Registre spécifique (npm, PyPI, etc.)

#### 2. Hébergement

**Question**: Self-hosted ou SaaS ?

**Self-hosted si**:
- Compliance stricte (données sensibles)
- Infrastructure on-premise
- Contrôle total nécessaire
- Budget infrastructure disponible

**SaaS si**:
- Pas de ressources pour maintenance
- Besoin de démarrer rapidement
- Infrastructure cloud
- Préférence opex vs capex

#### 3. Écosystème

**Question**: Quelle est votre infrastructure actuelle ?

- **GitHub** → GHCR, GitHub Packages
- **GitLab** → GitLab Container/Package Registry
- **AWS** → ECR, CodeArtifact
- **Azure** → ACR, Azure Artifacts
- **GCP** → Google Artifact Registry
- **Multi/Hybride** → Artifactory, Nexus, Harbor

#### 4. Sécurité

**Question**: Quels sont vos besoins de sécurité ?

**Sécurité basique**:
- RBAC standard
- HTTPS
- Basic authentication

**Sécurité avancée**:
- Scan de vulnérabilités intégré
- Signature d'images (Notary, Cosign)
- Compliance (SOC2, HIPAA)
- Audit logs détaillés
- Air-gapped deployment

→ Harbor, Artifactory, ACR/ECR/AR avec scanning

#### 5. Scale et Performance

**Question**: Quel est votre volume ?

**Petit volume** (<100GB, <10 users):
- Docker Hub, GHCR, Verdaccio
- Nexus OSS
- Cloud free tiers

**Volume moyen** (100GB-1TB, 10-50 users):
- Cloud provider registries
- Nexus Pro
- Harbor

**Gros volume** (>1TB, >50 users):
- Artifactory Enterprise
- Cloud provider avec geo-replication
- Harbor avec stockage distribué

#### 6. Budget

**Gratuit**:
- Docker Hub (limité)
- GHCR (limité)
- Nexus OSS
- Harbor
- Verdaccio, devpi, Athens

**Budget modéré** ($100-500/mois):
- Cloud provider pay-as-you-go
- Nexus Pro
- Docker Hub Team

**Budget élevé** (>$1000/mois):
- Artifactory Pro/Enterprise
- Cloud provider à grande échelle

## Best Practices

### 1. Sécurité des Artefacts

#### Scan de Vulnérabilités

**Automatisez le scan**:

```yaml
# GitHub Actions avec Trivy
- name: Scan image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'myimage:${{ github.sha }}'
    format: 'sarif'
    output: 'trivy-results.sarif'
```

**Politiques de scan**:
- Bloquer les images avec vulnérabilités CRITICAL
- Alerter sur vulnérabilités HIGH
- Scanner régulièrement les images existantes

#### Signature d'Images

**Cosign (Sigstore)**:

```bash
# Signer une image
cosign sign myregistry.io/myapp:1.0

# Vérifier une signature
cosign verify myregistry.io/myapp:1.0
```

**Notary (Docker Content Trust)**:

```bash
export DOCKER_CONTENT_TRUST=1
docker push myregistry.io/myapp:1.0
```

#### Contrôle d'Accès

**Principe du moindre privilège**:
- Read-only pour CI/CD pulls
- Write pour CI/CD pushes uniquement
- Admin pour équipe DevOps uniquement

**Rotation des credentials**:
- Tokens avec expiration
- Rotation régulière (90 jours max)
- Utiliser des service accounts

### 2. Organisation et Naming

#### Stratégie de Naming

**Docker images**:
```
registry.example.com/[team]/[app]:[version]
registry.example.com/platform/web-api:1.2.3
registry.example.com/platform/web-api:v1.2.3-alpine
registry.example.com/platform/web-api:latest
```

**Conventions**:
- `latest` → Dernière version stable
- `develop` → Branche develop
- `[sha]` → Commit SHA pour traçabilité
- `v[semver]` → Version sémantique

#### Structure des Repositories

**Par équipe**:
```
/team-platform
  /web-api
  /worker
  /frontend

/team-data
  /etl-pipeline
  /analytics
```

**Par environnement** (pas recommandé):
```
/production
/staging
/development
```

### 3. Retention et Cleanup

#### Politiques de Rétention

**Docker images**:
- Garder les 10 dernières versions taggées
- Supprimer les images non-taggées après 7 jours
- Garder `latest` et versions sémantiques
- Supprimer les branches feature après merge

**Exemple GitLab**:

```yaml
# .gitlab-ci.yml
cleanup:
  image: alpine
  script:
    - echo "Cleanup old images"
  only:
    - schedules
```

**Exemple Harbor**:

UI → Project → Policy → Tag Retention:
- Retain 10 most recently pushed images
- Exclude tags matching: `latest`, `v*`, `prod-*`

#### Quotas

**Définir des limites**:
- Par projet: 50GB max
- Par repo: 10GB max
- Alertes à 80% d'utilisation

### 4. Performance et Caching

#### Proxy Cache

**Nexus/Artifactory comme proxy**:

```
Internet → Proxy Cache → CI/CD
              ↓
         Cache local
```

Avantages:
- Builds plus rapides
- Moins de dépendance externe
- Évite rate limiting
- Cache pendant indisponibilité upstream

**Configuration Nexus proxy npm**:

1. Créer repository "npm-proxy" (type: proxy)
2. URL remote: `https://registry.npmjs.org`
3. Créer repository "npm-group" (group)
4. Members: npm-proxy + npm-hosted

**Utilisation**:

```bash
npm config set registry http://nexus.example.com:8081/repository/npm-group/
```

#### CDN et Geo-Replication

**Pour grandes équipes distribuées**:

- **Artifactory**: Edge nodes dans chaque région
- **Harbor**: Replication rules vers autres instances
- **Cloud providers**: Multi-region deployment

### 5. CI/CD Integration

#### Build et Push

**GitHub Actions**:

```yaml
name: Build and Push

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Login to GHCR
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ghcr.io/${{ github.repository }}:${{ github.sha }}
            ghcr.io/${{ github.repository }}:latest
```

**GitLab CI**:

```yaml
build:
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

#### Multi-Stage Build

```dockerfile
# Build stage
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

### 6. Monitoring et Observabilité

#### Métriques à Suivre

**Stockage**:
- Taille totale des artefacts
- Croissance par jour/semaine/mois
- Top repos par taille

**Utilisation**:
- Nombre de pulls/pushes par jour
- Top images les plus pullées
- Temps de réponse

**Sécurité**:
- Nombre de vulnérabilités par sévérité
- Images non-scannées
- Images avec vulnérabilités critiques non-résolues

#### Alerting

**Alertes importantes**:
- Espace disque >80%
- Scan de sécurité échoué
- Temps de réponse >5s
- Échecs d'authentication répétés

### 7. Backup et Disaster Recovery

#### Stratégie de Backup

**Ce qu'il faut sauvegarder**:
- Configuration du registry
- Métadonnées (DB PostgreSQL pour Harbor)
- Artefacts (si pas déjà en S3/blob storage)
- Credentials et secrets (dans vault séparé)

**Fréquence**:
- Configuration: Versionnée dans Git
- Métadonnées: Backup quotidien
- Artefacts: Selon criticité (souvent déjà redondant)

**Harbor backup example**:

```bash
# Backup Harbor
docker run --rm \
  -v /data/harbor:/data/harbor \
  -v /backup:/backup \
  goharbor/harbor-db:v2.9.0 \
  pg_dump -h postgres -U postgres harbor > /backup/harbor-$(date +%Y%m%d).sql
```

#### Tests de Recovery

- Tester restoration régulièrement (trimestriellement)
- Documenter procédure de recovery
- RTO/RPO définis (Recovery Time/Point Objective)

### 8. Documentation

**Documenter**:
- URLs des registries
- Conventions de naming
- Processus de publication
- Troubleshooting commun
- Contacts et escalation

**README.md exemple**:

```markdown
# Artifact Registry Guide

## Registries

- **Docker**: harbor.company.com
- **Maven**: nexus.company.com/maven-releases
- **npm**: nexus.company.com/npm-group

## Naming Conventions

Docker images: `harbor.company.com/[team]/[app]:[version]`

## How to Publish

### Docker
\`\`\`bash
docker login harbor.company.com
docker tag myapp:1.0 harbor.company.com/myteam/myapp:1.0
docker push harbor.company.com/myteam/myapp:1.0
\`\`\`

## Troubleshooting

### "unauthorized: authentication required"
→ Run `docker login harbor.company.com`

### "no space left on device"
→ Contact DevOps team
```

## Ressources Complémentaires

### Documentation Officielle

#### Registres de Conteneurs
- [Docker Hub Documentation](https://docs.docker.com/docker-hub/)
- [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [GitLab Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)
- [Harbor Documentation](https://goharbor.io/docs/)
- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/)
- [Amazon ECR](https://docs.aws.amazon.com/ecr/)
- [Google Artifact Registry](https://cloud.google.com/artifact-registry/docs)

#### Registres Universels
- [JFrog Artifactory](https://www.jfrog.com/confluence/display/JFROG/JFrog+Artifactory)
- [Sonatype Nexus](https://help.sonatype.com/repomanager3)

#### Registres Spécifiques
- [npm Documentation](https://docs.npmjs.com/)
- [Maven Central](https://central.sonatype.org/)
- [PyPI](https://pypi.org/)
- [NuGet](https://docs.microsoft.com/en-us/nuget/)

### Outils et Utilitaires

- [Trivy](https://aquasecurity.github.io/trivy/) - Scanner de vulnérabilités
- [Cosign](https://docs.sigstore.dev/cosign/overview) - Signature d'images
- [Crane](https://github.com/google/go-containerregistry/tree/main/cmd/crane) - Manipulation d'images
- [Skopeo](https://github.com/containers/skopeo) - Copie d'images entre registries
- [Docker Registry Pruner](https://github.com/tumblr/docker-registry-pruner) - Nettoyage automatique

### Guides et Tutoriels

- [CNCF Harbor Best Practices](https://goharbor.io/docs/)
- [Docker Security Best Practices](https://docs.docker.com/develop/security-best-practices/)
- [OCI Distribution Spec](https://github.com/opencontainers/distribution-spec)

### Communautés

- [CNCF Slack - #harbor](https://cloud-native.slack.com)
- [Docker Community Forums](https://forums.docker.com/)
- [r/docker](https://reddit.com/r/docker)
- [r/kubernetes](https://reddit.com/r/kubernetes)

---

**Dernière mise à jour**: Novembre 2025  
**Version**: 1.0.0
