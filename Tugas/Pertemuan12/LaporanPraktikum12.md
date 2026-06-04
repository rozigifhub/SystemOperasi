# LAPORAN PRAKTIKUM SISTEM OPERASI

## WEEK 10 – MANAJEMEN MEMORI & SYSTEM CALL

---

# 1. Tujuan Praktikum

1. Memahami arsitektur memori pada sistem Linux.
2. Memahami konsep memori virtual dan mekanisme paging.
3. Mengelola dan mengonfigurasi swap space.
4. Memantau penggunaan memori menggunakan berbagai utilitas Linux.
5. Memahami konsep system call dan interaksi user space dengan kernel space.
6. Mengamati aktivitas system call menggunakan utilitas strace.

---

# 2. Dasar Teori

## 2.1 Arsitektur Memori Linux

Linux menggunakan konsep **virtual memory**, yaitu setiap proses memiliki ruang alamat virtual yang terisolasi dari proses lain. Kernel bertugas memetakan alamat virtual tersebut ke memori fisik (RAM). Struktur memori proses umumnya terdiri dari:

* Text Segment
* Data Segment
* Heap
* Stack
* Kernel Space

Pendekatan ini meningkatkan stabilitas sistem karena proses tidak dapat mengakses memori proses lain secara langsung.

## 2.2 Memori Virtual dan Paging

Memori virtual memungkinkan program menggunakan ruang memori yang lebih besar daripada kapasitas RAM fisik. Linux membagi memori menjadi page dan menggunakan mekanisme paging untuk memindahkan page yang tidak aktif ke swap ketika RAM mulai penuh. Aktivitas ini dapat diamati melalui utilitas `vmstat`.

## 2.3 Swap Space

Swap adalah area penyimpanan pada disk yang digunakan sebagai perpanjangan RAM. Swap membantu mencegah sistem kehabisan memori, tetapi performanya jauh lebih lambat dibandingkan RAM karena bergantung pada media penyimpanan. Linux menyediakan parameter `swappiness` untuk mengatur tingkat agresivitas penggunaan swap.

## 2.4 Monitoring Penggunaan Memori

Beberapa utilitas yang digunakan untuk memantau memori antara lain:

* `free -h`
* `cat /proc/meminfo`
* `top`
* `ps aux`
* `vmstat`

Utilitas tersebut membantu administrator menganalisis kondisi memori dan mengidentifikasi proses yang menggunakan sumber daya terbesar.

## 2.5 System Call

System call merupakan mekanisme komunikasi antara program di user space dengan kernel. Program tidak dapat mengakses hardware atau sumber daya kernel secara langsung sehingga seluruh permintaan harus melalui system call seperti:

* open()
* read()
* write()
* close()
* fork()
* exec()
* wait()
* mmap()

Aktivitas system call dapat diamati menggunakan utilitas `strace`.

---

# 3. Langkah-Langkah Praktikum

## 3.1 Praktikum Melihat Penggunaan Memori

Melihat ringkasan penggunaan RAM dan swap:

```bash
free -h
```

Melihat informasi detail memori dari kernel:

```bash
cat /proc/meminfo | head -n 20
```

Memantau sistem secara real-time:

```bash
top
```

---

## 3.2 Praktikum Mengamati Aktivitas Paging

Menampilkan statistik memori virtual:

```bash
vmstat 1 5
```

Parameter yang diamati:

* si (swap in)
* so (swap out)
* free
* buff

---

## 3.3 Praktikum Konfigurasi Swap File

Membuat swap file:

```bash
sudo fallocate -l 512M /swapfile-week10
```

Mengatur permission:

```bash
sudo chmod 600 /swapfile-week10
```

Memformat dan mengaktifkan swap:

```bash
sudo mkswap /swapfile-week10

sudo swapon /swapfile-week10
```

Verifikasi:

```bash
swapon --show

free -h
```

Melihat dan mengubah swappiness:

```bash
cat /proc/sys/vm/swappiness

sudo sysctl vm.swappiness=10

cat /proc/sys/vm/swappiness
```

Menghapus swap setelah praktikum:

```bash
sudo swapoff /swapfile-week10

sudo rm /swapfile-week10
```

---

## 3.4 Praktikum Monitoring Memory

