# Atlas Project
## Engineering Log

---

# Day 13 — Docker Storage & Persistence

**Date:** 13 August 2026

**Phase:** Docker Storage

**Milestone:** Container Filesystem, Named Volume & Bind Mount

---

## Objective

Mempelajari bagaimana Docker menangani storage dan persistence pada container.

Eksperimen difokuskan pada perbedaan antara data yang disimpan langsung pada writable layer container, Docker Named Volume, dan Bind Mount.

---

## Activities

### 1. Container Filesystem Persistence Test

- Membuat container BusyBox untuk menguji penyimpanan data pada filesystem container:

```bash
sudo docker run -dit --name atlas-storage-test busybox sh
```

- Masuk ke shell container:

```bash
sudo docker exec -it atlas-storage-test sh
```

- Membuat file pada filesystem container:

```bash
echo "Atlas Day 13" > /data.txt
```

- Melakukan verifikasi:

```bash
cat /data.txt
```

Output:

```text
Atlas Day 13
```

- Container kemudian dihentikan:

```bash
sudo docker stop atlas-storage-test
```

- Container dijalankan kembali:

```bash
sudo docker start atlas-storage-test
```

- File masih dapat diakses:

```bash
sudo docker exec atlas-storage-test cat /data.txt
```

Output:

```text
Atlas Day 13
```

- Hal ini menunjukkan bahwa data pada writable layer container masih tersedia selama container yang sama masih ada.

### 2. Container Removal Test

- Container kemudian dihentikan dan dihapus:

```bash
sudo docker stop atlas-storage-test
sudo docker rm atlas-storage-test
```

- Container baru dibuat dengan nama yang sama:

```bash
sudo docker run -dit --name atlas-storage-test busybox sh
```

- Dilakukan pengecekan terhadap file sebelumnya:

```bash
sudo docker exec atlas-storage-test cat /data.txt
```

- File tidak ditemukan.

- Eksperimen menunjukkan bahwa data yang disimpan langsung pada writable layer container tidak bertahan setelah container dihapus.

### 3. Named Volume Creation

- Membuat Docker Named Volume:

```bash
sudo docker volume create atlas-data
```

- Melakukan verifikasi:

```bash
sudo docker volume ls
```

Output:

```text
DRIVER    VOLUME NAME
local     atlas-data
```

- Volume `atlas-data` berhasil dibuat dengan driver `local`.

### 4. Named Volume Persistence Test

- Membuat container dan melakukan mount terhadap `atlas-data`:

```bash
sudo docker run -dit --name atlas-volume-test -v atlas-data:/data busybox sh
```

- Masuk ke container:

```bash
sudo docker exec -it atlas-volume-test sh
```

- Membuat file pada mounted directory:

```bash
echo "Atlas Volume Day 13" > /data/data.txt
```

- Melakukan verifikasi:

```bash
cat /data/data.txt
```

Output:

```text
Atlas Volume Day 13
```

- Container kemudian dihentikan dan dihapus:

```bash
sudo docker stop atlas-volume-test
sudo docker rm atlas-volume-test
```

- Container baru dibuat menggunakan volume yang sama:

```bash
sudo docker run -dit --name atlas-volume-test2 -v atlas-data:/data busybox sh
```

- File kemudian diperiksa:

```bash
sudo docker exec atlas-volume-test2 cat /data/data.txt
```

Output:

```text
Atlas Volume Day 13
```

- Data tetap tersedia walaupun container sebelumnya telah dihapus.

- Hal ini membuktikan bahwa Docker Volume memiliki lifecycle yang terpisah dari container.

### 5. Named Volume Inspection

- Melakukan pemeriksaan terhadap volume:

```bash
sudo docker volume inspect atlas-data
```

- Hasil menunjukkan:

```text
Driver: local
Mountpoint: /var/lib/docker/volumes/atlas-data/_data
Scope: local
```

- Docker mengelola lokasi penyimpanan volume pada:

```text
/var/lib/docker/volumes/atlas-data/_data
```

### 6. Bind Mount Preparation

- Membuat directory pada Docker Host:

```bash
mkdir -p ~/atlas-bind-data
```

- Membuat file pada directory tersebut:

```bash
echo "Atlas Bind Mount Day 13" > ~/atlas-bind-data/data.txt
```

- Melakukan verifikasi:

