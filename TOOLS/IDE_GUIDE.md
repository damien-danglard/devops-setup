# Guide de l'Environnement de Développement et des IDEs

Guide détaillé pour la configuration et l'utilisation des environnements de développement modernes, incluant les IDEs, les dev containers et les technologies de développement à distance.

## Table des Matières

- [Introduction](#introduction)
- [IDEs et Éditeurs](#ides-et-éditeurs)
- [Dev Containers](#dev-containers)
- [Environnements de Développement à Distance](#environnements-de-développement-à-distance)
- [Extensions et Plugins Essentiels](#extensions-et-plugins-essentiels)
- [Configuration Locale](#configuration-locale)
- [Best Practices](#best-practices)

## Introduction

Un environnement de développement bien configuré est essentiel pour la productivité et la qualité du code. Ce guide couvre les aspects logiciels (IDEs), les technologies de conteneurisation (dev containers), et les solutions de développement à distance.

### Objectifs d'un Bon Environnement de Développement

- **Reproductibilité**: Tous les développeurs travaillent dans le même environnement
- **Isolation**: Les projets ne s'interfèrent pas entre eux
- **Efficacité**: Outils et automatisations pour augmenter la productivité
- **Accessibilité**: Possibilité de développer depuis n'importe où
- **Onboarding Rapide**: Les nouveaux développeurs sont opérationnels rapidement

## IDEs et Éditeurs

### Visual Studio Code

![License](https://img.shields.io/badge/License-MIT-green)  
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-blue)

**Avantages:**

- **Gratuit et Open Source** (version base)
- **Légèreté**: Démarrage rapide, faible consommation de ressources
- **Extensibilité**: Marketplace avec des milliers d'extensions
- **Intégration Git**: Support natif excellent
- **Remote Development**: SSH, Containers, WSL
- **IntelliSense**: Autocomplétion intelligente
- **Debugging**: Support multi-langage intégré
- **Terminal Intégré**: Plusieurs terminaux simultanés
- **Live Share**: Collaboration en temps réel

**Inconvénients:**

- Moins de fonctionnalités "out of the box" que les IDEs JetBrains
- Peut nécessiter beaucoup d'extensions pour certains langages
- Performance variable avec de très gros projets

**Cas d'Usage Idéaux:**

- Développement web (JavaScript/TypeScript, React, Vue, Angular)
- Python, Go, Rust
- DevOps (YAML, Terraform, Kubernetes)
- Projets multi-langages
- Équipes recherchant un éditeur gratuit et polyvalent

#### Configuration Recommandée

**settings.json**:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll": true,
    "source.organizeImports": true
  },
  "files.autoSave": "onFocusChange",
  "editor.rulers": [80, 120],
  "editor.bracketPairColorization.enabled": true,
  "workbench.colorTheme": "Default Dark+",
  "terminal.integrated.defaultProfile.linux": "bash",
  "git.autofetch": true,
  "git.confirmSync": false
}
```

### JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, etc.)

![License](https://img.shields.io/badge/License-Proprietary%20(CE%20gratuit)-orange)  
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-blue)

**Avantages:**

- **IDE Complet**: Tout intégré sans extensions
- **Refactoring Puissant**: Meilleur du marché
- **Analyse de Code**: Détection d'erreurs avancée
- **Debugging**: Outils de debugging très sophistiqués
- **Base de Données**: Outils intégrés pour SQL
- **Version Control**: Support Git/SVN excellent
- **Productivity Features**: Code completion, navigation, inspections

**Inconvénients:**

- **Coût**: Versions professionnelles payantes (sauf versions Community)
- **Ressources**: Consomme plus de RAM et CPU
- **Démarrage**: Plus lent que VS Code
- **Spécialisation**: Un IDE par langage principal

#### IDEs par Langage/Framework

- **IntelliJ IDEA**: Java, Kotlin, Scala, Groovy
- **PyCharm**: Python, Data Science
- **WebStorm**: JavaScript, TypeScript, React, Vue, Angular
- **PhpStorm**: PHP, Laravel, Symfony
- **GoLand**: Go
- **RubyMine**: Ruby, Rails
- **Rider**: C#, .NET
- **CLion**: C, C++

**Cas d'Usage Idéaux:**

- Développement Java/JVM
- Projets Python complexes
- Développement professionnel avec budget
- Besoin de refactoring avancé
- Équipes cherchant un IDE tout-en-un

### Vim/Neovim

![License](https://img.shields.io/badge/License-Vim%20License-green)  
![Platform](https://img.shields.io/badge/Platform-Unix%20%7C%20Windows%20%7C%20macOS-blue)

**Avantages:**

- **Performance**: Extrêmement rapide et léger
- **Disponibilité**: Présent sur tous les systèmes Unix/Linux
- **Efficacité**: Navigation et édition ultra-rapide une fois maîtrisé
- **Customisation**: Infiniment configurable
- **Remote**: Parfait pour éditer sur serveurs distants via SSH

**Inconvénients:**

- **Courbe d'Apprentissage**: Très abrupte
- **Configuration**: Beaucoup de temps pour configurer
- **Debugging**: Moins intuitif que les IDEs modernes

**Cas d'Usage Idéaux:**

- Administration système
- Édition rapide de fichiers de configuration
- Développement sur serveurs distants
- Développeurs expérimentés cherchant la performance

#### Configuration Moderne (Neovim)

Neovim avec **Lazy.nvim** + **LSP** offre une expérience moderne:

```lua
-- init.lua example
require("lazy").setup({
  -- LSP
  "neovim/nvim-lspconfig",
  -- Autocompletion
  "hrsh7th/nvim-cmp",
  -- Syntax highlighting
  "nvim-treesitter/nvim-treesitter",
  -- Fuzzy finder
  "nvim-telescope/telescope.nvim",
  -- Git integration
  "lewis6991/gitsigns.nvim",
})
```

### Autres Éditeurs

#### Sublime Text

- **Avantages**: Très rapide, interface élégante, multi-cursors puissants
- **Inconvénients**: Payant, moins d'extensions que VS Code
- **Cas d'usage**: Édition de gros fichiers, développeurs cherchant rapidité et simplicité

#### Emacs

- **Avantages**: Extrêmement puissant, Org-mode, Magit (Git client)
- **Inconvénients**: Courbe d'apprentissage très difficile
- **Cas d'usage**: Power users, développeurs Lisp

#### Zed

- **Avantages**: Nouveau éditeur moderne, focus sur la performance et collaboration
- **Inconvénients**: Jeune, écosystème d'extensions limité
- **Cas d'usage**: Early adopters, développement collaboratif

## Dev Containers

### Introduction aux Dev Containers

Les **Dev Containers** permettent de définir un environnement de développement complet dans un conteneur Docker, garantissant que tous les développeurs travaillent dans exactement le même environnement.

#### Concepts Clés

- **Configuration as Code**: `.devcontainer/devcontainer.json`
- **Isolation**: Chaque projet a son environnement
- **Reproductibilité**: Identique sur toutes les machines
- **Onboarding**: Setup automatique pour nouveaux développeurs

### Architecture d'un Dev Container

```
projet/
├── .devcontainer/
│   ├── devcontainer.json          # Configuration principale
│   ├── Dockerfile                 # (Optionnel) Image personnalisée
│   └── docker-compose.yml         # (Optionnel) Services multiples
├── src/
└── ...
```

### Configuration devcontainer.json

#### Configuration Simple (Image Pré-construite)

```json
{
  "name": "Node.js Development",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:20",
  
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "eamodio.gitlens"
      ],
      "settings": {
        "editor.formatOnSave": true
      }
    }
  },
  
  "postCreateCommand": "npm install",
  "forwardPorts": [3000, 5000],
  "portsAttributes": {
    "3000": {
      "label": "Application",
      "onAutoForward": "notify"
    }
  }
}
```

#### Configuration Avancée (Dockerfile Personnalisé)

```json
{
  "name": "Full Stack Dev Environment",
  "build": {
    "dockerfile": "Dockerfile",
    "args": {
      "NODE_VERSION": "20",
      "PYTHON_VERSION": "3.11"
    }
  },
  
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {
      "version": "latest"
    },
    "ghcr.io/devcontainers/features/kubectl-helm-minikube:1": {
      "version": "latest",
      "helm": "latest",
      "minikube": "latest"
    },
    "ghcr.io/devcontainers/features/terraform:1": {
      "version": "latest"
    }
  },
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-python.vscode-pylance",
        "dbaeumer.vscode-eslint",
        "hashicorp.terraform",
        "ms-kubernetes-tools.vscode-kubernetes-tools"
      ]
    }
  },
  
  "mounts": [
    "source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,readonly,type=bind",
    "source=${localEnv:HOME}/.aws,target=/home/vscode/.aws,readonly,type=bind"
  ],
  
  "postCreateCommand": "npm install && pip install -r requirements.txt",
  "postStartCommand": "docker --version && kubectl version --client",
  
  "remoteUser": "vscode",
  "containerEnv": {
    "ENVIRONMENT": "development"
  }
}
```

#### Dockerfile Personnalisé

```dockerfile
FROM mcr.microsoft.com/devcontainers/python:3.11

