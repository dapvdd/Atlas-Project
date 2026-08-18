# Atlas Project
## Engineering Log

---

# Day 16 — Docker Images Part 2

**Date:** 18 August 2026

**Phase:** Docker Images

**Milestone:** Building and Running a Custom Docker Image

---

## Objective

Melanjutkan pembelajaran Docker Images dengan mempelajari proses pembuatan Docker Image custom menggunakan Dockerfile.

Fokus pembelajaran:

- Memahami Dockerfile sebagai instruksi untuk membangun Image.
- Memahami instruction `FROM`.
- Memahami instruction `COPY`.
- Memahami build context.
- Membuat Docker Image custom menggunakan `docker build`.
- Memahami Image Tag pada custom Image.
- Menjalankan Container berdasarkan Image hasil build.
- Memverifikasi aplikasi yang berjalan dari custom Image.

---

## Activities

### 1. Creating Dockerfile

Direktori khusus untuk eksperimen Dockerfile dibuat menggunakan:

```bash
mkdir -p ~/atlas-dockerfile
cd ~/atlas-dockerfile
```

Dockerfile dibuat dengan konfigurasi:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html
```

Instruction `FROM` digunakan untuk menentukan `nginx:alpine` sebagai base image.

Instruction `COPY` digunakan untuk menyalin file `index.html` dari build context ke dalam filesystem Image pada lokasi `/usr/share/nginx/html/index.html`.

File `index.html` dibuat dengan isi:

```html
<h1>Atlas Project - Day 16</h1>
<p>Custom Docker Image.</p>
```

Struktur direktori:

```text
atlas-dockerfile/
├── Dockerfile
└── index.html
```

### 2. Dockerfile and Base Image

Dockerfile menggunakan `nginx:alpine` yang sebelumnya telah dipelajari pada Day 15 sebagai base image.

Secara konseptual:

```text
nginx:alpine
      │
      │ FROM
      ↓
  Dockerfile
      │
      │ COPY index.html
      ↓
 Custom Image
```

Dengan menggunakan `FROM`, Image baru tidak perlu dibuat dari sistem operasi kosong, tetapi dapat menggunakan Image yang sudah tersedia sebagai dasar.

### 3. Building Custom Docker Image

Docker Image kemudian dibuat menggunakan:

```bash
sudo docker build -t atlas-web:v1 .
```

Titik (`.`) menunjukkan current directory digunakan sebagai build context.

Proses build menghasilkan:

```text
Step 1/2 : FROM nginx:alpine
 ---> 4a73073bd557
Step 2/2 : COPY index.html /usr/share/nginx/html/index.html
 ---> 078f8d250877
Successfully built 078f8d250877
Successfully tagged atlas-web:v1
```

Docker menggunakan `nginx:alpine` sebagai base Image dan kemudian menambahkan layer dari instruction `COPY`.

Image hasil build diberi nama dan tag:

```text
atlas-web:v1
```

### 4. Verifying Custom Image

Image hasil build diverifikasi menggunakan:

```bash
sudo docker images | grep atlas-web
```

Hasil:

```text
atlas-web:v1         078f8d250877       92.7MB         26.1MB
```

Hasil tersebut menunjukkan bahwa `atlas-web:v1` telah berhasil tersedia pada Docker Host.

Image memiliki Image ID:

```text
078f8d250877
```

### 5. Running Container from Custom Image

Custom Image digunakan untuk membuat Container:

```bash
sudo docker run -d --name atlas-custom-web -p 8082:80 atlas-web:v1
```

Container yang dibuat memiliki nama:

```text
atlas-custom-web
```

Port host `8082` dipetakan ke port `80` pada Container.

Secara konseptual:

```text
Host :8082
    │
    ↓
Container :80
    │
    ↓
Nginx
    │
    ↓
index.html
```

### 6. Testing Custom Application

Aplikasi yang berjalan di dalam Container diuji menggunakan:

```bash
curl http://localhost:8082
```

Hasil:

```html
<h1>Atlas Project - Day 16</h1>
<p>Custom Docker Image.</p>
```

Hasil tersebut membuktikan bahwa `index.html` berhasil dimasukkan ke dalam Image melalui instruction `COPY` dan kemudian disajikan oleh Nginx di dalam Container.

---

## Key Concepts

### Dockerfile

Dockerfile merupakan file yang berisi instruksi untuk membangun Docker Image.

Pada eksperimen ini digunakan:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html
```

