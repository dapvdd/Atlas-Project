# Atlas Project
## Engineering Log

---

# Day 2 — Birth of the Core Router

**Date:** 26 July 2026

---

## Objective

Membangun virtual machine pertama sebagai router inti (core router) Atlas Project serta memahami konsep dasar penggunaan VirtualBox NAT dan Virtual Disk (VDI).

---

## Activities

### 1. Core Router Creation

- Membuat virtual machine pertama dengan nama **RTR-CORE-01**.
- Menggunakan **MikroTik Cloud Hosted Router (CHR)** sebagai sistem operasi router.
- Menyiapkan spesifikasi virtual machine sesuai kebutuhan RouterOS.

### 2. Virtual Disk Configuration

- Menggunakan file **CHR.vdi** sebagai virtual hard disk.
- Tidak menggunakan file ISO karena RouterOS sudah tersedia dalam bentuk Virtual Disk (VDI) yang siap dijalankan.

### 3. Network Adapter Configuration

- Mengonfigurasi **Adapter 1** menggunakan **VirtualBox NAT**.
- Adapter 1 berfungsi sebagai jalur **WAN** menuju Internet.
- Adapter 2 belum dikonfigurasi dan akan digunakan sebagai koneksi menuju jaringan internal (LAB-LAN) pada sesi berikutnya.

### 4. First Boot

- Berhasil melakukan boot pertama RouterOS.
- Berhasil mencapai halaman login MikroTik tanpa kendala.

---

## Current Topology

Internet
    │
VirtualBox NAT
    │
Adapter 1 (NAT)
    │
RTR-CORE-01
    │
Adapter 2 (Not Configured)
    │
Internal Network (LAB-LAN)
    │
Future Infrastructure
(Ubuntu Server, Windows Server, etc.)

---

## Documentation

### Screenshots

- VirtualBox Manager
- RTR-CORE-01 Settings
- MikroTik First Boot

### Topology

- Topology v2

---

## Problems

- Pada awal proses sempat mengira MikroTik CHR harus diinstal menggunakan file ISO.
- Setelah dipelajari, diketahui bahwa CHR telah disediakan dalam bentuk Virtual Disk (VDI) sehingga tidak memerlukan proses instalasi seperti sistem operasi pada umumnya.

---

## Lessons Learned

- File **VDI (Virtual Disk Image)** merupakan hard disk virtual yang telah berisi sistem operasi dan siap digunakan sebagai media boot virtual machine.
- Tidak semua sistem operasi virtual memerlukan instalasi menggunakan file ISO.
- VirtualBox NAT berfungsi sebagai koneksi awal menuju Internet (WAN) bagi router virtual.
- Sebuah router pada umumnya memiliki minimal dua interface:
  - WAN → menuju Internet.
  - LAN → menuju jaringan internal.
- Pada tahap ini hanya interface WAN yang telah dikonfigurasi, sedangkan interface LAN akan dibuat pada sesi berikutnya menggunakan **Internal Network (LAB-LAN)**.

---

## Next Session

- Menambahkan Adapter 2 menggunakan Internal Network.
- Mengenal interface yang dimiliki RouterOS.
- Memahami konsep WAN dan LAN pada router.
- Memulai konfigurasi dasar RouterOS.

---

## Status

✅ RTR-CORE-01 Successfully Created

✅ First Boot Completed

✅ WAN Interface (Adapter 1 - NAT) Ready

⏳ LAN Interface (Adapter 2 - Internal Network)

⬜ Basic Router Configuration