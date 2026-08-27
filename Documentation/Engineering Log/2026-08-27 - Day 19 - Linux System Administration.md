**# Atlas Project**

**## Engineering Log**

**---**

**# Day 19 — Linux Users, Groups & Permissions**

**\*\*Date:\*\*** 27 August 2026

**\*\*Phase:\*\*** Linux System Administration

**\*\*Milestone:\*\*** Understanding Linux Users, Groups, Ownership & Permissions

**---**

**## Objective**

Memulai fase Linux System Administration dengan mempelajari user, group, ownership, dan permission pada Linux.

Fokus pembelajaran:

\- Memahami UID dan GID.

\- Memahami primary dan supplementary group.

\- Memahami file ownership.

\- Memahami permission \`read\`, \`write\`, dan \`execute\`.

\- Memahami penggunaan \`chown\` dan \`chmod\`.

\- Menguji access control menggunakan user yang berbeda.

**---**

**## Activities**

**### 1. Linux User & Group**

User \`atlas\` diverifikasi menggunakan:

```bash
id atlas
getent passwd atlas
getent group atlas
```

Hasil menunjukkan:

```text
uid=1001(atlas) gid=1001(atlas) groups=1001(atlas)
```

User \`atlas\` memiliki UID 1001 dan primary group \`atlas\` dengan GID 1001.

Kemudian dibuat supplementary group:

```bash
sudo groupadd atlas-dev
sudo usermod -aG atlas-dev atlas
```

Membership diverifikasi menggunakan:

```bash
id atlas
getent group atlas-dev
```

Hasil:

```text
uid=1001(atlas) gid=1001(atlas) groups=1001(atlas),1002(atlas-dev)
```

Hal ini menunjukkan bahwa user dapat memiliki primary group dan supplementary group.

**---**

**### 2. File Ownership**

Direktori dan file untuk eksperimen dibuat:

```bash
sudo mkdir -p /opt/atlas
sudo touch /opt/atlas/config.txt
```

Ownership awal:

```text
-rw-r--r-- 1 root root ...
```

Ownership kemudian diubah:

```bash
sudo chown atlas:atlas /opt/atlas/config.txt
```

Hasil:

```text
-rw-r--r-- 1 atlas atlas ...
```

Eksperimen menunjukkan bahwa \`chown\` digunakan untuk menentukan owner dan group suatu file.

**---**

**### 3. File Permissions**

Permission file diubah menggunakan:

```bash
sudo chmod 640 /opt/atlas/config.txt
```

Hasil:

```text
-rw-r----- 1 atlas atlas ...
```

Permission tersebut berarti:

```text
Owner  → rw-
Group  → r--
Others → ---
```

Dengan:

```text
r = read
w = write
x = execute
```

**---**

**### 4. Permission Testing**

User \`atlas\` digunakan untuk membaca dan menulis file:

```bash
sudo -u atlas cat /opt/atlas/config.txt
sudo -u atlas sh -c 'echo "Atlas Permission Test" > /opt/atlas/config.txt'
```

Hasil penulisan berhasil dan menghasilkan:

```text
Atlas Permission Test
```

Kemudian dibuat user sementara:

```bash
sudo useradd -m tester
```

User \`tester\` mencoba membaca file:

```bash
sudo -u tester cat /opt/atlas/config.txt
```

Hasil:

```text
Permission denied
```

Hal ini membuktikan bahwa user yang tidak memiliki permission tidak dapat mengakses file.

**---**

**### 5. Cleanup**

User testing kemudian dihapus:

```bash
sudo userdel -r tester
```

Verifikasi:

```bash
getent passwd tester
```

Tidak terdapat output, sehingga user \`tester\` berhasil dihapus.

**---**

**## Key Concepts**

**### Users & Groups**

Linux menggunakan UID untuk user dan GID untuk group.

User dapat memiliki:

```text
Primary Group
Supplementary Groups
```

**### Ownership**

Setiap file memiliki owner dan group.

Contoh:

```text
-rw-r----- 1 atlas atlas ...
```

**### Permissions**

Permission dibagi menjadi:

```text
Owner
Group
Others
```

dengan operasi:

```text
r = read
w = write
x = execute
```

**### chown & chmod**

\`chown\` digunakan untuk mengubah ownership.

\`chmod\` digunakan untuk mengubah permission.

Contoh:

```bash
sudo chown atlas:atlas /opt/atlas/config.txt
sudo chmod 640 /opt/atlas/config.txt
```

**---**

**## Lessons Learned**

\- Linux menggunakan UID dan GID untuk identifikasi user dan group.

\- User dapat memiliki primary dan supplementary group.

\- \`id\` dan \`getent\` dapat digunakan untuk memeriksa user dan group.

\- File memiliki owner dan group.

\- \`chown\` digunakan untuk mengubah ownership.

\- \`chmod\` digunakan untuk mengatur permission.

\- Permission menentukan siapa yang dapat membaca, menulis, atau mengeksekusi resource.

\- User tanpa permission dapat memperoleh \`Permission denied\`.

Secara keseluruhan:

```text
User
 ↓
Group
 ↓
Ownership
 ↓
Permission
 ↓
Access
```

**---**

**## Problems**

Pada awal pembuatan user, user \`atlas\` ternyata sudah tersedia sehingga command \`useradd\` kedua menghasilkan pesan bahwa user telah ada.

Kondisi tersebut diverifikasi menggunakan \`id atlas\` dan \`getent\`.

Pada pengujian permission, user \`david\` dan \`tester\` tidak dapat membaca file karena permission \`640\` tidak memberikan akses kepada kategori \`others\`.

Kondisi tersebut merupakan hasil yang sesuai dengan konfigurasi permission.

**---**

**## Documentation**

**### Screenshots**

**\*\*01 - Atlas User & Groups.png\*\***

Menampilkan:

```text
id atlas
getent passwd atlas
getent group atlas-dev
```

Fokus pada UID, GID, primary group, dan supplementary group.

**---**

**\*\*02 - File Ownership.png\*\***

Menampilkan perubahan ownership:

```text
root:root
    ↓
atlas:atlas
```

menggunakan \`chown\`.

**---**

**\*\*03 - File Permissions.png\*\***

Menampilkan:

```text
-rw-r----- 1 atlas atlas ...
```

setelah penggunaan \`chmod 640\`.

**---**

**\*\*04 - Permission Test.png\*\***

Menampilkan:

```text
atlas → berhasil read/write
tester → Permission denied
```

serta verifikasi cleanup user \`tester\`.

**---**

**## Next Session**

**### Day 20 — Linux Permissions Part 2**

Materi akan dilanjutkan dengan:

\- Numeric dan symbolic permission.

\- Directory permissions.

\- Group-based access control.

\- Perbedaan permission file dan directory.

\- Penggunaan \`sudo\`.

**---**

**## Status**

✅ Linux User

✅ UID & GID

✅ Primary Group

✅ Supplementary Group

✅ \`/etc/passwd\`

✅ \`/etc/group\`

✅ File Ownership

✅ \`chown\`

✅ File Permissions

✅ \`chmod\`

✅ Permission Testing

✅ Permission Denied

✅ Cleanup

⏳ Directory Permissions

⏳ Advanced Permissions

⏳ Group-based Access Control