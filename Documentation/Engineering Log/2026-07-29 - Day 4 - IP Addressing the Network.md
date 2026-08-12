# Atlas Project
## Engineering Log

---

# Day 4 — IP Addressing the Network

**Date:** 29 July 2026

**Phase:** Foundation

**Milestone:** Router Interface IP Configuration

---

## Objective

Mengonfigurasi IP Address pada interface LAN router serta memahami konsep addressing pada jaringan internal Atlas Project.

---

## Activities

### 1. IP Address Planning

- Menentukan skema IP Address untuk jaringan **LAB-LAN**.
- Menggunakan subnet **192.168.10.0/28** sebagai jaringan internal pertama Atlas Project.
- Menetapkan **192.168.10.1/28** sebagai IP Address untuk interface LAN router.

### 2. IP Address Configuration

Menambahkan IP Address pada interface **ether2** menggunakan perintah:

```bash
/ip address add address=192.168.10.1/28 interface=ether2
```

### 3. Configuration Verification

Memverifikasi konfigurasi menggunakan perintah:

```bash
/ip address print
```

Hasil verifikasi menunjukkan:

- Interface **ether2** berhasil dikonfigurasi.
- IP Address **192.168.10.1/28** telah aktif.
- Network Address terdeteksi sebagai **192.168.10.0/28**.

---

## Current Topology

```text
                 ☁ Internet
                       │
               VirtualBox NAT
                       │
                Adapter 1 (NAT)
                       │
                 RTR-CORE-01
                       │
        ether2 (192.168.10.1/28)
                       │
      Adapter 2 (Internal Network)
                       │
      Internal Network (LAB-LAN)
                       │
           Future Infrastructure
     (Ubuntu Server, Windows Server, etc.)
```

---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| RTR-CORE-01 IP Address Configuration.png | Konfigurasi IP Address pada interface ether2. |
| RTR-CORE-01 IP Address Print.png | Verifikasi konfigurasi menggunakan perintah `/ip address print`. |

### Topology

| File | Description |
|------|-------------|
| Topology-v4.png | Topologi Atlas Project setelah konfigurasi IP Address pada jaringan LAB-LAN. |
| Topology Atlas Project.drawio | File sumber topologi yang diperbarui untuk Day 4. |

---

## Problems

- Tidak ditemukan kendala selama proses konfigurasi maupun verifikasi IP Address.

---

## Lessons Learned

- Setiap interface router dapat memiliki IP Address yang berbeda sesuai fungsi jaringan.
- IP Address dikonfigurasi pada **interface**, bukan pada Internal Network VirtualBox.
- Prefix **/28** menyediakan 16 alamat IP dengan 14 host yang dapat digunakan.
- Interface **ether2** akan berfungsi sebagai **default gateway** bagi perangkat pada jaringan LAB-LAN.
- Perintah `/ip address print` digunakan untuk memverifikasi konfigurasi addressing pada RouterOS.

---

## Next Session

- Mengonfigurasi interface WAN (ether1).
- Menguji konektivitas dasar menggunakan `ping`.
- Melakukan konfigurasi routing dasar.
- Mempersiapkan deployment server pertama pada LAB-LAN.

---

## Status

✅ IP Address Planning Completed

✅ ether2 Configured (192.168.10.1/28)

✅ IP Address Verified

✅ LAB-LAN Gateway Created

⬜ Basic Connectivity Test

⬜ First Server Deployment