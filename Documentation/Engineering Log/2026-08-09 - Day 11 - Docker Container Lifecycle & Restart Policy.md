# Atlas Project
## Engineering Log

---

# Day 11 — Docker Container Lifecycle & Restart Policy

**Date:** 09 August 2026

**Phase:** Server Administration

**Milestone:** Container Auto-Restart After Host Reboot

---

## Objective

Mempelajari Docker Container Lifecycle serta memahami fungsi Restart Policy pada Docker. Pada sesi ini dilakukan pengujian terhadap proses stop/start container dan pengujian automatic container restart setelah Ubuntu Server mengalami reboot.

---

## Activities

### 1. Container State Verification

- Mengakses **UBU-SRV-01** melalui SSH.
- Melakukan pengecekan container menggunakan `sudo docker ps`.
- Memastikan `atlas-nginx` dalam kondisi running.
- Melakukan pengecekan seluruh container menggunakan `sudo docker ps -a`.

### 2. Restart Policy Verification

- Melakukan pengecekan Restart Policy pada `atlas-nginx` menggunakan `sudo docker inspect atlas-nginx --format='RestartPolicy={{.HostConfig.RestartPolicy.Name}}'`.
- Ditemukan bahwa Restart Policy awal adalah `no`.
- Kondisi tersebut menjelaskan mengapa `atlas-nginx` tidak otomatis berjalan kembali setelah VM mengalami reboot pada sesi sebelumnya.

### 3. Restart Policy Configuration

- Mengubah Restart Policy menggunakan `sudo docker update --restart unless-stopped atlas-nginx`.
- Melakukan verifikasi kembali menggunakan `sudo docker inspect atlas-nginx --format='RestartPolicy={{.HostConfig.RestartPolicy.Name}}'`.
- Restart Policy berhasil berubah menjadi `unless-stopped`.

### 4. Manual Container Lifecycle Test

- Menghentikan container menggunakan `sudo docker stop atlas-nginx`.
- Memastikan container tidak lagi muncul pada `sudo docker ps`.
- Memastikan container masih tersedia menggunakan `sudo docker ps -a`.
- Menjalankan kembali container menggunakan `sudo docker start atlas-nginx`.
- Container kembali berjalan dengan normal.

### 5. Restart Policy Testing

- Melakukan reboot terhadap Ubuntu Server menggunakan `sudo reboot`.
- SSH terputus karena server melakukan reboot.
- Setelah Ubuntu Server kembali aktif, dilakukan koneksi SSH kembali.
- Melakukan pengecekan menggunakan `sudo docker ps`.
- `atlas-nginx` berhasil berjalan kembali secara otomatis tanpa menjalankan `docker start` secara manual.

### 6. Service Verification After Reboot

- Melakukan pengujian Nginx dari Ubuntu Server menggunakan `curl http://localhost:8080`.
- Nginx berhasil memberikan HTTP response.
- Melakukan pengujian kembali dari Windows Host melalui `http://192.168.56.103:8080`.
- Halaman **Welcome to nginx!** berhasil ditampilkan kembali.

---

## Container Lifecycle

Container dapat mengalami beberapa kondisi selama lifecycle:

**Image → Container → Running → Stopped → Running**

Dengan Restart Policy, alur setelah host reboot menjadi:

**Ubuntu Server Reboot → Docker Engine Start → Restart Policy → atlas-nginx Auto Start → Nginx Running**

---

## Restart Policy

Restart Policy yang digunakan adalah `unless-stopped`.

Konfigurasi ini memungkinkan Docker menjalankan kembali container setelah Docker Engine atau host mengalami restart, selama container tidak sengaja dihentikan dan dibiarkan dalam keadaan stopped.

---

## Current Service Flow

**Windows Host → 192.168.56.103:8080 → UBU-SRV-01 → Docker → atlas-nginx → Nginx :80 → HTTP Response**

---

## Current Topology

Topology tidak mengalami perubahan pada Day 11.

Docker dan Nginx masih berjalan di dalam **UBU-SRV-01** dan belum menambahkan network atau perangkat baru.

Topology tetap menggunakan **Topology v8**:

- Production Network → LAB-LAN
- Management Network → Host-Only Network

---

## Documentation

### Screenshots

1. **Restart Policy Before** — menunjukkan Restart Policy awal `no`.
2. **Restart Policy Configured** — menunjukkan Restart Policy berubah menjadi `unless-stopped`.
3. **Container Lifecycle** — menunjukkan proses stop/start container.
4. **Auto Start After Reboot** — menunjukkan `atlas-nginx` kembali `Up` setelah VM reboot tanpa `docker start`.
5. **Nginx Access After Reboot** — menunjukkan halaman **Welcome to nginx!** berhasil diakses kembali dari Windows.

### Hero Screenshot

**Auto Start After Reboot** menjadi screenshot utama Day 11 karena membuktikan fungsi Restart Policy secara langsung.

### Topology

Tidak ada topology baru.

Tetap menggunakan **Topology-v8.png** dan **Topology.drawio**.

---

## Problems

- Pada sesi sebelumnya, `atlas-nginx` ditemukan dalam status `Exited (255)` setelah VM dimatikan dan dinyalakan kembali.
- Investigasi menggunakan Docker logs, Docker inspect, Docker events, dan system journal menunjukkan bahwa Ubuntu Server mengalami reboot.
- Docker Engine ikut mengalami restart setelah sistem boot kembali.
- Container `atlas-nginx` sebelumnya menggunakan Restart Policy `no`, sehingga container tidak otomatis berjalan kembali setelah reboot.
- Masalah diselesaikan dengan konfigurasi Restart Policy `unless-stopped`.

---

## Lessons Learned

- Docker Container memiliki lifecycle seperti created, running, stopped, dan exited.
- `docker ps` hanya menampilkan container yang sedang running.
- `docker ps -a` menampilkan seluruh container, termasuk container yang stopped atau exited.
- `docker stop` menghentikan container tanpa menghapus container maupun image.
- `docker start` menjalankan kembali container yang telah dibuat sebelumnya.
- Restart Policy menentukan perilaku container ketika container atau Docker Engine mengalami restart.
- Restart Policy `unless-stopped` dapat digunakan agar service kembali berjalan setelah Docker Engine atau host melakukan reboot.
- Pengujian setelah VM reboot berhasil membuktikan bahwa `atlas-nginx` dapat berjalan kembali secara otomatis.
- Restart Policy membuat service containerized lebih resilient terhadap system reboot.
- Troubleshooting menggunakan Docker logs, inspect, events, dan system journal dapat digunakan untuk mencari penyebab container berhenti.

---

## Next Session

- Mempelajari Docker Networking.
- Memahami Docker Bridge Network.
- Memahami hubungan antara Host Network, Docker Network, dan Container Network.
- Mempelajari IP address dan gateway pada Docker Container.
- Menguji komunikasi antara host dan container.

---

## Status

✅ Container State Verified

✅ Restart Policy Verified

✅ `unless-stopped` Configured

✅ Manual Stop/Start Tested

✅ VM Reboot Tested

✅ Container Auto-Start Verified

✅ Nginx Service Verified After Reboot

✅ Windows Web Access Verified After Reboot

✅ Topology v8 Maintained

⬜ Docker Bridge Network

⬜ Container Networking

⬜ Docker Network Communication

⬜ Docker Volume

⬜ Docker Compose