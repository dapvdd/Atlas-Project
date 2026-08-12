# Atlas Project
## Engineering Log

---

# Day 8 — Internet Access & System Update

**Date:** 5 August 2026

**Phase:** Infrastructure

**Milestone:** Internet Access Verification

---

## Objective

Mengaktifkan akses Internet pada Ubuntu Server melalui RTR-CORE-01 menggunakan NAT (Masquerade) serta memverifikasi konektivitas melalui pengujian jaringan dan pembaruan sistem.

---

## Activities

### 1. NAT Configuration

Melakukan pengecekan konfigurasi NAT pada RouterOS.

```bash
/ip firewall nat print
```

Menambahkan NAT Rule apabila belum tersedia.

```bash
/ip firewall nat add \
chain=srcnat \
out-interface=ether1 \
action=masquerade
```

NAT digunakan agar seluruh perangkat pada jaringan LAB-LAN dapat mengakses Internet menggunakan interface WAN router.

---

### 2. DNS Configuration

Melakukan konfigurasi DNS RouterOS.

```bash
/ip dns set servers=8.8.8.8,1.1.1.1 allow-remote-requests=yes
```

---

### 3. Router Connectivity Test

Melakukan pengujian konektivitas Internet dari RouterOS.

```bash
ping 8.8.8.8
```

Hasil:

- Ping berhasil.
- Packet Loss 0%.

---

### 4. Ubuntu Internet Connectivity Test

Melakukan pengujian konektivitas dari Ubuntu Server.

```bash
ping 8.8.8.8
```

Hasil:

- Ubuntu Server berhasil mengakses Internet melalui RTR-CORE-01.
- Packet Loss 0%.

---

### 5. DNS Resolution Test

Melakukan pengujian resolusi nama domain.

```bash
ping google.com
```

Hasil:

- DNS berhasil melakukan resolusi domain.
- Ubuntu dapat mengakses host berdasarkan nama domain.

---

### 6. System Update

Melakukan pembaruan daftar repository.

```bash
sudo apt update
```

Repository Ubuntu berhasil diakses dan daftar paket berhasil diperbarui.

---

## Current Topology

```text
                 ☁ Internet
                       │
               VirtualBox NAT
                       │
                 RTR-CORE-01
          ether1 (WAN - NAT)
                       │
        NAT (Masquerade Enabled)
                       │
      ether2 (192.168.10.1/28)
                       │
      Internal Network (LAB-LAN)
                       │
      UBU-SRV-01 (Ubuntu Server)
          192.168.10.2/28

Internet Connectivity ✔
```

---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| NAT Rule.png | Konfigurasi NAT (Masquerade) pada RouterOS. |
| Router Internet Test.png | Ping 8.8.8.8 dari RTR-CORE-01. |
| Ubuntu Internet Test.png | Ping 8.8.8.8 dari Ubuntu Server. |
| DNS Resolution Test.png | Pengujian ping menggunakan nama domain. |
| apt update.png | Hasil pembaruan repository Ubuntu. |

### Topology

| File | Description |
|------|-------------|
| Topology-v8.png | Topologi setelah Ubuntu memperoleh akses Internet. |
| Topology Atlas Project.drawio | Versi terbaru diagram topologi. |

---

## Problems

- Tidak ditemukan kendala selama proses konfigurasi.
- Seluruh konfigurasi berhasil diterapkan pada percobaan pertama.

---

## Lessons Learned

- NAT (Masquerade) memungkinkan jaringan privat mengakses Internet melalui satu alamat IP publik.
- DNS diperlukan agar server dapat mengakses layanan menggunakan nama domain.
- Pengujian konektivitas sebaiknya dilakukan secara bertahap:
  1. Ping Gateway.
  2. Ping Public IP.
  3. Ping Domain Name.
- `apt update` merupakan salah satu metode verifikasi bahwa routing, NAT, dan DNS telah berjalan dengan baik.

---

## Next Session

- Mengaktifkan OpenSSH Server.
- Melakukan remote access ke Ubuntu Server dari host.
- Mengenal administrasi dasar Linux.
- Mempersiapkan deployment layanan pertama.

---

## Status

✅ NAT Configured

✅ DNS Configured

✅ Router Internet Access Verified

✅ Ubuntu Internet Access Verified

✅ DNS Resolution Successful

✅ apt update Completed

⬜ OpenSSH Server

⬜ Remote Administration

⬜ First Service Deployment