Menampilkan proses dengan penggunaan memori terbesar:

```bash
ps aux --sort=-%mem | head
```

Monitoring real-time:

```bash
top
```

Pengurutan pada top:

```text
M = berdasarkan memori
P = berdasarkan CPU
q = keluar
```

---

## 3.5 Praktikum Script Monitor Memori

Membuat file:

```bash
nano monitor-memori.sh
```

Isi script:

```bash
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

```bash
chmod +x monitor-memori.sh

bash monitor-memori.sh
```

---

## 3.6 Praktikum Studi Kasus Permission dan System Call

Membuat file konfigurasi:

```bash
mkdir -p ~/praktikum-os/week10-memory/syscall-case

cd ~/praktikum-os/week10-memory/syscall-case

echo "PORT=8080" > app.conf
```

Melihat isi file:

```bash
cat app.conf
```

Menghapus seluruh permission:

```bash
chmod 000 app.conf

cat app.conf
```

Mengembalikan permission:

```bash
chmod 644 app.conf

cat app.conf
```

---

## 3.7 Praktikum Mengamati System Call

Melihat detail system call:

```bash
strace ls 2>&1 | head -n 30
```

Melihat statistik system call:

```bash
strace -c ls

strace -c ls /etc 2>&1 | tail -5
```

---

# 4. Hasil dan Pembahasan

Hasil praktikum menunjukkan bahwa Linux menggunakan sistem virtual memory yang memungkinkan setiap proses memiliki ruang alamat tersendiri sehingga meningkatkan keamanan dan stabilitas sistem. Informasi penggunaan memori dapat diperoleh melalui `free -h` dan `/proc/meminfo`. Nilai `available` merupakan indikator utama untuk menentukan ketersediaan memori yang sebenarnya.

Pada pengamatan paging menggunakan `vmstat`, sistem normal menunjukkan nilai `si` dan `so` mendekati nol. Nilai yang terus-menerus tinggi menunjukkan tekanan memori (memory pressure) dan dapat menyebabkan penurunan performa sistem akibat aktivitas swap yang berlebihan.

Swap file berhasil dibuat menggunakan kombinasi perintah `fallocate`, `mkswap`, dan `swapon`. Permission 600 diperlukan karena swap dapat berisi data sensitif yang berasal dari memori aplikasi. Jika permission terlalu longgar, terdapat risiko kebocoran informasi.

Monitoring menggunakan `ps aux` dan `top` memungkinkan identifikasi proses yang menggunakan memori terbesar. Nilai RSS menunjukkan penggunaan RAM fisik aktual, sedangkan VSZ menunjukkan total memori virtual yang dialokasikan. Karena itu nilai VSZ hampir selalu lebih besar daripada RSS.

Pada pengujian system call, perintah `strace` memperlihatkan bahwa program sederhana seperti `ls` sebenarnya memanggil banyak system call, misalnya:

* openat()
* read()
* write()
* close()
* newfstatat()

Hal ini menunjukkan bahwa seluruh akses file dan perangkat keras dilakukan melalui kernel menggunakan mekanisme system call.

---

# 5. Kesimpulan

1. Linux menggunakan virtual memory untuk mengisolasi proses dan meningkatkan stabilitas sistem.
2. Aktivitas paging dapat diamati menggunakan vmstat melalui kolom si dan so.
3. Swap berfungsi sebagai perpanjangan RAM tetapi memiliki performa lebih lambat karena menggunakan media penyimpanan.
4. Monitoring memori dapat dilakukan menggunakan free, top, ps, dan meminfo.
5. System call menjadi penghubung antara program di user space dan kernel.
6. Utilitas strace dapat digunakan untuk mengamati serta menganalisis system call yang dilakukan suatu program.

---

# 6. Lampiran

### Perintah Monitoring Memori

```bash
free -h
cat /proc/meminfo
vmstat 1 5
top
```

### Perintah Konfigurasi Swap

```bash
sudo fallocate -l 512M /swapfile-week10
sudo chmod 600 /swapfile-week10
sudo mkswap /swapfile-week10
sudo swapon /swapfile-week10
```

### Perintah Monitoring Proses

```bash
ps aux --sort=-%mem | head
```

### Perintah Analisis System Call

```bash
strace ls
strace -c ls
```

---
