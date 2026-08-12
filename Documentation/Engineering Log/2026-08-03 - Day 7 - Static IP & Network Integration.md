# Atlas Project
## Engineering Log

---

# Day 7 — Static IP & Network Integration

**Date:** 3 August 2026

**Phase:** Infrastructure

**Milestone:** Ubuntu Server Network Integration

---

## Objective

Mengintegrasikan Ubuntu Server ke dalam jaringan LAB-LAN menggunakan konfigurasi IP statis serta memverifikasi komunikasi dengan router RTR-CORE-01.

---

## Activities

### 1. Network Configuration Planning

- Menentukan skema addressing untuk Ubuntu Server.
- Menggunakan alamat **192.168.10.2/28**.
- Menetapkan **192.168.10.1** sebagai Default Gateway.
- Menggunakan DNS:
  - 8.8.8.8
  - 1.1.1.1

---

### 2. Netplan Configuration

Melakukan backup konfigurasi Netplan.

```bash
sudo cp /etc/netplan/00-installer-config.yaml \
/etc/netplan/00-installer-config.yaml.bak
```

Mengedit file konfigurasi:

```text
/etc/netplan/00-installer-config.yaml
```

Mengubah konfigurasi DHCP menjadi Static IP.

---

### 3. Apply Configuration

Menerapkan konfigurasi menggunakan:

```bash
sudo netplan apply
```

---

### 4. Network Verification

Melakukan verifikasi menggunakan:

```bash
ip address
```

Hasil:

- Interface **enp0s3** berhasil memperoleh IP:

```text
192.168.10.2/28
```

---

### 5. Routing Verification

Melakukan pengecekan routing table.

```bash
ip route
```

Memastikan default route menuju:

```text
192.168.10.1
```

---

### 6. Connectivity Test

Melakukan pengujian konektivitas menuju router.

```bash
ping 192.168.10.1
```

Hasil:

- Ping berhasil.
- Packet Loss **0%**.
- Ubuntu Server berhasil berkomunikasi dengan RTR-CORE-01 melalui LAB-LAN.

---

## Current Topology

```text
                 ☁ Internet
                       │
               VirtualBox NAT
                       │
                 RTR-CORE-01
        ether2 (192.168.10.1/28)
                       │
      Internal Network (LAB-LAN)
                       │
      SRV-UBU-01 (Ubuntu Server)
         192.168.10.2/28
```

---

## Documentation

### Screenshots

| File | Description |
|------|-------------|
| Netplan Configuration.png | Konfigurasi Static IP menggunakan Netplan. |
| IP Address Verification.png | Verifikasi IP menggunakan `ip address`. |
| Routing Table.png | Hasil `ip route`. |
| Ping RTR-CORE-01.png | Hasil ping menuju gateway 192.168.10.1. |

### Topology

| File | Description |
|------|-------------|
| Topology-v7.png | Topologi setelah Ubuntu Server berhasil terintegrasi ke LAB-LAN. |
| Topology Atlas Project.drawio | File sumber topologi terbaru. |

---

## Problems

### YAML Configuration Error

Saat menjalankan:

```bash
sudo netplan apply
```

muncul error:

```text
Invalid YAML:
did not find expected '-' indicator
```

Penyebab:

- Kesalahan indentasi YAML.
- Salah penulisan keyword `routes`.

Solusi:

- Memperbaiki struktur indentasi YAML.
- Mengganti keyword menjadi `routes`.
- Menjalankan kembali `sudo netplan apply`.

Konfigurasi berhasil diterapkan setelah perbaikan.

---

## Lessons Learned

- Ubuntu Server menggunakan **Netplan** sebagai sistem konfigurasi jaringan.
- File YAML sangat sensitif terhadap indentasi dan struktur penulisan.
- Internal Network VirtualBox tidak menyediakan DHCP secara otomatis sehingga diperlukan konfigurasi IP statis.
- Default Gateway memungkinkan server mengirim paket ke luar subnet.
- Backup konfigurasi sebelum melakukan perubahan merupakan praktik yang baik.
- Verifikasi konfigurasi setelah perubahan membantu memastikan sistem berjalan sesuai harapan.

---

## Next Session

- Mengonfigurasi NAT pada RouterOS agar Ubuntu dapat mengakses Internet.
- Menguji konektivitas Internet dari Ubuntu Server.
- Melakukan update sistem menggunakan `apt update`.
- Mempersiapkan instalasi layanan pertama pada Ubuntu Server.

---

## Status

✅ Static IP Configured

✅ Gateway Configured

✅ DNS Configured

✅ Netplan Applied Successfully

✅ Routing Verified

✅ Ping RTR-CORE-01 Successful

✅ 0% Packet Loss

⬜ Internet Access

⬜ System Update

⬜ First Network Service