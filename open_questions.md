# Open Questions – Industrializing the Solution

## 1. What steps are missing to industrialize such a solution further?

The current implementation in a Databricks notebook using Python/Spark is a solid starting point, but several elements are typically missing for a production-grade (industrialized) data pipeline:

### **1. Separation of concerns**
- Move logic out of notebooks into modular, reusable components
- Introduce layered architecture (e.g., **Bronze / Silver / Gold**)
- Decouple ingestion, transformation, and serving layers

### **2. Version control and CI/CD**
- Code should be managed in Git
- Introduce:
  - Pull request reviews
  - Automated testing pipelines
  - CI/CD pipelines for deployment (e.g., GitHub Actions, Jenkins)

### **3. Testing and data quality**
- Add:
  - Unit tests for transformation logic
  - Data quality checks (schema validation, null checks, uniqueness)
  - Regression tests for transformations
- Currently, validation is likely ad hoc or minimal

### **4. Orchestration**
- Notebooks are typically manually triggered or loosely scheduled
- Need a proper orchestrator for:
  - Scheduling
  - Dependency management
  - Retry mechanisms
- Options: Databricks Workflows, Apache Airflow, Dagster

### **5. Monitoring and alerting**
- Add:
  - Pipeline monitoring
  - Failure alerts (Slack, email, etc.)
- Capture logs and metrics systematically

### **6. Lineage and observability**
- No automatic lineage tracking in notebooks
- Introduce tools or frameworks that provide:
  - Data lineage
  - Impact analysis
  - Documentation

### **7. Parameterization and configuration**
- Move hardcoded values to config:
  - Paths
  - Environment-specific configs (dev/staging/prod)
- Use parameterized jobs instead of static notebook execution

### **8. Data governance**
- Define:
  - Ownership
  - Data contracts
  - Schema evolution strategy
- Implement access control and cataloging (e.g., Unity Catalog)

---

## 2. If the solution was implemented in dbt-core, how would the overall architecture change? Would there be other cloud resources needed?

Using dbt-core would introduce a more structured transformation layer and shift responsibilities:

### **Architecture changes**
- Transformation logic moves from notebooks to **SQL-based models**
- Clear layering:
  - `staging` (raw → cleaned)
  - `intermediate`
  - `marts` (business-ready data)
- dbt manages:
  - Dependencies between models
  - Execution order
  - Data lineage

### **Execution flow**
Instead of: Notebook → Spark transformations → Output tables
You get: dbt models → compiled SQL → executed on Databricks SQL Warehouse / cluster


### **Additional cloud resources needed**
Yes, typically:

- **Orchestrator**
  - e.g., Airflow, Dugster or such
- **Compute for dbt execution**
  - Databricks SQL Warehouse or cluster
- **CI/CD system**
  - GitHub Actions, GitLab CI, Jenkins
- (Optional) **dbt Cloud**
  - If not using dbt-core + custom orchestration

---

## 3. What would implementation in dbt-core bring to the project? Upsides and downsides

### **Upsides**

#### **1. Standardization**
- Strong conventions for project structure
- Clear separation of layers (staging, marts, etc.)

#### **2. Built-in lineage**
- Automatic DAG generation of transformations
- Easier impact analysis and documentation

#### **3. Data testing**
- Native support for:
  - schema tests (not null, unique, relationships)
  - custom tests
- Improves data quality significantly

#### **4. Documentation**
- Auto-generated docs + lineage visualization

#### **5. Maintainability**
- SQL-based models are easier to reason about than complex notebook logic
- Modular and reusable transformations

#### **6. Team collaboration**
- Better for multiple engineers working on the same project
- Enforces good engineering practices

---

### **Downsides**

#### **1. Less flexibility than Python/Spark**
- Complex logic is harder to implement in SQL
- Requires workarounds or Python models (if needed)

#### **2. Learning curve**
- dbt introduces new concepts (models, refs, macros, tests)
- Requires team adoption and training

#### **3. Orchestration is external**
- dbt itself does not schedule jobs
- Requires integration with tools like:
  - Airflow
  - Dagster
  - dbt Cloud

#### **4. Not ideal for heavy compute logic**
- Use cases involving:
  - complex ML
  - heavy Python transformations  
  may still require Spark/Python notebooks

---

## 4. Estimated effort to implement the solution in dbt-core

### **Assumptions**
- Existing solution is already implemented in Databricks notebooks
- Data sources and business logic are known
- Moderate complexity (multiple transformations, some business rules)

---

### **Effort breakdown**

| Task | Estimated Effort |
|------|-----------------|
| Project setup (dbt structure, configs) | 1–2 days |
| Model migration (SQL transformation logic) | 3–7 days |
| Refactoring logic from Python → SQL | 3–5 days |
| Adding tests and validations | 2–4 days |
| Setting up orchestration | 2–3 days |
| CI/CD pipeline setup | 2–4 days |
| Documentation + lineage validation | 1–2 days |
| **Total estimate** | **~2–4 weeks** |

---

### **Factors that increase effort**
- Complex Python logic that must be rewritten
- Lack of clear existing documentation
- Large number of models/tables
- Strict data quality requirements
- Setting up full CI/CD + environments (dev/staging/prod)

---

### **Factors that reduce effort**
- Well-structured notebook already following layered architecture
- Heavy use of SQL already present
- Strong data modeling discipline in current solution

---

## **Final Takeaway**

- Notebooks in Databricks are great for prototyping and flexibility.
- dbt-core introduces:
  - structure
  - governance
  - maintainability
  - and observability

The best production setup is often:
**Databricks (compute) + dbt (transformation) + orchestrator (e.g. Airflow/Dagster)**

This combination provides both flexibility and industrial-grade robustness.