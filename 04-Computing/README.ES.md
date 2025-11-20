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

### 1.2 Imágenes (Images)

Una imagen es el estado base de un disco, normalmente SO + paquetes mínimos.

Tipos:
- Imágenes públicas: Debian, Ubuntu, Rocky, Windows, Container-Optimized OS.
- Imágenes personalizadas: creadas a partir de una VM ya configurada.
- Family Images: siempre apuntan a la última versión estable.

### 1.3 Snapshots

Un snapshot es una copia incremental del disco.
Sirve para respaldo o migraciones rápidas.

Ejemplo (CLI):

```bash
gcloud compute disks snapshot my-disk \
  --snapshot-names=my-disk-snap \
  --zone=us-central1-a
```

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

### 2.2 Autoescalado

Tipos de autoescalado:
- CPU utilization
- Load balancing capacity
- Cloud Monitoring métricas personalizadas
- Predictive autoscaling

Ejemplo (CLI):

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
- Pub/Sub
- HTTP
- Storage events
- Firebase
- Cron jobs

Características:
- Escalamiento automático.
- Pago por invocación.
- Ideal para micro-tareas.

Ejemplo (CLI):

```bash
gcloud functions deploy helloWorld \
  --runtime=nodejs20 \
  --trigger-http \
  --allow-unauthenticated
```

### 3.2 Cloud Run

Plataforma serverless basada en contenedores.
Soporta cualquier lenguaje siempre que esté dockerizado.

Ventajas:
- Autoscala desde 0 a N.
- Seguridad integrada.
- Fácil integración con Cloud Build.

Ejemplo (CLI):

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

### 4.2 Desplegar una app en Cloud Function

Código simple (Node.js)

index.js

```javascript
exports.helloWorld = (req, res) => {
  res.send("Hola desde Cloud Functions 🚀");
};
```

package.json

```json
{
  "name": "demo-function",
  "version": "1.0.0",
  "main": "index.js"
}
```

Desplegar

```bash
gcloud functions deploy helloWorld \
  --runtime=nodejs20 \
  --trigger-http \
  --allow-unauthenticated \
  --region=us-central1
```

### 4.3 Desplegar una app en Cloud Run

Código simple (Node.js)

index.js

```javascript
const express = require('express')
const app = express()
app.get('/', (req, res) => res.send('Hola desde Cloud Run!'))
app.listen(process.env.PORT || 8080)
```

Desplegar

```bash
gcloud run deploy demo-cloudrun \
  --source=. \
  --allow-unauthenticated \
  --region=us-central1
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