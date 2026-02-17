# 🎛️ Configuration Control - Cost-Aware Scaling & Multi-Environment Setup

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 8/8  
**Status:** Production Ready ✅  
**Purpose:** Control system behavior via configuration, not code changes

---

## 📍 Purpose

This file teaches you to build **cost-aware, scalable systems** where:
- Change `scale.yaml` to scale from $20/mo → $500+/mo
- No code changes required
- Every feature has documented cost impact
- Configuration drives architecture decisions
- Tier presets help you pick right starting point

**Core Philosophy:** "Configuration as Code. Costs as Data."

---

## 🗺️ Quick Navigation

- [Overview & Philosophy](#-overview--why-configuration-matters)
- [The Control File: scale.yaml](#-the-control-file-scaleyaml)
- [Tier Presets](#-tier-presets-migration-guide)
- [Cost Calculator](#-cost-calculator-per-feature)
- [Implementation: Pydantic](#-implementation-python--pydantic)
- [Best Practices 2026](#-best-practices-2026-edition)
- [Multi-Environment Setup](#-multi-environment-setup)
- [Troubleshooting](#-troubleshooting)

---

## 🔗 Related Files

**Before this:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (Infrastructure)  
**This file:** [07_CONFIGURATION_CONTROL.md](./07_CONFIGURATION_CONTROL.md) (You are here)  
**After this:** [08_AGNOSTIC_FACTORIES.md](./08_AGNOSTIC_FACTORIES.md) (Component swapping)  
**Reference:** [01_QUICK_REFERENCE.md](./01_QUICK_REFERENCE.md) (Formulas)

---

## ✅ What You'll Learn

- [ ] Why configuration matters (cost control)
- [ ] How to structure scale.yaml
- [ ] Tier presets (Learning, Small, Growing, Enterprise)
- [ ] Cost impact of each feature
- [ ] How to implement in Python (Pydantic)
- [ ] Multi-environment setup (dev, staging, prod)
- [ ] How to migrate between tiers safely
- [ ] Common pitfalls and solutions

---

## 🎯 Overview: Why Configuration Matters

### The Problem

**Hardcoded approach (BAD):**
```python
# You want to scale from MVP to production
# MVP: Use cheap LLM (Haiku), local workers, 1 database
# Production: Use smart LLM (Sonnet), distributed workers, multi-region

# Solution: Rewrite entire codebase? ❌
```

**Configuration approach (GOOD):**
```yaml
# scale.yaml
llm.model: "claude-3-haiku"      # MVP
# Change to:
llm.model: "claude-3-5-sonnet"   # Production

# NO code changes needed ✅
```

### Why It Matters

```
MVP to Production Journey:

Week 1: $10/mo (Haiku, SQLite, no workers)
Week 4: $50/mo (Haiku, Postgres, basic workers)
Month 2: $150/mo (Sonnet, Postgres, 3 workers, caching)
Month 6: $500/mo (Sonnet + Opus, multi-region, auto-scaling)

With configuration-driven approach:
→ Same code all the way
→ Just change scale.yaml at each stage
→ No rewrites, no bugs from refactoring
```

---

## 🎛️ The Control File: `scale.yaml`

### Purpose

**Single source of truth** for:
- LLM choice (Claude vs OpenAI)
- Database type (SQLite vs Postgres vs Qdrant)
- Caching strategy (memory vs Redis)
- Worker configuration (local vs distributed)
- MCP tools (enabled or disabled)
- Observability (console vs Jaeger)
- Cost controls (budget limits)

### Template (Copy This)

Create `config/scale.yaml`:

```yaml
# 🎛️ SYSTEM CONTROL PANEL
# Change these values to scale. No code changes required.

deployment:
  tier: "small"                 # Options: learning, small, growing, enterprise
  environment: "dev"            # Options: dev, staging, prod
  debug_mode: true              # Set false for production

# 🤖 LLM INTELLIGENCE
llm:
  primary:
    provider: "anthropic"       # Options: anthropic, openai, google
    model: "claude-3-haiku"     # Cost: ~$0.25/M tokens (high speed, low cost)
    # Model options by tier:
    # learning:  claude-3-haiku        (~$0.25/M)
    # small:     claude-3-haiku        (~$0.25/M)
    # growing:   claude-3-5-sonnet     (~$3/M)
    # enterprise: claude-3-opus        (~$15/M)
  
  fallback:
    enabled: true               # Backup if primary fails
    provider: "openai"
    model: "gpt-4o-mini"        # Cheaper fallback option
  
  routing:
    # Route different tasks to different models (save 90% on costs)
    simple_tasks: "claude-3-haiku"      # Summarization, formatting
    complex_tasks: "claude-3-5-sonnet"  # Reasoning, coding
    critical_tasks: "claude-3-opus"     # Final review, complex analysis

# 💾 DATA PERSISTENCE
database:
  type: "postgresql"            # Options: sqlite, postgresql, qdrant
  # Type selection:
  # learning:  sqlite (local file, free)
  # small:     postgresql (managed, $15-30/mo)
  # growing:   postgresql (multi-region, $50+/mo)
  # enterprise: postgresql + replicas (HA, $100+/mo)
  
  replication:
    enabled: false              # 💰 COST: Doubles DB costs
    replicas: 0                 
  
  connection_pooling:
    enabled: true               # Always keep true (improves performance)
    max_connections: 20

# 📄 WORKERS & ASYNC SCALING
workers:
  enabled: false                # 💰 COST: +$50-200/mo (infrastructure)
  # Enable when > 1000 requests/day or tasks take > 10s
  
  autoscaling:
    enabled: false              # 💰 COST: Adds complexity
    min_replicas: 0             # Scale to zero to save money
    max_replicas: 5
    trigger_queue_depth: 10     # Scale up when queue > 10

# 🛠️ MCP TOOLS (Sidecars)
mcp:
  enabled: false                # 💰 COST: +$20/mo (container overhead)
  isolation_level: "process"    # Options: process (cheap), container (secure)
  
  active_tools:
    - filesystem                # Local file access (if enabled)
    # - github                  # Uncomment for Git integration (+$10/mo)
    # - slack                   # Uncomment for ChatOps (+$5/mo)

# 📊 OBSERVABILITY & TRACING
observability:
  enabled: false                # 💰 COST: +$20/mo (data ingestion)
  provider: "console"           # Options: console, jaeger, honeycomb
  # For production, always use Jaeger or Honeycomb
  sample_rate: 1.0              # 1.0 = 100% of traces (expensive)

# 🗄️ CACHING STRATEGY
caching:
  enabled: true                 # 💰 SAVINGS: Reduces LLM/DB costs by ~40-80%
  provider: "redis"             # Options: memory, redis
  ttl_seconds: 3600             # 1 Hour (adjust based on data freshness needs)

# 💰 BUDGET GUARDRAILS
cost_controls:
  hard_limit_usd: 50.00         # Stop processing if monthly spend exceeds this
  alert_threshold_usd: 40.00    # Send email warning at this point
  max_tokens_per_request: 4000  # Prevent infinite loops / runaway costs
  track_per_request: true       # Log cost of every request

```

---

## 📊 Tier Presets (Migration Guide)

### Tier 1: "Learning" (Free - $5/month)

**Goal:** Free/cheap development on laptop  
**Who:** Students, learning, portfolio projects  
**Scale:** <100 requests/day  

**Configuration:**
```yaml
deployment:
  tier: learning
  environment: dev

llm:
  primary:
    provider: anthropic
    model: claude-3-haiku        # Cheapest option

database:
  type: sqlite                    # Local file, free

workers:
  enabled: false                  # Run in-process

mcp:
  enabled: false

observability:
  enabled: false                  # Just print logs
  provider: console
```

**What This Gives You:**
- ✅ Local SQLite database
- ✅ Claude Haiku (cheapest LLM)
- ✅ In-process workers (no overhead)
- ✅ Console logging only
- ✅ Cost: $0-5/month

---

### Tier 2: "Small" (MVP) ($20-50/month)

**Goal:** First production deployment  
**Who:** MVPs, side projects, small startups  
**Scale:** 100-1K users, <10K requests/day  

**Configuration:**
```yaml
deployment:
  tier: small
  environment: prod

llm:
  primary:
    provider: anthropic
    model: claude-3-haiku        # Still cheap for MVP
  
  fallback:
    enabled: true
    provider: openai
    model: gpt-4o-mini           # Backup option

database:
  type: postgresql               # Managed cloud database
  replication:
    enabled: false

workers:
  enabled: false                 # Not needed yet

caching:
  enabled: true                  # Help with costs
  provider: redis                # Small Redis cache

observability:
  enabled: true
  provider: jaeger               # Basic tracing
  sample_rate: 0.5               # 50% of traces (cheaper)
```

**What This Gives You:**
- ✅ Cloud Postgres database ($15-30/mo)
- ✅ Redis cache ($5-10/mo)
- ✅ Claude Haiku (cost-effective)
- ✅ Basic observability
- ✅ Cost: $20-50/month

---

### Tier 3: "Growing" (Production) ($100-500/month)

**Goal:** High availability, multi-user system  
**Who:** Production systems, growing users  
**Scale:** 1K-10K users, <100K requests/day  

**Configuration:**
```yaml
deployment:
  tier: growing
  environment: prod

llm:
  primary:
    provider: anthropic
    model: claude-3-5-sonnet     # Better reasoning for scale

  routing:
    simple_tasks: claude-3-haiku      # Save costs on simple tasks
    complex_tasks: claude-3-5-sonnet  # Use smart LLM for hard tasks
    critical_tasks: claude-3-opus     # Best results for critical

database:
  type: postgresql
  replication:
    enabled: true                # High availability
    replicas: 2

workers:
  enabled: true                  # Distributed processing
  autoscaling:
    enabled: true                # Auto-scale workers

caching:
  enabled: true
  provider: redis                # Larger cache ($20-50/mo)

observability:
  enabled: true
  provider: honeycomb            # Better than Jaeger
  sample_rate: 1.0               # 100% of traces (important now)
```

**What This Gives You:**
- ✅ Distributed workers ($40-100/mo)
- ✅ Database replication ($30-50/mo)
- ✅ Claude Sonnet (better reasoning)
- ✅ Full observability
- ✅ Auto-scaling
- ✅ Cost: $100-500/month

---

### Tier 4: "Enterprise" ($500+/month)

**Goal:** Critical production, high scale  
**Who:** Mission-critical systems, large user base  
**Scale:** 10K+ users, millions of requests/day  

**Configuration:**
```yaml
deployment:
  tier: enterprise
  environment: prod

llm:
  routing:
    simple_tasks: claude-3-haiku      # Minimal cost
    complex_tasks: claude-3-5-sonnet
    critical_tasks: claude-3-opus     # Use best for critical work

database:
  type: postgresql                    # Multi-region
  replication:
    enabled: true
    replicas: 3+

workers:
  enabled: true
  autoscaling:
    enabled: true
    min_replicas: 3
    max_replicas: 20+

caching:
  enabled: true
  provider: redis                     # Large cache ($100+/mo)

observability:
  enabled: true
  provider: honeycomb                 # Enterprise observability
  sample_rate: 1.0                    # 100% of traces
```

**What This Gives You:**
- ✅ Global scaling
- ✅ Multi-region deployment
- ✅ Hybrid LLM routing (cost + quality)
- ✅ Full observability
- ✅ Auto-scaling to 20+ workers
- ✅ Cost: $500-2000+/month

---

## 💰 Cost Calculator (Per Feature)

### Before enabling any feature, check the cost:

| Feature | Impact | When to Use |
|---------|--------|------------|
| **LLM Model** | | |
| `claude-3-haiku` | $0.25/M tokens | MVP, cost-sensitive |
| `claude-3-5-sonnet` | $3/M tokens | Production, balanced |
| `claude-3-opus` | $15/M tokens | Critical tasks only |
| **Database** | | |
| SQLite | Free | Learning/dev only |
| PostgreSQL basic | $15-30/mo | Small projects |
| PostgreSQL HA | $50-100/mo | Production |
| **Workers** | | |
| In-process | $0 | <1K requests/day |
| 1 worker | $40/mo | 1K-5K requests/day |
| 3 workers + auto-scaling | $100-200/mo | 5K+ requests/day |
| **Caching** | | |
| In-memory | Free | Small datasets |
| Redis 250MB | $5-10/mo | Standard caching |
| Redis 5GB | $50/mo | Large datasets |
| **Observability** | | |
| Console logs | $0 | Development only |
| Jaeger (self-hosted) | $20/mo | Basic production |
| Honeycomb (SaaS) | $50-200/mo | Enterprise production |
| **MCP Tools** | | |
| None | $0 | Use local adapters |
| 1 tool (Filesystem) | $10/mo | Safe external access |
| 2-3 tools | $20-30/mo | Multiple integrations |

### Cost Optimization Tips

```
Save 90% on LLM costs:
→ Use Haiku for simple tasks (summarization, formatting)
→ Use Sonnet for complex tasks (reasoning)
→ Use Opus only for critical tasks (final review)

Save 40-80% on compute:
→ Enable caching (prevent LLM re-runs)
→ Use rate limiting (prevent abuse)
→ Scale to zero when idle

Save 50% on infrastructure:
→ Don't use workers until you need them (>1000 req/day)
→ Don't enable replication until HA required
→ Use free tier tools (SQLite, local cache) until scaling
```

---

## ⚙️ Implementation: Python + Pydantic

### File: `config/settings.py`

```python
import os
import yaml
from typing import Literal, Optional
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings

# Define configuration structure
class LLMConfig(BaseModel):
    provider: Literal["anthropic", "openai", "google"]
    model: str

class RoutingConfig(BaseModel):
    simple_tasks: str
    complex_tasks: str
    critical_tasks: str

class DatabaseConfig(BaseModel):
    type: Literal["sqlite", "postgresql", "qdrant"]
    replication_enabled: bool = False
    connection_pooling_enabled: bool = True
    max_connections: int = 20

class WorkerConfig(BaseModel):
    enabled: bool
    autoscaling_enabled: bool = False
    min_replicas: int = 0
    max_replicas: int = 5

class CostConfig(BaseModel):
    hard_limit_usd: float
    alert_threshold_usd: float
    max_tokens_per_request: int

# Master configuration
class AppConfig(BaseSettings):
    """
    Load configuration from:
    1. scale.yaml (defaults)
    2. Environment variables (overrides)
    3. .env file (fallback)
    """
    
    deployment_tier: str = "small"
    llm: LLMConfig
    routing: RoutingConfig
    database: DatabaseConfig
    workers: WorkerConfig
    cost_controls: CostConfig

    @classmethod
    def load(cls, yaml_path: str = "config/scale.yaml"):
        """Load and validate configuration."""
        
        # 1. Load YAML
        with open(yaml_path) as f:
            config_data = yaml.safe_load(f)
        
        # 2. Override with environment variables
        # Example: DEPLOYMENT_TIER=enterprise sets tier
        for key in os.environ:
            if key.startswith("APP_"):
                # Convert APP_DEPLOYMENT_TIER → deployment_tier
                python_key = key[4:].lower()
                config_data[python_key] = os.environ[key]
        
        # 3. Validate and initialize
        return cls(**config_data)

# Usage
config = AppConfig.load()

# Access anywhere in app
print(config.llm.model)          # "claude-3-haiku"
print(config.deployment_tier)    # "small"
print(config.cost_controls.hard_limit_usd)  # 50.00

# Change tier at runtime
if config.workers.enabled:
    start_celery_workers()

if config.observability.enabled:
    setup_telemetry()
```

---

## 🎯 Best Practices (2026 Edition)

### Rule 1: Secrets ≠ Config

**BAD:**
```yaml
# scale.yaml
database:
  password: "super-secret-123"  # DON'T DO THIS
```

**GOOD:**
```yaml
# scale.yaml
database:
  url: "${DATABASE_URL}"  # Load from environment

# .env (never commit this)
DATABASE_URL=postgresql://user:password@host:5432/db
```

---

### Rule 2: GitOps

**DO:** Commit scale.yaml to Git
```bash
git add config/scale.yaml
git commit -m "config: Update LLM to Sonnet for production"
```

**DON'T:** Manually change values on server
```bash
# Never do this:
ssh prod-server
vim /app/config/scale.yaml  # ❌ Can't track changes
```

---

### Rule 3: Environment Overrides

Allow environment variables to override YAML:

```bash
# Local dev: Use YAML defaults
python main.py

# Production: Override with env vars
export DEPLOYMENT_TIER=enterprise
export LLM_PRIMARY_MODEL=claude-3-opus
python main.py
```

---

### Rule 4: Fail Fast

If config is invalid, crash immediately:

```python
# BAD: Silent failures
try:
    config = AppConfig.load()
except:
    pass  # ❌ App runs with broken config

# GOOD: Crash loudly
try:
    config = AppConfig.load()
except ValidationError as e:
    print("❌ CONFIGURATION ERROR:")
    print(e)
    exit(1)  # ✅ Stops immediately
```

---

## 🔄 Multi-Environment Setup

### File Structure

```
config/
├── scale.yaml                 # Base configuration
├── scale.dev.yaml             # Dev overrides
├── scale.staging.yaml         # Staging overrides
└── scale.prod.yaml            # Production overrides
```

### Implementation

```python
# In settings.py
environment = os.getenv("ENVIRONMENT", "dev")
base_config = "config/scale.yaml"
env_config = f"config/scale.{environment}.yaml"

# Load base first
config = AppConfig.load(base_config)

# Merge environment-specific overrides
if os.path.exists(env_config):
    env_overrides = yaml.safe_load(open(env_config))
    config = config.copy(update=env_overrides)
```

### Example Overrides

**scale.prod.yaml:**
```yaml
# Overrides for production
deployment:
  tier: enterprise
  debug_mode: false

llm:
  primary:
    model: claude-3-opus        # Use best LLM in prod

observability:
  enabled: true
  provider: honeycomb
  sample_rate: 1.0              # 100% of traces

cost_controls:
  hard_limit_usd: 500.00        # Higher budget
  alert_threshold_usd: 450.00
```

---

## 🧪 Testing Configuration

### Verify Configuration Works

```python
def test_config_loads():
    """Ensure config.yaml is valid."""
    config = AppConfig.load("config/scale.yaml")
    assert config.llm.provider in ["anthropic", "openai"]
    assert config.database.type in ["sqlite", "postgresql", "qdrant"]

def test_tier_migration():
    """Test switching tiers."""
    # Start as 'small'
    config_small = AppConfig.load("config/scale.yaml")
    assert config_small.workers.enabled == False
    
    # Switch to 'growing'
    # Verify workers enabled
    config_growing = AppConfig.load("config/scale.growing.yaml")
    assert config_growing.workers.enabled == True
```

---

## 🤖 For Claude Code/Cursor (Explicit Instructions)

When Robert works with configuration:

### Always Ask

1. **"What tier are you scaling to?"**
   - Reference tier presets above
   - Suggest config changes needed

2. **"What's the cost impact?"**
   - Check cost calculator
   - Warn if costs spike

3. **"Need environment overrides?"**
   - Dev vs staging vs prod differ
   - Use appropriate scale.yaml

### When Robert Says "Scale to production"

```
You should:
1. Load scale.prod.yaml config
2. Verify tier matches scale (enterprise vs small)
3. Calculate monthly cost based on feature flags
4. Suggest: "This config costs ~$X/month"
5. Ask: "Ready to apply?"
```

---

## 🐛 Troubleshooting

### Problem: "Configuration is invalid"

**Symptom:** `ValidationError: invalid configuration`

**Fix:**
```bash
# Check YAML syntax
python -c "import yaml; yaml.safe_load(open('config/scale.yaml'))"

# Check against schema
python -c "from config import AppConfig; AppConfig.load()"
```

---

### Problem: "Environment variable not being used"

**Symptom:** Changed ENVIRON but config still old value

**Fix:**
```bash
# Make sure env var is set
export LLM_PRIMARY_MODEL=claude-3-opus

# Verify it's being read
python -c "from config import AppConfig; print(AppConfig.load().llm.model)"

# Restart application
```

---

### Problem: "Costs are too high"

**Symptom:** Monthly bill is $500+ (unexpected)

**Fix:**
1. Check scale.yaml for expensive settings
2. Look at routing (are you using Opus for simple tasks?)
3. Check observability.sample_rate (100% traces are expensive)
4. Check workers (do you need autoscaling?)

**Cost-saving changes:**
```yaml
# Use routing to save 90%
routing:
  simple_tasks: claude-3-haiku        # Not Opus!
  complex_tasks: claude-3-5-sonnet
  critical_tasks: claude-3-opus

# Reduce trace sampling
observability:
  sample_rate: 0.1                    # Only 10% of traces
```

---

## ✅ Checklist

### Before First Deployment

- [ ] scale.yaml created and valid
- [ ] Tier preset matches your scale
- [ ] Cost estimated and acceptable
- [ ] Environment overrides configured
- [ ] AppConfig loads without errors
- [ ] .env.example has all vars documented

### For Each Tier Migration

- [ ] New scale.yaml version created
- [ ] Feature flags reviewed
- [ ] Cost impact calculated
- [ ] Configuration tested locally
- [ ] Team trained on new tier
- [ ] Deployment verified

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework (Part 8/9)  

**Next File:** [08_AGNOSTIC_FACTORIES.md](./08_AGNOSTIC_FACTORIES.md) (Final)

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

**Configuration is your scaling lever.**  
**Change scale.yaml to scale your system. No code rewrites needed.** 🎛️