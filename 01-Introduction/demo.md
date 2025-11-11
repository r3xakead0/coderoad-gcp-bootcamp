# DEMO: Exploring the Google Cloud Console and Cloud Shell

## 🎯 Objective
Gain hands-on experience with the graphical interface (Console) and the command-line tool (Cloud Shell) in Google Cloud Platform (GCP) to manage and deploy basic cloud resources.

---

## 🖥️ 1. Accessing the Google Cloud Console

### Step-by-Step Walkthrough

1. Log in to [Google Cloud Console](https://console.cloud.google.com).
2. Confirm your **active project** at the top navigation bar (check the Project ID).
3. From the left-hand menu, explore the main sections:
   - **Compute Engine:** Manage virtual machines.
   - **Cloud Storage:** Manage object storage.
   - **IAM & Admin:** Configure users, roles, and permissions.
   - **Billing:** View budgets and spending reports.
   - **APIs & Services:** Enable APIs and create credentials.

4. In the main panel, you can:
   - Use the **search bar** to quickly find services.
   - Monitor **resource health** and current activity.
   - Review **notifications** and recent operations.

### 🧩 Basic Demonstrations

#### 🔹 Create a Virtual Machine from the Console
1. Go to **Compute Engine > VM Instances**.
2. Click **Create Instance**.
3. Select a region (e.g., *us-central1*) and zone (e.g., *us-central1-a*).
4. Choose a machine type (e.g., *e2-micro*).
5. Select an image such as **Debian 12**.
6. Click **Create**.

👉 **Result:** A new VM will appear in your instance list, ready to connect via SSH from the browser.

#### 🔹 Create a Cloud Storage Bucket
1. Navigate to **Cloud Storage > Buckets**.
2. Click **Create Bucket**.
3. Give it a globally unique name, e.g., `my-demo-bucket-123`.
4. Select a region (e.g., *us-central1*).
5. Keep access control as **Uniform** and click **Create**.

👉 **Result:** Your bucket is ready to store files, images, or website content.

#### 🔹 Enable an API
1. Go to **APIs & Services > Library**.
2. Search for **Cloud Vision API**.
3. Click **Enable**.

👉 **Result:** The API becomes available for use via Cloud Shell or client libraries.

---

## 💻 2. Using Cloud Shell

Cloud Shell provides a secure, preconfigured Debian Linux environment with the GCP SDK (`gcloud`, `gsutil`, and `bq`) already installed.

### Step-by-Step Walkthrough

1. Open Cloud Shell by clicking the **>_** icon in the top-right corner.
2. Wait for initialization.
3. Verify authentication and project configuration:
   ```bash
   gcloud auth list
   gcloud config list project
   ```
4. List current VM instances:
   ```bash
   gcloud compute instances list
   ```
5. View all buckets:
   ```bash
   gsutil ls
   ```

### 🧩 Basic Demonstrations

#### 🔹 Create a Virtual Machine via Command Line
```bash
gcloud compute instances create my-shell-vm \
  --zone=us-central1-a \
  --machine-type=e2-micro \
  --image-family=debian-12 \
  --image-project=debian-cloud
```
👉 **Result:** A new VM will be created with minimal configuration.

#### 🔹 Upload a File to Cloud Storage
```bash
echo "Hello Cloud!" > hello.txt
gsutil cp hello.txt gs://my-demo-bucket-123
```
👉 **Result:** The file is uploaded to your bucket and can be viewed in the Console.

#### 🔹 Describe a Resource
```bash
gcloud compute instances describe my-shell-vm --zone=us-central1-a
```
👉 **Result:** Returns details such as IP address, machine type, and disk configuration.

#### 🔹 Connect via SSH
```bash
gcloud compute ssh my-shell-vm --zone=us-central1-a
```
👉 **Result:** Opens an SSH terminal session directly from your browser.

---

## ⚖️ 3. Key Differences Between the Web Console and Cloud Shell

| Feature | Web Console | Cloud Shell |
|----------|--------------|-------------|
| **Interface** | Graphical (GUI) | Command-line (CLI) |
| **Ease of Use** | Ideal for beginners | Ideal for technical users |
| **Automation** | Limited | High (scripts, gcloud, Terraform) |
| **Requirements** | Web browser only | Browser with Cloud Shell enabled |
| **API Access** | Manual (GUI) | Direct via `gcloud` commands |

---

## ✅ Final Outcome
By completing these demonstrations, students will:
- Understand how to navigate and use the **Google Cloud Console**.
- Deploy resources like **VMs** and **storage buckets**.
- Use **Cloud Shell** for basic automation and resource management.
- Recognize when to choose between GUI or CLI workflows.

---

> 🧠 **Tip:** Start with the Console for visualization, then move to Cloud Shell as you gain confidence to automate your workflow using commands and scripts.

