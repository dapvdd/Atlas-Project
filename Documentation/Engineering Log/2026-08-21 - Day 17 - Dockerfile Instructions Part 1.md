# Atlas Project
## Engineering Log

---

# Day 17 — Dockerfile Instructions

**Date:** 21 August 2026

**Phase:** Docker Images

**Milestone:** Understanding Dockerfile Runtime Instructions

---

## Objective

Melanjutkan pembelajaran Dockerfile dengan mempelajari instruction `RUN`, `CMD`, dan `ENTRYPOINT` serta memahami perbedaan waktu eksekusi dan perilaku masing-masing instruction.

Fokus pembelajaran:

- Memahami instruction `RUN`.
- Memahami instruction `CMD`.
- Memahami instruction `ENTRYPOINT`.
- Memahami perbedaan build-time dan runtime.
- Memahami bagaimana `CMD` digunakan sebagai default command.
- Memahami bagaimana `ENTRYPOINT` menerima argument dari `docker run`.
- Menghubungkan Dockerfile instructions dengan proses Image Build dan Container Runtime.

---

## Activities

### 1. Dockerfile `RUN`

Eksperimen pertama dilakukan untuk memahami instruction `RUN`.

Dockerfile dibuat menggunakan base image Alpine:

```dockerfile
FROM alpine:latest

RUN echo "Atlas Day 17" > /atlas.txt
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-run:v1 -f Dockerfile.run .
```

Setelah Image berhasil dibuat, keberadaan file yang dihasilkan oleh `RUN` diverifikasi dengan menjalankan:

```bash
sudo docker run --rm atlas-run:v1 cat /atlas.txt
```

Hasil:

```text
Atlas Day 17
```

Eksperimen ini menunjukkan bahwa instruction `RUN` dieksekusi ketika Docker Image sedang dibangun.

Perubahan filesystem yang dihasilkan oleh `RUN` kemudian menjadi bagian dari Image.

Secara konseptual:

```text
Dockerfile
    │
    ↓
docker build
    │
    ↓
RUN
    │
    ↓
Image Layer
    │
    ↓
Custom Image
```

### 2. Dockerfile `CMD`

Eksperimen kedua dilakukan untuk memahami instruction `CMD`.

Dockerfile dibuat dengan:

```dockerfile
FROM alpine:latest

CMD ["echo", "Atlas Day 17 - CMD"]
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-cmd:v1 -f Dockerfile.cmd .
```

Container dijalankan menggunakan:

```bash
sudo docker run --rm atlas-cmd:v1
```

Container menghasilkan output:

```text
Atlas Day 17 - CMD
```

Eksperimen ini menunjukkan bahwa `CMD` tidak dijalankan ketika Image dibuild.

`CMD` digunakan sebagai default command yang dijalankan ketika Container dimulai.

Secara konseptual:

```text
docker build
    │
    ↓
Image
    │
    ↓
docker run
    │
    ↓
CMD executed
```

### 3. Dockerfile `ENTRYPOINT`

Eksperimen ketiga dilakukan untuk memahami instruction `ENTRYPOINT`.

Dockerfile dibuat dengan:

```dockerfile
FROM alpine:latest

ENTRYPOINT ["echo", "Atlas Day 17 - ENTRYPOINT"]
```

Image kemudian dibuild menggunakan:

```bash
sudo docker build -t atlas-entrypoint:v1 -f Dockerfile.entrypoint .
```

Container pertama dijalankan tanpa argument tambahan:

```bash
sudo docker run --rm atlas-entrypoint:v1
```

Kemudian Container dijalankan dengan argument tambahan:

```bash
sudo docker run --rm atlas-entrypoint:v1 "Hello Docker"
```

Hasil:

```text
Atlas Day 17 - ENTRYPOINT Hello Docker
```

Eksperimen ini menunjukkan bahwa argument yang diberikan melalui `docker run` diteruskan sebagai argument tambahan kepada `ENTRYPOINT`.

Secara konseptual:

```text
ENTRYPOINT
    +
docker run argument
    ↓
ENTRYPOINT + argument
    ↓
Command execution
```

### 4. Comparing `RUN`, `CMD`, and `ENTRYPOINT`

Ketiga instruction kemudian dibandingkan berdasarkan waktu dan cara eksekusinya.

```text
RUN
│
└── Build Time
    └── Membuat perubahan pada Image


CMD
│
└── Container Runtime
    └── Default command


ENTRYPOINT
│
└── Container Runtime
    └── Main executable
        + argument dari docker run
```

Perbandingan ini memperjelas bahwa `RUN` memiliki lifecycle yang berbeda dari `CMD` dan `ENTRYPOINT`.

---

## Key Concepts

### RUN

`RUN` digunakan untuk menjalankan command selama proses Image build.

Contoh:

```dockerfile
RUN echo "Atlas Day 17" > /atlas.txt
```

Hasil dari instruction tersebut menjadi bagian dari filesystem Image.