```bash
ls -l ~/atlas-bind-data
```

- File `data.txt` berhasil dibuat pada Host.

### 7. Bind Mount Test

- Membuat container dengan Bind Mount:

```bash
sudo docker run -dit --name atlas-bind-test -v ~/atlas-bind-data:/data busybox sh
```

- Melakukan pengecekan isi directory dari container:

```bash
sudo docker exec atlas-bind-test ls -l /data
```

- File `data.txt` terlihat dari dalam container.

- Membaca file dari container:

```bash
sudo docker exec atlas-bind-test cat /data/data.txt
```

Output:

```text
Atlas Bind Mount Day 13
```

- Hal ini menunjukkan bahwa directory Host `~/atlas-bind-data` berhasil di-mount ke `/data` pada container.

### 8. Host-to-Container Data Synchronization

- File pada Host diubah:

```bash
echo "Modified from Host" > ~/atlas-bind-data/data.txt
```

- Isi file kemudian diperiksa dari container:

```bash
sudo docker exec atlas-bind-test cat /data/data.txt
```

- Perubahan dari Host langsung terlihat pada container.

### 9. Container-to-Host Data Synchronization

- File kemudian diubah dari dalam container menggunakan `docker exec`:

```bash
sudo docker exec atlas-bind-test sh -c 'echo "Modified from Container" > /data/data.txt'
```

- File diperiksa kembali dari Host:

```bash
cat ~/atlas-bind-data/data.txt
```

Output:

```text
Modified from Container
```

- Perubahan dari container langsung terlihat pada Host.

- Hal ini membuktikan bahwa Bind Mount menyediakan akses terhadap data yang sama dari kedua sisi.

### 10. Bind Mount Persistence After Container Removal

- Container Bind Mount dihentikan dan dihapus:

```bash
sudo docker stop atlas-bind-test
sudo docker rm atlas-bind-test
```

- Directory pada Host diperiksa kembali:

```bash
ls -l ~/atlas-bind-data
```

- File `data.txt` masih tersedia.

- Isi file diperiksa:

```bash
cat ~/atlas-bind-data/data.txt
```

Output:

```text
Modified from Container
```

- Data tetap tersedia setelah container dihapus karena data sebenarnya berada pada directory Host.

### 11. Named Volume vs Bind Mount Inspection

- Membuat container menggunakan Named Volume:

```bash
sudo docker run -dit --name atlas-volume-final -v atlas-data:/data busybox sh
```

- Membuat container menggunakan Bind Mount:

```bash
sudo docker run -dit --name atlas-bind-final -v ~/atlas-bind-data:/data busybox sh
```

- Melakukan pemeriksaan terhadap mount pada Named Volume:

```bash
sudo docker inspect atlas-volume-final --format='{{range .Mounts}}{{printf "Type: %s\nSource: %s\nDestination: %s\n\n" .Type .Source .Destination}}{{end}}'
```

Output:

```text
Type: volume
Source: /var/lib/docker/volumes/atlas-data/_data
Destination: /data
```

- Melakukan pemeriksaan terhadap mount pada Bind Mount:

```bash
sudo docker inspect atlas-bind-final --format='{{range .Mounts}}{{printf "Type: %s\nSource: %s\nDestination: %s\n\n" .Type .Source .Destination}}{{end}}'
```

Output:

```text
Type: bind
Source: /home/david/atlas-bind-data
Destination: /data
```

- Hasil tersebut memperlihatkan perbedaan storage source antara Named Volume dan Bind Mount.

---

## Comparison

| Feature | Container Filesystem | Named Volume | Bind Mount |
|---|---|---|---|
| Storage location | Writable layer container | Docker-managed storage | Host directory |
| Survives container stop | Yes | Yes | Yes |
| Survives container removal | No | Yes | Yes |
| Storage managed by | Container | Docker | User |
| Host path controlled by user | No | No | Yes |
| Example | `/data.txt` | `atlas-data:/data` | `~/atlas-bind-data:/data` |

---

## Storage Architecture

### Container Filesystem

```text
Container
└── Writable Layer
    └── /data.txt
```

Data berada pada writable layer container dan hilang ketika container dihapus.

### Named Volume

```text
Container
└── /data
    │
    ↓
atlas-data
    │
    ↓
/var/lib/docker/volumes/atlas-data/_data
```

Docker mengelola storage volume dan lifecycle volume terpisah dari container.

