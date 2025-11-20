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
gcloud compute disks snapshot my-disk   --snapshot-names=my-disk-snap   --zone=us-central1-a
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

### 2.2 Autoscaling Example

```bash
gcloud compute instance-groups managed set-autoscaling my-mig   --max-num-replicas=5   --min-num-replicas=1   --target-cpu-utilization=0.6   --cool-down-period=90   --zone=us-central1-a
```

---

## 3. Cloud Functions and Cloud Run (Serverless)

### 3.1 Cloud Functions

Event-driven serverless functions.

Triggers:
- HTTP  
- Pub/Sub  
- Storage  
- Firebase  
- Cloud Scheduler  

Example:

```bash
gcloud functions deploy helloWorld   --runtime=nodejs20   --trigger-http   --allow-unauthenticated
```

---

### 3.2 Cloud Run

Serverless container platform.  
Supports any dockerized application.

Example:

```bash
gcloud run deploy my-app   --source=.   --region=us-central1   --allow-unauthenticated
```

---

## 🧪 4. Demo: Launching a VM and Deploying Serverless Apps

### 4.1 Launch a Simple VM

```bash
gcloud compute instances create demo-vm   --zone=us-central1-a   --machine-type=e2-micro   --image-family=debian-12   --image-project=debian-cloud   --tags=http-server   --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install -y apache2
    echo "Hello from Compute Engine" > /var/www/html/index.html
  '
```

Firewall rule:

```bash
gcloud compute firewall-rules create allow-http   --allow=tcp:80   --target-tags=http-server
```

---

### 4.2 Deploy a Cloud Function

Node.js sample:

```javascript
exports.helloWorld = (req, res) => {
  res.send("Hello from Cloud Functions 🚀");
};
```

Deploy:

```bash
gcloud functions deploy helloWorld   --runtime=nodejs20   --trigger-http   --allow-unauthenticated   --region=us-central1
```

---

### 4.3 Deploy an App on Cloud Run

```javascript
const express = require('express')
const app = express()
app.get('/', (req, res) => res.send('Hello from Cloud Run!'))
app.listen(process.env.PORT || 8080)
```

Deploy:

```bash
gcloud run deploy demo-cloudrun   --source=.   --allow-unauthenticated   --region=us-central1
```

---

# 🧹 5. Resource Cleanup (Highly Recommended)

To avoid unnecessary costs, delete all created resources:

### Delete VM
```bash
gcloud compute instances delete demo-vm   --zone=us-central1-a --quiet
```

### Delete firewall rule
```bash
gcloud compute firewall-rules delete allow-http --quiet
```

### Delete Cloud Function
```bash
gcloud functions delete helloWorld   --region=us-central1 --quiet
```

### Delete Cloud Run service
```bash
gcloud run services delete demo-cloudrun   --region=us-central1 --quiet
```

### If you created a MIG or template:
```bash
gcloud compute instance-groups managed delete my-mig --zone=us-central1-a --quiet
gcloud compute instance-templates delete my-template --quiet
```

---

This module provides a complete journey through traditional compute, autoscaling, and serverless paradigms in GCP.  
Happy learning! 🚀
