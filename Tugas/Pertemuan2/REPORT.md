|  | System Operasi  |
|--|--|
| NIM |  254107020081|
| Nama |  Muhammad Zainur Roziqin |
| Kelas | TI - 1G |
| Repository | [link] (https://github.com/rozigifhub/SystemOperasi/blob/Rozi/Tugas/Pertemuan2/REPORT.md) |

# Sintak-sintaks Linux

## Praktikum 2.1 - Identifikasi CPU dan Memori

![Screenshot](Hasil/image1.png)

lscpu

Melihat informasi CPU.

![Screenshot](Hasil/image2.png)

free -h

Melihat penggunaan memori.

## Praktikum 2.2 - Identifikasi Perangkat PCI/USB dan Driver

![Screenshot](Hasil/image3.png)

lspci

Melihat daftar PCI.

![Screenshot](Hasil/image4.png)

lspci -nnk

Lihat perangkat PCI beserta driver kernel yang digunakan.

![Screenshot](Hasil/image5.png)

lspci - nnk | grep - A3 -i ethernet

Mencari info NIC dan drivernya

![Screenshot](Hasil/image6.png)

lsusb

Melihat perangkat USB

## Praktikum 2.3 - Identifikasi Storage dan Filesystem

![Screenshot](Hasil/image7.png)

lsblk -f

Melihat daftar disk/partisi

![Screenshot](Hasil/image8.png)

sudo blkid

melihat uuid filesystem

![Screenshot](Hasil/image9.png)

findmnt / 

Lihat mount point untuk root filesystem

## Praktikum 2.4 - Melihat Modul Aktif dan Informasinya

![Screenshot](Hasil/image10.png)

uname -r

mengecek versi kernel

![Screenshot](Hasil/image11.png)

lsmod | head

cek modul yang aktif

![Screenshot](Hasil/image12.png)

modinfo

cek info modul

![Screenshot](Hasil/image12.png)

sudo modprobe loop
lsmod | grep -i loop

Load modul dan verifikasi

## Praktikum 2.5 - Konfigurasi Auto Load dan Blacklist

![Screenshot](Hasil/image13.png)

echo " loop " | sudo tee / etc / modules - load . d / loop . conf

Menambahkan modul untuk auto-load (demo)

![Screenshot](Hasil/image14.png)

lsmod | grep -i loop

verifikasi modul aktif

## Praktikum 2.6 - Mengenali Block vs Character Device

![alt text](Hasil/image15.png)

 ls -l / dev / sda

 Melihat detail device node disk

 ![alt text](Hasil/image15.png)

 ls -l / dev / tty

 Melihat detail device node disk

 ## Praktikum 2.7 — Melihat Informasi udev

 ![alt text](Hasil/image16.png)

udevadm info -- query = all -- name =/ dev / sda | head -n 30

 Melihat atribut udev untuk disk

## Praktikum 2.8 — Membuat Workspace Praktikum
