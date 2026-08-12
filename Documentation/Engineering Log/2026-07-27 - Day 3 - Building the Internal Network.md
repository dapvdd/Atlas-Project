# Atlas Project
## Engineering Log

---

# Day 3 — Building the Internal Network

**Date:** 27 July 2026

**Phase:** Foundation

**Milestone:** Internal Network Initialization

---

## Objective

Menambahkan jalur LAN pada router serta memahami konsep interface, WAN, LAN, dan Internal Network pada VirtualBox.

---

## Activities

### 1. Internal Network Configuration

- Menambahkan **Adapter 2** pada virtual machine **RTR-CORE-01**.
- Menggunakan **Internal Network** sebagai tipe koneksi.
- Membuat jaringan virtual dengan nama **LAB-LAN**.

### 2. Router Interface Identification

- Melakukan boot RouterOS.
- Menggunakan perintah:

```bash
/interface print
```

- Mengidentifikasi interface yang dimiliki router.

### 3. Interface Analysis

Interface yang terdeteksi:

- **ether1**
- **ether2**
- **loopback (lo)**

### 4. Router Identity Configuration

- Mengubah identitas router menggunakan perintah:

```bash
/system identity set name=RTR-CORE-01
```

- Memverifikasi perubahan identitas menggunakan:

```bash
/ system identity print
```

---

## Current Topology


                 ☁ Internet
                       │
               VirtualBox NAT
                       │
                Adapter 1 (NAT)
                       │
                RTR-CORE-01
                       │
      Adapter 2 (Internal Network)
                       │
      Internal Network (LAB-LAN)
                       │
           Future Infrastructure
     (Ubuntu Server, Windows Server, etc.)


---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| Adapter 2 Configuration.png | Konfigurasi Adapter 2 menggunakan Internal Network (LAB-LAN). |
| Router Interfaces.png | Output perintah `/interface print` yang menampilkan interface RouterOS. |

### Topology

| File | Description |
|------|-------------|
| Topology-v3.png | Topologi Atlas Project setelah konfigurasi Internal Network. |
| Topology.drawio | Versi terbaru diagram topologi. |

---

## Problems

- Tidak ada kendala teknis selama proses konfigurasi.

---

## Lessons Learned

- Router memiliki beberapa interface yang masing-masing memiliki fungsi berbeda.
- **ether1** digunakan sebagai jalur **WAN** menuju Internet melalui VirtualBox NAT.
- **ether2** digunakan sebagai jalur **LAN** menuju jaringan Internal Network (LAB-LAN).
- **Loopback (lo)** merupakan interface internal milik router dan bukan merupakan koneksi fisik ke jaringan lain.
- Internal Network pada VirtualBox memungkinkan beberapa virtual machine saling berkomunikasi tanpa terhubung langsung ke jaringan fisik.

---

## Next Session

- Memberikan IP Address pada interface router.
- Mengonfigurasi interface ether1 dan ether2.
- Melakukan pengujian konektivitas dasar.
- Mempersiapkan koneksi untuk server pertama.

---

## Status

✅ Adapter 2 Configured

✅ Internal Network (LAB-LAN) Created

✅ Router Interface Identified

✅ WAN and LAN Concept Understood

⬜ IP Address Configuration

⬜ First Server Deployment