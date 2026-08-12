# Atlas Project
## Engineering Log

---

# Day 12 — Custom Docker Networking

**Date:** 12 August 2026

**Phase:** Container Networking

**Milestone:** Custom Bridge Network, Container DNS & Multi-Network Container

---

## Objective

Melanjutkan pembelajaran Docker Networking dengan membuat User-Defined Bridge Network, memahami komunikasi antar-container, Docker DNS, network isolation, serta konfigurasi multi-network pada container.

---

## Activities

### 1. Custom Docker Network Creation

- Membuat User-Defined Bridge Network menggunakan:

```bash
sudo docker network create atlas-net
```

- Docker berhasil membuat network `atlas-net`.
- Network menggunakan driver `bridge`.
- Network mendapatkan subnet `172.18.0.0/16`.
- Gateway network berada pada `172.18.0.1`.

### 2. Custom Network Inspection

- Melakukan pengecekan menggunakan:

```bash
sudo docker network inspect atlas-net
```

- Ditemukan konfigurasi:
  - Network: `atlas-net`
  - Driver: `bridge`
  - Subnet: `172.18.0.0/16`
  - Gateway: `172.18.0.1`
- Pada awal pemeriksaan, bagian `Containers` masih kosong karena belum terdapat container yang terhubung.
- Subnet `172.18.0.0/16` digunakan karena `172.17.0.0/16` telah digunakan oleh default Docker `bridge`.

### 3. First Container on Custom Network

- Membuat container pertama pada `atlas-net` menggunakan:

```bash
sudo docker run -d --name atlas-test1 --network atlas-net busybox sleep 3600
```

- Container `atlas-test1` berhasil dibuat.
- Container mendapatkan IP `172.18.0.2`.

### 4. Second Container on Custom Network

- Membuat container kedua pada `atlas-net` menggunakan:

```bash
sudo docker run -d --name atlas-test2 --network atlas-net busybox sleep 3600
```

- Container `atlas-test2` berhasil dibuat.
- Container mendapatkan IP `172.18.0.3`.

- Setelah kedua container dibuat, network `atlas-net` memiliki:

```text
atlas-test1 → 172.18.0.2
atlas-test2 → 172.18.0.3
```

### 5. Container-to-Container Connectivity Test Using IP

- Melakukan pengujian komunikasi dari `atlas-test1` menuju `atlas-test2` menggunakan IP:

```bash
sudo docker exec atlas-test1 ping -c 4 172.18.0.3
```

- Pengujian berhasil.
- Hasil menunjukkan `4 packets transmitted` dan `4 packets received`.
- Tidak terdapat packet loss.
- Pengujian membuktikan bahwa container yang berada pada custom network yang sama dapat berkomunikasi menggunakan IP address.

### 6. Container-to-Container Connectivity Test Using Container Name

- Melakukan pengujian menggunakan nama container:

```bash
sudo docker exec atlas-test1 ping -c 4 atlas-test2
```

- Docker berhasil melakukan name resolution:

```text
atlas-test2 → 172.18.0.3
```

- Ping berhasil dengan `0% packet loss`.
- Pengujian membuktikan bahwa Docker menyediakan DNS resolution antar-container pada User-Defined Bridge Network.

### 7. Network Isolation Test

- `atlas-test1` berada pada custom network:

```text
atlas-net
172.18.0.0/16
```

- Sedangkan `atlas-nginx` sebelumnya hanya berada pada default:

```text
bridge
172.17.0.0/16
```

- Dilakukan pengujian dari `atlas-test1` menuju IP `atlas-nginx`:

```bash
sudo docker exec atlas-test1 ping -c 4 172.17.0.2
```

- Pengujian gagal.
- Hal tersebut menunjukkan bahwa container yang berada pada network berbeda tidak dapat berkomunikasi secara langsung melalui network attachment yang berbeda tersebut.

### 8. Container Routing Verification

- Melakukan pengecekan routing dari dalam `atlas-test1` menggunakan:

```bash
sudo docker exec atlas-test1 ip route
```

- Ditemukan:

