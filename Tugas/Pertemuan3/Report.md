# Laporan Praktikum 3

## Operasi File dan Struktur Direktori (Linux)

## Analisa Hasil Percobaan

------------------------------------------------------------------------

# Percobaan 1: Directory

## 1. Melihat direktori HOME

Perintah:

    pwd
    echo $HOME

### Analisa

-   `pwd` menampilkan direktori kerja saat ini.
-   `echo $HOME` menampilkan lokasi home directory milik user.

Biasanya hasil kedua perintah sama jika posisi kita berada di direktori
home.

Contoh:

    /home/username

Ini menunjukkan bahwa variabel lingkungan `$HOME` menyimpan lokasi
direktori utama user.

------------------------------------------------------------------------

## 2. Melihat direktori aktual dan parent direktori

Perintah:

    pwd
    cd .
    pwd
    cd ..
    pwd
    cd

### Analisa

-   `cd .` berarti tetap berada pada direktori saat ini.
-   `cd ..` berpindah ke direktori parent (satu tingkat di atas).
-   `cd` tanpa argumen akan kembali ke direktori home.

Simbol penting:

  Simbol   Fungsi
  -------- --------------------
  `.`      direktori saat ini
  `..`     direktori parent
  `~`      home directory

------------------------------------------------------------------------

## 3. Membuat lebih dari satu direktori

Perintah:

    mkdir A B C A/D A/E B/F A/D/A
    ls -l
    ls -l A
    ls -l A/D

### Analisa

Perintah `mkdir` dapat membuat beberapa direktori sekaligus termasuk
subdirektorinya.

Struktur direktori yang terbentuk:

    .
    ├── A
    │   ├── D
    │   │   └── A
    │   └── E
    ├── B
    │   └── F
    └── C

-   `ls -l` menampilkan direktori A, B, dan C.
-   `ls -l A` menampilkan isi direktori A yaitu D dan E.
-   `ls -l A/D` menampilkan subdirektori A yang berada di dalam D.

Hal ini menunjukkan bahwa Linux menggunakan struktur direktori berbentuk
pohon.

------------------------------------------------------------------------

## 4. Menghapus direktori

Perintah:

    rmdir B
    ls -l B
    rmdir B/F B
    ls -l B

### Analisa

-   `rmdir B` menghasilkan error karena direktori B tidak kosong.
-   `ls -l B` menunjukkan bahwa masih ada direktori F.
-   `rmdir B/F B` menghapus F terlebih dahulu kemudian B.

Perintah `rmdir` hanya bisa menghapus direktori kosong.

------------------------------------------------------------------------

## 5. Navigasi direktori menggunakan cd

Perintah:

    cd A
    pwd
    cd ..
    pwd
    cd /home/user/C
    pwd

### Analisa

-   `cd A` masuk ke direktori A.
-   `cd ..` kembali ke direktori sebelumnya.
-   `cd /home/user/C` menggunakan path absolut.

Linux mengenal dua jenis path:

  Jenis Path   Penjelasan
  ------------ ---------------------------------
  Absolut      dimulai dari `/`
  Relatif      dimulai dari direktori saat ini

------------------------------------------------------------------------

# Percobaan 2: Manipulasi File

## 1. Perintah cp

    cat > contoh
    cp contoh contoh1
    ls -l
    cp contoh A
    ls -l A
    cp contoh1 A/D
    ls -l A/D

### Analisa

-   `cat > contoh` membuat file baru.
-   `cp contoh contoh1` membuat salinan file.
-   `cp contoh A` menyalin file ke direktori A.
-   `cp contoh1 A/D` menyalin file ke subdirektori.

Perintah `cp` digunakan untuk menyalin file.

------------------------------------------------------------------------

## 2. Perintah mv

    mv contoh contoh2
    ls -l
    mv contoh1 contoh2 A/D
    ls -l A/D
    mv contoh contoh1 C
    ls -l C

### Analisa

Perintah `mv` memiliki dua fungsi:

1.  Memindahkan file
2.  Mengganti nama file

------------------------------------------------------------------------

## 3. Perintah rm

    rm contoh2
    ls -l
    rm -i contoh
    rm -rf A C
    ls -l

### Analisa

-   `rm contoh2` menghapus file.
-   `rm -i` meminta konfirmasi.
-   `rm -rf` menghapus direktori beserta seluruh isinya.

------------------------------------------------------------------------

# Percobaan 3: Symbolic Link

    echo "Hallo apa khabar" > halo.txt
    ln halo.txt z
    ls -l
    cat z
    mkdir mydir
    ln z mydir/halo.juga
    cat mydir/halo.juga
    ln -s z bye.txt
    ls -l bye.txt
    cat bye.txt

### Analisa

-   `ln halo.txt z` membuat hard link.
-   `ln z mydir/halo.juga` membuat hard link lain.
-   `ln -s z bye.txt` membuat symbolic link.

Perbedaan:

  Hard Link                           Symbolic Link
  ----------------------------------- ------------------------------
  menunjuk inode file                 menunjuk path file
  tetap ada walau file asli dihapus   rusak jika file asli dihapus

------------------------------------------------------------------------

# Percobaan 4: Melihat Isi File

    ls -l
    file halo.txt
    file bye.txt

### Analisa

Perintah `file` digunakan untuk mengetahui tipe file.

-   `halo.txt` dikenali sebagai file teks.
-   `bye.txt` dikenali sebagai symbolic link.

------------------------------------------------------------------------

# Percobaan 5: Mencari File

## Perintah find

    find /home -name "*.txt" -print > myerror.txt
    cat myerror.txt
    find . -name "*.txt" -exec wc -l '{}' ';'

### Analisa

-   `find` mencari file berdasarkan nama.
-   `*.txt` berarti semua file berekstensi txt.
-   `wc -l` menghitung jumlah baris.

------------------------------------------------------------------------

## Perintah which

    which ls

### Analisa

Menampilkan lokasi program `ls`, biasanya:

    /bin/ls

------------------------------------------------------------------------

## Perintah locate

    locate "*.txt"

### Analisa

`locate` mencari file menggunakan database indeks sistem sehingga lebih
cepat dibanding `find`.

------------------------------------------------------------------------

# Percobaan 6: Mencari teks dalam file

    grep Hallo *.txt

### Analisa

Perintah `grep` digunakan untuk mencari teks dalam file.

-   `Hallo` adalah kata yang dicari.
-   `*.txt` berarti semua file teks.

Output akan menampilkan file yang mengandung kata tersebut.
