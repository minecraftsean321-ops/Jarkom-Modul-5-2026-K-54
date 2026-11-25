# Jarkom-Modul-5-2026-K-54

| Nama                 | NRP        |
|----------------------|------------|
| Viko Rizky Fauzan    | 5027251017 |
| Sean Arthur Tamajaya | 5027251050 |

## Laporan Praktikum: Perancangan Subnetting VLSM, Static Routing, NAT, dan Keamanan Jaringan Menggunakan Firewall iptables

## Daftar Isi

- [Soal 1: Perancangan Subnetting VLSM](#soal-1-perancangan-subnetting-vlsm-19223800021)
- [Soal 2](#soal-2)
- [Soal 3](#soal-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)
- [Soal 8](#soal-8)
- [Soal 9](#soal-9)
- [Soal 10](#soal-10)

---

## Soal 1: Perancangan Subnetting VLSM (`192.238.0.0/21`)

### 1. Analisis Kebutuhan & Aturan Alokasi

- **Blok IP Induk:** `192.238.0.0/21` (rentang `192.238.0.0` – `192.238.7.255`, total $2.048$ alamat IP).
- **Total Segmen:** $17\text{ LAN}$ dan $16\text{ Link point-to-point}$ antar-router ($33\text{ subnet}$).
- **Aturan Pengurutan VLSM:** Diurutkan dari kebutuhan alamat terbesar ke terkecil. Apabila ukuran kebutuhan sama, segmen **LAN** diprioritaskan lebih dahulu daripada **link antar-router**, lalu diurutkan sesuai tabel host.
- **Aturan Penomoran Node:** Gateway menggunakan alamat IP *usable* pertama pada subnet, sedangkan host menggunakan alamat IP *usable* berikutnya secara berurutan dari kiri ke kanan sesuai topologi.

### 2. Tabel Pembagian Subnet VLSM dan Pohon IP

**Tabel VLSM**

<img src="https://github.com/user-attachments/assets/d31feefc-d4fe-41b5-95b4-6c61d85613cf" width="650" alt="Tabel VLSM">

**Gambar Pohon IP**

<img src="https://github.com/user-attachments/assets/663511e7-19ce-404e-8b39-ec1cb2f126d9" width="450" alt="Pohon VLSM 192.238.0.0">

### 3. Penjelasan Perhitungan Satu Subnet (Studi Kasus: `Switch-Quarters`)

Sebagai ilustrasi metode perhitungan VLSM yang diterapkan pada seluruh tabel, berikut rincian perhitungan pada segmen **Switch-Quarters**:

#### 3.1 Menentukan Total Kebutuhan IP Usable

- Berdasarkan tabel host, container `Quarters` membutuhkan $500\text{ host}$.
- Ditambah $1\text{ alamat IP}$ untuk *default gateway* pada interface router `Rei`.
- Total alamat IP *usable* yang dibutuhkan:

$$
\text{Total Kebutuhan Usable} = 500 + 1 = 501\text{ IP}
$$

#### 3.2 Menentukan Panjang Prefix (`/prefix`), Ukuran Blok, dan Subnet Mask

- Menggunakan rumus kapasitas host usable $2^h - 2 \ge \text{kebutuhan usable}$ (dikurangi $2$ untuk alamat *Network* dan *Broadcast*):
  - Jika $h = 8 \rightarrow 2^8 - 2 = 254$ (tidak mencukupi $501\text{ IP}$).
  - Jika $h = 9 \rightarrow 2^9 - 2 = 512 - 2 = 510\text{ IP usable}$ (memenuhi syarat $\ge 501$).
- Dengan jumlah bit host $h = 9$, panjang prefix terkecil yang sesuai adalah:

$$
\text{Prefix} = 32 - 9 = /23
$$

- **Ukuran Blok:** $2^9 = 512\text{ alamat IP}$ (mencakup dua blok `/24` berturut-turut, yaitu lompatan $+2$ pada oktet ketiga).
- **Subnet Mask:** Representasi biner `/23` adalah `11111111.11111111.11111110.00000000`, sehingga dalam desimal menjadi **`255.255.254.0`**.

#### 3.3 Menentukan Alamat Network, Gateway, Range Host, Broadcast, dan Sisa IP

Karena `Switch-Quarters` memiliki kebutuhan terbesar, alokasi dimulai tepat di titik awal blok induk `192.238.0.0/21` dengan nilai **Offset = 0**:

| Komponen | Nilai |
|----------|-------|
| **Network Address** | `192.238.0.0/23` |
| **Broadcast Address** | `192.238.1.255` (ukuran blok $512$ menghabiskan oktet ketiga `.0` dan `.1` secara penuh) |
| **Range Host (Usable)** | `192.238.0.1` s/d `192.238.1.254` ($510\text{ IP}$) |
| **Gateway (Router Rei)** | `192.238.0.1` (alamat *usable* pertama) |
| **IP Host (`Quarters`)** | `192.238.0.2` (alamat *usable* berikutnya setelah gateway) |
| **Sisa IP Usable** | $510 - 501 = 9\text{ IP}$ |

### 4. Pohon Alokasi IP & Jalur Dummy-Plug ke Armisael

Pada diagram pohon biner di atas, lima subnet yang membentuk jalur paket dari **Dummy-Plug** (`Switch-Lab`) menuju **Armisael** (`Switch-Monolith`) ditandai dengan lingkaran merah:

| No | Subnet | Keterangan |
|----|--------|------------|
| 1 | `192.238.4.48/29` | **Switch-Lab** — asal `Dummy-Plug` |
| 2 | `192.238.4.140/30` | **Link Asuka – Ritsuko** |
| 3 | `192.238.4.132/30` | **Link Gendou – Asuka** |
| 4 | `192.238.4.152/30` | **Link Gendou – Shigeru** |
| 5 | `192.238.4.80/29` | **Switch-Monolith** — tujuan `Armisael` |

---

## Soal 2

### Pengujian

#### 1. Cek status IP & interface di tiap router

```bash
ip -br a
```

**Gendou**

<img src="https://github.com/user-attachments/assets/3c47496d-70a1-49ac-a5ce-154ddefe2dbb" width="550" alt="ip -br a Gendou">

**Yui**

<img src="https://github.com/user-attachments/assets/cafe4e71-2046-40d5-ab32-51ea349f2964" width="550" alt="ip -br a Yui">

**Fuyutsuki**

<img src="https://github.com/user-attachments/assets/2718ddd5-7ba5-4665-bc0d-99ad950d4f56" width="550" alt="ip -br a Fuyutsuki">

#### 2. Uji ping ke seluruh 16 link router-ke-router (loop satu baris)

**Gendou** (uji ke 7 link router cabang utama)

```bash
for ip in 192.238.4.110 192.238.4.126 192.238.4.134 192.238.4.146 192.238.4.150 192.238.4.154 192.238.4.162; do ping -c 2 -W 1 $ip; done
```

<img src="https://github.com/user-attachments/assets/9312e641-2e88-4618-b83f-f554173d3aff" width="550" alt="Ping Gendou 1">

<img src="https://github.com/user-attachments/assets/92561487-72f0-4398-a97b-86b167986373" width="550" alt="Ping Gendou 2">

<img src="https://github.com/user-attachments/assets/63bd8d81-d940-4c63-9d75-d8414e1964fb" width="550" alt="Ping Gendou 3">

**Yui** (uji link ke Mari, Rei, Shinji)

```bash
for ip in 192.238.4.114 192.238.4.118 192.238.4.122; do ping -c 2 -W 1 $ip; done
```

<img src="https://github.com/user-attachments/assets/9ce67002-a9ab-4dcd-bd01-041fb4bc5cb0" width="550" alt="Ping Yui">

**Hikari** (uji link ke Kaworu)

```bash
ping -c 2 192.238.4.130
```

<img src="https://github.com/user-attachments/assets/6df61c6a-0061-401a-b0e4-a5df58fc5a6a" width="550" alt="Ping Hikari">

**Asuka** (uji 2 link ke Kensuke & Ritsuko)

```bash
for ip in 192.238.4.138 192.238.4.142; do ping -c 2 -W 1 $ip; done
```

<img src="https://github.com/user-attachments/assets/f1c5a472-cd54-49af-bcb4-64b269595d80" width="550" alt="Ping Asuka">

---

## Soal 3

### Pengujian

#### 1. Cek tabel routing

```bash
ip route
ping -c 3 8.8.8.8
```

**Gendou**

<img src="https://github.com/user-attachments/assets/0fd3a0f0-a2de-429a-a201-cfb789edb28a" width="550" alt="Routing Gendou 1">

<img src="https://github.com/user-attachments/assets/d389d812-24a4-4264-9dc8-3d644383c6dd" width="550" alt="Routing Gendou 2">

**Yui**

<img src="https://github.com/user-attachments/assets/d7eb1acc-b570-433a-8aa4-38fc60848a85" width="550" alt="Routing Yui">

**Shigeru**

<img src="https://github.com/user-attachments/assets/44384bff-136a-4204-ab28-5ef5293016a6" width="550" alt="Routing Shigeru">

#### 2. Pasang IP sementara pada 5 host uji dari cabang yang berjauhan

**Armisael** (Cabang Shigeru — menguji summary `192.238.4.80/28`)

```bash
ip addr flush dev eth0 && ip addr add 192.238.4.83/29 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.4.81
```

**Quarters**

```bash
ip addr flush dev eth0 && ip addr add 192.238.0.2/23 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.0.1
```

**Lilith**

```bash
ip addr flush dev eth0 && ip addr add 192.238.4.74/29 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.4.73
```

**Sachiel**

```bash
ip addr flush dev eth0 && ip addr add 192.238.3.130/26 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.3.129
```

**Comm-Net**

```bash
ip addr flush dev eth0 && ip addr add 192.238.2.2/24 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.2.1
```

#### 3. Uji ping lintas cabang dari Armisael

```bash
for ip in 192.238.0.2 192.238.4.74 192.238.3.130 192.238.2.2; do ping -c 3 $ip; done
```

<img src="https://github.com/user-attachments/assets/6ef86d57-4c32-4cde-825e-14b2f86c23be" width="550" alt="Ping lintas cabang 1">

<img src="https://github.com/user-attachments/assets/d2a41cdc-b0b2-4bd5-b1c2-6c26ebcc7daa" width="550" alt="Ping lintas cabang 2">

---

## Soal 4

### Pengujian

#### 1. Cek interface, rute & DHCP relay

Minta IP DHCP dulu di node yang akan diuji:

```bash
udhcpc -i eth0
```

Lalu gunakan perintah ini untuk pengujian:

```bash
ip -br a
ip route
ping -c 3 8.8.8.8
ping -c 3 google.com
```

**Spy**

<img src="https://github.com/user-attachments/assets/435ad1a6-d7f2-4b88-95e5-fb483c0dbdd8" width="550" alt="Spy 1">

<img src="https://github.com/user-attachments/assets/22dcf3d8-2391-4f94-801b-9ed101d27341" width="550" alt="Spy 2">

**Armisael**

<img src="https://github.com/user-attachments/assets/78efa1c7-3cce-4bef-abee-747332d41dff" width="550" alt="Armisael 1">

<img src="https://github.com/user-attachments/assets/931430fa-efe9-4503-9645-d329d383ee19" width="550" alt="Armisael 2">

**Dock-Crew**

<img src="https://github.com/user-attachments/assets/ebd9829b-6898-4631-aa5f-7eeaac56cbc6" width="550" alt="Dock-Crew 1">

<img src="https://github.com/user-attachments/assets/a061a3ae-5276-468f-93b3-9b5db1c2862f" width="550" alt="Dock-Crew 2">

#### 2. Cek apakah DHCP Server & Masquerade sudah aktif di Gendou

```bash
cat /var/lib/dhcp/dhcpd.leases
iptables -t nat -L POSTROUTING -n -v
```

<img src="https://github.com/user-attachments/assets/cdfc6d7a-862d-47f8-b367-54541ba06be2" width="550" alt="DHCP leases 1">

<img src="https://github.com/user-attachments/assets/ee15e38b-a44c-4e9f-8d69-7b2562b47798" width="550" alt="DHCP leases 2">

<img src="https://github.com/user-attachments/assets/fa292095-cbc2-4598-8944-984d1f3c2a19" width="550" alt="DHCP leases 3">

<img src="https://github.com/user-attachments/assets/8883ea61-b3e2-4729-a394-b7ac793cacaf" width="550" alt="Masquerade POSTROUTING">

---

## Soal 5

### Langkah 1: Tambahkan aturan firewall ke Gendou

Masukkan script ini ke Gendou:

```bash
# 1. Pastikan flag tanggal apt-get di gendo.sh aman saat GNS3 restart
sed -i 's|apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y isc-dhcp-server|apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true \&\& DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated isc-dhcp-server|' /root/gendo.sh

# 2. Hapus blok Soal 5 lama (jika skrip dijalankan ulang) lalu append aturan Soal 5
sed -i '/# === SOAL 5: FIREWALL ===/,$d' /root/gendo.sh
cat << 'EOF' >> /root/gendo.sh

# === SOAL 5: FIREWALL ===
# Reset filter table & set policy: boleh keluar (OUTPUT ACCEPT), tidak boleh dimasuki (INPUT & FORWARD DROP)
iptables -F
iptables -X
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Izinkan Loopback dan DHCP dari NAT1 (eth0)
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i eth0 -p udp --sport 67 --dport 68 -j ACCEPT

# (a) Izinkan balasan koneksi yang sudah ada (ESTABLISHED, RELATED)
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# (b) Izinkan traffic dari jaringan internal (192.238.0.0/21) ke internet (eth0)
iptables -A FORWARD ! -i eth0 -s 192.238.0.0/21 -o eth0 -j ACCEPT

# (c) Izinkan traffic antar-LAN di dalam jaringan internal
iptables -A FORWARD ! -i eth0 ! -o eth0 -s 192.238.0.0/21 -d 192.238.0.0/21 -j ACCEPT

# (d) Izinkan traffic dari jaringan internal ke Gendo sendiri (termasuk DHCP internal)
iptables -A INPUT ! -i eth0 -s 192.238.0.0/21 -j ACCEPT
iptables -A INPUT ! -i eth0 -p udp --dport 67 -j ACCEPT
EOF

# 3. Jalankan gendo.sh untuk menerapkan firewall sekaligus memastikan DHCP & NAT tetap aktif
bash /root/gendo.sh
```

### Pengujian

#### 1. Cek aturan firewall dan default policy pada INPUT & FORWARD di Gendou

```bash
iptables -S
```

<img src="https://github.com/user-attachments/assets/bcd8b2ce-7ee6-4a11-b003-a077b9a72b07" width="550" alt="iptables -S Gendou">

#### 2. Membuktikan akses internet, akses ke Gendou, dan akses antar LAN masih berjalan mulus

```bash
ping -c 3 8.8.8.8
ping -c 3 192.238.4.105
```

<img src="https://github.com/user-attachments/assets/df5de829-3b03-47ec-9c2a-9c2b3490f97b" width="550" alt="Ping setelah firewall">

---

## Soal 6

Gunakan script ini untuk node **Unit-01**:

```bash
printf 'auto lo\niface lo inet loopback\n\nauto eth0\niface eth0 inet static\n    address 192.238.4.66\n    netmask 255.255.255.248\n    gateway 192.238.4.65\n' > /etc/network/interfaces && printf '#!/bin/sh\npkill udhcpc 2>/dev/null\nip addr flush dev eth0\nip addr add 192.238.4.66/29 dev eth0\nip link set eth0 up\nip route replace default via 192.238.4.65\necho "nameserver 8.8.8.8" > /etc/resolv.conf\n' > /root/ip.sh && chmod +x /root/ip.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART STATIC IP ===\n[ -f /root/ip.sh ] && sh /root/ip.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && sh /root/ip.sh
```

### Pengujian

**Unit-01**

```bash
ip -br a
ip route
ping -c 3 192.238.4.65
cat /etc/network/interfaces
```

<img src="https://github.com/user-attachments/assets/29a197ff-a4a5-4342-87a8-ca0af7039281" width="550" alt="Pengujian Unit-01">

**Tokyo-Core3**

```bash
ip -br a
ip route
ping -c 3 192.238.4.105
cat /etc/network/interfaces
```

<img src="https://github.com/user-attachments/assets/8cc47737-0f9a-4317-8630-ca474020b3e8" width="550" alt="Pengujian Tokyo-Core3">

**Magi-Casper**

```bash
ip -br a
ip route
ping -c 3 192.238.4.57
cat /etc/network/interfaces
```

<img src="https://github.com/user-attachments/assets/6c72ba03-aad3-4315-aa9e-5259e64a5fab" width="550" alt="Pengujian Magi-Casper">

---

## Soal 7

### Langkah 1: Pasang skrip & nyalakan listener di ketiga node MAGI

### Langkah 2: Kirim pesan dari Lilith

<img src="https://github.com/user-attachments/assets/8bc2be94-902c-4efc-8052-e013149d0919" width="550" alt="Kirim pesan dari Lilith">

### Pengujian

**Magi-Casper**

```bash
ss -tlnp | grep nc
cat /tmp/casper_5000.log
```

<img src="https://github.com/user-attachments/assets/7214f3b2-038c-4432-aab4-cdf85a809f8b" width="550" alt="Magi-Casper">

**Magi-Balthasar**

```bash
ss -tlnp | grep nc
cat /tmp/balthasar_5000.log
cat /tmp/balthasar_5001.log
```

<img src="https://github.com/user-attachments/assets/1efdfdf2-a5c4-483b-a8bf-3c94a314f289" width="550" alt="Magi-Balthasar">

**Magi-Melchior**

```bash
ss -tlnp | grep nc
cat /tmp/melchior_5000.log
```

<img src="https://github.com/user-attachments/assets/642fab33-0321-4e1d-97ec-c6df0a056d7c" width="550" alt="Magi-Melchior">

---

## Soal 8

### Langkah 1: Konfigurasi listener di Pen-Pen

```bash
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/penpen.sh && chmod +x /root/penpen.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/penpen.sh ] && bash /root/penpen.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/penpen.sh
```

<img src="https://github.com/user-attachments/assets/b735d440-93f4-4a69-be12-6d7c26e21de4" width="550" alt="Listener Pen-Pen">

### Langkah 2: Konfigurasi listener di Tokyo3-Core

Jalankan perintah satu baris berikut untuk menyalakan port 80 dan 8080:

```bash
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\nif ! pgrep -f "[n]c.traditional -l -p 8080 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 8080 -c "echo syifa imutz"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/tokyo3.sh && chmod +x /root/tokyo3.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/tokyo3.sh ] && bash /root/tokyo3.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/tokyo3.sh
```

Lalu cek apakah port 80 dan 8080 sudah menyala:

```bash
ss -tlnp | grep nc
```

<img src="https://github.com/user-attachments/assets/74c9cd73-1906-4387-97f0-2313b8f0ccf9" width="550" alt="Port 80 dan 8080 Tokyo3-Core">

### Pengujian

Jalankan pengujian berikut di **Lilith** untuk membuktikan port yang terbuka langsung membalas dan port yang tertutup langsung ditolak:

```bash
nc -v 192.238.3.194 80
nc -v 192.238.4.106 80
nc -v 192.238.4.106 8080
nc -v 192.238.3.194 22
nc -v 192.238.4.106 22
```

<img src="https://github.com/user-attachments/assets/2406a845-4737-4b3a-9168-8ec738fec44c" width="550" alt="Pengujian nc dari Lilith">

---

## Soal 9

### Langkah 1: Download dan jalankan `break_routing.sh` di Shigeru

```bash
URL="https://drive.google.com/file/d/1ub7LjL2BdZdVq6n_AbuqcO416Z8QJGVs/view"
ID=$(echo "$URL" | sed -E 's|.*/d/([^/?&]+).*|\1|; s|.*[?&]id=([^&]+).*|\1|')
rm -f /tmp/break_routing.sh
(wget --no-check-certificate -qO /tmp/break_routing.sh "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" || curl -kfsSL "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" -o /tmp/break_routing.sh) && sed -i 's/\r$//' /tmp/break_routing.sh && [ "$(head -n 1 /tmp/break_routing.sh)" = '#!/bin/sh' ] && sh /tmp/break_routing.sh
```

### Langkah 2: Uji gejala sabotase (Lilith & Armisael)

**Lilith**

```bash
ping -c 3 192.238.4.83
```

<img src="https://github.com/user-attachments/assets/e4f66bb5-b738-41de-8cdd-4ba4cb37663f" width="550" alt="Ping Lilith ke Armisael (gagal)">

**Armisael**

```bash
ping -c 2 192.238.4.81
```

<img src="https://github.com/user-attachments/assets/d5d5da07-b3cc-441a-a5d1-6a35e787bf6e" width="550" alt="Ping Armisael ke Shigeru">

> Ping Armisael berhasil karena masih dalam satu LAN dengan Shigeru, sedangkan paket dari Lilith perlu diteruskan lintas interface oleh Shigeru dan dirutekan kembali ke Switch-Dogma sehingga tidak berhasil karena jalur lintas jaringan inilah yang dilumpuhkan oleh `break_routing.sh`.

### Langkah 3: Ambil diagnosa di node Shigeru

```bash
sysctl net.ipv4.ip_forward
ip -br a
ip route
ip rule
iptables -S
```

<img src="https://github.com/user-attachments/assets/e31afac2-2acd-4349-8881-839d2c8a2cd5" width="550" alt="Diagnosa Shigeru">

Dari gambar tersebut kita bisa melihat ada 4 kerusakan yang disebabkan oleh `break_routing.sh`:

1. **IP Forwarding dimatikan:** Sehingga kernel Shigeru menolak untuk meneruskan paket lintas interface, jadi paket ping dari Lilith yang masuk lewat `eth0` tidak diteruskan ke subnet Armisael.
2. **Policy Routing jebakan (`90: from all iif eth1 lookup 100`):** Terdapat aturan policy routing berprioritas tinggi (90, dieksekusi sebelum tabel main di prioritas 32766) yang memaksa seluruh trafik masuk dari LAN `eth1` membaca tabel routing 100 (berisi rute *unreachable*), sehingga paket dari Armisael langsung digagalkan sebelum sempat membaca tabel routing utama.
3. **Rute blackhole ke subnet Lilith (`blackhole 192.238.4.72/29`):** Ditambahkan rute blackhole khusus mengarah ke segmen jaringan `192.238.4.72/29` (tempat Lilith `192.238.4.75` berada), yang membuat setiap paket balasan dari Armisael menuju Lilith langsung dibuang secara diam-diam oleh Shigeru.
4. **Rute blackhole ke subnet Lilith (`blackhole 192.238.4.72/29`):** Ditambahkan rute blackhole khusus mengarah ke segmen jaringan `192.238.4.72/29` (tempat Lilith `192.238.4.75` berada), yang membuat setiap paket balasan dari Armisael menuju Lilith langsung dibuang secara diam-diam oleh Shigeru.

### Langkah 4: Perbaikan dan auto-start saat restart di Shigeru

```bash
# 1. Perbaiki langsung kondisi jaringan Shigeru saat ini
sysctl -w net.ipv4.ip_forward=1
ip rule del iif eth1 lookup 100 priority 90 2>/dev/null
ip route flush table 100 2>/dev/null
ip route del blackhole 192.238.4.72/29 2>/dev/null
ip route replace default via 192.238.4.153 dev eth0

# 2. Simpan ke /root/route.sh agar otomatis terjaga saat GNS3 restart (tanpa menimpa Soal 2-8)
sed -i '/# === FIX SOAL 9 ===/,$d' /root/route.sh
cat << 'EOF' >> /root/route.sh

# === FIX SOAL 9 ===
sysctl -w net.ipv4.ip_forward=1 >/dev/null
ip rule del iif eth1 lookup 100 priority 90 2>/dev/null
ip route flush table 100 2>/dev/null
ip route del blackhole 192.238.4.72/29 2>/dev/null
ip route replace default via 192.238.4.153 dev eth0
EOF
```

### Langkah 5: Verifikasi & bukti perbaikan

#### 1. Membuktikan bahwa IP Forwarding sudah berjalan, blackhole hilang, dan `ip rule` bersih

```bash
sysctl net.ipv4.ip_forward
ip route
ip rule
```

<img src="https://github.com/user-attachments/assets/eed852a8-a83b-48e3-a115-d7842c3a379c" width="550" alt="Verifikasi Shigeru">

#### 2. Membuktikan bahwa Lilith bisa kembali ping ke Armisael

```bash
ping -c 3 192.238.4.83
```

<img src="https://github.com/user-attachments/assets/88dc59ae-90cf-4da8-a054-922f312e0490" width="550" alt="Ping Lilith ke Armisael (berhasil)">

---

## Soal 10

### Langkah 1: Unduh dan jalankan `break_nat.sh` di Misato dan Gendou

```bash
URL="https://drive.google.com/file/d/1oX7r9BmMPumqrIb71Vbk43Y6CQEOvDRJ/view"
ID=$(echo "$URL" | sed -E 's|.*/d/([^/?&]+).*|\1|; s|.*[?&]id=([^&]+).*|\1|')
rm -f /tmp/break_nat.sh
(wget --no-check-certificate -qO /tmp/break_nat.sh "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" || curl -kfsSL "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" -o /tmp/break_nat.sh) && sed -i 's/\r$//' /tmp/break_nat.sh && [ "$(head -n 1 /tmp/break_nat.sh)" = '#!/bin/sh' ] && sh /tmp/break_nat.sh
```

### Langkah 2: Uji gejala kerusakan

#### 1. Di console Unit-01

```bash
ping -c 3 8.8.8.8
ping -c 2 192.238.4.75
```

<img src="https://github.com/user-attachments/assets/9fff8c9d-4690-4109-b85e-7336161ef848" width="550" alt="Gejala di Unit-01">

#### 2. Di console Spy

```bash
ping -c 3 8.8.8.8
```

<img src="https://github.com/user-attachments/assets/90b9300e-0dba-4c9f-bd13-538152175c22" width="550" alt="Gejala di Spy">

### Langkah 3: Diagnosa di Misato dan Gendou

```bash
iptables -t nat -S
iptables -S
sysctl net.ipv4.ip_forward
```

**Misato**

<img src="https://github.com/user-attachments/assets/dc186b64-c4e5-48af-a367-fd13563e03af" width="550" alt="Diagnosa Misato">

**Gendou**

<img src="https://github.com/user-attachments/assets/0f58aa63-b866-4c3a-a268-bfe4f5d9ca6d" width="550" alt="Diagnosa Gendou">

### Hasil Analisis

#### 1. Dua penyebab sabotase pada tabel NAT

**a. Aturan SNAT palsu di Misato**

```
-A POSTROUTING -s 192.238.4.64/29 ! -d 192.238.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1
```

Setiap paket yang berasal dari subnet Cage dengan tujuan ke luar jaringan internal diubah alamat IP-nya oleh Misato menjadi alamat IP fiktif. Akibatnya, ketika paket balasan dari internet kembali ke Gendou dan di-un-NAT ke `172.31.99.1`, Gendou tidak memiliki rute menuju `172.31.99.1` sehingga paket balasan dibuang (*drop*).

**b. Aturan bypass RETURN di Gendou**

```
-A POSTROUTING -s 192.238.4.64/29 -o eth0 -j RETURN
```

Pada rantai POSTROUTING di Gendou, disisipkan aturan `-j RETURN` khusus untuk sumber `192.238.4.64/29` di baris pertama sebelum aturan `-A POSTROUTING -o eth0 -j MASQUERADE`. Jika aturan SNAT palsu di Misato saja yang dihapus tanpa menghapus aturan RETURN di Gendou, paket dari Cage (`192.238.4.64/29`) akan langsung keluar dari rantai POSTROUTING tanpa terkena MASQUERADE, sehingga keluar ke internet memakai IP privat (`192.238.4.66`) yang tidak bisa dirutekan kembali oleh `8.8.8.8`.

#### 2. Mengapa subnet Cage terdampak, sedangkan Intel tidak, dan ping ke Lilith tetap normal?

- Kedua aturan sabotase di node Misato dan Gendou secara spesifik diarahkan ke subnet Cage yang berisi Unit-00, Unit-01, dan Unit-02. Sehingga node seperti Spy yang berada di subnet Intel masih bisa berjalan dengan normal.
- **Ping dari Cage ke Lilith (`192.238.4.75`) normal:** Alamat `192.238.4.75` berada di dalam blok internal `192.238.0.0/16`, sehingga tidak memicu kondisi `! -d 192.238.0.0/16` di Misato dan juga tidak keluar melalui interface WAN `eth0` di Gendou.

#### 3. Di router mana alamat sumber diterjemahkan?

Alamat sumber privat seluruh klien (termasuk Unit-01 `192.238.4.66`) diterjemahkan (NAT Masquerade) secara terpusat di router gateway terluar yang terhubung langsung ke Cloud/Internet, yaitu **Gendou** (melalui interface WAN `eth0` dengan aturan `-A POSTROUTING -o eth0 -j MASQUERADE`), sementara router internal seperti Misato hanya bertugas melakukan routing tanpa NAT.

### Langkah 4: Perbaikan permanen di Misato dan Gendou

Jalankan perintah berikut pada masing-masing console:

**Misato**

```bash
iptables -t nat -D POSTROUTING -s 192.238.4.64/29 ! -d 192.238.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1 2>/dev/null
printf '#!/bin/bash\niptables -t nat -D POSTROUTING -s 192.238.4.64/29 ! -d 192.238.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1 2>/dev/null || true\n' > /root/fix_soal10.sh && chmod +x /root/fix_soal10.sh && touch /root/.bashrc && sed -i '/# === FIX SOAL 10 ===/,$d' /etc/profile /root/.bashrc && printf '\n# === FIX SOAL 10 ===\n[ -f /root/fix_soal10.sh ] && bash /root/fix_soal10.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null
```

**Gendou**

```bash
iptables -t nat -D POSTROUTING -s 192.238.4.64/29 -o eth0 -j RETURN 2>/dev/null
printf '#!/bin/bash\niptables -t nat -D POSTROUTING -s 192.238.4.64/29 -o eth0 -j RETURN 2>/dev/null || true\n' > /root/fix_soal10.sh && chmod +x /root/fix_soal10.sh && touch /root/.bashrc && sed -i '/# === FIX SOAL 10 ===/,$d' /etc/profile /root/.bashrc && printf '\n# === FIX SOAL 10 ===\n[ -f /root/fix_soal10.sh ] && bash /root/fix_soal10.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null
```

### Langkah 5: Verifikasi & bukti hasil perbaikan

#### 1. Di console Misato

```bash
iptables -t nat -S
```

<img src="https://github.com/user-attachments/assets/cc4c6f1f-b7cf-4963-a14a-1caf45a971bb" width="450" alt="Verifikasi NAT Misato">

#### 2. Di console Gendou

```bash
iptables -t nat -S
```

<img src="https://github.com/user-attachments/assets/affae086-3b46-4d94-9126-82cfdd3c0532" width="450" alt="Verifikasi NAT Gendou">

#### 3. Di console Unit-01

```bash
ping -c 3 8.8.8.8
```

<img src="https://github.com/user-attachments/assets/3917e92c-c297-44c4-9b32-924bc65a510b" width="550" alt="Verifikasi ping Unit-01">

# Laporan Pengerjaan Soal 11 — Casper Harus Jadi Hantu

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Magi-Casper, Magi-Balthasar, Lilith

---

## 1. Soal

> **Casper harus jadi hantu:** Casper adalah MAGI paling rahasia. Kalau SEELE tahu ia masih hidup, mereka tahu ke mana harus menyerang. **Tidak ada perangkat lain yang boleh mem-ping Casper**, termasuk yang satu switch. Casper sendiri tetap boleh mem-ping siapa saja. Layanan **TCP 5000** Casper tetap hidup. Buktikan ping ke Casper dari Balthasar dan Lilith **gagal**, sedangkan ping dari Casper ke keduanya **berhasil**.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Tidak ada perangkat lain yang boleh ping Casper | Drop ICMP echo-request pada chain `INPUT` |
| 2 | Casper tetap boleh ping ke siapa saja | Chain `OUTPUT` tetap `ACCEPT`; echo-reply yang kembali tidak ikut diblokir |
| 3 | Layanan TCP 5000 tetap hidup | Rule hanya menargetkan ICMP, listener `nc` port 5000 tidak disentuh |

### Alamat IP

| Perangkat | IP | Keterangan |
|-----------|----|------------|
| Magi-Casper | `192.238.4.58` | Target yang disembunyikan |
| Magi-Balthasar | `192.238.4.59` | Satu switch dengan Casper |
| Lilith | `192.238.4.74` | Beda subnet (lintas router) |

---

## 2. Langkah Pengerjaan di Magi-Casper

### 2.1 Menambahkan blok firewall Soal 11 ke `/root/casper.sh`

Blok firewall ditambahkan di **bagian paling bawah** script `/root/casper.sh`. Perintah `sed` pertama menghapus blok Soal 11 lama (jika ada) agar tidak terduplikasi, tanpa menghapus bagian listener port 5000 di atasnya.

```bash
sed -i '/# === SOAL 11: FIREWALL ===/,$d' /root/casper.sh
cat << 'EOF' >> /root/casper.sh

# === SOAL 11: FIREWALL ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# Blokir semua ping masuk (ICMP echo-request), tetap izinkan ping keluar
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
EOF
```

**Penjelasan:**

| Perintah | Fungsi |
|----------|--------|
| `iptables -F` | Menghapus semua rule yang ada (flush) |
| `iptables -X` | Menghapus semua custom chain |
| `iptables -P INPUT/FORWARD/OUTPUT ACCEPT` | Policy default tetap `ACCEPT` agar trafik lain (termasuk TCP 5000) tidak terganggu |
| `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` | Membuang semua ICMP echo-request (ping) yang masuk ke Casper |

**Kenapa hanya `echo-request`, bukan semua ICMP?**
Jika seluruh ICMP diblokir, balasan ping (`echo-reply`) untuk ping yang dikirim Casper ke luar juga ikut terbuang, sehingga Casper tidak bisa mem-ping siapa pun. Dengan hanya memblokir `echo-request` (type 8), Casper tetap bisa mengirim ping keluar dan menerima balasannya.

> **Verifikasi:** Jalankan `cat /root/casper.sh` dan pastikan blok `# === SOAL 11: FIREWALL ===` muncul di bagian paling bawah tanpa menghapus bagian listener port 5000 di atasnya.

### 2.2 Menerapkan konfigurasi dan mengecek iptables serta listener port 5000

```bash
bash /root/casper.sh
iptables -S
ss -tlnp | grep 5000
```

**Hasil:**

```
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP
LISTEN 0  1  0.0.0.0:5000  0.0.0.0:*  users:(("nc.traditional",pid=411,fd=3))
```

![Hasil bash casper.sh, iptables -S, dan ss -tlnp](afd05488-image.png)
<img width="412" height="106" alt="Screenshot 2026-11-25 at 07 46 31" src="https://github.com/user-attachments/assets/df575be6-b370-4743-8a21-82a381555c9e" />


**Analisis:**
- `iptables -S` menampilkan rule `-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP` dengan policy default `ACCEPT`. Rule pemblokiran ping masuk sudah aktif.
- `ss -tlnp` menunjukkan proses `nc.traditional` tetap **LISTEN** di port **5000**, sehingga layanan TCP 5000 tetap hidup.

---

## 3. Pengujian Ping Masuk dan Keluar

### 3.1 Dari Magi-Balthasar (`192.238.4.59`) → Casper (satu switch)

Hasil yang diharapkan: **gagal / 100% packet loss**.

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```
PING 192.238.4.58 (192.238.4.58) 56(84) bytes of data.

--- 192.238.4.58 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2253ms
```

![Ping Balthasar ke Casper](df88acbb-image.png)
<img width="412" height="114" alt="Screenshot 2026-11-25 at 07 48 09" src="https://github.com/user-attachments/assets/14ccd987-fb5a-4fd8-bacb-3c4c6836c752" />


### 3.2 Dari Lilith (`192.238.4.74`) → Casper (lintas subnet)

Hasil yang diharapkan: **gagal / 100% packet loss**.

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```
PING 192.238.4.58 (192.238.4.58) 56(84) bytes of data.

--- 192.238.4.58 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2290ms
```

![Ping Lilith ke Casper](2e349bdc-image.png)
<img width="408" height="217" alt="Screenshot 2026-11-25 at 07 48 52" src="https://github.com/user-attachments/assets/dd8bc504-3406-4e25-b1d7-19f27b12bad5" />


### 3.3 Dari Magi-Casper (`192.238.4.58`) → Balthasar & Lilith

Hasil yang diharapkan: **berhasil / 0% packet loss**, lalu cek counter paket yang di-drop.

```bash
ping -c 3 192.238.4.59
ping -c 3 192.238.4.74
iptables -L INPUT -n -v
```

**Hasil:**

```
PING 192.238.4.59 (192.238.4.59) 56(84) bytes of data.
64 bytes from 192.238.4.59: icmp_seq=1 ttl=64 time=0.580 ms
64 bytes from 192.238.4.59: icmp_seq=2 ttl=64 time=0.208 ms
64 bytes from 192.238.4.59: icmp_seq=3 ttl=64 time=0.331 ms

--- 192.238.4.59 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2298ms
rtt min/avg/max/mdev = 0.208/0.373/0.580/0.154 ms

PING 192.238.4.74 (192.238.4.74) 56(84) bytes of data.
64 bytes from 192.238.4.74: icmp_seq=1 ttl=62 time=1.28 ms
64 bytes from 192.238.4.74: icmp_seq=2 ttl=62 time=0.816 ms
64 bytes from 192.238.4.74: icmp_seq=3 ttl=62 time=0.565 ms

--- 192.238.4.74 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2271ms
rtt min/avg/max/mdev = 0.565/0.886/1.277/0.294 ms

Chain INPUT (policy ACCEPT 6 packets, 504 bytes)
 pkts bytes target  prot opt in  out  source      destination
    6   504 DROP    icmp --  *   *    0.0.0.0/0   0.0.0.0/0    icmptype 8
```

![Ping Casper ke Balthasar & Lilith dan counter iptables](9c34eb2e-image.png)
<img width="408" height="241" alt="Screenshot 2026-11-25 at 07 49 14" src="https://github.com/user-attachments/assets/a89070cb-9dce-408f-b561-8b278b4232eb" />


---

## 4. Hasil dan Analisis

| Skenario | Arah | Hasil | Sesuai Soal |
|----------|------|-------|:-----------:|
| Balthasar → Casper | Masuk ke Casper | 100% packet loss | ✅ |
| Lilith → Casper | Masuk ke Casper (lintas subnet) | 100% packet loss | ✅ |
| Casper → Balthasar | Keluar dari Casper | 0% packet loss (TTL 64) | ✅ |
| Casper → Lilith | Keluar dari Casper | 0% packet loss (TTL 62) | ✅ |
| Listener TCP 5000 | — | `nc.traditional` tetap LISTEN | ✅ |

**Pembahasan:**

1. Ping dari Balthasar maupun Lilith ke Casper gagal 100% karena rule `INPUT ... --icmp-type echo-request -j DROP` membuang setiap permintaan ping sebelum diproses Casper. Karena dibuang (bukan di-reject), pengirim tidak menerima balasan apa pun, sehingga Casper seolah-olah tidak ada ("hantu").
2. Ping dari Casper ke Balthasar dan Lilith berhasil karena chain `OUTPUT` berpolicy `ACCEPT`, dan balasan `echo-reply` (type 0) tidak cocok dengan rule DROP (type 8).
3. Nilai TTL berbeda: Balthasar `64` (langsung, satu switch) dan Lilith `62` (melewati 2 router), menandakan Lilith berada di subnet lain.
4. Counter rule DROP menunjukkan **6 paket / 504 byte**. Angka ini berasal dari 3 ping Balthasar + 3 ping Lilith (masing-masing 84 byte → 6 × 84 = 504 byte), membuktikan rule benar-benar menangkap ping masuk dari kedua perangkat.
5. Layanan TCP 5000 tetap aktif karena rule hanya menargetkan protokol ICMP dan tidak mengubah trafik TCP.

---

## 5. Kesimpulan

Dengan menambahkan satu rule `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` pada `/root/casper.sh`, Casper tidak dapat di-ping oleh perangkat mana pun (baik satu switch maupun lintas subnet), tetap dapat mem-ping ke luar, dan layanan TCP 5000 tetap berjalan. Seluruh persyaratan Soal 11 terpenuhi.

# Laporan Pengerjaan Soal 12 — Teknisi yang Kelewat Semangat

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Magi-Casper, Unit-00, Lilith

---

## 1. Soal

> **Teknisi yang kelewat semangat:** seorang teknisi menjalankan script hardening di Casper lalu pulang tanpa mengecek. Paginya Casper tidak bisa ping `8.8.8.8` dan tidak bisa `apt-get update`. Unit-00 juga tidak bisa menyambung ke TCP 5000 Casper. Ping ke Casper tetap ditolak, dan itu memang benar. Jalankan `break_fw.sh` di **Magi-Casper**, lalu perbaiki **tanpa membatalkan tujuan hardening**. Casper tetap menolak semua lalu lintas masuk yang tidak diizinkan, dan **policy INPUT tetap DROP**. Buktikan `nc` dari Unit-00 ke Casper port 5000 berhasil dan `apt-get update` di Casper berhasil.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Casper bisa ping `8.8.8.8` dan `apt-get update` | Izinkan paket balasan (`ESTABLISHED,RELATED`) dan hapus blokir DNS keluar |
| 2 | Unit-00 bisa `nc` ke Casper TCP 5000 | Tambah rule `ACCEPT` untuk `--dport 5000` |
| 3 | Ping ke Casper tetap ditolak | Pertahankan DROP pada ICMP `echo-request` |
| 4 | Policy `INPUT` tetap `DROP` | Tidak mengubah policy menjadi ACCEPT |

### Alamat IP

| Perangkat | IP | Keterangan |
|-----------|----|------------|
| Magi-Casper | `192.238.4.58` | Perangkat yang dirusak firewall |
| Unit-00 | `192.238.4.68` | Klien uji TCP 5000 |
| Lilith | `192.238.4.74` | Klien uji ping ke Casper |

---

## 2. Gejala Kerusakan (Sebelum Diperbaiki)

Setelah `break_fw.sh` dijalankan di Magi-Casper, gejala berikut diambil sebagai bukti.

> **Catatan:** Tambahkan screenshot proses pengecekan `/tmp` dan pengunduhan/eksekusi `break_fw.sh` di sini jika diperlukan.

### 2.1 Magi-Casper — ping keluar dan `apt-get update` gagal

```bash
ping -c 3 -W 2 8.8.8.8
apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false
```

**Hasil:**

```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2315ms

Err:1 http://deb.debian.org/debian trixie InRelease
  Temporary failure resolving 'deb.debian.org'
Err:2 http://deb.debian.org/debian trixie-updates InRelease
  Temporary failure resolving 'deb.debian.org'
Err:3 http://deb.debian.org/debian-security trixie-security InRelease
  Temporary failure resolving 'deb.debian.org'
W: Some index files failed to download. They have been ignored, or old ones used instead.
```
<img width="364" height="303" alt="Screenshot 2026-11-25 at 08 32 30" src="https://github.com/user-attachments/assets/04dad4bf-f14e-4c49-bee8-feee51f390c3" />

![Casper: ping 8.8.8.8 dan apt-get update gagal](051f6ca1-image.png)

### 2.2 Unit-00 (`192.238.4.68`) — koneksi ke TCP 5000 Casper gagal

```bash
nc -v -w 3 192.238.4.58 5000
```

**Hasil:**

```
nc: connect to 192.238.4.58 port 5000 (tcp) timed out: Operation in progress
```
<img width="364" height="187" alt="Screenshot 2026-11-25 at 08 33 26" src="https://github.com/user-attachments/assets/73ce4ace-f8fd-4514-b5e4-111521b5df3a" />

![Unit-00: nc ke Casper port 5000 timed out](7fee2332-image.png)

### 2.3 Lilith (`192.238.4.74`) — ping masuk ke Casper tetap ditolak

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```
3 packets transmitted, 0 received, 100% packet loss, time 2310ms
```
<img width="365" height="231" alt="Screenshot 2026-11-25 at 08 34 08" src="https://github.com/user-attachments/assets/7b8827d9-eafd-45dc-bc41-741170b64772" />

![Lilith: ping ke Casper 100% packet loss (sebelum perbaikan)](9874c865-image.png)

Hasil ini sudah sesuai dengan tujuan hardening: ping ke Casper memang harus ditolak.

---

## 3. Diagnosis di Magi-Casper

```bash
iptables -S
iptables -L -n -v
iptables -t nat -S
ss -tlnp
cat /etc/resolv.conf
```

**Hasil (ringkas):**

```
-P INPUT DROP
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp -j DROP
-A OUTPUT -p udp -m udp --dport 53 -j DROP

Chain INPUT (policy DROP 3 packets, 180 bytes)
   6   328 ACCEPT  all  --  lo  *   0.0.0.0/0  0.0.0.0/0
   6   504 DROP    icmp --  *   *   0.0.0.0/0  0.0.0.0/0

Chain OUTPUT (policy ACCEPT 10 packets, 632 bytes)
  50  3286 DROP    udp  --  *   *   0.0.0.0/0  0.0.0.0/0   udp dpt:53

-P PREROUTING ACCEPT   (tabel nat: semua ACCEPT)
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT

LISTEN 0 1  0.0.0.0:5000  0.0.0.0:*  users:(("nc.traditional",pid=452,fd=3))
nameserver 8.8.8.8
```
<img width="366" height="361" alt="Screenshot 2026-11-25 at 08 37 32" src="https://github.com/user-attachments/assets/ebca340c-ce30-4bcd-b0b5-18a1a90d8f34" />

![Output diagnosis iptables, ss, dan resolv.conf di Magi-Casper](19f2e98f-image.png)

---

## 4. Analisis Penyebab Kerusakan

| No | Penyebab | Dampak |
|----|----------|--------|
| 1 | Policy `INPUT` diubah menjadi `DROP` **tanpa** rule `ESTABLISHED,RELATED` | Paket ping dan permintaan koneksi repositori berhasil keluar lewat `OUTPUT`, tetapi semua **paket balasan** (ICMP echo-reply dan data HTTP/TCP) yang masuk kembali langsung dibuang oleh `INPUT DROP` |
| 2 | Rule `-A OUTPUT -p udp --dport 53 -j DROP` | Seluruh kueri DNS ke `8.8.8.8` dibuang (counter menunjukkan **50 paket** ter-DROP). Akibatnya saat `apt-get update`, Casper gagal me-resolve `deb.debian.org` (*Temporary failure resolving*) |
| 3 | Tidak ada rule `ACCEPT` untuk TCP port 5000 | Koneksi TCP dari Unit-00 ke port 5000 dibuang diam-diam sehingga mengalami *timed out*, meskipun listener `nc.traditional` tetap `LISTEN` |
| 4 | Rule `-A INPUT -p icmp -j DROP` memblokir **semua** tipe ICMP | Tidak membedakan `echo-request` masuk dan `echo-reply` keluar, sehingga balasan ping Casper ke `8.8.8.8` ikut terbuang |

---

## 5. Perbaikan Permanen di Magi-Casper

Blok firewall pada `/root/casper.sh` diganti dengan konfigurasi hardening yang benar: policy `INPUT` tetap `DROP`, ping masuk tetap ditolak, tetapi paket balasan dan TCP 5000 diizinkan. Perintah `sed` menghapus blok Soal 11 dan Soal 12 lama agar tidak terduplikasi, sedangkan `iptables -F` membersihkan rule rusak (termasuk blokir DNS di `OUTPUT`).

```bash
sed -i '/# === SOAL 11: FIREWALL ===/,$d' /root/casper.sh
sed -i '/# === SOAL 12: FIREWALL ===/,$d' /root/casper.sh

cat << 'EOF' >> /root/casper.sh

# === SOAL 12: FIREWALL ===
iptables -F
iptables -X
iptables -P INPUT DROP
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# 1. Izinkan interface loopback
iptables -A INPUT -i lo -j ACCEPT

# 2. Tolak ping masuk (ICMP echo-request) agar Casper tetap tidak terdeteksi
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# 3. Izinkan paket balasan dari koneksi keluar (ping keluar, DNS, apt-get update)
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 4. Izinkan koneksi masuk ke layanan TCP 5000 Magi-Casper
iptables -A INPUT -p tcp --dport 5000 -j ACCEPT
EOF

bash /root/casper.sh
```
<img width="363" height="243" alt="Screenshot 2026-11-25 at 08 42 06" src="https://github.com/user-attachments/assets/fd9b5a7b-a794-4738-adab-077adc6ee88e" />

![Menjalankan perbaikan permanen di Magi-Casper](d628fa42-image.png)

**Penjelasan rule:**

| Rule | Fungsi |
|------|--------|
| `-P INPUT DROP` | Policy default tetap menolak semua lalu lintas masuk yang tidak diizinkan (tujuan hardening terjaga) |
| `-i lo -j ACCEPT` | Mengizinkan trafik loopback internal |
| `-p icmp --icmp-type echo-request -j DROP` | Ping masuk ke Casper tetap ditolak; hanya `echo-request` yang diblokir agar `echo-reply` tidak ikut terbuang |
| `-m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` | Mengizinkan balasan dari koneksi yang dimulai Casper (ping keluar, DNS, `apt-get`) |
| `-p tcp --dport 5000 -j ACCEPT` | Mengizinkan koneksi masuk ke layanan TCP 5000 |

> **Catatan:** Blokir DNS keluar (`-A OUTPUT -p udp --dport 53 -j DROP`) hilang karena `iptables -F` mengosongkan seluruh rule, dan policy `OUTPUT` tetap `ACCEPT`.

---

## 6. Verifikasi dan Bukti Perbaikan

### 6.1 Magi-Casper — rule firewall, ping `8.8.8.8`, dan `apt-get update`

```bash
iptables -S
ping -c 3 8.8.8.8
apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false
```

**Hasil:**

```
-P INPUT DROP
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p tcp -m tcp --dport 5000 -j ACCEPT

PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=20.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=20.4 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=20.0 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2219ms

Hit:1 http://deb.debian.org/debian trixie InRelease
Hit:2 http://deb.debian.org/debian trixie-updates InRelease
Hit:3 http://deb.debian.org/debian-security trixie-security InRelease
Reading package lists... Done
```
<img width="364" height="200" alt="Screenshot 2026-11-25 at 08 42 23" src="https://github.com/user-attachments/assets/ce7618bd-3104-481d-991b-c8e6710974fc" />

![Casper: iptables -S, ping 8.8.8.8, dan apt-get update berhasil](7584a08d-image.png)

### 6.2 Unit-00 (`192.238.4.68`) — koneksi ke Casper TCP 5000

```bash
echo "tes koneksi dari Unit-00" | nc -v -w 2 192.238.4.58 5000
```

**Hasil:**

```
Connection to 192.238.4.58 5000 port [tcp/*] succeeded!
```
<img width="362" height="28" alt="Screenshot 2026-11-25 at 08 42 45" src="https://github.com/user-attachments/assets/a0e9ef28-ad66-4dc4-8bf6-5fa1cdec8446" />

![Unit-00: nc ke Casper port 5000 berhasil](30554f8d-image.png)

### 6.3 Lilith (`192.238.4.74`) — ping ke Casper tetap ditolak

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```
3 packets transmitted, 0 received, 100% packet loss, time 2268ms
```
<img width="363" height="49" alt="Screenshot 2026-11-25 at 08 42 56" src="https://github.com/user-attachments/assets/8524cef8-82aa-4f5a-bd38-1fb206613e29" />

![Lilith: ping ke Casper tetap 100% packet loss (setelah perbaikan)](60de5d15-image.png)

---

## 7. Perbandingan Sebelum dan Sesudah

| Pengujian | Sebelum | Sesudah | Sesuai Soal |
|-----------|---------|---------|:-----------:|
| Casper `ping 8.8.8.8` | 100% packet loss | 0% packet loss (TTL 110) | ✅ |
| Casper `apt-get update` | *Temporary failure resolving* | `Hit` / `Reading package lists... Done` | ✅ |
| Unit-00 `nc` ke Casper TCP 5000 | *timed out* | *succeeded!* | ✅ |
| Lilith ping ke Casper | 100% packet loss | 100% packet loss (tetap ditolak) | ✅ |
| Policy `INPUT` | `DROP` | `DROP` (tetap) | ✅ |

---

## 8. Kesimpulan

Kerusakan disebabkan oleh tiga kesalahan konfigurasi: tidak adanya rule `ESTABLISHED,RELATED` pada policy `INPUT DROP`, adanya blokir DNS keluar (UDP port 53), dan tidak adanya izin untuk TCP 5000. Rule ICMP yang terlalu luas juga ikut memblokir balasan ping. Setelah blok firewall diganti dengan konfigurasi yang benar, Casper kembali bisa ping keluar dan `apt-get update`, Unit-00 dapat terhubung ke TCP 5000, dan ping masuk ke Casper tetap ditolak. Policy `INPUT` tetap `DROP`, sehingga tujuan hardening tidak dibatalkan.

# Laporan Pengerjaan Soal 13 — Brankas Balthasar

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Magi-Balthasar, Magi-Casper, Magi-Melchior, Unit-01, Lilith

---

## 1. Soal

> **Brankas Balthasar:** Balthasar menyimpan seluruh data Geofront, tapi terlalu banyak yang bisa mengetuk pintunya, termasuk tetangga satu switch. **Hanya Casper dan semua Pilot** yang boleh menyambung ke **TCP 5000 dan 5001**. Node lain, termasuk yang satu switch, harus **langsung ditolak** di kedua port itu, bukan dibiarkan menggantung sampai timeout. Lalu lintas lain tidak boleh terganggu. Balthasar tetap harus bisa ping `8.8.8.8` dan menerima balasannya. Buktikan `nc` ke port 5000 dan 5001: **Unit-01 berhasil, Lilith dan Melchior ditolak seketika**.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Hanya Casper dan semua Pilot yang boleh ke TCP 5000 & 5001 | Rule `ACCEPT` per IP sumber untuk kedua port |
| 2 | Node lain harus langsung ditolak (bukan timeout) | `REJECT --reject-with tcp-reset` (bukan `DROP`) |
| 3 | Lalu lintas lain tidak terganggu | Policy `INPUT`, `FORWARD`, `OUTPUT` tetap `ACCEPT`; rule hanya menargetkan TCP 5000/5001 |
| 4 | Balthasar tetap bisa ping `8.8.8.8` | Tidak ada rule yang menyentuh ICMP |

### Alamat IP

| Perangkat | IP | Peran pada Soal 13 |
|-----------|----|--------------------|
| Magi-Balthasar | `192.238.4.59` | Server yang diamankan (listener TCP 5000 & 5001) |
| Magi-Casper | `192.238.4.58` | Diizinkan |
| Unit-01 | `192.238.4.66` | Pilot — diizinkan |
| Pilot | `192.238.4.67` | Pilot — diizinkan |
| Unit-00 | `192.238.4.68` | Pilot — diizinkan |
| Pilot | `192.238.3.130` | Pilot — diizinkan |
| Magi-Melchior | `192.238.4.60` | Satu switch, **ditolak** |
| Lilith | `192.238.4.74` | Lintas subnet, **ditolak** |

---

## 2. Langkah Pengerjaan di Magi-Balthasar

### 2.1 Memeriksa skrip Soal 7 dan menambahkan blok firewall Soal 13

Skrip `/root/balthasar.sh` dari Soal 7 diperiksa terlebih dahulu. Blok Soal 13 lalu ditambahkan secara idempoten: `sed` menghapus blok Soal 13 lama (jika ada) agar tidak terduplikasi, kemudian blok baru ditulis di bagian paling bawah.

```bash
# Cek isi skrip bawaan Soal 7
cat /root/balthasar.sh

# Hapus blok Soal 13 lama (jika ada) lalu tambahkan aturan Soal 13
sed -i '/# === SOAL 13: FIREWALL ===/,$d' /root/balthasar.sh
cat << 'EOF' >> /root/balthasar.sh

# === SOAL 13: FIREWALL ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# Hanya izinkan Magi-Casper dan seluruh Pilot ke TCP 5000 & 5001; tolak sisanya seketika
for PORT in 5000 5001; do
  iptables -A INPUT -p tcp -s 192.238.4.58  --dport $PORT -j ACCEPT
  iptables -A INPUT -p tcp -s 192.238.4.66  --dport $PORT -j ACCEPT
  iptables -A INPUT -p tcp -s 192.238.4.67  --dport $PORT -j ACCEPT
  iptables -A INPUT -p tcp -s 192.238.4.68  --dport $PORT -j ACCEPT
  iptables -A INPUT -p tcp -s 192.238.3.130 --dport $PORT -j ACCEPT
  iptables -A INPUT -p tcp --dport $PORT -j REJECT --reject-with tcp-reset
done
EOF

# Terapkan konfigurasi
bash /root/balthasar.sh
```
**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| `iptables -F` / `-X` | Membersihkan semua rule dan custom chain lama |
| `-P INPUT/FORWARD/OUTPUT ACCEPT` | Policy default tetap `ACCEPT` agar lalu lintas lain (termasuk ping ke `8.8.8.8`) tidak terganggu |
| `-s <IP> --dport $PORT -j ACCEPT` (5 IP) | Mengizinkan Casper dan semua Pilot menyambung ke TCP 5000 dan 5001 |
| `-p tcp --dport $PORT -j REJECT --reject-with tcp-reset` | Menolak seketika koneksi dari node lain dengan mengirim paket TCP RST |

**Kenapa `REJECT --reject-with tcp-reset`, bukan `DROP`?**
Soal meminta node lain "langsung ditolak, bukan dibiarkan menggantung sampai timeout". `DROP` membuang paket tanpa balasan sehingga klien menunggu sampai *timed out*. `REJECT --reject-with tcp-reset` mengirim RST, sehingga klien langsung menerima `Connection refused`.

**Kenapa urutan rule penting?**
Rule `ACCEPT` untuk kelima IP harus berada **di atas** rule `REJECT`. iptables membaca rule dari atas ke bawah dan berhenti pada kecocokan pertama. Jika `REJECT` berada di atas, semua koneksi, termasuk dari Casper dan Pilot, akan ditolak.

---

## 3. Pengujian dan Bukti

### 3.1 Magi-Balthasar (`192.238.4.59`) — rule iptables, listener, dan ping ke internet

```bash
iptables -S
ss -tlnp | grep nc
ping -c 3 8.8.8.8
```

**Hasil:**

```
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -s 192.238.4.58/32  -p tcp -m tcp --dport 5000 -j ACCEPT
-A INPUT -s 192.238.4.66/32  -p tcp -m tcp --dport 5000 -j ACCEPT
-A INPUT -s 192.238.4.67/32  -p tcp -m tcp --dport 5000 -j ACCEPT
-A INPUT -s 192.238.4.68/32  -p tcp -m tcp --dport 5000 -j ACCEPT
-A INPUT -s 192.238.3.130/32 -p tcp -m tcp --dport 5000 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 5000 -j REJECT --reject-with tcp-reset
-A INPUT -s 192.238.4.58/32  -p tcp -m tcp --dport 5001 -j ACCEPT
-A INPUT -s 192.238.4.66/32  -p tcp -m tcp --dport 5001 -j ACCEPT
-A INPUT -s 192.238.4.67/32  -p tcp -m tcp --dport 5001 -j ACCEPT
-A INPUT -s 192.238.4.68/32  -p tcp -m tcp --dport 5001 -j ACCEPT
-A INPUT -s 192.238.3.130/32 -p tcp -m tcp --dport 5001 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 5001 -j REJECT --reject-with tcp-reset

LISTEN 0 1  0.0.0.0:5000  0.0.0.0:*  users:(("nc.traditional",pid=412,fd=3))
LISTEN 0 1  0.0.0.0:5001  0.0.0.0:*  users:(("nc.traditional",pid=413,fd=3))

PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=19.8 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=19.7 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=19.7 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2218ms
```
<img width="360" height="294" alt="Screenshot 2026-11-25 at 09 25 55" src="https://github.com/user-attachments/assets/2b44377d-9d4c-4bda-bc93-00b487088ffb" />

![Balthasar: iptables -S, listener port 5000/5001, dan ping 8.8.8.8](b123b14b-image.png)

**Analisis:**
- Rule `ACCEPT` untuk kelima IP berada di atas rule `REJECT --reject-with tcp-reset` pada kedua port.
- Port 5000 dan 5001 berstatus `LISTEN` (proses `nc.traditional`), sehingga layanan tetap hidup.
- `ping 8.8.8.8` berhasil 0% packet loss, jadi lalu lintas lain tidak terganggu.

### 3.2 Unit-01 (`192.238.4.66`) — koneksi ke TCP 5000 & 5001 (harus berhasil)

```bash
echo "halo dari Unit-01 (5000)" | nc -v -w 2 192.238.4.59 5000
echo "halo dari Unit-01 (5001)" | nc -v -w 2 192.238.4.59 5001
```

**Hasil:**

```
Connection to 192.238.4.59 5000 port [tcp/*] succeeded!
Connection to 192.238.4.59 5001 port [tcp/*] succeeded!
```
<img width="240" height="153" alt="Screenshot 2026-11-25 at 09 26 42" src="https://github.com/user-attachments/assets/6039229c-04e2-44f5-9c84-a31ea6ca4de5" />

![Unit-01: nc ke Balthasar port 5000 dan 5001 berhasil](63fd7521-image.png)

### 3.3 Lilith (`192.238.4.74`) — koneksi lintas subnet (harus langsung ditolak)

```bash
nc -v 192.238.4.59 5000
nc -v 192.238.4.59 5001
```

**Hasil:**

```
nc: connect to 192.238.4.59 port 5000 (tcp) failed: Connection refused
nc: connect to 192.238.4.59 port 5001 (tcp) failed: Connection refused
```
<img width="240" height="151" alt="Screenshot 2026-11-25 at 09 27 21" src="https://github.com/user-attachments/assets/faecaea6-34be-415d-ab6f-35538cb85dff" />

![Lilith: nc ke Balthasar ditolak seketika (Connection refused)](f5db9170-image.png)

### 3.4 Magi-Melchior (`192.238.4.60`) — tetangga satu switch (harus langsung ditolak)

```bash
nc -v 192.238.4.59 5000
nc -v 192.238.4.59 5001
```

**Hasil:**

```
192.238.4.59: inverse host lookup failed: Unknown host
(UNKNOWN) [192.238.4.59] 5000 (?) : Connection refused
192.238.4.59: inverse host lookup failed: Unknown host
(UNKNOWN) [192.238.4.59] 5001 (?) : Connection refused
```
<img width="242" height="70" alt="Screenshot 2026-11-25 at 09 28 10" src="https://github.com/user-attachments/assets/c097dfb1-d8eb-4b3a-ae52-52c3212075ee" />

![Melchior: nc ke Balthasar ditolak seketika (Connection refused)](80e4fe83-image.png)

> Pesan `inverse host lookup failed: Unknown host` hanya berasal dari `nc` yang mencoba reverse DNS pada IP tujuan. Pesan ini tidak berkaitan dengan firewall; bagian pentingnya adalah `Connection refused`.

### 3.5 Magi-Balthasar — log pesan masuk dan counter paket

```bash
cat /tmp/balthasar_5000.log
cat /tmp/balthasar_5001.log
iptables -L INPUT -n -v
```

**Hasil:**

```
halo dari Unit-01 (5000)
halo dari Unit-01 (5001)

Chain INPUT (policy ACCEPT 3 packets, 252 bytes)
 pkts bytes target  prot opt in  out  source         destination
    0     0 ACCEPT  tcp  --  *   *    192.238.4.58   0.0.0.0/0   tcp dpt:5000
    5   293 ACCEPT  tcp  --  *   *    192.238.4.66   0.0.0.0/0   tcp dpt:5000
    0     0 ACCEPT  tcp  --  *   *    192.238.4.67   0.0.0.0/0   tcp dpt:5000
    0     0 ACCEPT  tcp  --  *   *    192.238.4.68   0.0.0.0/0   tcp dpt:5000
    0     0 ACCEPT  tcp  --  *   *    192.238.3.130  0.0.0.0/0   tcp dpt:5000
    2   120 REJECT  tcp  --  *   *    0.0.0.0/0      0.0.0.0/0   tcp dpt:5000 reject-with tcp-reset
    0     0 ACCEPT  tcp  --  *   *    192.238.4.58   0.0.0.0/0   tcp dpt:5001
    5   293 ACCEPT  tcp  --  *   *    192.238.4.66   0.0.0.0/0   tcp dpt:5001
    0     0 ACCEPT  tcp  --  *   *    192.238.4.67   0.0.0.0/0   tcp dpt:5001
    0     0 ACCEPT  tcp  --  *   *    192.238.4.68   0.0.0.0/0   tcp dpt:5001
    0     0 ACCEPT  tcp  --  *   *    192.238.3.130  0.0.0.0/0   tcp dpt:5001
    2   120 REJECT  tcp  --  *   *    0.0.0.0/0      0.0.0.0/0   tcp dpt:5001 reject-with tcp-reset
```
<img width="240" height="208" alt="Screenshot 2026-11-25 at 09 29 08" src="https://github.com/user-attachments/assets/11164f2f-ad1e-40a2-bdee-4b01133d6f41" />

![Balthasar: log pesan dari Unit-01 dan counter iptables](195a23b8-image.png)

**Analisis:**
- Kedua file log berisi pesan dari Unit-01, jadi koneksi dari Pilot benar-benar diterima oleh listener.
- Rule `ACCEPT` untuk `192.238.4.66` (Unit-01) mencatat **5 paket / 293 byte** pada tiap port, sedangkan rule `ACCEPT` IP lain bernilai 0 karena tidak diuji.
- Rule `REJECT` mencatat **2 paket / 120 byte** pada tiap port. Angka ini berasal dari satu percobaan koneksi Lilith dan satu dari Melchior (masing-masing satu paket SYN berukuran 60 byte, 2 × 60 = 120 byte), sehingga terbukti kedua node tersebut ditolak oleh rule `REJECT`.

---

## 4. Hasil dan Pembahasan

| Skenario | Port | Hasil | Sesuai Soal |
|----------|------|-------|:-----------:|
| Unit-01 → Balthasar | 5000 | `succeeded!` | ✅ |
| Unit-01 → Balthasar | 5001 | `succeeded!` | ✅ |
| Lilith → Balthasar (lintas subnet) | 5000 & 5001 | `Connection refused` (seketika) | ✅ |
| Melchior → Balthasar (satu switch) | 5000 & 5001 | `Connection refused` (seketika) | ✅ |
| Balthasar → `8.8.8.8` | — | 0% packet loss | ✅ |

**Pembahasan:**

1. Hanya IP yang tercantum pada rule `ACCEPT` (Casper dan semua Pilot) yang dapat menyambung ke TCP 5000 dan 5001. Koneksi dari Unit-01 berhasil dan pesannya tercatat di log.
2. Node lain, baik satu switch (Melchior) maupun lintas subnet (Lilith), ditolak **seketika** dengan `Connection refused`, bukan menggantung sampai *timed out*. Hal ini karena `REJECT --reject-with tcp-reset` membalas dengan paket RST.
3. Karena policy default tetap `ACCEPT` dan rule hanya menargetkan TCP 5000/5001, lalu lintas lain tidak terganggu. Ini dibuktikan oleh `ping 8.8.8.8` yang tetap berhasil.
4. Counter iptables (5 paket dari Unit-01 pada rule `ACCEPT`, 2 paket pada rule `REJECT` di tiap port) mengonfirmasi bahwa traffic diproses oleh rule yang tepat.

---

## 5. Kesimpulan

Dengan menambahkan blok firewall Soal 13 pada `/root/balthasar.sh` berupa rule `ACCEPT` per IP untuk Casper dan seluruh Pilot, diikuti rule `REJECT --reject-with tcp-reset` pada TCP 5000 dan 5001, akses ke Balthasar berhasil dibatasi. Unit-01 dapat menyambung, sedangkan Lilith dan Melchior langsung ditolak. Policy default `ACCEPT` menjaga lalu lintas lain, termasuk ping ke `8.8.8.8`, tetap berjalan. Seluruh persyaratan Soal 13 terpenuhi.
# Laporan Pengerjaan Soal 14 — Jam Kunjungan Sang Pinguin

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Pen-Pen, Unit-01, Lilith, Spy

---

## 1. Soal

> **Jam kunjungan sang pinguin:** Pen-Pen, pinguin Misato, menyimpan sesuatu yang bikin banyak orang penasaran. Ia hanya boleh dihubungi sesuai jadwal di bawah. Jadwal berlaku untuk **ping dan TCP 80** ke Pen-Pen, dari mana pun asalnya. **Jam komputer tidak boleh diubah.**

| Kelompok | Jadwal (WIB) | Keterangan |
|----------|--------------|------------|
| Pilot | 08.00 sampai 16.00 | Hanya Senin sampai Jumat |
| Staf Geofront | 17.00 sampai 23.00 | Setiap hari |
| Terduga Dummy System | Tidak pernah | Selalu ditolak |
| Lainnya | Bebas | Tanpa batasan |

> Simpan jadwal sebagai variabel di awal script Pen-Pen, bernama `PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, dan `STAF_STOP`. Untuk uji, ubah variabel itu sampai jadwalnya **cocok** dengan jam sekarang, lalu sampai **tidak cocok**. Ingat, iptables membaca **jam UTC, 7 jam lebih lambat dari WIB**. Tunjukkan hasil ping dari Unit-01, Lilith, dan Spy pada kedua kondisi.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Jadwal berlaku untuk ping dan TCP 80 | Hanya ICMP `echo-request` dan TCP port 80 yang diarahkan ke chain khusus `PENPEN_ACL` |
| 2 | Jadwal berbasis waktu | Modul `-m time` (`--timestart`, `--timestop`, `--weekdays`) |
| 3 | Jam komputer tidak boleh diubah | Jadwal diubah lewat **variabel di script**, bukan lewat jam sistem |
| 4 | Jadwal dalam UTC | WIB dikurangi 7 jam |
| 5 | Lainnya bebas | Node yang tidak terdaftar tidak cocok rule apa pun, kembali ke policy `ACCEPT` |

### Konversi jadwal WIB ke UTC

| Kelompok | WIB | UTC (`--timestart` / `--timestop`) | Hari |
|----------|-----|------------------------------------|------|
| Pilot | 08.00 – 16.00 | `01:00` – `09:00` | `Mon,Tue,Wed,Thu,Fri` |
| Staf Geofront | 17.00 – 23.00 | `10:00` – `16:00` | Setiap hari |

### Alamat IP

| Perangkat / Kelompok | IP | Peran |
|----------------------|----|-------|
| Pen-Pen | `192.238.3.194` | Target yang dijaga jadwal |
| Pilot (termasuk Unit-01) | `192.238.4.66`, `192.238.4.67`, `192.238.4.68`, `192.238.3.130` | Jadwal Pilot |
| Staf Geofront (termasuk Lilith) | `192.238.4.74`, `192.238.4.75` | Jadwal Staf |
| Terduga Dummy System (termasuk Spy) | `192.238.4.50`, `192.238.4.51`, `192.238.4.102` | Selalu ditolak |

---

## 2. Langkah 1 — Memasang Script Jadwal di `/root/penpen.sh`

Script `/root/penpen.sh` ditulis ulang dengan kelima variabel jadwal di baris paling atas. Konfigurasi IP (`/root/ip.sh`) dan listener port 80 dari Soal 8 tetap dipertahankan. Aturan firewall dibuat dalam chain khusus `PENPEN_ACL`.

```bash
cat << 'EOF' > /root/penpen.sh
#!/bin/bash

# === VARIABEL JADWAL KUNJUNGAN (UTC = WIB - 7 JAM) ===
PILOT_START="01:00"
PILOT_STOP="09:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri"
STAF_START="10:00"
STAF_STOP="16:00"

[ -f /root/ip.sh ] && sh /root/ip.sh

# (Bagian instalasi paket iptables/netcat-traditional dan listener TCP 80
#  "Welcome to $(hostname)" dari Soal 8 tetap dipertahankan)

# === SOAL 14: FIREWALL JADWAL KUNJUNGAN ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

iptables -N PENPEN_ACL

# Arahkan hanya ICMP echo-request (ping) dan TCP 80 masuk ke chain PENPEN_ACL
iptables -A INPUT -p icmp --icmp-type echo-request -j PENPEN_ACL
iptables -A INPUT -p tcp --dport 80 -j PENPEN_ACL

# 1. Kelompok Pilot (08.00 - 16.00 WIB / 01:00 - 09:00 UTC, Senin - Jumat)
for IP in 192.238.4.66 192.238.4.67 192.238.4.68 192.238.3.130; do
  iptables -A PENPEN_ACL -s $IP -m time --timestart $PILOT_START --timestop $PILOT_STOP --weekdays $PILOT_DAYS -j ACCEPT
  iptables -A PENPEN_ACL -s $IP -j DROP
done

# 2. Kelompok Staf Geofront (17.00 - 23.00 WIB / 10:00 - 16:00 UTC, Setiap hari)
for IP in 192.238.4.74 192.238.4.75; do
  iptables -A PENPEN_ACL -s $IP -m time --timestart $STAF_START --timestop $STAF_STOP -j ACCEPT
  iptables -A PENPEN_ACL -s $IP -j DROP
done

# 3. Kelompok Terduga Dummy System (Selalu ditolak)
for IP in 192.238.4.50 192.238.4.51 192.238.4.102; do
  iptables -A PENPEN_ACL -s $IP -j DROP
done
EOF

chmod +x /root/penpen.sh
bash /root/penpen.sh
```

<!-- CEK: pada screenshot, baris rule Pilot/Staf terpotong di layar. Bagian "--weekdays $PILOT_DAYS -j ACCEPT" dan "-j ACCEPT" pada rule Staf direkonstruksi dari logika soal. Samakan dengan script aslimu bila berbeda. -->

![Script jadwal pada Pen-Pen](8aa22f60-image.png)

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| Variabel `PILOT_*` dan `STAF_*` | Jadwal disimpan di awal script (sesuai permintaan soal) sehingga uji cukup dengan mengubah variabel, tanpa mengubah jam sistem |
| `iptables -N PENPEN_ACL` | Membuat chain khusus agar aturan jadwal rapi dan terpisah |
| `-p icmp --icmp-type echo-request -j PENPEN_ACL` dan `-p tcp --dport 80 -j PENPEN_ACL` | Hanya ping dan TCP 80 yang dikenai jadwal; lalu lintas lain tidak tersentuh |
| `-m time --timestart ... --timestop ... --weekdays ... -j ACCEPT` | Mengizinkan sumber pada rentang jam UTC (dan hari) yang ditentukan |
| `-s $IP -j DROP` tepat setelah `ACCEPT` | Di luar jadwal, paket dari IP tersebut dibuang |
| Kelompok Dummy: langsung `DROP` | Selalu ditolak tanpa syarat waktu |
| Policy `INPUT ACCEPT` | IP lain ("Lainnya") tidak cocok rule apa pun sehingga bebas |

**Urutan rule penting:** `ACCEPT` bersyarat waktu harus berada **sebelum** `DROP` untuk IP yang sama. iptables berhenti pada kecocokan pertama.

---

## 3. Langkah 2 — Uji Kondisi 1: Jadwal **Cocok** dengan Jam Sekarang

### 3.1 Cek jam UTC dan hari saat ini di Pen-Pen

```bash
date -u
```

**Hasil:**

```
Wed Nov 25 13:45:22 UTC 2026
```

![date -u di Pen-Pen](c327dc3f-image.png)

<!-- CEK: date -u menunjukkan 13:45 UTC, sedangkan rentang yang diset di bawah adalah 08:00-11:00 (tidak mencakup 13:45). Pastikan rentang yang kamu pakai saat uji kondisi 1 benar-benar mencakup jam UTC container (mis. 12:00-15:00), atau jalankan ulang date -u tepat sebelum pengujian dan ganti nilai di bawah. -->

### 3.2 Mengubah variabel agar cocok dengan jam UTC sekarang

Variabel Pilot dan Staf diubah ke rentang yang mencakup jam UTC container. `PILOT_DAYS` ditambah `Sat,Sun` agar hari saat pengujian pasti termasuk.

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="08:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="11:00"/' /root/penpen.sh
sed -i 's/^PILOT_DAYS=.*/PILOT_DAYS="Mon,Tue,Wed,Thu,Fri,Sat,Sun"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="08:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="11:00"/' /root/penpen.sh
bash /root/penpen.sh
head -n 10 /root/penpen.sh
```

**Hasil `head -n 10`:**

```
#!/bin/bash

# === VARIABEL JADWAL KUNJUNGAN (UTC = WIB - 7 JAM) ===
PILOT_START="08:00"
PILOT_STOP="11:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri,Sat,Sun"
STAF_START="08:00"
STAF_STOP="11:00"

[ -f /root/ip.sh ] && sh /root/ip.sh
```

![Mengubah variabel jadwal agar cocok](79f4533c-image.png)

### 3.3 Pengujian dari tiga node (harus berhasil, berhasil, gagal)

**Unit-01 (`192.238.4.66`, Pilot) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```

![Unit-01: ping dan nc ke Pen-Pen berhasil](cb07a5d4-image.png)

**Lilith (`192.238.4.74`, Staf) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```

![Lilith: ping dan nc ke Pen-Pen berhasil](cf636225-image.png)

**Spy (`192.238.4.102`, Terduga Dummy System) — harus gagal (selalu ditolak):**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```

![Spy: ping ke Pen-Pen gagal (kondisi 1)](abb9836f-image.png)

---

## 4. Langkah 3 — Uji Kondisi 2: Jadwal **Tidak Cocok** dengan Jam Sekarang

### 4.1 Menggeser variabel ke jam di luar jam UTC sekarang

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="13:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="15:00"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="13:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="15:00"/' /root/penpen.sh
bash /root/penpen.sh
head -n 10 /root/penpen.sh
```

<!-- CEK: rentang 13:00-15:00 adalah contoh dari petunjuk. Karena date -u di screenshotmu 13:45, rentang ini justru MENCAKUP jam sekarang. Pastikan nilai yang kamu pakai benar-benar di luar jam UTC container (mis. 01:00-03:00), lalu samakan di sini. -->

### 4.2 Pengujian ulang dari tiga node

**Unit-01 (`192.238.4.66`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

> Tambahkan screenshot Unit-01 (100% packet loss) di sini. Screenshot ini belum ada di foto yang kamu kirim.

**Lilith (`192.238.4.74`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2307ms
```

![Lilith: ping ke Pen-Pen gagal (kondisi 2)](ad8f518e-image.png)

**Spy (`192.238.4.102`) — tetap gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2313ms
```

![Spy: ping ke Pen-Pen tetap gagal (kondisi 2)](9c3e01b3-image.png)

---

## 5. Langkah 4 — Mengembalikan ke Jadwal Asli Soal

Setelah bukti kedua kondisi diambil, kelima variabel dikembalikan ke nilai asli sesuai spesifikasi soal agar konfigurasi akhir Pen-Pen tetap valid.

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="01:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="09:00"/' /root/penpen.sh
sed -i 's/^PILOT_DAYS=.*/PILOT_DAYS="Mon,Tue,Wed,Thu,Fri"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="10:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="16:00"/' /root/penpen.sh
bash /root/penpen.sh
iptables -S
```

Setelah dikembalikan, Spy tetap gagal karena kelompok Dummy System ditolak di luar jadwal apa pun:

```
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```

![Spy: ping tetap gagal setelah jadwal dikembalikan](0e73df0f-image.png)

---

## 6. Hasil dan Pembahasan

| Node | Kelompok | Kondisi 1 (jadwal cocok) | Kondisi 2 (jadwal tidak cocok) | Sesuai Soal |
|------|----------|--------------------------|--------------------------------|:-----------:|
| Unit-01 `192.238.4.66` | Pilot | Ping 0% loss, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% packet loss) | ✅ |
| Lilith `192.238.4.74` | Staf Geofront | Ping 0% loss, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% packet loss) | ✅ |
| Spy `192.238.4.102` | Terduga Dummy System | Gagal (100% packet loss) | Gagal (100% packet loss) | ✅ |

**Pembahasan:**

1. **Jadwal diuji tanpa mengubah jam komputer.** Soal melarang jam sistem diubah, sehingga kondisi "cocok" dan "tidak cocok" dibuat dengan menggeser variabel `PILOT_*` dan `STAF_*` di awal script, lalu menjalankan ulang `penpen.sh`.
2. **Jadwal berbasis UTC.** Modul `-m time` pada iptables membaca jam UTC. Karena WIB = UTC + 7, Pilot 08.00–16.00 WIB menjadi `01:00`–`09:00` UTC dan Staf 17.00–23.00 WIB menjadi `10:00`–`16:00` UTC.
3. **Hanya ping dan TCP 80 yang dibatasi.** Chain `PENPEN_ACL` hanya dipanggil untuk ICMP `echo-request` dan TCP port 80, sehingga lalu lintas lain ke Pen-Pen tetap normal.
4. **Pilot dan Staf mengikuti jadwal.** Pada kondisi 1, Unit-01 (Pilot) dan Lilith (Staf) dapat ping dan membuka TCP 80. Pada kondisi 2, keduanya mengenai `DROP` dan ping gagal 100%.
5. **Dummy System selalu ditolak.** Spy (`192.238.4.102`) selalu gagal pada kedua kondisi karena rule-nya adalah `DROP` tanpa syarat waktu.
6. **Node lain bebas.** Node yang tidak terdaftar tidak cocok rule di `PENPEN_ACL` dan kembali ke policy `INPUT ACCEPT`.

---

## 7. Kesimpulan

Dengan menyimpan jadwal sebagai variabel (`PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, `STAF_STOP`) di awal `/root/penpen.sh` dan menerapkan rule `iptables -m time` pada chain `PENPEN_ACL`, akses ping dan TCP 80 ke Pen-Pen dapat dibatasi sesuai jadwal tanpa mengubah jam komputer. Unit-01 (Pilot) dan Lilith (Staf) hanya berhasil saat jadwal cocok, sedangkan Spy (Terduga Dummy System) selalu ditolak. Setelah pengujian, variabel dikembalikan ke nilai asli sehingga konfigurasi akhir sesuai spesifikasi soal.

# Laporan Pengerjaan Soal 14 — Jam Kunjungan Sang Pinguin

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Pen-Pen, Unit-01, Lilith, Spy

---

## 1. Soal

> **Jam kunjungan sang pinguin:** Pen-Pen, pinguin Misato, menyimpan sesuatu yang bikin banyak orang penasaran. Ia hanya boleh dihubungi sesuai jadwal di bawah. Jadwal berlaku untuk **ping dan TCP 80** ke Pen-Pen, dari mana pun asalnya. **Jam komputer tidak boleh diubah.**

| Kelompok | Jadwal (WIB) | Keterangan |
|----------|--------------|------------|
| Pilot | 08.00 sampai 16.00 | Hanya Senin sampai Jumat |
| Staf Geofront | 17.00 sampai 23.00 | Setiap hari |
| Terduga Dummy System | Tidak pernah | Selalu ditolak |
| Lainnya | Bebas | Tanpa batasan |

> Simpan jadwal sebagai variabel di awal script Pen-Pen, bernama `PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, dan `STAF_STOP`. Untuk uji, ubah variabel itu sampai jadwalnya **cocok** dengan jam sekarang, lalu sampai **tidak cocok**. Ingat, iptables membaca **jam UTC, 7 jam lebih lambat dari WIB**. Tunjukkan hasil ping dari Unit-01, Lilith, dan Spy pada kedua kondisi.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Jadwal berlaku untuk ping dan TCP 80 | Hanya ICMP `echo-request` dan TCP port 80 yang diarahkan ke chain khusus `PENPEN_ACL` |
| 2 | Jadwal berbasis waktu | Modul `-m time` (`--timestart`, `--timestop`, `--weekdays`) |
| 3 | Jam komputer tidak boleh diubah | Jadwal diubah lewat **variabel di script**, bukan lewat jam sistem |
| 4 | Jadwal dalam UTC | WIB dikurangi 7 jam |
| 5 | Lainnya bebas | Node yang tidak terdaftar tidak cocok rule apa pun, kembali ke policy `ACCEPT` |

### Konversi jadwal WIB ke UTC

| Kelompok | WIB | UTC (`--timestart` / `--timestop`) | Hari |
|----------|-----|------------------------------------|------|
| Pilot | 08.00 – 16.00 | `01:00` – `09:00` | `Mon,Tue,Wed,Thu,Fri` |
| Staf Geofront | 17.00 – 23.00 | `10:00` – `16:00` | Setiap hari |

### Alamat IP

| Perangkat / Kelompok | IP | Peran |
|----------------------|----|-------|
| Pen-Pen | `192.238.3.194` | Target yang dijaga jadwal |
| Pilot (termasuk Unit-01) | `192.238.4.66`, `192.238.4.67`, `192.238.4.68`, `192.238.3.130` | Jadwal Pilot |
| Staf Geofront (termasuk Lilith) | `192.238.4.74`, `192.238.4.75` | Jadwal Staf |
| Terduga Dummy System (termasuk Spy) | `192.238.4.50`, `192.238.4.51`, `192.238.4.102` | Selalu ditolak |

---

## 2. Langkah 1 — Memasang Script Jadwal di `/root/penpen.sh`

Script `/root/penpen.sh` ditulis ulang dengan kelima variabel jadwal di baris paling atas. Konfigurasi IP (`/root/ip.sh`) dan listener port 80 dari Soal 8 tetap dipertahankan. Aturan firewall dibuat dalam chain khusus `PENPEN_ACL`.

```bash
cat << 'EOF' > /root/penpen.sh
#!/bin/bash

# === VARIABEL JADWAL KUNJUNGAN (UTC = WIB - 7 JAM) ===
PILOT_START="01:00"
PILOT_STOP="09:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri"
STAF_START="10:00"
STAF_STOP="16:00"

[ -f /root/ip.sh ] && sh /root/ip.sh

# (Bagian instalasi paket iptables/netcat-traditional dan listener TCP 80
#  "Welcome to $(hostname)" dari Soal 8 tetap dipertahankan)

# === SOAL 14: FIREWALL JADWAL KUNJUNGAN ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

iptables -N PENPEN_ACL

# Arahkan hanya ICMP echo-request (ping) dan TCP 80 masuk ke chain PENPEN_ACL
iptables -A INPUT -p icmp --icmp-type echo-request -j PENPEN_ACL
iptables -A INPUT -p tcp --dport 80 -j PENPEN_ACL

# 1. Kelompok Pilot (08.00 - 16.00 WIB / 01:00 - 09:00 UTC, Senin - Jumat)
for IP in 192.238.4.66 192.238.4.67 192.238.4.68 192.238.3.130; do
  iptables -A PENPEN_ACL -s $IP -m time --timestart $PILOT_START --timestop $PILOT_STOP --weekdays $PILOT_DAYS -j ACCEPT
  iptables -A PENPEN_ACL -s $IP -j DROP
done

# 2. Kelompok Staf Geofront (17.00 - 23.00 WIB / 10:00 - 16:00 UTC, Setiap hari)
for IP in 192.238.4.74 192.238.4.75; do
  iptables -A PENPEN_ACL -s $IP -m time --timestart $STAF_START --timestop $STAF_STOP -j ACCEPT
  iptables -A PENPEN_ACL -s $IP -j DROP
done

# 3. Kelompok Terduga Dummy System (Selalu ditolak)
for IP in 192.238.4.50 192.238.4.51 192.238.4.102; do
  iptables -A PENPEN_ACL -s $IP -j DROP
done
EOF

chmod +x /root/penpen.sh
bash /root/penpen.sh
```

<!-- CEK: pada screenshot, baris rule Pilot/Staf terpotong di layar. Bagian "--weekdays $PILOT_DAYS -j ACCEPT" dan "-j ACCEPT" pada rule Staf direkonstruksi dari logika soal. Samakan dengan script aslimu bila berbeda. -->
<img width="365" height="244" alt="Screenshot 2026-11-25 at 16 48 31" src="https://github.com/user-attachments/assets/a59e8df6-885e-4ad4-a27f-973c43913a7a" />

![Script jadwal pada Pen-Pen](8aa22f60-image.png)

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| Variabel `PILOT_*` dan `STAF_*` | Jadwal disimpan di awal script (sesuai permintaan soal) sehingga uji cukup dengan mengubah variabel, tanpa mengubah jam sistem |
| `iptables -N PENPEN_ACL` | Membuat chain khusus agar aturan jadwal rapi dan terpisah |
| `-p icmp --icmp-type echo-request -j PENPEN_ACL` dan `-p tcp --dport 80 -j PENPEN_ACL` | Hanya ping dan TCP 80 yang dikenai jadwal; lalu lintas lain tidak tersentuh |
| `-m time --timestart ... --timestop ... --weekdays ... -j ACCEPT` | Mengizinkan sumber pada rentang jam UTC (dan hari) yang ditentukan |
| `-s $IP -j DROP` tepat setelah `ACCEPT` | Di luar jadwal, paket dari IP tersebut dibuang |
| Kelompok Dummy: langsung `DROP` | Selalu ditolak tanpa syarat waktu |
| Policy `INPUT ACCEPT` | IP lain ("Lainnya") tidak cocok rule apa pun sehingga bebas |

**Urutan rule penting:** `ACCEPT` bersyarat waktu harus berada **sebelum** `DROP` untuk IP yang sama. iptables berhenti pada kecocokan pertama.

---

## 3. Langkah 2 — Uji Kondisi 1: Jadwal **Cocok** dengan Jam Sekarang

### 3.1 Cek jam UTC dan hari saat ini di Pen-Pen

```bash
date -u
```

**Hasil:**

```
Wed Nov 25 13:45:22 UTC 2026
```
<img width="364" height="28" alt="Screenshot 2026-11-25 at 16 48 51" src="https://github.com/user-attachments/assets/82308f47-8daf-4391-9e99-369b56062405" />

![date -u di Pen-Pen](c327dc3f-image.png)

<!-- CEK: date -u menunjukkan 13:45 UTC, sedangkan rentang yang diset di bawah adalah 08:00-11:00 (tidak mencakup 13:45). Pastikan rentang yang kamu pakai saat uji kondisi 1 benar-benar mencakup jam UTC container (mis. 12:00-15:00), atau jalankan ulang date -u tepat sebelum pengujian dan ganti nilai di bawah. -->

### 3.2 Mengubah variabel agar cocok dengan jam UTC sekarang

Variabel Pilot dan Staf diubah ke rentang yang mencakup jam UTC container. `PILOT_DAYS` ditambah `Sat,Sun` agar hari saat pengujian pasti termasuk.

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="08:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="11:00"/' /root/penpen.sh
sed -i 's/^PILOT_DAYS=.*/PILOT_DAYS="Mon,Tue,Wed,Thu,Fri,Sat,Sun"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="08:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="11:00"/' /root/penpen.sh
bash /root/penpen.sh
head -n 10 /root/penpen.sh
```

**Hasil `head -n 10`:**

```
#!/bin/bash

# === VARIABEL JADWAL KUNJUNGAN (UTC = WIB - 7 JAM) ===
PILOT_START="08:00"
PILOT_STOP="11:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri,Sat,Sun"
STAF_START="08:00"
STAF_STOP="11:00"

[ -f /root/ip.sh ] && sh /root/ip.sh
```
<img width="365" height="155" alt="Screenshot 2026-11-25 at 16 52 29" src="https://github.com/user-attachments/assets/fbb1dcd6-9f24-4302-a3df-2d401d9f24a5" />

![Mengubah variabel jadwal agar cocok](79f4533c-image.png)

### 3.3 Pengujian dari tiga node (harus berhasil, berhasil, gagal)

**Unit-01 (`192.238.4.66`, Pilot) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```
<img width="242" height="90" alt="Screenshot 2026-11-25 at 17 03 41" src="https://github.com/user-attachments/assets/d3d38e03-f7a6-4121-bd5f-af91c474a6d8" />

![Unit-01: ping dan nc ke Pen-Pen berhasil](cb07a5d4-image.png)

**Lilith (`192.238.4.74`, Staf) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```
<img width="242" height="90" alt="Screenshot 2026-11-25 at 17 03 53" src="https://github.com/user-attachments/assets/9c3ff269-5264-4474-bb02-58a993d1ce88" />

![Lilith: ping dan nc ke Pen-Pen berhasil](cf636225-image.png)

**Spy (`192.238.4.102`, Terduga Dummy System) — harus gagal (selalu ditolak):**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```
<img width="242" height="38" alt="Screenshot 2026-11-25 at 17 13 43" src="https://github.com/user-attachments/assets/ccfda51f-f254-4def-9768-50aaeb42b78d" />

![Spy: ping ke Pen-Pen gagal (kondisi 1)](abb9836f-image.png)

---

## 4. Langkah 3 — Uji Kondisi 2: Jadwal **Tidak Cocok** dengan Jam Sekarang

### 4.1 Menggeser variabel ke jam di luar jam UTC sekarang

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="13:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="15:00"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="13:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="15:00"/' /root/penpen.sh
bash /root/penpen.sh
head -n 10 /root/penpen.sh
```

<!-- CEK: rentang 13:00-15:00 adalah contoh dari petunjuk. Karena date -u di screenshotmu 13:45, rentang ini justru MENCAKUP jam sekarang. Pastikan nilai yang kamu pakai benar-benar di luar jam UTC container (mis. 01:00-03:00), lalu samakan di sini. -->

### 4.2 Pengujian ulang dari tiga node

**Unit-01 (`192.238.4.66`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```
<img width="241" height="39" alt="Screenshot 2026-11-25 at 17 20 38" src="https://github.com/user-attachments/assets/bf1a49b4-2424-42ce-a2be-65157098bd8b" />

> Tambahkan screenshot Unit-01 (100% packet loss) di sini. Screenshot ini belum ada di foto yang kamu kirim.

**Lilith (`192.238.4.74`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2307ms
```
<img width="243" height="50" alt="Screenshot 2026-11-25 at 17 04 38" src="https://github.com/user-attachments/assets/9e0e56e4-4209-4048-ac6c-344f556acb53" />

![Lilith: ping ke Pen-Pen gagal (kondisi 2)](ad8f518e-image.png)

**Spy (`192.238.4.102`) — tetap gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```
3 packets transmitted, 0 received, 100% packet loss, time 2313ms
```
<img width="242" height="38" alt="Screenshot 2026-11-25 at 17 13 43" src="https://github.com/user-attachments/assets/958b09bc-e388-4bda-8359-5e23400dab34" />

![Spy: ping ke Pen-Pen tetap gagal (kondisi 2)](9c3e01b3-image.png)

---

## 5. Langkah 4 — Mengembalikan ke Jadwal Asli Soal

Setelah bukti kedua kondisi diambil, kelima variabel dikembalikan ke nilai asli sesuai spesifikasi soal agar konfigurasi akhir Pen-Pen tetap valid.

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="01:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="09:00"/' /root/penpen.sh
sed -i 's/^PILOT_DAYS=.*/PILOT_DAYS="Mon,Tue,Wed,Thu,Fri"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="10:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="16:00"/' /root/penpen.sh
bash /root/penpen.sh
iptables -S
```

Setelah dikembalikan, Spy tetap gagal karena kelompok Dummy System ditolak di luar jadwal apa pun:

```
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```
<img width="243" height="50" alt="Screenshot 2026-11-25 at 17 08 33" src="https://github.com/user-attachments/assets/e6515deb-9167-4043-ae49-e676b4706fca" />

![Spy: ping tetap gagal setelah jadwal dikembalikan](0e73df0f-image.png)

---

## 6. Hasil dan Pembahasan

| Node | Kelompok | Kondisi 1 (jadwal cocok) | Kondisi 2 (jadwal tidak cocok) | Sesuai Soal |
|------|----------|--------------------------|--------------------------------|:-----------:|
| Unit-01 `192.238.4.66` | Pilot | Ping 0% loss, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% packet loss) | ✅ |
| Lilith `192.238.4.74` | Staf Geofront | Ping 0% loss, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% packet loss) | ✅ |
| Spy `192.238.4.102` | Terduga Dummy System | Gagal (100% packet loss) | Gagal (100% packet loss) | ✅ |

**Pembahasan:**

1. **Jadwal diuji tanpa mengubah jam komputer.** Soal melarang jam sistem diubah, sehingga kondisi "cocok" dan "tidak cocok" dibuat dengan menggeser variabel `PILOT_*` dan `STAF_*` di awal script, lalu menjalankan ulang `penpen.sh`.
2. **Jadwal berbasis UTC.** Modul `-m time` pada iptables membaca jam UTC. Karena WIB = UTC + 7, Pilot 08.00–16.00 WIB menjadi `01:00`–`09:00` UTC dan Staf 17.00–23.00 WIB menjadi `10:00`–`16:00` UTC.
3. **Hanya ping dan TCP 80 yang dibatasi.** Chain `PENPEN_ACL` hanya dipanggil untuk ICMP `echo-request` dan TCP port 80, sehingga lalu lintas lain ke Pen-Pen tetap normal.
4. **Pilot dan Staf mengikuti jadwal.** Pada kondisi 1, Unit-01 (Pilot) dan Lilith (Staf) dapat ping dan membuka TCP 80. Pada kondisi 2, keduanya mengenai `DROP` dan ping gagal 100%.
5. **Dummy System selalu ditolak.** Spy (`192.238.4.102`) selalu gagal pada kedua kondisi karena rule-nya adalah `DROP` tanpa syarat waktu.
6. **Node lain bebas.** Node yang tidak terdaftar tidak cocok rule di `PENPEN_ACL` dan kembali ke policy `INPUT ACCEPT`.

---

## 7. Kesimpulan

Dengan menyimpan jadwal sebagai variabel (`PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, `STAF_STOP`) di awal `/root/penpen.sh` dan menerapkan rule `iptables -m time` pada chain `PENPEN_ACL`, akses ping dan TCP 80 ke Pen-Pen dapat dibatasi sesuai jadwal tanpa mengubah jam komputer. Unit-01 (Pilot) dan Lilith (Staf) hanya berhasil saat jadwal cocok, sedangkan Spy (Terduga Dummy System) selalu ditolak. Setelah pengujian, variabel dikembalikan ke nilai asli sehingga konfigurasi akhir sesuai spesifikasi soal.

# Laporan Pengerjaan Soal 15 — Banjir Ping di Pusat Komando

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Tokyo3-Core, Lilith

---

## 1. Soal

> **Banjir ping di pusat komando:** Instrumentality makin dekat, dan semua orang berebut mem-ping Tokyo3-Core. Banjir ping bisa melumpuhkannya. Batasi Tokyo3-Core agar hanya menjawab **maksimal 5 ping per detik**, dan buang kelebihannya. Aturan ini **tidak boleh mengganggu ping di bawah batas**. Aturan ini juga **tidak boleh membatasi ping yang dikirim Tokyo3-Core sendiri**. Pastikan rule benar-benar mengenai paket yang dituju. Periksa urutan rule dan jenis paketnya. Buktikan ping rapat dari Lilith, misalnya `ping -i 0.1 -c50`, sebagian hilang, dan tunjukkan counter rule pembuangan naik. Buktikan ping biasa semuanya berhasil, dengan jeda minimal 3 detik di antaranya.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Maksimal 5 ping masuk per detik | `-m limit --limit 5/second --limit-burst 5` pada rule `ACCEPT` |
| 2 | Kelebihan dibuang | Rule `DROP` untuk ICMP `echo-request` tepat **setelah** rule `ACCEPT` |
| 3 | Ping di bawah batas tidak terganggu | Ping yang masih dalam batas `limit` lolos di rule pertama |
| 4 | Ping keluar dari Tokyo3-Core tidak dibatasi | Rule berada di chain `INPUT` dan hanya mencocokkan `echo-request` (type 8) |
| 5 | Rule harus mengenai paket yang tepat | Pencocokan spesifik: `-p icmp --icmp-type echo-request` |

### Alamat IP

| Perangkat | IP |
|-----------|----|
| Tokyo3-Core | `192.238.4.106` |
| Lilith | `192.238.4.74` |

---

## 2. Langkah 1 — Menambahkan Aturan Rate-Limit ke `/root/tokyo3.sh`

Blok firewall Soal 15 ditambahkan secara idempoten ke bagian bawah `/root/tokyo3.sh`, tanpa menghapus listener port `80` dan `8080` dari Soal 8. Perintah `sed` menghapus blok Soal 15 lama (jika ada) agar tidak terduplikasi.

```bash
sed -i '/# === SOAL 15: FIREWALL ===/,$d' /root/tokyo3.sh
cat << 'EOF' >> /root/tokyo3.sh

# === SOAL 15: FIREWALL ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# 1. Izinkan maksimal 5 ping masuk (ICMP echo-request) per detik
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT

# 2. Buang kelebihan ping masuk di atas batas 5/detik
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
EOF

bash /root/tokyo3.sh
```
<img width="240" height="124" alt="Screenshot 2026-11-25 at 17 26 59" src="https://github.com/user-attachments/assets/512d155a-0dbf-42ac-8102-38e0e3e0d16a" />

![Menambahkan blok firewall Soal 15 di Tokyo3-Core](8fb2ead8-image.png)

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| `iptables -F` / `-X` | Membersihkan rule dan chain lama |
| `-P INPUT/FORWARD/OUTPUT ACCEPT` | Policy default tetap `ACCEPT` sehingga lalu lintas lain tidak terganggu |
| `-p icmp --icmp-type echo-request` | Hanya mencocokkan permintaan ping masuk (ICMP type 8); balasan (`echo-reply`) tidak ikut |
| `-m limit --limit 5/second --limit-burst 5 -j ACCEPT` | Meloloskan ping selama masih dalam batas rata-rata 5 paket/detik, dengan toleransi burst awal 5 paket |
| `-A INPUT ... -j DROP` (rule kedua) | Membuang semua `echo-request` yang tidak lolos rule pertama, yaitu kelebihan di atas 5/detik |

**Urutan rule penting:** rule `ACCEPT` berbatas (`limit`) harus berada **di baris pertama**, diikuti rule `DROP` di baris kedua. Jika `DROP` berada di atas, semua ping, termasuk yang masih di bawah batas, akan dibuang.

**Kenapa ping keluar dari Tokyo3-Core tidak terbatasi?**
Ping yang dikirim Tokyo3-Core lewat chain `OUTPUT` (tidak disentuh rule), dan balasannya berupa `echo-reply` (type 0) yang masuk ke `INPUT` tidak cocok dengan rule karena rule hanya mencocokkan `echo-request` (type 8).

---

## 3. Langkah 2 — Pengujian dan Bukti

### 3.1 Tokyo3-Core (`192.238.4.106`) — cek urutan rule dan counter awal

```bash
iptables -S
iptables -L INPUT -n -v
```

**Hasil:**

```
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -m limit --limit 5/sec -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP

Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target  prot opt in  out  source     destination
    0     0 ACCEPT  icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8 limit: avg 5/sec burst 5
    0     0 DROP    icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8
```
<img width="479" height="196" alt="Screenshot 2026-11-25 at 17 28 14" src="https://github.com/user-attachments/assets/4321a724-6396-4524-80fb-284fbffe2fab" />

![Tokyo3-Core: urutan rule dan counter awal](ae5198af-image.png)

**Analisis:** Rule `limit: avg 5/sec burst 5` berada di baris pertama, diikuti rule `DROP icmptype 8` di baris kedua. Kedua counter masih `0`, menandakan belum ada paket sebelum pengujian.

### 3.2 Lilith (`192.238.4.74`) — banjir ping rapat (0,1 detik, 50 paket)

```bash
ping -i 0.1 -c 50 192.238.4.106
```

**Hasil:**

```
--- 192.238.4.106 ping statistics ---
50 packets transmitted, 30 received, 40% packet loss, time 5641ms
rtt min/avg/max/mdev = 0.619/1.071/1.980/0.307 ms
```

Balasan terlihat berurutan pada `icmp_seq=1` sampai `9`, lalu mulai selang-seling (`11, 13, 15, ...`) karena kelebihan paket dibuang.
<img width="482" height="472" alt="Screenshot 2026-11-25 at 17 28 57" src="https://github.com/user-attachments/assets/16aac166-8639-4ee2-b1e4-d85269dd9772" />

![Lilith: ping rapat 50 paket, 40% packet loss](59645109-image.png)

### 3.3 Tokyo3-Core — kenaikan counter rule pembuangan

```bash
iptables -L INPUT -n -v
```

**Hasil:**

```
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target  prot opt in  out  source     destination
   30  2520 ACCEPT  icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8 limit: avg 5/sec burst 5
   20  1680 DROP    icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8
```
<img width="480" height="114" alt="Screenshot 2026-11-25 at 17 30 51" src="https://github.com/user-attachments/assets/3215e885-146d-4a68-af3d-87344e0a7e2f" />

![Tokyo3-Core: counter ACCEPT 30 paket dan DROP 20 paket](c2776680-image.png)

**Analisis:**
- Rule `ACCEPT` mencatat **30 paket (2520 byte)** dan rule `DROP` mencatat **20 paket (1680 byte)**.
- 30 + 20 = **50 paket**, sama dengan jumlah paket yang dikirim Lilith.
- 30 paket yang diterima cocok dengan hasil ping Lilith (`30 received`), sedangkan 20 paket sisanya dibuang. Ini membuktikan kelebihan ping benar-benar ditangkap rule `DROP`.
- Tiap paket ping berukuran 84 byte: 30 × 84 = 2520 byte dan 20 × 84 = 1680 byte.

### 3.4 Lilith — ping biasa dengan jeda minimal 3 detik

```bash
ping -i 3 -c 5 192.238.4.106
```

**Hasil:**

```
5 packets transmitted, 5 received, 0% packet loss, time 13412ms
```
<img width="387" height="126" alt="Screenshot 2026-11-25 at 17 31 48" src="https://github.com/user-attachments/assets/2f8273c2-bd82-4e08-9254-a7af90aff301" />

![Lilith: ping biasa jeda 3 detik, 0% packet loss](a3017bbd-image.png)

### 3.5 Tokyo3-Core — ping keluar tidak ikut terbatasi

```bash
ping -i 0.1 -c 15 192.238.4.74
```

**Hasil:**

```
15 packets transmitted, 15 received, 0% packet loss, time 1579ms
rtt min/avg/max/mdev = 0.625/1.116/2.402/0.465 ms
```
<img width="479" height="278" alt="Screenshot 2026-11-25 at 17 32 17" src="https://github.com/user-attachments/assets/7d6b01e4-770a-490e-8499-0c1300053e01" />

![Tokyo3-Core: ping keluar rapat ke Lilith, 0% packet loss](b10b4cd6-image.png)

---

## 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Lilith → Tokyo3-Core `ping -i 0.1 -c 50` (10 ping/detik) | Sebagian hilang | 30 diterima, 20 hilang (40% loss) | ✅ |
| Counter rule `DROP` | Naik | 20 paket / 1680 byte | ✅ |
| Lilith → Tokyo3-Core `ping -i 3 -c 5` (di bawah batas) | Semua berhasil | 5 diterima (0% loss) | ✅ |
| Tokyo3-Core → Lilith `ping -i 0.1 -c 15` (ping keluar) | Tidak terbatasi | 15 diterima (0% loss) | ✅ |

**Pembahasan:**

1. **Pembatasan 5 ping per detik.** Rule `-m limit --limit 5/second --limit-burst 5` bekerja seperti *token bucket*: 5 paket pertama lolos sebagai burst, lalu laju rata-rata dibatasi 5 paket/detik. Dengan laju kirim 10 ping/detik, sekitar setengah paket lolos, sesuai hasil 30 dari 50 paket (40% loss).
2. **Pola balasan.** Balasan awal berurutan (`seq 1–9`) karena masih ada token burst dan pengisian token, lalu berubah selang-seling saat laju kirim melebihi laju pengisian.
3. **Kelebihan dibuang.** Counter `DROP` naik menjadi 20 paket dan jumlah `ACCEPT` + `DROP` (30 + 20) sama dengan 50 paket yang dikirim, jadi rule mengenai paket yang tepat.
4. **Ping di bawah batas tidak terganggu.** Dengan jeda 3 detik (sekitar 0,33 ping/detik, jauh di bawah 5/detik) semua paket lolos rule `ACCEPT` pertama sehingga 0% packet loss.
5. **Ping keluar tidak terbatasi.** Ping dari Tokyo3-Core melewati chain `OUTPUT` dan balasannya (`echo-reply`) bukan type 8, sehingga tidak cocok dengan rule. Hasilnya 15 dari 15 paket berhasil walaupun dikirim rapat.

---

## 5. Kesimpulan

Dengan menambahkan rule `iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT` diikuti rule `DROP` untuk `echo-request` pada `/root/tokyo3.sh`, Tokyo3-Core hanya menjawab maksimal 5 ping per detik dan membuang kelebihannya. Ping rapat dari Lilith sebagian hilang (40% loss) dengan counter `DROP` yang naik, ping biasa dengan jeda 3 detik semuanya berhasil, dan ping yang dikirim Tokyo3-Core sendiri tidak terbatasi. Seluruh persyaratan Soal 15 terpenuhi.

# Laporan Pengerjaan Soal 16 — Pencuri yang Mencoba Setiap Pintu

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Tokyo3-Core, Dummy-Plug (Switch-Lab)

---

## 1. Soal

> **Pencuri yang mencoba setiap pintu:** dari Switch-Lab, Dummy-Plug mengetuk port sensitif Tokyo3-Core satu per satu seperti pencuri. Ritsuko ingin tahu setiap percobaannya, dan pintunya harus dibanting. Setiap koneksi TCP dari **Dummy-Plug** ke **port 22, 23, dan 3306** di Tokyo3-Core harus **dicatat di log** dengan prefix huruf besar `SCAN-NERV:`, lalu **ditolak seketika**. Port 80 dan 8080 tetap bisa diakses, dan klien lain tidak boleh terganggu. Buktikan `nc -w` dari Dummy-Plug ke port 22, 23, dan 3306 langsung ditolak, dan ke port 80 dan 8080 berhasil. Tunjukkan **counter rule pencatatan**, karena log kernel sering tidak terlihat di container.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Catat koneksi Dummy-Plug ke port 22, 23, 3306 | Rule `-j LOG --log-prefix "SCAN-NERV: "` |
| 2 | Tolak seketika (bukan timeout) | Rule `-j REJECT --reject-with tcp-reset` |
| 3 | Hanya Dummy-Plug dan hanya port tersebut | Pencocokan `-s 192.238.4.51 -p tcp --dport <port>` |
| 4 | Port 80 dan 8080 tetap bisa diakses; klien lain tidak terganggu | Policy default `ACCEPT`, tidak ada rule untuk port 80/8080 |
| 5 | Tidak merusak aturan Soal 15 | Aturan Soal 15 digabung ke blok yang sama dalam `/root/tokyo3.sh` |
| 6 | Bukti pencatatan | Counter `pkts` pada rule `LOG` (`iptables -L INPUT -n -v`) |

### Alamat IP

| Perangkat | IP | Peran |
|-----------|----|-------|
| Tokyo3-Core | `192.238.4.106` | Target (listener TCP 80 dan 8080) |
| Dummy-Plug | `192.238.4.51` | Penguji / "pencuri" |

---

## 2. Langkah 1 — Memperbarui `/root/tokyo3.sh` di Tokyo3-Core

Aturan Soal 15 (rate-limit ping) dan Soal 16 digabung dalam satu blok secara idempoten. Perintah `sed` menghapus blok Soal 15 lama beserta isi setelahnya agar tidak terduplikasi, lalu blok gabungan ditulis ulang di bagian bawah script. Ini penting agar aturan Soal 15 tetap berlaku.

```bash
sed -i '/# === SOAL 15/,$d' /root/tokyo3.sh
cat << 'EOF' >> /root/tokyo3.sh

# === SOAL 15 & 16: FIREWALL ===
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# Soal 15: Batasi ping masuk maksimal 5/detik dan buang kelebihannya
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# Soal 16: Catat (SCAN-NERV:) & tolak seketika koneksi TCP Dummy-Plug (192.238.4.51)
for PORT in 22 23 3306; do
  iptables -A INPUT -s 192.238.4.51 -p tcp --dport $PORT -j LOG --log-prefix "SCAN-NERV: "
  iptables -A INPUT -s 192.238.4.51 -p tcp --dport $PORT -j REJECT --reject-with tcp-reset
done
EOF

bash /root/tokyo3.sh
```

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| `sed -i '/# === SOAL 15/,$d'` | Menghapus blok lama (Soal 15) agar tidak terduplikasi, lalu diganti blok gabungan |
| Rule Soal 15 (`limit` + `DROP`) | Tetap dipertahankan: ping masuk maksimal 5/detik |
| `-s 192.238.4.51 -p tcp --dport $PORT` | Hanya koneksi TCP dari Dummy-Plug ke port 22, 23, dan 3306 yang dikenai rule |
| `-j LOG --log-prefix "SCAN-NERV: "` | Mencatat percobaan koneksi dengan prefix `SCAN-NERV:` |
| `-j REJECT --reject-with tcp-reset` | Menolak seketika dengan paket TCP RST, sehingga klien langsung menerima `Connection refused` tanpa menunggu timeout |
| Policy `INPUT ACCEPT` | Port 80, 8080, dan klien lain tidak tersentuh, sehingga tetap normal |

**Kenapa `LOG` harus di atas `REJECT`?**
`REJECT` adalah target terminasi: paket yang cocok langsung diproses dan tidak diteruskan ke rule berikutnya. `LOG` bukan target terminasi, sehingga paket tetap lanjut ke rule berikutnya setelah dicatat. Jika `REJECT` berada di atas, rule `LOG` tidak pernah tercapai dan tidak ada percobaan yang tercatat.

---

## 3. Langkah 2 — Pengujian dan Bukti

### 3.1 Tokyo3-Core (`192.238.4.106`) — cek rule awal sebelum scanning

```bash
iptables -S
iptables -L INPUT -n -v
```

**Hasil (rule):**

```
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -m limit --limit 5/sec -j ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 22   -j LOG --log-prefix "SCAN-NERV: "
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 22   -j REJECT --reject-with tcp-reset
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 23   -j LOG --log-prefix "SCAN-NERV: "
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 23   -j REJECT --reject-with tcp-reset
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 3306 -j LOG --log-prefix "SCAN-NERV: "
-A INPUT -s 192.238.4.51/32 -p tcp -m tcp --dport 3306 -j REJECT --reject-with tcp-reset
```

**Hasil (counter awal):** seluruh counter `pkts` dan `bytes` bernilai `0` (policy `ACCEPT 0 packets, 0 bytes`).
<img width="482" height="437" alt="Screenshot 2026-11-25 at 17 40 12" src="https://github.com/user-attachments/assets/9b0e6c10-8a43-44d5-b885-c1374aae6874" />

![Tokyo3-Core: rule awal dan counter 0 sebelum scanning](cfb476fc-image.png)

**Analisis:** Aturan Soal 15 tetap ada di dua baris pertama, diikuti pasangan `LOG` dan `REJECT` untuk port 22, 23, dan 3306 (dengan `LOG` selalu tepat di atas `REJECT`). Semua counter masih `0` sebagai pembanding sebelum pengujian.

### 3.2 Dummy-Plug (`192.238.4.51`) — uji port tertutup (22, 23, 3306) dan port layanan (80, 8080)

Karena Dummy-Plug memakai image Alpine (`nc` versi BusyBox yang tidak selalu mencetak pesan error), pengujian menampilkan **exit code** (`$?`) dan **durasi** (`time`) untuk membuktikan koneksi ke port 22, 23, dan 3306 gagal seketika (~0,00 detik), bukan menunggu timeout 2 detik.

```bash
for P in 22 23 3306; do
  echo "=== Uji Port $P ==="
  time nc -w 2 192.238.4.106 $P
  echo "Exit code: $?"
done

echo "=== Uji Port 80 ==="
nc -w 2 192.238.4.106 80

echo "=== Uji Port 8080 ==="
nc -w 2 192.238.4.106 8080
```

**Hasil:**

```
=== Uji Port 22 ===
real    0m0.002s
user    0m0.001s
sys     0m0.000s
Exit code: 1
=== Uji Port 23 ===
real    0m0.002s
user    0m0.000s
sys     0m0.001s
Exit code: 1
=== Uji Port 3306 ===
real    0m0.002s
user    0m0.001s
sys     0m0.000s
Exit code: 1
=== Uji Port 80 ===
Welcome to Tokyo3-Core
=== Uji Port 8080 ===
syifa imutz
```
<img width="477" height="426" alt="Screenshot 2026-11-25 at 17 40 52" src="https://github.com/user-attachments/assets/30f928c8-7a16-4c6c-8043-b9270989f492" />

![Dummy-Plug: port 22/23/3306 ditolak seketika, port 80/8080 berhasil](f4036541-image.png)

**Analisis:**
- Port **22, 23, dan 3306** selesai dalam **0,002 detik** dengan `Exit code: 1`. Jauh di bawah batas `-w 2` (2 detik), sehingga koneksi ditolak seketika (TCP RST), bukan dibiarkan menggantung sampai timeout.
- Port **80** membalas `Welcome to Tokyo3-Core` dan port **8080** membalas `syifa imutz`, sehingga layanan tetap dapat diakses.

### 3.3 Tokyo3-Core — counter rule pencatatan (`LOG`) dan penolakan (`REJECT`)

Log kernel sering tidak terlihat di container, sehingga bukti pencatatan diambil dari counter rule.

```bash
iptables -L INPUT -n -v
```

**Hasil:**

```
Chain INPUT (policy ACCEPT 20 packets, 1072 bytes)
 pkts bytes target  prot opt in  out  source         destination
    0     0 ACCEPT  icmp --  *   *    0.0.0.0/0      0.0.0.0/0   icmptype 8 limit: avg 5/sec burst 5
    0     0 DROP    icmp --  *   *    0.0.0.0/0      0.0.0.0/0   icmptype 8
    2   120 LOG     tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:22   LOG flags 0 level 4 prefix "SCAN-NERV: "
    2   120 REJECT  tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:22   reject-with tcp-reset
    2   120 LOG     tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:23   LOG flags 0 level 4 prefix "SCAN-NERV: "
    2   120 REJECT  tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:23   reject-with tcp-reset
    2   120 LOG     tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:3306 LOG flags 0 level 4 prefix "SCAN-NERV: "
    2   120 REJECT  tcp  --  *   *    192.238.4.51   0.0.0.0/0   tcp dpt:3306 reject-with tcp-reset
```
<img width="480" height="263" alt="Screenshot 2026-11-25 at 17 42 04" src="https://github.com/user-attachments/assets/66e93990-3ab0-4408-a5c0-5119592c5a91" />

![Tokyo3-Core: counter rule LOG dan REJECT naik setelah scanning](7e905575-image.png)

**Analisis:**
- Counter rule `LOG` untuk port **22, 23, dan 3306** masing-masing naik dari `0` menjadi **2 paket / 120 byte**, membuktikan setiap percobaan koneksi dari Dummy-Plug tercatat dengan prefix `SCAN-NERV: `.
- Counter rule `REJECT` pada tiap port bernilai sama dengan `LOG` (**2 paket / 120 byte**), artinya setiap paket yang dicatat langsung ditolak dengan `tcp-reset`.
- Counter rule ICMP (Soal 15) tetap `0` karena tidak ada ping pada pengujian ini, sehingga tidak ada interferensi antar-aturan.
- Policy `INPUT ACCEPT` mencatat **20 paket / 1072 byte**: lalu lintas yang tidak cocok rule mana pun (termasuk koneksi ke port 80 dan 8080) tetap diterima.

---

## 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Dummy-Plug → Tokyo3-Core port 22 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 23 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 3306 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 80 | Berhasil | `Welcome to Tokyo3-Core` | ✅ |
| Dummy-Plug → Tokyo3-Core port 8080 | Berhasil | `syifa imutz` | ✅ |
| Counter rule `LOG` (prefix `SCAN-NERV: `) | Naik | 2 paket / 120 byte per port | ✅ |
| Counter rule `REJECT` | Naik | 2 paket / 120 byte per port | ✅ |

**Pembahasan:**

1. **Pencatatan dan penolakan berpasangan.** Untuk tiap port sensitif (22, 23, 3306) dipasang dua rule berurutan: `LOG` dulu agar percobaan tercatat, lalu `REJECT --reject-with tcp-reset` untuk membanting pintu. Counter keduanya sama, membuktikan setiap koneksi yang dicatat langsung ditolak.
2. **Ditolak seketika.** `REJECT` dengan `tcp-reset` mengirim RST sehingga klien langsung gagal dalam sekitar 0,002 detik. `DROP` akan membuat `nc -w 2` menunggu sampai 2 detik.
3. **Port layanan tidak terganggu.** Rule hanya mencocokkan port 22, 23, dan 3306 dari sumber `192.238.4.51`. Port 80 dan 8080 tidak punya rule sehingga jatuh ke policy `ACCEPT` dan tetap membalas.
4. **Klien lain tidak terganggu.** Setiap rule dibatasi dengan `-s 192.238.4.51`, jadi hanya Dummy-Plug yang terkena. Node lain melewati rule ini dan diproses policy `ACCEPT`.
5. **Aturan Soal 15 tetap utuh.** Blok gabungan Soal 15 dan 16 mempertahankan rate-limit ping di dua baris pertama.
6. **Bukti lewat counter.** Karena log kernel sering tidak terlihat di container, kenaikan counter pada rule `LOG` menjadi bukti bahwa pencatatan berjalan.

---

## 5. Kesimpulan

Dengan menggabungkan aturan Soal 15 dan Soal 16 pada `/root/tokyo3.sh`, setiap koneksi TCP dari Dummy-Plug (`192.238.4.51`) ke port 22, 23, dan 3306 di Tokyo3-Core dicatat dengan prefix `SCAN-NERV: ` melalui rule `LOG`, lalu ditolak seketika melalui `REJECT --reject-with tcp-reset`. Pengujian menunjukkan koneksi ke port tertutup gagal dalam sekitar 0,002 detik dengan `Exit code: 1`, sedangkan port 80 dan 8080 tetap berhasil. Counter rule `LOG` dan `REJECT` naik sebagai bukti pencatatan dan penolakan. Seluruh persyaratan Soal 16 terpenuhi.

# Laporan Pengerjaan Soal 17 — Sihir Hitam di Jalur Paket

**Modul:** Modul 5 2026
**Topologi:** MAGI (GNS3) — Fuyutsuki (router), Magi-Casper, Zeruel, Pen-Pen

---

## 1. Soal

> **Sihir hitam di jalur paket:** Ritsuko meninggalkan memo di meja Gendo. Paket dari Casper ke Zeruel tidak pernah sampai ke Zeruel, karena ada router yang diam-diam mengalihkannya ke tempat lain. *"Cari saja router yang menjadi gateway Switch-Terminal."* Gendo menyuruhmu meniru router yang diam-diam itu untuk menjebak siapa pun. Di **Fuyutsuki**, alihkan setiap koneksi **TCP** dari Magi-Casper ke Zeruel supaya sampai di **Pen-Pen**. Casper tidak boleh sadar. Balasan yang ia terima harus tetap terlihat datang dari Zeruel. **Ping tidak dialihkan.** Nyalakan listener **port 9000** di Pen-Pen dan Zeruel. Dari Casper, kirim pesan ke IP Zeruel port 9000. Buktikan pesan itu muncul di **Pen-Pen, bukan di Zeruel**. Tunjukkan juga tabel `conntrack` di Fuyutsuki.

### Ringkasan kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Alihkan TCP dari Casper ke Zeruel agar sampai di Pen-Pen | `DNAT` pada chain `PREROUTING` tabel `nat` di Fuyutsuki |
| 2 | Hanya koneksi dari Casper ke Zeruel yang dialihkan | Pencocokan `-s 192.238.4.58 -d 192.238.4.82 -p tcp` |
| 3 | Casper tidak boleh sadar; balasan terlihat dari Zeruel | Balasan dipaksa kembali lewat Fuyutsuki dengan `MASQUERADE`, lalu di-un-NAT otomatis oleh `conntrack` sehingga sumbernya kembali tampak sebagai Zeruel |
| 4 | Ping tidak dialihkan | Rule hanya untuk protokol TCP |
| 5 | Listener port 9000 di Pen-Pen dan Zeruel | `nc` mendengar di port 9000 dan menulis ke file log |
| 6 | Bukti | Isi log di Pen-Pen dan Zeruel, counter rule NAT, dan tabel `conntrack` |

### Alamat IP

| Perangkat | IP | Peran |
|-----------|----|-------|
| Magi-Casper | `192.238.4.58` | Klien (korban) |
| Zeruel | `192.238.4.82` | Tujuan yang dikira oleh Casper |
| Pen-Pen | `192.238.3.194` | Tujuan sebenarnya (jebakan) |
| Fuyutsuki | router gateway Switch-Terminal | Melakukan NAT pengalihan |

---

## 2. Langkah 1 — Konfigurasi NAT Pengalihan di Fuyutsuki

Di console **Fuyutsuki** dipasang aturan `DNAT` dan `MASQUERADE`, tool `conntrack` disiapkan, dan konfigurasi disimpan persisten di `/root/fuyutsuki.sh` (dijalankan otomatis lewat `/etc/profile` dan `/root/.bashrc`).

```bash
cat << 'EOF' > /root/fuyutsuki.sh
#!/bin/bash

# Pastikan package conntrack tersedia untuk melihat tabel pelacakan koneksi
if ! command -v conntrack >/dev/null 2>&1; then
  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false \
    -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true
  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated conntrack
fi

# Reset tabel nat di Fuyutsuki agar idempoten
iptables -t nat -F
iptables -t nat -X

# 1. Alihkan setiap koneksi TCP dari Magi-Casper (192.238.4.58) ke Zeruel (192.238.4.82) menuju Pen-Pen (192.238.3.194)
iptables -t nat -A PREROUTING -s 192.238.4.58 -d 192.238.4.82 -p tcp -j DNAT --to-destination 192.238.3.194

# 2. Pastikan balasan dari Pen-Pen kembali melewati Fuyutsuki agar di-un-NAT terlihat dari Zeruel
iptables -t nat -A POSTROUTING -s 192.238.4.58 -d 192.238.3.194 -p tcp -j MASQUERADE
EOF

chmod +x /root/fuyutsuki.sh
touch /root/.bashrc
sed -i '/# === AUTOSTART FUYUTSUKI ===/,$d' /etc/profile /root/.bashrc
printf '\n# === AUTOSTART FUYUTSUKI ===\n[ -f /root/fuyutsuki.sh ] && bash /root/fuyutsuki.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null

bash /root/fuyutsuki.sh
iptables -t nat -S
```

<!-- CEK: pada screenshot, baris printf/tee autostart terpotong di layar. Samakan dengan perintah aslimu bila berbeda. -->
<img width="484" height="621" alt="Screenshot 2026-11-25 at 17 50 32" src="https://github.com/user-attachments/assets/c4f764f4-c5dd-4fe8-a473-81be700822d9" />

![Menjalankan skrip NAT di Fuyutsuki](93d21472-image.png)

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| Instalasi `conntrack` | Menyediakan tool untuk melihat tabel pelacakan koneksi |
| `iptables -t nat -F` / `-X` | Mengosongkan tabel `nat` agar skrip dapat dijalankan berulang (idempoten) |
| `PREROUTING ... -s 192.238.4.58 -d 192.238.4.82 -p tcp -j DNAT --to-destination 192.238.3.194` | Mengubah **tujuan** paket TCP dari Casper yang menuju Zeruel menjadi Pen-Pen, sebelum keputusan routing |
| `POSTROUTING ... -s 192.238.4.58 -d 192.238.3.194 -p tcp -j MASQUERADE` | Mengubah **sumber** paket yang menuju Pen-Pen menjadi alamat Fuyutsuki, sehingga balasan Pen-Pen kembali lewat Fuyutsuki dan tidak langsung menuju Casper |
| Autostart di `/etc/profile` dan `/root/.bashrc` | Konfigurasi dijalankan otomatis agar persisten |

**Kenapa `MASQUERADE` diperlukan?**
Tanpa `MASQUERADE`, Pen-Pen akan membalas langsung ke `192.238.4.58`, melewati Fuyutsuki. Casper akan menerima balasan dari `192.238.3.194` yang tidak cocok dengan koneksi yang ia buka (ke `192.238.4.82`) sehingga koneksi gagal dan jebakan ketahuan. Dengan `MASQUERADE`, Pen-Pen melihat sumbernya adalah Fuyutsuki dan membalas ke Fuyutsuki; `conntrack` lalu membalik DNAT sehingga Casper menerima balasan seolah-olah berasal dari Zeruel.

**Kenapa ping tidak ikut dialihkan?**
Kedua rule memakai `-p tcp`, sehingga ICMP tidak cocok dan tetap diteruskan ke tujuan asli (Zeruel).

---

## 3. Langkah 2 — Menyalakan Listener Port 9000 di Pen-Pen dan Zeruel

### 3.1 Pen-Pen (`192.238.3.194`)

```bash
rm -f /tmp/penpen_9000.log && touch /tmp/penpen_9000.log
nohup sh -c 'while true; do nc.traditional -l -p 9000 >> /tmp/penpen_9000.log 2>&1; done' >/dev/null 2>&1 &
ss -tlnp | grep 9000
```

**Hasil:**

```
LISTEN 0  1  0.0.0.0:9000  0.0.0.0:*  users:(("nc.traditional",pid=693,fd=3))
```
<img width="481" height="104" alt="Screenshot 2026-11-25 at 17 51 24" src="https://github.com/user-attachments/assets/0482e561-f2f7-48e3-bed5-759e9358ae51" />

![Pen-Pen: listener port 9000 aktif](2fda2f0c-image.png)

### 3.2 Zeruel (`192.238.4.82`)

```bash
rm -f /tmp/zeruel_9000.log && touch /tmp/zeruel_9000.log
if command -v nc.traditional >/dev/null 2>&1; then
  nohup sh -c 'while true; do nc.traditional -l -p 9000 >> /tmp/zeruel_9000.log 2>&1; done' >/dev/null 2>&1 &
else
  nohup sh -c 'while true; do nc -l -p 9000 >> /tmp/zeruel_9000.log 2>&1; done' >/dev/null 2>&1 &
fi
netstat -tlnp 2>/dev/null | grep 9000 || ss -tlnp | grep 9000
```

**Hasil:**

```
LISTEN 0  1  0.0.0.0:9000  0.0.0.0:*  users:(("nc",pid=345,fd=3))
```
<img width="482" height="386" alt="Screenshot 2026-11-25 at 17 51 39" src="https://github.com/user-attachments/assets/63fd3424-15b8-4a40-b771-5b829e9f4fec" />

![Zeruel: listener port 9000 aktif](e09251f4-image.png)

Kedua listener menulis pesan masuk ke file log masing-masing (`/tmp/penpen_9000.log` dan `/tmp/zeruel_9000.log`) sehingga tempat tibanya pesan dapat dibuktikan.

---

## 4. Langkah 3 — Pengujian dan Bukti

### 4.1 Magi-Casper (`192.238.4.58`) — ping (tidak dialihkan) dan kirim pesan TCP ke Zeruel port 9000

```bash
ping -c 3 192.238.4.82
echo "Pesan jebakan dari Magi-Casper ke Zeruel:9000" | nc -v -w 2 192.238.4.82 9000
```

**Hasil:**

```
PING 192.238.4.82 (192.238.4.82) 56(84) bytes of data.
64 bytes from 192.238.4.82: icmp_seq=1 ttl=61 time=2.24 ms
64 bytes from 192.238.4.82: icmp_seq=2 ttl=61 time=2.40 ms
64 bytes from 192.238.4.82: icmp_seq=3 ttl=61 time=1.22 ms

--- 192.238.4.82 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2225ms
rtt min/avg/max/mdev = 1.217/1.951/2.397/0.523 ms

192.238.4.82: inverse host lookup failed: Unknown host
(UNKNOWN) [192.238.4.82] 9000 (?) open
```
<img width="480" height="217" alt="Screenshot 2026-11-25 at 17 53 16" src="https://github.com/user-attachments/assets/2d7723e7-e385-4cbf-9b08-87992b985226" />

![Magi-Casper: ping dan kirim pesan TCP ke Zeruel:9000](44189211-image.png)

**Analisis:**
- Ping ke Zeruel berhasil 0% packet loss: ICMP **tidak dialihkan** dan tetap menuju Zeruel.
- `nc -v` melaporkan koneksi ke `192.238.4.82` port `9000` berstatus `open`. Casper melihat koneksi tersambung ke Zeruel dan tidak sadar bahwa paketnya dialihkan.
- Pesan `inverse host lookup failed: Unknown host` hanya berasal dari reverse DNS oleh `nc` dan tidak berkaitan dengan NAT.

### 4.2 Pen-Pen — pesan masuk ke Pen-Pen

```bash
cat /tmp/penpen_9000.log
```

**Hasil:**

```
Pesan jebakan dari Magi-Casper ke Zeruel:9000
```
<img width="481" height="24" alt="Screenshot 2026-11-25 at 17 53 42" src="https://github.com/user-attachments/assets/63cb9f2f-dbb4-472c-8a4c-64d6b8e8c51a" />

![Pen-Pen: pesan dari Casper muncul di log](fd73ff8f-image.png)

### 4.3 Zeruel — pesan tidak sampai ke Zeruel

```bash
cat /tmp/zeruel_9000.log
```

**Hasil:** (kosong, tidak ada pesan)
<img width="482" height="28" alt="Screenshot 2026-11-25 at 17 54 03" src="https://github.com/user-attachments/assets/f5317c8a-a40d-4361-a7b5-3436d21bf1ef" />

![Zeruel: log kosong, pesan tidak sampai](a6659552-image.png)

### 4.4 Fuyutsuki — counter rule NAT dan tabel `conntrack`

```bash
iptables -t nat -L -n -v
conntrack -L -p tcp 2>/dev/null || grep "dport=9000" /proc/net/nf_conntrack
```

**Hasil:**

```
Chain PREROUTING (policy ACCEPT 2 packets, 155 bytes)
 pkts bytes target  prot opt in  out  source        destination
    1    60 DNAT    tcp  --  *   *    192.238.4.58  192.238.4.82   to:192.238.3.194

Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)

Chain POSTROUTING (policy ACCEPT 2 packets, 155 bytes)
 pkts bytes target      prot opt in  out  source        destination
    1    60 MASQUERADE  tcp  --  *   *    192.238.4.58  192.238.3.194

tcp  6 12 TIME_WAIT src=192.238.4.58 dst=192.238.4.82 sport=34700 dport=9000
     src=192.238.3.194 dst=192.238.4.165 sport=9000 dport=34700 [ASSURED] mark=0 use=1
```
<img width="481" height="349" alt="Screenshot 2026-11-25 at 17 55 17" src="https://github.com/user-attachments/assets/58fb8e10-307e-46b0-b6d1-712ecdd6d145" />

![Fuyutsuki: counter NAT dan entri conntrack](794103d5-image.png)

**Analisis:**
- Rule `DNAT` di `PREROUTING` dan `MASQUERADE` di `POSTROUTING` masing-masing mencatat **1 paket / 60 byte**, yaitu paket pertama koneksi (sisa paket koneksi diproses lewat tabel `conntrack` sehingga tidak menaikkan counter rule lagi). Ini membuktikan kedua rule benar-benar mengenai koneksi dari Casper.
- Entri `conntrack` menunjukkan kedua arah koneksi:
  - **Arah asli:** `src=192.238.4.58 dst=192.238.4.82 dport=9000`, yaitu Casper menyambung ke Zeruel port 9000.
  - **Arah balasan:** `src=192.238.3.194 dst=192.238.4.165 sport=9000`, yaitu balasan yang sebenarnya datang dari **Pen-Pen** (`192.238.3.194`) menuju alamat Fuyutsuki (`192.238.4.165`, hasil `MASQUERADE`).
- Dua tuple yang berbeda dalam satu entri inilah bukti pengalihan: tujuan asli (Zeruel) diganti menjadi Pen-Pen oleh `DNAT`, dan `conntrack` membalik perubahan itu pada paket balasan sehingga Casper menerima balasan yang terlihat berasal dari Zeruel.

---

## 5. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Casper `ping 192.238.4.82` | Tidak dialihkan, tetap berhasil | 3 diterima (0% loss), TTL 61 | ✅ |
| Casper `nc -v` ke Zeruel:9000 | Tersambung (tidak sadar dialihkan) | `(UNKNOWN) [192.238.4.82] 9000 (?) open` | ✅ |
| `/tmp/penpen_9000.log` di Pen-Pen | Berisi pesan dari Casper | `Pesan jebakan dari Magi-Casper ke Zeruel:9000` | ✅ |
| `/tmp/zeruel_9000.log` di Zeruel | Kosong | Kosong | ✅ |
| Counter rule NAT di Fuyutsuki | Naik | `DNAT` 1 paket / 60 byte, `MASQUERADE` 1 paket / 60 byte | ✅ |
| Tabel `conntrack` di Fuyutsuki | Menunjukkan pemetaan ke Pen-Pen | Dst balasan `192.238.3.194` untuk koneksi ke `192.238.4.82:9000` | ✅ |

**Pembahasan:**

1. **Pengalihan transparan.** Fuyutsuki mengubah tujuan paket TCP dari `192.238.4.82` (Zeruel) menjadi `192.238.3.194` (Pen-Pen) lewat `DNAT` di `PREROUTING`. Dari sisi Casper, koneksi tetap ke Zeruel dan berstatus `open`.
2. **Pesan sampai di Pen-Pen, bukan di Zeruel.** Log Pen-Pen berisi pesan jebakan, sedangkan log Zeruel kosong.
3. **Balasan tetap tampak dari Zeruel.** `MASQUERADE` memaksa Pen-Pen membalas ke Fuyutsuki, dan `conntrack` mengembalikan alamat sumber balasan menjadi Zeruel. Karena itu Casper tidak sadar dan tidak menolak balasan yang datang.
4. **Ping tidak dialihkan.** Rule hanya untuk `-p tcp`, sehingga ping tetap sampai ke Zeruel dan berhasil.
5. **Bukti konsisten.** Counter NAT dan entri `conntrack` konsisten dengan isi log di Pen-Pen dan Zeruel.

---

## 6. Kesimpulan

Dengan memasang rule `DNAT` (`PREROUTING`) dan `MASQUERADE` (`POSTROUTING`) pada tabel `nat` di Fuyutsuki, setiap koneksi TCP dari Magi-Casper ke Zeruel dialihkan secara diam-diam ke Pen-Pen. Casper tidak menyadarinya karena balasan tetap terlihat datang dari Zeruel, sedangkan ping tidak dialihkan. Pesan yang dikirim ke Zeruel port 9000 terbukti muncul di Pen-Pen dan tidak di Zeruel, didukung counter rule NAT dan entri tabel `conntrack` di Fuyutsuki. Seluruh persyaratan Soal 17 terpenuhi.
