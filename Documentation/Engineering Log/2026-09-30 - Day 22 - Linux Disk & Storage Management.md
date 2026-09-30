# Engineering Log — Day 22
## Linux Disk & Storage Management

**Date:** 30 September 2026  
**Phase:** Linux Fundamentals  
**Milestone:** Disk & Storage Management  
**Status:** ✅ Completed

---

## Objective

Mempelajari struktur disk, filesystem, penggunaan storage, serta melakukan storage audit pada `SRV-UBU-01`.

---

## Activities

### 1. Disk & Partition Inspection

Menggunakan:

```bash
lsblk
```

Ditemukan:

```text
sda  → 25G disk
├── sda1 → 1M
└── sda2 → 25G → /
```

Filesystem utama:

```text
/dev/sda2 → ext4 → /
```

---

### 2. Filesystem Usage

Menggunakan:

```bash
df -h
df -Th
lsblk -f
```

Kondisi root filesystem:

```text
Size  : 25G
Used  : 4.5G
Avail : 19G
Use%  : 20%
Type  : ext4
```

Storage masih dalam kondisi normal dengan sekitar 19 GB tersedia.

---

### 3. Storage Audit

Menggunakan `du` untuk mengetahui directory yang menggunakan storage terbesar.

Hasil utama:

```text
/usr  → 3.2G
/var  → 1.3G
/boot → 180M
```

Pada `/var` ditemukan:

```text
/var/lib  → 719M
/var/log  → 403M
/var/cache → 156M
```

---

### 4. Docker & Containerd Storage

Storage Docker dan containerd diperiksa menggunakan:

```bash
sudo docker system df -v
```

serta:

```bash
sudo du -h --max-depth=1 /var/lib/docker
sudo du -h --max-depth=1 /var/lib/containerd
```

Hasil:

```text
/var/lib/docker      → 166M
/var/lib/containerd  → 329M
```

Docker memiliki 12 images dengan total penggunaan sekitar 342.1 MB. Beberapa image tidak sedang digunakan oleh container aktif.

Tidak dilakukan `docker system prune` karena image dan container merupakan bagian dari artefak pembelajaran Atlas.

---

### 5. Systemd Journal Storage

Penggunaan persistent journal diperiksa menggunakan:

```bash
sudo du -sh /var/log/journal
sudo journalctl --disk-usage
```

Hasil:

```text
/var/log/journal → 397M
journalctl       → 396.9M
```

Hasil menunjukkan bahwa systemd journal merupakan salah satu pengguna storage terbesar pada `/var`.

---

## Key Concepts

- `lsblk` — melihat struktur disk dan partition.
- `df` — melihat penggunaan filesystem.
- `du` — melihat penggunaan storage berdasarkan directory.
- `ext4` — filesystem yang digunakan pada root filesystem.
- `/var/lib` — menyimpan data state/persistent berbagai service.
- `/var/log` — menyimpan system dan service logs.
- Docker dan containerd memiliki storage masing-masing di bawah `/var/lib`.
- `journalctl --disk-usage` — melihat penggunaan storage systemd journal.

---

## Lessons Learned

Storage troubleshooting dimulai dari melihat penggunaan filesystem dengan `df`, kemudian mempersempit lokasi penggunaan menggunakan `du`.

Pada `SRV-UBU-01`, penggunaan storage terbesar ditemukan pada `/usr` dan `/var`. Di dalam `/var`, penggunaan terbesar berasal dari `containerd`, Docker, APT, dan persistent systemd journal.

---

## Problems

Tidak terdapat masalah teknis atau kondisi disk penuh.

Storage root filesystem masih sekitar 20% terpakai dengan sekitar 19 GB tersedia.

---

## Documentation

1. Disk Layout & Filesystem
2. Root Storage Breakdown
3. `/var` Storage Breakdown
4. Docker & Containerd Storage
5. Systemd Journal Storage

---

## Next Session

**Day 23 — Linux Storage Management & Log Maintenance**

---

## Status

**DAY 22 — COMPLETED ✅**