```text
default via 172.18.0.1 dev eth0
172.18.0.0/16 dev eth0 scope link src 172.18.0.2
```

- Routing tersebut menunjukkan bahwa:
  - IP container adalah `172.18.0.2`.
  - Gateway container adalah `172.18.0.1`.
  - Network yang terhubung langsung adalah `172.18.0.0/16`.

### 9. Network Attachment Verification

- Melakukan pemeriksaan network attachment pada `atlas-test1` menggunakan:

```bash
sudo docker inspect atlas-test1 --format='{{json .NetworkSettings.Networks}}'
```

- `atlas-test1` hanya terhubung ke `atlas-net`.

- Melakukan pemeriksaan network attachment pada `atlas-nginx` menggunakan:

```bash
sudo docker inspect atlas-nginx --format='{{json .NetworkSettings.Networks}}'
```

- `atlas-nginx` sebelumnya hanya terhubung ke default `bridge`.

- Perbedaan network attachment tersebut menjelaskan hasil network isolation pada pengujian sebelumnya.

### 10. Multi-Network Container Configuration

- Menambahkan `atlas-nginx` ke custom network menggunakan:

```bash
sudo docker network connect atlas-net atlas-nginx
```

- `atlas-nginx` tidak dipindahkan dari default `bridge`.
- Container mendapatkan network attachment tambahan pada `atlas-net`.

- Melakukan verifikasi kembali menggunakan:

```bash
sudo docker inspect atlas-nginx --format='{{json .NetworkSettings.Networks}}'
```

- Setelah konfigurasi, `atlas-nginx` memiliki dua network:

```text
bridge
IP: 172.17.0.2

atlas-net
IP: 172.18.0.4
```

### 11. Multi-Network Connectivity Test

- Melakukan pengujian dari `atlas-test1` menuju `atlas-nginx` menggunakan nama container:

```bash
sudo docker exec atlas-test1 ping -c 4 atlas-nginx
```

- Docker melakukan resolution terhadap `atlas-nginx` melalui `atlas-net`.
- `atlas-nginx` diakses melalui IP `172.18.0.4`.
- Pengujian berhasil dengan `0% packet loss`.

- Setelah `atlas-nginx` terhubung ke `atlas-net`, komunikasi menjadi:

```text
atlas-test1
172.18.0.2
     |
     | atlas-net
     v
atlas-nginx
172.18.0.4
```

- Pada saat yang sama, `atlas-nginx` tetap mempertahankan koneksi ke default `bridge` melalui `172.17.0.2`.

---

## Custom Docker Network

User-Defined Bridge Network yang dibuat pada sesi ini:

**Network:** `atlas-net`  
**Driver:** `bridge`  
**Subnet:** `172.18.0.0/16`  
**Gateway:** `172.18.0.1`

Container pada network:

**`atlas-test1` → `172.18.0.2`**

**`atlas-test2` → `172.18.0.3`**

**`atlas-nginx` → `172.18.0.4`**

---

## Docker DNS

Pada User-Defined Bridge Network, container dapat berkomunikasi menggunakan nama container.

Pengujian:

```text
atlas-test1 → atlas-test2
```

Docker melakukan resolution:

```text
atlas-test2 → 172.18.0.3
```

Hal ini membuat komunikasi antar-container tidak bergantung langsung pada hard-coded IP address.

---

## Network Isolation

Sebelum `atlas-nginx` ditambahkan ke `atlas-net`:

```text
atlas-test1
172.18.0.2
     |
     X
     |
atlas-nginx
172.17.0.2
```

Komunikasi gagal karena kedua container berada pada network yang berbeda.

Setelah `atlas-nginx` terhubung ke `atlas-net`:

```text
atlas-test1
172.18.0.2
     |
     | atlas-net
     v
atlas-nginx
172.18.0.4
```

Komunikasi berhasil.

---

## Multi-Network Container

`atlas-nginx` sekarang memiliki dua network attachment:

```text
atlas-nginx
    |
    +-- bridge
    |   └── 172.17.0.2
    |
    +-- atlas-net
        └── 172.18.0.4
```

Konfigurasi ini memungkinkan satu container terhubung ke beberapa Docker network secara bersamaan.

