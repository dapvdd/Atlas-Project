# Atlas Project
## Engineering Log

---

# Day 14 — Docker Compose

**Date:** 15 August 2026

**Phase:** Docker Orchestration

**Milestone:** Docker Compose, Multi-Service Stack & Persistent Storage

---

## Objective

Mempelajari Docker Compose sebagai metode deklaratif untuk mendefinisikan dan mengelola beberapa resource Docker melalui satu file konfigurasi.

Eksperimen difokuskan pada:

- Instalasi Docker Compose V2.
- Pembuatan `compose.yaml`.
- Pengelolaan service menggunakan Docker Compose.
- Port publishing.
- Compose-managed network.
- Komunikasi antar-service menggunakan Docker DNS.
- Lifecycle management menggunakan `docker compose up` dan `docker compose down`.
- Integrasi Named Volume dengan Docker Compose.
- Persistence data setelah container di-recreate.

---

## Activities

### 1. Docker Compose Availability Check

- Dilakukan pengecekan Docker Compose pada `SRV-UBU-01`:

```bash
sudo docker compose version
```

- Docker merespons bahwa `docker compose` belum tersedia.

- Pengecekan instalasi Docker menunjukkan bahwa Docker menggunakan package Ubuntu:

```text
docker.io
```

dengan versi:

```text
29.1.3-0ubuntu4.1
```

- Package `docker-compose-plugin` tidak tersedia pada repository yang digunakan.

- Docker Compose V2 kemudian dipasang menggunakan package Ubuntu:

```bash
sudo apt update
sudo apt install docker-compose-v2
```

- Setelah instalasi, Docker Compose berhasil diverifikasi:

```bash
sudo docker compose version
```

Docker Compose berhasil tersedia sebagai subcommand Docker CLI.

### 2. Compose Workspace Preparation

- Dibuat directory khusus untuk konfigurasi Compose:

```bash
mkdir -p ~/atlas-compose
cd ~/atlas-compose
```

- File konfigurasi Compose dibuat dengan nama:

```text
compose.yaml
```

### 3. First Docker Compose Service

Konfigurasi awal dibuat untuk satu service Nginx:

```yaml
services:
  atlas-web:
    image: nginx:alpine
    container_name: atlas-compose-web
    ports:
      - "8080:80"
```

- Konfigurasi tersebut mendefinisikan:

  - Service `atlas-web`.
  - Image `nginx:alpine`.
  - Container `atlas-compose-web`.
  - Port Host `8080` yang dipetakan ke port `80` pada container.

- Saat menjalankan Compose, terjadi konflik karena port `8080` sudah digunakan oleh container `atlas-nginx` dari eksperimen sebelumnya.

Error:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

- Container `atlas-nginx` dipertahankan agar konfigurasi dan service dari eksperimen sebelumnya tidak terganggu.

- Port Compose kemudian diubah menjadi:

```yaml
ports:
  - "8081:80"
```

- Stack berhasil dijalankan:

```bash
sudo docker compose up -d
```

- Compose membuat network:

```text
atlas-compose_default
```

- Container berhasil dijalankan:

```text
atlas-compose-web
```

### 4. Web Service Verification

- Status service diperiksa:

```bash
sudo docker compose ps
```

Output menunjukkan:

```text
NAME                IMAGE          COMMAND                  SERVICE     STATUS         PORTS
atlas-compose-web   nginx:alpine   "/docker-entrypoint.…"   atlas-web   Up             0.0.0.0:8081->80/tcp
```

- Service kemudian diuji dari Host:

```bash
curl http://localhost:8081
```

- Nginx berhasil memberikan response halaman default.

- Hal ini membuktikan bahwa service yang dikelola Docker Compose berhasil berjalan dan dapat diakses melalui port yang dipublish.

### 5. Compose Lifecycle Test

- Stack kemudian dihentikan menggunakan:

```bash
sudo docker compose down
```

- Container `atlas-compose-web` berhasil dihentikan dan dihapus.

- Container `atlas-nginx` tetap berjalan karena container tersebut bukan bagian dari Compose project.

- Stack kemudian dibuat kembali menggunakan:

```bash
sudo docker compose up -d
```

- Service kembali berjalan dan dapat diuji kembali menggunakan:

```bash
curl http://localhost:8081
```

- Hal ini menunjukkan bahwa Docker Compose dapat mengelola lifecycle resource yang berada dalam project Compose tanpa memengaruhi container lain pada Docker Host.

### 6. Multiple Services

Konfigurasi Compose kemudian dikembangkan menjadi dua service:

```yaml
services:
  atlas-web:
    image: nginx:alpine
    container_name: atlas-compose-web
    ports:
      - "8081:80"

  atlas-test:
    image: busybox:latest
    container_name: atlas-compose-test
    command: ["sleep", "3600"]
```

- Service yang didefinisikan:

```text
atlas-web
atlas-test
```

