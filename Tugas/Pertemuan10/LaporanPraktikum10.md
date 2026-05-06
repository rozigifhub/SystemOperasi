# LAPORAN PRAKTIKUM SISTEM OPERASI

## WEEK 10 – MANAJEMEN MEMORI & SYSTEM CALL

---

## 1. Tujuan Praktikum

1. Memahami arsitektur memori pada Linux.
2. Memahami konsep memori virtual dan paging.
3. Mengelola serta mengonfigurasi swap space.
4. Melakukan monitoring penggunaan memori.
5. Memahami konsep system call pada Linux.
6. Mengamati interaksi antara user space dan kernel space.

---

## 2. Dasar Teori

Linux menggunakan sistem memori virtual, di mana setiap proses memiliki ruang alamat virtual sendiri yang diatur oleh kernel. Struktur memori proses terdiri dari text segment, data segment, heap, stack, dan kernel space. 

Memori virtual memungkinkan program menggunakan kapasitas memori lebih besar dari RAM fisik dengan bantuan swap space pada disk. Aktivitas perpindahan page antara RAM dan disk disebut paging. 

Swap digunakan sebagai perpanjangan RAM untuk mencegah sistem crash saat memori penuh, meskipun performanya lebih lambat dibanding RAM. Linux juga menyediakan parameter `swappiness` untuk mengatur agresivitas penggunaan swap. 

System call merupakan mekanisme komunikasi antara user space dan kernel space. Program tidak dapat langsung mengakses hardware sehingga harus menggunakan system call seperti `open`, `read`, `write`, dan `close`. 

---

## 3. Langkah-Langkah Praktikum

### 3.1 Melihat Penggunaan Memori

Menampilkan ringkasan penggunaan RAM dan swap:

```bash id="a1m10"
free -h
```

Melihat detail memori kernel:

```bash id="b2m10"
cat /proc/meminfo | head -n 20
```

---

### 3.2 Mengamati Aktivitas Paging

Menampilkan statistik memori virtual:

```bash id="c3m10"
vmstat 1 5
```

Kolom penting:

* `si` : swap in
* `so` : swap out

---

### 3.3 Membuat dan Mengonfigurasi Swap File

Membuat swap file 512MB:

```bash id="d4m10"
sudo fallocate -l 512M /swapfile-week10
```

Mengatur permission:

```bash id="e5m10"
sudo chmod 600 /swapfile-week10
```

Memformat dan mengaktifkan swap:

```bash id="f6m10"
sudo mkswap /swapfile-week10
sudo swapon /swapfile-week10
```

Verifikasi swap:

```bash id="g7m10"
swapon --show
free -h
```

Mengubah nilai swappiness:

```bash id="h8m10"
cat /proc/sys/vm/swappiness
sudo sysctl vm.swappiness=10
cat /proc/sys/vm/swappiness
```

---

### 3.4 Monitoring Penggunaan Memori

Melihat proses dengan penggunaan memori terbesar:

```bash id="i9m10"
ps aux --sort=-%mem | head
```

Monitoring real-time:

```bash id="j10m10"
top
```

---

### 3.5 Membuat Script Monitor Memori

Membuat file:

```bash id="k11m10"
nano monitor-memori.sh
```

Isi script:

```bash id="l12m10"
#!/bin/bash
set -euo pipefail

THRESHOLD=20

echo "=== Monitor Memori ==="
date
echo

free -h
echo

AVAIL=$(free | awk '/Mem/ {printf "%d", $7/$2*100}')

if [ "$AVAIL" -lt "$THRESHOLD" ]; then
    echo "PERINGATAN: Memori tersedia hanya ${AVAIL}%!"
else
    echo "Status: Memori tersedia ${AVAIL}% (normal)"
fi

echo
echo "--- 5 Proses Memori Tertinggi ---"
ps aux --sort=-%mem | head -n 6 | tail -n 5
```

Menjalankan script:

```bash id="m13m10"
chmod +x monitor-memori.sh
bash monitor-memori.sh
```

---

### 3.6 Studi Kasus Permission File

Membuat file konfigurasi:

```bash id="n14m10"
mkdir -p ~/praktikum-os/week10-memory/syscall-case
cd ~/praktikum-os/week10-memory/syscall-case

echo "PORT=8080" > app.conf
```

Menghapus permission:

```bash id="o15m10"
chmod 000 app.conf
cat app.conf
```

Mengembalikan permission:

```bash id="p16m10"
chmod 644 app.conf
cat app.conf
```

---

### 3.7 Mengamati System Call dengan strace

Menampilkan detail system call:

```bash id="q17m10"
strace ls 2>&1 | head -n 30
```

Menampilkan ringkasan statistik:

```bash id="r18m10"
strace -c ls
```

---

## 4. Hasil dan Pembahasan

Praktikum menunjukkan bahwa Linux menggunakan memori virtual untuk mengatur penggunaan RAM secara efisien. Perintah `free -h` dan `/proc/meminfo` memberikan informasi penggunaan memori secara detail.

Aktivitas paging dapat diamati menggunakan `vmstat`, khususnya pada kolom `si` dan `so`. Jika kedua nilai tersebut tinggi secara terus-menerus, maka sistem mengalami tekanan memori. 

Pembuatan swap file berhasil dilakukan menggunakan `fallocate`, `mkswap`, dan `swapon`. Pengaturan permission `600` penting untuk menjaga keamanan data memori yang tersimpan di swap.

Monitoring proses dengan `ps aux` dan `top` membantu mengidentifikasi proses yang menggunakan RAM terbesar. Selain itu, script Bash berhasil digunakan untuk otomatisasi monitoring memori.

Penggunaan `strace` memperlihatkan bagaimana program berinteraksi dengan kernel melalui system call seperti `openat`, `read`, `write`, dan `close`. Hal ini membuktikan bahwa aplikasi user space tidak dapat langsung mengakses hardware tanpa perantara kernel.

---

## 5. Kesimpulan

1. Linux menggunakan memori virtual untuk mengelola RAM dan swap secara efisien.
2. Paging memungkinkan data dipindahkan antara RAM dan swap saat memori penuh.
3. Swap berfungsi sebagai cadangan RAM namun memiliki performa lebih lambat.
4. Monitoring memori dapat dilakukan menggunakan `free`, `top`, `vmstat`, dan `ps aux`.
5. System call menjadi jembatan antara user space dan kernel space.
6. Bash scripting dapat digunakan untuk otomatisasi monitoring sistem Linux.

---

## 6. Lampiran

* Script `monitor-memori.sh`
* Script `memory-audit.sh`
* Script `diagnosa-server.sh`
* File `top-memory-process.txt`
* File `swap-check.txt`
* File `strace-summary.txt`

---