# Install Node.js
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs

# Install additional tools
RUN apt-get update && apt-get install -y \
    git \
    curl \
    wget \
    vim \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install global npm packages
RUN npm install -g \
    typescript \
    eslint \
    prettier

# Install global Python packages
RUN pip install --no-cache-dir \
    black \
    flake8 \
    pytest \
    mypy

USER vscode
```

### Features et Addons

Les **features** permettent d'ajouter des outils sans créer de Dockerfile:

```json
{
  "features": {
    // Docker in Docker
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    
    // Node.js et npm
    "ghcr.io/devcontainers/features/node:1": {
      "version": "20"
    },
    
    // Python
    "ghcr.io/devcontainers/features/python:1": {
      "version": "3.11"
    },
    
    // AWS CLI
    "ghcr.io/devcontainers/features/aws-cli:1": {},
    
    // Azure CLI
    "ghcr.io/devcontainers/features/azure-cli:1": {},
    
    // GitHub CLI
    "ghcr.io/devcontainers/features/github-cli:1": {},
    
    // Kubernetes tools
    "ghcr.io/devcontainers/features/kubectl-helm-minikube:1": {},
    
    // Terraform
    "ghcr.io/devcontainers/features/terraform:1": {}
  }
}
```

### Dev Container avec Docker Compose

Pour des environnements avec plusieurs services:

**docker-compose.yml**:

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - ../..:/workspaces:cached
    command: sleep infinity
    network_mode: service:db
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    restart: unless-stopped
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: devuser
      POSTGRES_DB: devdb
      POSTGRES_PASSWORD: devpassword

  redis:
    image: redis:7-alpine
    restart: unless-stopped

volumes:
  postgres-data:
```

