# Guide de Sécurité DevSecOps

Ce guide couvre les pratiques de sécurité essentielles à intégrer tout au long du cycle de développement.

## Table des Matières
- [Shift Left Security](#shift-left-security)
- [Sécurité du Code](#sécurité-du-code)
- [Sécurité des Dépendances](#sécurité-des-dépendances)
- [Sécurité des Conteneurs](#sécurité-des-conteneurs)
- [Sécurité de l'Infrastructure](#sécurité-de-linfrastructure)
- [Gestion des Secrets](#gestion-des-secrets)
- [Compliance et Audit](#compliance-et-audit)

## Shift Left Security

### Principe
Intégrer la sécurité dès les premières phases du développement, pas à la fin.

```
Traditional:   Dev → QA → Security → Prod
Shift Left:    Security + Dev → Security + QA → Security + Prod
```

### DevSecOps Pipeline

```
1. Pre-commit
   ├── Secret scanning (git-secrets)
   ├── Linting sécurité
   └── Pre-commit hooks

2. Commit
   ├── SAST (code analysis)
   ├── Dependency scanning
   └── License compliance

3. Build
   ├── Container scanning
   ├── SBOM generation
   └── Artifact signing

4. Deploy
   ├── Infrastructure scanning
   ├── Config validation
   └── DAST (runtime testing)

5. Runtime
   ├── Runtime monitoring
   ├── Anomaly detection
   └── Incident response
```

## Sécurité du Code

### SAST (Static Application Security Testing)

**Outils par langage:**

**JavaScript/TypeScript:**
```yaml
Tools:
  - ESLint + security plugins
  - SonarQube
  - Snyk Code
  - Semgrep

ESLint config:
  plugins:
    - eslint-plugin-security
    - eslint-plugin-no-secrets
  rules:
    - security/detect-object-injection: error
    - security/detect-non-literal-regexp: error
```

**Python:**
```yaml
Tools:
  - Bandit
  - Pylint
  - SonarQube
  - Semgrep

Bandit config:
  tests:
    - B201  # flask_debug_true
    - B301  # pickle usage
    - B302  # marshal usage
    - B303  # insecure hash functions
```

**Java:**
```yaml
Tools:
  - SpotBugs + Find Security Bugs
  - SonarQube
  - Checkmarx

Maven plugin:
  <plugin>
    <groupId>com.github.spotbugs</groupId>
    <artifactId>spotbugs-maven-plugin</artifactId>
  </plugin>
```

### Vulnérabilités Communes

#### Injection SQL

**❌ Vulnérable:**
```javascript
// Node.js
const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
db.query(query);

// Python
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
```

**✅ Sécurisé:**
```javascript
// Node.js - Parameterized query
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [req.params.id]);

// Python - Parameterized query
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

#### XSS (Cross-Site Scripting)

**❌ Vulnérable:**
```javascript
// Insertion directe HTML
document.getElementById('output').innerHTML = userInput;

// Template non échappé
res.send(`<h1>Welcome ${req.query.name}</h1>`);
```

**✅ Sécurisé:**
```javascript
// Échappement automatique
document.getElementById('output').textContent = userInput;

// Framework avec auto-escape (React, Vue)
return <h1>Welcome {userName}</h1>;

// Template engine avec escape
res.render('welcome', { name: req.query.name });
```

#### CSRF (Cross-Site Request Forgery)

**✅ Protection:**
```javascript
// Express avec csurf
const csrf = require('csurf');
app.use(csrf({ cookie: true }));

app.get('/form', (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

// Template
<form method="POST">
  <input type="hidden" name="_csrf" value="{{ csrfToken }}">
  ...
</form>
```

#### Authentication & Authorization

**Bonnes pratiques:**
```javascript
// ✅ Hashage de mots de passe
const bcrypt = require('bcrypt');
const hashedPassword = await bcrypt.hash(password, 10);

// ✅ JWT avec expiration
const token = jwt.sign(
  { userId: user.id },
  process.env.JWT_SECRET,
  { expiresIn: '1h' }
);

// ✅ Rate limiting
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});
app.use('/api/', limiter);

// ✅ Vérification permissions
function checkPermission(requiredRole) {
  return (req, res, next) => {
    if (req.user.role !== requiredRole) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}
```

### Secrets Hardcodés

**❌ À éviter:**
```javascript
// Secrets en dur
const apiKey = 'sk-1234567890abcdef';
const dbPassword = 'MyP@ssw0rd123';

// Secrets dans Git
config.json:
{
  "apiKey": "secret-key-here"
}
```

**✅ Bonnes pratiques:**
```javascript
// Variables d'environnement
const apiKey = process.env.API_KEY;
const dbPassword = process.env.DB_PASSWORD;

// .env (jamais commit)
API_KEY=sk-1234567890abcdef
DB_PASSWORD=MyP@ssw0rd123

// .gitignore
.env
.env.local
secrets/
```

**Pre-commit hook:**
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Detect secrets
if git diff --cached | grep -i "password\|secret\|api[_-]key"; then
    echo "⚠️  Possible secret detected!"
    exit 1
fi

# Use git-secrets
git secrets --scan
```

## Sécurité des Dépendances

### Scanning de Vulnérabilités

**npm/yarn:**
```bash
# Audit
npm audit
npm audit fix

# Snyk
npx snyk test
npx snyk monitor

# Configuration package.json
{
  "scripts": {
    "audit": "npm audit --audit-level=moderate"
  }
}
```

**Python:**
```bash
# pip-audit
pip-audit

# Safety
safety check

# Snyk
snyk test --file=requirements.txt
```

**GitHub Dependabot:**

Dependabot est l'outil natif GitHub pour la gestion automatique des dépendances et la détection de vulnérabilités.

**Configuration complète:**
```yaml
# .github/dependabot.yml
version: 2

# Registries privés (si nécessaire)
registries:
  npm-private:
    type: npm-registry
    url: https://npm.example.com
    token: ${{ secrets.NPM_TOKEN }}

updates:
  # npm/JavaScript
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"  # daily, weekly, monthly
      time: "09:00"
      timezone: "Europe/Paris"
    open-pull-requests-limit: 10
    reviewers:
      - "security-team"
      - "tech-leads"
    assignees:
      - "maintainer"
    labels:
      - "dependencies"
      - "security"
    milestone: 1
    
    # Message de commit personnalisé
    commit-message:
      prefix: "chore(deps)"
      prefix-development: "chore(deps-dev)"
      include: "scope"
    
    # Ignorer certaines dépendances
    ignore:
      - dependency-name: "express"
        versions: ["4.x", "5.x"]
      - dependency-name: "lodash"
        update-types: ["version-update:semver-major"]
    
    # Grouper les PRs similaires
    groups:
      # Grouper toutes les mises à jour de patch
      production-dependencies:
        patterns:
          - "*"
        update-types:
          - "patch"
          - "minor"
  
  # Python
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
    groups:
      dev-dependencies:
        dependency-type: "development"
        update-types: ["minor", "patch"]
  
  # Docker
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"
    
  # Terraform
  - package-ecosystem: "terraform"
    directory: "/"
    schedule:
      interval: "weekly"
  
  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "monthly"
    
  # Composer (PHP)
  - package-ecosystem: "composer"
    directory: "/"
    schedule:
      interval: "weekly"
  
  # Maven (Java)
  - package-ecosystem: "maven"
    directory: "/"
    schedule:
      interval: "weekly"
  
  # Go modules
  - package-ecosystem: "gomod"
    directory: "/"
    schedule:
      interval: "weekly"
```

**Dependabot Security Updates:**

En plus des version updates, Dependabot envoie automatiquement des PRs pour les vulnérabilités:

```yaml
# Activé par défaut dans Settings > Security > Dependabot security updates
# Crée automatiquement des PRs pour vulnérabilités CVE
```

**Auto-merge Workflow:**

```yaml
# .github/workflows/dependabot-auto-merge.yml
name: Dependabot Auto-Merge

on: pull_request_target

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    if: github.actor == 'dependabot[bot]'
    
    steps:
      - name: Dependabot metadata
        id: metadata
        uses: dependabot/fetch-metadata@v1.6.0
      
      - name: Enable auto-merge for patch and minor
        if: |
          steps.metadata.outputs.update-type == 'version-update:semver-patch' ||
          steps.metadata.outputs.update-type == 'version-update:semver-minor'
        run: gh pr merge --auto --squash "$PR_URL"
        env:
          PR_URL: ${{ github.event.pull_request.html_url }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Approve PR
        if: steps.metadata.outputs.update-type == 'version-update:semver-patch'
        run: gh pr review --approve "$PR_URL"
        env:
          PR_URL: ${{ github.event.pull_request.html_url }}
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Monitoring Dependabot:**

```bash
# Via GitHub CLI
gh api repos/{owner}/{repo}/dependabot/alerts

# Filtrer par sévérité
gh api repos/{owner}/{repo}/dependabot/alerts --jq '.[] | select(.security_advisory.severity=="high")'

# Statistiques
gh api repos/{owner}/{repo}/dependabot/alerts --jq 'group_by(.state) | map({state: .[0].state, count: length})'
```

### Politique de Dépendances

**Critères d'évaluation:**
```yaml
Checklist pour nouvelle dépendance:
  - [ ] Activement maintenue (dernière release < 6 mois)
  - [ ] Bonne réputation (stars, downloads)
  - [ ] Pas de vulnérabilités connues
  - [ ] License compatible (MIT, Apache, etc.)
  - [ ] Taille raisonnable (bundle size)
  - [ ] Pas de dépendances transitives suspectes
  - [ ] Bonne documentation
```

**Lockfiles:**
```yaml
Toujours commiter:
  - package-lock.json (npm)
  - yarn.lock (yarn)
  - requirements.txt + requirements.lock (Python)
  - go.sum (Go)
  - Gemfile.lock (Ruby)
```

## Sécurité des Conteneurs

### Dockerfile Sécurisé

**❌ Mauvais:**
```dockerfile
FROM node:latest
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["npm", "start"]
```

**✅ Bon:**
```dockerfile
# 1. Version spécifique et image minimale
FROM node:18-alpine3.17 AS builder

# 2. User non-root
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# 3. Copy seulement ce qui est nécessaire
COPY package*.json ./

# 4. Install production dependencies uniquement
RUN npm ci --only=production && \
    npm cache clean --force

COPY --chown=nodejs:nodejs . .

# 5. Multi-stage build
FROM node:18-alpine3.17

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --chown=nodejs:nodejs . .

# 6. User non-root
USER nodejs

# 7. Port non-privilégié
EXPOSE 3000

# 8. Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js

# 9. Commande explicite
CMD ["node", "server.js"]
```

### Scanning d'Images

**Trivy:**
```bash
# Scan image locale
trivy image myapp:latest

# Scan avec severity
trivy image --severity HIGH,CRITICAL myapp:latest

# Output JSON
trivy image -f json -o results.json myapp:latest

# CI/CD
- name: Run Trivy scanner
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'myapp:${{ github.sha }}'
    format: 'sarif'
    output: 'trivy-results.sarif'
    severity: 'CRITICAL,HIGH'
```

**Snyk:**
```bash
# Scan Dockerfile
snyk container test myapp:latest --file=Dockerfile

# Monitor en prod
snyk container monitor myapp:latest
```

### Best Practices Images

```yaml
Sécurité:
  - ✅ Images minimales (alpine, distroless)
  - ✅ Versions spécifiques (pas latest)
  - ✅ Multi-stage builds
  - ✅ User non-root
  - ✅ Scan régulier
  - ✅ Signature d'images (Docker Content Trust)
  - ❌ Pas de secrets dans l'image
  - ❌ Pas de ports privilégiés (<1024)

Optimisation:
  - ✅ .dockerignore
  - ✅ Layers optimisés (cache)
  - ✅ Nettoyage des caches
  - ✅ Compression
```

## Sécurité de l'Infrastructure

### IaC Security Scanning

**Terraform:**
```bash
# tfsec
tfsec .

# Checkov
checkov -d .

# terrascan
terrascan scan

# Example issues detected:
# - S3 bucket not encrypted
# - Security group too permissive
# - No MFA on IAM users
# - Public RDS instance
```

**Configuration tfsec:**
```yaml
# .tfsec/config.yml
severity_overrides:
  aws-s3-enable-bucket-logging: ERROR
  aws-vpc-no-public-egress-sgr: WARNING

exclude:
  - aws-s3-enable-versioning  # Exception documentée
```

### Kubernetes Security

**Pod Security Standards:**
```yaml
# Restricted (le plus sécurisé)
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 1000
    seccompProfile:
      type: RuntimeDefault
  
  containers:
  - name: app
    image: myapp:1.0.0
    
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
          - ALL
    
    resources:
      limits:
        cpu: "1"
        memory: "512Mi"
      requests:
        cpu: "100m"
        memory: "128Mi"
    
    volumeMounts:
    - name: tmp
      mountPath: /tmp
  
  volumes:
  - name: tmp
    emptyDir: {}
```

**NetworkPolicy:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: app-network-policy
spec:
  podSelector:
    matchLabels:
      app: myapp
  
  policyTypes:
  - Ingress
  - Egress
  
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 8080
  
  egress:
  - to:
    - podSelector:
        matchLabels:
          role: database
    ports:
    - protocol: TCP
      port: 5432
  
  # Autoriser DNS
  - to:
    - namespaceSelector:
        matchLabels:
          name: kube-system
    ports:
    - protocol: UDP
      port: 53
```

**Scanning Kubernetes:**
```bash
# kubesec
kubesec scan pod.yaml

# kube-bench (CIS Kubernetes Benchmark)
kube-bench run --targets master,node

# kube-hunter
kube-hunter --remote cluster.example.com
```

### Cloud Security

**AWS Security Best Practices:**
```yaml
IAM:
  - ✅ MFA pour tous les utilisateurs
  - ✅ Principe du moindre privilège
  - ✅ Roles plutôt que users pour applications
  - ✅ Rotation des credentials
  - ✅ Password policy forte

Network:
  - ✅ VPC avec subnets publics/privés
  - ✅ Security groups restrictifs
  - ✅ NACLs comme couche supplémentaire
  - ✅ VPN/Direct Connect pour admin
  - ✅ VPC Flow Logs activés

Encryption:
  - ✅ S3 buckets chiffrés (SSE-S3, SSE-KMS)
  - ✅ EBS volumes chiffrés
  - ✅ RDS encryption at rest
  - ✅ Secrets Manager/Parameter Store
  - ✅ ACM pour certificats TLS

Monitoring:
  - ✅ CloudTrail activé
  - ✅ GuardDuty activé
  - ✅ Config rules
  - ✅ Security Hub
  - ✅ CloudWatch alarmes
```

## Gestion des Secrets

### HashiCorp Vault

**Architecture:**
```
Application → Vault Agent → Vault Server
              (authentification)  (secrets storage)
```

**Configuration:**
```hcl
# vault.hcl
storage "raft" {
  path = "/vault/data"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  tls_disable = false
  tls_cert_file = "/vault/tls/cert.pem"
  tls_key_file  = "/vault/tls/key.pem"
}

seal "awskms" {
  region     = "us-east-1"
  kms_key_id = "vault-seal-key"
}

api_addr = "https://vault.example.com:8200"
```

**Usage dans app:**
```javascript
const vault = require('node-vault')({
  apiVersion: 'v1',
  endpoint: 'https://vault.example.com:8200',
  token: process.env.VAULT_TOKEN
});

// Read secret
const { data } = await vault.read('secret/data/myapp/database');
const dbPassword = data.data.password;

// Dynamic secrets (AWS)
const aws = await vault.read('aws/creds/myapp-role');
// Credentials auto-révoqués après TTL
```

### Kubernetes Secrets

**External Secrets Operator:**
```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: app-secrets
spec:
  refreshInterval: 1h
  
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  
  target:
    name: app-secrets
    creationPolicy: Owner
  
  data:
  - secretKey: database-password
    remoteRef:
      key: secret/myapp/database
      property: password
```

**Sealed Secrets:**
```bash
# Encrypt secret
kubectl create secret generic mysecret \
  --from-literal=password=mypassword \
  --dry-run=client -o yaml | \
  kubeseal -o yaml > sealed-secret.yaml

# Commit sealed-secret.yaml to Git
# Controller in cluster will decrypt it
```

## Compliance et Audit

### Logs d'Audit

**Kubernetes Audit:**
```yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
  # Log all requests at RequestResponse level
  - level: RequestResponse
    verbs: ["create", "update", "patch", "delete"]
    resources:
      - group: ""
        resources: ["secrets", "configmaps"]
  
  # Log metadata only for reads
  - level: Metadata
    verbs: ["get", "list", "watch"]
```

**AWS CloudTrail:**
```terraform
resource "aws_cloudtrail" "main" {
  name                          = "main-trail"
  s3_bucket_name                = aws_s3_bucket.cloudtrail.id
  include_global_service_events = true
  is_multi_region_trail         = true
  enable_log_file_validation    = true
  
  event_selector {
    read_write_type           = "All"
    include_management_events = true
    
    data_resource {
      type   = "AWS::S3::Object"
      values = ["arn:aws:s3:::sensitive-bucket/*"]
    }
  }
}
```

### RGPD (GDPR en anglais)

**Checklist:**
```yaml
Data Protection:
  - [ ] Data mapping (où sont les données personnelles)
  - [ ] Legal basis pour chaque traitement
  - [ ] Privacy by design
  - [ ] Data minimization
  - [ ] Encryption données sensibles
  - [ ] Pseudonymization quand possible

User Rights:
  - [ ] Droit d'accès (export données)
  - [ ] Droit de rectification
  - [ ] Droit à l'effacement (delete account)
  - [ ] Droit à la portabilité
  - [ ] Droit d'opposition

Technical:
  - [ ] Consent management
  - [ ] Data retention policies
  - [ ] Breach notification procedure (<72h)
  - [ ] DPO désigné si nécessaire
  - [ ] Privacy policy publique
```

**Implementation:**
```javascript
// Data retention
const deleteOldData = async () => {
  const cutoffDate = new Date();
  cutoffDate.setFullYear(cutoffDate.getFullYear() - 3);
  
  await db.users.deleteMany({
    lastLogin: { $lt: cutoffDate },
    accountStatus: 'inactive'
  });
};

// Data export (GDPR right to access)
app.get('/api/user/export', authenticate, async (req, res) => {
  const userData = await db.users.findOne({ id: req.user.id });
  const userOrders = await db.orders.find({ userId: req.user.id });
  
  const exportData = {
    profile: userData,
    orders: userOrders,
    exportDate: new Date()
  };
  
  res.json(exportData);
});

// Account deletion
app.delete('/api/user/account', authenticate, async (req, res) => {
  // Anonymize instead of delete for legal obligations
  await db.users.updateOne(
    { id: req.user.id },
    {
      $set: {
        email: `deleted-${req.user.id}@deleted.local`,
        name: 'Deleted User',
        deletedAt: new Date()
      },
      $unset: { phone: '', address: '' }
    }
  );
});
```

## Security Checklist

### Development
- [ ] Pre-commit hooks pour secrets
- [ ] SAST intégré dans IDE
- [ ] Code review avec focus sécurité
- [ ] Dependency scanning automatique

### CI/CD
- [ ] SAST dans pipeline
- [ ] Dependency scanning
- [ ] Container scanning
- [ ] IaC scanning
- [ ] Secrets scanning

### Infrastructure
- [ ] Network segmentation
- [ ] Encryption at rest et in transit
- [ ] Principle of least privilege
- [ ] MFA activé partout
- [ ] Audit logs activés

### Runtime
- [ ] WAF configuré
- [ ] DDoS protection
- [ ] Runtime security (Falco)
- [ ] Intrusion detection
- [ ] Security monitoring

### Compliance
- [ ] Data protection policies
- [ ] Incident response plan
- [ ] Regular security audits
- [ ] Vulnerability management
- [ ] Security training pour équipe
