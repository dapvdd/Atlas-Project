# Atlas Project
## Engineering Log

---

# Day 6 — First Server Deployment

**Date:** 2 August 2026

**Phase:** Infrastructure

**Milestone:** Ubuntu Server Deployment

---

## Objective

Melakukan deployment server pertama pada Atlas Project menggunakan Ubuntu Server serta mengintegrasikan server ke dalam jaringan Internal Network (LAB-LAN).

---

## Activities

### 1. Virtual Machine Creation

- Membuat virtual machine baru dengan nama **SRV-UBU-01**.
- Menggunakan **Ubuntu Server 26.04 LTS** sebagai sistem operasi.
- Mengalokasikan resource virtual machine sesuai kebutuhan server.

### 2. Virtual Hardware Configuration

- Menggunakan Virtual Hard Disk (VDI) sebagai media penyimpanan.
- Menghubungkan optical drive ke file ISO Ubuntu Server.
- Mengonfigurasi **Network Adapter** menggunakan **Internal Network (LAB-LAN)**.

### 3. Ubuntu Server Installation

- Melakukan instalasi Ubuntu Server hingga selesai.
- Membuat akun administrator.
- Mengatur hostname menjadi **SRV-UBU-01**.

### 4. First Boot

- Berhasil melakukan boot pertama.
- Berhasil login menggunakan akun yang telah dibuat.
- Memastikan sistem operasi berjalan dengan normal.

### 5. Network Verification

Melakukan pengecekan interface jaringan menggunakan perintah:

```bash
ip address
```

Hasil verifikasi:

- Interface **enp0s3** berhasil terdeteksi.
- Interface berada dalam status **UP**.
- Belum memperoleh alamat **IPv4** karena belum dikonfigurasi IP statis maupun DHCP.
- Interface hanya memiliki alamat **IPv6 Link-Local**, yang merupakan kondisi normal pada jaringan Internal Network tanpa DHCP Server.

---

## Current Topology

```text
                 ☁ Internet
                       │
               VirtualBox NAT
                       │
                 RTR-CORE-01
                       │
        ether2 (192.168.10.1/28)
                       │
      Internal Network (LAB-LAN)
                       │
               SRV-UBU-01
             (Ubuntu Server)
```

---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| SRV-UBU-01 VM Settings.png | Konfigurasi virtual machine Ubuntu Server. |
| Ubuntu Server Installation.png | Proses instalasi Ubuntu Server. |
| SRV-UBU-01 First Login.png | Login pertama setelah instalasi berhasil. |
| SRV-UBU-01 Network Verification.png | Hasil perintah `ip address` untuk memverifikasi interface jaringan. |

### Topology

| File | Description |
|------|-------------|
| Topology-v6.png | Topologi Atlas Project setelah deployment Ubuntu Server. |
| Topology Atlas Project.drawio | File sumber topologi yang diperbarui pada Day 6. |

---

## Problems

### Slow Boot During Network Initialization

Pada proses boot awal, Ubuntu membutuhkan waktu lebih lama karena service:

```text
systemd-networkd-wait-online.service
```

menunggu konfigurasi jaringan selesai.

Hal ini terjadi karena server telah menggunakan **Internal Network (LAB-LAN)**, namun belum memiliki konfigurasi **IPv4**, **Gateway**, maupun **DHCP Server**.

---

## Lessons Learned

- Ubuntu Server dapat dijalankan sepenuhnya melalui terminal tanpa graphical interface.
- Interface jaringan Ubuntu menggunakan penamaan modern seperti **enp0s3**, bukan **eth0**.
- Internal Network VirtualBox tidak menyediakan layanan DHCP secara otomatis.
- Sebuah server tetap dapat melakukan boot meskipun belum memiliki alamat IPv4.
- Perintah `ip address` merupakan metode standar untuk memverifikasi interface jaringan pada distribusi Linux modern.

---

## Next Session

- Memberikan IP Address statis pada SRV-UBU-01.
- Mengonfigurasi Default Gateway menuju RTR-CORE-01.
- Mengonfigurasi DNS Server.
- Melakukan pengujian konektivitas menggunakan `ping`.
- Memastikan komunikasi antara Ubuntu Server dan RouterOS berhasil.

---

## Status

✅ Ubuntu Server Installed

✅ SRV-UBU-01 Deployed

✅ First Login Successful

✅ Connected to LAB-LAN

✅ Network Interface Detected

⬜ Static IP Configuration

⬜ Gateway Configuration

⬜ Connectivity Test