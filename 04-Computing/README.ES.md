# Módulo 4

Aprovisionamiento y Gestión de Recursos de Cómputo en GCP.

## 🎯 Objetivos del módulo

Al finalizar esta clase, el alumno será capaz de:
- Comprender los componentes clave para ejecutar cargas de trabajo en GCP.
- Implementar máquinas virtuales en Compute Engine y gestionar imágenes, snapshots y plantillas.
- Crear grupos administrados con autoescalado.
- Ejecutar aplicaciones serverless en Cloud Functions y Cloud Run.
- Implementar un demo práctico creando una VM y desplegando una app en Cloud Run.
- Desarrollar un taller práctico construyendo un autoscaling group expuesto mediante un Load Balancer.

---

## 1. Compute Engine: VMs, imágenes, snapshots y plantillas (Instance Templates)

### 1.1 ¿Qué es Compute Engine?

Servicio IaaS que permite crear máquinas virtuales altamente personalizables en Google Cloud. Tú manejas el sistema operativo, paquetes, monitoreo y seguridad.

Componentes principales:
	•	VM Instances: servidores virtuales.
	•	Machine Types: e2, n2, n2d, c3, etc.
	•	Discos persistentes: Standard, SSD, Balanced.
	•	Firewalls: basados en tags.
	•	Service Accounts: permisos para las apps.

---

### 1.2 Imágenes (Images)

Una imagen es el estado base de un disco, normalmente SO + paquetes mínimos.

Tipos:
- Imágenes públicas: Debian, Ubuntu, Rocky, Windows, Container-Optimized OS.
- Imágenes personalizadas: creadas a partir de una VM ya configurada.
- Family Images: siempre apuntan a la última versión estable.

---

### 1.3 Snapshots

Un snapshot es una copia incremental del disco.
Sirve para respaldo o migraciones rápidas.

Ejemplo:

```bash
gcloud compute disks snapshot my-disk \
  --snapshot-names=my-disk-snap \
  --zone=us-central1-a
```

---

### 1.4 Instance Templates

Una plantilla de instancia define la configuración base para múltiples VMs:
- Tipo de máquina
- Imagen
- Disco
- Metadata/startup scripts
- Tags
- Service Account

Estas plantillas permiten crear grupos manejados (MIG) con autoescalado.

---

## 2. Autoescalado y Grupos de Instancias (Managed Instance Groups)

### 2.1 ¿Qué es un MIG?

Un Managed Instance Group es un conjunto de VMs creadas desde una plantilla.

Permite:
- Autoescalado horizontal (añadir o quitar VMs).
- Health checks automáticos.
- Actualizaciones automáticas (rolling updates).
- Integración con Load Balancer.

---

### 2.2 Autoescalado

Tipos de autoescalado:
- CPU utilization
- Load balancing capacity
- Cloud Monitoring métricas personalizadas
- Predictive autoscaling

Características:
- Escalado automático.
- Pago por llamada.
- Ideal para microtareas.

Ejemplo:

```bash
gcloud compute instance-groups managed set-autoscaling my-mig \
  --max-num-replicas=5 \
  --min-num-replicas=1 \
  --target-cpu-utilization=0.6 \
  --cool-down-period=90 \
  --zone=us-central1-a
```

---

## 3. Cloud Functions y Cloud Run (Serverless)

### 3.1 Cloud Functions

Modelo FaaS. Ejecutas funciones que responden a eventos:

Desencadenantes:
- Pub/Sub
- HTTP
- Storage events
- Firebase
- Cron jobs

Características:
- Escalamiento automático.
- Pago por invocación.
- Ideal para micro-tareas.

Ejemplo:

```bash
gcloud functions deploy helloWorld \
  --runtime=nodejs20 \
  --trigger-http \
  --allow-unauthenticated
```

---

### 3.2 Cloud Run

Plataforma serverless basada en contenedores.
Soporta cualquier lenguaje siempre que esté dockerizado.

