---
name: cloud-sql
description: Managed Cloud SQL operations covering secure local/remote connections via Cloud SQL Auth Proxy, IAM database authentication, instance monitoring, and automated backup strategies.
---

# ☁️ Google Cloud SQL Management Skill

Use this skill when you need to configure, monitor, or connect applications securely to managed **Google Cloud SQL** database instances (PostgreSQL, MySQL, or SQL Server).

---

## 1. Secure Connections via Cloud SQL Auth Proxy

For security, production databases must not expose public IP addresses. Connect securely from local environments or containerized microservices using the **Cloud SQL Auth Proxy**.

```
Local Client App -> Connects to localhost:5432 -> Cloud SQL Auth Proxy -> Secure Tunnel -> Cloud SQL Instance
```

### 🚀 Starting the Auth Proxy Locally
Download and run the proxy in the background to forward database traffic securely:
```bash
# Download the proxy binary
curl -o cloud-sql-proxy https://dl.google.com/cloudsql/cloud_sql_proxy.linux.amd64
chmod +x cloud-sql-proxy

# Run the proxy targeting your Cloud SQL instance connection name
./cloud-sql-proxy \
  --address 127.0.0.1 \
  --port 5432 \
  "PROJECT-ID:REGION:INSTANCE-NAME"
```

---

## 2. IAM Database Authentication (Passwordless)

Eliminate static credentials by utilizing IAM database authentication, allowing users and service accounts to authenticate with their active Google Cloud identity tokens.

### 💡 Python SQLAlchemy IAM Authentication Connection Template:
Ensure `google-oauth2` credentials are in your environment:
```python
import os
import google.auth
from google.auth.transport.requests import Request
from sqlalchemy import create_engine

def get_iam_connection_string():
    # Retrieve active credentials
    credentials, project = google.auth.default(
        scopes=["https://www.googleapis.com/auth/sqlservice.admin"]
    )
    credentials.refresh(Request())
    
    # Extract the OAuth2 token to use as a temporary database password
    token = credentials.token
    
    # Instance metadata
    db_user = "iam-service-account@project.iam"  # Service Account Email without suffix
    db_name = "production_db"
    
    # Route via Cloud SQL Auth Proxy bound to localhost
    return f"postgresql://{db_user}:{token}@localhost:5432/{db_name}"

# Create engine with automatic token refresh hooks as needed
engine = create_engine(get_iam_connection_string())
```

---

## 3. High Availability and Backup Verification

Ensure database operations are robust and fully prepared for disaster recovery.

*   **High Availability (HA):** Deploy instances with Regional HA enabled. This configures a primary and standby instance in separate zones with automatic synchronous replication.
*   **Backups & Point-in-Time Recovery (PITR):** Enable automated daily backups and write transaction logs to allow restoring the database state to any specific second.

### 🔍 CLI Verification of Backups
```bash
gcloud sql backups list --instance="INSTANCE-NAME" --project="PROJECT-ID"
```
