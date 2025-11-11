# DEMO: Exploring the Google Cloud Console and Cloud Shell

## 🎯 Objective
Become familiar with the graphical interface (Console) and the command-line tool (Cloud Shell) in Google Cloud Platform (GCP) to manage cloud resources.

---

## 🖥️ 1. Accessing the Google Cloud Console

1. Log in to [Google Cloud Console](https://console.cloud.google.com).
2. Check the **active project** at the top bar (Project ID).
3. Explore the main sections from the left-hand menu:
   - **Compute Engine:** Manage virtual machines.
   - **Cloud Storage:** Manage object storage.
   - **IAM & Admin:** Control users and permissions.
   - **Billing:** View costs and budgets.
   - **APIs & Services:** Enable APIs and manage credentials.

4. In the main navigation panel, you can:
   - Search for services using the top search bar.
   - Monitor the global status of your resources.
   - Check alerts and system notifications.

### 🧩 Suggested Exercise
- Enable the **Compute Engine API**.
- Create a **basic VM instance** from the console (choose region, machine type, and OS).

---

## 💻 2. Using Cloud Shell

Cloud Shell provides a preconfigured Linux environment with GCP management tools ready to use.

1. Click the **>_** icon in the upper-right corner to open Cloud Shell.
2. Wait for the Debian environment to initialize.
3. Verify your authenticated account and active project:
   ```bash
   gcloud auth list
   gcloud config list project
   ```
4. List available instances:
   ```bash
   gcloud compute instances list
   ```
5. Create a working folder and a test file:
   ```bash
   mkdir demo-cloudshell && cd demo-cloudshell
   echo "Hello from Cloud Shell" > greeting.txt
   cat greeting.txt
   ```
6. Open the **integrated editor** (similar to VS Code):
   ```bash
   cloudshell open-editor
   ```

### 🧩 Suggested Exercise
- Create a startup script to automate VM creation.
- Connect via SSH to the created instance:
   ```bash
   gcloud compute ssh my-vm-demo --zone=us-central1-a
   ```

---

## ⚖️ 3. Key Differences Between the Web Console and Cloud Shell

| Feature | Web Console | Cloud Shell |
|----------|--------------|-------------|
| **Interface** | Graphical (GUI) | Command-line (CLI) |
| **Ease of Use** | Ideal for beginners | Ideal for technical users |
| **Automation** | Limited | High (scripts, gcloud, Terraform) |
| **Requirements** | Web browser only | Browser with Cloud Shell enabled |
| **API Access** | Manual (via GUI) | Direct via `gcloud` commands |

---

## ✅ Final Outcome
Students will have explored both the **Web Console** and **Cloud Shell**, understanding how to use both tools to manage projects, resources, and automate basic tasks within Google Cloud.

---

> 🧠 **Tip:** A good practice is to alternate between the Console and Cloud Shell depending on the task. Use the Console for visualization and Cloud Shell for automation.