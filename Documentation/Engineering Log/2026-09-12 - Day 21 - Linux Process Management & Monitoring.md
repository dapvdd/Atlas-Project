# Engineering Log — Day 21
## Linux Process Management & Monitoring

**Date:** 12 September 2026  
**Phase:** Linux Fundamentals  
**Milestone:** Process Management & Monitoring  
**Status:** ✅ Completed

---

## Objective

Mempelajari konsep Linux process dan melakukan monitoring serta management process secara langsung menggunakan `ps`, `pstree`, `top`, dan `kill`.

---

## Activities

### 1. Process Inspection

Melihat daftar process yang sedang berjalan:

```bash
ps aux | head -20
```

Ditemukan beberapa process penting seperti:

- `systemd` — PID 1
- `sshd` — PID 880
- `containerd` — PID 908
- `dockerd` — PID 1225

---

### 2. Docker & SSH Process

Melakukan filtering process Docker dan SSH:

```bash
ps aux | grep -E 'dockerd|containerd' | grep -v grep
```

```bash
ps aux | grep sshd | grep -v grep
```

Hasil menunjukkan bahwa service Docker dan SSH yang dipelajari pada Day 20 memiliki process yang aktif di sistem.

---

### 3. Parent & Child Process

Menggunakan:

```bash
pstree -p
```

Untuk melihat hubungan antar-process.

Contoh hierarki SSH:

```text
sshd(880)
└── sshd-session(1958)
    └── sshd-session(2034)
        └── bash(2035)
```

Hubungan PID dan PPID kemudian diverifikasi menggunakan:

```bash
ps -o pid,ppid,user,stat,cmd -p 1225,880,1958,2034,2035
```

---

### 4. Real-Time Monitoring

Menggunakan:

```bash
top
```

Ditemukan kondisi server:

- 132 total tasks
- 2 running
- 130 sleeping
- 0 stopped
- 0 zombie
- CPU sekitar 97% idle
- Memory available sekitar 1.23 GiB

---

### 5. Process Lifecycle & Signal

Membuat process yang menggunakan CPU:

```bash
yes > /dev/null &
```

Process kemudian terdeteksi dengan:

```bash
ps aux | grep '[y]es'
```

Process `yes` mencapai penggunaan CPU hingga sekitar 100%.

Process kemudian dihentikan menggunakan SIGTERM:

```bash
kill -TERM <PID>
```

Kemudian diuji kembali menggunakan SIGKILL:

```bash
kill -KILL <PID>
```

Kedua process berhasil dihentikan dan diverifikasi menggunakan:

```bash
ps -p <PID>
```

---

## Key Concepts

- **PID** — identitas unik sebuah process.
- **PPID** — PID dari parent process.
- **Process State** — kondisi process seperti Running (`R`), Sleeping (`S`), dan Idle (`I`).
- **`ps`** — menampilkan informasi process.
- **`pstree`** — menampilkan hierarki parent-child process.
- **`top`** — monitoring process secara realtime.
- **SIGTERM (15)** — meminta process berhenti secara normal.
- **SIGKILL (9)** — menghentikan process secara paksa.

---

## Lessons Learned

Process merupakan bagian fundamental dari sistem Linux. Service yang dikelola menggunakan `systemd` pada Day 20 dapat diamati lebih lanjut sebagai process melalui PID dan process hierarchy.

Eksperimen `yes` juga menunjukkan bagaimana process dapat dimonitor berdasarkan penggunaan CPU dan dihentikan menggunakan signal.

---

## Problems

Tidak ada masalah teknis yang berdampak pada sistem.

Terdapat satu kesalahan kecil ketika teks `----------------------` ikut dimasukkan ke terminal sehingga muncul:

```text
command not found
```

Tidak ada dampak terhadap sistem.

---

## Documentation

Screenshot yang disimpan:

1. **Process Overview** — output `ps aux`
2. **Docker & SSH Processes** — process `dockerd`, `containerd`, dan `sshd`
3. **Process Tree** — output `pstree -p`
4. **Process Monitoring** — output `top`
5. **CPU Intensive Process** — process `yes` dengan CPU tinggi
6. **Process Termination** — hasil SIGTERM dan SIGKILL

---

## Next Session

**Day 22 — Linux Process & Resource Management**

Materi berikutnya dapat dilanjutkan ke process priority, resource monitoring, dan troubleshooting process.

---

## Status

**DAY 21 — COMPLETED ✅**