# 🏭 Agnostic Factories - Swap Components Without Rewriting Code

**Version:** 1.2.0 | **Updated:** February 14, 2026 | **Part:** 9/9  
**Status:** Production Ready ✅  
**Purpose:** Build systems that adapt to configuration changes without code rewrites

---

## 📍 Purpose

This is the **FINAL file** - it teaches you to build truly adaptable systems where:
- Swap PostgreSQL ↔ Vector Database (1 env var)
- Swap Claude ↔ OpenAI (1 line change)
- Swap local execution ↔ distributed execution (1 config toggle)
- Swap MCP bridges ↔ local adapters (1 flag)
- **No code rewrites. No bugs from refactoring. Pure configuration.**

**Core Pattern:** "One interface, many implementations. Pick at runtime."

---

## 🗺️ Quick Navigation

- [Pattern Overview](#-pattern-overview-what-is-agnostic-design)
- [The Database Factory](#-the-database-factory-swap-without-rewriting)
- [The LLM Factory](#-the-llm-factory-anthropic--openai--google)
- [The Worker Factory](#-the-worker-factory-local--distributed)
- [The Tooling Factory](#-the-tooling-factory-local--mcp)
- [Real-World Examples](#-real-world-examples)
- [Best Practices](#-best-practices)
- [Troubleshooting](#-troubleshooting)

---

## 🔗 Related Files

**Before this:** [07_CONFIGURATION_CONTROL.md](./07_CONFIGURATION_CONTROL.md) (Cost control)  
**This file:** [08_AGNOSTIC_FACTORIES.md](./08_AGNOSTIC_FACTORIES.md) (You are here - FINAL!)  
**Foundational:** [02_COMPLETE_GUIDE.md](./02_COMPLETE_GUIDE.md) Section 7 (Database Adapter Pattern)  
**Reference:** [06_INFRASTRUCTURE_AS_CODE.md](./06_INFRASTRUCTURE_AS_CODE.md) (Where factories run)

---

## ✅ What You'll Learn

- [ ] What agnostic design means (and why it matters)
- [ ] How to build database adapters (swap SQL ↔ Vector)
- [ ] How to build LLM factories (swap Claude ↔ OpenAI)
- [ ] How to build worker factories (swap local ↔ distributed)
- [ ] How to build tooling factories (swap local ↔ MCP)
- [ ] How to test factories safely
- [ ] When NOT to use factories (avoid over-engineering)
- [ ] Common pitfalls and solutions

---

## 🎯 Pattern Overview: What is Agnostic Design?

### The Problem (Without Factories)

```python
# BAD: Hardcoded to PostgreSQL
class ResearchAgent:
    def __init__(self):
        self.db = PostgresAdapter()  # 🔒 LOCKED IN
    
    def search(self, query):
        results = self.db.query(f"SELECT * FROM docs WHERE {query}")
        return results

# Later: "Let's try Qdrant (vector DB) for better search"
# Problem: Rewrite entire agent, search for all PostgresAdapter references
```

### The Solution (With Factories)

```python
# GOOD: Agnostic to database
class ResearchAgent:
    def __init__(self, db_adapter):
        self.db = db_adapter  # 🔓 FLEXIBLE
    
    def search(self, query):
        results = self.db.search(query)  # Same interface
        return results

# Usage
# Dev: Use local PostgreSQL
agent = ResearchAgent(PostgresAdapter())

# Prod: Switch to Qdrant
agent = ResearchAgent(QdrantAdapter())

# No agent code changed. Perfect.
```

### Why This Matters

| Scenario | Without Factories | With Factories |
|----------|-------------------|----------------|
| **Swap databases** | Rewrite agent code | Change 1 env var |
| **Test with mock DB** | Hard to isolate | Easy (inject mock) |
| **Support multiple DB types** | Duplicate code | Same code, different adapters |
| **Add new provider** | Modify agent | Add new adapter class |
| **Scale from SQLite → PostgreSQL** | Major refactor | 1 config change |

---

## 🏗️ The Database Factory (Swap Without Rewriting)

### Step 1: Define the Interface

**File: `app/interfaces/database.py`**

```python
from abc import ABC, abstractmethod
from typing import List, Dict, Optional

class DatabaseAdapter(ABC):
    """Abstract interface that all databases must implement."""
    
    @abstractmethod
    async def search(self, query: str, limit: int = 10) -> List[Dict]:
        """Search documents. Return list of results."""
        pass
    
    @abstractmethod
    async def save(self, collection: str, data: Dict) -> str:
        """Save document. Return document ID."""
        pass
    
    @abstractmethod
    async def delete(self, collection: str, doc_id: str) -> bool:
        """Delete document. Return success."""
        pass
    
    @abstractmethod
    async def update(self, collection: str, doc_id: str, data: Dict) -> bool:
        """Update document. Return success."""
        pass
```

### Step 2: Implement PostgreSQL Adapter

**File: `app/adapters/postgres_adapter.py`**

```python
from sqlalchemy import create_engine, text
from sqlalchemy.orm import sessionmaker
import json

class PostgresAdapter(DatabaseAdapter):
    """PostgreSQL implementation."""
    
    def __init__(self, database_url: str):
        self.engine = create_engine(database_url)
        self.Session = sessionmaker(bind=self.engine)
    
    async def search(self, query: str, limit: int = 10) -> List[Dict]:
        """Full-text search on PostgreSQL."""
        session = self.Session()
        try:
            # PostgreSQL full-text search
            sql = text(f"""
                SELECT id, content, ts_rank(to_tsvector(content), 
                query) as rank
                FROM documents, plainto_tsquery('{query}') query
                WHERE to_tsvector(content) @@ query
                ORDER BY rank DESC
                LIMIT {limit}
            """)
            results = session.execute(sql).fetchall()
            return [dict(r) for r in results]
        finally:
            session.close()
    
    async def save(self, collection: str, data: Dict) -> str:
        """Insert document."""
        session = self.Session()
        try:
            sql = text(f"""
                INSERT INTO {collection} (data, created_at)
                VALUES (:data, NOW())
                RETURNING id
            """)
            result = session.execute(sql, {"data": json.dumps(data)})
            session.commit()
            return str(result.scalar())
        finally:
            session.close()
    
    async def delete(self, collection: str, doc_id: str) -> bool:
        """Delete document."""
        session = self.Session()
        try:
            sql = text(f"DELETE FROM {collection} WHERE id = :id")
            session.execute(sql, {"id": doc_id})
            session.commit()
            return True
        finally:
            session.close()
    
    async def update(self, collection: str, doc_id: str, data: Dict) -> bool:
        """Update document."""
        session = self.Session()
        try:
            sql = text(f"""
                UPDATE {collection}
                SET data = :data, updated_at = NOW()
                WHERE id = :id
            """)
            session.execute(sql, {"data": json.dumps(data), "id": doc_id})
            session.commit()
            return True
        finally:
            session.close()
```

### Step 3: Implement Qdrant Adapter (Vector DB)

**File: `app/adapters/qdrant_adapter.py`**

```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams

class QdrantAdapter(DatabaseAdapter):
    """Qdrant vector database implementation."""
    
    def __init__(self, qdrant_url: str):
        self.client = QdrantClient(url=qdrant_url)
    
    async def search(self, query: str, limit: int = 10) -> List[Dict]:
        """Vector similarity search."""
        # First: Convert query to embedding (via LLM or embedding model)
        query_embedding = await self._embed(query)
        
        # Second: Search Qdrant
        results = self.client.search(
            collection_name="documents",
            query_vector=query_embedding,
            limit=limit
        )
        
        return [
            {
                "id": result.id,
                "content": result.payload.get("content"),
                "score": result.score
            }
            for result in results
        ]
    
    async def save(self, collection: str, data: Dict) -> str:
        """Save document with embeddings."""
        doc_id = str(uuid.uuid4())
        embedding = await self._embed(data.get("content", ""))
        
        self.client.upsert(
            collection_name="documents",
            points=[{
                "id": doc_id,
                "vector": embedding,
                "payload": data
            }]
        )
        return doc_id
    
    async def _embed(self, text: str) -> List[float]:
        """Convert text to embedding."""
        # Could use OpenAI, Anthropic, or local embedding model
        # For now, use a simple approach
        from sentence_transformers import SentenceTransformer
        model = SentenceTransformer('all-MiniLM-L6-v2')
        return model.encode(text).tolist()
```

### Step 4: Create the Factory

**File: `app/factory.py`**

```python
import os
from app.interfaces.database import DatabaseAdapter
from app.adapters.postgres_adapter import PostgresAdapter
from app.adapters.qdrant_adapter import QdrantAdapter

def get_database_adapter() -> DatabaseAdapter:
    """
    Factory: Create appropriate database adapter based on config.
    
    Usage:
        db = get_database_adapter()
        results = await db.search("AI agents")
    """
    
    db_type = os.getenv("DATABASE_TYPE", "postgresql").lower()
    
    if db_type == "postgresql":
        return PostgresAdapter(os.getenv("DATABASE_URL"))
    
    elif db_type == "qdrant":
        return QdrantAdapter(os.getenv("QDRANT_URL"))
    
    else:
        raise ValueError(f"Unknown database type: {db_type}")
```

### Step 5: Use in Your Application

**File: `app/agents/researcher.py`**

```python
from app.factory import get_database_adapter

class ResearcherAgent:
    def __init__(self):
        # Agent doesn't care which database!
        self.db = get_database_adapter()
    
    async def research(self, topic: str):
        # Same code works with PostgreSQL or Qdrant
        results = await self.db.search(topic)
        return results

# Usage:
# DATABASE_TYPE=postgresql → Uses PostgreSQL
# DATABASE_TYPE=qdrant → Uses Qdrant
# No agent code changes!
```

### Switching Databases

```bash
# Dev: Use PostgreSQL
export DATABASE_TYPE=postgresql
export DATABASE_URL=postgresql://localhost/agent
python main.py

# Production: Switch to Qdrant (1 env var!)
export DATABASE_TYPE=qdrant
export QDRANT_URL=http://qdrant-server:6333
python main.py
# Agent works exactly the same
```

---

## 🤖 The LLM Factory (Claude ↔ OpenAI ↔ Google)

### Interface

**File: `app/interfaces/llm.py`**

```python
from abc import ABC, abstractmethod
from typing import List, Dict

class LLMProvider(ABC):
    """All LLM providers must implement this."""
    
    @abstractmethod
    async def generate(self, prompt: str, max_tokens: int = 1000) -> str:
        """Generate text."""
        pass
    
    @abstractmethod
    async def chat(self, messages: List[Dict]) -> str:
        """Chat completion."""
        pass
    
    @abstractmethod
    def get_cost_per_1m_tokens(self) -> float:
        """Return cost for tracking."""
        pass
```

### Implementation: Claude

**File: `app/adapters/claude_llm.py`**

```python
from anthropic import Anthropic

class ClaudeLLM(LLMProvider):
    def __init__(self, model: str = "claude-3-haiku"):
        self.client = Anthropic()
        self.model = model
    
    async def generate(self, prompt: str, max_tokens: int = 1000) -> str:
        message = self.client.messages.create(
            model=self.model,
            max_tokens=max_tokens,
            messages=[{"role": "user", "content": prompt}]
        )
        return message.content[0].text
    
    async def chat(self, messages: List[Dict]) -> str:
        response = self.client.messages.create(
            model=self.model,
            max_tokens=1000,
            messages=messages
        )
        return response.content[0].text
    
    def get_cost_per_1m_tokens(self) -> float:
        costs = {
            "claude-3-haiku": 0.25,
            "claude-3-5-sonnet": 3.0,
            "claude-3-opus": 15.0
        }
        return costs.get(self.model, 3.0)
```

### Implementation: OpenAI

**File: `app/adapters/openai_llm.py`**

```python
from openai import OpenAI

class OpenAILLM(LLMProvider):
    def __init__(self, model: str = "gpt-4o-mini"):
        self.client = OpenAI()
        self.model = model
    
    async def generate(self, prompt: str, max_tokens: int = 1000) -> str:
        response = self.client.completions.create(
            model=self.model,
            prompt=prompt,
            max_tokens=max_tokens
        )
        return response.choices[0].text
    
    async def chat(self, messages: List[Dict]) -> str:
        response = self.client.chat.completions.create(
            model=self.model,
            messages=messages,
            max_tokens=1000
        )
        return response.choices[0].message.content
    
    def get_cost_per_1m_tokens(self) -> float:
        costs = {
            "gpt-4o-mini": 0.15,
            "gpt-4": 30.0,
            "gpt-4o": 5.0
        }
        return costs.get(self.model, 5.0)
```

### Factory

**File: `app/factory.py`** (add this)

```python
def get_llm_provider() -> LLMProvider:
    """Create LLM provider based on config."""
    
    provider = os.getenv("LLM_PROVIDER", "anthropic").lower()
    
    if provider == "anthropic":
        model = os.getenv("LLM_MODEL", "claude-3-haiku")
        return ClaudeLLM(model)
    
    elif provider == "openai":
        model = os.getenv("LLM_MODEL", "gpt-4o-mini")
        return OpenAILLM(model)
    
    else:
        raise ValueError(f"Unknown LLM provider: {provider}")

# Usage
llm = get_llm_provider()
response = await llm.generate("What is AI?")
```

### Switching LLMs

```bash
# Use Claude
export LLM_PROVIDER=anthropic
export LLM_MODEL=claude-3-haiku

# Switch to OpenAI
export LLM_PROVIDER=openai
export LLM_MODEL=gpt-4

# No code changes!
```

---

## ⚙️ The Worker Factory (Local ↔ Distributed)

### Interface

**File: `app/interfaces/task_runner.py`**

```python
from abc import ABC, abstractmethod
from typing import Dict

class TaskRunner(ABC):
    """All task runners must implement this."""
    
    @abstractmethod
    async def run(self, task_name: str, payload: Dict) -> Dict:
        """Run a task, return result."""
        pass
    
    @abstractmethod
    async def queue(self, task_name: str, payload: Dict) -> str:
        """Queue a task for async execution, return task ID."""
        pass
```

### Implementation: Local (In-Process)

**File: `app/runners/local_runner.py`**

```python
import asyncio

class LocalTaskRunner(TaskRunner):
    """Run tasks in current process."""
    
    async def run(self, task_name: str, payload: Dict) -> Dict:
        """Execute task immediately."""
        # Import task function dynamically
        task_func = self._get_task_func(task_name)
        result = await task_func(payload)
        return result
    
    async def queue(self, task_name: str, payload: Dict) -> str:
        """Queue task (just runs immediately in local mode)."""
        result = await self.run(task_name, payload)
        return "completed"
    
    def _get_task_func(self, task_name: str):
        """Get task function by name."""
        from app import tasks
        return getattr(tasks, task_name)
```

### Implementation: Distributed (Celery)

**File: `app/runners/celery_runner.py`**

```python
from celery import Celery
import os

celery_app = Celery(
    'agent',
    broker=os.getenv("REDIS_URL", "redis://localhost:6379"),
    backend=os.getenv("REDIS_URL", "redis://localhost:6379")
)

class DistributedTaskRunner(TaskRunner):
    """Run tasks on distributed worker pool."""
    
    async def run(self, task_name: str, payload: Dict) -> Dict:
        """Execute task immediately on worker."""
        task = celery_app.send_task(task_name, args=[payload])
        result = task.get(timeout=30)
        return result
    
    async def queue(self, task_name: str, payload: Dict) -> str:
        """Queue task for later execution."""
        task = celery_app.send_task(task_name, args=[payload])
        return task.id
```

### Factory

**File: `app/factory.py`** (add this)

```python
def get_task_runner() -> TaskRunner:
    """Create task runner based on config."""
    
    if os.getenv("WORKERS_ENABLED") == "true":
        return DistributedTaskRunner()
    else:
        return LocalTaskRunner()

# Usage
runner = get_task_runner()
result = await runner.run("analyze_document", {"doc_id": "123"})
```

### Switching Execution Models

```bash
# Local (MVP)
export WORKERS_ENABLED=false
# Tasks run in same process

# Distributed (Production)
export WORKERS_ENABLED=true
# Tasks run on separate workers
# No agent code changes!
```

---

## 🛠️ The Tooling Factory (Local ↔ MCP)

### Interface

**File: `app/interfaces/tool_adapter.py`**

```python
from abc import ABC, abstractmethod
from typing import Any

class ToolAdapter(ABC):
    """All tools must implement this interface."""
    
    @abstractmethod
    async def execute(self, command: str, args: Dict) -> Any:
        """Execute tool command."""
        pass
```

### Implementation: Local (Fast)

**File: `app/adapters/local_tools.py`**

```python
class LocalFilesystemTool(ToolAdapter):
    """Direct filesystem access (fast, less safe)."""
    
    async def execute(self, command: str, args: Dict) -> Any:
        if command == "read":
            with open(args["path"]) as f:
                return f.read()
        elif command == "write":
            with open(args["path"], "w") as f:
                f.write(args["content"])
            return "written"
```

### Implementation: MCP Bridge (Safe)

**File: `app/adapters/mcp_tools.py`**

```python
import httpx

class MCPFilesystemTool(ToolAdapter):
    """MCP-based filesystem access (safe, slower)."""
    
    def __init__(self, mcp_url: str):
        self.mcp_url = mcp_url
        self.client = httpx.AsyncClient()
    
    async def execute(self, command: str, args: Dict) -> Any:
        """Send request to MCP server."""
        response = await self.client.post(
            f"{self.mcp_url}/execute",
            json={"command": command, "args": args}
        )
        return response.json()
```

### Factory

**File: `app/factory.py`** (add this)

```python
def get_filesystem_tool() -> ToolAdapter:
    """Create filesystem tool based on config."""
    
    if os.getenv("USE_MCP") == "true":
        return MCPFilesystemTool(os.getenv("MCP_FILESYSTEM_URL"))
    else:
        return LocalFilesystemTool()

# Usage
tool = get_filesystem_tool()
content = await tool.execute("read", {"path": "/data/config.json"})
```

---

## 💡 Real-World Examples

### Example 1: Research Agent with Pluggable Database

```python
class ResearchAgent:
    def __init__(self):
        self.db = get_database_adapter()  # Agnostic!
        self.llm = get_llm_provider()     # Agnostic!
    
    async def research(self, topic: str):
        # Save research to DB (any type)
        doc_id = await self.db.save("research", {
            "topic": topic,
            "timestamp": datetime.now()
        })
        
        # Generate summary (any LLM)
        summary = await self.llm.generate(
            f"Summarize research on {topic}"
        )
        
        # Update DB
        await self.db.update("research", doc_id, {
            "summary": summary
        })
        
        return summary

# Usage: Same code, different backends
# Dev: PostgreSQL + Claude Haiku ($0.01 per call)
# Prod: Qdrant + Claude Opus ($0.50 per call)
# Code unchanged!
```

### Example 2: Data Processing Pipeline

```python
class DataProcessor:
    def __init__(self):
        self.runner = get_task_runner()  # Local or distributed
    
    async def process_batch(self, documents: List[str]):
        # Queue multiple tasks
        task_ids = []
        for doc in documents:
            task_id = await self.runner.queue("process_doc", {
                "doc": doc
            })
            task_ids.append(task_id)
        
        return task_ids

# Usage: Same code scales differently
# Local (MVP): Tasks run in-process, simple
# Distributed (Prod): Tasks run on 10+ workers, auto-scaling
# Code unchanged!
```

---

## 🎯 Best Practices

### Rule 1: Use Dependency Injection

```python
# GOOD: Pass adapters to constructor
class Agent:
    def __init__(self, db: DatabaseAdapter, llm: LLMProvider):
        self.db = db
        self.llm = llm

agent = Agent(
    db=get_database_adapter(),
    llm=get_llm_provider()
)

# BAD: Hardcode factory calls inside class
class Agent:
    def __init__(self):
        self.db = get_database_adapter()  # Can't test easily
```

---

### Rule 2: Test with Mocks

```python
# Create mock adapter for testing
class MockDatabase(DatabaseAdapter):
    def __init__(self):
        self.data = {}
    
    async def search(self, query: str) -> List[Dict]:
        return [{"mock": True}]

# Test agent with mock
mock_db = MockDatabase()
agent = Agent(db=mock_db)
assert len(await agent.search("test")) == 1
```

---

### Rule 3: Don't Over-Engineer

```python
# Good: Factory when you'll actually swap
✅ Database (SQL vs Vector)
✅ LLM (Claude vs OpenAI)
✅ Workers (local vs distributed)
✅ Tools (local vs MCP)

# Bad: Factory for one-off choices
❌ Authentication (just pick one per project)
❌ Email provider (not swapping mid-project)
❌ Payment processor (locked at launch)
```

---

## 🤖 For Claude Code/Cursor (Explicit Instructions)

When Robert needs to swap components:

### Always Ask

1. **"What are you swapping?"**
   - Database? LLM? Workers? Tools?
   - Reference appropriate factory above

2. **"Have you tested with mock?"**
   - Create mock adapter
   - Test agent with mock first
   - Then test with real adapter

3. **"Is the interface consistent?"**
   - Both adapters implement same ABC?
   - Same method signatures?
   - Will agent code work unchanged?

### When Robert Says "Switch to Qdrant"

```
You should:
1. Verify QdrantAdapter implements DatabaseAdapter interface
2. Add QdrantAdapter case to get_database_adapter() factory
3. Create mock QdrantAdapter for testing
4. Test Agent with mock first
5. Update environment variables
6. Test with real Qdrant

Result: Agent code unchanged ✅
```

---

## 🐛 Troubleshooting

### Problem: "Factory returns wrong adapter"

**Symptom:** Agent uses wrong database/LLM

**Fix:**
```bash
# Check environment variable
echo $DATABASE_TYPE
echo $LLM_PROVIDER

# Verify factory is using correct env var
python -c "from app.factory import get_database_adapter; print(type(get_database_adapter()))"

# Restart application
```

---

### Problem: "Adapter throws 'method not implemented'"

**Symptom:** `NotImplementedError` when calling adapter method

**Fix:**
1. Check that adapter implements full interface (all abstract methods)
2. Review ABC definition vs adapter implementation
3. Make sure method signatures match exactly

---

### Problem: "Mock adapter not working in tests"

**Symptom:** Tests pass with mock, fail with real adapter

**Fix:**
1. Mock might be too simple
2. Add more realistic data to mock
3. Test with both mock AND real adapter:

```python
@pytest.mark.parametrize("db", [
    MockDatabase(),
    PostgresAdapter(test_url)
])
def test_agent_search(db):
    agent = Agent(db=db)
    assert len(agent.search("test")) > 0
```

---

## ✅ Checklist

### Before Using Factories

- [ ] Interface (ABC) defined clearly
- [ ] All implementations complete
- [ ] Factory function created
- [ ] Agent/app uses factory, not hardcoded adapters
- [ ] Mocks created for testing
- [ ] Tests pass with mock AND real adapter
- [ ] Environment variables documented

### For Each New Adapter

- [ ] Implements full interface
- [ ] No missing methods
- [ ] Method signatures match
- [ ] Error handling consistent
- [ ] Tests included
- [ ] Factory case added

---

## 📌 File Meta

**Version:** 1.2.0  
**Released:** February 14, 2026  
**Status:** Production Ready ✅  
**Last Reviewed:** February 14, 2026  
**Part of:** 8-doc AI Agent Framework (Part 9/9)  

**This is the FINAL file!**

**Principles Applied:** See [MERGE_PRINCIPLES_LOCKED.md](./MERGE_PRINCIPLES_LOCKED.md)

---

## 🏁 THE FRAMEWORK IS COMPLETE

You now have a complete, integrated AI Agent Framework:

**Tier 1: Foundation**
- 00_START_HERE.md (Entry point)
- 01_QUICK_REFERENCE.md (Quick lookup)
- 02_COMPLETE_GUIDE.md (Deep methodology)
- 04_AI_ASSISTANT_INTEGRATION.md (Claude setup)
- 05_CLAUDE_CONTEXT_AND_BUGS.md (Project memory)

**Tier 2: Implementation**
- 03_DEPENDENCY_MANAGEMENT.md (Python setup)
- 06_INFRASTRUCTURE_AS_CODE.md (Deployment)

**Tier 3: Advanced**
- 07_CONFIGURATION_CONTROL.md (Scaling)
- 08_AGNOSTIC_FACTORIES.md (This file - Component swapping)

**Total:** 9 files, 160K+ words, production-ready ✅

---

**Master factories, and you'll never be locked into one implementation again.**  
**Build once, swap components forever.** 🏭