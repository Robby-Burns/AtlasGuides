# 🏗️ Infrastructure as Code - Terraform & Deployment

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 7/8  
**Status:** Production Ready ✅  
**Purpose:** Define reproducible, version-controlled cloud infrastructure via code

---

## 📍 Purpose

This file teaches you to deploy AI agents to production using **Infrastructure as Code (IaC)** principles:

- **Never click cloud console buttons** (define everything in code)
- **Reproducible deployments** (same config = same result every time)
- **Version-controlled infrastructure** (track changes in Git)
- **Fast disaster recovery** (rebuild everything in 10 minutes)
- **Cost predictability** (know exactly what you're paying for)

**Core Philosophy:** "If it's not in Terraform, it doesn't exist in production."

---

## 🗺️ Quick Navigation

- [Philosophy & Why It Matters](#-philosophy--why-infrastructure-as-code-matters)
- [Cost Estimation by Tier](#-cost-estimation-by-tier)
- [File Structure](#-file-structure)
- [Terraform Basics](#-terraform-basics-5-min-intro)
- [Azure Container Apps Template](#-azure-container-apps-template)
- [Google Cloud Run Template](#-google-cloud-run-template)
- [Docker Compose (Local Dev)](#-docker-compose-local-development)
- [Deployment Workflow](#-deployment-workflow)
- [Troubleshooting](#-troubleshooting)

---

## 🔗 Related Files

**Before this:** [03_DEPENDENCY_MANAGEMENT.md](./03_DEPENDENCY_MANAGEMENT.md) (Python setup)  
**This file:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (You are here)  
**For config:** [07_CONFIGURATION_CONTROL.md](./07_CONFIGURATION_CONTROL.md) (Cost controls)  
**For local:** [05_CLAUDE_CONTEXT_AND_BUGS.md](./05_CLAUDE_CONTEXT_AND_BUGS.md) (Docker Compose reference)

---

## ✅ What You'll Learn

- [ ] Why Infrastructure as Code matters
- [ ] How to structure Terraform code
- [ ] Azure Container Apps deployment (enterprise)
- [ ] Google Cloud Run deployment (startup-friendly)
- [ ] Docker Compose for local development
- [ ] Deployment workflow (safe, reproducible)
- [ ] Cost estimation by tier
- [ ] How to troubleshoot infrastructure issues

---

## 🎯 Philosophy & Why Infrastructure as Code Matters

### Without IaC (The Old Way — Don't Do This)

```bash
"Click in AWS Console:
1. Login to cloud portal
2. Create container instance (manually configure 47 settings)
3. Create database (choose region, size, backups)
4. Create cache (Redis settings)
5. Create secrets manager
6. Configure networking
7. Set up monitoring
...
8. Try to recreate in disaster recovery
9. Fail because you forgot step 23
10. Spend 4 hours debugging why prod ≠ staging"
```

**Problems:**
- ❌ Not reproducible (hard to recreate)
- ❌ Not version-controlled (no audit trail)
- ❌ Manual error-prone (forgot a setting)
- ❌ No disaster recovery plan
- ❌ Hard to document

### With IaC (The Modern Way — Do This)

```bash
# Define infrastructure in code
terraform apply

# Everything deployed perfectly, reproducibly
# Tracked in Git with audit trail
# Can recreate in minutes
# Documented by code itself
```

**Benefits:**
- ✅ **Reproducible:** Same config = same result always
- ✅ **Version-controlled:** Track every change in Git
- ✅ **Documented:** Code IS the documentation
- ✅ **Fast recovery:** Rebuild entire infrastructure in 10 minutes
- ✅ **Auditable:** Who changed what, when, why
- ✅ **Cost control:** Prevent configuration drift that wastes money
- ✅ **Testable:** Spin up environments for testing
- ✅ **Multi-cloud:** Same code, different cloud providers

---

## 💰 Cost Estimation by Tier

### Tier 1: Learning / Portfolio (Free-$20/mo)

**When:** Building portfolio, learning, side projects  
**Scale:** <100 requests/day, <1K users  
**Monthly Cost:** $0-20

**Stack:**
```
- Cloud Run (free tier) or Railway
- PostgreSQL: Free tier (Neon, Supabase)
- Redis: Free tier (Upstash)
- No workers
- Scales to zero when idle
```

**Best Platforms:** Railway, Fly.io, GCP Cloud Run (free tier)

---

### Tier 2: MVP / Small Project ($20-100/mo)

**When:** MVP testing, small production  
**Scale:** 100-1K users, <10K requests/day  
**Monthly Cost:** $20-100

**Stack:**
```
- Container Apps: 0.5 CPU, 1GB RAM
- PostgreSQL: Basic tier ($15-30/mo)
- Redis: 250MB standard
- 1-2 workers (auto-scaling)
- Basic monitoring
```

**Best Platforms:** Azure ACA, GCP Cloud Run, Fly.io

---

### Tier 3: Production / Growing ($100-500/mo)

**When:** Production system, real users  
**Scale:** 1K-10K users, <100K requests/day  
**Monthly Cost:** $100-500

**Stack:**
```
- Container Apps: 2 CPU, 4GB RAM
- PostgreSQL: Managed database with backups
- Redis: 1GB+ cache
- 3-5 auto-scaling workers
- Full monitoring + alerting
- Load balancing
```

**Best Platforms:** Azure ACA, GCP (production), AWS (complex)

---

### Tier 4: Enterprise ($500+/mo)

**When:** Critical production, high scale  
**Scale:** 10K+ users, millions of requests/day  
**Monthly Cost:** $500-2000+

**Stack:**
```
- Kubernetes (EKS/AKS)
- Multi-region deployment
- PostgreSQL: Enterprise managed
- Redis: Cluster mode (HA)
- 10+ auto-scaling workers
- Comprehensive monitoring
- Disaster recovery
- DDoS protection
```

**Best Platforms:** AWS EKS, Azure AKS, GCP GKE

---

## 📁 File Structure

### Terraform Project Layout

```
/infra/
├── main.tf                    # Main configuration
├── variables.tf               # Input variables
├── outputs.tf                 # Output values
├── providers.tf               # Cloud provider config
│
├── /modules/                  # Reusable modules
│   ├── /agent_service/        # Container app
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   │
│   ├── /database/             # PostgreSQL/Redis
│   │   ├── main.tf
│   │   └── variables.tf
│   │
│   ├── /monitoring/           # Observability
│   │   ├── main.tf
│   │   └── variables.tf
│   │
│   └── /networking/           # VPC, security
│       ├── main.tf
│       └── variables.tf
│
├── /environments/
│   ├── /dev/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   │
│   ├── /staging/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   │
│   └── /prod/
│       ├── main.tf
│       ├── terraform.tfvars
│       └── backend.tf
│
└── /scripts/
    └── deploy.sh              # Deployment script
```

---

## ⚙️ Terraform Basics (5 Min Intro)

### Installation

```bash
# MacOS
brew install terraform

# Linux
curl https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update && sudo apt-get install terraform

# Verify
terraform --version
```

### Basic Workflow

```bash
# 1. Initialize (downloads providers)
terraform init

# 2. Plan (shows what will change)
terraform plan

# 3. Apply (makes changes)
terraform apply

# 4. Destroy (cleanup when done)
terraform destroy
```

### Basic Syntax

```hcl
# Define a resource
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-bucket-name"
  
  tags = {
    Environment = "production"
    Project     = "agent"
  }
}

# Reference another resource
resource "aws_s3_bucket_versioning" "my_bucket" {
  bucket = aws_s3_bucket.my_bucket.id
  
  versioning_configuration {
    status = "Enabled"
  }
}

# Output a value
output "bucket_name" {
  value       = aws_s3_bucket.my_bucket.id
  description = "The S3 bucket name"
}
```

---

## ☁️ Azure Container Apps Template

### File: `infra/environments/prod/main.tf`

```hcl
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.90.0"  # Lock exact version
    }
  }
}

provider "azurerm" {
  features {}
}

# Create resource group
resource "azurerm_resource_group" "agent" {
  name     = "rg-agent-prod"
  location = "East US"
}

# Create App Service Plan
resource "azurerm_app_service_plan" "agent" {
  name                = "plan-agent-prod"
  location            = azurerm_resource_group.agent.location
  resource_group_name = azurerm_resource_group.agent.name
  kind                = "Linux"
  reserved            = true

  sku {
    tier = "Standard"
    size = "S1"
  }
}

# Create Container App
resource "azurerm_container_app" "agent" {
  name                         = "agent-prod"
  container_app_environment_id = azurerm_container_app_environment.agent.id
  resource_group_name          = azurerm_resource_group.agent.name
  revision_mode                = "Single"

  template {
    container {
      name   = "agent"
      image  = var.container_image  # gcr.io/project/agent:latest
      cpu    = 0.5
      memory = "1Gi"

      env {
        name  = "OPENAI_API_KEY"
        secret_name = "openai-key"
      }

      env {
        name  = "DATABASE_URL"
        secret_name = "database-url"
      }

      env {
        name  = "ENVIRONMENT"
        value = "production"
      }
    }
  }

  ingress {
    allow_insecure_connections = false
    external_enabled           = true
    target_port                = 8000
    transport                  = "auto"
  }

  identity {
    type = "SystemAssigned"
  }
}

# Create PostgreSQL Database
resource "azurerm_postgresql_server" "agent" {
  name                         = "postgres-agent-prod"
  location                     = azurerm_resource_group.agent.location
  resource_group_name          = azurerm_resource_group.agent.name
  administrator_login          = "agentadmin"
  administrator_login_password = random_password.db_password.result
  version                      = "13"
  ssl_enforcement_enabled      = true

  sku_name = "B_Gen5_2"  # Burstable, 2 vCores

  storage_mb = 51200  # 50GB
}

# Create Redis Cache
resource "azurerm_redis_cache" "agent" {
  name                = "redis-agent-prod"
  location            = azurerm_resource_group.agent.location
  resource_group_name = azurerm_resource_group.agent.name
  capacity            = 1
  family              = "C"
  sku_name            = "Standard"
  enable_non_ssl_port = false
}

# Generate random password
resource "random_password" "db_password" {
  length  = 32
  special = true
}

# Store secret in Key Vault
resource "azurerm_key_vault" "agent" {
  name                = "kv-agent-prod"
  location            = azurerm_resource_group.agent.location
  resource_group_name = azurerm_resource_group.agent.name
  tenant_id           = data.azurerm_client_config.current.tenant_id
  sku_name            = "standard"
}

# Store database password
resource "azurerm_key_vault_secret" "db_password" {
  name         = "database-password"
  value        = random_password.db_password.result
  key_vault_id = azurerm_key_vault.agent.id
}

# Output connection string
output "database_url" {
  value = "postgresql://${azurerm_postgresql_server.agent.administrator_login}:${random_password.db_password.result}@${azurerm_postgresql_server.agent.fqdn}:5432/agent"
  sensitive = true
}

output "redis_url" {
  value = azurerm_redis_cache.agent.primary_connection_string
  sensitive = true
}
```

### Deploy to Azure

```bash
# Navigate to environment
cd infra/environments/prod

# Initialize
terraform init

# Plan (review changes)
terraform plan -out=tfplan

# Apply
terraform apply tfplan

# View outputs
terraform output
```

---

## ☁️ Google Cloud Run Template

### File: `infra/environments/prod/main.tf` (GCP Version)

```hcl
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "=5.9.0"
    }
  }
}

provider "google" {
  project = var.gcp_project_id
  region  = var.gcp_region
}

# Create Cloud Run Service
resource "google_cloud_run_service" "agent" {
  name     = "agent-prod"
  location = var.gcp_region

  template {
    spec {
      service_account_name = google_service_account.agent.email
      
      containers {
        image = var.container_image  # gcr.io/project/agent:latest
        
        env {
          name  = "OPENAI_API_KEY"
          value_from {
            secret_key_ref {
              name = google_secret_manager_secret.openai_key.id
              key  = "latest"
            }
          }
        }

        env {
          name  = "DATABASE_URL"
          value_from {
            secret_key_ref {
              name = google_secret_manager_secret.database_url.id
              key  = "latest"
            }
          }
        }

        resources {
          limits = {
            cpu    = "1"
            memory = "512Mi"
          }
        }
      }

      timeout_seconds       = 300
      service_account_email = google_service_account.agent.email
    }

    metadata {
      annotations = {
        "run.googleapis.com/cloudsql-instances" = google_sql_database_instance.agent.connection_name
      }
    }
  }

  traffic {
    percent         = 100
    latest_revision = true
  }
}

# Create Cloud SQL (PostgreSQL)
resource "google_sql_database_instance" "agent" {
  name             = "postgres-agent-prod"
  database_version = "POSTGRES_15"
  region           = var.gcp_region

  settings {
    tier              = "db-f1-micro"
    availability_type = "REGIONAL"

    backup_configuration {
      enabled    = true
      start_time = "03:00"
    }

    database_flags {
      name  = "max_connections"
      value = "100"
    }
  }
}

# Create database
resource "google_sql_database" "agent" {
  name     = "agent"
  instance = google_sql_database_instance.agent.name
}

# Create Cloud Memorystore (Redis)
resource "google_redis_instance" "agent" {
  name           = "redis-agent-prod"
  tier           = "standard"
  memory_size_gb = 1
  region         = var.gcp_region
  redis_version  = "7.0"
}

# Create Service Account
resource "google_service_account" "agent" {
  account_id   = "agent-prod"
  display_name = "Agent Service Account"
}

# Grant permissions
resource "google_project_iam_member" "agent" {
  project = var.gcp_project_id
  role    = "roles/cloudsql.client"
  member  = "serviceAccount:${google_service_account.agent.email}"
}

# Output
output "cloud_run_url" {
  value = google_cloud_run_service.agent.status[0].url
}
```

### Deploy to Google Cloud

```bash
cd infra/environments/prod
terraform init
terraform plan -out=tfplan
terraform apply tfplan
terraform output cloud_run_url
```

---

## 🐳 Docker Compose (Local Development)

### File: `docker-compose.yml`

```yaml
version: '3.8'

services:
  # Main application
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/agent
      - REDIS_URL=redis://redis:6379
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - ENVIRONMENT=development
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - .:/app  # Mount source for hot reload
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload

  # PostgreSQL Database
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: agent
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Jaeger for distributed tracing
  jaeger:
    image: jaegertracing/all-in-one:latest
    ports:
      - "16686:16686"  # UI
      - "4317:4317"    # OTLP gRPC

volumes:
  postgres_data:
  redis_data:
```

### Run Locally

```bash
# Start all services
docker-compose up

# In another terminal, watch logs
docker-compose logs -f app

# Access:
# App: http://localhost:8000
# Jaeger UI: http://localhost:16686
```

---

## 📋 Deployment Workflow

### Pre-Deployment Checklist

- [ ] Tests pass (80%+ coverage)
- [ ] Docker image built and tested
- [ ] Secrets configured in Key Vault / Secret Manager
- [ ] Terraform plan reviewed
- [ ] Database migrations tested
- [ ] Monitoring configured
- [ ] Runbook written

### Step-by-Step Deployment

```bash
# 1. Build Docker image
docker build -t my-agent:latest .

# 2. Push to registry
docker tag my-agent:latest gcr.io/myproject/agent:latest
docker push gcr.io/myproject/agent:latest

# 3. Plan infrastructure
cd infra/environments/prod
terraform plan -out=tfplan

# 4. Review plan carefully!
cat tfplan

# 5. Apply infrastructure
terraform apply tfplan

# 6. Verify deployment
terraform output

# 7. Test endpoint
curl https://agent-prod.azurecontainers.io/health

# 8. Monitor
# Watch logs and metrics in cloud console
```

---

## 🔧 Troubleshooting

### Problem: "Terraform state out of sync"

**Symptom:** `terraform plan` shows changes that don't exist

**Fix:**
```bash
# Refresh state
terraform refresh

# Or: Force state lock release
terraform force-unlock <LOCK_ID>
```

---

### Problem: "Container won't start in cloud"

**Symptom:** Cloud Run / Container Apps shows "unhealthy"

**Steps:**
1. Check logs: `gcloud run logs read --limit 50`
2. Verify environment variables set
3. Check health endpoint: `curl /health`
4. Verify secrets mounted correctly

---

### Problem: "Database connection fails"

**Symptom:** App can't connect to PostgreSQL

**Fix:**
```bash
# Check security group / firewall rules
# Verify DATABASE_URL in Key Vault matches actual instance
# Test connection locally first

# Local test:
psql postgresql://user:password@localhost:5432/agent
```

---

## 🤖 For Claude Code/Cursor (Explicit Instructions)

When Robert works with infrastructure:

### Always Do

- ✅ Review `terraform plan` before applying
- ✅ Never use `terraform destroy` without confirmation
- ✅ Keep secrets in Key Vault / Secret Manager (never hardcoded)
- ✅ Use `terraform lock file` (commit `.terraform.lock.hcl`)
- ✅ Tag resources with environment, project, owner

### When Robert Says "Deploy to production"

```
You should:
1. Ask: "Have you reviewed the terraform plan?"
2. Suggest: terraform plan -out=tfplan
3. Show: What resources will be created/modified
4. Ask: "Ready to apply?"
5. Execute: terraform apply tfplan
6. Verify: terraform output and test endpoint
```

### When Infrastructure Breaks

```
Check in order:
1. terraform plan (what changed?)
2. Cloud provider console (what does it show?)
3. Application logs (are they running?)
4. Environment variables (are secrets mounted?)
5. Network (can it reach database?)
```

---

## ✅ Deployment Checklist

### Before Production Deployment

- [ ] Terraform code reviewed and tested
- [ ] Docker image built, scanned for vulnerabilities
- [ ] Secrets stored in Key Vault / Secret Manager
- [ ] Database backups configured
- [ ] Monitoring and alerting configured
- [ ] Runbook written (how to deploy, rollback, troubleshoot)
- [ ] Team trained on Terraform workflow
- [ ] CI/CD pipeline configured

### After Deployment

- [ ] Verify application is running
- [ ] Test critical endpoints
- [ ] Check logs for errors
- [ ] Verify database connection
- [ ] Monitor resource usage
- [ ] Update .claude-context.md with deployment details

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework  

**Next Files:**
- [07_CONFIGURATION_CONTROL.md](./07_CONFIGURATION_CONTROL.md) (Cost controls)
- [08_AGNOSTIC_FACTORIES.md](./08_AGNOSTIC_FACTORIES.md) (Component swapping)

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

**Infrastructure as Code is your deployment safety net.**  
**Master Terraform, and you'll deploy with confidence.** 🚀