### Bind Mount

```text
Host
└── /home/david/atlas-bind-data
    └── data.txt
          │
          ↓
Container
└── /data
    └── data.txt
```

Host directory secara langsung di-mount ke directory pada container.

---

## Key Concepts

### Container Filesystem

Data yang ditulis langsung pada writable layer container mengikuti lifecycle container.

Container yang sama masih menyimpan data setelah `stop` dan `start`, tetapi data hilang ketika container dihapus.

### Named Volume

Named Volume merupakan storage yang dikelola oleh Docker.

Contoh:

```bash
-v atlas-data:/data
```

Docker menentukan dan mengelola lokasi penyimpanan volume.

### Bind Mount

Bind Mount menghubungkan directory tertentu pada Host dengan directory pada container.

Contoh:

```bash
-v ~/atlas-bind-data:/data
```

User menentukan lokasi storage pada Host.

### Docker Exec

`docker exec` digunakan untuk menjalankan command di dalam container yang sedang berjalan.

Contoh:

```bash
sudo docker exec atlas-bind-test cat /data/data.txt
```

Command tersebut diketik dari Host, tetapi `cat /data/data.txt` dieksekusi di dalam container.

---

## Problems

- Pada awal eksperimen, data pada writable layer container terlihat tetap tersedia setelah container dihentikan dan dijalankan kembali.
- Hal tersebut dapat menyebabkan asumsi bahwa data container bersifat persistent.
- Setelah container dihapus dan dibuat ulang, data tidak lagi tersedia.
- Eksperimen kemudian dilanjutkan menggunakan Named Volume untuk memisahkan storage dari lifecycle container.
- Bind Mount juga diuji untuk memahami penyimpanan langsung pada directory Host.

---

## Lessons Learned

- Container filesystem bersifat ephemeral terhadap lifecycle container.
- `docker stop` tidak menghapus data pada writable layer container.
- `docker rm` menghapus container beserta writable layer-nya.
- Named Volume memungkinkan data tetap tersedia setelah container dihapus.
- Bind Mount memungkinkan directory Host digunakan langsung oleh container.
- Named Volume dikelola oleh Docker.
- Bind Mount menggunakan lokasi storage yang ditentukan oleh user.
- Named Volume dan Bind Mount sama-sama dapat menyediakan persistence di luar lifecycle container.
- `docker exec` dapat digunakan untuk menjalankan command di dalam container tanpa harus masuk secara interaktif ke shell container.
- Storage abstraction memungkinkan container dibuat dan dihapus tanpa kehilangan data aplikasi yang persistent.

---

## Documentation

### Screenshots

1. **Container Filesystem Persistence** — menunjukkan hasil pengujian data pada writable layer container.
2. **Named Volume Persistence** — menunjukkan data `atlas-data` tetap tersedia setelah container pertama dihapus dan container kedua dibuat.
3. **Bind Mount Persistence** — menunjukkan sinkronisasi data antara Host dan container serta persistence setelah container dihapus.
4. **Volume and Bind Mount Inspection** — menunjukkan perbedaan `Type`, `Source`, dan `Destination` antara Named Volume dan Bind Mount.

### Hero Screenshot

**Volume and Bind Mount Inspection** menjadi screenshot utama karena secara langsung menunjukkan perbedaan konfigurasi storage:

```text
Type: volume
Source: /var/lib/docker/volumes/atlas-data/_data
Destination: /data
```

dan:

```text
Type: bind
Source: /home/david/atlas-bind-data
Destination: /data
```

---

## Next Session

- Mempelajari Docker Compose.
- Mengelola beberapa container menggunakan satu konfigurasi.
- Menghubungkan service dengan Docker Network.
- Mengintegrasikan persistence menggunakan Docker Volume.
- Melanjutkan pengembangan infrastructure Atlas.

---

## Status

✅ Container Filesystem Persistence Tested

✅ Container Removal Persistence Behavior Verified

✅ Named Volume Created

✅ Named Volume Persistence Tested

✅ Named Volume Inspected

✅ Bind Mount Created

✅ Bind Mount Tested

✅ Host-to-Container Synchronization Tested

✅ Container-to-Host Synchronization Tested

✅ Bind Mount Persistence After Container Removal Verified

✅ Named Volume vs Bind Mount Compared

✅ Docker Storage & Persistence Completed