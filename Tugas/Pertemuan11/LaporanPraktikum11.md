# LAPORAN PRAKTIKUM SISTEM OPERASI

## WEEK 11 – MANAJEMEN FILE & USER/GROUP

---

## 1. Tujuan Praktikum

1. Memahami sistem permission pada Linux.
2. Menggunakan chmod, chown, dan chgrp.
3. Memahami Access Control List (ACL).
4. Mengelola user dan group Linux.
5. Menggunakan sudo dan su dengan aman.
6. Memahami konsep dan konfigurasi disk quota.

---

## 2. Dasar Teori

Linux menggunakan model kontrol akses berbasis owner, group, dan others. Setiap file memiliki permission read (r), write (w), dan execute (x). Permission ini dapat dimodifikasi menggunakan perintah seperti `chmod`, `chown`, dan `chgrp`. 

Permission dapat ditulis dalam bentuk simbolik maupun oktal. Contoh umum:

* `755` untuk direktori atau program publik
* `644` untuk file biasa
* `600` untuk file privat

Linux juga mendukung special permission seperti SUID, SGID, dan sticky bit. 

Selain permission standar, Linux menyediakan ACL (Access Control List) untuk memberikan akses lebih granular kepada user atau group tertentu tanpa mengubah owner utama file. 

Manajemen user dan group dilakukan melalui file `/etc/passwd`, `/etc/shadow`, dan `/etc/group`. Administrasi dilakukan menggunakan perintah seperti `useradd`, `usermod`, `groupadd`, dan `passwd`. 

---

## 3. Langkah-Langkah Praktikum

### 3.1 Praktikum Permission

Membuat direktori dan file uji:

```bash id="w11a1"
mkdir ~/lab-permissions && cd ~/lab-permissions

echo "data rahasia" > secret.txt

echo '#!/bin/bash' > myscript.sh
echo 'echo Hello' >> myscript.sh

ls -la
```

Mengubah file menjadi privat:

```bash id="w11a2"
chmod 600 secret.txt
ls -l secret.txt
```

Menjadikan script executable:

```bash id="w11a3"
chmod 755 myscript.sh
ls -l myscript.sh

./myscript.sh
```

Mengaktifkan SGID pada direktori:

```bash id="w11a4"
mkdir shared-dir
chmod g+s shared-dir

ls -ld shared-dir
```

Menguji umask:

```bash id="w11a5"
umask
umask 027

touch testfile-027
ls -l testfile-027
```

---

### 3.2 Praktikum ACL

Membuat file uji ACL:

```bash id="w11b1"
mkdir ~/lab-acl && cd ~/lab-acl

echo "Data penting" > confidential.txt
chmod 640 confidential.txt

ls -l confidential.txt
getfacl confidential.txt
```

Menambahkan ACL untuk user tertentu:

```bash id="w11b2"
setfacl -m u:userA:r confidential.txt

ls -l confidential.txt
getfacl confidential.txt
```

Membuat default ACL pada direktori:

```bash id="w11b3"
mkdir shared

setfacl -d -m u:userA:rwx shared
setfacl -d -m u:userB:r-x shared

getfacl shared

touch shared/inherited.txt

getfacl shared/inherited.txt
```

---

### 3.3 Praktikum User Management

Membuat user baru:

```bash id="w11c1"
sudo useradd -m -s /bin/bash userA
sudo useradd -m -s /bin/bash userB

sudo passwd userA
sudo passwd userB
```

Verifikasi user:

```bash id="w11c2"
id userA
getent passwd userA
```

Mengubah shell:

```bash id="w11c3"
sudo usermod -s /bin/zsh userA

getent passwd userA
```

Lock dan unlock user:

```bash id="w11c4"
sudo usermod -L userB
sudo passwd -S userB

sudo usermod -U userB
sudo passwd -S userB
```

---

### 3.4 Praktikum Group Management

Membuat group:

```bash id="w11d1"
sudo groupadd labgroup
sudo groupadd readonly-group
```

Menambahkan user ke group:

