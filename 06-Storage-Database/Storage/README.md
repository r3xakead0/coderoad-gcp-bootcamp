# Module 6 – Storage in GCP  
## Workshop: Hosting a Static Website using Cloud Storage

This workshop guides students through creating and publishing a static website (HTML/CSS/JS) using **Google Cloud Storage**, enabling public access through the bucket's website endpoint.

---

## 🗂️ 1. Website Structure

Create a local folder named `web-static/` with the following structure:

```
web-static/
├── index.html
├── 404.html
└── styles.css   (optional)
```

### Sample – index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>My static page on GCP</title>
</head>
<body>
  <h1>Hello from Cloud Storage!</h1>
  <p>This is a static website served directly from a bucket.</p>
</body>
</html>
```

### Sample – 404.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Page not found</title>
</head>
<body>
  <h1>404 - Page not found</h1>
  <p>Sorry, the requested resource does not exist.</p>
</body>
</html>
```

---

## 🪣 2. Create the Bucket

**Recommendations:**
- Bucket name: `module6-static-web-<your-name>`
- Region: `us-central1` or the closest nearby.

---

## ⬆️ 3. Upload the Files

### Using the Console:
1. Open the bucket.
2. Click **Upload folder** and select `web-static/`.

### Using CLI:
```bash
gsutil cp ./web-static/* gs://module6-static-web-user/
```

---

## 🌐 4. Configure the Static Website

```bash
gsutil web set -m index.html -e 404.html gs://module6-static-web-user/
```

**Parameters:**
- `-m index.html` → main page  
- `-e 404.html` → custom error page  

---

## 🔓  Make the Website Public

```bash
gsutil iam ch allUsers:objectViewer gs://module6-static-web-user/
```

> ⚠️ Suitable for demos.  
> Production environments should use an **HTTPS Load Balancer** and tighter IAM controls.

---

## 🔍 6. Test the Website

From the console:
1. Open the bucket.
2. Select **index.html**.
3. Copy the **public URL**.

**Example URL:**
```
https://storage.googleapis.com/module6-static-web-user/index.html
```
