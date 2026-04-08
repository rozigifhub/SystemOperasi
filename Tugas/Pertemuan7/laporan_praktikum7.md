# Laporan Praktikum 7 — Bash Shell

**Nama:** Muhammad Zainur Roziqin  
**NIM:** 254107020081  
**Kelas:** TI-1G  

---

## Praktikum 7.1 — Mengenali Bash dan Workspace

```bash
echo "Shell login : $SHELL"
echo "Shell aktif : $0"
bash --version | head -n 1
```

```bash
echo $$
ps -p $$ -o pid,ppid,args=
```

```bash
mkdir -p ~/praktikum-os/week07-bash/{bin,backup,logs,sampel,ruang-nama}
cd ~/praktikum-os/week07-bash
pwd
```

---

## Praktikum 7.2 — Ringkasan Sesi

```bash
{
echo "=== RINGKASAN SESI BASH ==="
date
echo "User : $(whoami)"
echo "Hostname : $(hostname)"
echo "Shell login : $SHELL"
echo "Shell aktif : $0"
echo "PID : $$"
echo "Direktori : $(pwd)"
} | tee session-info.txt
```

---

## Praktikum 7.3 — Konfigurasi .bashrc

```bash
cp ~/.bashrc ~/.bashrc.bak

cat <<'EOF' >> ~/.bashrc
export PRAKTIKUM_BASH_DIR="$HOME/praktikum-os/week07-bash"
export EDITOR=nano
EOF

source ~/.bashrc
```

---

## Praktikum 7.4 — .bash_profile

```bash
cat <<'EOF' >> ~/.bash_profile
if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi
EOF
```

---

## Praktikum 7.5 — Variabel & PATH

```bash
KELAS_OS="Sistem Operasi"
echo $KELAS_OS

export KELAS_OS="Sistem Operasi"

echo $PATH
```

---

## Praktikum 7.6 — PATH & Script

```bash
mkdir -p ~/praktikum-os/week07-bash/bin

export PATH="$HOME/praktikum-os/week07-bash/bin:$PATH"

cat <<'EOF' > ~/praktikum-os/week07-bash/bin/ringkas-sistem
#!/usr/bin/env bash
echo "Hostname: $(hostname)"
echo "User: $(whoami)"
echo "Uptime: $(uptime -p)"
df -h /
EOF

chmod +x ~/praktikum-os/week07-bash/bin/ringkas-sistem
```

---

## Praktikum 7.7 — Alias

```bash
alias ll='ls -lah'
alias hist10='history | tail -n 10'
```

---

## Praktikum 7.8 — Fungsi Backup

```bash
backup_conf() {
  local src="$1"
  local dst="$HOME/praktikum-os/week07-bash/backup"
  mkdir -p "$dst"
  cp "$src" "$dst/$(basename "$src").bak"
}
```

---

## Praktikum 7.9 — History

```bash
history | tail -n 10
```

---

## Praktikum 7.10 — Wildcard

```bash
ls *.log
ls catatan-?.txt
```

---

## Praktikum 7.11 — Arsip

```bash
mkdir arsip-log
mv *.log arsip-log/
tar -czf arsip-log.tar.gz arsip-log
```

---

## Praktikum 7.12 — Quoting

```bash
echo '$USER'
echo "$USER"
```

---

# 🔥 LATIHAN 7

## Latihan 7.1 — Toolkit Bash

```bash
# PATH
export PATH="$HOME/bin:$PATH"

# alias
alias ll='ls -lah'
alias gs='git status'

# fungsi
hello() {
  echo "Hello $(whoami)"
}
```

---

## Latihan 7.2 — Audit Konfigurasi

```bash
find /etc -name "*.conf" > audit.txt 2> error.log
wc -l audit.txt
```

Penjelasan:
- stdout dan stderr dipisah agar error tidak bercampur

---

## Latihan 7.3 — Health Check

```bash
#!/usr/bin/env bash
{
date
hostname
whoami
uptime
free -h
df -h /
} | tee health.log
```

---

## Latihan 7.4 — File Kompleks

```bash
cp "file [test].txt" backup/
```

---

# Kesimpulan

- Bash penting untuk admin Linux
- PATH menentukan eksekusi
- Alias & fungsi mempercepat kerja
- Quoting penting untuk keamanan

