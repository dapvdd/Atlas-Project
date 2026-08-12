# Atlas Project
## Engineering Log

---

# Day 10 — Docker Engine & First Container

**Date:** 08 August 2026

**Phase:** Server Administration

**Milestone:** First Containerized Web Service

---

## Objective

Mempelajari konsep dasar Docker serta melakukan deployment container pertama pada Ubuntu Server. Pada sesi ini juga dilakukan deployment Nginx sebagai web server di dalam container dan pengujian akses web melalui Management Network.

---

## Activities

### 1. Docker Environment Verification

- Mengakses **UBU-SRV-01** melalui SSH dari Windows Host.
- Memastikan Docker Engine dapat digunakan pada Ubuntu Server.
- Melakukan pengecekan container menggunakan:

```bash
sudo docker ps
```

### 2. First Container — Hello World

- Menjalankan container pertama menggunakan image `hello-world`.

```bash
sudo docker run hello-world
```

- Docker melakukan pull terhadap image `hello-world` karena image belum tersedia secara lokal.
- Container berhasil menjalankan proses dan menghasilkan status:

```text
Exited (0)
```

- Melakukan pengecekan seluruh container menggunakan:

```bash
sudo docker ps -a
```

### 3. Docker Image & Container Identification

- Melakukan pengecekan image yang tersedia menggunakan:

```bash
sudo docker images
```

- Mengidentifikasi perbedaan antara Docker Image dan Docker Container.
- `hello-world:latest` merupakan Docker Image.
- `relaxed_taussig` merupakan Container yang dibuat dari image `hello-world`.

### 4. Nginx Container Deployment

- Menjalankan Nginx menggunakan Docker:

```bash
sudo docker run -d --name atlas-nginx -p 8080:80 nginx
```

- Docker mendeteksi bahwa image `nginx:latest` belum tersedia secara lokal.
- Docker melakukan pull image Nginx dari registry.
- Setelah proses selesai, container bernama **atlas-nginx** berhasil dibuat dan dijalankan.

### 5. Container Verification

- Memeriksa container yang sedang berjalan:

```bash
sudo docker ps
```

- Container **atlas-nginx** berhasil berjalan dengan status `Up`.
- Port mapping yang digunakan:

```text
0.0.0.0:8080->80/tcp
```

### 6. HTTP Service Testing

- Melakukan pengujian web server dari Ubuntu Server:

```bash
curl http://localhost:8080
```

- Nginx berhasil memberikan HTTP response.
- Melakukan pengujian kembali dari Windows Host melalui browser:

```text
http://192.168.56.103:8080
```

- Halaman default **Welcome to nginx!** berhasil ditampilkan.

---

## Docker Structure

```text
UBU-SRV-01
│
└── Docker Engine
      │
      ├── Images
      │     ├── hello-world:latest
      │     └── nginx:latest
      │
      └── Containers
            ├── relaxed_taussig
            │     └── Exited (0)
            │
            └── atlas-nginx
                  └── Up
```

---

## Current Service Flow

```text
Windows Host
      │
      │ HTTP :8080
      ↓
192.168.56.103
      │
      ↓
UBU-SRV-01
      │
      │ Docker Port Mapping
      │ 8080 → 80/tcp
      ↓
atlas-nginx
      │
      ↓
Nginx :80
      │
      ↓
HTTP Response
```

---

## Current Topology

Topology tidak mengalami perubahan pada Day 10.

Docker dan Nginx masih berjalan di dalam **UBU-SRV-01** sehingga belum diperlukan pembuatan topology version baru.

Topology tetap menggunakan **Topology v8** dengan dua jaringan:

- Production Network → LAB-LAN
- Management Network → Host-Only Network

---

## Documentation

### Screenshots

- Docker Hello World
- Docker Containers
- Docker Images
- Atlas Nginx Running
- Nginx HTTP Test
- Nginx Web Access from Windows

### Hero Screenshot

- Windows Browser mengakses:

```text
http://192.168.56.103:8080
```

- Menampilkan halaman **Welcome to nginx!**

### Topology

- Tidak ada topology version baru.
- **Topology-v8.png** tetap digunakan.
- **Topology.drawio** tidak mengalami perubahan.

---

## Problems

- Pada saat deployment Nginx, image `nginx:latest` belum tersedia secara lokal sehingga Docker harus melakukan pull dari registry.
- Tidak terdapat kendala teknis setelah proses download image selesai.
- Pada awal pembelajaran masih terdapat kebingungan mengenai perbedaan Image, Container, Docker Engine, dan Port Mapping. Konsep berhasil dipahami setelah melakukan praktik langsung menggunakan `hello-world` dan `nginx`.

---

## Lessons Learned

- Docker Engine merupakan runtime yang mengelola Docker Image dan Container.
- Docker Image dapat dianggap sebagai template yang digunakan untuk membuat Container.
- Container merupakan instance yang dibuat berdasarkan Image.
- Satu Image dapat digunakan untuk membuat beberapa Container.
- `docker ps` menampilkan Container yang sedang berjalan.
- `docker ps -a` menampilkan seluruh Container, termasuk Container yang telah berhenti.
- Status `Exited (0)` menunjukkan bahwa Container telah menyelesaikan proses tanpa error.
- Container yang dihentikan dapat dijalankan kembali tanpa melakukan pull Image selama Image masih tersedia secara lokal.
- Port Mapping menghubungkan port pada Host dengan port pada Container.
- `0.0.0.0:8080->80/tcp` berarti port 8080 pada seluruh interface IPv4 Host dipetakan menuju port 80 TCP pada Container.
- Port 80 merupakan nomor port yang secara umum digunakan oleh HTTP, bukan nama protokol itu sendiri.
- Nginx dapat dijalankan sebagai Container tanpa melakukan instalasi langsung pada Ubuntu Host.
- Web service yang berjalan di dalam Container dapat diakses dari Windows Host melalui Management Network.
- SSH menjadi metode administrasi yang lebih praktis dibandingkan VirtualBox Console karena memudahkan scrolling, copy-paste, dan pengelolaan terminal.

---

## Next Session

- Memahami Docker Container Lifecycle lebih lanjut.
- Mengenal Docker Network.
- Mengenal Docker Volume.
- Mempelajari Docker Compose.
- Mempersiapkan deployment multi-container.

---

## Status

✅ Docker Engine Verified

✅ First Container Created

✅ `hello-world` Container Tested

✅ Nginx Image Pulled

✅ `atlas-nginx` Created

✅ `atlas-nginx` Running

✅ Port Mapping `8080 → 80/tcp`

✅ HTTP Response Verified

✅ Nginx Accessible from Windows Host

✅ Docker Image & Container Concept Understood

✅ Topology v8 Maintained

⬜ Docker Network

⬜ Docker Volume

⬜ Docker Compose

⬜ Multi-Container Deployment