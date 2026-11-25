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
