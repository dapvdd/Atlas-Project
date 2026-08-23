# Atlas Project
## Engineering Log

---

# Day 18 — Dockerfile Instructions Part 2

**Date:** 23 August 2026

**Phase:** Docker Images

**Milestone:** Understanding WORKDIR, ENV & EXPOSE

---

## Objective

Melanjutkan pembelajaran Dockerfile dengan mempelajari instruction `WORKDIR`, `ENV`, dan `EXPOSE`.

Fokus pembelajaran:

- Memahami instruction `WORKDIR`.
- Memahami working directory di dalam Container.
- Memahami instruction `ENV`.
- Memahami environment variable pada Container.
- Memahami instruction `EXPOSE`.
- Memahami perbedaan `EXPOSE` dengan port publishing.
- Menghubungkan `EXPOSE` dengan konsep port mapping yang telah dipelajari sebelumnya.

---

## Activities

### 1. Dockerfile `WORKDIR`

Eksperimen pertama dilakukan untuk memahami instruction `WORKDIR`.

Dockerfile dibuat dengan konfigurasi:

```dockerfile
FROM alpine:latest

WORKDIR /app

RUN echo "Atlas Day 18" > test.txt
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-workdir:v1 -f Dockerfile.workdir .
```

File yang dibuat oleh instruction `RUN` kemudian diverifikasi menggunakan:

```bash
sudo docker run --rm atlas-workdir:v1 cat /app/test.txt
```

Hasil:

```text
Atlas Day 18
```

Working directory Container juga diperiksa menggunakan:

```bash
sudo docker run --rm atlas-workdir:v1 pwd
```

Hasil:

```text
/app
```

Eksperimen menunjukkan bahwa `WORKDIR` menentukan working directory default yang digunakan oleh instruction berikutnya dan proses Container.

Secara konseptual:

```text
WORKDIR /app
      ↓
Working Directory
      ↓
/app
```

Karena `RUN` dijalankan setelah `WORKDIR`, file `test.txt` dibuat pada:

```text
/app/test.txt
```

---

### 2. Dockerfile `ENV`

Eksperimen kedua dilakukan untuk memahami environment variable menggunakan instruction `ENV`.

Dockerfile dibuat dengan konfigurasi:

```dockerfile
FROM alpine:latest

ENV ATLAS_ENV=development

CMD ["sh", "-c", "echo Atlas environment: $ATLAS_ENV"]
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-env:v1 -f Dockerfile.env .
```

Container dijalankan menggunakan:

```bash
sudo docker run --rm atlas-env:v1
```

Hasil:

```text
Atlas environment: development
```

Eksperimen menunjukkan bahwa `ENV` dapat digunakan untuk menetapkan environment variable yang tersedia di dalam Container.

Secara konseptual:

```text
ENV ATLAS_ENV=development
          ↓
Environment Variable
          ↓
$ATLAS_ENV
          ↓
development
```

---

### 3. Dockerfile `EXPOSE`

Eksperimen ketiga dilakukan untuk memahami instruction `EXPOSE`.

Dockerfile dibuat dengan konfigurasi:

```dockerfile
FROM nginx:alpine

EXPOSE 80
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-expose:v1 -f Dockerfile.expose .
```

Build berhasil menghasilkan:

```text
Successfully built a870c3de8506
Successfully tagged atlas-expose:v1
```

Container kemudian dijalankan tanpa port publishing:

```bash
sudo docker run -d --name atlas-expose-test atlas-expose:v1
```

Status Container diperiksa menggunakan:

```bash
sudo docker ps --filter "name=atlas-expose-test"
```

Hasil menunjukkan:

```text
PORTS
80/tcp
```

Hal ini menunjukkan bahwa Container mendeklarasikan penggunaan port 80.

### 4. Verifying `EXPOSE` vs Port Publishing

Service Nginx berhasil diakses dari dalam Container menggunakan:

```bash
sudo docker exec atlas-expose-test wget -qO- http://localhost:80
```

Nginx memberikan response HTML.

Namun ketika mencoba mengakses:

```bash
curl http://localhost:80
```

akses dari host gagal karena port 80 Container belum dipublish ke host.

Hal ini menunjukkan bahwa:

```text
EXPOSE 80
```

tidak sama dengan:

```text
-p 8083:80
```

`EXPOSE` hanya mendeklarasikan port yang digunakan oleh aplikasi di dalam Container, sedangkan `-p` melakukan port publishing dari host menuju Container.

Untuk membuktikannya, Container kemudian dijalankan dengan port publishing:

```bash
sudo docker run -d --name atlas-expose-published -p 8083:80 atlas-expose:v1
```

Port mapping menjadi:

```text
Host :8083
    ↓
Container :80
    ↓
Nginx
```

Service kemudian berhasil diuji menggunakan:

