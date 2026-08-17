# Atlas Project
## Engineering Log

---

# Day 15 — Docker Images Part 1

**Date:** 17 August 2026

**Phase:** Docker Images

**Milestone:** Understanding Docker Images, Layers & Containers

---

## Objective

Memahami konsep dasar Docker Image dan hubungannya dengan Container.

Fokus pembelajaran:

- Memahami Docker Image.
- Memahami Image Tag dan Image ID.
- Memahami konfigurasi yang terdapat pada Image.
- Memahami filesystem layers pada Docker Image.
- Memahami hubungan antara Image dan Container.
- Menghubungkan Image Layers dengan writable layer yang telah dipelajari pada Day 13.

---

## Activities

### 1. Docker Image Listing

Daftar Docker Image yang tersedia pada Docker Host diperiksa menggunakan:

```bash
sudo docker images
```

Command tersebut digunakan untuk melihat image yang tersedia pada host.

Alternatif command:

```bash
sudo docker image ls
```

Keduanya menghasilkan informasi daftar Docker Image yang sama.

### 2. Docker Image Inspection

Image `nginx:alpine` yang sebelumnya digunakan pada eksperimen Docker Compose diperiksa menggunakan:

```bash
sudo docker image inspect nginx:alpine
```

Hasil inspection menunjukkan bahwa image memiliki tag:

```text
nginx:alpine
```

dan Image ID:

```text
sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2c3aeac22d2a470c1752
```

Image juga memiliki konfigurasi seperti:

```text
ExposedPorts:
80/tcp
```

```text
Entrypoint:
/docker-entrypoint.sh
```

dan:

```text
Cmd:
nginx
-g
daemon off;
```

Hal ini menunjukkan bahwa Docker Image tidak hanya berisi filesystem, tetapi juga memiliki metadata dan konfigurasi default yang digunakan ketika Container dibuat dari Image tersebut.

### 3. Image Layers

Pada hasil `docker image inspect`, bagian `RootFS` menunjukkan:

```text
RootFS:
    Type: layers
```

Image `nginx:alpine` memiliki beberapa filesystem layers.

Secara konseptual:

```text
nginx:alpine
│
├── Layer 1
├── Layer 2
├── Layer 3
├── Layer 4
├── Layer 5
├── Layer 6
├── Layer 7
└── Layer 8
```

Image yang terlihat sebagai satu kesatuan sebenarnya tersusun dari beberapa filesystem layers.

### 4. Image and Container Relationship

Docker Image dipahami sebagai template dasar yang digunakan untuk membuat Container.

Secara konseptual:

```text
Docker Image
nginx:alpine
       │
       ├── Container A
       │
       └── Container B
```

Satu Image dapat digunakan sebagai dasar untuk membuat beberapa Container.

Container kemudian memiliki writable layer sendiri di atas Image layers:

```text
Container
│
├── Image Layer 1
├── Image Layer 2
├── Image Layer 3
├── ...
└── Writable Layer
```

Image layers menjadi filesystem dasar, sedangkan writable layer digunakan untuk perubahan filesystem yang dilakukan oleh Container selama runtime.

### 5. Connection with Day 13

Konsep ini menghubungkan kembali materi Docker Storage pada Day 13.

Pada Day 13 dipelajari bahwa data yang ditulis langsung pada writable layer mengikuti lifecycle Container.

Secara konseptual:

```text
Docker Image
     │
     ├── Read-only Layers
     │
     ↓
Writable Layer
     │
     ↓
Container
```

Ketika Container dihapus, writable layer ikut dihapus.

Image dan image layers tetap tersedia karena merupakan resource yang terpisah dari Container.

---

## Key Concepts

### Docker Image

Docker Image merupakan template dasar yang digunakan untuk membuat Container.

Image memiliki:

- Filesystem layers.
- Metadata.
- Configuration.
- Default runtime configuration.

### Image Tag

Contoh:

```text
nginx:alpine
```

`nginx` merupakan nama image/repository, sedangkan `alpine` merupakan tag.

### Image ID

Image memiliki identifier berupa SHA-256, contohnya:

```text
sha256:4a73073bd557c65b759505da037898b61f1be6cbcc3c2c3aeac22d2a470c1752
```

### Image Layers

Docker Image tersusun dari beberapa filesystem layers.

```text
Image
├── Layer
├── Layer
├── Layer
└── Layer
```

### Writable Layer

Container memiliki writable layer di atas image layers.

Perubahan filesystem yang dilakukan Container ditulis pada writable layer tersebut.

---

## Lessons Learned

- Docker Image berbeda dengan Docker Container.
- Image merupakan template dasar, sedangkan Container merupakan instance yang berjalan berdasarkan Image.
- Satu Image dapat digunakan untuk membuat beberapa Container.
- Docker Image tersusun dari beberapa filesystem layers.
- Image memiliki metadata dan konfigurasi selain filesystem.
- Container memiliki writable layer sendiri di atas Image layers.
- Writable layer mengikuti lifecycle Container.
- Image layers tetap tersedia ketika Container dihapus.
- Konsep Image Layers menjelaskan lebih lanjut writable layer yang dipelajari pada Day 13.

---

## Problems

Tidak terdapat masalah teknis pada sesi ini.

Sesi sengaja dibatasi pada pemahaman Docker Image, Image Layers, dan hubungan Image dengan Container.

Pembahasan mengenai Dockerfile, custom image, dan proses image building akan dilanjutkan pada Day 16.

---

## Documentation

### Screenshot

**01 - Docker Image Inspect.png**

Screenshot berisi output:

```bash
sudo docker image inspect nginx:alpine
```

Fokus screenshot pada bagian:

```text
RepoTags
Id
Config
RootFS
Layers
```

Bagian `RootFS` dan `Layers` menjadi bukti utama bahwa Docker Image tersusun dari beberapa filesystem layers.

---

## Next Session

### Day 16 — Docker Images Part 2

- Mempelajari Dockerfile.
- Mempelajari instruction dasar seperti `FROM`, `COPY`, `RUN`, dan `CMD`.
- Membuat Docker Image custom.
- Mempelajari proses `docker build`.
- Menjalankan Container dari Image hasil build.

---

## Status

✅ Docker Image Concept

✅ Image vs Container

✅ Image Tag & Image ID

✅ Image Configuration

✅ Image Layers

✅ Writable Layer Relationship

⏳ Dockerfile

⏳ Custom Image

⏳ Docker Build