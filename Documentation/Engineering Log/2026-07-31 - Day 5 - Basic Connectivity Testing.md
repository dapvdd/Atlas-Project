# Atlas Project
## Engineering Log

---

# Day 5 — Basic Connectivity Testing

**Date:** 31 July 2026

**Phase:** Foundation

**Milestone:** Internet Connectivity Verification

---

## Objective

Memverifikasi konfigurasi jaringan dasar pada router serta memastikan konektivitas menuju Internet melalui interface WAN sebelum melakukan deployment server pertama.

---

## Activities

### 1. Interface Verification

- Memastikan seluruh interface router berada pada kondisi aktif.
- Melakukan pengecekan interface menggunakan RouterOS untuk memastikan **ether1** dan **ether2** beroperasi dengan normal.

### 2. IP Address Verification

Memverifikasi konfigurasi IP Address pada router menggunakan perintah:

```bash
/ip address print
```

Hasil verifikasi:

- Interface **ether2** menggunakan IP Address **192.168.10.1/28**.
- Network Address terdeteksi sebagai **192.168.10.0/28**.

### 3. Internet Connectivity Test

Melakukan pengujian konektivitas menuju Internet menggunakan perintah:

```bash
/ping 8.8.8.8
```

Hasil pengujian:

- Ping berhasil.
- Packet Loss **0%**.
- Router berhasil mengakses Internet melalui **VirtualBox NAT**.

---

## Current Topology

```text
                 ☁ Internet
                       │
                Ping Success
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
| RTR-CORE-01 Interface Status.png | Verifikasi status interface pada RouterOS. |
| RTR-CORE-01 IP Address Verification.png | Verifikasi konfigurasi IP Address menggunakan `/ip address print`. |
| RTR-CORE-01 Ping Test.png | Hasil pengujian konektivitas Internet menggunakan `/ping 8.8.8.8`. |

### Topology

| File | Description |
|------|-------------|
| Topology-v5.png | Topologi Atlas Project setelah verifikasi konektivitas Internet. |
| Topology Atlas Project.drawio | File sumber topologi yang diperbarui pada Day 5. |

---

## Problems

- Tidak ditemukan kendala selama proses verifikasi maupun pengujian konektivitas.

---

## Lessons Learned

- Interface WAN (**ether1**) berhasil memperoleh akses Internet melalui VirtualBox NAT.
- Interface LAN (**ether2**) tetap berfungsi sebagai gateway jaringan internal dengan alamat **192.168.10.1/28**.
- Perintah `/ping` digunakan untuk memverifikasi konektivitas jaringan menuju host lain.
- Pengujian konektivitas merupakan langkah penting sebelum melakukan deployment server atau layanan jaringan.
- Verifikasi konfigurasi sebelum menambahkan perangkat baru dapat mempermudah proses troubleshooting.

---

## Next Session

- Melakukan deployment Ubuntu Server sebagai server pertama pada Atlas Project.
- Menghubungkan Ubuntu Server ke jaringan LAB-LAN.
- Memberikan IP Address pada Ubuntu Server.
- Melakukan pengujian konektivitas antara Ubuntu Server dan RTR-CORE-01.

---

## Status

✅ Interface Verification Completed

✅ IP Address Verified

✅ Internet Connectivity Verified

✅ Ping Test Successful (0% Packet Loss)

⬜ Ubuntu Server Deployment

⬜ Internal Network Connectivity Test