# Atlas Project
## Engineering Log

---

# Day 12 — Docker Bridge Networking

**Date:** 11 August 2026

**Phase:** Container Networking

**Milestone:** Docker Bridge Network & Container Connectivity

---

## Objective

Mempelajari dasar Docker Networking dengan memahami konsep Docker Bridge Network, hubungan antara Docker Host dengan Container, routing pada Docker Host, serta bagaimana container dapat mengakses network eksternal melalui NAT/MASQUERADE.

---

## Activities

### 1. Docker Network Verification

- Mengakses **SRV-UBU-01** melalui SSH.
- Melakukan pengecekan network menggunakan `sudo docker network ls`.
- Ditemukan tiga Docker network bawaan:
  - `bridge`
  - `host`
  - `none`
- Fokus sesi diarahkan pada default `bridge` network.

### 2. Docker Bridge Network Inspection

- Melakukan pengecekan detail default bridge menggunakan `sudo docker network inspect bridge`.
- Ditemukan konfigurasi:
  - Driver: `bridge`
  - Subnet: `172.17.0.0/16`
  - Gateway: `172.17.0.1`
- Container `atlas-nginx` ditemukan terhubung ke default bridge dengan IP `172.17.0.2`.

### 3. Docker Bridge Interface Verification

- Melakukan pengecekan interface Docker menggunakan `ip addr show docker0`.
- Ditemukan interface `docker0` dengan IP `172.17.0.1/16`.
- Interface `docker0` digunakan sebagai bridge interface pada Docker Host untuk default Docker network.

### 4. Routing Table Verification

- Melakukan pengecekan routing table menggunakan `ip route`.
- Ditemukan route `172.17.0.0/16` melalui interface `docker0`.
- Ditemukan LAB-LAN `192.168.10.0/28` melalui `enp0s3`.
- Default route menggunakan gateway `192.168.10.1` melalui `enp0s3`.

### 5. Container-to-Host Connectivity Test

- Menjalankan temporary BusyBox container menggunakan:

```bash
sudo docker run --rm busybox ping -c 4 172.17.0.1
```

- Container berhasil melakukan ping terhadap gateway Docker `172.17.0.1`.
- Pengujian menghasilkan `0% packet loss`.
- Pengujian membuktikan bahwa container dapat berkomunikasi dengan Docker Host melalui Docker Bridge Network.

### 6. Container-to-Internet Connectivity Test

- Menjalankan temporary BusyBox container menggunakan:

```bash
sudo docker run --rm busybox ping -c 4 8.8.8.8
```

- Container berhasil melakukan ping terhadap `8.8.8.8`.
- Pengujian menghasilkan `0% packet loss`.
- Pengujian membuktikan bahwa container dapat mengakses Internet melalui Docker Host.

### 7. Docker NAT / MASQUERADE Verification

- Melakukan pengecekan NAT table menggunakan:

```bash
sudo iptables -t nat -L POSTROUTING -n -v
```

- Ditemukan rule `MASQUERADE` dengan source `172.17.0.0/16`.
- Rule tersebut menunjukkan bahwa Docker melakukan NAT/MASQUERADE terhadap traffic yang berasal dari network container ketika keluar menuju network eksternal.

---

## Docker Bridge Network

Default Docker Bridge Network menggunakan:

**Network:** `bridge`  
**Driver:** `bridge`  
**Subnet:** `172.17.0.0/16`  
**Gateway:** `172.17.0.1`

Docker Host menggunakan interface:

**`docker0` → `172.17.0.1/16`**

Container:

**`atlas-nginx` → `172.17.0.2/16`**

---

## Connectivity Flow

**Container → docker0 → Docker Host → NAT/MASQUERADE → Internet**

Pengujian pada sesi ini membuktikan:

**Container → Host:** ✅

**Container → Internet:** ✅

---

## Problems

- Saat mencoba melakukan pemeriksaan network dari dalam container menggunakan command `ip`, ditemukan error:

```text
OCI runtime exec failed: exec failed to start container process: exec: "ip": executable file not found in $PATH
```

- Hal tersebut terjadi karena utility `ip` tidak tersedia pada container/image yang digunakan.
- Pemeriksaan network kemudian dilakukan dari Docker Host menggunakan Docker inspection dan command networking pada Ubuntu Server.

---

## Lessons Learned

- Docker menyediakan beberapa network bawaan, termasuk `bridge`, `host`, dan `none`.
- Default Docker Bridge Network menggunakan subnet `172.17.0.0/16`.
- Gateway default Docker Bridge Network berada pada `172.17.0.1`.
- Interface `docker0` berfungsi sebagai bridge interface pada Docker Host.
- Container mendapatkan IP address dari Docker Bridge Network.
- Container dapat berkomunikasi dengan Docker Host melalui Docker bridge.
- Container dapat mengakses Internet melalui Docker Host.
- Docker menggunakan NAT/MASQUERADE untuk traffic dari network container menuju network eksternal.
- Tidak semua utility Linux tersedia pada setiap Docker image.

---

## Next Session

- Membuat User-Defined Docker Bridge Network.
- Memahami perbedaan default `bridge` dengan custom bridge network.
- Menguji komunikasi antar-container.
- Mempelajari Docker DNS dan name resolution.
- Mempelajari network isolation.
- Mempelajari multi-network container.

---

## Status

✅ Docker Network Verified

✅ Default Bridge Network Verified

✅ `docker0` Interface Verified

✅ Routing Table Verified

✅ Container-to-Host Connectivity Tested

✅ Container-to-Internet Connectivity Tested

✅ Docker NAT / MASQUERADE Verified

⬜ User-Defined Bridge Network

⬜ Container-to-Container Connectivity

⬜ Docker DNS Resolution

⬜ Network Isolation

⬜ Multi-Network Container