Ventajas:
- Autoscala desde 0 a N.
- Seguridad integrada.
- Fácil integración con Cloud Build.

Ejemplo:

```bash
gcloud run deploy my-app \
  --source=. \
  --region=us-central1 \
  --allow-unauthenticated
```

---

## 🧪 4. DEMO: Lanzar una VM y desplegar una app en Cloud Run

### 4.1 Lanzar una VM sencilla

```bash
gcloud compute instances create demo-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --tags=http-server \
  --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y apache2
    echo "Hola desde Compute Engine" > /var/www/html/index.html
  '
```

Crear regla firewall:

```bash
gcloud compute firewall-rules create allow-http \
  --allow=tcp:80 \
  --target-tags=http-server
```

---

### 4.2 Desplegar una app en Cloud Function

#### Estructura del proyecto

```text
demo-cloud-function/
├── index.js
└── package.json
```

#### Codigo de la aplicación

index.js
```javascript
// Cloud Function HTTP (2nd gen)
// Endpoint: GET /  (y también POST)
exports.helloHttp = (req, res) => {
  // Nombre desde query (?name=Oshin), body JSON, o valor por defecto
  const name =
    req.query.name ||
    (req.body && req.body.name) ||
    "Cloud Engineer";

  const response = {
    message: `Hola, ${name}! 👋`,
    service: "Cloud Functions (2nd gen)",
    method: req.method,
    timestamp: new Date().toISOString()
  };

  // Log en Cloud Logging
  console.log("Petición recibida:", {
    name,
    ip: req.ip,
    userAgent: req.get("User-Agent")
  });

  res.status(200).json(response);
};
```

package.json
```json
{
  "name": "demo-cloud-function",
  "version": "1.0.0",
  "description": "Demo completa de Cloud Functions HTTP 2nd gen",
  "main": "index.js",
  "scripts": {
    "start": "node index.js" // opcional, solo si quisieras probar con Express
  },
  "dependencies": {}
}
```

#### Desplegar la Cloud Function (2nd gen):

- Asegúrate de tener el proyecto configurado:

  ```bash
  gcloud config set project TU_PROJECT_ID
  ```

- Activa las APIs si no lo hiciste antes:

  ```bash
  gcloud services enable \
    cloudfunctions.googleapis.com \
    cloudbuild.googleapis.com \
    artifactregistry.googleapis.com
  ```

- Ahora despliega la función:

  ```bash
  gcloud functions deploy helloHttp \
    --gen2 \
    --runtime=nodejs20 \
    --region=us-central1 \
    --trigger-http \
    --allow-unauthenticated
  ```

- Explicación rápida:

  ```text
	--gen2 → usa Cloud Functions 2nd gen (sobre Cloud Run + Eventarc).
	--runtime=nodejs20 → versión del runtime.
	--trigger-http → función HTTP.
	--allow-unauthenticated → cualquiera puede invocarla (tipo API pública).
  ```

#### Prueba:

- Desde el navegador

  ```text
  https://us-central1-PROJECT_ID.cloudfunctions.net/helloHttp
  ```

  ```json
  {
    "message": "Hola, Cloud Engineer! 👋",
    "service": "Cloud Functions (2nd gen)",
    "method": "GET",
    "timestamp": "2025-11-21T00:00:00.000Z"
  }
  ```

- Con parámetro name en la query

  ```text
  https://us-central1-PROJECT_ID.cloudfunctions.net/helloHttp?name=User
  ```

  ```json
  {
    "message": "Hola, User! 👋",
    ...
  }
  ```

- Con curl enviando JSON en el body

  ```bash
  curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"name":"Student GCP"}' \
    https://us-central1-PROJECT_ID.cloudfunctions.net/helloHttp
  ```

- Ver logs en Cloud Logging

  ```bash
  gcloud functions logs read helloHttp --gen2 --region=us-central1
  ```

---

### 4.3 Desplegar una app en Cloud Run

#### Estructura del proyecto

