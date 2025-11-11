# Guide CI/CD

Ce guide couvre les meilleures pratiques pour mettre en place un pipeline CI/CD efficace.

## Table des Matières
- [Principes Fondamentaux](#principes-fondamentaux)
- [Pipeline CI](#pipeline-ci)
- [Pipeline CD](#pipeline-cd)
- [Stratégies de Déploiement](#stratégies-de-déploiement)
- [GitOps](#gitops)
- [Exemples de Configuration](#exemples-de-configuration)

## Principes Fondamentaux

### Les 4 Piliers du CI/CD

1. **Continuous Integration (CI)**
   - Intégration fréquente du code
   - Build automatique
   - Tests automatisés
   - Feedback rapide

2. **Continuous Delivery (CD)**
   - Déploiement automatisable à tout moment
   - Code toujours en état déployable
   - Approbation manuelle pour production

3. **Continuous Deployment**
   - Déploiement automatique en production
   - Après passage des tests
   - Sans intervention manuelle

4. **Continuous Monitoring**
   - Surveillance continue
   - Alertes automatiques
   - Métriques en temps réel

### Bonnes Pratiques Générales

- ✅ Build une seule fois, déployer partout
- ✅ Pipeline as Code (versioning)
- ✅ Fail fast (tests rapides d'abord)
- ✅ Artifacts immuables
- ✅ Variables d'environnement pour configuration
- ✅ Secrets sécurisés
- ✅ Rollback rapide
- ✅ Tests à chaque niveau

## Pipeline CI

### Structure Type

```
1. Trigger (push, PR, schedule)
   ↓
2. Checkout Code
   ↓
3. Setup Environment (caches, dépendances)
   ↓
4. Lint & Format Check
   ↓
5. Security Scan (code, dépendances)
   ↓
6. Build
   ↓
7. Unit Tests
   ↓
8. Integration Tests
   ↓
9. Code Coverage
   ↓
10. Build Artifacts (Docker image, binaires)
    ↓
11. Push Artifacts to Registry
    ↓
12. Notifications
```

### Étapes Détaillées

#### 1. Linting et Formatage

**Objectif:** Garantir qualité et cohérence du code

**Outils par langage:**
```yaml
JavaScript/TypeScript:
  - ESLint
  - Prettier
  - TypeScript compiler

Python:
  - Pylint / Flake8
  - Black
  - mypy (type checking)

Java:
  - Checkstyle
  - SpotBugs
  - PMD

Go:
  - golint
  - gofmt
  - go vet

C#:
  - StyleCop
  - FxCop
```

**Exemple configuration:**
```yaml
lint:
  script:
    - npm run lint
    - npm run format:check
  allow_failure: false  # Bloque si fail
```

#### 2. Security Scanning

**SAST (Static Application Security Testing):**
```yaml
sast:
  - SonarQube / SonarCloud
  - Snyk
  - Checkmarx
  - Veracode
```

**Dependency Scanning:**
```yaml
dependencies:
  - Snyk
  - Dependabot
  - npm audit / pip-audit
  - OWASP Dependency-Check
```

**Secret Scanning:**
```yaml
secrets:
  - git-secrets
  - truffleHog
  - GitLeaks
```

#### 3. Tests

**Pyramide de Tests:**
```
       /\
      /E2E\        10% - Lents, fragiles
     /------\
    /  Integ \     20% - Moyens
   /----------\
  /    Unit    \   70% - Rapides, fiables
 /--------------\
```

**Configuration tests:**
```yaml
test:
  unit:
    script: npm run test:unit
    coverage: 80%
    timeout: 5min
    
  integration:
    script: npm run test:integration
    services:
      - postgres
      - redis
    timeout: 10min
    
  e2e:
    script: npm run test:e2e
    when: manual  # Optionnel sur PR
    timeout: 20min
```

#### 4. Build et Artifacts

**Docker Multi-stage Build:**
```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER node
CMD ["node", "dist/main.js"]
```

**Versioning:**
```yaml
# Semantic versioning
version: 1.2.3-beta.4+commit.sha

# Tag strategy
- main → v1.2.3, latest
- develop → v1.2.3-dev, dev
- PR → v1.2.3-pr123
```

### Optimisation des Pipelines

#### Parallélisation

```yaml
# Exécution parallèle
stages:
  - test
  
test:unit:
  stage: test
  script: npm run test:unit

test:integration:
  stage: test
  script: npm run test:integration

test:e2e:
  stage: test
  script: npm run test:e2e
```

#### Caching

```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .npm/
    - .cache/
```

#### Conditions d'Exécution

```yaml
# Exécution conditionnelle
deploy:
  script: ./deploy.sh
  only:
    - main
    - tags
  except:
    - schedules
```

## Pipeline CD

### Environnements

**Configuration type:**
```
Development (dev)
├── Auto-deploy sur push develop
├── Dernières features
└── Peut être instable

Staging (preprod)
├── Similaire à production
├── Tests finaux
└── Approbation requise

Production (prod)
├── Approbation manuelle
├── Monitoring strict
└── Rollback plan
```

### Stratégie de Déploiement

#### Variables d'Environnement

```yaml
environments:
  dev:
    url: https://dev.example.com
    variables:
      DATABASE_URL: $DEV_DB_URL
      LOG_LEVEL: debug
      
  staging:
    url: https://staging.example.com
    variables:
      DATABASE_URL: $STAGING_DB_URL
      LOG_LEVEL: info
      
  prod:
    url: https://example.com
    variables:
      DATABASE_URL: $PROD_DB_URL
      LOG_LEVEL: error
    manual: true  # Déploiement manuel
```

#### Smoke Tests Post-Déploiement

```yaml
smoke_test:
  script:
    - curl -f https://example.com/health || exit 1
    - npm run test:smoke
  after_deploy: true
```

## Stratégies de Déploiement

### Rolling Deployment

**Description:** Mise à jour progressive des instances

```
Instance 1: v1 → v2
Instance 2: v1 → v2
Instance 3: v1 → v2
Instance 4: v1 → v2
```

**Avantages:**
- Simple
- Pas de ressources supplémentaires

**Inconvénients:**
- Mix de versions temporaire
- Rollback plus lent

### Blue/Green Deployment

**Description:** Deux environnements identiques

```
Blue (v1) ← Traffic 100%
Green (v2) ← Testing

Switch traffic →

Blue (v1) ← Standby
Green (v2) ← Traffic 100%
```

**Avantages:**
- Rollback instantané
- Test en conditions réelles
- Zero downtime

**Inconvénients:**
- Coût double (temporaire)
- Complexité base de données

**Implémentation:**
```yaml
deploy:blue-green:
  script:
    - deploy to green environment
    - run smoke tests on green
    - switch traffic to green
    - keep blue for rollback
```

### Canary Deployment

**Description:** Déploiement progressif avec pourcentages

```
v1: 100% → 90% → 70% → 50% → 0%
v2:   0% → 10% → 30% → 50% → 100%
```

**Avantages:**
- Risque minimal
- Détection précoce de problèmes
- Impact limité

**Inconvénients:**
- Complexe à mettre en place
- Requiert monitoring avancé

**Configuration:**
```yaml
# Kubernetes avec Istio
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: app
spec:
  hosts:
  - app.example.com
  http:
  - route:
    - destination:
        host: app-v2
      weight: 10
    - destination:
        host: app-v1
      weight: 90
```

### Feature Flags

**Description:** Activation progressive des fonctionnalités

```javascript
if (featureFlags.isEnabled('new-checkout')) {
  return newCheckoutFlow();
} else {
  return oldCheckoutFlow();
}
```

**Avantages:**
- Déploiement découplé de l'activation
- A/B testing
- Rollback instantané (flag off)
- Trunk-based development

**Outils:**
- LaunchDarkly
- Unleash
- Split
- Custom solution

## GitOps

### Principe

GitOps est une approche où Git est la source de vérité unique pour l'infrastructure et les déploiements. Toute modification passe par Git, et un opérateur automatique synchronise l'état désiré (Git) avec l'état actuel (cluster).

```
Git Repository (État désiré)
      ↓
GitOps Operator (surveille et synchronise)
      ↓
Kubernetes Cluster (État actuel)
```

### Avantages

- ✅ **Traçabilité complète:** Historique Git de tous les changements
- ✅ **Audit:** Qui a changé quoi, quand, pourquoi (commits)
- ✅ **Rollback facile:** git revert pour revenir en arrière
- ✅ **Déclaratif:** Déclarer l'état désiré, pas les étapes
- ✅ **Disaster recovery:** Clone du repo = restauration complète
- ✅ **Pull vs Push:** Le cluster pull les changements (plus sécurisé)

### Outils GitOps

#### Flux CD

**Type:** Open source (CNCF) | Self-hosted

**Avantages:**
- Natif Kubernetes
- Support multi-tenant
- Notifications avancées
- Helm et Kustomize intégrés
- Progressive delivery (Flagger)

**Inconvénients:**
- Courbe d'apprentissage
- Kubernetes uniquement

**Cas d'usage:** Standard GitOps Kubernetes

**Installation:**
```bash
flux bootstrap github \
  --owner=mon-org \
  --repository=mon-repo \
  --path=clusters/production \
  --personal
```

#### ArgoCD

**Type:** Open source (CNCF) | Self-hosted & SaaS (Codefresh)

**Avantages:**
- UI excellente
- Multi-cluster natif
- Sync policies flexibles
- SSO intégré
- Application dependencies

**Inconvénients:**
- Plus lourd que Flux
- Configuration plus complexe

**Cas d'usage:** Équipes préférant UI, multi-cluster

**Configuration exemple:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: mon-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/mon-org/mon-repo
    targetRevision: HEAD
    path: k8s/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

#### Jenkins X

**Type:** Open source | Self-hosted

**Avantages:**
- GitOps + CI/CD intégré
- Preview environments automatiques
- Promotion automatique entre environnements

**Inconvénients:**
- Complexe
- Moins actif que Flux/Argo

**Cas d'usage:** Legacy Jenkins, besoin solution tout-en-un

### Structure Repository GitOps

**Mono-repo:**
```
gitops-repo/
├── apps/
│   ├── app1/
│   │   ├── base/
│   │   └── overlays/
│   │       ├── dev/
│   │       ├── staging/
│   │       └── prod/
│   └── app2/
├── infrastructure/
│   ├── ingress/
│   ├── cert-manager/
│   └── monitoring/
└── clusters/
    ├── dev/
    ├── staging/
    └── prod/
```

**Repo par environnement:**
```
gitops-dev/
gitops-staging/
gitops-prod/
```

### Best Practices GitOps

1. **Séparer config et code:** Repos distincts pour app et config
2. **Environnements par branches ou dossiers:** Isoler les environnements
3. **Protection de branches:** Approvals pour production
4. **Secrets externes:** Sealed Secrets, External Secrets
5. **Automated sync avec limites:** Auto-sync dev/staging, manuel prod
6. **Health checks:** Vérifier la santé des applications
7. **Notifications:** Alertes sur échecs de sync

### Workflow GitOps Typique

```
1. Développeur modifie k8s/deployment.yaml
2. Commit + Push sur Git
3. PR review + merge
4. GitOps operator détecte changement
5. Operator applique changement au cluster
6. Operator vérifie health checks
7. Si OK: sync complete, Si KO: rollback ou alerte
```

### GitOps vs Traditional CI/CD

**Traditional CI/CD (Push):**
```
CI/CD Pipeline → kubectl apply → Cluster
```
- ❌ Credentials dans CI/CD
- ❌ Pas de garantie état désiré = état actuel
- ❌ Drift possible

**GitOps (Pull):**
```
Git → GitOps Operator (dans cluster) → Cluster
```
- ✅ Pas de credentials externes
- ✅ Réconciliation continue
- ✅ Self-healing automatique

## Exemples de Configuration

### GitHub Actions

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Format check
        run: npm run format:check

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Snyk
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      
      - name: Run Trivy
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'

  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests
        run: npm run test:unit
      
      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3

  build:
    needs: [lint, security, test]
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha
      
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-dev:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: development
    
    steps:
      - name: Deploy to dev
        run: |
          # Kubernetes deployment
          kubectl set image deployment/app \
            app=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }} \
            -n development

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - name: Deploy to staging
        run: |
          kubectl set image deployment/app \
            app=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }} \
            -n staging
      
      - name: Smoke tests
        run: |
          sleep 30
          curl -f https://staging.example.com/health

  deploy-prod:
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/app \
            app=${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }} \
            -n production
      
      - name: Smoke tests
        run: |
          sleep 30
          curl -f https://example.com/health
      
      - name: Notify Slack
        uses: slackapi/slack-github-action@v1
        with:
          payload: |
            {
              "text": "Déploiement en production réussi! Version: ${{ github.sha }}"
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

### GitLab CI

```yaml
stages:
  - lint
  - test
  - security
  - build
  - deploy

variables:
  DOCKER_REGISTRY: registry.gitlab.com
  IMAGE_NAME: $CI_PROJECT_PATH

.node_template: &node_template
  image: node:18-alpine
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
  before_script:
    - npm ci

lint:
  <<: *node_template
  stage: lint
  script:
    - npm run lint
    - npm run format:check

test:unit:
  <<: *node_template
  stage: test
  script:
    - npm run test:unit
  coverage: '/Lines\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

test:integration:
  <<: *node_template
  stage: test
  services:
    - postgres:15
  variables:
    POSTGRES_PASSWORD: postgres
    DATABASE_URL: postgres://postgres:postgres@postgres:5432/test
  script:
    - npm run test:integration

security:sast:
  stage: security
  image: returntocorp/semgrep
  script:
    - semgrep --config=auto --json -o report.json .
  artifacts:
    reports:
      sast: report.json

security:dependency:
  stage: security
  image: node:18-alpine
  script:
    - npm audit --audit-level=high

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$CI_COMMIT_SHA .
    - docker push $DOCKER_REGISTRY/$IMAGE_NAME:$CI_COMMIT_SHA
    - docker tag $DOCKER_REGISTRY/$IMAGE_NAME:$CI_COMMIT_SHA $DOCKER_REGISTRY/$IMAGE_NAME:latest
    - docker push $DOCKER_REGISTRY/$IMAGE_NAME:latest
  only:
    - main
    - develop

.deploy_template: &deploy_template
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/app app=$DOCKER_REGISTRY/$IMAGE_NAME:$CI_COMMIT_SHA -n $KUBE_NAMESPACE
    - kubectl rollout status deployment/app -n $KUBE_NAMESPACE

deploy:dev:
  <<: *deploy_template
  stage: deploy
  environment:
    name: development
    url: https://dev.example.com
  variables:
    KUBE_NAMESPACE: development
  only:
    - develop

deploy:staging:
  <<: *deploy_template
  stage: deploy
  environment:
    name: staging
    url: https://staging.example.com
  variables:
    KUBE_NAMESPACE: staging
  only:
    - main

deploy:prod:
  <<: *deploy_template
  stage: deploy
  environment:
    name: production
    url: https://example.com
  variables:
    KUBE_NAMESPACE: production
  when: manual
  only:
    - main
```

## Monitoring du Pipeline

### Métriques Clés

**Lead Time for Changes:**
- Temps entre commit et production
- Objectif: < 1 jour

**Deployment Frequency:**
- Fréquence de déploiement
- Objectif: Multiple par jour

**Change Failure Rate:**
- % de déploiements causant des incidents
- Objectif: < 15%

**Time to Restore Service:**
- Temps pour corriger un incident
- Objectif: < 1 heure

### Dashboards

**Éléments à monitorer:**
- Success/failure rate des pipelines
- Durée moyenne des pipelines
- Temps d'attente (queuing)
- Coût des runners
- Couverture de tests
- Vulnérabilités détectées

## Troubleshooting

### Pipeline Lent

**Solutions:**
- Paralléliser les jobs
- Optimiser le caching
- Réduire les dépendances
- Utiliser des runners plus puissants
- Tests incrémentaux

### Pipeline Flaky

**Solutions:**
- Identifier tests instables
- Améliorer isolation des tests
- Augmenter timeouts appropriés
- Retry automatique (avec limite)
- Mocking approprié

### Problèmes de Secrets

**Solutions:**
- Utiliser secret management dédié
- Masquer dans les logs
- Rotation régulière
- Audit trail

## Checklist Finale

- [ ] Pipeline as code versionné
- [ ] Tests automatisés à tous les niveaux
- [ ] Security scanning intégré
- [ ] Artifacts versionnés et immutables
- [ ] Déploiements automatisés
- [ ] Rollback plan testé
- [ ] Monitoring et alertes configurés
- [ ] Documentation à jour
- [ ] Secrets sécurisés
- [ ] Optimisation des temps d'exécution
