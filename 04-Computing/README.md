# Module 4  
Provisioning and Managing Compute Resources in GCP

## 🎯 Module Objectives

By the end of this class, the student will be able to:
- Understand the key components required to run workloads on GCP.  
- Deploy virtual machines in Compute Engine and manage images, snapshots, and instance templates.  
- Create managed instance groups with autoscaling.  
- Run serverless applications using Cloud Functions and Cloud Run.  
- Implement a hands-on demo by launching a VM and deploying an app on Cloud Run.  
- Develop a practical workshop building an autoscaling group exposed through a Load Balancer.

---

## 1. Compute Engine: VMs, Images, Snapshots, and Instance Templates

### 1.1 What is Compute Engine?

Compute Engine is an IaaS service that allows you to create highly customizable virtual machines on Google Cloud.  
You manage the OS, packages, monitoring, and security.

Key components:
- VM Instances  
- Machine Types (e2, n2, n2d, c3...)  
- Persistent Disks (Standard, SSD, Balanced)  
- Firewall Rules  
- Service Accounts  

---

### 1.2 Images

An image is the base state of a disk, typically OS + minimal packages.

Types:
- Public images: Debian, Ubuntu, Rocky, Windows  
- Custom images  
- Image families: point to the latest stable version  

---

### 1.3 Snapshots

A snapshot is an incremental backup of a disk.  
Useful for backups and quick migrations.

Example:

```bash
gcloud compute disks snapshot my-disk \
  --snapshot-names=my-disk-snap \
  --zone=us-central1-a
```

---

### 1.4 Instance Templates

Define the base configuration for multiple VMs:
- Machine type  
- Image  
- Disk  
- Metadata/startup scripts  
- Tags  
- Service Account  

These templates are used to create Managed Instance Groups (MIGs).

---

## 2. Autoscaling and Managed Instance Groups (MIGs)

### 2.1 What is a MIG?

A Managed Instance Group is a set of VMs created from a single instance template.

Features:
- Horizontal autoscaling  
- Health checks  
- Rolling updates  
- Load balancer integration  

---

### 2.2 Autoscaling 

Types of autoscaling:
- CPU utilization
- Load balancing capacity
- Cloud Monitoring custom metrics
- Predictive autoscaling

Features:
- Automatic scaling.
- Pay-per-call.
- Ideal for micro-tasks.

Example:

```bash
gcloud compute instance-groups managed set-autoscaling my-mig \
  --max-num-replicas=5 \
  --min-num-replicas=1 \
  --target-cpu-utilization=0.6 \
  --cool-down-period=90 \
  --zone=us-central1-a
```

---

## 3. Cloud Functions and Cloud Run (Serverless)

### 3.1 Cloud Functions

Event-driven serverless functions.

Triggers:
- Pub/Sub
- HTTP
- Storage events
- Firebase
- Cron jobs

Features:
- Automatic scaling.
- Pay-per-call.
- Ideal for micro-tasks.

Example:

```bash
gcloud functions deploy helloWorld \
  --runtime=nodejs20 \
  --trigger-http \
  --allow-unauthenticated
```

---

### 3.2 Cloud Run

Serverless container platform.  
Supports any dockerized application.

Advantages:
- Auto-scales from 0 to N.
- Built-in security.
- Easy integration with Cloud Build.

Example:

```bash
gcloud run deploy my-app \
  --source=. \
  --region=us-central1 \
  --allow-unauthenticated
```

---

## 🧪 4. Demo: Launching a VM and Deploying Serverless Apps

### 4.1 Launch a Simple VM

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

Firewall rule:

```bash
gcloud compute firewall-rules create allow-http \
  --allow=tcp:80 \
  --target-tags=http-server
```

---

### 4.2 Deploy a Cloud Function

#### Project structure

```text
demo-cloud-function/
├── index.js
└── package.json
```

#### Application code

index.js
```javascript
// Cloud Function HTTP (2nd gen)
// Endpoint: GET /  (y también POST)
exports.helloHttp = (req, res) => {
  // Name from query (?name=user), JSON body, or default value
  const name =
    req.query.name ||
    (req.body && req.body.name) ||
    "Cloud Engineer";

  const response = {
    message: `Hello, ${name}! 👋`,
    service: "Cloud Functions (2nd gen)",
    method: req.method,
    timestamp: new Date().toISOString()
  };

  // Log en Cloud Logging
  console.log("Request received:", {
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
  "description": "Cloud Functions HTTP 2nd gen full demo",
  "main": "index.js",
  "scripts": {
    "start": "node index.js" // Optional, only if you want to try Express
  },
  "dependencies": {}
}
```

#### Deploy the Cloud Function (2nd gen):

- Make sure you have the project set up:

  ```bash
  gcloud config set project TU_PROJECT_ID
  ```

