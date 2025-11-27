# 📘 Módulo 6 – Base de Datos en GCP  
## 🧪 Taller: Crear una Base de Datos en Cloud SQL (PostgreSQL) y Conectarse desde Cloud Shell

Este taller enseña paso a paso cómo crear una instancia de **Cloud SQL (PostgreSQL)**, conectarse de forma segura desde **Cloud Shell**, crear tablas, ejecutar consultas y limpiar los recursos.

---

## ✅ 1. Prerrequisitos

- Proyecto de GCP con **billing habilitado**  
- Roles necesarios:
  - **Cloud SQL Admin**
  - **Compute Network Viewer** (por uso de Auth Proxy)
- Tener **Cloud Shell** habilitado

---

## 🏗️ 2. Crear la Instancia de Cloud SQL (GUI)

1. Ir a:  
   **SQL → Create Instance**
2. Seleccionar **PostgreSQL**
3. Configurar:
   - **Name:** `demo-sql-user`
   - **Password:** segura, definida por el alumno  
   - **Region:** ej. `us-central1`
   - **Zone:** ej. `us-central1-a`
4. Configuración de máquina:
   - **Tier:** Sandbox o la mínima para demo  
   - **Storage:** 10 GB (por defecto)
5. Activar:
   - **Backups automáticos**
6. Crear instancia  
⏱️ Tiempo aproximado: *2–4 minutos*

---

## 🗄️ 3. Crear Base de Datos y Usuario

### 📌 3.1 Crear base de datos

Ruta:  
**Instance → Databases → Create Database**

- **Name:** `demo_db`

### 👤 3.2 Crear usuario

Ruta:  
**Instance → Users → Create User**

- **Username:** `demo_user`
- **Password:** definida por el alumno

---

## 🔐 4. Conectarse con Cloud SQL Auth Proxy (Cloud Shell)

Abrir **Cloud Shell** y ejecutar:

```bash
gcloud sql connect demo-sql-user --user=postgres
```

La CLI ejecuta el **Cloud SQL Auth Proxy automáticamente**, sin exponer IP pública.

Se solicitará la contraseña del usuario `postgres`.

Salida esperada:

```
Connected to Cloud SQL instance.
psql (14.x)
Type "help" for help.

demo-sql-user=#
```

---

## 📝 5. Crear Tabla y Ejecutar Consultas

### 5.1 Cambiar a la base de datos

```sql
\c demo_db;
```

### 5.2 Crear tabla

```sql
CREATE TABLE alumnos (
  id SERIAL PRIMARY KEY,
  nombre VARCHAR(50),
  email VARCHAR(100),
  creado TIMESTAMP DEFAULT NOW()
);
```

### 5.3 Insertar datos

```sql
INSERT INTO alumnos (nombre, email)
VALUES 
  ('user', 'user@example.com'),
  ('Juan Perez', 'jperez@example.com'),
  ('Maria Lopez', 'mlopez@example.com');
```

### 5.4 Consultar datos

```sql
SELECT * FROM alumnos;
```

Ejemplo:

```
 id |   nombre     |       email           |        creado
----+--------------+-----------------------+----------------------------
 1  | user        | user@example.com     | 2025-11-25 20:01:23
 2  | Juan Perez   | jperez@example.com    | 2025-11-25 20:01:23
 3  | Maria Lopez  | mlopez@example.com    | 2025-11-25 20:01:23
```

---

## 👤 6. Conexión con Usuario No-Admin

```bash
gcloud sql connect demo-sql-user --user=demo_user
```

Luego:

```sql
\c demo_db;
SELECT * FROM alumnos;
```

---

## 🧹 7. Limpieza (evitar cobros)

```bash
gcloud sql instances delete demo-sql-user
```

---

