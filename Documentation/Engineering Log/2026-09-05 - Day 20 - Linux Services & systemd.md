# Atlas Project

## Engineering Log

---

# Day 20 — Linux Services & systemd

**Date:** 5 September 2026

**Phase:** Linux Administration

**Milestone:** Understanding Linux Services & systemd

---

## Objective

Memahami konsep Linux Service dan peran `systemd` dalam mengelola service pada Linux Server.

Fokus pembelajaran:

- Memahami konsep Linux Service.
- Memahami peran `systemd`.
- Melihat service yang sedang berjalan.
- Memeriksa failed services.
- Memeriksa status service menggunakan `systemctl`.
- Memahami perbedaan status `active` dan `enabled`.
- Mempelajari service lifecycle.
- Menggunakan `start`, `stop`, dan `restart`.
- Memahami penggunaan `is-active` dan `is-enabled`.

---

## Activities

### 1. Systemd Inspection

Versi `systemd` pada `SRV-UBU-01` diperiksa menggunakan:

```bash
systemctl --version
```

Hasil menunjukkan bahwa sistem menggunakan:

```text
systemd 259
```

`systemd` merupakan system and service manager yang digunakan untuk mengelola berbagai service dan proses sistem pada Linux.

Service yang mengalami kegagalan juga diperiksa menggunakan:

```bash
systemctl --failed
```

Hasil menunjukkan:

```text
0 loaded units listed.
```

Hal ini menunjukkan bahwa tidak terdapat failed service pada saat pemeriksaan dilakukan.

---

### 2. Running Services Inspection

Daftar service yang sedang berjalan diperiksa menggunakan:

```bash
systemctl list-units --type=service --state=running
```

Beberapa service yang ditemukan antara lain:

```text
chrony.service
containerd.service
cron.service
docker.service
ssh.service
rsyslog.service
systemd-networkd.service
systemd-resolved.service
```

Hal ini menunjukkan bahwa `SRV-UBU-01` menjalankan berbagai background service yang mendukung fungsi Linux Server.

Secara konseptual:

```text
Linux Server
     │
     ▼
systemd
     │
     ├── ssh.service
     ├── docker.service
     ├── cron.service
     ├── containerd.service
     └── Other System Services
```

---

### 3. SSH Service Inspection

Service SSH diperiksa menggunakan:

```bash
systemctl status ssh --no-pager
```

Hasil menunjukkan bahwa SSH memiliki status:

```text
Loaded: loaded
Active: active (running)
```

Service juga memiliki status:

```text
enabled
```

Hal ini menunjukkan bahwa SSH:

```text
Active
↓
Sedang berjalan sekarang
```

dan:

```text
Enabled
↓
Dikonfigurasi untuk berjalan otomatis saat boot
```

Output juga menunjukkan bahwa SSH Server sedang melakukan listening pada:

```text
0.0.0.0 port 22
```

dan:

```text
:: port 22
```

Hal ini menunjukkan bahwa SSH Service aktif dan siap menerima koneksi.

Pada log SSH juga terlihat adanya koneksi dari user `david`, yang menunjukkan bahwa aktivitas SSH dicatat oleh service tersebut.

---

### 4. Docker Service Inspection

Docker Service diperiksa menggunakan:

```bash
systemctl status docker --no-pager
```

Hasil menunjukkan:

```text
Loaded: loaded
Active: active (running)
```

Docker Service juga memiliki status:

```text
enabled
```

Process utama yang menjalankan Docker adalah:

```text
dockerd
```

Secara konseptual:

```text
systemd
    │
    ▼
docker.service
    │
    ▼
dockerd
    │
    ├── Containers
    ├── Networks
    └── Docker Runtime
```

Hal ini menghubungkan pembelajaran `systemd` dengan Docker yang sebelumnya telah dipelajari pada Atlas Project.

Docker yang dijalankan menggunakan:

```bash
sudo docker run
```

bergantung pada Docker daemon yang dikelola melalui:

```text
docker.service
```

---

### 5. Understanding Active vs Enabled

Status service diperiksa menggunakan:

```bash
systemctl is-active cron
```

dan:

```bash
systemctl is-enabled cron
```