- Stack dijalankan menggunakan:

```bash
sudo docker compose up -d
```

- Status service diperiksa:

```bash
sudo docker compose ps
```

- Kedua service berhasil berjalan:

```text
atlas-compose-web
atlas-compose-test
```

### 7. Compose Network and Service Discovery

- Docker Compose secara otomatis membuat network:

```text
atlas-compose_default
```

- Kedua service terhubung ke network tersebut.

- Komunikasi antar-service diuji dari `atlas-test`:

```bash
sudo docker compose exec atlas-test ping -c 4 atlas-web
```

- Docker berhasil melakukan DNS resolution:

```text
PING atlas-web (172.19.0.2)
```

- Hasil pengujian:

```text
4 packets transmitted
4 packets received
0% packet loss
```

- Container `atlas-test` memiliki alamat:

```text
172.19.0.3
```

- Container `atlas-web` memiliki alamat:

```text
172.19.0.2
```

- Pengujian menunjukkan bahwa service dapat saling berkomunikasi menggunakan **service name** tanpa perlu menentukan IP address secara manual.

- IP address digunakan sebagai hasil resolusi internal Docker DNS, sedangkan `atlas-web` digunakan sebagai identifier yang stabil untuk komunikasi antar-service.

### 8. Compose and Named Volume Integration

Konfigurasi `atlas-web` kemudian dikembangkan dengan Named Volume:

```yaml
services:
  atlas-web:
    image: nginx:alpine
    container_name: atlas-compose-web
    ports:
      - "8081:80"
    volumes:
      - atlas-web-data:/usr/share/nginx/html

  atlas-test:
    image: busybox:latest
    container_name: atlas-compose-test
    command: ["sleep", "3600"]

volumes:
  atlas-web-data:
```

- Named Volume dideklarasikan melalui:

```yaml
volumes:
  atlas-web-data:
```

- Volume kemudian di-mount ke directory web root Nginx:

```text
atlas-web-data:/usr/share/nginx/html
```

- Konfigurasi diperiksa menggunakan:

```bash
sudo docker compose config
```

- Compose menghasilkan deklarasi volume:

```text
volumes:
  atlas-web-data:
    name: atlas-compose_atlas-web-data
```

### 9. Compose-managed Volume Creation

- Stack dijalankan kembali:

```bash
sudo docker compose up -d
```

- Docker Compose secara otomatis membuat Named Volume:

```text
atlas-compose_atlas-web-data
```

- Volume diverifikasi menggunakan:

```bash
sudo docker volume ls
```

Output menunjukkan:

```text
DRIVER    VOLUME NAME
local     atlas-compose_atlas-web-data
local     atlas-data
```

- Hal ini menunjukkan bahwa Compose dapat membuat dan mengelola Named Volume berdasarkan deklarasi pada `compose.yaml`.

### 10. Volume Mount Verification

- Mount pada `atlas-compose-web` diperiksa:

```bash
sudo docker inspect atlas-compose-web --format='{{range .Mounts}}{{printf "Type: %s\nSource: %s\nDestination: %s\n\n" .Type .Source .Destination}}{{end}}'
```

Output:

```text
Type: volume
Source: /var/lib/docker/volumes/atlas-compose_atlas-web-data/_data
Destination: /usr/share/nginx/html
```

- Hasil tersebut membuktikan bahwa Named Volume berhasil di-mount ke web root Nginx.

### 11. Persistent Data Test with Compose

- Masuk ke shell `atlas-web` menggunakan:

```bash
sudo docker compose exec atlas-web sh
```

- Data ditulis ke directory yang di-mount:

```bash
echo "Atlas Compose Persistent Data" > /usr/share/nginx/html/index.html
```

- Isi file diverifikasi:

```bash
cat /usr/share/nginx/html/index.html
```

Output:

```text
Atlas Compose Persistent Data
```

- Shell kemudian ditutup:

```bash
exit
```

- Stack dihentikan dan resource Compose dihapus:

```bash
sudo docker compose down
```

- Stack kemudian dibuat kembali:

```bash
sudo docker compose up -d
```

- Data diuji kembali melalui Nginx:

```bash
curl http://localhost:8081
```

Output:

```text
Atlas Compose Persistent Data
```

- Data tetap tersedia setelah container di-recreate.

- Hal ini membuktikan bahwa Named Volume memiliki lifecycle yang terpisah dari container dan dapat dikelola secara deklaratif melalui Docker Compose.

---

## Architecture

```text
                         Docker Host
                      SRV-UBU-01
                           │
                    Docker Compose
                           │
                  compose.yaml
                           │
             atlas-compose_default
                           │
              ┌────────────┴────────────┐
              │                         │
         atlas-web                 atlas-test
              │                         │
       nginx:alpine                 busybox
              │
       Host :8081
              │
       Container :80
              │
       /usr/share/nginx/html
              │
              ↓
   atlas-compose_atlas-web-data
```