```text
demo-cloudrun-docker/
├── Dockerfile
├── .dockerignore
├── package.json
└── index.js
```

#### Codigo de la aplicación

index.js
```javascript
const express = require("express");
const app = express();

// Puerto que usará Cloud Run (viene por variable de entorno)
const PORT = process.env.PORT || 8080;

app.get("/", (req, res) => {
  res.send("Hola desde Cloud Run con Docker 🚀");
});

// Ejemplo de endpoint extra
app.get("/saludo/:nombre", (req, res) => {
  const nombre = req.params.nombre;
  res.json({
    mensaje: `Hola, ${nombre}!`,
    origen: "Cloud Run (Docker)",
    timestamp: new Date().toISOString()
  });
});

app.listen(PORT, () => {
  console.log(`Servidor escuchando en puerto ${PORT}`);
});
```

package.json
```json
{
  "name": "demo-cloudrun-docker",
  "version": "1.0.0",
  "description": "Demo de Cloud Run usando Docker",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.19.0"
  }
}
```

.dockerignore
```bash
node_modules
npm-debug.log
Dockerfile
.git
.gitignore
README.md
```

Dockerfile
```bash
# Imagen base de Node
FROM node:20-slim

# Crear directorio de la app
WORKDIR /usr/src/app

# Copiar package.json y package-lock.json si existe
COPY package*.json ./

# Instalar solo dependencias de producción
RUN npm install --only=production

# Copiar el resto del código
COPY . .

# Cloud Run inyecta PORT, tu app debe escuchar en este puerto
ENV PORT=8080

# Exponer puerto (informativo)
EXPOSE 8080

# Comando de inicio
CMD [ "npm", "start" ]
```

#### Construir la imagen:

```bash
docker build -t demo-cloudrun-docker .
```

#### Subir la imagen a Artifact Registry:

- Habilitar APIs necesarias (si no lo hiciste antes)

  ```bash
  gcloud services enable artifactregistry.googleapis.com run.googleapis.com
  ```

- Crear repositorio en Artifact Registry

  ```bash
  gcloud artifacts repositories create docker-repo \
    --repository-format=docker \
    --location=us-central1 \
    --description="Repo de imágenes para Cloud Run"
  ```

- Autenticarse con Docker a Artifact Registry

  ```bash
  gcloud auth configure-docker us-central1-docker.pkg.dev
  ```

- Etiquetar la imagen

  ```bash
  PROJECT_ID=$(gcloud config get-value project)

  docker tag demo-cloudrun-docker \
    us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1
  ```

- Subir la imagen

  ```bash
  docker push \
    us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1
  ```

#### Desplegar en Cloud Run (con imagen Docker):

```bash
gcloud run deploy demo-cloudrun-docker \
  --image=us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1 \
  --platform=managed \
  --region=us-central1 \
  --allow-unauthenticated
```

#### Prueba:

```bash
https://demo-cloudrun-docker-xxxxxxxxx-uc.a.run.app
```

---

# 🧹 5. Limpieza de recursos

Para evitar costes innecesarios, elimine todos los recursos creados:

### Eliminar VM
```bash
gcloud compute instances delete demo-vm \  
  --zone=us-central1-a --quiet
```

### Eliminar regla de firewall
```bash
gcloud compute firewall-rules delete allow-http --quiet
```

### Eliminar Cloud Function
```bash
gcloud functions delete helloWorld \  
  --region=us-central1 --quiet
```

### Eliminar Cloud Run service
```bash
gcloud run services delete demo-cloudrun \  
  --region=us-central1 --quiet
```

### Si creó un archivo MIG o una plantilla:
```bash
gcloud compute instance-groups managed delete my-mig --zone=us-central1-a --quiet
gcloud compute instance-templates delete my-template --quiet
```

---

Este módulo ofrece un recorrido completo por los paradigmas de computación tradicional, escalado automático y sin servidor en GCP.

¡Que disfrutes aprendiendo! 🚀