```bash
curl http://localhost:8083
```

Hal ini membuktikan bahwa port publishing harus dilakukan secara eksplisit apabila service di dalam Container ingin diakses melalui port host.

---

## Key Concepts

### WORKDIR

`WORKDIR` digunakan untuk menentukan working directory di dalam Container.

Contoh:

```dockerfile
WORKDIR /app
```

Setelah instruction tersebut, working directory menjadi:

```text
/app
```

Instruction berikutnya dapat bekerja relatif terhadap directory tersebut.

### ENV

`ENV` digunakan untuk menetapkan environment variable.

Contoh:

```dockerfile
ENV ATLAS_ENV=development
```

Variable tersebut kemudian dapat digunakan oleh proses di dalam Container.

### EXPOSE

`EXPOSE` digunakan untuk mendeklarasikan port yang digunakan oleh aplikasi di dalam Container.

Contoh:

```dockerfile
EXPOSE 80
```

Namun `EXPOSE` tidak otomatis melakukan port publishing ke host.

### EXPOSE vs Port Publishing

Perbedaannya:

```text
EXPOSE 80
    ↓
Deklarasi port Container
```

sedangkan:

```text
-p 8083:80
    ↓
Host :8083
    ↓
Container :80
```

Dengan demikian:

```text
EXPOSE ≠ Port Publishing
```

---

## Lessons Learned

- `WORKDIR` menentukan working directory default di dalam Container.
- File atau command setelah `WORKDIR` dapat bekerja relatif terhadap working directory tersebut.
- `ENV` digunakan untuk membuat environment variable.
- Environment variable dapat digunakan oleh proses yang berjalan di dalam Container.
- `EXPOSE` mendeklarasikan port yang digunakan oleh aplikasi di dalam Container.
- `EXPOSE` tidak secara otomatis membuka port Container ke host.
- Port publishing dilakukan menggunakan `-p`.
- Port Container dapat diakses dari dalam Container tanpa harus dipublish ke host.
- Konsep `EXPOSE` memperkuat pemahaman mengenai perbedaan antara port internal Container dan port host.

Secara keseluruhan:

```text
WORKDIR
    ↓
Menentukan lokasi kerja

ENV
    ↓
Menentukan environment variable

EXPOSE
    ↓
Mendeklarasikan port aplikasi

-p
    ↓
Melakukan port publishing
```

---

## Problems

Tidak terdapat masalah teknis yang menghambat eksperimen.

Pada pengujian `EXPOSE`, akses menggunakan:

```bash
curl http://localhost:80
```

dari host gagal karena port 80 Container belum dipublish.

Hal tersebut kemudian diverifikasi dengan mengakses service dari dalam Container dan membuktikan bahwa Nginx tetap berjalan pada port 80.

Setelah port publishing menggunakan `-p 8083:80`, service berhasil diakses dari host.

Eksperimen tersebut digunakan untuk memperjelas perbedaan antara `EXPOSE` dan port publishing.

---

## Documentation

### Screenshots

**01 - WORKDIR.png**

Screenshot berisi Dockerfile dan hasil pengujian `WORKDIR`.

Fokus dokumentasi pada:

```dockerfile
WORKDIR /app
```

dan hasil:

```text
/app
```

---

**02 - ENV.png**

Screenshot berisi Dockerfile `ENV` dan hasil Container:

```text
Atlas environment: development
```

Fokus dokumentasi pada environment variable `ATLAS_ENV`.

---

**03 - EXPOSE.png**

Screenshot berisi hasil build dan status Container yang menunjukkan:

```text
PORTS
80/tcp
```

Fokus dokumentasi pada penggunaan:

```dockerfile
EXPOSE 80
```

---

**04 - EXPOSE Port Publishing.png**

Screenshot berisi Container yang dijalankan menggunakan:

```bash
sudo docker run -d --name atlas-expose-published -p 8083:80 atlas-expose:v1
```

dan hasil:

```bash
curl http://localhost:8083
```

Screenshot ini menjadi bukti perbedaan antara `EXPOSE` dan port publishing.

---

## Next Session

### Day 19 — Docker Images Part 3

Pembelajaran Docker Images akan dilanjutkan dengan memahami struktur Image secara lebih mendalam.

Materi dapat mencakup:

- `docker history`
- Image Layers
- Build Cache
- Hubungan Dockerfile instruction dengan Image Layers
- Layer reuse
- Dasar image optimization

Materi akan dipelajari melalui eksperimen langsung pada Atlas Project.

---

## Status

✅ WORKDIR

✅ Working Directory

✅ ENV

✅ Environment Variable

✅ EXPOSE

✅ Container Internal Port

✅ EXPOSE vs Port Publishing

⏳ Docker Image History

⏳ Image Layers & Build Cache

⏳ Image Optimization