- Enable necessary APIs (if you haven't done so already)

  ```bash
  gcloud services enable \
    cloudfunctions.googleapis.com \
    cloudbuild.googleapis.com \
    artifactregistry.googleapis.com
  ```

- Deploy the function:

  ```bash
  gcloud functions deploy helloHttp \
    --gen2 \
    --runtime=nodejs20 \
    --region=us-central1 \
    --trigger-http \
    --allow-unauthenticated
  ```

- Quick explanation:

  ```text
  --gen2 → uses Cloud Functions 2nd gen (based on Cloud Run + Eventarc).
  --runtime=nodejs20 → runtime version.
  --trigger-http → HTTP function.
  --allow-unauthenticated → anyone can invoke it (public API type).
  ```

#### Test:

- From the browser

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

- With the name parameter in the query

  ```text
  https://us-central1-PROJECT_ID.cloudfunctions.net/helloHttp?name=User
  ```

  ```json
  {
    "message": "Hola, User! 👋",
    ...
  }
  ```

- With curl sending JSON to the body

  ```bash
  curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"name":"Student GCP"}' \
    https://us-central1-PROJECT_ID.cloudfunctions.net/helloHttp
  ```

- View logs in Cloud Logging

  ```bash
  gcloud functions logs read helloHttp --gen2 --region=us-central1
  ```

---

### 4.3 Deploy an App on Cloud Run

#### Project structure

```text
demo-cloudrun-docker/
├── Dockerfile
├── .dockerignore
├── package.json
└── index.js
```

#### Application code

index.js
```javascript
const express = require("express");
const app = express();

// Puerto que usará Cloud Run (viene por variable de entorno)
const PORT = process.env.PORT || 8080;

app.get("/", (req, res) => {
  res.send("Hello from Cloud Run with Docker 🚀");
});

// Ejemplo de endpoint extra
app.get("/saludo/:name", (req, res) => {
  const name = req.params.name;
  res.json({
    mensaje: `Hola, ${name}!`,
    origen: "Cloud Run (Docker)",
    timestamp: new Date().toISOString()
  });
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});
```

package.json
```json
{
  "name": "demo-cloudrun-docker",
  "version": "1.0.0",
  "description": "Cloud Run demo using Docker",
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
# Base Node image
FROM node:20-slim

# Create app directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json if they exist
COPY package*.json ./

# Install only production dependencies
RUN npm install --only=production

# Copy the rest of the code
COPY . .

# Cloud Run injects a port; your app should listen on this port
ENV PORT=8080

# Expose port (for informational purposes)
EXPOSE 8080

# Startup command
CMD [ "npm", "start" ]
```

#### Build the image:

```bash
docker build -t demo-cloudrun-docker .
```

#### Upload the image to Artifact Registry:

- Enable necessary APIs (if you haven't done so already)

  ```bash
  gcloud services enable artifactregistry.googleapis.com run.googleapis.com
  ```

- Create repository in Artifact Registry

  ```bash
  gcloud artifacts repositories create docker-repo \
    --repository-format=docker \
    --location=us-central1 \
    --description="Image repository for Cloud Run"
  ```

- Authenticate to Artifact Registry using Docker

  ```bash
  gcloud auth configure-docker us-central1-docker.pkg.dev
  ```

- Tag the image

  ```bash
  PROJECT_ID=$(gcloud config get-value project)

  docker tag demo-cloudrun-docker \
    us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1
  ```

- Upload the image

  ```bash
  docker push \
    us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1
  ```

#### Deploy to Cloud Run (using Docker image):

```bash
gcloud run deploy demo-cloudrun-docker \
  --image=us-central1-docker.pkg.dev/$PROJECT_ID/docker-repo/demo-cloudrun-docker:v1 \
  --platform=managed \
  --region=us-central1 \
  --allow-unauthenticated
```

#### Test:

```bash
https://demo-cloudrun-docker-xxxxxxxxx-uc.a.run.app
```

---

# 🧹 5. Resource Cleanup

To avoid unnecessary costs, delete all created resources:

### Delete VM
```bash
gcloud compute instances delete demo-vm \  
  --zone=us-central1-a --quiet
```

### Delete firewall rule
```bash
gcloud compute firewall-rules delete allow-http --quiet
```

### Delete Cloud Function
```bash
gcloud functions delete helloWorld  \  
  --region=us-central1 --quiet
```

### Delete Cloud Run service
```bash
gcloud run services delete demo-cloudrun \  
  --region=us-central1 --quiet
```

### If you created a MIG or template:
```bash
gcloud compute instance-groups managed delete my-mig --zone=us-central1-a --quiet
gcloud compute instance-templates delete my-template --quiet
```

---

This module provides a complete journey through traditional compute, autoscaling, and serverless paradigms in GCP.  

Happy learning! 🚀