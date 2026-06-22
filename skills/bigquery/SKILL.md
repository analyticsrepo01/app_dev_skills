---
name: bigquery
description: BigQuery warehouse engineering covering table partitioning/clustering for cost optimization, programmatic queries via python SDK, streaming insertions, and external federated queries.
---

# 📊 Google BigQuery Analytical Engineering Skill

Use this skill when designing analytical data warehouses, writing optimized SQL queries on massive datasets, or programmatically interacting with **Google BigQuery**.

---

## 1. Table Optimization (Partitioning & Clustering)

To prevent querying entire tables and significantly lower compute costs, always structure analytical tables with partitioning and clustering keys.

```
                    Raw Table Data (Unstructured)
                                  |
                                  v
         [ Partitioning ] by Date (e.g. created_at)
                    /             |             \
             2026-06-20       2026-06-21     2026-06-22
                                  |
         [ Clustering ] by High-Cardinality Field (e.g. tenant_id)
                    /             |             \
               Tenant A        Tenant B       Tenant C
```

### 💡 Golden Partitioning Rules:
*   **Partitioning:** Physically divides table storage based on a column (usually a `TIMESTAMP`, `DATE`, or integer range). Queries targeting specific partitions read only those chunks.
*   **Clustering:** Sorts data within each partition based on the values in specified columns (typically high-cardinality fields like `user_id` or `tenant_id`).

### 🛠️ SQL Table Creation Blueprint:
```sql
CREATE TABLE `my_project.analytics.user_clicks` (
  click_id STRING,
  user_id STRING,
  tenant_id STRING,
  created_at TIMESTAMP,
  event_type STRING
)
PARTITION BY DATE(created_at)
CLUSTER BY tenant_id, event_type;
```

---

## 2. Programmatic Python SDK Integration

Interact with BigQuery securely without using hardcoded keys, leveraging the official `google-cloud-bigquery` client library.

### 🐍 Query Execution and Schema Handling:
```python
import os
from google.cloud import bigquery

def query_daily_metrics(target_date: str):
    # Initialize client (uses Application Default Credentials)
    client = bigquery.Client()
    
    # Configure parameterized query to prevent SQL Injection
    query = """
        SELECT 
            tenant_id, 
            COUNT(click_id) as click_count
        FROM `my_project.analytics.user_clicks`
        WHERE DATE(created_at) = @target_date
        GROUP BY tenant_id
        ORDER BY click_count DESC
    """
    
    job_config = bigquery.QueryJobConfig(
        query_parameters=[
            bigquery.ScalarQueryParameter("target_date", "DATE", target_date)
        ]
    )
    
    # Run the query
    query_job = client.query(query, job_config=job_config)
    results = query_job.result()  # Wait for query to complete
    
    # Process rows
    for row in results:
        print(f"Tenant: {row.tenant_id} | Total Clicks: {row.click_count}")

if __name__ == "__main__":
    query_daily_metrics("2026-06-22")
```

---

## 3. High-Performance Streaming Insertions

When injecting real-time events, use BigQuery Storage Write API (or legacy `insert_rows` for lower-volume streams) for high throughput.

```python
from google.cloud import bigquery

def stream_events_to_bigquery(rows_to_insert):
    client = bigquery.Client()
    table_id = "my_project.analytics.user_clicks"
    
    errors = client.insert_rows_json(table_id, rows_to_insert)
    if errors == []:
        print("🎉 New rows have been successfully streamed.")
    else:
        print(f"❌ Errors encountered during streaming insertion: {errors}")
```
*(Ensure incoming keys in JSON rows map precisely to BigQuery destination table schemas).*
