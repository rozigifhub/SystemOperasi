# LAPORAN PRAKTIKUM SISTEM OPERASI

## WEEK 09 – PEMROGRAMAN BASH

---

### 1. Tujuan Praktikum

1. Memahami dasar pembuatan script Bash.
2. Menggunakan variabel, parameter posisional, dan substitusi perintah.
3. Menerapkan struktur kontrol (if, for, while, case).
4. Membuat dan menggunakan fungsi dalam Bash.
5. Mengelola argumen command line menggunakan getopts.
6. Melakukan debugging pada script Bash.

---

### 2. Dasar Teori

Bash merupakan shell sekaligus bahasa scripting yang digunakan untuk menjalankan perintah sistem operasi Linux secara otomatis. Script Bash berisi kumpulan perintah terminal yang disusun dengan logika tertentu seperti percabangan dan perulangan. 

Struktur dasar script Bash diawali dengan **shebang (`#!/bin/bash`)** yang menentukan interpreter yang digunakan. Script kemudian dapat berisi variabel, kontrol alur, fungsi, dan pemrosesan argumen. 

Variabel dalam Bash bersifat string secara default dan diakses menggunakan `$VAR`. Selain itu, Bash menyediakan parameter posisional seperti `$1`, `$2`, dan `$#` untuk membaca input dari command line. 

Struktur kontrol seperti `if`, `for`, `while`, dan `case` digunakan untuk mengatur alur program, mirip dengan bahasa pemrograman seperti Java namun dengan sintaks yang berbeda. 

---

### 3. Langkah-Langkah Praktikum

#### 3.1 Membuat Script Laporan Sistem

1. Membuat direktori kerja:

```bash
mkdir -p ~/praktikum-os/week09/{scripts,logs,data}
cd ~/praktikum-os/week09/scripts
```

2. Membuat file script:

```bash
nano laporan-sistem.sh
```

3. Isi script:

```bash
#!/bin/bash
echo "================================"
echo "LAPORAN SISTEM"
echo "================================"
echo "Tanggal : $(date '+%A, %d %B %Y')"
echo "Jam : $(date '+%H:%M:%S')"
echo "Hostname : $(hostname)"
echo "User : $(whoami)"
echo "CPU core : $(nproc)"
echo "RAM bebas : $(free -h | awk '/^Mem/ {print $4}')"
echo "Disk / : $(df -h / | awk 'NR==2 {print $5}') terpakai"
echo "================================"
```

4. Menjalankan script:

```bash
chmod +x laporan-sistem.sh
./laporan-sistem.sh
```

---

#### 3.2 Script dengan Parameter

Script menerima argumen dari user:

```bash
#!/bin/bash
ADMIN=${1:-"Tidak dikenal"}
BATAS=${2:-80}

DISK=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

echo "Admin : $ADMIN"
echo "Disk : $DISK%"

if [ "$DISK" -gt "$BATAS" ]; then
    echo "Status : PERINGATAN"
else
    echo "Status : Normal"
fi
```

---

#### 3.3 Struktur Kontrol (Grading)

```bash
#!/bin/bash
MAHASISWA=("Andi:92" "Budi:73" "Citra:55")

for DATA in "${MAHASISWA[@]}"; do
    NAMA=$(echo $DATA | cut -d: -f1)
    NILAI=$(echo $DATA | cut -d: -f2)

    if [ "$NILAI" -ge 85 ]; then
        GRADE="A"
    elif [ "$NILAI" -ge 75 ]; then
        GRADE="B"
    else
        GRADE="C"
    fi

    echo "$NAMA - $NILAI - $GRADE"
done
```

---

#### 3.4 Menu Interaktif

```bash
#!/bin/bash
while true; do
    echo "1. Info Disk"
    echo "2. Info Memori"
    echo "3. Keluar"
    read -p "Pilih: " PILIH

    case $PILIH in
        1) df -h ;;
        2) free -h ;;
        3) exit ;;
        *) echo "Pilihan salah" ;;
    esac
done
```

---

#### 3.5 Fungsi dalam Bash

```bash
#!/bin/bash
hitung_luas() {
    local p=$1
    local l=$2
    echo $((p * l))
}

hasil=$(hitung_luas 5 4)
echo "Luas: $hasil"
```

---

#### 3.6 Script Backup dengan Opsi

Menggunakan `getopts`:

```bash
while getopts "v" opt; do
    case $opt in
        v) echo "Verbose aktif" ;;
    esac
done
```

---

#### 3.7 Debugging

Menjalankan debug:

```bash
bash -x script.sh
```

Menggunakan mode aman:

```bash
set -euo pipefail
```

---

### 4. Hasil dan Pembahasan

Pada praktikum ini, script Bash berhasil dibuat dan dijalankan untuk berbagai kebutuhan seperti laporan sistem, pengecekan disk, grading, dan backup data.

Penggunaan variabel dan parameter memungkinkan script menjadi dinamis. Struktur kontrol membantu dalam pengambilan keputusan dan perulangan data.

Selain itu, penggunaan fungsi membuat kode lebih modular dan mudah digunakan kembali. Debugging dengan `-x` sangat membantu dalam menemukan kesalahan saat eksekusi.

---

### 5. Kesimpulan

1. Bash dapat digunakan untuk otomatisasi tugas sistem operasi.
2. Script Bash mendukung variabel, fungsi, dan struktur kontrol.
3. Parameter posisional memungkinkan interaksi dengan user.
4. Debugging penting untuk memastikan script berjalan dengan benar.
5. Praktikum ini memberikan dasar kuat untuk pengembangan automation di Linux.

---