**devcontainer.json**:

```json
{
  "name": "Full Stack with Services",
  "dockerComposeFile": "docker-compose.yml",
  "service": "app",
  "workspaceFolder": "/workspaces/${localWorkspaceFolderBasename}",
  
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "ms-azuretools.vscode-docker",
        "ckolkman.vscode-postgres"
      ]
    }
  },
  
  "forwardPorts": [5000, 5432, 6379],
  "postCreateCommand": "pip install -r requirements.txt"
}
```

### Lifecycle Scripts

Les scripts de lifecycle permettent d'automatiser les tâches:

- **onCreateCommand**: Exécuté une seule fois à la création du container
- **updateContentCommand**: Exécuté lors de la reconstruction
- **postCreateCommand**: Après création, à chaque fois
- **postStartCommand**: À chaque démarrage du container
- **postAttachCommand**: Quand on s'attache au container

```json
{
  "onCreateCommand": "echo 'Container created'",
  "updateContentCommand": "echo 'Content updated'",
  "postCreateCommand": "npm install && pip install -r requirements.txt",
  "postStartCommand": "echo 'Container started' && docker ps",
  "postAttachCommand": "echo 'Attached to container'"
}
```

### Best Practices Dev Containers

1. **Utilisez des images officielles** quand possible
2. **Version pinning**: Spécifiez les versions exactes
3. **Minimisez les layers**: Combinez les commandes RUN
4. **Utilisez .dockerignore**: Excluez les fichiers inutiles
5. **Montez les credentials en read-only**: SSH keys, AWS credentials
6. **Documentez**: README expliquant le setup
7. **Testez le setup complet**: Supprimez et recréez régulièrement