### FROM

`FROM` menentukan base Image yang digunakan dalam proses build.

Pada Day 16:

```text
FROM nginx:alpine
```

`nginx:alpine` digunakan sebagai dasar untuk custom Image Atlas.

### COPY

`COPY` digunakan untuk menyalin file dari build context ke dalam filesystem Image.

Pada eksperimen ini:

```text
index.html
     │
     │ COPY
     ↓
/usr/share/nginx/html/index.html
```

### Build Context

Build context merupakan directory yang dikirim ke Docker ketika proses `docker build` dilakukan.

Pada eksperimen ini current directory digunakan sebagai build context:

```bash
sudo docker build -t atlas-web:v1 .
```

Sehingga Docker dapat mengakses `Dockerfile` dan `index.html` yang berada di directory tersebut.

### Custom Docker Image

Docker Image custom berhasil dibuat dengan:

```text
atlas-web:v1
```

Image tersebut kemudian dapat digunakan sebagai dasar untuk menjalankan Container.

---

## Lessons Learned

- Dockerfile digunakan sebagai instruksi untuk membangun Docker Image.
- `FROM` digunakan untuk menentukan base Image.
- `COPY` digunakan untuk memasukkan file ke dalam Image.
- Docker Image dapat dibuat menggunakan `docker build`.
- Build context menentukan file yang dapat digunakan selama proses build.
- Custom Image dapat diberikan nama dan tag menggunakan `-t`.
- Image hasil build dapat digunakan untuk menjalankan Container.
- Proses pembuatan aplikasi Containerized dapat dipahami sebagai:

```text
Dockerfile
    ↓
docker build
    ↓
Custom Image
    ↓
docker run
    ↓
Container
    ↓
Application
```

- Day 16 menghubungkan konsep Image Layers pada Day 15 dengan proses pembuatan Image custom.

---

## Problems

Terdapat warning mengenai penggunaan legacy builder ketika melakukan proses build:

```text
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
```

Namun proses build tetap berhasil:

```text
Successfully built 078f8d250877
Successfully tagged atlas-web:v1
```

Warning tersebut tidak menghambat proses pembuatan maupun penggunaan custom Image pada sesi ini.

Pembahasan mengenai BuildKit/buildx belum dilakukan dan dapat dipelajari pada sesi berikutnya jika diperlukan.

---

## Documentation

### Screenshots

**01 - Dockerfile.png**

Screenshot berisi Dockerfile yang digunakan untuk membuat custom Image.

Fokus screenshot pada:

```text
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

**02 - Docker Image Build.png**

Screenshot berisi proses:

```bash
sudo docker build -t atlas-web:v1 .
```

dan hasil build hingga:

```text
Successfully built 078f8d250877
Successfully tagged atlas-web:v1
```

**03 - Custom Image Running.png**

Screenshot berisi proses menjalankan custom Image:

```bash
sudo docker run -d --name atlas-custom-web -p 8082:80 atlas-web:v1
```

serta pengujian:

```bash
curl http://localhost:8082
```

dengan hasil:

```html
<h1>Atlas Project - Day 16</h1>
<p>Custom Docker Image.</p>
```

---

## Next Session

### Day 17 — Docker Images Part 3

Materi Docker Images akan dilanjutkan dengan instruction Dockerfile dan konsep image building yang lebih lanjut.

Materi yang dapat dipelajari:

- `RUN`
- `CMD`
- `ENTRYPOINT`
- `WORKDIR`
- `EXPOSE`
- Image Layers dan Build Cache
- `docker history`
- Image optimization

Materi akan dipelajari secara bertahap sesuai eksperimen Atlas Project.

---

## Status

✅ Dockerfile

✅ `FROM`

✅ `COPY`

✅ Build Context

✅ Docker Build

✅ Custom Docker Image

✅ Image Tagging

✅ Run Container from Custom Image

✅ Application Verification

⏳ Advanced Dockerfile Instructions

⏳ Image Optimization