```bash id="w11d2"
sudo usermod -aG labgroup,readonly-group userA

sudo usermod -aG readonly-group userB
```

Verifikasi group:

```bash id="w11d3"
id userA
id userB

getent group labgroup
getent group readonly-group
```

---

### 3.5 Praktikum Password Aging Policy

Mengatur aging policy:

```bash id="w11e1"
sudo chage -M 60 -W 7 -m 1 userA

sudo chage -l userA
```

Memaksa ganti password:

```bash id="w11e2"
sudo chage -d 0 userA
```

Lock password:

```bash id="w11e3"
sudo passwd -l userB
sudo passwd -S userB
```

Unlock password:

```bash id="w11e4"
sudo passwd -u userB
sudo passwd -S userB
```

---

### 3.6 Praktikum Konfigurasi sudo

Membuat file sudoers:

```bash id="w11f1"
sudo visudo -f /etc/sudoers.d/lab-userA
```

Isi konfigurasi:

```bash id="w11f2"
userA ALL=(root) NOPASSWD: /usr/bin/apt update, /usr/bin/apt upgrade

userA ALL=(root) /bin/systemctl status *
```

Verifikasi aturan:

```bash id="w11f3"
sudo -l -U userA

sudo grep "userA" /var/log/auth.log | tail -10
```

---

### 3.7 Praktikum Disk Quota

Membuat filesystem uji quota:

```bash id="w11g1"
sudo dd if=/dev/zero of=/tmp/quota-test.img bs=1M count=100

sudo mkfs.ext4 /tmp/quota-test.img

sudo mkdir -p /mnt/quota-test

sudo mount -o loop,usrquota,grpquota /tmp/quota-test.img /mnt/quota-test
```

Mengaktifkan quota:

```bash id="w11g2"
sudo quotacheck -cug /mnt/quota-test

sudo quotaon -v /mnt/quota-test

sudo repquota /mnt/quota-test
```

Menetapkan quota:

```bash id="w11g3"
sudo edquota -u userA

sudo repquota /mnt/quota-test
```

Membersihkan lingkungan:

```bash id="w11g4"
sudo quotaoff /mnt/quota-test

sudo umount /mnt/quota-test

sudo rm /tmp/quota-test.img
```

---

## 4. Hasil dan Pembahasan

Praktikum menunjukkan bahwa permission Linux menggunakan model owner, group, dan others dengan kombinasi hak akses read, write, dan execute. Permission dapat diatur menggunakan notasi simbolik maupun oktal. 

Penggunaan ACL memungkinkan pemberian akses lebih spesifik kepada user tertentu tanpa mengubah owner atau group utama file. Setelah ACL diterapkan, output `ls -l` menampilkan tanda `+` pada permission file.

Pada manajemen user dan group, akun baru berhasil dibuat menggunakan `useradd` dan dimodifikasi menggunakan `usermod`. Group tambahan dapat diberikan menggunakan opsi `-aG`.

Penggunaan `sudo` lebih aman dibanding `su` karena semua aktivitas dapat dicatat di log sistem dan aturan akses dapat dibatasi secara granular. 

Praktikum disk quota menunjukkan bagaimana administrator dapat membatasi penggunaan storage melalui soft limit dan hard limit. Quota membantu mencegah satu user menggunakan seluruh kapasitas disk sistem.

---

## 5. Kesimpulan

1. Linux menggunakan model permission berbasis owner, group, dan others.
2. Permission dapat diatur menggunakan chmod, chown, dan chgrp.
3. ACL memberikan kontrol akses yang lebih fleksibel dibanding permission standar.
4. User dan group Linux dapat dikelola menggunakan useradd, usermod, dan groupadd.
5. sudo memberikan mekanisme administrasi yang lebih aman dibanding su.
6. Disk quota berguna untuk membatasi penggunaan storage pada sistem multi-user.

---

## 6. Lampiran

* File `secret.txt`
* File `myscript.sh`
* File `confidential.txt`
* File konfigurasi sudoers `lab-userA`
* File `quota-test.img`
* Output `repquota` dan `getfacl`

---