---

## Documentation

### Screenshots

1. **Custom Docker Network Creation** — menunjukkan network `atlas-net` berhasil dibuat.
2. **Custom Network Inspection** — menunjukkan subnet `172.18.0.0/16` dan gateway `172.18.0.1`.
3. **atlas-test1 & atlas-test2 Network** — menunjukkan kedua container terhubung ke `atlas-net`.
4. **Container-to-Container Ping** — menunjukkan komunikasi menggunakan IP `172.18.0.3`.
5. **Docker DNS Resolution** — menunjukkan `atlas-test1` berhasil melakukan ping menggunakan nama `atlas-test2`.
6. **Network Isolation Test** — menunjukkan komunikasi dari `atlas-test1` menuju `atlas-nginx` pada network berbeda gagal.
7. **Multi-Network atlas-nginx** — menunjukkan `atlas-nginx` memiliki network `bridge` dan `atlas-net`.
8. **Multi-Network Connectivity** — menunjukkan komunikasi dari `atlas-test1` menuju `atlas-nginx` setelah network attachment ditambahkan.

### Hero Screenshot

**Multi-Network Connectivity** menjadi screenshot utama Day 12 Part 2 karena menunjukkan hasil akhir eksperimen network isolation dan konfigurasi multi-network container.

### Topology

Tidak terdapat perubahan pada physical/virtual topology utama.

Eksperimen dilakukan pada Docker Networking di dalam **SRV-UBU-01** dengan penambahan User-Defined Bridge Network `atlas-net`.

---

## Problems

- Pada awal pengujian, `atlas-test1` tidak dapat berkomunikasi dengan `atlas-nginx`.
- Pemeriksaan menunjukkan bahwa `atlas-test1` berada pada `atlas-net`, sedangkan `atlas-nginx` hanya berada pada default `bridge`.
- Network isolation menyebabkan komunikasi antar-container gagal.
- Masalah diselesaikan dengan menambahkan `atlas-nginx` ke `atlas-net` menggunakan `docker network connect`.
- Setelah mendapatkan network attachment kedua, `atlas-nginx` memperoleh IP `172.18.0.4` dan berhasil berkomunikasi dengan `atlas-test1`.

---

## Lessons Learned

- User-Defined Bridge Network dapat dibuat menggunakan `docker network create`.
- Docker dapat memberikan subnet berbeda untuk network baru ketika subnet sebelumnya telah digunakan.
- Container yang berada pada network yang sama dapat berkomunikasi menggunakan IP address.
- User-Defined Bridge Network menyediakan DNS resolution antar-container.
- Container dapat diakses menggunakan nama tanpa harus mengetahui IP address secara langsung.
- IP address container dapat berubah sehingga penggunaan nama container lebih fleksibel untuk komunikasi antar-service.
- Container pada network berbeda memiliki network isolation.
- Satu container dapat terhubung ke beberapa Docker network.
- `docker network connect` dapat digunakan untuk menambahkan network attachment tanpa menghapus network yang sudah ada.
- Container multi-network dapat berfungsi sebagai bagian dari beberapa network secara bersamaan.
- Network isolation dan multi-network dapat digunakan untuk mengatur komunikasi antar-service.

---

## Next Session

- Mempelajari Docker Volume.
- Memahami persistence pada Docker Container.
- Mempelajari perbedaan container filesystem dengan Docker Volume.
- Menguji Named Volume dan Bind Mount.
- Melanjutkan pengembangan infrastructure Atlas.

---

## Status

✅ User-Defined Bridge Network Created

✅ Custom Network Inspection Verified

✅ `atlas-test1` Created

✅ `atlas-test2` Created

✅ Container-to-Container Connectivity Tested

✅ Docker DNS Resolution Tested

✅ Network Isolation Tested

✅ `atlas-nginx` Connected to `atlas-net`

✅ Multi-Network Container Verified

✅ Multi-Network Connectivity Tested

✅ Docker Networking Completed

⬜ Docker Volume

⬜ Bind Mount

⬜ Named Volume

⬜ Docker Compose