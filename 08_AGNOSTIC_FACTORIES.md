# 🏭 Agnostic Factories - The "Tech Radar" Integration Pipeline

**Version:** 1.4.0 | **Updated:** March 1, 2026 | **Part:** 9/9  
**Status:** Production Ready ✅  
**Purpose:** How to safely integrate new tools discovered by the Tech Radar without rewriting your agent's core logic.

---

## 📍 Purpose

This file is the **bridge** between the "Tech Radar" (File 04) and your Codebase.

**The Problem:** The Tech Radar finds a cool new library (e.g., `pypdf-turbo`), but if you `import pypdf_turbo` directly in your agent, you are "vendor-locked" to that specific library. If it breaks next week, you have to rewrite the agent.

**The Solution:**
1.  **Discover** via Tech Radar.
2.  **Encapsulate** via Adapter.
3.  **Inject** via Factory.

**Core Pattern:** "One interface, many implementations. Pick at runtime."

---

## 🗺️ Quick Navigation

- [The Tech Radar Pipeline (New)](#-the-tech-radar-pipeline-mandatory)
- [The Database Factory](#-the-database-factory)
- [The LLM Factory](#-the-llm-factory)
- [The Orchestrator Factory](#-the-orchestrator-factory)
- [Best Practices](#-best-practices)

---

## 📡 The Tech Radar Pipeline (Mandatory)

This is the standard workflow for adding **ANY** new dependency to the project.

### Step 1: Discovery (Tech Radar)
The AI assistant runs the `tech-radar-skill` and determines that `pypdf-turbo` is faster than `pypdf2`.

### Step 2: Define the Interface (Contract)
Create a generic interface that defines *what* we need, not *how* it's done.

```python
# app/interfaces/document_loader.py
from abc import ABC, abstractmethod

class DocumentLoader(ABC):
    @abstractmethod
    def load(self, file_path: str) -> str:
        """Loads text from a file. Must return strict string or raise Error."""
        pass

Step 3: Create the Adapter (Encapsulation)

Wrap the specific library. If the library changes, only this file changes.
Python

# app/adapters/pdf_turbo_adapter.py
from app.interfaces.document_loader import DocumentLoader
import pypdf_turbo  # <--- The ONLY place this is imported

class PyPDFTurboAdapter(DocumentLoader):
    def load(self, file_path: str) -> str:
        try:
            return pypdf_turbo.extract_text(file_path)
        except Exception as e:
            # Standardize error handling (No Happy Paths)
            raise ValueError(f"PDF Turbo failed: {e}")

Step 4: The Factory (Injection)

Update the factory to allow swapping between the old way and the new way via config.
Python

# app/factories/loader_factory.py
import os
from app.interfaces.document_loader import DocumentLoader

def get_document_loader() -> DocumentLoader:
    mode = os.getenv("PDF_ENGINE", "standard").lower()
    
    if mode == "turbo":
        from app.adapters.pdf_turbo_adapter import PyPDFTurboAdapter
        return PyPDFTurboAdapter()
    
    from app.adapters.standard_pdf_adapter import StandardPDFAdapter
    return StandardPDFAdapter()

🏗️ The Database Factory

Never hardcode psycopg2 or qdrant_client in your business logic.
Python

def get_database_adapter() -> DatabaseAdapter:
    """Creates the database connection based on environment settings."""
    db_type = os.getenv("DATABASE_TYPE", "postgresql").lower()
    
    if db_type == "qdrant":
        return QdrantAdapter() # Vector DB for RAG
    
    # Default to Postgres for state management
    return PostgresAdapter()   

🤖 The LLM Factory

Switch providers (Anthropic, OpenAI, Google) by changing a single line in scale.yaml.
Python

def get_llm_provider(model_type: str = "primary") -> LLMProvider:
    """Fetches the provider configured in scale.yaml."""
    provider = os.getenv("LLM_PROVIDER", "anthropic").lower()
    
    if provider == "openai":
        return OpenAILLM()
    elif provider == "google":
        return GoogleGeminiLLM()
    elif provider == "local":
        return OllamaLLM() # Great for free local testing
        
    return ClaudeLLM()

🎼 The Orchestrator Factory

Multi-agent orchestration frameworks change constantly. This factory isolates your business logic from the routing engine.
Python

def get_orchestrator() -> AgentOrchestrator:
    """Creates the router based on scale.yaml/env settings."""
    engine = os.getenv("ORCHESTRATION_ENGINE", "langgraph").lower()
    
    if engine == "langgraph":
        from app.adapters.langgraph_orchestrator import LangGraphOrchestrator
        return LangGraphOrchestrator()
    elif engine == "crewai":
        from app.adapters.crew_orchestrator import CrewOrchestrator
        return CrewOrchestrator()
    
    # Default: Simple linear execution for debugging
    from app.adapters.simple_orchestrator import SimpleAsyncOrchestrator
    return SimpleAsyncOrchestrator()

💡 Best Practices

    Lazy Imports: Notice the imports are inside the if statements. This prevents loading heavy libraries (like langgraph or torch) if you aren't using them.

    Standardized State: Every orchestrator adapter must accept and return a standard State dictionary.

    The "Local" Fallback: Always provide a simple or local implementation that runs without API keys or complex dependencies. This allows for rapid unit testing.

📌 File Meta

Version: 1.4.0

Released: March 1, 2026

Status: Production Ready ✅

Part of: 9-Part AI Agent Framework

🏁 THE FRAMEWORK IS COMPLETE.