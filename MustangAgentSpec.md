# 📄 AgentSpec.md: The Mustang Sage

## 1. Executive Summary & ROI
**Problem:** Custom sign quoting is plagued by "leaky" margins due to forgotten travel fees, missed permit costs, and local code violations that cause costly re-works.
**Solution:** A consultative, tiered-RAG AI agent system that acts as a shadow collaborator for salespeople.
**Core Hypothesis:** Automating the logistics, compliance, and "recipe" lookup will increase quote turnaround by 60% and ensure 100% permit-fee capture.
**Success Metrics:** Reduction in "Lost to Code" rejections and 15% increase in average job margin.

---

## 2. Agent Architecture
**Orchestration Choice:** **Antigravity (Mission Control)**
* **The Liaison (Wrapper):** Manages the chat interface, identifies intent, and locks the project address.
* **The Archivist (S3 Specialist):** Performs tiered searches:
    * *Primary:* Vectorized S3 Sandbox (2-year "Won" recipes, price-stripped).
    * *Secondary:* Raw S3 Main Bucket (Legacy archives).
* **The Auditor (Compliance & Logistics):** Uses Google Maps API for distance and filters the **Regulatory Sandbox** for city-specific codes and permit fees.
* **The Merchant (shopVOX Specialist):** Pulls live pricing via API and pushes the final validated draft into shopVOX.

---

## 3. Risk Score & Required Guardrails
**Risk Score:** **11 / 17** (Medium-High due to financial system write-access).
**Required Guardrails:**
* **Human-in-the-Loop (HITL):** The agent is strictly a "Drafter." All quotes must be reviewed and sent manually through shopVOX by a salesperson.
* **Geo-Lock Validation:** Every compliance check must be preceded by a verified Google Maps lat/long to prevent "Wrong City" hallucinations.
* **Margin Floor:** Automated alert if the calculated Gross Margin is below 35%.

---

## 4. Agnostic Factories & Data Models
**Factories:**
* `ShopvoxFactory`: Standardized REST API connector for product lookups and quote creation.
* `S3VectorFactory`: Manages semantic search against the price-stripped project recipes.
* `GeoLogisticsFactory`: Interfaces with Distance Matrix and GIS Portals.

**Data Models:**
* `ProjectRecipe`: `Project_Type`, `Part_List[]`, `Labor_Hours`, `Zoning_Tags`.
* `ComplianceRule`: `Jurisdiction`, `Max_Sq_Ft`, `Height_Limit`, `Permit_Fee_Schedule`.

---

## 5. Non-Functional Requirements
* **Fault Tolerance:** If the shopVOX API is unreachable, the agent falls back to Sandbox historical pricing but flags it as an "Estimate Only".
* **Container Defensiveness:** No ephemeral disk I/O; all state must be maintained in the Antigravity Agent Manager.
* **Strict UI Timeouts:** Address lookups and initial recipe suggestions must respond in < 3 seconds.

---

## 6. Maintenance & Feedback Loop
* **Audit Channel:** `#sage-ops-alerts`
* **Schedule:** Monthly review of "Human Correction Logs" (comparing AI guesses vs. final human edits) to retrain baseline labor hours.
* **HITL Reviewer:** Designated Production Manager.

---

## 7. Skills Identification (`/skills/`)
1.  **`DistanceCalculator`:** Automatic travel SKU assignment based on mileage.
2.  **`PriceScrubber`:** Lambda-driven regex to remove dollar amounts from quote PDFs before vectorization.
3.  **`CodeCiter`:** Pattern for extracting specific city-code paragraphs for display.

---

## 8. Phase 1 Implementation Steps
1.  Initialize `.agents/workflows/mustang_sage/` and register in `.build-context.md`.
2.  Deploy `PriceScrubber` Lambda to populate the **S3 Vector Sandbox**.
3.  Build `LocationTool` to handle Geo-Locking and travel-fee calculation.
4.  Configure `ShopvoxFactory` to push drafts to the shopVOX sandbox environment.