Kedua command tersebut memiliki fungsi yang berbeda.

`is-active` digunakan untuk menjawab pertanyaan:

```text
Apakah service sedang berjalan sekarang?
```

Contoh hasil:

```text
active
```

atau:

```text
inactive
```

Sedangkan `is-enabled` digunakan untuk menjawab pertanyaan:

```text
Apakah service dikonfigurasi untuk berjalan otomatis saat boot?
```

Contoh hasil:

```text
enabled
```

atau:

```text
disabled
```

Dengan demikian:

```text
ACTIVE ≠ ENABLED
```

Kedua status tersebut merupakan konsep yang berbeda.

Sebuah service dapat memiliki kondisi:

```text
active + disabled
```

Artinya service sedang berjalan sekarang, tetapi tidak dikonfigurasi untuk otomatis berjalan saat boot.

Sebaliknya, sebuah service juga dapat berada dalam kondisi:

```text
inactive + enabled
```

Artinya service sedang tidak berjalan saat ini, tetapi dikonfigurasi untuk berjalan otomatis ketika sistem melakukan boot.

---

### 6. Service Lifecycle Experiment

Eksperimen lifecycle dilakukan menggunakan:

```text
cron.service
```

Service `cron` dipilih karena dapat digunakan untuk mempelajari lifecycle dasar tanpa mengganggu service utama Atlas seperti SSH dan Docker.

Status awal diperiksa menggunakan:

```bash
sudo systemctl status cron --no-pager
```

Service kemudian dihentikan menggunakan:

```bash
sudo systemctl stop cron
```

Status service diperiksa menggunakan:

```bash
systemctl is-active cron
```

Hasil menunjukkan:

```text
inactive
```

Hal ini membuktikan bahwa service sudah tidak berjalan setelah dihentikan.

Service kemudian dijalankan kembali menggunakan:

```bash
sudo systemctl start cron
```

Status service kembali menjadi:

```text
active
```

Service juga diuji menggunakan:

```bash
sudo systemctl restart cron
```

Command `restart` digunakan untuk menghentikan dan menjalankan kembali service.

Lifecycle yang dipelajari dapat digambarkan sebagai:

```text
ACTIVE
   │
   │ systemctl stop
   ▼
INACTIVE
   │
   │ systemctl is-active
   ▼
inactive
   │
   │ systemctl start
   ▼
ACTIVE
   │
   │ systemctl restart
   ▼
ACTIVE
```

---

## Key Concepts

### Linux Service

Linux Service merupakan proses atau aplikasi yang berjalan di background untuk menyediakan fungsi tertentu pada sistem.

Contohnya:

```text
ssh.service
```

menyediakan SSH Server.

```text
docker.service
```

menjalankan Docker daemon.

```text
cron.service
```

menjalankan scheduled background tasks.

---

### systemd

`systemd` merupakan system and service manager yang digunakan untuk mengelola service pada Linux.

Melalui `systemctl`, administrator dapat melakukan:

```text
Check Status
     ↓
Start Service
     ↓
Stop Service
     ↓
Restart Service
     ↓
Enable at Boot
     ↓
Disable at Boot
```

---

### is-active

Command:

```bash
systemctl is-active <service>
```

digunakan untuk memeriksa apakah sebuah service sedang berjalan saat ini.

Contoh:

```text
active
```

berarti service sedang berjalan.

Sedangkan:

```text
inactive
```

berarti service sedang tidak berjalan.

---

### is-enabled

Command:

```bash
systemctl is-enabled <service>
```

digunakan untuk memeriksa apakah sebuah service dikonfigurasi untuk berjalan otomatis saat sistem boot.

Contoh:

```text
enabled
```

berarti service dikonfigurasi untuk otomatis berjalan saat boot.

Sedangkan:

```text
disabled
```

berarti service tidak dikonfigurasi untuk otomatis berjalan saat boot.

---

### Service Lifecycle

Lifecycle dasar service yang dipelajari:

```text
ACTIVE
   │
   ├── stop
   │      ↓
   │   INACTIVE
   │
   ├── start
   │      ↓
   │   ACTIVE
   │
   └── restart
          ↓
        ACTIVE
```

