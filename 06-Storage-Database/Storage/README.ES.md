# Módulo 6 – Storage en GCP  
## Taller: Publicar una Página Web Estática en Cloud Storage

Este taller guía a los alumnos para crear y publicar una página web estática simple (HTML/CSS/JS) usando **Google Cloud Storage**, habilitando acceso público mediante el endpoint del bucket.

---

## 🗂️ 1. Estructura de la Web

Crea una carpeta local llamada `web-estatica/` con la siguiente estructura:

```
web-estatica/
├── index.html
├── 404.html
└── styles.css   (opcional)
```

### Ejemplo – index.html
```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Mi página estática en GCP</title>
</head>
<body>
  <h1>¡Hola desde Cloud Storage!</h1>
  <p>Esta es una página web estática servida directamente desde un bucket.</p>
</body>
</html>
```

### Ejemplo – 404.html
```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Página no encontrada</title>
</head>
<body>
  <h1>404 - Página no encontrada</h1>
  <p>Lo sentimos, el recurso solicitado no existe.</p>
</body>
</html>
```

---

## 🪣 2. Crear el Bucket

**Recomendación:**
- Nombre del bucket: `modulo6-web-estatica-<tu-nombre>`
- Región recomendada: `us-central1` u otra cercana.

---

## ⬆️ 3. Subir los Archivos

### Desde la consola:
1. Entra al bucket.
2. Haz clic en **Upload folder** y selecciona `web-estatica/`.

### Desde CLI:
```bash
gsutil cp ./web-estatica/* gs://modulo6-web-estatica-user/
```

---

## 🌐 4. Configurar el Bucket como Sitio Web Estático

```bash
gsutil web set -m index.html -e 404.html gs://modulo6-web-estatica-user/
```

**Parámetros:**
- `-m index.html` → página principal  
- `-e 404.html` → página de error personalizada  

---

## 🔓  Hacer Pública la Web

```bash
gsutil iam ch allUsers:objectViewer gs://modulo6-web-estatica-user/
```

> ⚠️ Para demos está bien.  
> En casos productivos se recomienda usar un **Load Balancer HTTPS** y políticas más estrictas.

---

## 🔍 6. Probar la Web

En la consola:
1. Entra al bucket.
2. Selecciona **index.html**.
3. Copia la **URL pública**.

**Ejemplo de URL:**
```
https://storage.googleapis.com/modulo6-web-estatica-user/index.html
```