Service discovery:

```text
atlas-test
    │
    │ ping atlas-web
    ↓
Docker DNS
    │
    ↓
atlas-web
    │
    ↓
172.19.0.2
```

---

## Problems

### Port Conflict

- Port `8080` sudah digunakan oleh container `atlas-nginx`.
- Docker Compose tidak dapat melakukan port binding pada port yang sudah digunakan.

Error:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

- Port Compose kemudian dipindahkan ke `8081`.
- Container `atlas-nginx` tetap dipertahankan dan berjalan secara independen dari Compose project.

### Docker Compose Not Initially Available

- Docker Host menggunakan package `docker.io` dari Ubuntu.
- Docker Compose V2 belum tersedia pada instalasi awal.
- Compose kemudian dipasang menggunakan `docker-compose-v2`.

---

## Lessons Learned

- Docker Compose memungkinkan konfigurasi service didefinisikan secara deklaratif menggunakan YAML.
- `docker compose up -d` dapat membuat network, volume, container, dan menjalankan service berdasarkan konfigurasi.
- `docker compose down` mengelola resource yang dibuat oleh Compose project tanpa menghapus container Docker lain yang tidak berada dalam project tersebut.
- Compose secara otomatis membuat default network untuk service.
- Service dapat berkomunikasi menggunakan service name melalui Docker DNS.
- IP address internal container tidak perlu ditentukan secara manual untuk komunikasi antar-service.
- Port publishing tetap menggunakan konsep `HOST:CONTAINER`.
- Port Host tidak dapat digunakan oleh lebih dari satu binding yang konflik.
- Docker Compose dapat mengelola Named Volume secara deklaratif.
- Named Volume memungkinkan data tetap tersedia setelah container di-recreate.
- `docker compose exec` dapat digunakan untuk menjalankan command di dalam service container.
- Docker Compose menggabungkan pengelolaan beberapa resource Docker menjadi satu application stack.
- Compose dapat mengintegrasikan konsep networking dari Day 12 dengan persistence dari Day 13.

---

## Key Commands

### Compose Version

```bash
sudo docker compose version
```

### Validate Configuration

```bash
sudo docker compose config
```

### Start Stack

```bash
sudo docker compose up -d
```

### Check Services

```bash
sudo docker compose ps
```

### Execute Command in Service

```bash
sudo docker compose exec atlas-test ping -c 4 atlas-web
```

### Stop and Remove Compose Resources

```bash
sudo docker compose down
```

### List Volumes

```bash
sudo docker volume ls
```

### Inspect Container Mounts

```bash
sudo docker inspect atlas-compose-web --format='{{range .Mounts}}{{printf "Type: %s\nSource: %s\nDestination: %s\n\n" .Type .Source .Destination}}{{end}}'
```

---

## Documentation

### Screenshots

1. **Docker Compose Installation & Verification** — menunjukkan Compose V2 berhasil tersedia pada `SRV-UBU-01`.
2. **First Compose Service** — menunjukkan `atlas-compose-web` berhasil dijalankan dan Nginx dapat diakses melalui port `8081`.
3. **Multiple Compose Services** — menunjukkan `atlas-compose-web` dan `atlas-compose-test` berjalan dalam satu Compose project.
4. **Compose Service DNS** — menunjukkan `atlas-test` berhasil melakukan ping terhadap `atlas-web` menggunakan service name.
5. **Compose Volume Creation** — menunjukkan `atlas-compose_atlas-web-data` dibuat otomatis oleh Compose.
6. **Compose Volume Inspection** — menunjukkan Named Volume ter-mount ke `/usr/share/nginx/html`.
7. **Compose Persistence Test** — menunjukkan data tetap tersedia setelah Compose stack di-recreate.

### Hero Screenshot

**Compose Volume Inspection** menjadi screenshot utama karena menunjukkan hubungan langsung antara Docker Compose, Named Volume, dan container:

```text
Type: volume
Source: /var/lib/docker/volumes/atlas-compose_atlas-web-data/_data
Destination: /usr/share/nginx/html
```

---

## Next Session

- Memperluas Compose stack menjadi beberapa service yang lebih realistis.
- Mempelajari environment variables pada Compose.
- Mempelajari konfigurasi service dan dependency antar-container.
- Mengintegrasikan konfigurasi jaringan dan storage ke dalam stack Atlas.
- Melanjutkan pembangunan infrastructure services pada Atlas Project.

---

## Status

✅ Docker Compose V2 Installed

✅ Docker Compose Verified

✅ First Compose Service Created

✅ Port Publishing Tested

✅ Compose Network Created

✅ Compose Lifecycle Tested

✅ Multiple Services Created

✅ Service-to-Service DNS Tested

✅ Named Volume Managed by Compose

✅ Volume Mount Verified

✅ Persistent Data Tested

✅ Docker Compose Fundamentals Completed