---

## Lessons Learned

- Linux Server menjalankan berbagai background service.
- `systemd` digunakan untuk mengelola system services.
- `systemctl` digunakan untuk berinteraksi dengan service.
- `systemctl status` digunakan untuk melihat informasi detail service.
- `systemctl --failed` digunakan untuk memeriksa failed services.
- `systemctl list-units` dapat digunakan untuk melihat service yang sedang berjalan.
- `active` menunjukkan apakah service sedang berjalan saat ini.
- `enabled` menunjukkan apakah service dikonfigurasi untuk otomatis berjalan saat boot.
- `active` dan `enabled` merupakan dua status yang berbeda.
- `stop` digunakan untuk menghentikan service.
- `start` digunakan untuk menjalankan service.
- `restart` digunakan untuk memulai ulang service.
- `is-active` digunakan untuk memeriksa kondisi runtime service.
- `is-enabled` digunakan untuk memeriksa konfigurasi service saat boot.

Secara keseluruhan:

```text
SERVICE STATUS

is-active
    │
    ▼
Apakah service berjalan sekarang?

is-enabled
    │
    ▼
Apakah service otomatis berjalan saat boot?


SERVICE CONTROL

stop
    ↓
Matikan service

start
    ↓
Jalankan service

restart
    ↓
Mulai ulang service
```

---

## Problems

Tidak terdapat masalah teknis yang menghambat eksperimen.

Eksperimen lifecycle sengaja menggunakan:

```text
cron.service
```

agar proses `stop`, `start`, dan `restart` dapat dilakukan tanpa mengganggu service utama Atlas seperti:

```text
ssh.service
```

dan:

```text
docker.service
```

Dengan demikian, eksperimen dapat dilakukan dengan aman tanpa memutus akses SSH atau mengganggu Docker environment yang sedang berjalan.

---

## Documentation

### Screenshots

**01 - Systemd Services Overview.png**

Screenshot berisi:

```bash
systemctl --version
```

serta:

```bash
systemctl --failed
```

dan daftar running services.

Fokus dokumentasi pada:

```text
systemd 259
```

serta kondisi:

```text
0 failed services
```

---

**02 - SSH Service Status.png**

Screenshot berisi:

```bash
systemctl status ssh --no-pager
```

Fokus dokumentasi pada:

```text
Loaded
Active
Enabled
Main PID
```

serta informasi bahwa SSH Server melakukan listening pada port 22.

---

**03 - Docker Service Status.png**

Screenshot berisi:

```bash
systemctl status docker --no-pager
```

Fokus dokumentasi pada hubungan:

```text
systemd
    ↓
docker.service
    ↓
dockerd
```

---

**04 - Service Lifecycle.png**

Screenshot berisi eksperimen:

```bash
sudo systemctl stop cron
systemctl is-active cron
sudo systemctl start cron
sudo systemctl restart cron
```

Fokus dokumentasi pada perubahan status:

```text
active
↓
inactive
↓
active
```

---

**05 - Active vs Enabled.png**

Screenshot berisi:

```bash
systemctl is-active cron
systemctl is-enabled cron
```

Fokus dokumentasi pada perbedaan:

```text
ACTIVE ≠ ENABLED
```

---

## Next Session

### Day 21 — Process Management & Monitoring

Pembelajaran akan dilanjutkan dengan memahami proses yang berjalan pada Linux Server.

Materi yang dapat dipelajari:

- Linux Processes.
- PID.
- Process hierarchy.
- `ps`.
- `top`.
- `htop`.
- Process monitoring.
- CPU dan Memory usage.
- Menghentikan process menggunakan signal.

Pembelajaran akan menghubungkan Linux Processes dengan service yang telah dipelajari melalui `systemd`.

---

## Status

✅ Linux Services

✅ systemd

✅ Running Services

✅ Failed Services

✅ SSH Service Inspection

✅ Docker Service Inspection

✅ Active vs Enabled

✅ Service Lifecycle

✅ systemctl stop

✅ systemctl start

✅ systemctl restart

⏳ Linux Processes

⏳ Process Monitoring

⏳ Process Signals