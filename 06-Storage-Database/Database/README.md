# 📘 Module 6 – Databases in GCP  
## 🧪 Workshop: Create a Cloud SQL (PostgreSQL) Database and Connect from Cloud Shell

This workshop guides students on how to create a **Cloud SQL (PostgreSQL)** instance, connect securely using **Cloud Shell**, create tables, run queries, and clean up resources.

---

## ✅ 1. Prerequisites

- A GCP project with **billing enabled**  
- Required roles:
  - **Cloud SQL Admin**
  - **Compute Network Viewer** (due to Auth Proxy)
- **Cloud Shell** enabled

---

## 🏗️ 2. Create the Cloud SQL Instance (GUI)

1. Navigate to:  
   **SQL → Create Instance**
2. Choose **PostgreSQL**
3. Configure:
   - **Name:** `demo-sql-user`
   - **Password:** secure, chosen by student
   - **Region:** e.g., `us-central1`
   - **Zone:** e.g., `us-central1-a`
4. Machine Configuration:
   - **Tier:** Sandbox or minimum for demo
   - **Storage:** 10 GB
5. Enable:
   - **Automatic backups**
6. Create instance  
⏱️ Time: *2–4 minutes*

---

## 🗄️ 3. Create Database and User

### 📌 3.1 Create database

Path:  
**Instance → Databases → Create Database**

- **Name:** `demo_db`

### 👤 3.2 Create user

Path:  
**Instance → Users → Create User**

- **Username:** `demo_user`
- **Password:** chosen by student

---

## 🔐 4. Connect Using Cloud SQL Auth Proxy (Cloud Shell)

Open **Cloud Shell** and run:

```bash
gcloud sql connect demo-sql-user --user=postgres
```

This automatically runs the **Cloud SQL Auth Proxy**, avoiding public IP exposure.

Output example:

```
Connected to Cloud SQL instance.
psql (14.x)
Type "help" for help.

demo-sql-user=#
```

---

## 📝 5. Create Table and Run Queries

### 5.1 Switch database

```sql
\c demo_db;
```

### 5.2 Create table

```sql
CREATE TABLE alumnos (
  id SERIAL PRIMARY KEY,
  nombre VARCHAR(50),
  email VARCHAR(100),
  creado TIMESTAMP DEFAULT NOW()
);
```

### 5.3 Insert data

```sql
INSERT INTO alumnos (nombre, email)
VALUES 
  ('user', 'user@example.com'),
  ('Juan Perez', 'jperez@example.com'),
  ('Maria Lopez', 'mlopez@example.com');
```

### 5.4 Query data

```sql
SELECT * FROM alumnos;
```

---

## 👤 6. Connect Using Non‑Admin User

```bash
gcloud sql connect demo-sql-user --user=demo_user
```

Then:

```sql
\c demo_db;
SELECT * FROM alumnos;
```

---

## 🧹 7. Cleanup (avoid charges)

```bash
gcloud sql instances delete demo-sql-user
```

---