### Support Multi-IDE

Les dev containers ne sont pas limités à VS Code:

#### JetBrains IDEs

JetBrains Gateway supporte les dev containers:

1. Installer JetBrains Gateway
2. Sélectionner "Dev Container"
3. Choisir le projet
4. Gateway lance l'IDE dans le container

#### Support GitHub Codespaces

Les devcontainers sont natifs dans Codespaces:

```json
{
  "name": "Codespaces Ready",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  "codespaces": {
    "recommendedSecrets": [
      "API_KEY"
    ]
  }
}
```

## Environnements de Développement à Distance

### GitHub Codespaces

![License](https://img.shields.io/badge/License-Proprietary-red)  
![Platform](https://img.shields.io/badge/Platform-Cloud-blue)

**Description:**

GitHub Codespaces est un environnement de développement cloud basé sur des dev containers, hébergé par GitHub.

**Avantages:**

- **Setup Instantané**: Lancez un environnement en quelques secondes
- **Pas de Machine Locale Requise**: Développez depuis un iPad
- **Préconfiguré**: Utilise devcontainer.json
- **Intégration GitHub Native**: Accès repos, secrets, etc.
- **Plusieurs Instances**: Plusieurs codespaces en parallèle
- **Retour Facile**: Retrouvez votre environnement intact

**Configuration:**

**.devcontainer/devcontainer.json** avec options Codespaces:

```json
{
  "name": "My Project",
  "image": "mcr.microsoft.com/devcontainers/typescript-node:20",
  
  "customizations": {
    "codespaces": {
      "openFiles": [
        "README.md",
        "src/index.ts"
      ],
      "repositories": {
        "my-org/*": {
          "permissions": {
            "contents": "write"
          }
        }
      }
    },
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint"
      ]
    }
  },
  
  "forwardPorts": [3000],
  "portsAttributes": {
    "3000": {
      "visibility": "public"
    }
  }
}
```

#### Tarification (2024)

- **Gratuit**: 60 heures/mois pour comptes personnels
- **Pro**: 90 heures/mois incluses
- **Team**: 180 heures/mois incluses
- **Enterprise**: Personnalisable

Types de machines: 2-core, 4-core, 8-core, 16-core, 32-core

#### Secrets et Variables

Gérez les secrets via GitHub Settings:

```bash
# Accès dans le codespace
echo $MY_SECRET
```

**Utilisation:**

```bash
# Créer depuis le repo GitHub (bouton "Code" > "Codespaces")

# Ou via CLI
gh codespace create --repo owner/repo
gh codespace list
gh codespace ssh -c codespace-name
gh codespace stop -c codespace-name
```

**Prebuilds:**

Accélérez le démarrage avec les prebuilds:

```json
{
  "githubActions": {
    "prebuild": {
      "branches": ["main", "develop"],
      "triggers": ["push", "workflowDispatch"]
    }
  }
}
```

### GitPod

![License](https://img.shields.io/github/license/gitpod-io/gitpod)  
![Platform](https://img.shields.io/badge/Platform-Cloud%20%7C%20Self--hosted-blue)

**Description:**

Alternative open source à Codespaces, support multi-plateformes (GitHub, GitLab, Bitbucket).

**Avantages:**

- **Open Source**: Peut être self-hosted
- **Multi-Plateforme**: GitHub, GitLab, Bitbucket
- **Snapshots**: Sauvegardez l'état de votre environnement
- **Configuration as Code**: `.gitpod.yml`
- **Prebuilds**: Environnements préconstruits

**Configuration:**

**.gitpod.yml**:

```yaml
image:
  file: .gitpod.Dockerfile

tasks:
  - name: Install Dependencies
    init: npm install && pip install -r requirements.txt
    command: npm run dev

  - name: Database
    command: docker-compose up -d postgres

ports:
  - port: 3000
    onOpen: open-preview
    visibility: public
  - port: 5432
    onOpen: ignore
    visibility: private

vscode:
  extensions:
    - dbaeumer.vscode-eslint
    - esbenp.prettier-vscode

github:
  prebuilds:
    master: true
    branches: true
    pullRequests: true
    addCheck: true
```

**.gitpod.Dockerfile**:

```dockerfile
FROM gitpod/workspace-full:latest

USER gitpod

RUN npm install -g typescript eslint prettier
RUN pip install black flake8 pytest
```

#### Self-Hosted

Pour héberger GitPod:

```bash
# Kubernetes avec Helm
helm repo add gitpod https://charts.gitpod.io
helm install gitpod gitpod/gitpod --values values.yaml
```

**Utilisation:**

```bash
# Depuis n'importe quel repo GitHub:
https://gitpod.io/#https://github.com/owner/repo

# CLI
gitpod workspace create
gitpod workspace list
gitpod workspace stop
```

### AWS Cloud9

![License](https://img.shields.io/badge/License-Proprietary-red)  
![Platform](https://img.shields.io/badge/Platform-AWS-orange)

**Avantages:**

- **Intégration AWS Native**: Accès direct aux services AWS
- **Collaboration**: Pair programming intégré
- **Terminal avec AWS CLI**: Préconfigured
- **Gratuit**: Pas de coût additionnel (juste l'EC2)

**Inconvénients:**

- **AWS Uniquement**: Lock-in AWS
- **Performance**: Dépend de l'instance EC2
- **Interface**: Moins moderne que VS Code

**Cas d'Usage:**

- Développement d'applications AWS
- Formation et workshops AWS
- Administration AWS

### Visual Studio Code Remote

#### Remote - SSH

Développez sur un serveur distant via SSH:

```json
// .vscode/settings.json sur le serveur distant
{
  "remote.SSH.remotePlatform": {
    "my-server": "linux"
  }
}
```

**Utilisation**:

1. Install extension "Remote - SSH"
2. Cmd+Shift+P > "Remote-SSH: Connect to Host"
3. Entrez `user@hostname`
4. Choisissez un dossier sur le serveur distant

#### Remote - WSL

Développez dans Windows Subsystem for Linux:

1. Install WSL2
2. Install extension "Remote - WSL"
3. Ouvrez un dossier dans WSL

#### Remote - Containers

Développez dans un container local:

1. Docker doit être installé
2. Extension "Remote - Containers"
3. Ouvrez un dossier avec `.devcontainer/`

### Comparaison des Environnements à Distance

| Caractéristique | Codespaces | GitPod | Cloud9 | Remote SSH |
|----------------|-----------|--------|--------|------------|
| **Hébergement** | GitHub | Cloud/Self | AWS | Your server |
| **Coût** | $$ | $$ | $ (EC2) | Gratuit |
| **Setup** | Instantané | Instantané | Minutes | Minutes |
| **Multi-IDE** | VS Code | Multi | Web only | VS Code |
| **Offline** | Non | Non | Non | Non |
| **Customisation** | Haute | Haute | Moyenne | Totale |

## Extensions et Plugins Essentiels

### VS Code Extensions par Catégorie

#### Langages

**JavaScript/TypeScript**:

- `dbaeumer.vscode-eslint` - Linting
- `esbenp.prettier-vscode` - Formatting
- `VisualStudioExptTeam.vscodeintellicode` - AI suggestions

**Python**:

- `ms-python.python` - Support Python
- `ms-python.vscode-pylance` - Type checking
- `ms-python.black-formatter` - Formatting

**Go**:

- `golang.go` - Support Go complet

**Rust**:

- `rust-lang.rust-analyzer` - LSP pour Rust

**Java**:

- `vscjava.vscode-java-pack` - Pack complet Java

#### DevOps et Infrastructure

- `hashicorp.terraform` - Terraform
- `ms-kubernetes-tools.vscode-kubernetes-tools` - Kubernetes
- `ms-azuretools.vscode-docker` - Docker
- `amazonwebservices.aws-toolkit-vscode` - AWS
- `ms-vscode.azure-account` - Azure

#### Git et Version Control

- `eamodio.gitlens` - Git supercharged
- `mhutchie.git-graph` - Visualisation de l'historique
- `github.vscode-pull-request-github` - GitHub PRs

#### Productivité

- `formulahendry.auto-rename-tag` - Renommage de tags HTML
- `streetsidesoftware.code-spell-checker` - Spell checker
- `usernamehw.errorlens` - Erreurs inline
- `wayou.vscode-todo-highlight` - Highlight TODOs
- `gruntfuggly.todo-tree` - TODO explorer

#### Collaboration

- `ms-vsliveshare.vsliveshare` - Live Share (pair programming)
- `ms-vscode.remote-repositories` - Browse GitHub repos

#### Thèmes et Apparence

- `github.github-vscode-theme` - GitHub Theme
- `pkief.material-icon-theme` - Icons
- `zhuangtongfa.material-theme` - Material Theme

### JetBrains Plugins Essentiels

#### IntelliJ IDEA / PyCharm / WebStorm

**Productivité**:

- **String Manipulation**: Manipulation de strings avancée
- **Key Promoter X**: Apprendre les raccourcis clavier
- **Rainbow Brackets**: Coloration des brackets
- **Grep Console**: Coloration des logs

**Quality**:

- **SonarLint**: Analyse de code en temps réel
- **CheckStyle-IDEA**: Code style checker

**DevOps**:

- **Terraform and HCL**: Support Terraform
- **Kubernetes**: Support k8s

**Git**:

- **GitToolBox**: Enrichissements Git

## Configuration Locale

### Prérequis Système

#### Outils Essentiels

```bash
# macOS
brew install git node python3 docker docker-compose

# Ubuntu/Debian
sudo apt update
sudo apt install -y git nodejs npm python3 python3-pip docker.io docker-compose

# Windows (via Chocolatey)
choco install git nodejs python docker-desktop
```

#### Gestionnaires de Versions

**nvm (Node Version Manager)**:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install 20
nvm use 20
```

**pyenv (Python Version Manager)**:

```bash
curl https://pyenv.run | bash
pyenv install 3.11.0
pyenv global 3.11.0
```

**asdf (Universal Version Manager)**:

```bash
git clone https://github.com/asdf-vm/asdf.git ~/.asdf
asdf plugin add nodejs
asdf plugin add python
asdf install nodejs 20.0.0
asdf install python 3.11.0
```

### Dotfiles et Configuration

#### Structure Recommandée

```
~/
├── .gitconfig
├── .bashrc / .zshrc
├── .vimrc / .config/nvim/
├── .ssh/
│   ├── config
│   ├── id_rsa
│   └── id_rsa.pub
└── .config/
    └── Code/User/settings.json
```

#### Git Configuration

```bash
# ~/.gitconfig
[user]
    name = Your Name
    email = your.email@example.com

[core]
    editor = vim
    autocrlf = input
    excludesfile = ~/.gitignore_global

[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --graph --oneline --decorate --all

[pull]
    rebase = true

[init]
    defaultBranch = main
```

#### Shell Configuration (Zsh + Oh My Zsh)

```bash
# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# ~/.zshrc
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="robbyrussell"

plugins=(
    git
    docker
    kubectl
    terraform
    aws
    node
    python
    vscode
)

source $ZSH/oh-my-zsh.sh

# Aliases
alias k=kubectl
alias tf=terraform
alias dc=docker-compose
```

### Synchronisation Multi-Machines

#### VS Code Settings Sync

Intégré dans VS Code via compte GitHub/Microsoft.

#### Dotfiles Repository

Créez un repo pour vos dotfiles:

```bash
git init --bare $HOME/.dotfiles
alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
dotfiles add .gitconfig .zshrc .vimrc
dotfiles commit -m "Add dotfiles"
dotfiles remote add origin git@github.com:username/dotfiles.git
dotfiles push
```

Restaurez sur une nouvelle machine:

```bash
git clone --bare git@github.com:username/dotfiles.git $HOME/.dotfiles
alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
dotfiles checkout
```

## Best Practices

### 1. Standardisation d'Équipe

**Définissez un environnement standard**:

- Même IDE ou configuration multi-IDE
- Dev containers pour tous les projets
- Extensions obligatoires documentées
- Linters et formatters partagés

**Documentez tout**:

```markdown
# Setup Development Environment

## Prérequis
- Docker Desktop
- VS Code + Extensions recommandées

## Setup
1. Clone the repo
2. Open in VS Code
3. "Reopen in Container"
4. Done!
```

### 2. Reproductibilité

**Utilisez des dev containers**:

- Tout développeur a exactement le même environnement
- Onboarding en minutes au lieu de jours
- Pas de "ça marche sur ma machine"

**Versionnez tout**:

- Configuration IDE (settings.json)
- Linters (.eslintrc, .prettierrc)
- Dev container config
- Pre-commit hooks

### 3. Performance

**Optimisez les dev containers**:

```json
{
  "mounts": [
    // Cache npm pour accélérer npm install
    "source=projectname-node-modules,target=${containerWorkspaceFolder}/node_modules,type=volume"
  ]
}
```

**Utilisez des volumes nommés**:

- Plus rapides que bind mounts (surtout macOS/Windows)
- Persistent entre rebuilds

### 4. Sécurité

**Ne commitez JAMAIS**:

- Secrets ou credentials
- Clés API
- Certificats

**Montez les credentials en read-only**:

```json
{
  "mounts": [
    "source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,readonly,type=bind"
  ]
}
```

**Utilisez des secrets managers**:

- GitHub Secrets pour Codespaces
- Variables d'environnement locales
- Vault pour production

### 5. Maintenance

**Mettez à jour régulièrement**:

- Images de base des containers
- Extensions IDE
- Outils (Docker, kubectl, etc.)

**Testez les mises à jour**:

```bash
# Rebuild le container après changement
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

### 6. Documentation

**README.md complet**:

```markdown
## Development Environment

### Local Setup
- Instructions pour setup local

### Dev Container Setup
- Prérequis: Docker + VS Code
- Steps: 1, 2, 3

### Troubleshooting
- Problème X → Solution Y
```

### 7. Outils de Qualité Intégrés

**Pre-commit hooks**:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/psf/black
    rev: 23.1.0
    hooks:
      - id: black

  - repo: https://github.com/eslint/eslint
    rev: v8.36.0
    hooks:
      - id: eslint
```

### 8. Collaboration

**Live Share** pour pair programming:

- Partage de session en temps réel
- Debugging collaboratif
- Pas besoin de partager l'écran

**Code Reviews**:

- GitHub PR reviews intégrées dans IDE
- Commentaires inline
- Suggestions de code

## Checklist de Setup

### Pour un Nouveau Projet

- [ ] Choisir l'IDE principal
- [ ] Créer devcontainer.json
- [ ] Définir les extensions obligatoires
- [ ] Configurer les linters et formatters
- [ ] Setup pre-commit hooks
- [ ] Documenter le setup dans README
- [ ] Tester le setup complet sur machine vierge
- [ ] Créer un troubleshooting guide

### Pour un Nouveau Développeur

- [ ] Installer prérequis (Docker, IDE)
- [ ] Cloner le repository
- [ ] Ouvrir dans dev container
- [ ] Vérifier que les tests passent
- [ ] Lire la documentation
- [ ] Faire un premier commit test

## Ressources Complémentaires

### Documentation Officielle

- [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)
- [Dev Containers Specification](https://containers.dev/)
- [GitHub Codespaces Docs](https://docs.github.com/en/codespaces)
- [GitPod Documentation](https://www.gitpod.io/docs)
- [JetBrains Remote Development](https://www.jetbrains.com/remote-development/)

### Exemples de Configurations

- [VS Code Dev Containers Samples](https://github.com/microsoft/vscode-dev-containers)
- [GitPod Example Projects](https://github.com/gitpod-io/template-golang-cli)
- [Awesome Dev Containers](https://github.com/manekinekko/awesome-devcontainers)

### Communautés

- [r/vscode](https://reddit.com/r/vscode)
- [r/neovim](https://reddit.com/r/neovim)
- [Dev Containers Discussions](https://github.com/devcontainers/spec/discussions)

---

**Dernière mise à jour**: Novembre 2024  
**Version**: 1.0.0