### CMD

`CMD` menentukan default command yang dijalankan ketika Container dimulai.

Contoh:

```dockerfile
CMD ["echo", "Atlas Day 17 - CMD"]
```

Command tersebut dijalankan ketika Container dibuat dan dimulai dari Image tersebut.

### ENTRYPOINT

`ENTRYPOINT` digunakan untuk menentukan executable utama yang dijalankan oleh Container.

Contoh:

```dockerfile
ENTRYPOINT ["echo", "Atlas Day 17 - ENTRYPOINT"]
```

Argument tambahan dari `docker run` diteruskan kepada `ENTRYPOINT`.

Contoh:

```bash
sudo docker run --rm atlas-entrypoint:v1 "Hello Docker"
```

Menghasilkan:

```text
Atlas Day 17 - ENTRYPOINT Hello Docker
```

---

## Lessons Learned

- `RUN` dijalankan pada saat Image build.
- Perubahan yang dihasilkan `RUN` menjadi bagian dari Image.
- `CMD` dijalankan ketika Container dimulai.
- `CMD` berfungsi sebagai default command.
- `ENTRYPOINT` menentukan executable utama Container.
- Argument dari `docker run` dapat diteruskan kepada `ENTRYPOINT`.
- `RUN` memiliki konteks build-time.
- `CMD` dan `ENTRYPOINT` memiliki konteks container runtime.
- Dockerfile instructions memiliki fungsi dan lifecycle yang berbeda.

Secara keseluruhan:

```text
Dockerfile
     │
     ├── FROM
     │     └── Base Image
     │
     ├── RUN
     │     └── Build Time
     │
     ├── COPY
     │     └── Add Files
     │
     ├── CMD
     │     └── Default Runtime Command
     │
     └── ENTRYPOINT
           └── Main Runtime Executable
```

---

## Problems

Terdapat kesalahan ketika instruction Dockerfile `RUN` dan `CMD` sempat dijalankan langsung pada Bash.

Contoh:

```bash
RUN echo "Atlas Day 17" > /atlas.txt
```

menghasilkan:

```text
-bash: /atlas.txt: Permission denied
```

Sedangkan:

```bash
CMD ["echo", "Atlas Day 17 - CMD"]
```

menghasilkan:

```text
CMD: command not found
```

Hal tersebut terjadi karena `RUN` dan `CMD` merupakan instruction Dockerfile, bukan command Linux yang dijalankan langsung pada terminal.

Kesalahan tersebut kemudian dikoreksi dengan menempatkan instruction pada masing-masing Dockerfile sebelum melakukan proses build.

Eksperimen kemudian berhasil diselesaikan.

---

## Documentation

### Screenshot

**01 - Dockerfile RUN.png**

Screenshot berisi Dockerfile dan/atau proses build yang menunjukkan:

```dockerfile
FROM alpine:latest

RUN echo "Atlas Day 17" > /atlas.txt
```

Fokus dokumentasi pada penggunaan instruction `RUN`.

---

**02 - RUN Image Test.png**

Screenshot berisi pengujian Image hasil build:

```bash
sudo docker run --rm atlas-run:v1 cat /atlas.txt
```

dengan output:

```text
Atlas Day 17
```

Screenshot ini menjadi bukti bahwa perubahan yang dibuat oleh `RUN` tersedia di dalam Image.

---

**03 - CMD Test.png**

Screenshot berisi proses menjalankan:

```bash
sudo docker run --rm atlas-cmd:v1
```

dengan output:

```text
Atlas Day 17 - CMD
```

Screenshot ini menjadi bukti bahwa `CMD` dijalankan ketika Container dimulai.

---

**04 - ENTRYPOINT Test.png**

Screenshot berisi pengujian:

```bash
sudo docker run --rm atlas-entrypoint:v1 "Hello Docker"
```

dengan output:

```text
Atlas Day 17 - ENTRYPOINT Hello Docker
```

Screenshot ini menjadi bukti bahwa argument dari `docker run` diteruskan kepada `ENTRYPOINT`.

---

## Next Session

### Day 18 — Docker Images Part 4

Pembelajaran Docker Images akan dilanjutkan dengan instruction Dockerfile lainnya dan pemahaman lebih lanjut mengenai proses Image Build.

Materi dapat mencakup:

- `WORKDIR`
- `ENV`
- `EXPOSE`
- `docker history`
- Image Layers
- Build Cache
- Image optimization

Materi akan dipelajari secara bertahap melalui eksperimen pada Atlas Project.

---

## Status

✅ Dockerfile `RUN`

✅ Build-time Execution

✅ Dockerfile `CMD`

✅ Runtime Default Command

✅ Dockerfile `ENTRYPOINT`

✅ Runtime Arguments

✅ RUN vs CMD vs ENTRYPOINT

⏳ Advanced Dockerfile Instructions

⏳ Image History

⏳ Build Cache

⏳ Image Optimization