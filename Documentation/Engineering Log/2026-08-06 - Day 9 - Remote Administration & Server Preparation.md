# Atlas Project
## Engineering Log

---

# Day 9 — Remote Administration & Server Preparation

**Date:** 06 August 2026

**Phase:** Server Administration

**Milestone:** Remote Server Management

---

## Objective

Mengaktifkan remote administration pada Ubuntu Server menggunakan OpenSSH, menyiapkan server dengan tools administrasi dasar, serta membangun jalur Management Network antara Windows Host dan Ubuntu Server.

---

## Activities

### 1. OpenSSH Server

Melakukan instalasi OpenSSH Server pada Ubuntu.

Memastikan service SSH aktif menggunakan:

```bash
sudo systemctl status ssh
```

Status:

- Active (running)

Mengaktifkan service agar otomatis berjalan setiap sistem boot.

```bash
sudo systemctl enable --now ssh
```

---

### 2. Remote Administration

Melakukan pengujian remote login dari Windows Host menggunakan Windows Terminal.

```bash
ssh david@192.168.56.103
```

Hasil:

- SSH Authentication berhasil.
- Fingerprint host berhasil disimpan.
- Remote login berhasil dilakukan.
- Ubuntu Server kini dapat dikelola tanpa membuka VirtualBox Console.

---

### 3. Management Network

Menambahkan interface kedua pada Ubuntu Server.

Interface:

- **enp0s3**
  - Internal Network (LAB-LAN)
  - 192.168.10.2/28

- **enp0s8**
  - Host-Only Network
  - 192.168.56.103/24

Management Network digunakan sebagai jalur administrasi server dari Windows Host tanpa mengganggu jaringan produksi (LAB-LAN).

---

### 4. Essential Server Tools

Menginstal tools administrasi dasar.

Installed Packages:

- Git
- Curl
- Wget
- Vim
- Tree
- Htop
- Net-tools

Tools tersebut dipersiapkan sebagai kebutuhan administrasi dan deployment pada tahap berikutnya.

---

### 5. Basic Linux Administration

Melakukan pengenalan beberapa perintah administrasi Linux.

Command yang digunakan:

```bash
hostnamectl
uptime
free -h
df -h
lsblk
ip address
ip route
systemctl list-units --type=service --state=running
```

---

## Current Topology

Internet

↓

VirtualBox NAT

↓

RTR-CORE-01

↓

Internal Network (LAB-LAN)

↓

UBU-SRV-01

├── enp0s3 → 192.168.10.2/28 (Production Network)

└── enp0s8 → 192.168.56.103/24 (Management Network)

↓

Windows Host

↓

SSH Remote Access (Port 22)

---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| SSH Service Running.png | Status service OpenSSH (Active: running). |
| SSH Auto Start Enabled.png | Service SSH berhasil diaktifkan saat boot. |
| SSH Login from Windows.png | Remote login dari Windows Terminal menggunakan SSH. |
| Essential Tools Installed.png | Verifikasi tools administrasi dasar berhasil diinstal. |
| Basic Linux Administration.png | Penggunaan command administrasi Linux. |

### Topology

| File | Description |
|------|-------------|
| Topology-v8.png | Penambahan Management Network dan jalur SSH Remote Administration. |
| Topology Atlas Project.drawio | Versi terbaru diagram topologi. |

---

## Problems

### 1. SSH Connection Timeout

Pada awal pengujian, koneksi SSH mengalami timeout.

Penyebab:

Ubuntu Server hanya terhubung ke **Internal Network (LAB-LAN)** sehingga Windows Host tidak memiliki jalur menuju alamat 192.168.10.2.

Solusi:

- Menambahkan **Host-Only Adapter** sebagai interface kedua.
- Menggunakan alamat IP Management Network (192.168.56.103) untuk remote administration.

---

### 2. VirtualBox Network Configuration

Sempat mengira Adapter 2 pada VirtualBox hilang.

Penyebab:

VirtualBox berada pada tampilan **Basic Mode**, sehingga hanya Adapter 1 yang ditampilkan.

Solusi:

Mengubah tampilan kembali ke **Expert Mode**.

---

## Lessons Learned

- OpenSSH memungkinkan server Linux dikelola secara remote tanpa menggunakan VirtualBox Console.
- `systemctl enable --now` menjalankan service sekaligus mengaktifkannya saat proses boot.
- Internal Network hanya dapat diakses oleh virtual machine yang berada pada jaringan yang sama.
- Host-Only Network memungkinkan komunikasi langsung antara Host dan Virtual Machine.
- Memisahkan Production Network dan Management Network merupakan praktik yang umum digunakan pada lingkungan enterprise untuk meningkatkan keamanan dan mempermudah administrasi.
- VirtualBox Console kini berfungsi sebagai emergency console apabila akses SSH tidak tersedia.

---

## Next Session

- Instalasi Docker Engine.
- Memahami arsitektur Docker.
- Verifikasi Docker Service.
- Menjalankan container pertama.

---

## Status

✅ OpenSSH Installed

✅ SSH Service Running

✅ SSH Auto Start Enabled

✅ Remote Login Successful

✅ Management Network Created

✅ Essential Tools Installed

✅ Basic Linux Administration

⬜ Docker Engine

⬜ First Container Deployment