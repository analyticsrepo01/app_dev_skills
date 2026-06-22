---
name: postgres
description: PostgreSQL enterprise database management skill covering schema design, migration flows with Alembic, connection pool optimization, parameterized query writing, and slow query tuning.
---

# 🐘 PostgreSQL Database Management Skill

Use this skill when you need to interface with a **PostgreSQL** database, manage database migrations, write optimized SQL, configure connection pools, or perform performance tuning.

---

## 1. Connection Pool Optimization

For enterprise applications, use **SQLAlchemy** (or AsyncPG) with robust connection pool settings to avoid exhaustion errors.

### 💡 Gold Standard SQLAlchemy Configuration:
```python
from sqlalchemy import create_engine
import os

DATABASE_URL = os.getenv("DATABASE_URL", "postgresql://user:pass@localhost:5432/dbname")

# Advanced production pool parameters
engine = create_engine(
    DATABASE_URL,
    pool_size=20,           # Maximum active connections maintained in pool
    max_overflow=10,        # Temporary connections allowed beyond pool_size
    pool_timeout=30,        # Max seconds to wait for a connection from the pool
    pool_recycle=1800,      # Recycle connections after 30 minutes to clear memory
    pool_pre_ping=True      # Check connection liveness before executing queries
)
```

---

## 2. Safe Parameterized Queries

To completely eliminate **SQL Injection** risks, never construct SQL strings using raw python f-strings or string concatenation.

### ❌ Dangerous:
```python
# VULNERABLE TO SQL INJECTION!
cursor.execute(f"SELECT * FROM users WHERE username = '{user_input}'")
```

### ✅ Safe Parameterized Method:
```python
# Safe parameterized query
query = "SELECT * FROM users WHERE username = :username"
result = session.execute(text(query), {"username": user_input})
```

---

## 3. Database Migration Management (Alembic)

Database schema evolutions should always be managed using migrations to guarantee reproducibility.

```
Local Code Change 
      |
      v
alembic revision --autogenerate -m "add_user_roles_table"
      |
      v
Review generated file in migrations/versions/
      |
      v
alembic upgrade head
```

### 🚀 Key Migration Commands
*   **Generate an Auto-migration:**
    ```bash
    alembic revision --autogenerate -m "add_audit_trail_table"
    ```
*   **Apply Migrations to Current Head:**
    ```bash
    alembic upgrade head
    ```
*   **Downgrade a Single Migration:**
    ```bash
    alembic downgrade -1
    ```

---

## 4. Query Performance Tuning

To optimize slow-running queries:
1.  Prefix the queries with `EXPLAIN ANALYZE` to get the database execution plan.
2.  Add indexes on heavily filtered columns (e.g. `WHERE user_id = X` or `JOIN on tenant_id`).
3.  Ensure foreign keys are fully indexed.

### 🔍 Index Creation Syntax:
```sql
CREATE INDEX CONCURRENTLY idx_users_tenant_created 
ON users(tenant_id, created_at DESC);
```
*(Using `CONCURRENTLY` prevents locking the database during index builds in production environments).*
