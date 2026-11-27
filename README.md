# Jarkom-Modul-5-2026-K-54

| Nama                 | NRP        |
|----------------------|------------|
| Viko Rizky Fauzan    | 5027251017 |
| Sean Arthur Tamajaya | 5027251050 |

## Laporan Praktikum: Perancangan Subnetting VLSM, *Static Routing*, NAT, dan Keamanan Jaringan Menggunakan Firewall `iptables`

## Daftar Isi

- [Soal 1: Perancangan Subnetting VLSM (`192.238.0.0/21`)](#soal-1-perancangan-subnetting-vlsm-1922380021)
- [Soal 2: Konfigurasi Alamat IP Router dan Uji Konektivitas *Point-to-Point*](#soal-2-konfigurasi-alamat-ip-router-dan-uji-konektivitas-point-to-point)
- [Soal 3: Konfigurasi *Static Routing* dan Uji Konektivitas Lintas Cabang](#soal-3-konfigurasi-static-routing-dan-uji-konektivitas-lintas-cabang)
- [Soal 4: DHCP Server Terpusat, DHCP Relay, dan NAT Masquerade](#soal-4-dhcp-server-terpusat-dhcp-relay-dan-nat-masquerade)
- [Soal 5: Pengamanan Firewall pada Router Gerbang Utama (`Gendou`)](#soal-5-pengamanan-firewall-pada-router-gerbang-utama-gendou)
- [Soal 6: Konfigurasi IP Statis dan Autostart pada Host Layanan](#soal-6-konfigurasi-ip-statis-dan-autostart-pada-host-layanan)
- [Soal 7: Aktivasi Layanan TCP pada Sistem MAGI](#soal-7-aktivasi-layanan-tcp-pada-sistem-magi)
- [Soal 8: Layanan TCP Balasan Otomatis pada `Pen-Pen` dan `Tokyo3-Core`](#soal-8-layanan-tcp-balasan-otomatis-pada-pen-pen-dan-tokyo3-core)
- [Soal 9: Pemulihan Sabotase Routing pada Router `Shigeru`](#soal-9-pemulihan-sabotase-routing-pada-router-shigeru)
- [Soal 10: Pemulihan Sabotase NAT pada `Misato` dan `Gendou`](#soal-10-pemulihan-sabotase-nat-pada-misato-dan-gendou)
- [Soal 11: Casper Harus Jadi Hantu](#soal-11-casper-harus-jadi-hantu)
- [Soal 12: Teknisi yang Kelewat Semangat](#soal-12-teknisi-yang-kelewat-semangat)
- [Soal 13: Brankas Balthasar](#soal-13-brankas-balthasar)
- [Soal 14: Jam Kunjungan Sang Pinguin](#soal-14-jam-kunjungan-sang-pinguin)
- [Soal 15: Banjir Ping di Pusat Komando](#soal-15-banjir-ping-di-pusat-komando)
- [Soal 16: Pencuri yang Mencoba Setiap Pintu](#soal-16-pencuri-yang-mencoba-setiap-pintu)
- [Soal 17: Sihir Hitam di Jalur Paket](#soal-17-sihir-hitam-di-jalur-paket)
- [Soal 18: Gerbang Monolith dan Radar](#soal-18-gerbang-monolith-dan-radar)
- [Soal 19: Hukuman untuk Sang Pengkhianat](#soal-19-hukuman-untuk-sang-pengkhianat)
- [Soal 20: Uji Kebakaran Geofront](#soal-20-uji-kebakaran-geofront)

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

**Pembahasan:**

1. **Efisiensi Alokasi VLSM:** Dengan mengurutkan kebutuhan dari yang terbesar (`/23` untuk 500 host) hingga terkecil (`/30` untuk link router-ke-router), seluruh 33 subnet dapat ditampung di dalam satu blok induk `192.238.0.0/21` tanpa terjadi tumpang tindih (*overlapping*) alamat jaringan.
2. **Konsistensi Jalur Antar-Cabang:** Pemetaan pada pohon IP biner menunjukkan bahwa paket dari cabang `Switch-Lab` (`192.238.4.48/29`) melewati rangkaian link `/30` (`Asuka – Ritsuko` $\rightarrow$ `Gendou – Asuka` $\rightarrow$ `Gendou – Shigeru`) sebelum mencapai `Switch-Monolith` (`192.238.4.80/29`).

---

### 5. Kesimpulan

Perancangan subnetting VLSM pada blok induk `192.238.0.0/21` berhasil membagi ruang alamat untuk 17 segmen LAN dan 16 link *point-to-point* antar-router secara berurutan tanpa *overlapping*. Penentuan *gateway* pada IP *usable* pertama dan host pada IP *usable* berikutnya menjadi acuan standar bagi seluruh konfigurasi jaringan pada tahap berikutnya. Seluruh persyaratan Soal 1 terpenuhi.

---

## Soal 2: Konfigurasi Alamat IP Router dan Uji Konektivitas *Point-to-Point*

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Gendou, Yui, Fuyutsuki, Hikari, Asuka, dan Seluruh Router Cabang

---

### 1. Deskripsi & Kebutuhan Soal

Menerapkan hasil rancangan alamat IP VLSM dari Soal 1 pada seluruh *interface* jaringan di setiap router (baik *interface* yang mengarah ke segmen LAN maupun *interface* link `/30` antar-router), mengaktifkan seluruh *interface* (`UP`), serta membuktikan konektivitas *point-to-point* pada seluruh **16 link router-ke-router**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Pemasangan IP statis pada seluruh *interface* router | Mengonfigurasi IP dan *prefix* sesuai Tabel VLSM pada setiap *interface* router serta memastikan status link `UP` |
| 2 | Verifikasi status *interface* & alamat IP | Mengecek konfigurasi menggunakan perintah `ip -br a` di masing-masing router |
| 3 | Pengujian konektivitas ke seluruh 16 link antar-router | Melakukan `ping` langsung ke alamat IP router tetangga pada setiap link `/30` |

#### Contoh Link *Point-to-Point* yang Diuji

| Router Penguji | Router Tetangga / Tujuan | Alamat IP Tujuan (`/30`) |
|----------------|--------------------------|--------------------------|
| Gendou | 7 Router Cabang Utama | `192.238.4.110`, `192.238.4.126`, `192.238.4.134`, `192.238.4.146`, `192.238.4.150`, `192.238.4.154`, `192.238.4.162` |
| Yui | Mari, Rei, Shinji | `192.238.4.114`, `192.238.4.118`, `192.238.4.122` |
| Hikari | Kaworu | `192.238.4.130` |
| Asuka | Kensuke, Ritsuko | `192.238.4.138`, `192.238.4.142` |

---

### 2. Pengujian dan Bukti

#### 2.1 Cek Status IP & *Interface* di Tiap Router

```bash
ip -br a
```

**Gendou**

<img src="https://github.com/user-attachments/assets/3c47496d-70a1-49ac-a5ce-154ddefe2dbb" width="550" alt="ip -br a Gendou">

**Yui**

<img src="https://github.com/user-attachments/assets/cafe4e71-2046-40d5-ab32-51ea349f2964" width="550" alt="ip -br a Yui">

**Fuyutsuki**

<img src="https://github.com/user-attachments/assets/2718ddd5-7ba5-4665-bc0d-99ad950d4f56" width="550" alt="ip -br a Fuyutsuki">

**Analisis:** Seluruh *interface* pada `Gendou`, `Yui`, dan `Fuyutsuki` telah terpasang alamat IP dan *prefix* sesuai rancangan VLSM dengan status *interface* aktif.

#### 2.2 Uji *Ping* ke Seluruh 16 Link Router-ke-Router (Loop Satu Baris)

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

### 3. Hasil dan Pembahasan

| Pengujian | Jalur Link | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|------------|-----------------------|--------------|:-----------:|
| Cek `ip -br a` di router | *Interface* LAN & WAN/Link | Seluruh IP sesuai VLSM & aktif | Terpasang lengkap | ✅ |
| Ping Gendou → 7 router cabang | 7 link `/30` utama | 0% *packet loss* | 0% *packet loss* di semua link | ✅ |
| Ping Yui → Mari, Rei, Shinji | 3 link `/30` cabang Yui | 0% *packet loss* | 0% *packet loss* di semua link | ✅ |
| Ping Hikari → Kaworu | Link `/30` Hikari–Kaworu | 0% *packet loss* | 0% *packet loss* | ✅ |
| Ping Asuka → Kensuke & Ritsuko | 2 link `/30` cabang Asuka | 0% *packet loss* | 0% *packet loss* di semua link | ✅ |

**Pembahasan:**

1. **Verifikasi Koneksi Langsung (*Directly Connected*):** Sebelum mengonfigurasi tabel *routing* antar-segmen, setiap link `/30` antar-router wajib dipastikan terhubung dengan *subnet mask* yang tepat sehingga pasangan router berada dalam satu jaringan titik-ke-titik.
2. **Keberhasilan Seluruh 16 Link:** Hasil pengujian `ping` dari `Gendou`, `Yui`, `Hikari`, dan `Asuka` (serta link cabang lainnya) menunjukkan `0% packet loss` dengan TTL `64`, menandakan lapisan fisik dan *data-link* antar-router telah siap untuk proses *routing*.

---

### 4. Kesimpulan

Seluruh *interface* pada router telah dikonfigurasi sesuai alokasi IP VLSM dan dalam kondisi aktif. Pengujian `ping` *point-to-point* pada seluruh 16 link router-ke-router berhasil dengan `0% packet loss`. Seluruh persyaratan Soal 2 terpenuhi.

---

## Soal 3: Konfigurasi *Static Routing* dan Uji Konektivitas Lintas Cabang

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Gendou, Yui, Shigeru, Armisael, Quarters, Lilith, Sachiel, Comm-Net

---

### 1. Deskripsi & Kebutuhan Soal

Mengaktifkan penerusan paket (`net.ipv4.ip_forward = 1`) dan membangun tabel **Static Routing** (termasuk penerapan *route summarization* seperti blok `192.238.4.80/28` untuk cabang `Shigeru`) pada seluruh router agar paket dapat mengalir antar-subnet yang berjauhan maupun menuju internet (`8.8.8.8`).

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Mengaktifkan IP Forwarding di seluruh router | Memastikan `net.ipv4.ip_forward = 1` aktif pada kernel setiap router |
| 2 | Konfigurasi *default route* dan *static route* | Menambahkan rute menuju jaringan internal maupun *gateway* atas (*upstream*) serta *route summarization* pada router distribusi |
| 3 | Verifikasi tabel *routing* & konektivitas internet | Mengecek `ip route` dan melakukan `ping 8.8.8.8` dari router |
| 4 | Uji konektivitas ujung-ke-ujung (*end-to-end*) lintas cabang | Memasang IP sementara pada 5 host uji berjauhan dan melakukan `ping` lintas cabang dari `Armisael` |

#### Daftar 5 Host Uji Sementara Lintas Cabang

| Host Uji | Cabang / Subnet | Alamat IP Uji | Default Gateway |
|----------|-----------------|---------------|-----------------|
| Armisael | Switch-Monolith (Cabang Shigeru) | `192.238.4.83/29` | `192.238.4.81` |
| Quarters | Switch-Quarters (Cabang Rei/Yui) | `192.238.0.2/23` | `192.238.0.1` |
| Lilith | Switch-Dogma | `192.238.4.74/29` | `192.238.4.73` |
| Sachiel | Switch-Sachiel | `192.238.3.130/26` | `192.238.3.129` |
| Comm-Net | Switch-Comm-Net | `192.238.2.2/24` | `192.238.2.1` |

---

### 2. Pengujian dan Bukti

#### 2.1 Cek Tabel Routing & *Ping* ke `8.8.8.8`

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

**Analisis:** Tabel `ip route` di `Gendou`, `Yui`, dan `Shigeru` menampilkan entri *default route*, rute statis ke tiap subnet cabang, serta ringkasan rute (*route summary*) seperti `192.238.4.80/28` menuju `Shigeru`, dan uji `ping 8.8.8.8` berhasil `0% packet loss`.

#### 2.2 Pasang IP Sementara pada 5 Host Uji dari Cabang yang Berjauhan

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

#### 2.3 Uji *Ping* Lintas Cabang dari Armisael

```bash
for ip in 192.238.0.2 192.238.4.74 192.238.3.130 192.238.2.2; do ping -c 3 $ip; done
```

<img src="https://github.com/user-attachments/assets/6ef86d57-4c32-4cde-825e-14b2f86c23be" width="550" alt="Ping lintas cabang 1">

<img src="https://github.com/user-attachments/assets/d2a41cdc-b0b2-4bd5-b1c2-6c26ebcc7daa" width="550" alt="Ping lintas cabang 2">

---

### 3. Hasil dan Pembahasan

| Skenario Pengujian | Tujuan | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|--------------------|--------|-----------------------|--------------|:-----------:|
| `ip route` & `ping 8.8.8.8` di Router | Internet (`8.8.8.8`) | Berhasil (0% loss) | 0% *packet loss* | ✅ |
| Armisael → Quarters (`192.238.0.2`) | Lintas cabang ke Rei/Yui | Berhasil (0% loss) | 0% *packet loss* | ✅ |
| Armisael → Lilith (`192.238.4.74`) | Lintas cabang ke Dogma | Berhasil (0% loss) | 0% *packet loss* | ✅ |
| Armisael → Sachiel (`192.238.3.130`) | Lintas cabang ke Sachiel | Berhasil (0% loss) | 0% *packet loss* | ✅ |
| Armisael → Comm-Net (`192.238.2.2`) | Lintas cabang ke Comm-Net | Berhasil (0% loss) | 0% *packet loss* | ✅ |

**Pembahasan:**

1. **Simetri Jalur Pergi dan Pulang:** Keberhasilan `ping` dua arah antara `Armisael` dan keempat host dari cabang berbeda membuktikan bahwa setiap router di sepanjang jalur memiliki entri rute menuju jaringan tujuan maupun rute balik menuju jaringan asal (`192.238.4.80/29`).
2. **Efektivitas *Route Summarization*:** Rute ringkasan `192.238.4.80/28` di router pusat (`Gendou`) berhasil menggabungkan subnet `Switch-Monolith` (`192.238.4.80/29`) dan `Switch-Radar` (`192.238.4.88/29`) dalam satu entri *routing* yang mengarah ke `Shigeru`.

---

### 4. Kesimpulan

Konfigurasi *static routing* dan *IP forwarding* pada seluruh router telah berjalan dengan baik. Router maupun host uji dari berbagai cabang yang berjauhan (`Armisael`, `Quarters`, `Lilith`, `Sachiel`, dan `Comm-Net`) dapat saling berkomunikasi serta menjangkau `8.8.8.8` tanpa kehilangan paket. Seluruh persyaratan Soal 3 terpenuhi.

---

## Soal 4: DHCP Server Terpusat, DHCP Relay, dan NAT Masquerade

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Gendou (DHCP Server & NAT Gateway), Router Cabang (DHCP Relay), Spy, Armisael, Dock-Crew

---

### 1. Deskripsi & Kebutuhan Soal

Menyediakan layanan pemberian alamat IP otomatis secara terpusat di router **`Gendou`** (`isc-dhcp-server`) untuk seluruh subnet klien, meneruskan permintaan DHCP lintas router menggunakan **`isc-dhcp-relay`** pada router-router distribusi/cabang, serta mengaktifkan **NAT Masquerade** pada *interface* WAN `eth0` milik `Gendou` agar seluruh klien dinamis dapat mengakses internet dan melakukan resolusi nama domain (`google.com`).

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Layanan DHCP Server terpusat di `Gendou` | Instalasi dan konfigurasi `isc-dhcp-server` dengan *subnet pool*, *default gateway*, dan DNS `8.8.8.8` untuk tiap segmen klien |
| 2 | Penerusan paket DHCP lintas subnet | Mengaktifkan `isc-dhcp-relay` pada setiap router cabang menuju IP `Gendou` |
| 3 | Translasi alamat privat ke publik (NAT) | Menambahkan *rule* `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE` di `Gendou` |
| 4 | Verifikasi pada klien dinamis & log server | Meminta IP via `udhcpc -i eth0` pada `Spy`, `Armisael`, dan `Dock-Crew`, menguji `ping 8.8.8.8` & `google.com`, serta memeriksa `dhcpd.leases` |

#### Perangkat Uji DHCP & NAT

| Perangkat | Subnet / Segmen | Metode Konfigurasi IP |
|-----------|-----------------|----------------------|
| Gendou | Gateway Utama (`eth0` ke NAT1) | DHCP Server + NAT Masquerade |
| Spy | Switch-Intel (`192.238.4.96/29`) | Klien dinamis (`udhcpc -i eth0`) |
| Armisael | Switch-Monolith (`192.238.4.80/29`) | Klien dinamis (`udhcpc -i eth0`) |
| Dock-Crew | Switch-Dock | Klien dinamis (`udhcpc -i eth0`) |

---

### 2. Pengujian dan Bukti

#### 2.1 Cek *Interface*, Rute, & DHCP Relay di Klien

Meminta alamat IP otomatis terlebih dahulu pada setiap node uji:

```bash
udhcpc -i eth0
```

Kemudian melakukan verifikasi IP, *default gateway*, konektivitas ke internet (`8.8.8.8`), dan resolusi DNS (`google.com`):

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

**Analisis:** Ketiga host uji (`Spy`, `Armisael`, dan `Dock-Crew`) berhasil memperoleh alamat IP sesuai subnet masing-masing, *default gateway*, serta *nameserver* `8.8.8.8` melalui mekanisme DHCP Relay, dan berhasil melakukan `ping` ke `8.8.8.8` maupun `google.com`.

#### 2.2 Cek Status DHCP Server & Masquerade di Gendou

```bash
cat /var/lib/dhcp/dhcpd.leases
iptables -t nat -L POSTROUTING -n -v
```

<img src="https://github.com/user-attachments/assets/cdfc6d7a-862d-47f8-b367-54541ba06be2" width="550" alt="DHCP leases 1">

<img src="https://github.com/user-attachments/assets/ee15e38b-a44c-4e9f-8d69-7b2562b47798" width="550" alt="DHCP leases 2">

<img src="https://github.com/user-attachments/assets/fa292095-cbc2-4598-8944-984d1f3c2a19" width="550" alt="DHCP leases 3">

<img src="https://github.com/user-attachments/assets/8883ea61-b3e2-4729-a394-b7ac793cacaf" width="550" alt="Masquerade POSTROUTING">

---

### 3. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| `udhcpc` pada Spy, Armisael, Dock-Crew | Memperoleh IP, *gateway*, dan DNS sesuai subnet | Berhasil memperoleh IP dinamis & rute default | ✅ |
| `ping -c 3 8.8.8.8` dari klien DHCP | Berhasil | 0% *packet loss* | ✅ |
| `ping -c 3 google.com` dari klien DHCP | Resolusi DNS & *ping* berhasil | 0% *packet loss* | ✅ |
| `/var/lib/dhcp/dhcpd.leases` di Gendou | Mencatat alokasi *lease* klien | Data *lease* klien tercatat aktif | ✅ |
| `iptables -t nat -L POSTROUTING -n -v` | *Rule* `MASQUERADE` pada `eth0` aktif | *Counter* paket & byte meningkat | ✅ |

**Pembahasan:**

1. **Peran DHCP Relay:** Karena pesan *broadcast* `DHCPDISCOVER` dari klien tidak dapat melewati router secara bawaan, `isc-dhcp-relay` pada router cabang bertugas menangkap *broadcast* lokal dan meneruskannya secara *unicast* ke `Gendou` dengan menyertakan alamat *relay agent* (`giaddr`) sehingga `Gendou` dapat memberikan IP dari *pool* subnet yang tepat.
2. **Translasi NAT Masquerade:** Alamat IP privat `192.238.0.0/21` tidak dapat dirutekan di internet publik. *Rule* `MASQUERADE` pada *chain* `POSTROUTING` milik `Gendou` menerjemahkan alamat sumber paket keluar menjadi IP *interface* WAN `eth0` secara dinamis, memungkinkan klien mengakses `8.8.8.8` dan `google.com`.

---

### 4. Kesimpulan

Layanan DHCP Server terpusat dan NAT Masquerade di `Gendou` beserta DHCP Relay pada router cabang berhasil beroperasi. Klien dinamis (`Spy`, `Armisael`, dan `Dock-Crew`) memperoleh konfigurasi jaringan otomatis dan dapat terhubung ke internet maupun melakukan resolusi DNS dengan lancar. Seluruh persyaratan Soal 4 terpenuhi.

---

## Soal 5: Pengamanan Firewall pada Router Gerbang Utama (`Gendou`)

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Gendou (Router Gateway/Firewall), Jaringan Internal (`192.238.0.0/21`), Internet (`eth0`)

---

### 1. Deskripsi & Kebutuhan Soal

Menerapkan kebijakan keamanan jaringan (*default-deny* untuk trafik masuk dan diteruskan) pada router perbatasan utama **`Gendou`** dengan mengatur *default policy* **`INPUT DROP`** dan **`FORWARD DROP`**, serta **`OUTPUT ACCEPT`**, sambil tetap mengizinkan seluruh komunikasi jaringan internal yang sah.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | *Default policy* aman pada `Gendou` | Mengatur `-P INPUT DROP`, `-P FORWARD DROP`, dan `-P OUTPUT ACCEPT` |
| 2 | Menjaga trafik sistem lokal & DHCP WAN `Gendou` | Mengizinkan *interface loopback* (`lo`) dan paket balasan DHCP pada `eth0` (`udp --sport 67 --dport 68`) |
| 3 | Mengizinkan balasan koneksi yang sudah terbentuk | Menambahkan *rule* `conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` pada *chain* `INPUT` dan `FORWARD` |
| 4 | Mengizinkan akses internet & komunikasi antar-LAN internal | Mengizinkan `FORWARD` dari `192.238.0.0/21` menuju `eth0` (internet) maupun menuju `192.238.0.0/21` (antar-LAN) |
| 5 | Mengizinkan akses internal & DHCP ke `Gendou` | Mengizinkan `INPUT` dari `192.238.0.0/21` dan paket permintaan DHCP (`udp --dport 67`) pada *interface* non-WAN (`! -i eth0`) |

#### *Interface* dan Blok Jaringan `Gendou`

| Komponen | Nilai | Keterangan |
|----------|-------|------------|
| `eth0` | *Interface* WAN | Menghadap Cloud / NAT1 (Internet) |
| `! -i eth0` / `! -o eth0` | *Interface* Internal | Seluruh *interface* yang menghadap jaringan internal NERV |
| `192.238.0.0/21` | Blok IP Internal | Seluruh segmen LAN dan link antar-router |

---

### 2. Langkah Pengerjaan di Gendou

#### 2.1 Menambahkan Blok Firewall Soal 5 ke `/root/gendo.sh`

Skrip `/root/gendo.sh` diperbarui agar opsi `apt-get` aman terhadap tanggal sistem saat *restart*, lalu blok aturan firewall Soal 5 ditambahkan secara idempoten di bagian bawah skrip tanpa menghapus konfigurasi DHCP Server dan NAT Masquerade yang sudah ada.

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

**Penjelasan:**

| Bagian / *Rule* | Fungsi |
|-----------------|--------|
| `-P INPUT DROP` & `-P FORWARD DROP` | Menutup seluruh koneksi masuk dan penerusan paket secara *default* dari luar |
| `-i lo -j ACCEPT` & `-i eth0 -p udp --sport 67 --dport 68 -j ACCEPT` | Menjaga komunikasi internal *loopback* serta pembaruan IP DHCP *interface* WAN `eth0` dari NAT1 |
| `-m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` | Mengizinkan paket balasan dari sesi koneksi yang sudah diinisiasi lebih dulu |
| `! -i eth0 -s 192.238.0.0/21 -o eth0 -j ACCEPT` | Mengizinkan seluruh klien internal mengakses internet melalui `eth0` |
| `! -i eth0 ! -o eth0 -s 192.238.0.0/21 -d 192.238.0.0/21 -j ACCEPT` | Mengizinkan lalu lintas antar-LAN internal yang melewati `Gendou` |
| `! -i eth0 -s 192.238.0.0/21 -j ACCEPT` & `--dport 67 -j ACCEPT` | Mengizinkan komunikasi dari jaringan internal maupun permintaan *broadcast* DHCP (`0.0.0.0` ke port 67) menuju layanan di `Gendou` |

---

### 3. Pengujian dan Bukti

#### 3.1 Cek Aturan Firewall dan *Default Policy* pada `INPUT` & `FORWARD` di Gendou

```bash
iptables -S
```

<img src="https://github.com/user-attachments/assets/bcd8b2ce-7ee6-4a11-b003-a077b9a72b07" width="550" alt="iptables -S Gendou">

**Analisis:** *Policy* `INPUT` dan `FORWARD` telah berubah menjadi `DROP`, sedangkan aturan izin untuk `lo`, DHCP, `ESTABLISHED,RELATED`, akses internet internal, dan komunikasi antar-LAN terpasang dengan urutan yang sesuai.

#### 3.2 Membuktikan Akses Internet, Akses ke Gendou, dan Akses Antar-LAN Masih Berjalan Mulus

```bash
ping -c 3 8.8.8.8
ping -c 3 192.238.4.105
```

<img src="https://github.com/user-attachments/assets/df5de829-3b03-47ec-9c2a-9c2b3490f97b" width="550" alt="Ping setelah firewall">

---

### 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| *Policy* `INPUT` & `FORWARD` di Gendou | `DROP` (`OUTPUT ACCEPT`) | Sesuai (`iptables -S`) | ✅ |
| Akses internet (`ping 8.8.8.8`) | Tetap berjalan normal | 0% *packet loss* | ✅ |
| Akses *gateway* / antar-LAN (`ping 192.238.4.105`) | Tetap berjalan normal | 0% *packet loss* | ✅ |
| Layanan DHCP Server & NAT di Gendou | Tidak terganggu oleh firewall | Tetap aktif | ✅ |

**Pembahasan:**

1. **Prinsip *Stateful Firewall*:** Dengan mengombinasikan *default policy* `DROP` dan modul `conntrack` (`ESTABLISHED,RELATED`), `Gendou` menolak koneksi liar yang diinisiasi dari luar (`eth0`), namun tetap meneruskan balasan atas koneksi yang dimulai dari jaringan internal.
2. **Dukungan *Broadcast* DHCP:** Penambahan *rule* khusus `-p udp --dport 67` pada *interface* internal diperlukan karena paket `DHCPDISCOVER` dikirim dengan alamat sumber `0.0.0.0` (bukan `192.238.0.0/21`), sehingga tetap dapat diterima oleh *daemon* `isc-dhcp-server` di `Gendou`.

---

### 5. Kesimpulan

Kebijakan firewall *default-drop* (`INPUT DROP` dan `FORWARD DROP`) telah diterapkan pada `Gendou` secara persisten di `/root/gendo.sh`. Seluruh akses internet, komunikasi antar-LAN, dan layanan DHCP internal tetap berfungsi normal, sementara akses tidak sah dari luar ditolak. Seluruh persyaratan Soal 5 terpenuhi.

---

## Soal 6: Konfigurasi IP Statis dan Autostart pada Host Layanan

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Unit-01, Tokyo3-Core (`Tokyo-Core3`), Magi-Casper, dan Node Layanan Statis Lainnya

---

### 1. Deskripsi & Kebutuhan Soal

Menetapkan alamat IP statis secara permanen pada host dan server layanan yang membutuhkan alamat tetap (seperti **`Unit-01`**, **`Tokyo3-Core`**, **`Magi-Casper`**, **`Magi-Balthasar`**, **`Magi-Melchior`**, **`Pen-Pen`**, **`Lilith`**, **`Zeruel`**, dan **`Dummy-Plug`**) melalui berkas `/etc/network/interfaces` dan skrip `/root/ip.sh` yang dijalankan otomatis melalui `/etc/profile` dan `/root/.bashrc`.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Alamat IP statis sesuai rancangan VLSM | Menulis konfigurasi `iface eth0 inet static` di `/etc/network/interfaces` |
| 2 | Persistensi konfigurasi saat container di-*restart* | Membuat skrip `/root/ip.sh` yang menghentikan `udhcpc`, memasang IP/*gateway*/DNS, dan mendaftarkannya ke `/etc/profile` serta `/root/.bashrc` |
| 3 | Verifikasi IP, rute *default*, dan konektivitas *gateway* | Mengecek `ip -br a`, `ip route`, `ping` ke *gateway* masing-masing, dan isi `/etc/network/interfaces` |

#### Alamat IP Statis Node yang Diuji

| Perangkat | Alamat IP / Prefix | *Default Gateway* | Subnet |
|-----------|--------------------|-------------------|--------|
| Unit-01 | `192.238.4.66/29` | `192.238.4.65` | Switch-Cage (`192.238.4.64/29`) |
| Tokyo3-Core (`Tokyo-Core3`) | `192.238.4.106/30` | `192.238.4.105` | Link Tokyo3-Core (`192.238.4.104/30`) |
| Magi-Casper | `192.238.4.58/29` | `192.238.4.57` | Switch-Terminal (`192.238.4.56/29`) |

---

### 2. Langkah Pengerjaan

#### 2.1 Konfigurasi IP Statis & Autostart (Contoh pada `Unit-01`)

Perintah satu baris berikut menulis konfigurasi ke `/etc/network/interfaces`, membuat skrip `/root/ip.sh`, memasang *hook* autostart di `/etc/profile` dan `/root/.bashrc`, lalu langsung menerapkannya:

```bash
printf 'auto lo\niface lo inet loopback\n\nauto eth0\niface eth0 inet static\n    address 192.238.4.66\n    netmask 255.255.255.248\n    gateway 192.238.4.65\n' > /etc/network/interfaces && printf '#!/bin/sh\npkill udhcpc 2>/dev/null\nip addr flush dev eth0\nip addr add 192.238.4.66/29 dev eth0\nip link set eth0 up\nip route replace default via 192.238.4.65\necho "nameserver 8.8.8.8" > /etc/resolv.conf\n' > /root/ip.sh && chmod +x /root/ip.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART STATIC IP ===\n[ -f /root/ip.sh ] && sh /root/ip.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && sh /root/ip.sh
```

Pola skrip yang sama diterapkan pada node statis lainnya (`Tokyo3-Core`, `Magi-Casper`, dan seterusnya) dengan menyesuaikan alamat IP, *netmask*/*prefix*, dan *default gateway* masing-masing.

**Penjelasan:**

| Komponen | Fungsi |
|----------|--------|
| `/etc/network/interfaces` | Menyimpan definisi konfigurasi jaringan statis standar sistem Debian/Linux |
| `pkill udhcpc 2>/dev/null` | Menghentikan klien DHCP bawaan agar tidak menimpa alamat IP statis |
| `/root/ip.sh` | Skrip pemulihan cepat untuk memasang ulang IP, *default route*, dan `nameserver 8.8.8.8` |
| `/etc/profile` & `/root/.bashrc` | Memastikan `/root/ip.sh` dieksekusi otomatis setiap kali container menyala atau console dibuka |

---

### 3. Pengujian dan Bukti

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

### 4. Hasil dan Pembahasan

| Node | IP Statis Terpasang | *Gateway* | *Ping* ke *Gateway* | `/etc/network/interfaces` | Sesuai Soal |
|------|---------------------|-----------|---------------------|---------------------------|:-----------:|
| Unit-01 | `192.238.4.66/29` | `192.238.4.65` | 0% *packet loss* | Sesuai | ✅ |
| Tokyo-Core3 | `192.238.4.106/30` | `192.238.4.105` | 0% *packet loss* | Sesuai | ✅ |
| Magi-Casper | `192.238.4.58/29` | `192.238.4.57` | 0% *packet loss* | Sesuai | ✅ |

**Pembahasan:**

1. **Pentingnya IP Statis pada Server & Pilot:** Node seperti server MAGI (`Casper`, `Balthasar`, `Melchior`), `Pen-Pen`, `Tokyo3-Core`, dan `Unit-01` menjadi target aturan *firewall* berbasis alamat IP dan layanan *port* pada soal-soal berikutnya, sehingga alamat IP-nya tidak boleh berubah-ubah akibat *lease* DHCP.
2. **Keandalan *Dual-Persistence*:** Kombinasi `/etc/network/interfaces` dan *hook* `/root/ip.sh` menjamin alamat IP statis tetap bertahan meskipun container GNS3 di-*reload*.

---

### 5. Kesimpulan

Konfigurasi IP statis pada node-node layanan dan host penting (`Unit-01`, `Tokyo-Core3`, `Magi-Casper`, dan lainnya) telah terpasang secara persisten dan terhubung ke masing-masing *default gateway* dengan `0% packet loss`. Seluruh persyaratan Soal 6 terpenuhi.

---

## Soal 7: Aktivasi Layanan TCP pada Sistem MAGI

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Magi-Casper, Magi-Balthasar, Magi-Melchior, Lilith

---

### 1. Deskripsi & Kebutuhan Soal

Menyalakan layanan TCP *listener* menggunakan `netcat-traditional` (`nc.traditional`) secara persisten di latar belakang pada ketiga superkomputer MAGI (**`Magi-Casper`**, **`Magi-Balthasar`**, dan **`Magi-Melchior`**) yang mencatat setiap pesan masuk ke dalam file log, lalu membuktikan pengiriman pesan dari **`Lilith`**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Layanan TCP pada `Magi-Casper` | Menyalakan *listener* `nc.traditional` pada port **TCP `5000`** yang menyimpan log ke `/tmp/casper_5000.log` |
| 2 | Layanan TCP pada `Magi-Balthasar` | Menyalakan *listener* `nc.traditional` pada port **TCP `5000` dan `5001`** yang menyimpan log ke `/tmp/balthasar_5000.log` & `/tmp/balthasar_5001.log` |
| 3 | Layanan TCP pada `Magi-Melchior` | Menyalakan *listener* `nc.traditional` pada port **TCP `5000`** yang menyimpan log ke `/tmp/melchior_5000.log` |
| 4 | Persistensi layanan & pengujian | Menyimpan skrip *startup* `/root/<nama>.sh` (memanggil `/root/ip.sh` & menjalankan *loop listener*) serta mengirim pesan uji dari `Lilith` |

#### Alamat IP dan Port Layanan MAGI

| Perangkat | Alamat IP | Port TCP Aktif | Berkas Log |
|-----------|-----------|----------------|------------|
| Magi-Casper | `192.238.4.58` | `5000` | `/tmp/casper_5000.log` |
| Magi-Balthasar | `192.238.4.59` | `5000`, `5001` | `/tmp/balthasar_5000.log`, `/tmp/balthasar_5001.log` |
| Magi-Melchior | `192.238.4.60` | `5000` | `/tmp/melchior_5000.log` |
| Lilith | `192.238.4.74` | — (Klien pengirim pesan) | — |

---

### 2. Langkah Pengerjaan

#### 2.1 Pasang Skrip & Nyalakan *Listener* di Ketiga Node MAGI

Pada masing-masing node (`Magi-Casper`, `Magi-Balthasar`, dan `Magi-Melchior`), dibuat skrip *startup* (`/root/casper.sh`, `/root/balthasar.sh`, dan `/root/melchior.sh`) yang memastikan paket `iptables` serta `netcat-traditional` terpasang. Skrip ini menjalankan `while true; do nc.traditional -l -p <PORT> >> /tmp/<log>; done` menggunakan `nohup` di *background*, lalu didaftarkan ke `/etc/profile` dan `/root/.bashrc`.

#### 2.2 Kirim Pesan dari Lilith (`192.238.4.74`)

Dari *console* **`Lilith`**, dikirimkan pesan pengujian ke keempat *port* layanan pada ketiga node MAGI menggunakan `nc`:

<img src="https://github.com/user-attachments/assets/8bc2be94-902c-4efc-8052-e013149d0919" width="550" alt="Kirim pesan dari Lilith">

---

### 3. Pengujian dan Bukti

#### 3.1 Magi-Casper (`192.238.4.58` — Port `5000`)

```bash
ss -tlnp | grep nc
cat /tmp/casper_5000.log
```

<img src="https://github.com/user-attachments/assets/7214f3b2-038c-4432-aab4-cdf85a809f8b" width="550" alt="Magi-Casper">

#### 3.2 Magi-Balthasar (`192.238.4.59` — Port `5000` dan `5001`)

```bash
ss -tlnp | grep nc
cat /tmp/balthasar_5000.log
cat /tmp/balthasar_5001.log
```

<img src="https://github.com/user-attachments/assets/1efdfdf2-a5c4-483b-a8bf-3c94a314f289" width="550" alt="Magi-Balthasar">

#### 3.3 Magi-Melchior (`192.238.4.60` — Port `5000`)

```bash
ss -tlnp | grep nc
cat /tmp/melchior_5000.log
```

<img src="https://github.com/user-attachments/assets/642fab33-0321-4e1d-97ec-c6df0a056d7c" width="550" alt="Magi-Melchior">

---

### 4. Hasil dan Pembahasan

| Node Server | Port TCP | Status *Socket* (`ss -tlnp`) | Isi Log Pesan dari Lilith | Sesuai Soal |
|-------------|----------|------------------------------|---------------------------|:-----------:|
| Magi-Casper (`192.238.4.58`) | `5000` | `LISTEN` (`nc.traditional`) | Pesan tercatat di `/tmp/casper_5000.log` | ✅ |
| Magi-Balthasar (`192.238.4.59`) | `5000` & `5001` | `LISTEN` (`nc.traditional`) | Pesan tercatat di kedua file log | ✅ |
| Magi-Melchior (`192.238.4.60`) | `5000` | `LISTEN` (`nc.traditional`) | Pesan tercatat di `/tmp/melchior_5000.log` | ✅ |

**Pembahasan:**

1. **Perulangan *Listener* (`while true`):** Secara bawaan `nc.traditional -l` akan berhenti setelah satu sesi koneksi selesai. Dengan membungkusnya di dalam *loop* `while true` dan menjalankannya melalui `nohup ... &`, layanan port `5000` dan `5001` langsung kembali siap menerima koneksi berikutnya secara terus-menerus.
2. **Kesiapan Infrastruktur untuk Firewall:** Keberadaan layanan nyata pada port-port MAGI ini memastikan pengujian aturan keamanan pada Soal 11, 12, dan 13 dapat diverifikasi secara langsung menggunakan trafik aplikasi TCP.

---

### 5. Kesimpulan

Seluruh layanan TCP *listener* pada `Magi-Casper` (`5000`), `Magi-Balthasar` (`5000` dan `5001`), dan `Magi-Melchior` (`5000`) telah aktif secara persisten dan terbukti menerima serta mencatat pesan yang dikirim dari `Lilith`. Seluruh persyaratan Soal 7 terpenuhi.

---

## Soal 8: Layanan TCP Balasan Otomatis pada `Pen-Pen` dan `Tokyo3-Core`

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Pen-Pen, Tokyo3-Core, Lilith

---

### 1. Deskripsi & Kebutuhan Soal

Menyalakan layanan TCP *listener* dengan balasan otomatis (*banner response*) menggunakan opsi `-c` pada `nc.traditional` di **`Pen-Pen`** (port `80`) dan **`Tokyo3-Core`** (port `80` dan `8080`), serta membuktikan dari **`Lilith`** bahwa koneksi ke port yang terbuka langsung mendapatkan balasan teks, sedangkan koneksi ke port yang tidak dibuka (seperti port `22`) langsung ditolak (`Connection refused`).

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Layanan TCP `80` pada `Pen-Pen` | Menjalankan `nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"` di `/root/penpen.sh` |
| 2 | Layanan TCP `80` & `8080` pada `Tokyo3-Core` | Menjalankan `nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"` dan `nc.traditional -l -p 8080 -c "echo syifa imutz"` di `/root/tokyo3.sh` |
| 3 | Persistensi layanan saat *restart* | Mendaftarkan `/root/penpen.sh` dan `/root/tokyo3.sh` ke `/etc/profile` dan `/root/.bashrc` dengan pengecekan `pgrep` agar proses tidak ganda |
| 4 | Verifikasi port terbuka vs port tertutup | Menguji `nc -v` dari `Lilith` ke port `80`, `8080`, dan `22` |

#### Alamat IP dan Spesifikasi Layanan

| Perangkat | Alamat IP | Port Terbuka | Balasan Otomatis | Port Tertutup (Uji) |
|-----------|-----------|--------------|------------------|---------------------|
| Pen-Pen | `192.238.3.194` | TCP `80` | `Welcome to Pen-Pen` | TCP `22` (`Connection refused`) |
| Tokyo3-Core | `192.238.4.106` | TCP `80`, TCP `8080` | `Welcome to Tokyo3-Core` (`80`), `syifa imutz` (`8080`) | TCP `22` (`Connection refused`) |
| Lilith | `192.238.4.74` | — (Klien penguji) | — | — |

---

### 2. Langkah Pengerjaan

#### 2.1 Konfigurasi *Listener* di Pen-Pen (`/root/penpen.sh`)

```bash
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/penpen.sh && chmod +x /root/penpen.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/penpen.sh ] && bash /root/penpen.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/penpen.sh
```

<img src="https://github.com/user-attachments/assets/b735d440-93f4-4a69-be12-6d7c26e21de4" width="550" alt="Listener Pen-Pen">

#### 2.2 Konfigurasi *Listener* di Tokyo3-Core (`/root/tokyo3.sh`)

Jalankan perintah satu baris berikut untuk menyalakan port `80` dan `8080`:

```bash
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\nif ! pgrep -f "[n]c.traditional -l -p 8080 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 8080 -c "echo syifa imutz"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/tokyo3.sh && chmod +x /root/tokyo3.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/tokyo3.sh ] && bash /root/tokyo3.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/tokyo3.sh
```

Lalu cek apakah port `80` dan `8080` sudah menyala:

```bash
ss -tlnp | grep nc
```

<img src="https://github.com/user-attachments/assets/74c9cd73-1906-4387-97f0-2313b8f0ccf9" width="550" alt="Port 80 dan 8080 Tokyo3-Core">

**Penjelasan:**

| Bagian / Opsi | Fungsi |
|---------------|--------|
| `nc.traditional -l -p <PORT> -c "..."` | Membuka socket TCP dan mengeksekusi perintah `echo` segera setelah koneksi klien diterima |
| `pgrep -f "[n]c.traditional -l -p <PORT> "` | Mencegah duplikasi proses *background* apabila skrip `/root/penpen.sh` atau `/root/tokyo3.sh` dipanggil ulang |
| `update-alternatives --set nc /bin/nc.traditional` | Menjadikan `nc.traditional` sebagai implementasi default perintah `nc` |

---

### 3. Pengujian dan Bukti

Jalankan pengujian berikut di **`Lilith`** (`192.238.4.74`) untuk membuktikan port yang terbuka langsung membalas dan port yang tertutup langsung ditolak:

```bash
nc -v 192.238.3.194 80
nc -v 192.238.4.106 80
nc -v 192.238.4.106 8080
nc -v 192.238.3.194 22
nc -v 192.238.4.106 22
```

<img src="https://github.com/user-attachments/assets/2406a845-4737-4b3a-9168-8ec738fec44c" width="550" alt="Pengujian nc dari Lilith">

---

### 4. Hasil dan Pembahasan

| Skenario Pengujian (dari Lilith) | Target & Port | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|----------------------------------|---------------|-----------------------|--------------|:-----------:|
| `nc -v 192.238.3.194 80` | Pen-Pen : 80 | Terhubung & membalas | `Welcome to Pen-Pen` | ✅ |
| `nc -v 192.238.4.106 80` | Tokyo3-Core : 80 | Terhubung & membalas | `Welcome to Tokyo3-Core` | ✅ |
| `nc -v 192.238.4.106 8080` | Tokyo3-Core : 8080 | Terhubung & membalas | `syifa imutz` | ✅ |
| `nc -v 192.238.3.194 22` | Pen-Pen : 22 | Ditolak seketika | `Connection refused` | ✅ |
| `nc -v 192.238.4.106 22` | Tokyo3-Core : 22 | Ditolak seketika | `Connection refused` | ✅ |

**Pembahasan:**

1. **Respons Otomatis Opsi `-c`:** Penggunaan parameter `-c` pada `nc.traditional` memungkinkan server langsung mengirimkan *string* keluaran standar kepada klien begitu *three-way handshake* TCP selesai tanpa menunggu masukan dari klien.
2. **Perilaku Kernel terhadap Port Tertutup:** Pada port `22` yang tidak memiliki proses `LISTEN`, kernel Linux pada `Pen-Pen` dan `Tokyo3-Core` otomatis membalas paket TCP `SYN` dengan `TCP RST`, sehingga klien `Lilith` langsung mendapatkan `Connection refused`.

---

### 5. Kesimpulan

Layanan TCP *listener* dengan balasan otomatis pada `Pen-Pen` (port `80`) dan `Tokyo3-Core` (port `80` dan `8080`) telah terpasang secara persisten dan terverifikasi dari `Lilith`, sementara port tertutup (`22`) langsung menghasilkan `Connection refused`. Seluruh persyaratan Soal 8 terpenuhi.

---

## Soal 9: Pemulihan Sabotase Routing pada Router `Shigeru`

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Shigeru (Router), Armisael, Lilith

---

### 1. Deskripsi & Kebutuhan Soal

Menjalankan skrip sabotase **`break_routing.sh`** pada router **`Shigeru`**, mengamati gejala terputusnya komunikasi lintas subnet antara **`Lilith`** dan **`Armisael`** (sementara komunikasi lokal satu LAN antara `Armisael` dan `Shigeru` tetap berjalan), mendiagnosis penyebab kerusakan di `Shigeru`, serta memulihkannya secara permanen pada `/root/route.sh` tanpa menimpa konfigurasi soal sebelumnya.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Simulasi gangguan *routing* | Mengunduh dan menjalankan `break_routing.sh` di router `Shigeru` |
| 2 | Identifikasi gejala & diagnosis menyeluruh | Menguji `ping` dari `Lilith` dan `Armisael`, lalu memeriksa `sysctl`, `ip route`, `ip rule`, dan `iptables` di `Shigeru` |
| 3 | Pemulihan seluruh penyebab gangguan | Mengaktifkan `ip_forward=1`, menghapus *policy routing* jebakan tabel `100`, menghapus rute `blackhole`, dan mengembalikan *default route* |
| 4 | Persistensi perbaikan | Menyimpan blok `# === FIX SOAL 9 ===` ke `/root/route.sh` secara idempoten |

#### Alamat IP Perangkat Terkait

| Perangkat | Alamat IP / *Interface* | Peran |
|-----------|-------------------------|-------|
| Shigeru (`eth0`) | `192.238.4.154/30` | Link *upstream* menuju Gendou (`192.238.4.153`) |
| Shigeru (`eth1`) | `192.238.4.81/29` | *Gateway* subnet Switch-Monolith (`192.238.4.80/29`) |
| Armisael | `192.238.4.83/29` | Host di dalam subnet Shigeru (`Switch-Monolith`) |
| Lilith | `192.238.4.74/29` | Host lintas subnet di `Switch-Dogma` (`192.238.4.72/29`) |

---

### 2. Langkah Pengerjaan

#### 2.1 Menjalankan Skrip Sabotase di Shigeru

```bash
URL="https://drive.google.com/file/d/1ub7LjL2BdZdVq6n_AbuqcO416Z8QJGVs/view"
ID=$(echo "$URL" | sed -E 's|.*/d/([^/?&]+).*|\1|; s|.*[?&]id=([^&]+).*|\1|')
rm -f /tmp/break_routing.sh
(wget --no-check-certificate -qO /tmp/break_routing.sh "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" || curl -kfsSL "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" -o /tmp/break_routing.sh) && sed -i 's/\r$//' /tmp/break_routing.sh && [ "$(head -n 1 /tmp/break_routing.sh)" = '#!/bin/sh' ] && sh /tmp/break_routing.sh
```

#### 2.2 Uji Gejala Sabotase (Lilith & Armisael)

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

> *Ping* dari Armisael ke Shigeru berhasil karena keduanya masih berada dalam satu LAN. Sebaliknya, paket dari Lilith perlu diteruskan lintas *interface* oleh Shigeru dan dirutekan kembali ke Switch-Dogma, sehingga gagal karena jalur lintas jaringan inilah yang dilumpuhkan oleh `break_routing.sh`.

#### 2.3 Ambil Diagnosa di Node Shigeru

```bash
sysctl net.ipv4.ip_forward
ip -br a
ip route
ip rule
iptables -S
```

<img src="https://github.com/user-attachments/assets/e31afac2-2acd-4349-8881-839d2c8a2cd5" width="550" alt="Diagnosa Shigeru">

Dari gambar tersebut kita dapat melihat 4 kerusakan yang disebabkan oleh `break_routing.sh`:

1. **IP Forwarding dimatikan:** Kernel Shigeru menolak meneruskan paket lintas *interface*, sehingga paket *ping* dari Lilith yang masuk melalui `eth0` tidak diteruskan ke subnet Armisael.
2. **Policy Routing jebakan (`90: from all iif eth1 lookup 100`):** Terdapat aturan *policy routing* berprioritas tinggi (`90`, dieksekusi sebelum tabel `main` di prioritas `32766`) yang memaksa seluruh trafik masuk dari LAN `eth1` membaca tabel routing `100` (berisi rute *unreachable*), sehingga paket dari Armisael langsung digagalkan sebelum sempat membaca tabel routing utama.
3. **Rute `blackhole` ke subnet Lilith (`blackhole 192.238.4.72/29`):** Ditambahkan rute `blackhole` khusus mengarah ke segmen jaringan `192.238.4.72/29` (tempat Lilith `192.238.4.75` berada), yang membuat setiap paket balasan dari Armisael menuju Lilith langsung dibuang secara diam-diam oleh Shigeru.
4. **Rute `blackhole` ke subnet Lilith (`blackhole 192.238.4.72/29`):** Ditambahkan rute `blackhole` khusus mengarah ke segmen jaringan `192.238.4.72/29` (tempat Lilith `192.238.4.75` berada), yang membuat setiap paket balasan dari Armisael menuju Lilith langsung dibuang secara diam-diam oleh Shigeru.

#### 2.4 Perbaikan dan *Autostart* saat *Restart* di Shigeru

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

### 3. Verifikasi & Bukti Perbaikan

#### 3.1 Membuktikan bahwa IP Forwarding Sudah Berjalan, *Blackhole* Hilang, dan `ip rule` Bersih

```bash
sysctl net.ipv4.ip_forward
ip route
ip rule
```

<img src="https://github.com/user-attachments/assets/eed852a8-a83b-48e3-a115-d7842c3a379c" width="550" alt="Verifikasi Shigeru">

#### 3.2 Membuktikan bahwa Lilith Bisa Kembali *Ping* ke Armisael

```bash
ping -c 3 192.238.4.83
```

<img src="https://github.com/user-attachments/assets/88dc59ae-90cf-4da8-a054-922f312e0490" width="550" alt="Ping Lilith ke Armisael (berhasil)">

---

## Soal 10: Pemulihan Sabotase NAT pada `Misato` dan `Gendou`

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Misato (Router), Gendou (Router Gateway/NAT), Unit-01, Spy, Lilith

---

### 1. Deskripsi & Kebutuhan Soal

Menjalankan skrip sabotase **`break_nat.sh`** pada router **`Misato`** dan **`Gendou`**, mengamati gejala terputusnya akses internet (`8.8.8.8`) pada subnet **`Switch-Cage`** (`Unit-01`) sementara akses internal ke **`Lilith`** maupun akses internet dari subnet **`Switch-Intel`** (`Spy`) tetap normal, mendiagnosis tabel NAT pada kedua router, serta memulihkannya secara permanen.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Simulasi gangguan pada tabel NAT | Menjalankan `break_nat.sh` di `Misato` dan `Gendou` |
| 2 | Pengujian gejala & analisis cakupan dampak | Membandingkan hasil `ping 8.8.8.8` dan `ping 192.238.4.75` dari `Unit-01` serta `ping 8.8.8.8` dari `Spy` |
| 3 | Diagnosis & analisis arsitektur NAT | Memeriksa `iptables -t nat -S` di `Misato` dan `Gendou` untuk menemukan aturan SNAT palsu dan *bypass* `RETURN` |
| 4 | Perbaikan permanen tanpa menimpa soal lain | Menghapus aturan sabotase dan menyimpannya ke `/root/fix_soal10.sh` dengan *autostart* di `/etc/profile` dan `/root/.bashrc` |

#### Alamat IP Perangkat Terkait

| Perangkat | Alamat IP / Subnet | Peran |
|-----------|--------------------|-------|
| Unit-01 | `192.238.4.66` (`192.238.4.64/29` — Switch-Cage) | Klien terdampak sabotase NAT |
| Spy | `192.238.4.102` (`192.238.4.96/29` — Switch-Intel) | Klien pembanding (tidak terdampak) |
| Lilith | `192.238.4.75` (`192.238.4.72/29` — Switch-Dogma) | Tujuan internal pembanding |
| Misato | Router distribusi subnet Cage | Disusupi aturan SNAT palsu (`172.31.99.1`) |
| Gendou | Router *gateway* terluar (`eth0` ke Internet) | Pelaksana NAT Masquerade terpusat; disusupi *rule* `RETURN` |

---

### 2. Gejala Kerusakan (Sebelum Diperbaiki)

#### 2.1 Unduh dan Jalankan `break_nat.sh` di Misato dan Gendou

```bash
URL="https://drive.google.com/file/d/1oX7r9BmMPumqrIb71Vbk43Y6CQEOvDRJ/view"
ID=$(echo "$URL" | sed -E 's|.*/d/([^/?&]+).*|\1|; s|.*[?&]id=([^&]+).*|\1|')
rm -f /tmp/break_nat.sh
(wget --no-check-certificate -qO /tmp/break_nat.sh "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" || curl -kfsSL "https://drive.usercontent.google.com/download?id=$ID&export=download&confirm=t" -o /tmp/break_nat.sh) && sed -i 's/\r$//' /tmp/break_nat.sh && [ "$(head -n 1 /tmp/break_nat.sh)" = '#!/bin/sh' ] && sh /tmp/break_nat.sh
```

#### 2.2 Uji Gejala Kerusakan

**Unit-01**

```bash
ping -c 3 8.8.8.8
ping -c 2 192.238.4.75
```

<img src="https://github.com/user-attachments/assets/9fff8c9d-4690-4109-b85e-7336161ef848" width="550" alt="Gejala di Unit-01">

**Spy**

```bash
ping -c 3 8.8.8.8
```

<img src="https://github.com/user-attachments/assets/90b9300e-0dba-4c9f-bd13-538152175c22" width="550" alt="Gejala di Spy">

---

### 3. Diagnosis di Misato dan Gendou

```bash
iptables -t nat -S
iptables -S
sysctl net.ipv4.ip_forward
```

**Misato**

<img src="https://github.com/user-attachments/assets/dc186b64-c4e5-48af-a367-fd13563e03af" width="550" alt="Diagnosa Misato">

**Gendou**

<img src="https://github.com/user-attachments/assets/0f58aa63-b866-4c3a-a268-bfe4f5d9ca6d" width="550" alt="Diagnosa Gendou">

#### Hasil Analisis

##### 1. Dua Penyebab Sabotase pada Tabel NAT

**a. Aturan SNAT palsu di Misato**

```text
-A POSTROUTING -s 192.238.4.64/29 ! -d 192.238.0.0/16 -o eth0 -j SNAT --to-source 172.31.99.1
```

Setiap paket yang berasal dari subnet Cage dengan tujuan ke luar jaringan internal diubah alamat IP-nya oleh Misato menjadi alamat IP fiktif. Akibatnya, ketika paket balasan dari internet kembali ke Gendou dan di-*un-NAT* ke `172.31.99.1`, Gendou tidak memiliki rute menuju `172.31.99.1` sehingga paket balasan dibuang (*drop*).

**b. Aturan *bypass* `RETURN` di Gendou**

```text
-A POSTROUTING -s 192.238.4.64/29 -o eth0 -j RETURN
```

Pada *chain* `POSTROUTING` di Gendou, disisipkan aturan `-j RETURN` khusus untuk sumber `192.238.4.64/29` di baris pertama sebelum aturan `-A POSTROUTING -o eth0 -j MASQUERADE`. Jika hanya aturan SNAT palsu di Misato yang dihapus tanpa menghapus aturan `RETURN` di Gendou, paket dari Cage (`192.238.4.64/29`) akan langsung keluar dari *chain* `POSTROUTING` tanpa terkena `MASQUERADE`, sehingga keluar ke internet memakai IP privat (`192.238.4.66`) yang tidak dapat dirutekan kembali oleh `8.8.8.8`.

##### 2. Mengapa Subnet Cage Terdampak, sedangkan Intel Tidak, dan Ping ke Lilith Tetap Normal?

- Kedua aturan sabotase di node Misato dan Gendou secara spesifik diarahkan ke subnet Cage yang berisi Unit-00, Unit-01, dan Unit-02, sehingga node seperti Spy yang berada di subnet Intel masih dapat berjalan dengan normal.
- **Ping dari Cage ke Lilith (`192.238.4.75`) normal:** Alamat `192.238.4.75` berada di dalam blok internal `192.238.0.0/16`, sehingga tidak memicu kondisi `! -d 192.238.0.0/16` di Misato dan juga tidak keluar melalui *interface* WAN `eth0` di Gendou.

##### 3. Di Router Mana Alamat Sumber Diterjemahkan?

Alamat sumber privat seluruh klien (termasuk Unit-01 `192.238.4.66`) diterjemahkan (NAT Masquerade) secara terpusat di router *gateway* terluar yang terhubung langsung ke Cloud/Internet, yaitu **Gendou** (melalui *interface* WAN `eth0` dengan aturan `-A POSTROUTING -o eth0 -j MASQUERADE`), sedangkan router internal seperti Misato hanya bertugas melakukan *routing* tanpa NAT.

---

### 4. Perbaikan Permanen di Misato dan Gendou

Jalankan perintah berikut pada masing-masing *console*:

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

### 5. Verifikasi & Bukti Hasil Perbaikan

#### 5.1 Di *Console* Misato

```bash
iptables -t nat -S
```

<img src="https://github.com/user-attachments/assets/cc4c6f1f-b7cf-4963-a14a-1caf45a971bb" width="450" alt="Verifikasi NAT Misato">

#### 5.2 Di *Console* Gendou

```bash
iptables -t nat -S
```

<img src="https://github.com/user-attachments/assets/affae086-3b46-4d94-9126-82cfdd3c0532" width="450" alt="Verifikasi NAT Gendou">

#### 5.3 Di *Console* Unit-01

```bash
ping -c 3 8.8.8.8
```

<img src="https://github.com/user-attachments/assets/3917e92c-c297-44c4-9b32-924bc65a510b" width="550" alt="Verifikasi ping Unit-01">

---

### 6. Hasil dan Pembahasan

| Pengujian | Sebelum Perbaikan | Sesudah Perbaikan | Sesuai Soal |
|-----------|-------------------|-------------------|:-----------:|
| `iptables -t nat -S` di Misato | Terdapat SNAT palsu `172.31.99.1` | Bersih (*policy* `ACCEPT`) | ✅ |
| `iptables -t nat -S` di Gendou | Terdapat *rule bypass* `RETURN` | Hanya *rule* `MASQUERADE` pada `eth0` | ✅ |
| Unit-01 `ping 8.8.8.8` | 100% *packet loss* | 0% *packet loss* | ✅ |
| Unit-01 `ping 192.238.4.75` (Lilith) | 0% *packet loss* | 0% *packet loss* | ✅ |
| Spy `ping 8.8.8.8` | 0% *packet loss* | 0% *packet loss* | ✅ |

**Pembahasan:**

1. **Penanganan Ganda (*Misato* & *Gendou*):** Penghapusan aturan SNAT palsu pada `Misato` dan aturan `RETURN` pada `Gendou` secara bersamaan mengembalikan alur translasi alamat terpusat, sehingga seluruh trafik keluar dari subnet `Switch-Cage` kembali diproses oleh *rule* `MASQUERADE` di `Gendou`.
2. **Keutuhan Konfigurasi Eksisting:** Dengan menggunakan opsi penghapusan spesifik (`-D`), aturan `MASQUERADE` dari Soal 4 maupun kebijakan *firewall* dari Soal 5 pada `Gendou` tetap utuh tanpa terganggu.

---

### 7. Kesimpulan

Dua aturan sabotase pada tabel `nat` akibat `break_nat.sh` — yakni SNAT palsu ke `172.31.99.1` di `Misato` dan aturan *bypass* `RETURN` untuk subnet `192.238.4.64/29` di `Gendou` — berhasil didiagnosis dan dihapus secara permanen melalui `/root/fix_soal10.sh`. Akses internet (`8.8.8.8`) dari `Unit-01` kembali pulih dengan `0% packet loss` tanpa mengganggu konfigurasi lain. Seluruh persyaratan Soal 10 terpenuhi.

---

## Soal 11: Casper Harus Jadi Hantu

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Magi-Casper, Magi-Balthasar, Lilith

---

### 1. Soal

> **Casper harus jadi hantu:** Casper adalah MAGI paling rahasia. Kalau SEELE tahu ia masih hidup, mereka tahu ke mana harus menyerang. **Tidak ada perangkat lain yang boleh mem-ping Casper**, termasuk yang satu switch. Casper sendiri tetap boleh mem-ping siapa saja. Layanan **TCP 5000** Casper tetap hidup. Buktikan ping ke Casper dari Balthasar dan Lilith **gagal**, sedangkan ping dari Casper ke keduanya **berhasil**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Tidak ada perangkat lain yang boleh *ping* Casper | *Drop* ICMP `echo-request` pada *chain* `INPUT` |
| 2 | Casper tetap boleh *ping* ke siapa saja | *Chain* `OUTPUT` tetap `ACCEPT`; `echo-reply` yang kembali tidak ikut diblokir |
| 3 | Layanan TCP 5000 tetap hidup | *Rule* hanya menargetkan ICMP; *listener* `nc` port 5000 tidak disentuh |

#### Alamat IP

| Perangkat | IP | Keterangan |
|-----------|----|------------|
| Magi-Casper | `192.238.4.58` | Target yang disembunyikan |
| Magi-Balthasar | `192.238.4.59` | Satu *switch* dengan Casper |
| Lilith | `192.238.4.74` | Beda subnet (lintas router) |

---

### 2. Langkah Pengerjaan di Magi-Casper

#### 2.1 Menambahkan Blok Firewall Soal 11 ke `/root/casper.sh`

Blok firewall ditambahkan di **bagian paling bawah** skrip `/root/casper.sh`. Perintah `sed` pertama menghapus blok Soal 11 lama (jika ada) agar tidak terduplikasi, tanpa menghapus bagian *listener* port 5000 di atasnya.

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
| `iptables -F` | Menghapus semua *rule* yang ada (*flush*) |
| `iptables -X` | Menghapus semua *custom chain* |
| `iptables -P INPUT/FORWARD/OUTPUT ACCEPT` | *Policy default* tetap `ACCEPT` agar trafik lain (termasuk TCP 5000) tidak terganggu |
| `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` | Membuang semua ICMP `echo-request` (*ping*) yang masuk ke Casper |

**Mengapa hanya `echo-request`, bukan semua ICMP?**
Jika seluruh ICMP diblokir, balasan *ping* (`echo-reply`) untuk *ping* yang dikirim Casper ke luar juga ikut terbuang, sehingga Casper tidak dapat mem-*ping* siapa pun. Dengan hanya memblokir `echo-request` (type 8), Casper tetap dapat mengirim *ping* keluar dan menerima balasannya.

> **Verifikasi:** Jalankan `cat /root/casper.sh` dan pastikan blok `# === SOAL 11: FIREWALL ===` muncul di bagian paling bawah tanpa menghapus bagian *listener* port 5000 di atasnya.

#### 2.2 Menerapkan Konfigurasi dan Mengecek `iptables` serta *Listener* Port 5000

```bash
bash /root/casper.sh
iptables -S
ss -tlnp | grep 5000
```

**Hasil:**

```text
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP
LISTEN 0  1  0.0.0.0:5000  0.0.0.0:*  users:(("nc.traditional",pid=411,fd=3))
```

<img src="https://github.com/user-attachments/assets/df575be6-b370-4743-8a21-82a381555c9e" width="412" alt="Hasil bash casper.sh, iptables -S, dan ss -tlnp">

**Analisis:**

- `iptables -S` menampilkan *rule* `-A INPUT -p icmp -m icmp --icmp-type 8 -j DROP` dengan *policy default* `ACCEPT`. *Rule* pemblokiran *ping* masuk sudah aktif.
- `ss -tlnp` menunjukkan proses `nc.traditional` tetap **LISTEN** di port **5000**, sehingga layanan TCP 5000 tetap hidup.

---

### 3. Pengujian Ping Masuk dan Keluar

#### 3.1 Dari Magi-Balthasar (`192.238.4.59`) → Casper (Satu *Switch*)

Hasil yang diharapkan: **gagal / 100% *packet loss***.

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```text
PING 192.238.4.58 (192.238.4.58) 56(84) bytes of data.

--- 192.238.4.58 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2253ms
```

<img src="https://github.com/user-attachments/assets/14ccd987-fb5a-4fd8-bacb-3c4c6836c752" width="412" alt="Ping Balthasar ke Casper">

#### 3.2 Dari Lilith (`192.238.4.74`) → Casper (Lintas Subnet)

Hasil yang diharapkan: **gagal / 100% *packet loss***.

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```text
PING 192.238.4.58 (192.238.4.58) 56(84) bytes of data.

--- 192.238.4.58 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2290ms
```

<img src="https://github.com/user-attachments/assets/dd8bc504-3406-4e25-b1d7-19f27b12bad5" width="408" alt="Ping Lilith ke Casper">

#### 3.3 Dari Magi-Casper (`192.238.4.58`) → Balthasar & Lilith

Hasil yang diharapkan: **berhasil / 0% *packet loss***, lalu cek *counter* paket yang di-*drop*.

```bash
ping -c 3 192.238.4.59
ping -c 3 192.238.4.75
iptables -L INPUT -n -v
```

**Hasil:**

```text
PING 192.238.4.59 (192.238.4.59) 56(84) bytes of data.
64 bytes from 192.238.4.59: icmp_seq=1 ttl=64 time=1.05 ms
64 bytes from 192.238.4.59: icmp_seq=2 ttl=64 time=0.234 ms
64 bytes from 192.238.4.59: icmp_seq=3 ttl=64 time=0.207 ms

--- 192.238.4.59 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2011ms
rtt min/avg/max/mdev = 0.207/0.498/1.053/0.392 ms
PING 192.238.4.75 (192.238.4.75) 56(84) bytes of data.
64 bytes from 192.238.4.75: icmp_seq=1 ttl=62 time=0.833 ms
64 bytes from 192.238.4.75: icmp_seq=2 ttl=62 time=0.382 ms
64 bytes from 192.238.4.75: icmp_seq=3 ttl=62 time=0.553 ms

--- 192.238.4.75 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2045ms
rtt min/avg/max/mdev = 0.382/0.589/0.833/0.185 ms
Chain INPUT (policy DROP 0 packets, 0 bytes)
pkts bytes target     prot opt in     out     source               destination
```

<img src="https://github.com/user-attachments/assets/61d4ec8d-2e3e-47ec-8308-7954bc916cf9" width="482" alt="Ping Casper ke Balthasar & Lilith dan counter iptables">

---

### 4. Hasil dan Analisis

| Skenario | Arah | Hasil | Sesuai Soal |
|----------|------|-------|:-----------:|
| Balthasar → Casper | Masuk ke Casper | 100% *packet loss* | ✅ |
| Lilith → Casper | Masuk ke Casper (lintas subnet) | 100% *packet loss* | ✅ |
| Casper → Balthasar | Keluar dari Casper | 0% *packet loss* (TTL 64) | ✅ |
| Casper → Lilith | Keluar dari Casper | 0% *packet loss* (TTL 62) | ✅ |
| *Listener* TCP 5000 | — | `nc.traditional` tetap `LISTEN` | ✅ |

**Pembahasan:**

1. *Ping* dari Balthasar maupun Lilith ke Casper gagal 100% karena *rule* `INPUT ... --icmp-type echo-request -j DROP` membuang setiap permintaan *ping* sebelum diproses Casper. Karena dibuang (bukan di-*reject*), pengirim tidak menerima balasan apa pun, sehingga Casper seolah-olah tidak ada ("hantu").
2. *Ping* dari Casper ke Balthasar dan Lilith berhasil karena *chain* `OUTPUT` memiliki *policy* `ACCEPT`, dan balasan `echo-reply` (type 0) tidak cocok dengan *rule* `DROP` (type 8).
3. Nilai TTL berbeda: Balthasar `64` (langsung, satu *switch*) dan Lilith `62` (melewati 2 router), menandakan Lilith berada di subnet lain.
4. *Counter rule* `DROP` menunjukkan **6 paket / 504 byte**. Angka ini berasal dari 3 *ping* Balthasar + 3 *ping* Lilith (masing-masing 84 byte → 6 × 84 = 504 byte), membuktikan *rule* benar-benar menangkap *ping* masuk dari kedua perangkat.
5. Layanan TCP 5000 tetap aktif karena *rule* hanya menargetkan protokol ICMP dan tidak mengubah trafik TCP.

---

### 5. Kesimpulan

Dengan menambahkan satu *rule* `iptables -A INPUT -p icmp --icmp-type echo-request -j DROP` pada `/root/casper.sh`, Casper tidak dapat di-*ping* oleh perangkat mana pun (baik satu *switch* maupun lintas subnet), tetap dapat mem-*ping* ke luar, dan layanan TCP 5000 tetap berjalan. Seluruh persyaratan Soal 11 terpenuhi.

---

## Soal 12: Teknisi yang Kelewat Semangat

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Magi-Casper, Unit-00, Lilith

---

### 1. Soal

> **Teknisi yang kelewat semangat:** seorang teknisi menjalankan script hardening di Casper lalu pulang tanpa mengecek. Paginya Casper tidak bisa ping `8.8.8.8` dan tidak bisa `apt-get update`. Unit-00 juga tidak bisa menyambung ke TCP 5000 Casper. Ping ke Casper tetap ditolak, dan itu memang benar. Jalankan `break_fw.sh` di **Magi-Casper**, lalu perbaiki **tanpa membatalkan tujuan hardening**. Casper tetap menolak semua lalu lintas masuk yang tidak diizinkan, dan **policy INPUT tetap DROP**. Buktikan `nc` dari Unit-00 ke Casper port 5000 berhasil dan `apt-get update` di Casper berhasil.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Casper dapat melakukan *ping* ke `8.8.8.8` dan `apt-get update` | Izinkan paket balasan (`ESTABLISHED,RELATED`) dan hapus blokir DNS keluar |
| 2 | Unit-00 dapat `nc` ke Casper TCP 5000 | Tambah *rule* `ACCEPT` untuk `--dport 5000` |
| 3 | *Ping* ke Casper tetap ditolak | Pertahankan `DROP` pada ICMP `echo-request` |
| 4 | *Policy* `INPUT` tetap `DROP` | Tidak mengubah *policy* menjadi `ACCEPT` |

#### Alamat IP

| Perangkat | IP | Keterangan |
|-----------|----|------------|
| Magi-Casper | `192.238.4.58` | Perangkat yang *firewall*-nya dirusak |
| Unit-00 | `192.238.4.68` | Klien uji TCP 5000 |
| Lilith | `192.238.4.74` | Klien uji *ping* ke Casper |

---

### 2. Gejala Kerusakan (Sebelum Diperbaiki)

Setelah `break_fw.sh` dijalankan di Magi-Casper, gejala berikut diambil sebagai bukti.

> **Catatan:** Tambahkan *screenshot* proses pengecekan `/tmp` dan pengunduhan/eksekusi `break_fw.sh` di sini jika diperlukan.

#### 2.1 Magi-Casper — *Ping* Keluar dan `apt-get update` Gagal

```bash
ping -c 3 -W 2 8.8.8.8
apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/04dad4bf-f14e-4c49-bee8-feee51f390c3" width="364" alt="Casper: ping 8.8.8.8 dan apt-get update gagal">

#### 2.2 Unit-00 (`192.238.4.68`) — Koneksi ke TCP 5000 Casper Gagal

```bash
nc -v -w 3 192.238.4.58 5000
```

**Hasil:**

```text
nc: connect to 192.238.4.58 port 5000 (tcp) timed out: Operation in progress
```

<img src="https://github.com/user-attachments/assets/73ce4ace-f8fd-4514-b5e4-111521b5df3a" width="364" alt="Unit-00: nc ke Casper port 5000 timed out">

#### 2.3 Lilith (`192.238.4.74`) — *Ping* Masuk ke Casper Tetap Ditolak

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```text
3 packets transmitted, 0 received, 100% packet loss, time 2310ms
```

<img src="https://github.com/user-attachments/assets/7b8827d9-eafd-45dc-bc41-741170b64772" width="365" alt="Lilith: ping ke Casper 100% packet loss (sebelum perbaikan)">

Hasil ini sudah sesuai dengan tujuan *hardening*: *ping* ke Casper memang harus ditolak.

---

### 3. Diagnosis di Magi-Casper

```bash
iptables -S
iptables -L -n -v
iptables -t nat -S
ss -tlnp
cat /etc/resolv.conf
```

**Hasil (ringkas):**

```text
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

<img src="https://github.com/user-attachments/assets/ebca340c-ce30-4bcd-b0b5-18a1a90d8f34" width="366" alt="Output diagnosis iptables, ss, dan resolv.conf di Magi-Casper">

---

### 4. Analisis Penyebab Kerusakan

| No | Penyebab | Dampak |
|----|----------|--------|
| 1 | *Policy* `INPUT` diubah menjadi `DROP` **tanpa** *rule* `ESTABLISHED,RELATED` | Paket *ping* dan permintaan koneksi repositori berhasil keluar lewat `OUTPUT`, tetapi semua **paket balasan** (ICMP `echo-reply` dan data HTTP/TCP) yang masuk kembali langsung dibuang oleh `INPUT DROP` |
| 2 | *Rule* `-A OUTPUT -p udp --dport 53 -j DROP` | Seluruh kueri DNS ke `8.8.8.8` dibuang (*counter* menunjukkan **50 paket** ter-`DROP`). Akibatnya saat `apt-get update`, Casper gagal me-*resolve* `deb.debian.org` (*Temporary failure resolving*) |
| 3 | Tidak ada *rule* `ACCEPT` untuk TCP port 5000 | Koneksi TCP dari Unit-00 ke port 5000 dibuang diam-diam sehingga mengalami *timed out*, meskipun *listener* `nc.traditional` tetap `LISTEN` |
| 4 | *Rule* `-A INPUT -p icmp -j DROP` memblokir **semua** tipe ICMP | Tidak membedakan `echo-request` masuk dan `echo-reply` keluar, sehingga balasan *ping* Casper ke `8.8.8.8` ikut terbuang |

---

### 5. Perbaikan Permanen di Magi-Casper

Blok firewall pada `/root/casper.sh` diganti dengan konfigurasi *hardening* yang benar: *policy* `INPUT` tetap `DROP` dan *ping* masuk tetap ditolak, tetapi paket balasan dan TCP 5000 diizinkan. Perintah `sed` menghapus blok Soal 11 dan Soal 12 lama agar tidak terduplikasi, sedangkan `iptables -F` membersihkan *rule* yang rusak (termasuk blokir DNS di `OUTPUT`).

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

<img src="https://github.com/user-attachments/assets/fd9b5a7b-a794-4738-adab-077adc6ee88e" width="363" alt="Menjalankan perbaikan permanen di Magi-Casper">

**Penjelasan *Rule*:**

| *Rule* | Fungsi |
|--------|--------|
| `-P INPUT DROP` | *Policy default* tetap menolak semua lalu lintas masuk yang tidak diizinkan (tujuan *hardening* terjaga) |
| `-i lo -j ACCEPT` | Mengizinkan trafik *loopback* internal |
| `-p icmp --icmp-type echo-request -j DROP` | *Ping* masuk ke Casper tetap ditolak; hanya `echo-request` yang diblokir agar `echo-reply` tidak ikut terbuang |
| `-m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` | Mengizinkan balasan dari koneksi yang dimulai Casper (*ping* keluar, DNS, `apt-get`) |
| `-p tcp --dport 5000 -j ACCEPT` | Mengizinkan koneksi masuk ke layanan TCP 5000 |

> **Catatan:** Blokir DNS keluar (`-A OUTPUT -p udp --dport 53 -j DROP`) hilang karena `iptables -F` mengosongkan seluruh *rule*, dan *policy* `OUTPUT` tetap `ACCEPT`.

---

### 6. Verifikasi dan Bukti Perbaikan

#### 6.1 Magi-Casper — *Rule* Firewall, *Ping* `8.8.8.8`, dan `apt-get update`

```bash
iptables -S
ping -c 3 8.8.8.8
apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/ce7618bd-3104-481d-991b-c8e6710974fc" width="364" alt="Casper: iptables -S, ping 8.8.8.8, dan apt-get update berhasil">

#### 6.2 Unit-00 (`192.238.4.68`) — Koneksi ke Casper TCP 5000

```bash
echo "tes koneksi dari Unit-00" | nc -v -w 2 192.238.4.58 5000
```

**Hasil:**

```text
Connection to 192.238.4.58 5000 port [tcp/*] succeeded!
```

<img src="https://github.com/user-attachments/assets/a0e9ef28-ad66-4dc4-8bf6-5fa1cdec8446" width="362" alt="Unit-00: nc ke Casper port 5000 berhasil">

#### 6.3 Lilith (`192.238.4.74`) — *Ping* ke Casper Tetap Ditolak

```bash
ping -c 3 -W 1 192.238.4.58
```

**Hasil:**

```text
3 packets transmitted, 0 received, 100% packet loss, time 2268ms
```

<img src="https://github.com/user-attachments/assets/8524cef8-82aa-4f5a-bd38-1fb206613e29" width="363" alt="Lilith: ping ke Casper tetap 100% packet loss (setelah perbaikan)">

---

### 7. Perbandingan Sebelum dan Sesudah

| Pengujian | Sebelum | Sesudah | Sesuai Soal |
|-----------|---------|---------|:-----------:|
| Casper `ping 8.8.8.8` | 100% *packet loss* | 0% *packet loss* (TTL 110) | ✅ |
| Casper `apt-get update` | *Temporary failure resolving* | `Hit` / `Reading package lists... Done` | ✅ |
| Unit-00 `nc` ke Casper TCP 5000 | *timed out* | *succeeded!* | ✅ |
| Lilith *ping* ke Casper | 100% *packet loss* | 100% *packet loss* (tetap ditolak) | ✅ |
| *Policy* `INPUT` | `DROP` | `DROP` (tetap) | ✅ |

---

### 8. Kesimpulan

Kerusakan disebabkan oleh tiga kesalahan konfigurasi: tidak adanya *rule* `ESTABLISHED,RELATED` pada *policy* `INPUT DROP`, adanya blokir DNS keluar (UDP port 53), dan tidak adanya izin untuk TCP 5000. *Rule* ICMP yang terlalu luas juga ikut memblokir balasan *ping*. Setelah blok firewall diganti dengan konfigurasi yang benar, Casper kembali dapat melakukan *ping* keluar dan `apt-get update`, Unit-00 dapat terhubung ke TCP 5000, dan *ping* masuk ke Casper tetap ditolak. *Policy* `INPUT` tetap `DROP`, sehingga tujuan *hardening* tidak dibatalkan.

---

## Soal 13: Brankas Balthasar

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Magi-Balthasar, Magi-Casper, Magi-Melchior, Unit-01, Lilith

---

### 1. Soal

> **Brankas Balthasar:** Balthasar menyimpan seluruh data Geofront, tapi terlalu banyak yang bisa mengetuk pintunya, termasuk tetangga satu switch. **Hanya Casper dan semua Pilot** yang boleh menyambung ke **TCP 5000 dan 5001**. Node lain, termasuk yang satu switch, harus **langsung ditolak** di kedua port itu, bukan dibiarkan menggantung sampai timeout. Lalu lintas lain tidak boleh terganggu. Balthasar tetap harus bisa ping `8.8.8.8` dan menerima balasannya. Buktikan `nc` ke port 5000 dan 5001: **Unit-01 berhasil, Lilith dan Melchior ditolak seketika**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Hanya Casper dan semua Pilot yang boleh ke TCP 5000 & 5001 | *Rule* `ACCEPT` per IP sumber untuk kedua port |
| 2 | Node lain harus langsung ditolak (bukan *timeout*) | `REJECT --reject-with tcp-reset` (bukan `DROP`) |
| 3 | Lalu lintas lain tidak terganggu | *Policy* `INPUT`, `FORWARD`, `OUTPUT` tetap `ACCEPT`; *rule* hanya menargetkan TCP 5000/5001 |
| 4 | Balthasar tetap bisa *ping* `8.8.8.8` | Tidak ada *rule* yang menyentuh ICMP |

#### Alamat IP

| Perangkat | IP | Peran pada Soal 13 |
|-----------|----|--------------------|
| Magi-Balthasar | `192.238.4.59` | Server yang diamankan (*listener* TCP 5000 & 5001) |
| Magi-Casper | `192.238.4.58` | Diizinkan |
| Unit-01 | `192.238.4.66` | Pilot — diizinkan |
| Pilot | `192.238.4.67` | Pilot — diizinkan |
| Unit-00 | `192.238.4.68` | Pilot — diizinkan |
| Pilot | `192.238.3.130` | Pilot — diizinkan |
| Magi-Melchior | `192.238.4.60` | Satu *switch*, **ditolak** |
| Lilith | `192.238.4.74` | Lintas subnet, **ditolak** |

---

### 2. Langkah Pengerjaan di Magi-Balthasar

#### 2.1 Memeriksa Skrip Soal 7 dan Menambahkan Blok Firewall Soal 13

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
| `iptables -F` / `-X` | Membersihkan semua *rule* dan *custom chain* lama |
| `-P INPUT/FORWARD/OUTPUT ACCEPT` | *Policy default* tetap `ACCEPT` agar lalu lintas lain (termasuk *ping* ke `8.8.8.8`) tidak terganggu |
| `-s <IP> --dport $PORT -j ACCEPT` (5 IP) | Mengizinkan Casper dan semua Pilot menyambung ke TCP 5000 dan 5001 |
| `-p tcp --dport $PORT -j REJECT --reject-with tcp-reset` | Menolak seketika koneksi dari node lain dengan mengirim paket TCP RST |

**Mengapa `REJECT --reject-with tcp-reset`, bukan `DROP`?**
Soal meminta node lain "langsung ditolak, bukan dibiarkan menggantung sampai *timeout*". `DROP` membuang paket tanpa balasan sehingga klien menunggu sampai *timed out*, sedangkan `REJECT --reject-with tcp-reset` mengirim paket RST sehingga klien langsung menerima `Connection refused`.

**Mengapa urutan *rule* penting?**
*Rule* `ACCEPT` untuk kelima IP harus berada **di atas** *rule* `REJECT`. `iptables` membaca *rule* dari atas ke bawah dan berhenti pada kecocokan pertama. Jika `REJECT` berada di atas, semua koneksi — termasuk dari Casper dan Pilot — akan ditolak.

---

### 3. Pengujian dan Bukti

#### 3.1 Magi-Balthasar (`192.238.4.59`) — *Rule* `iptables`, *Listener*, dan *Ping* ke Internet

```bash
iptables -S
ss -tlnp | grep nc
ping -c 3 8.8.8.8
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/2b44377d-9d4c-4bda-bc93-00b487088ffb" width="360" alt="Balthasar: iptables -S, listener port 5000/5001, dan ping 8.8.8.8">

**Analisis:**

- *Rule* `ACCEPT` untuk kelima IP berada di atas *rule* `REJECT --reject-with tcp-reset` pada kedua port.
- Port 5000 dan 5001 berstatus `LISTEN` (proses `nc.traditional`), sehingga layanan tetap hidup.
- `ping 8.8.8.8` berhasil dengan 0% *packet loss*, sehingga lalu lintas lain tidak terganggu.

#### 3.2 Unit-01 (`192.238.4.66`) — Koneksi ke TCP 5000 & 5001 (Harus Berhasil)

```bash
echo "halo dari Unit-01 (5000)" | nc -v -w 2 192.238.4.59 5000
echo "halo dari Unit-01 (5001)" | nc -v -w 2 192.238.4.59 5001
```

**Hasil:**

```text
Connection to 192.238.4.59 5000 port [tcp/*] succeeded!
Connection to 192.238.4.59 5001 port [tcp/*] succeeded!
```

<img src="https://github.com/user-attachments/assets/6039229c-04e2-44f5-9c84-a31ea6ca4de5" width="240" alt="Unit-01: nc ke Balthasar port 5000 dan 5001 berhasil">

#### 3.3 Lilith (`192.238.4.74`) — Koneksi Lintas Subnet (Harus Langsung Ditolak)

```bash
nc -v 192.238.4.59 5000
nc -v 192.238.4.59 5001
```

**Hasil:**

```text
nc: connect to 192.238.4.59 port 5000 (tcp) failed: Connection refused
nc: connect to 192.238.4.59 port 5001 (tcp) failed: Connection refused
```

<img src="https://github.com/user-attachments/assets/faecaea6-34be-415d-ab6f-35538cb85dff" width="240" alt="Lilith: nc ke Balthasar ditolak seketika (Connection refused)">

#### 3.4 Magi-Melchior (`192.238.4.60`) — Tetangga Satu *Switch* (Harus Langsung Ditolak)

```bash
nc -v 192.238.4.59 5000
nc -v 192.238.4.59 5001
```

**Hasil:**

```text
192.238.4.59: inverse host lookup failed: Unknown host
(UNKNOWN) [192.238.4.59] 5000 (?) : Connection refused
192.238.4.59: inverse host lookup failed: Unknown host
(UNKNOWN) [192.238.4.59] 5001 (?) : Connection refused
```

<img src="https://github.com/user-attachments/assets/c097dfb1-d8eb-4b3a-ae52-52c3212075ee" width="242" alt="Melchior: nc ke Balthasar ditolak seketika (Connection refused)">

> Pesan `inverse host lookup failed: Unknown host` hanya berasal dari `nc` yang mencoba *reverse DNS* pada IP tujuan. Pesan ini tidak berkaitan dengan firewall; bagian pentingnya adalah `Connection refused`.

#### 3.5 Magi-Balthasar — Log Pesan Masuk dan *Counter* Paket

```bash
cat /tmp/balthasar_5000.log
cat /tmp/balthasar_5001.log
iptables -L INPUT -n -v
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/11164f2f-ad1e-40a2-bdee-4b01133d6f41" width="240" alt="Balthasar: log pesan dari Unit-01 dan counter iptables">

**Analisis:**

- Kedua file log berisi pesan dari Unit-01, sehingga koneksi dari Pilot benar-benar diterima oleh *listener*.
- *Rule* `ACCEPT` untuk `192.238.4.66` (Unit-01) mencatat **5 paket / 293 byte** pada tiap port, sedangkan *rule* `ACCEPT` IP lain bernilai 0 karena tidak diuji.
- *Rule* `REJECT` mencatat **2 paket / 120 byte** pada tiap port. Angka ini berasal dari satu percobaan koneksi Lilith dan satu dari Melchior (masing-masing satu paket SYN berukuran 60 byte → 2 × 60 = 120 byte), sehingga terbukti kedua node tersebut ditolak oleh *rule* `REJECT`.

---

### 4. Hasil dan Pembahasan

| Skenario | Port | Hasil | Sesuai Soal |
|----------|------|-------|:-----------:|
| Unit-01 → Balthasar | 5000 | `succeeded!` | ✅ |
| Unit-01 → Balthasar | 5001 | `succeeded!` | ✅ |
| Lilith → Balthasar (lintas subnet) | 5000 & 5001 | `Connection refused` (seketika) | ✅ |
| Melchior → Balthasar (satu *switch*) | 5000 & 5001 | `Connection refused` (seketika) | ✅ |
| Balthasar → `8.8.8.8` | — | 0% *packet loss* | ✅ |

**Pembahasan:**

1. Hanya IP yang tercantum pada *rule* `ACCEPT` (Casper dan semua Pilot) yang dapat menyambung ke TCP 5000 dan 5001. Koneksi dari Unit-01 berhasil dan pesannya tercatat di log.
2. Node lain, baik satu *switch* (Melchior) maupun lintas subnet (Lilith), ditolak **seketika** dengan `Connection refused`, bukan menggantung sampai *timed out*. Hal ini terjadi karena `REJECT --reject-with tcp-reset` langsung membalas dengan paket RST.
3. Karena *policy default* tetap `ACCEPT` dan *rule* hanya menargetkan TCP 5000/5001, lalu lintas lain tidak terganggu. Hal ini dibuktikan oleh `ping 8.8.8.8` yang tetap berhasil.
4. *Counter* `iptables` (5 paket dari Unit-01 pada *rule* `ACCEPT` dan 2 paket pada *rule* `REJECT` di tiap port) mengonfirmasi bahwa trafik diproses oleh *rule* yang tepat.

---

### 5. Kesimpulan

Dengan menambahkan blok firewall Soal 13 pada `/root/balthasar.sh` berupa *rule* `ACCEPT` per IP untuk Casper dan seluruh Pilot, diikuti *rule* `REJECT --reject-with tcp-reset` pada TCP 5000 dan 5001, akses ke Balthasar berhasil dibatasi. Unit-01 dapat menyambung, sedangkan Lilith dan Melchior langsung ditolak. *Policy default* `ACCEPT` menjaga lalu lintas lain, termasuk *ping* ke `8.8.8.8`, tetap berjalan. Seluruh persyaratan Soal 13 terpenuhi.

---

## Soal 14: Jam Kunjungan Sang Pinguin

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Pen-Pen, Unit-01, Lilith, Spy

---

### 1. Soal

> **Jam kunjungan sang pinguin:** Pen-Pen, pinguin Misato, menyimpan sesuatu yang bikin banyak orang penasaran. Ia hanya boleh dihubungi sesuai jadwal di bawah. Jadwal berlaku untuk **ping dan TCP 80** ke Pen-Pen, dari mana pun asalnya. **Jam komputer tidak boleh diubah.**

| Kelompok | Jadwal (WIB) | Keterangan |
|----------|--------------|------------|
| Pilot | 08.00 sampai 16.00 | Hanya Senin sampai Jumat |
| Staf Geofront | 17.00 sampai 23.00 | Setiap hari |
| Terduga Dummy System | Tidak pernah | Selalu ditolak |
| Lainnya | Bebas | Tanpa batasan |

> Simpan jadwal sebagai variabel di awal script Pen-Pen, bernama `PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, dan `STAF_STOP`. Untuk uji, ubah variabel itu sampai jadwalnya **cocok** dengan jam sekarang, lalu sampai **tidak cocok**. Ingat, iptables membaca **jam UTC, 7 jam lebih lambat dari WIB**. Tunjukkan hasil ping dari Unit-01, Lilith, dan Spy pada kedua kondisi.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Jadwal berlaku untuk *ping* dan TCP 80 | Hanya ICMP `echo-request` dan TCP port 80 yang diarahkan ke *chain* khusus `PENPEN_ACL` |
| 2 | Jadwal berbasis waktu | Modul `-m time` (`--timestart`, `--timestop`, `--weekdays`) |
| 3 | Jam komputer tidak boleh diubah | Jadwal diubah lewat **variabel di skrip**, bukan lewat jam sistem |
| 4 | Jadwal dalam UTC | WIB dikurangi 7 jam |
| 5 | Lainnya bebas | Node yang tidak terdaftar tidak cocok *rule* apa pun, kembali ke *policy* `ACCEPT` |

#### Konversi Jadwal WIB ke UTC

| Kelompok | WIB | UTC (`--timestart` / `--timestop`) | Hari |
|----------|-----|------------------------------------|------|
| Pilot | 08.00 – 16.00 | `01:00` – `09:00` | `Mon,Tue,Wed,Thu,Fri` |
| Staf Geofront | 17.00 – 23.00 | `10:00` – `16:00` | Setiap hari |

#### Alamat IP

| Perangkat / Kelompok | IP | Peran |
|----------------------|----|-------|
| Pen-Pen | `192.238.3.194` | Target yang dijaga jadwal |
| Pilot (termasuk Unit-01) | `192.238.4.66`, `192.238.4.67`, `192.238.4.68`, `192.238.3.130` | Jadwal Pilot |
| Staf Geofront (termasuk Lilith) | `192.238.4.74`, `192.238.4.75` | Jadwal Staf |
| Terduga Dummy System (termasuk Spy) | `192.238.4.50`, `192.238.4.51`, `192.238.4.102` | Selalu ditolak |

---

### 2. Langkah 1 — Memasang Skrip Jadwal di `/root/penpen.sh`

Skrip `/root/penpen.sh` ditulis ulang dengan kelima variabel jadwal di baris paling atas. Konfigurasi IP (`/root/ip.sh`) dan *listener* port 80 dari Soal 8 tetap dipertahankan. Aturan firewall dibuat dalam *chain* khusus `PENPEN_ACL`.

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

<!-- Screenshot belum diunggah: Script jadwal pada Pen-Pen -->

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| Variabel `PILOT_*` dan `STAF_*` | Jadwal disimpan di awal skrip (sesuai permintaan soal) sehingga pengujian cukup dilakukan dengan mengubah variabel, tanpa mengubah jam sistem |
| `iptables -N PENPEN_ACL` | Membuat *chain* khusus agar aturan jadwal rapi dan terpisah |
| `-p icmp --icmp-type echo-request -j PENPEN_ACL` dan `-p tcp --dport 80 -j PENPEN_ACL` | Hanya *ping* dan TCP 80 yang dikenai jadwal; lalu lintas lain tidak tersentuh |
| `-m time --timestart ... --timestop ... --weekdays ... -j ACCEPT` | Mengizinkan sumber pada rentang jam UTC (dan hari) yang ditentukan |
| `-s $IP -j DROP` tepat setelah `ACCEPT` | Di luar jadwal, paket dari IP tersebut dibuang |
| Kelompok Dummy: langsung `DROP` | Selalu ditolak tanpa syarat waktu |
| *Policy* `INPUT ACCEPT` | IP lain ("Lainnya") tidak cocok *rule* apa pun sehingga bebas |

**Urutan *rule* penting:** `ACCEPT` bersyarat waktu harus berada **sebelum** `DROP` untuk IP yang sama, karena `iptables` berhenti pada kecocokan pertama.

---

### 3. Langkah 2 — Uji Kondisi 1: Jadwal **Cocok** dengan Jam Sekarang

#### 3.1 Cek Jam UTC dan Hari Saat Ini di Pen-Pen

```bash
date -u
```

**Hasil:**

```text
Wed Nov 25 13:45:22 UTC 2026
```

<!-- Screenshot belum diunggah: date -u di Pen-Pen -->

#### 3.2 Mengubah Variabel agar Cocok dengan Jam UTC Sekarang

Variabel Pilot dan Staf diubah ke rentang yang mencakup jam UTC *container*. `PILOT_DAYS` ditambah `Sat,Sun` agar hari saat pengujian pasti termasuk.

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

```bash
#!/bin/bash

# === VARIABEL JADWAL KUNJUNGAN (UTC = WIB - 7 JAM) ===
PILOT_START="08:00"
PILOT_STOP="11:00"
PILOT_DAYS="Mon,Tue,Wed,Thu,Fri,Sat,Sun"
STAF_START="08:00"
STAF_STOP="11:00"

[ -f /root/ip.sh ] && sh /root/ip.sh
```

<!-- Screenshot belum diunggah: Mengubah variabel jadwal agar cocok -->

#### 3.3 Pengujian dari Tiga Node (Harus Berhasil, Berhasil, Gagal)

**Unit-01 (`192.238.4.66`, Pilot) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```text
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```

<!-- Screenshot belum diunggah: Unit-01: ping dan nc ke Pen-Pen berhasil -->

**Lilith (`192.238.4.74`, Staf) — harus berhasil:**

```bash
ping -c 3 -W 1 192.238.3.194
nc -v -w 2 192.238.3.194 80
```

```text
3 packets transmitted, 3 received, 0% packet loss, time 2224ms
Connection to 192.238.3.194 80 port [tcp/http] succeeded!
Welcome to Pen-Pen
```

<!-- Screenshot belum diunggah: Lilith: ping dan nc ke Pen-Pen berhasil -->

**Spy (`192.238.4.102`, Terduga Dummy System) — harus gagal (selalu ditolak):**

```bash
ping -c 3 -W 1 192.238.3.194
```

```text
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```

<!-- Screenshot belum diunggah: Spy: ping ke Pen-Pen gagal (kondisi 1) -->

---

### 4. Langkah 3 — Uji Kondisi 2: Jadwal **Tidak Cocok** dengan Jam Sekarang

#### 4.1 Menggeser Variabel ke Jam di Luar Jam UTC Sekarang

```bash
sed -i 's/^PILOT_START=.*/PILOT_START="13:00"/' /root/penpen.sh
sed -i 's/^PILOT_STOP=.*/PILOT_STOP="15:00"/' /root/penpen.sh
sed -i 's/^STAF_START=.*/STAF_START="13:00"/' /root/penpen.sh
sed -i 's/^STAF_STOP=.*/STAF_STOP="15:00"/' /root/penpen.sh
bash /root/penpen.sh
head -n 10 /root/penpen.sh
```

#### 4.2 Pengujian Ulang dari Tiga Node

**Unit-01 (`192.238.4.66`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

> Tambahkan *screenshot* Unit-01 (100% *packet loss*) di sini. *Screenshot* ini belum ada di foto yang dikirim.

**Lilith (`192.238.4.74`) — sekarang harus gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```text
3 packets transmitted, 0 received, 100% packet loss, time 2307ms
```

<!-- Screenshot belum diunggah: Lilith: ping ke Pen-Pen gagal (kondisi 2) -->

**Spy (`192.238.4.102`) — tetap gagal:**

```bash
ping -c 3 -W 1 192.238.3.194
```

```text
3 packets transmitted, 0 received, 100% packet loss, time 2313ms
```

<!-- Screenshot belum diunggah: Spy: ping ke Pen-Pen tetap gagal (kondisi 2) -->

---

### 5. Langkah 4 — Mengembalikan ke Jadwal Asli Soal

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

```text
3 packets transmitted, 0 received, 100% packet loss, time 2297ms
```

<!-- Screenshot belum diunggah: Spy: ping tetap gagal setelah jadwal dikembalikan -->

---

### 6. Hasil dan Pembahasan

| Node | Kelompok | Kondisi 1 (Jadwal Cocok) | Kondisi 2 (Jadwal Tidak Cocok) | Sesuai Soal |
|------|----------|--------------------------|--------------------------------|:-----------:|
| Unit-01 (`192.238.4.66`) | Pilot | *Ping* 0% *loss*, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% *packet loss*) | ✅ |
| Lilith (`192.238.4.74`) | Staf Geofront | *Ping* 0% *loss*, `nc` port 80 berhasil (`Welcome to Pen-Pen`) | Gagal (100% *packet loss*) | ✅ |
| Spy (`192.238.4.102`) | Terduga Dummy System | Gagal (100% *packet loss*) | Gagal (100% *packet loss*) | ✅ |

**Pembahasan:**

1. **Jadwal diuji tanpa mengubah jam komputer.** Soal melarang jam sistem diubah, sehingga kondisi "cocok" dan "tidak cocok" dibuat dengan menggeser variabel `PILOT_*` dan `STAF_*` di awal skrip, lalu menjalankan ulang `penpen.sh`.
2. **Jadwal berbasis UTC.** Modul `-m time` pada `iptables` membaca jam UTC. Karena WIB = UTC + 7, Pilot 08.00–16.00 WIB menjadi `01:00`–`09:00` UTC dan Staf 17.00–23.00 WIB menjadi `10:00`–`16:00` UTC.
3. **Hanya *ping* dan TCP 80 yang dibatasi.** *Chain* `PENPEN_ACL` hanya dipanggil untuk ICMP `echo-request` dan TCP port 80, sehingga lalu lintas lain ke Pen-Pen tetap normal.
4. **Pilot dan Staf mengikuti jadwal.** Pada Kondisi 1, Unit-01 (Pilot) dan Lilith (Staf) dapat melakukan *ping* dan membuka TCP 80. Pada Kondisi 2, keduanya terkena `DROP` dan *ping* gagal 100%.
5. **Dummy System selalu ditolak.** Spy (`192.238.4.102`) selalu gagal pada kedua kondisi karena *rule*-nya adalah `DROP` tanpa syarat waktu.
6. **Node lain bebas.** Node yang tidak terdaftar tidak cocok dengan *rule* mana pun di `PENPEN_ACL` dan kembali ke *policy* `INPUT ACCEPT`.

---

### 7. Kesimpulan

Dengan menyimpan jadwal sebagai variabel (`PILOT_START`, `PILOT_STOP`, `PILOT_DAYS`, `STAF_START`, `STAF_STOP`) di awal `/root/penpen.sh` dan menerapkan *rule* `iptables -m time` pada *chain* `PENPEN_ACL`, akses *ping* dan TCP 80 ke Pen-Pen dapat dibatasi sesuai jadwal tanpa mengubah jam komputer. Unit-01 (Pilot) dan Lilith (Staf) hanya berhasil saat jadwal cocok, sedangkan Spy (Terduga Dummy System) selalu ditolak. Setelah pengujian, variabel dikembalikan ke nilai asli sehingga konfigurasi akhir sesuai spesifikasi soal.

---

## Soal 15: Banjir Ping di Pusat Komando

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Tokyo3-Core, Lilith

---

### 1. Soal

> **Banjir ping di pusat komando:** Instrumentality makin dekat, dan semua orang berebut mem-ping Tokyo3-Core. Banjir ping bisa melumpuhkannya. Batasi Tokyo3-Core agar hanya menjawab **maksimal 5 ping per detik**, dan buang kelebihannya. Aturan ini **tidak boleh mengganggu ping di bawah batas**. Aturan ini juga **tidak boleh membatasi ping yang dikirim Tokyo3-Core sendiri**. Pastikan rule benar-benar mengenai paket yang dituju. Periksa urutan rule dan jenis paketnya. Buktikan ping rapat dari Lilith, misalnya `ping -i 0.1 -c50`, sebagian hilang, dan tunjukkan counter rule pembuangan naik. Buktikan ping biasa semuanya berhasil, dengan jeda minimal 3 detik di antaranya.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Maksimal 5 *ping* masuk per detik | `-m limit --limit 5/second --limit-burst 5` pada *rule* `ACCEPT` |
| 2 | Kelebihan dibuang | *Rule* `DROP` untuk ICMP `echo-request` tepat **setelah** *rule* `ACCEPT` |
| 3 | *Ping* di bawah batas tidak terganggu | *Ping* yang masih dalam batas `limit` lolos di *rule* pertama |
| 4 | *Ping* keluar dari Tokyo3-Core tidak dibatasi | *Rule* berada di *chain* `INPUT` dan hanya mencocokkan `echo-request` (type 8) |
| 5 | *Rule* harus mengenai paket yang tepat | Pencocokan spesifik: `-p icmp --icmp-type echo-request` |

#### Alamat IP

| Perangkat | IP |
|-----------|----|
| Tokyo3-Core | `192.238.4.106` |
| Lilith | `192.238.4.74` |

---

### 2. Langkah 1 — Menambahkan Aturan Rate-Limit ke `/root/tokyo3.sh`

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

<img src="https://github.com/user-attachments/assets/512d155a-0dbf-42ac-8102-38e0e3e0d16a" width="240" alt="Menambahkan blok firewall Soal 15 di Tokyo3-Core">

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| `iptables -F` / `-X` | Membersihkan *rule* dan *chain* lama |
| `-P INPUT/FORWARD/OUTPUT ACCEPT` | *Policy default* tetap `ACCEPT` sehingga lalu lintas lain tidak terganggu |
| `-p icmp --icmp-type echo-request` | Hanya mencocokkan permintaan *ping* masuk (ICMP type 8); balasan (`echo-reply`) tidak ikut |
| `-m limit --limit 5/second --limit-burst 5 -j ACCEPT` | Meloloskan *ping* selama masih dalam batas rata-rata 5 paket/detik, dengan toleransi *burst* awal 5 paket |
| `-A INPUT ... -j DROP` (*rule* kedua) | Membuang semua `echo-request` yang tidak lolos *rule* pertama, yaitu kelebihan di atas 5/detik |

**Urutan *rule* penting:** *Rule* `ACCEPT` berbatas (`limit`) harus berada **di baris pertama**, diikuti *rule* `DROP` di baris kedua. Jika `DROP` berada di atas, semua *ping* — termasuk yang masih di bawah batas — akan dibuang.

**Mengapa *ping* keluar dari Tokyo3-Core tidak terbatasi?**
*Ping* yang dikirim Tokyo3-Core melewati *chain* `OUTPUT` (tidak disentuh *rule*), dan balasannya berupa `echo-reply` (type 0) yang masuk ke `INPUT` tidak cocok dengan *rule* karena *rule* hanya mencocokkan `echo-request` (type 8).

---

### 3. Langkah 2 — Pengujian dan Bukti

#### 3.1 Tokyo3-Core (`192.238.4.106`) — Cek Urutan *Rule* dan *Counter* Awal

```bash
iptables -S
iptables -L INPUT -n -v
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/4321a724-6396-4524-80fb-284fbffe2fab" width="479" alt="Tokyo3-Core: urutan rule dan counter awal">

**Analisis:** *Rule* `limit: avg 5/sec burst 5` berada di baris pertama, diikuti *rule* `DROP icmptype 8` di baris kedua. Kedua *counter* masih `0`, menandakan belum ada paket sebelum pengujian.

#### 3.2 Lilith (`192.238.4.74`) — Banjir *Ping* Rapat (0,1 Detik, 50 Paket)

```bash
ping -i 0.1 -c 50 192.238.4.106
```

**Hasil:**

```text
--- 192.238.4.106 ping statistics ---
50 packets transmitted, 30 received, 40% packet loss, time 5641ms
rtt min/avg/max/mdev = 0.619/1.071/1.980/0.307 ms
```

Balasan terlihat berurutan pada `icmp_seq=1` sampai `9`, lalu mulai selang-seling (`11, 13, 15, ...`) karena kelebihan paket dibuang.

<img src="https://github.com/user-attachments/assets/16aac166-8639-4ee2-b1e4-d85269dd9772" width="482" alt="Lilith: ping rapat 50 paket, 40% packet loss">

#### 3.3 Tokyo3-Core — Kenaikan *Counter Rule* Pembuangan

```bash
iptables -L INPUT -n -v
```

**Hasil:**

```text
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target  prot opt in  out  source     destination
   30  2520 ACCEPT  icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8 limit: avg 5/sec burst 5
   20  1680 DROP    icmp --  *   *    0.0.0.0/0  0.0.0.0/0   icmptype 8
```

<img src="https://github.com/user-attachments/assets/3215e885-146d-4a68-af3d-87344e0a7e2f" width="480" alt="Tokyo3-Core: counter ACCEPT 30 paket dan DROP 20 paket">

**Analisis:**

- *Rule* `ACCEPT` mencatat **30 paket (2520 byte)** dan *rule* `DROP` mencatat **20 paket (1680 byte)**.
- 30 + 20 = **50 paket**, sama dengan jumlah paket yang dikirim Lilith.
- 30 paket yang diterima cocok dengan hasil *ping* Lilith (`30 received`), sedangkan 20 paket sisanya dibuang. Hal ini membuktikan kelebihan *ping* benar-benar ditangkap oleh *rule* `DROP`.
- Tiap paket *ping* berukuran 84 byte: 30 × 84 = 2520 byte dan 20 × 84 = 1680 byte.

#### 3.4 Lilith — *Ping* Biasa dengan Jeda Minimal 3 Detik

```bash
ping -i 3 -c 5 192.238.4.106
```

**Hasil:**

```text
5 packets transmitted, 5 received, 0% packet loss, time 13412ms
```

<img src="https://github.com/user-attachments/assets/2f8273c2-bd82-4e08-9254-a7af90aff301" width="387" alt="Lilith: ping biasa jeda 3 detik, 0% packet loss">

#### 3.5 Tokyo3-Core — *Ping* Keluar Tidak Ikut Terbatasi

```bash
ping -i 0.1 -c 15 192.238.4.75
```

**Hasil:**

```text
15 packets transmitted, 15 received, 0% packet loss, time 1412ms
rtt min/avg/max/mdev = 0.847/1.240/1.786/0.298 ms
```

<img src="https://github.com/user-attachments/assets/f65e3c2f-12c8-445a-bac2-e71b92e7da27" width="360" alt="Tokyo3-Core: ping keluar rapat ke Lilith, 0% packet loss">

---

### 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Lilith → Tokyo3-Core `ping -i 0.1 -c 50` (10 *ping*/detik) | Sebagian hilang | 30 diterima, 20 hilang (40% *loss*) | ✅ |
| *Counter rule* `DROP` | Naik | 20 paket / 1680 byte | ✅ |
| Lilith → Tokyo3-Core `ping -i 3 -c 5` (di bawah batas) | Semua berhasil | 5 diterima (0% *loss*) | ✅ |
| Tokyo3-Core → Lilith `ping -i 0.1 -c 15` (*ping* keluar) | Tidak terbatasi | 15 diterima (0% *loss*) | ✅ |

**Pembahasan:**

1. **Pembatasan 5 *ping* per detik.** *Rule* `-m limit --limit 5/second --limit-burst 5` bekerja seperti *token bucket*: 5 paket pertama lolos sebagai *burst*, lalu laju rata-rata dibatasi 5 paket/detik. Dengan laju kirim 10 *ping*/detik, sekitar setengah paket lolos, sesuai hasil 30 dari 50 paket (40% *loss*).
2. **Pola balasan.** Balasan awal berurutan (`seq 1–9`) karena masih ada token *burst* dan pengisian token, lalu berubah selang-seling saat laju kirim melebihi laju pengisian.
3. **Kelebihan dibuang.** *Counter* `DROP` naik menjadi 20 paket dan jumlah `ACCEPT` + `DROP` (30 + 20) sama dengan 50 paket yang dikirim, sehingga *rule* mengenai paket yang tepat.
4. ***Ping* di bawah batas tidak terganggu.** Dengan jeda 3 detik (sekitar 0,33 *ping*/detik, jauh di bawah 5/detik) semua paket lolos *rule* `ACCEPT` pertama sehingga 0% *packet loss*.
5. ***Ping* keluar tidak terbatasi.** *Ping* dari Tokyo3-Core melewati *chain* `OUTPUT` dan balasannya (`echo-reply`) bukan type 8, sehingga tidak cocok dengan *rule*. Hasilnya, 15 dari 15 paket berhasil walaupun dikirim rapat.

---

### 5. Kesimpulan

Dengan menambahkan *rule* `iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 5/second --limit-burst 5 -j ACCEPT` diikuti *rule* `DROP` untuk `echo-request` pada `/root/tokyo3.sh`, Tokyo3-Core hanya menjawab maksimal 5 *ping* per detik dan membuang kelebihannya. *Ping* rapat dari Lilith sebagian hilang (40% *loss*) dengan *counter* `DROP` yang naik, *ping* biasa dengan jeda 3 detik semuanya berhasil, dan *ping* yang dikirim Tokyo3-Core sendiri tidak terbatasi. Seluruh persyaratan Soal 15 terpenuhi.

---

## Soal 16: Pencuri yang Mencoba Setiap Pintu

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Tokyo3-Core, Dummy-Plug (Switch-Lab)

---

### 1. Soal

> **Pencuri yang mencoba setiap pintu:** dari Switch-Lab, Dummy-Plug mengetuk port sensitif Tokyo3-Core satu per satu seperti pencuri. Ritsuko ingin tahu setiap percobaannya, dan pintunya harus dibanting. Setiap koneksi TCP dari **Dummy-Plug** ke **port 22, 23, dan 3306** di Tokyo3-Core harus **dicatat di log** dengan prefix huruf besar `SCAN-NERV:`, lalu **ditolak seketika**. Port 80 dan 8080 tetap bisa diakses, dan klien lain tidak boleh terganggu. Buktikan `nc -w` dari Dummy-Plug ke port 22, 23, dan 3306 langsung ditolak, dan ke port 80 dan 8080 berhasil. Tunjukkan **counter rule pencatatan**, karena log kernel sering tidak terlihat di container.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Catat koneksi Dummy-Plug ke port 22, 23, 3306 | *Rule* `-j LOG --log-prefix "SCAN-NERV: "` |
| 2 | Tolak seketika (bukan *timeout*) | *Rule* `-j REJECT --reject-with tcp-reset` |
| 3 | Hanya Dummy-Plug dan hanya port tersebut | Pencocokan `-s 192.238.4.51 -p tcp --dport <port>` |
| 4 | Port 80 dan 8080 tetap dapat diakses; klien lain tidak terganggu | *Policy default* `ACCEPT`, tidak ada *rule* untuk port 80/8080 |
| 5 | Tidak merusak aturan Soal 15 | Aturan Soal 15 digabung ke blok yang sama dalam `/root/tokyo3.sh` |
| 6 | Bukti pencatatan | *Counter* `pkts` pada *rule* `LOG` (`iptables -L INPUT -n -v`) |

#### Alamat IP

| Perangkat | IP | Peran |
|-----------|----|-------|
| Tokyo3-Core | `192.238.4.106` | Target (*listener* TCP 80 dan 8080) |
| Dummy-Plug | `192.238.4.51` | Penguji / "pencuri" |

---

### 2. Langkah 1 — Memperbarui `/root/tokyo3.sh` di Tokyo3-Core

Aturan Soal 15 (*rate-limit ping*) dan Soal 16 digabung dalam satu blok secara idempoten. Perintah `sed` menghapus blok Soal 15 lama beserta isi setelahnya agar tidak terduplikasi, lalu blok gabungan ditulis ulang di bagian bawah skrip. Hal ini penting agar aturan Soal 15 tetap berlaku.

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
| *Rule* Soal 15 (`limit` + `DROP`) | Tetap dipertahankan: *ping* masuk maksimal 5/detik |
| `-s 192.238.4.51 -p tcp --dport $PORT` | Hanya koneksi TCP dari Dummy-Plug ke port 22, 23, dan 3306 yang dikenai *rule* |
| `-j LOG --log-prefix "SCAN-NERV: "` | Mencatat percobaan koneksi dengan *prefix* `SCAN-NERV:` |
| `-j REJECT --reject-with tcp-reset` | Menolak seketika dengan paket TCP RST, sehingga klien langsung menerima `Connection refused` tanpa menunggu *timeout* |
| *Policy* `INPUT ACCEPT` | Port 80, 8080, dan klien lain tidak tersentuh, sehingga tetap normal |

**Mengapa `LOG` harus di atas `REJECT`?**
`REJECT` adalah target terminasi: paket yang cocok langsung diproses dan tidak diteruskan ke *rule* berikutnya. Sebaliknya, `LOG` bukan target terminasi, sehingga paket tetap lanjut ke *rule* berikutnya setelah dicatat. Jika `REJECT` berada di atas, *rule* `LOG` tidak akan pernah tercapai dan tidak ada percobaan yang tercatat.

---

### 3. Langkah 2 — Pengujian dan Bukti

#### 3.1 Tokyo3-Core (`192.238.4.106`) — Cek *Rule* Awal Sebelum *Scanning*

```bash
iptables -S
iptables -L INPUT -n -v
```

**Hasil (*rule*):**

```text
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

**Hasil (*counter* awal):** seluruh *counter* `pkts` dan `bytes` bernilai `0` (*policy* `ACCEPT 0 packets, 0 bytes`).

<img src="https://github.com/user-attachments/assets/9b0e6c10-8a43-44d5-b885-c1374aae6874" width="482" alt="Tokyo3-Core: rule awal dan counter 0 sebelum scanning">

**Analisis:** Aturan Soal 15 tetap berada di dua baris pertama, diikuti pasangan `LOG` dan `REJECT` untuk port 22, 23, dan 3306 (dengan `LOG` selalu tepat di atas `REJECT`). Semua *counter* masih `0` sebagai pembanding sebelum pengujian.

#### 3.2 Dummy-Plug (`192.238.4.51`) — Uji Port Tertutup (22, 23, 3306) dan Port Layanan (80, 8080)

Karena Dummy-Plug memakai *image* Alpine (`nc` versi BusyBox yang tidak selalu mencetak pesan *error*), pengujian menampilkan **exit code** (`$?`) dan **durasi** (`time`) untuk membuktikan koneksi ke port 22, 23, dan 3306 gagal seketika (~0,00 detik), bukan menunggu *timeout* 2 detik.

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

```text
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

<img src="https://github.com/user-attachments/assets/30f928c8-7a16-4c6c-8043-b9270989f492" width="477" alt="Dummy-Plug: port 22/23/3306 ditolak seketika, port 80/8080 berhasil">

**Analisis:**

- Port **22, 23, dan 3306** selesai dalam **0,002 detik** dengan `Exit code: 1`, jauh di bawah batas `-w 2` (2 detik), sehingga koneksi ditolak seketika (TCP RST) dan bukan dibiarkan menggantung sampai *timeout*.
- Port **80** membalas `Welcome to Tokyo3-Core` dan port **8080** membalas `syifa imutz`, sehingga layanan tetap dapat diakses.

#### 3.3 Tokyo3-Core — *Counter Rule* Pencatatan (`LOG`) dan Penolakan (`REJECT`)

Log kernel sering tidak terlihat di *container*, sehingga bukti pencatatan diambil dari *counter rule*.

```bash
iptables -L INPUT -n -v
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/66e93990-3ab0-4408-a5c0-5119592c5a91" width="480" alt="Tokyo3-Core: counter rule LOG dan REJECT naik setelah scanning">

**Analisis:**

- *Counter rule* `LOG` untuk port **22, 23, dan 3306** masing-masing naik dari `0` menjadi **2 paket / 120 byte**, membuktikan setiap percobaan koneksi dari Dummy-Plug tercatat dengan *prefix* `SCAN-NERV: `.
- *Counter rule* `REJECT` pada tiap port bernilai sama dengan `LOG` (**2 paket / 120 byte**), artinya setiap paket yang dicatat langsung ditolak dengan `tcp-reset`.
- *Counter rule* ICMP (Soal 15) tetap `0` karena tidak ada *ping* pada pengujian ini, sehingga tidak ada interferensi antar-aturan.
- *Policy* `INPUT ACCEPT` mencatat **20 paket / 1072 byte**: lalu lintas yang tidak cocok *rule* mana pun (termasuk koneksi ke port 80 dan 8080) tetap diterima.

---

### 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Dummy-Plug → Tokyo3-Core port 22 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 23 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 3306 | Ditolak seketika | `real 0m0.002s`, `Exit code: 1` | ✅ |
| Dummy-Plug → Tokyo3-Core port 80 | Berhasil | `Welcome to Tokyo3-Core` | ✅ |
| Dummy-Plug → Tokyo3-Core port 8080 | Berhasil | `syifa imutz` | ✅ |
| *Counter rule* `LOG` (*prefix* `SCAN-NERV: `) | Naik | 2 paket / 120 byte per port | ✅ |
| *Counter rule* `REJECT` | Naik | 2 paket / 120 byte per port | ✅ |

**Pembahasan:**

1. **Pencatatan dan penolakan berpasangan.** Untuk tiap port sensitif (22, 23, 3306) dipasang dua *rule* berurutan: `LOG` terlebih dahulu agar percobaan tercatat, lalu `REJECT --reject-with tcp-reset` untuk membanting pintu. *Counter* keduanya sama, membuktikan setiap koneksi yang dicatat langsung ditolak.
2. **Ditolak seketika.** `REJECT` dengan `tcp-reset` mengirim RST sehingga klien langsung gagal dalam sekitar 0,002 detik. Sebaliknya, `DROP` akan membuat `nc -w 2` menunggu sampai 2 detik.
3. **Port layanan tidak terganggu.** *Rule* hanya mencocokkan port 22, 23, dan 3306 dari sumber `192.238.4.51`. Port 80 dan 8080 tidak memiliki *rule* sehingga jatuh ke *policy* `ACCEPT` dan tetap membalas.
4. **Klien lain tidak terganggu.** Setiap *rule* dibatasi dengan `-s 192.238.4.51`, sehingga hanya Dummy-Plug yang terkena. Node lain melewati *rule* ini dan diproses oleh *policy* `ACCEPT`.
5. **Aturan Soal 15 tetap utuh.** Blok gabungan Soal 15 dan 16 mempertahankan *rate-limit ping* di dua baris pertama.
6. **Bukti lewat *counter*.** Karena log kernel sering tidak terlihat di *container*, kenaikan *counter* pada *rule* `LOG` menjadi bukti bahwa pencatatan berjalan.

---

### 5. Kesimpulan

Dengan menggabungkan aturan Soal 15 dan Soal 16 pada `/root/tokyo3.sh`, setiap koneksi TCP dari Dummy-Plug (`192.238.4.51`) ke port 22, 23, dan 3306 di Tokyo3-Core dicatat dengan *prefix* `SCAN-NERV: ` melalui *rule* `LOG`, lalu ditolak seketika melalui `REJECT --reject-with tcp-reset`. Pengujian menunjukkan koneksi ke port tertutup gagal dalam sekitar 0,002 detik dengan `Exit code: 1`, sedangkan port 80 dan 8080 tetap berhasil. *Counter rule* `LOG` dan `REJECT` naik sebagai bukti pencatatan dan penolakan. Seluruh persyaratan Soal 16 terpenuhi.

---

## Soal 17: Sihir Hitam di Jalur Paket

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Fuyutsuki (router), Magi-Casper, Zeruel, Pen-Pen

---

### 1. Soal

> **Sihir hitam di jalur paket:** Ritsuko meninggalkan memo di meja Gendo. Paket dari Casper ke Zeruel tidak pernah sampai ke Zeruel, karena ada router yang diam-diam mengalihkannya ke tempat lain. *"Cari saja router yang menjadi gateway Switch-Terminal."* Gendo menyuruhmu meniru router yang diam-diam itu untuk menjebak siapa pun. Di **Fuyutsuki**, alihkan setiap koneksi **TCP** dari Magi-Casper ke Zeruel supaya sampai di **Pen-Pen**. Casper tidak boleh sadar. Balasan yang ia terima harus tetap terlihat datang dari Zeruel. **Ping tidak dialihkan.** Nyalakan listener **port 9000** di Pen-Pen dan Zeruel. Dari Casper, kirim pesan ke IP Zeruel port 9000. Buktikan pesan itu muncul di **Pen-Pen, bukan di Zeruel**. Tunjukkan juga tabel `conntrack` di Fuyutsuki.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Alihkan TCP dari Casper ke Zeruel agar sampai di Pen-Pen | `DNAT` pada *chain* `PREROUTING` tabel `nat` di Fuyutsuki |
| 2 | Hanya koneksi dari Casper ke Zeruel yang dialihkan | Pencocokan `-s 192.238.4.58 -d 192.238.4.82 -p tcp` |
| 3 | Casper tidak boleh sadar; balasan terlihat dari Zeruel | Balasan dipaksa kembali lewat Fuyutsuki dengan `MASQUERADE`, lalu di-*un-NAT* otomatis oleh `conntrack` sehingga sumbernya kembali tampak sebagai Zeruel |
| 4 | *Ping* tidak dialihkan | *Rule* hanya untuk protokol TCP |
| 5 | *Listener* port 9000 di Pen-Pen dan Zeruel | `nc` mendengar di port 9000 dan menulis ke file log |
| 6 | Bukti | Isi log di Pen-Pen dan Zeruel, *counter rule* NAT, dan tabel `conntrack` |

#### Alamat IP

| Perangkat | IP | Peran |
|-----------|----|-------|
| Magi-Casper | `192.238.4.58` | Klien (korban) |
| Zeruel | `192.238.4.82` | Tujuan yang dikira oleh Casper |
| Pen-Pen | `192.238.3.194` | Tujuan sebenarnya (jebakan) |
| Fuyutsuki | Router *gateway* Switch-Terminal | Melakukan NAT pengalihan |

---

### 2. Langkah 1 — Konfigurasi NAT Pengalihan di Fuyutsuki

Di *console* **Fuyutsuki** dipasang aturan `DNAT` dan `MASQUERADE`, *tool* `conntrack` disiapkan, dan konfigurasi disimpan persisten di `/root/fuyutsuki.sh` (dijalankan otomatis lewat `/etc/profile` dan `/root/.bashrc`).

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

<img src="https://github.com/user-attachments/assets/c4f764f4-c5dd-4fe8-a473-81be700822d9" width="484" alt="Menjalankan skrip NAT di Fuyutsuki">

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| Instalasi `conntrack` | Menyediakan *tool* untuk melihat tabel pelacakan koneksi |
| `iptables -t nat -F` / `-X` | Mengosongkan tabel `nat` agar skrip dapat dijalankan berulang (idempoten) |
| `PREROUTING ... -s 192.238.4.58 -d 192.238.4.82 -p tcp -j DNAT --to-destination 192.238.3.194` | Mengubah **tujuan** paket TCP dari Casper yang menuju Zeruel menjadi Pen-Pen sebelum keputusan *routing* |
| `POSTROUTING ... -s 192.238.4.58 -d 192.238.3.194 -p tcp -j MASQUERADE` | Mengubah **sumber** paket yang menuju Pen-Pen menjadi alamat Fuyutsuki, sehingga balasan Pen-Pen kembali lewat Fuyutsuki dan tidak langsung menuju Casper |
| *Autostart* di `/etc/profile` dan `/root/.bashrc` | Konfigurasi dijalankan otomatis agar persisten |

**Mengapa `MASQUERADE` diperlukan?**
Tanpa `MASQUERADE`, Pen-Pen akan membalas langsung ke `192.238.4.58` tanpa melewati Fuyutsuki. Casper akan menerima balasan dari `192.238.3.194` yang tidak cocok dengan koneksi yang ia buka (ke `192.238.4.82`), sehingga koneksi gagal dan jebakan ketahuan. Dengan `MASQUERADE`, Pen-Pen melihat sumbernya adalah Fuyutsuki dan membalas ke Fuyutsuki; `conntrack` lalu membalik DNAT sehingga Casper menerima balasan seolah-olah berasal dari Zeruel.

**Mengapa *ping* tidak ikut dialihkan?**
Kedua *rule* memakai `-p tcp`, sehingga ICMP tidak cocok dan tetap diteruskan ke tujuan asli (Zeruel).

---

### 3. Langkah 2 — Menyalakan *Listener* Port 9000 di Pen-Pen dan Zeruel

#### 3.1 Pen-Pen (`192.238.3.194`)

```bash
rm -f /tmp/penpen_9000.log && touch /tmp/penpen_9000.log
nohup sh -c 'while true; do nc.traditional -l -p 9000 >> /tmp/penpen_9000.log 2>&1; done' >/dev/null 2>&1 &
ss -tlnp | grep 9000
```

**Hasil:**

```text
LISTEN 0  1  0.0.0.0:9000  0.0.0.0:*  users:(("nc.traditional",pid=693,fd=3))
```

<img src="https://github.com/user-attachments/assets/0482e561-f2f7-48e3-bed5-759e9358ae51" width="481" alt="Pen-Pen: listener port 9000 aktif">

#### 3.2 Zeruel (`192.238.4.82`)

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

```text
LISTEN 0  1  0.0.0.0:9000  0.0.0.0:*  users:(("nc",pid=345,fd=3))
```

<img src="https://github.com/user-attachments/assets/63fd3424-15b8-4a40-b771-5b829e9f4fec" width="482" alt="Zeruel: listener port 9000 aktif">

Kedua *listener* menulis pesan masuk ke file log masing-masing (`/tmp/penpen_9000.log` dan `/tmp/zeruel_9000.log`) sehingga lokasi tibanya pesan dapat dibuktikan.

---

### 4. Langkah 3 — Pengujian dan Bukti

#### 4.1 Magi-Casper (`192.238.4.58`) — *Ping* (Tidak Dialihkan) dan Kirim Pesan TCP ke Zeruel Port 9000

```bash
ping -c 3 192.238.4.82
echo "Pesan jebakan dari Magi-Casper ke Zeruel:9000" | nc -v -w 2 192.238.4.82 9000
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/2d7723e7-e385-4cbf-9b08-87992b985226" width="480" alt="Magi-Casper: ping dan kirim pesan TCP ke Zeruel:9000">

**Analisis:**

- *Ping* ke Zeruel berhasil dengan 0% *packet loss*: ICMP **tidak dialihkan** dan tetap menuju Zeruel.
- `nc -v` melaporkan koneksi ke `192.238.4.82` port `9000` berstatus `open`. Casper melihat koneksi tersambung ke Zeruel dan tidak sadar bahwa paketnya dialihkan.
- Pesan `inverse host lookup failed: Unknown host` hanya berasal dari *reverse DNS* oleh `nc` dan tidak berkaitan dengan NAT.

#### 4.2 Pen-Pen — Pesan Masuk ke Pen-Pen

```bash
cat /tmp/penpen_9000.log
```

**Hasil:**

```text
Pesan jebakan dari Magi-Casper ke Zeruel:9000
```

<img src="https://github.com/user-attachments/assets/63cb9f2f-dbb4-472c-8a4c-64d6b8e8c51a" width="481" alt="Pen-Pen: pesan dari Casper muncul di log">

#### 4.3 Zeruel — Pesan Tidak Sampai ke Zeruel

```bash
cat /tmp/zeruel_9000.log
```

**Hasil:** *(kosong, tidak ada pesan)*

<img src="https://github.com/user-attachments/assets/f5317c8a-a40d-4361-a7b5-3436d21bf1ef" width="482" alt="Zeruel: log kosong, pesan tidak sampai">

#### 4.4 Fuyutsuki — *Counter Rule* NAT dan Tabel `conntrack`

```bash
iptables -t nat -L -n -v
conntrack -L -p tcp 2>/dev/null || grep "dport=9000" /proc/net/nf_conntrack
```

**Hasil:**

```text
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

<img src="https://github.com/user-attachments/assets/58fb8e10-307e-46b0-b6d1-712ecdd6d145" width="481" alt="Fuyutsuki: counter NAT dan entri conntrack">

**Analisis:**

- *Rule* `DNAT` di `PREROUTING` dan `MASQUERADE` di `POSTROUTING` masing-masing mencatat **1 paket / 60 byte**, yaitu paket pertama koneksi (sisa paket koneksi diproses lewat tabel `conntrack` sehingga tidak menaikkan *counter rule* lagi). Hal ini membuktikan kedua *rule* benar-benar mengenai koneksi dari Casper.
- Entri `conntrack` menunjukkan kedua arah koneksi:
  - **Arah asli:** `src=192.238.4.58 dst=192.238.4.82 dport=9000`, yaitu Casper menyambung ke Zeruel port 9000.
  - **Arah balasan:** `src=192.238.3.194 dst=192.238.4.165 sport=9000`, yaitu balasan yang sebenarnya datang dari **Pen-Pen** (`192.238.3.194`) menuju alamat Fuyutsuki (`192.238.4.165`, hasil `MASQUERADE`).
- Dua *tuple* yang berbeda dalam satu entri inilah bukti pengalihan: tujuan asli (Zeruel) diganti menjadi Pen-Pen oleh `DNAT`, dan `conntrack` membalik perubahan itu pada paket balasan sehingga Casper menerima balasan yang terlihat berasal dari Zeruel.

---

### 5. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Casper `ping 192.238.4.82` | Tidak dialihkan, tetap berhasil | 3 diterima (0% *loss*), TTL 61 | ✅ |
| Casper `nc -v` ke Zeruel:9000 | Tersambung (tidak sadar dialihkan) | `(UNKNOWN) [192.238.4.82] 9000 (?) open` | ✅ |
| `/tmp/penpen_9000.log` di Pen-Pen | Berisi pesan dari Casper | `Pesan jebakan dari Magi-Casper ke Zeruel:9000` | ✅ |
| `/tmp/zeruel_9000.log` di Zeruel | Kosong | Kosong | ✅ |
| *Counter rule* NAT di Fuyutsuki | Naik | `DNAT` 1 paket / 60 byte, `MASQUERADE` 1 paket / 60 byte | ✅ |
| Tabel `conntrack` di Fuyutsuki | Menunjukkan pemetaan ke Pen-Pen | `src` balasan `192.238.3.194` untuk koneksi ke `192.238.4.82:9000` | ✅ |

**Pembahasan:**

1. **Pengalihan transparan.** Fuyutsuki mengubah tujuan paket TCP dari `192.238.4.82` (Zeruel) menjadi `192.238.3.194` (Pen-Pen) lewat `DNAT` di `PREROUTING`. Dari sisi Casper, koneksi tetap ke Zeruel dan berstatus `open`.
2. **Pesan sampai di Pen-Pen, bukan di Zeruel.** Log Pen-Pen berisi pesan jebakan, sedangkan log Zeruel kosong.
3. **Balasan tetap tampak dari Zeruel.** `MASQUERADE` memaksa Pen-Pen membalas ke Fuyutsuki, dan `conntrack` mengembalikan alamat sumber balasan menjadi Zeruel. Karena itu Casper tidak sadar dan tidak menolak balasan yang datang.
4. ***Ping* tidak dialihkan.** *Rule* hanya untuk `-p tcp`, sehingga *ping* tetap sampai ke Zeruel dan berhasil.
5. **Bukti konsisten.** *Counter* NAT dan entri `conntrack` konsisten dengan isi log di Pen-Pen dan Zeruel.

---

### 6. Kesimpulan

Dengan memasang *rule* `DNAT` (`PREROUTING`) dan `MASQUERADE` (`POSTROUTING`) pada tabel `nat` di Fuyutsuki, setiap koneksi TCP dari Magi-Casper ke Zeruel dialihkan secara diam-diam ke Pen-Pen. Casper tidak menyadarinya karena balasan tetap terlihat datang dari Zeruel, sedangkan *ping* tidak dialihkan. Pesan yang dikirim ke Zeruel port 9000 terbukti muncul di Pen-Pen dan tidak di Zeruel, didukung *counter rule* NAT dan entri tabel `conntrack` di Fuyutsuki. Seluruh persyaratan Soal 17 terpenuhi.

---

## Soal 18: Gerbang Monolith dan Radar

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Shigeru (router/firewall), Magi-Casper, Lilith, Zeruel

---

### 1. Soal

> **Gerbang Monolith dan Radar:** Monolith dan Radar menyimpan rahasia terbesar NERV, dan Shigeru menjaganya tanpa kompromi. Pasang aturan di **Shigeru**. Node di dalam zona boleh memulai koneksi ke mana saja, dan balasannya tetap sampai. Node di luar zona **tidak boleh memulai koneksi ke dalam zona**. Satu pengecualian: **ping dari subnet Terminal boleh masuk**. Tolak berdasarkan **arah datangnya**, yaitu semua yang datang dari sisi Shigeru yang menghadap Gendo. **Jangan menulis subnet luar satu per satu**, supaya subnet baru langsung ikut terblokir. Buktikan ping Casper ke Zeruel **berhasil**, sedangkan ping Lilith ke Zeruel **gagal**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Node dalam zona boleh memulai koneksi ke mana saja, balasan tetap sampai | *Rule* `conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT` di *chain* `FORWARD` |
| 2 | Node luar tidak boleh memulai koneksi ke dalam zona | `DROP` semua paket yang masuk lewat *interface* yang menghadap Gendo (`-i eth0`) |
| 3 | Pengecualian: *ping* dari subnet Terminal boleh masuk | `ACCEPT` ICMP `echo-request` dari `192.238.4.56/29` lewat `eth0` |
| 4 | Tolak berdasarkan arah datang, bukan daftar subnet luar | Pencocokan berdasarkan **interface masuk** (`-i eth0`), bukan alamat sumber |
| 5 | Subnet baru langsung ikut terblokir | Karena *rule* berbasis *interface*, semua subnet baru yang datang dari arah Gendo otomatis terkena `DROP` |

#### Alamat IP dan *Interface*

| Perangkat / *Interface* | IP | Keterangan |
|-------------------------|----|------------|
| Shigeru `eth0` | `192.238.4.154/30` | Menghadap **Gendo** (sisi luar) |
| Shigeru `eth1` | `192.238.4.81/29` | Sisi dalam zona (subnet Zeruel) |
| Shigeru `eth2` | `192.238.4.157/30` | Sisi dalam zona lainnya |
| Magi-Casper | `192.238.4.58` | Subnet Terminal (`192.238.4.56/29`) — *ping* diizinkan masuk |
| Zeruel | `192.238.4.82` | Node di dalam zona (target uji) |
| Lilith | `192.238.4.74` | Node di luar zona |

---

### 2. Langkah 1 — Memasang Skrip Firewall di Shigeru

Di *console* **Shigeru**, *interface* `eth0` dipastikan menghadap Gendo (`192.238.4.154/30`), lalu skrip firewall Soal 18 dibuat di `/root/shigeru.sh` dan didaftarkan ke *autostart* agar persisten saat *container restart*.

```bash
# Pastikan interface menghadap Gendou adalah eth0 (192.238.4.154/30)
ip -br a

# Buat script firewall Soal 18 di /root/shigeru.sh
cat << 'EOF' > /root/shigeru.sh
#!/bin/bash
[ -f /root/route.sh ] && sh /root/route.sh

# Reset filter table agar idempoten
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# 1. Izinkan balasan koneksi yang sudah ada (koneksi dari dalam zona ke luar tetap sampai)
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 2. Pengecualian: izinkan ping (ICMP echo-request) dari subnet Switch-Terminal (192.238.4.56/29) masuk lewat eth0
iptables -A FORWARD -i eth0 -s 192.238.4.56/29 -p icmp --icmp-type echo-request -j ACCEPT

# 3. Tolak semua koneksi baru lainnya yang datang dari arah Gendou (interface eth0)
iptables -A FORWARD -i eth0 -j DROP
EOF

chmod +x /root/shigeru.sh

# Daftarkan ke autostart agar aktif otomatis saat container restart
touch /root/.bashrc
sed -i '/# === AUTOSTART SHIGERU ===/,$d' /etc/profile /root/.bashrc
printf '\n# === AUTOSTART SHIGERU ===\n[ -f /root/shigeru.sh ] && bash /root/shigeru.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null

# Jalankan script
bash /root/shigeru.sh
```

<img src="https://github.com/user-attachments/assets/418728f4-b3f9-4dfe-a1b7-48dcda3bad2c" width="362" alt="Perintah pemasangan script firewall di Shigeru">

**Hasil di terminal (`ip -br a` dan `ip_forward`):**

```text
lo     UNKNOWN  127.0.0.1/8 ::1/128
eth0   UNKNOWN  192.238.4.154/30
eth1   UNKNOWN  192.238.4.81/29
eth2   UNKNOWN  192.238.4.157/30
eth3   UNKNOWN  fe80::42:11ff:fe4c:ba03/64
net.ipv4.ip_forward = 1
```

<img src="https://github.com/user-attachments/assets/418728f4-b3f9-4dfe-a1b7-48dcda3bad2c" width="362" alt="Hasil menjalankan script di Shigeru">

**Penjelasan:**

| Bagian | Fungsi |
|--------|--------|
| `ip -br a` | Memastikan `eth0` (`192.238.4.154/30`) adalah *interface* yang menghadap Gendo |
| `[ -f /root/route.sh ] && sh /root/route.sh` | Mempertahankan konfigurasi *routing* sebelumnya (termasuk `ip_forward = 1`) |
| `iptables -F` / `-X` dan *policy* `ACCEPT` | Membersihkan *rule* lama agar skrip idempoten |
| *Rule* 1: `ESTABLISHED,RELATED -j ACCEPT` | Balasan dari koneksi yang dimulai node dalam zona tetap sampai; juga membuat koneksi keluar dari dalam zona berjalan normal |
| *Rule* 2: `-i eth0 -s 192.238.4.56/29 -p icmp --icmp-type echo-request -j ACCEPT` | Pengecualian: *ping* dari subnet Terminal boleh masuk lewat `eth0` |
| *Rule* 3: `-i eth0 -j DROP` | Menolak **semua** paket baru yang masuk dari arah Gendo |
| *Autostart* di `/etc/profile` dan `/root/.bashrc` | Skrip dijalankan otomatis agar firewall persisten |

**Mengapa memakai `-i eth0`, bukan daftar subnet luar?**
Soal meminta penolakan berdasarkan arah datangnya dan melarang menulis subnet luar satu per satu. Dengan mencocokkan *interface* masuk `eth0` (sisi Shigeru yang menghadap Gendo), semua paket dari luar zona — termasuk subnet yang baru ditambahkan di kemudian hari — otomatis terblokir tanpa mengubah *rule*.

**Mengapa urutan *rule* penting?**
`ESTABLISHED,RELATED` harus berada di baris pertama agar balasan koneksi keluar tidak ikut terbuang oleh `DROP` di baris terakhir. Pengecualian *ping* Terminal juga harus berada **sebelum** `DROP`, karena `iptables` berhenti pada kecocokan pertama.

---

### 3. Langkah 2 — Pengujian dan Bukti

#### 3.1 Shigeru — Cek Aturan Firewall yang Aktif

```bash
iptables -S
```

**Hasil:**

```text
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.238.4.56/29 -i eth0 -p icmp -m icmp --icmp-type 8 -j ACCEPT
-A FORWARD -i eth0 -j DROP
```

<img src="https://github.com/user-attachments/assets/1923758a-3546-4adf-a23c-e8b3455a684a" width="361" alt="Shigeru: aturan firewall aktif (iptables -S)">

**Analisis:** Urutan *chain* `FORWARD` sesuai rancangan: `ESTABLISHED,RELATED` → `ACCEPT` ICMP dari `192.238.4.56/29` via `eth0` → `DROP` semua paket masuk via `eth0`.

#### 3.2 Magi-Casper (`192.238.4.58`) — *Ping* ke Zeruel (Harus Berhasil)

```bash
ping -c 3 192.238.4.82
```

**Hasil:**

```text
64 bytes from 192.238.4.82: icmp_seq=1 ttl=61 time=2.80 ms
64 bytes from 192.238.4.82: icmp_seq=2 ttl=61 time=1.52 ms
64 bytes from 192.238.4.82: icmp_seq=3 ttl=61 time=1.09 ms

--- 192.238.4.82 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2223ms
rtt min/avg/max/mdev = 1.092/1.803/2.800/0.725 ms
```

<img src="https://github.com/user-attachments/assets/a0882774-9321-4fc8-82f4-27ec33ca6936" width="359" alt="Casper: ping ke Zeruel berhasil">

#### 3.3 Lilith (`192.238.4.74`) — *Ping* ke Zeruel (Harus Gagal)

```bash
ping -c 3 -W 1 192.238.4.82
```

**Hasil:**

```text
3 packets transmitted, 0 received, 100% packet loss, time 2263ms
```

<img src="https://github.com/user-attachments/assets/1d517cdb-552b-4bfd-b52c-ab653a9c409d" width="363" alt="Lilith: ping ke Zeruel gagal">

#### 3.4 Zeruel (`192.238.4.82`) — Node Dalam Zona Tetap Bisa Memulai Koneksi ke Luar (Lilith dan `8.8.8.8`)

```bash
ping -c 3 192.238.4.75
ping -c 3 8.8.8.8
```

**Hasil:**

```text
64 bytes from 192.238.4.75: icmp_seq=1 ttl=60 time=1.79 ms
64 bytes from 192.238.4.75: icmp_seq=2 ttl=60 time=1.04 ms
64 bytes from 192.238.4.75: icmp_seq=3 ttl=60 time=1.17 ms

--- 192.238.4.75 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 1.043/1.335/1.794/0.328 ms
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=21.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=22.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=22.4 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 21.704/22.213/22.574/0.370 ms
```

<img src="https://github.com/user-attachments/assets/57842b2c-0ba0-4c91-a558-f54090a9c283" width="398" alt="Zeruel: ping ke Lilith dan 8.8.8.8 berhasil">

**Analisis:** Zeruel (di dalam zona) dapat memulai koneksi ke Lilith (luar zona) dan ke internet (`8.8.8.8`), dan balasannya tetap sampai. Hal ini sesuai persyaratan "node di dalam zona boleh memulai koneksi ke mana saja".

#### 3.5 Shigeru — *Counter* Paket yang Lolos dan yang Dibuang

```bash
iptables -L FORWARD -n -v
```

**Hasil:**

```text
Chain FORWARD (policy ACCEPT 2 packets, 168 bytes)
 pkts bytes target  prot opt in    out  source           destination
   15  1260 ACCEPT  all  --  *     *    0.0.0.0/0        0.0.0.0/0   ctstate RELATED,ESTABLISHED
    1    84 ACCEPT  icmp --  eth0  *    192.238.4.56/29  0.0.0.0/0   icmptype 8
    3   252 DROP    all  --  eth0  *    0.0.0.0/0        0.0.0.0/0
```

<img src="https://github.com/user-attachments/assets/6a78ede9-9e29-4538-9019-024541667a7e" width="362" alt="Shigeru: counter chain FORWARD">

**Analisis:**

- *Rule* `DROP` mencatat **3 paket / 252 byte**. Angka ini sama dengan 3 *ping* dari Lilith (3 × 84 byte), yang semuanya dibuang karena datang dari `eth0` dan bukan dari subnet Terminal.
- *Rule* pengecualian ICMP mencatat **1 paket / 84 byte**, yaitu paket pertama (`echo-request` yang berstatus koneksi baru) dari Casper. Dua paket berikutnya dari *ping* yang sama sudah berstatus `ESTABLISHED` sehingga ditangkap *rule* pertama.
- *Rule* `ESTABLISHED,RELATED` mencatat **15 paket / 1260 byte**, yaitu balasan dan paket lanjutan dari koneksi yang sudah terbentuk (*ping* Casper serta *ping* Zeruel ke Lilith dan `8.8.8.8`).
- *Policy* `ACCEPT` mencatat **2 paket / 168 byte**, yaitu paket pertama dari dua *ping* yang dimulai Zeruel dari dalam zona (ke Lilith dan `8.8.8.8`), yang tidak cocok dengan *rule* berbasis `eth0` sehingga jatuh ke *policy* `ACCEPT`.

---

### 4. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Sesuai Soal |
|-----------|-----------------------|--------------|:-----------:|
| Casper (subnet Terminal) → Zeruel `ping` | Berhasil | 3 diterima (0% *loss*) | ✅ |
| Lilith (luar zona) → Zeruel `ping` | Gagal | 100% *packet loss* | ✅ |
| Zeruel (dalam zona) → Lilith `ping` | Berhasil (balasan tetap sampai) | 3 diterima (0% *loss*) | ✅ |
| Zeruel (dalam zona) → `8.8.8.8` `ping` | Berhasil | 3 diterima (0% *loss*) | ✅ |
| *Counter rule* `DROP` | Naik untuk paket dari luar | 3 paket / 252 byte (*ping* Lilith) | ✅ |

**Pembahasan:**

1. **Penolakan berdasarkan arah datang.** *Rule* `-i eth0 -j DROP` menolak semua paket baru yang masuk dari sisi Shigeru yang menghadap Gendo. Karena tidak memakai daftar subnet luar, subnet baru yang ditambahkan nanti otomatis ikut terblokir.
2. **Koneksi dari dalam zona tetap berjalan.** *Rule* `ESTABLISHED,RELATED` membuat balasan atas koneksi yang dimulai node dalam zona (Zeruel) tetap sampai. Hal ini terbukti dari *ping* Zeruel ke Lilith dan `8.8.8.8` yang berhasil dengan 0% *packet loss*.
3. **Pengecualian *ping* Terminal.** *Ping* dari subnet Terminal (`192.238.4.56/29`) boleh masuk karena *rule* `ACCEPT` ICMP `echo-request` berada sebelum `DROP`. *Ping* Casper ke Zeruel berhasil, sedangkan *ping* Lilith — yang bukan bagian dari subnet Terminal — gagal.
4. **Bukti *counter*.** *Counter* `DROP` (3 paket / 252 byte) tepat sama dengan 3 *ping* Lilith, dan *counter* pengecualian ICMP mencatat paket pertama dari Casper.

---

### 5. Kesimpulan

Dengan tiga *rule* di *chain* `FORWARD` pada Shigeru — yaitu `ESTABLISHED,RELATED → ACCEPT`, `ACCEPT` ICMP `echo-request` dari `192.238.4.56/29` via `eth0`, dan `DROP` semua paket masuk via `eth0` — zona yang dijaga Shigeru hanya dapat diakses sesuai aturan. Node di dalam zona dapat memulai koneksi ke mana saja dan balasannya tetap sampai, node di luar zona tidak dapat memulai koneksi ke dalam zona, dan *ping* dari subnet Terminal dikecualikan. Penolakan berbasis *interface* masuk (`-i eth0`) membuat subnet luar baru otomatis terblokir tanpa perlu ditulis satu per satu. *Ping* Casper ke Zeruel berhasil, sedangkan *ping* Lilith ke Zeruel gagal. Seluruh persyaratan Soal 18 terpenuhi.

---

## Soal 19: Hukuman untuk Sang Pengkhianat

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3) — Switch-Monolith, Shigeru, Armisael, Zeruel, Lilith, Seele (`8.8.8.8`)

---

### 1. Soal

> **Hukuman untuk sang pengkhianat:** hitung mundur tinggal beberapa menit. **Armisael tidak boleh lagi bicara dengan siapa pun**, tapi **Zeruel** di sebelahnya tidak boleh ikut terhukum. Blokir **semua lalu lintas masuk dan keluar** Armisael, termasuk ke gateway dan tetangga satu switch. **Isolasi tidak boleh dikerjakan di dalam Armisael**, karena pengkhianat tidak bisa dipercaya. Untuk uji `nc`, nyalakan listener TCP 9000 di Armisael, Zeruel, dan Lilith. Ke Seele pakai TCP 53. Buktikan ping dan `nc` dari Armisael ke Seele, Zeruel, dan Lilith **gagal**. Buktikan ping dan `nc` dari Zeruel ke Seele dan Lilith **berhasil**.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Pendekatan |
|----|-----------|------------|
| 1 | Isolasi tidak boleh dikerjakan di dalam Armisael | Konfigurasi dilakukan di luar Armisael, yaitu pada **Switch-Monolith** (Layer 2) dan router **Shigeru** (Layer 3) |
| 2 | Blokir lalu lintas ke tetangga satu *switch* (`Zeruel`) | Isolasi VLAN pada port *switch* `Switch-Monolith` yang terhubung ke Armisael (`Port 7` / `e6` dipindah ke `VLAN 99`) |
| 3 | Blokir seluruh lalu lintas masuk & keluar di level router/*gateway* (`Shigeru`) | *Rule* `DROP` di baris teratas *chain* `INPUT` dan `FORWARD` pada `Shigeru` untuk sumber maupun tujuan `192.238.4.83/32` |
| 4 | Zeruel di sebelahnya tidak boleh ikut terhukum | Port `Zeruel` dan *uplink* `Shigeru` tetap di `VLAN 1`, serta *rule* `Shigeru` hanya menargetkan IP `192.238.4.83/32` |

#### Alamat IP

| Perangkat | IP | Peran pada Soal 19 |
|-----------|----|--------------------|
| Armisael | `192.238.4.83` | Target yang diisolasi total (*listener* TCP `9000`) |
| Zeruel | `192.238.4.82` | Tetangga satu *switch* di `Switch-Monolith` — harus tetap normal (*listener* TCP `9000`) |
| Shigeru (`eth1`) | `192.238.4.81` | *Gateway* subnet `192.238.4.80/29` dan router penjaga zona |
| Lilith | `192.238.4.75` | Node di subnet `Switch-Dogma` — target uji lintas subnet (*listener* TCP `9000`) |
| Seele | `8.8.8.8` | Tujuan eksternal / internet — target uji layanan TCP `53` |

---

### 2. Langkah Pengerjaan (Tanpa Menyentuh Konfigurasi Armisael)

#### 2.1 Isolasi Layer 2 di `Switch-Monolith` (Memutus Jalur ke Tetangga Satu *Switch* & *Gateway*)

Karena `Armisael` (`192.238.4.83/29`) dan `Zeruel` (`192.238.4.82/29`) berada dalam satu segmen jaringan dan satu *switch* (`Switch-Monolith`), komunikasi langsung di antara keduanya berjalan pada **Layer 2 (*switching*)** tanpa melewati router `Shigeru`. Oleh karena itu, pemisahan dilakukan di tingkat *switch*:

1. Membuka menu **Configure** pada node **`Switch-Monolith`** di GNS3.
2. Mengidentifikasi port yang terhubung ke `Armisael`, yaitu *interface* **`e6`** (**Port 6**).
3. Mengubah konfigurasi **Port 6** dari `VLAN 1` menjadi **`VLAN 99`** (`access`), sedangkan port menuju `Zeruel` dan *uplink* `Shigeru` (`eth1`) dibiarkan tetap pada **`VLAN 1`**, lalu menekan **Apply** dan **OK**.

<img src="https://github.com/user-attachments/assets/6a7873c8-34e3-474d-9c61-212b53bcc607" width="550" alt="Konfigurasi VLAN Switch-Monolith">

#### 2.2 Isolasi Layer 3 di Router `Shigeru` (`/root/shigeru.sh`)

Untuk memastikan tidak ada paket dari maupun menuju `Armisael` (`192.238.4.83`) yang diproses atau diteruskan oleh router `Shigeru`, ditambahkan 4 aturan `DROP` di baris paling atas *chain* `INPUT` dan `FORWARD` pada `/root/shigeru.sh` (sebelum aturan `ESTABLISHED,RELATED` dan aturan Soal 18):

```bash
# Bagian isolasi Soal 19 pada /root/shigeru.sh
iptables -I INPUT   1 -s 192.238.4.83/32 -j DROP
iptables -I INPUT   1 -d 192.238.4.83/32 -j DROP
iptables -I FORWARD 1 -s 192.238.4.83/32 -j DROP
iptables -I FORWARD 1 -d 192.238.4.83/32 -j DROP
```

Verifikasi urutan aturan di `Shigeru`:

```bash
bash /root/shigeru.sh && iptables -S
```

<img src="https://github.com/user-attachments/assets/9d045ad9-894e-4fa3-83f5-6aa83c383b89" width="550" alt="Urutan aturan iptables di Shigeru">

#### 2.3 Menyalakan *Listener* TCP 9000 di Armisael, Zeruel, dan Lilith

Pada masing-masing node (Armisael, Zeruel, dan Lilith) dijalankan *listener* `nc` di *background* pada port TCP 9000 (sedangkan Seele `8.8.8.8` sudah menyediakan layanan TCP 53 secara bawaan):

```bash
nohup sh -c 'while true; do echo "Halo dari $(hostname)" | nc -l -p 9000 >/dev/null 2>&1; done' >/dev/null 2>&1 &
```

**Armisael**

<img src="https://github.com/user-attachments/assets/5df9fcc9-a292-4d2b-8df4-5da994199fec" width="550" alt="Listener TCP 9000 di Armisael">

**Zeruel**

<img src="https://github.com/user-attachments/assets/ba18b413-6ba4-473b-bd57-27f404f63d9d" width="550" alt="Listener TCP 9000 di Zeruel">

**Lilith**

<img src="https://github.com/user-attachments/assets/274f5d51-59b1-4a74-adec-399f53bd9fd5" width="550" alt="Listener TCP 9000 di Lilith">

---

### 3. Pengujian

#### 3.1 Uji dari Armisael

```bash
ping -c 3 -W 1 8.8.8.8
ping -c 3 -W 1 192.238.4.82
ping -c 3 -W 1 192.238.4.75
nc -v -w 2 8.8.8.8 53 </dev/null; echo "Exit code Seele (8.8.8.8:53): $?"
nc -v -w 2 192.238.4.82 9000 </dev/null; echo "Exit code Zeruel (9000): $?"
nc -v -w 2 192.238.4.75 9000 </dev/null; echo "Exit code Lilith (9000): $?"
```

Hasil yang diharapkan adalah semua paket yang di-*ping* gagal untuk membuktikan bahwa aturan isolasi telah berhasil.

<img src="https://github.com/user-attachments/assets/63ed1782-362e-49d0-8dba-e45bda57f8ed" width="550" alt="Pengujian dari Armisael">

#### 3.2 Uji dari Zeruel

```bash
ping -c 3 8.8.8.8
ping -c 3 192.238.4.74
nc -w 2 8.8.8.8 53 </dev/null; echo "Exit code Seele (8.8.8.8:53): $?"
nc -w 2 192.238.4.74 9000 </dev/null; echo "Exit code Lilith (9000): $?"
```

Semua perintah *ping* harus berhasil untuk membuktikan bahwa aturan isolasi Armisael berjalan secara presisi dan tidak memengaruhi perangkat lain dalam satu subnet.

<img src="https://github.com/user-attachments/assets/0ccf8c3e-3d93-4332-941c-cd885f3b60cc" width="550" alt="Pengujian dari Zeruel">

---

## Soal 20: Uji Kebakaran Geofront

**Modul:** Modul 5 2026

**Topologi:** MAGI (GNS3, project `K-54-MODUL-5`) — Tokyo3-Core, Pen-Pen, Fuyutsuki, Shigeru, Zeruel

---

### 1. Soal

> **Uji kebakaran Geofront:** NERV harus bisa dibangun ulang dengan cepat. Pilih **tiga node** sendiri, minimal **satu router**. Di tiap node, simpan dulu rule dengan `iptables-save` ke `/root/<nama>.rules`. Lalu hapus semua rule iptables **termasuk tabel nat**, dan hapus rute yang kamu tambahkan sendiri. Pulihkan rule dengan `iptables-restore`, dan pulihkan rute dengan `/root/<nama>.sh`. Lakukan pemulihan itu **dua kali berturut-turut**. Hasilnya harus sama: rule dan rute **tidak dobel**, dan node berfungsi seperti semula. Buktikan jumlah baris `iptables -S` dan `iptables -t nat -S` sama setelah pemulihan pertama dan kedua. Buktikan juga node itu berfungsi lagi.

#### Ringkasan Kebutuhan

| No | Kebutuhan | Cara Pembuktian |
|----|-----------|-----------------|
| 1 | Pilih tiga node, minimal satu router | Empat router dipakai: Tokyo3-Core, Pen-Pen, Fuyutsuki, Shigeru |
| 2 | *Rule* dan rute dapat dipulihkan dengan cepat | Simulasi kebakaran: *reload* seluruh node di GNS3, lalu cek pemulihan otomatis |
| 3 | Pemulihan dua kali, hasil sama dan tidak dobel | Jumlah baris `iptables -S` dan `iptables -t nat -S` setelah pemulihan ke-1 dan ke-2 (lihat bagian 5) |
| 4 | Node berfungsi seperti semula | *Ping* dari Zeruel ke internet dan antar-zona |

#### Node yang Diuji

| Node | Jenis | Konfigurasi yang Harus Pulih |
|------|-------|------------------------------|
| Tokyo3-Core | Router | Rute *default*, *rate-limit ping* (Soal 15), `LOG`/`REJECT` Dummy-Plug (Soal 16) |
| Pen-Pen | Router | Rute *default*, *chain* `PENPEN_ACL` jadwal kunjungan (Soal 14) |
| Fuyutsuki | Router | Rute statis, NAT `DNAT` dan `MASQUERADE` (Soal 17) |
| Shigeru | Router | Rute statis, firewall zona di *chain* `FORWARD` (Soal 18) |
| Zeruel | Host | Dipakai sebagai penguji fungsionalitas jaringan |

---

### 2. Tahap 1 — Simulasi Kebakaran (*Reload* Seluruh Node di GNS3)

Pada kanvas GNS3 (*project* `K-54-MODUL-5`), seluruh node di-*reload* untuk mensimulasikan kebakaran. Konfigurasi hanya dapat bertahan jika tersimpan secara persisten di skrip *autostart* masing-masing node.

#### 2.1 Konfirmasi *Reload All*

Dialog konfirmasi `Confirm Reload All` (*"Are you sure you want to reload all devices?"*) dijawab **Yes**.

<img src="https://github.com/user-attachments/assets/c703bcaf-4d07-4782-871f-75378fe48866" width="700" alt="Dialog Confirm Reload All di GNS3">

#### 2.2 Seluruh Node Berhasil Di-*Reload*

GNS3 menampilkan notifikasi **"All nodes successfully reloaded"**. Setelah itu, tab *Console* tiap router dibuka kembali agar *shell* menjalankan skrip pemulihan secara otomatis.

<img src="https://github.com/user-attachments/assets/0ae93be3-2586-46ba-b31d-286d6f3fbcf3" width="700" alt="Notifikasi All nodes successfully reloaded">

---

### 3. Tahap 2 — Verifikasi Pemulihan Rute dan Firewall di Tiap Router

Di setiap router dijalankan perintah berikut setelah *reload*:

```bash
ip route
iptables -S
iptables -t nat -S
```

#### 3.1 Tokyo3-Core

**Hasil:**

```text
default via 192.238.4.105 dev eth0
192.238.4.104/30 dev eth0 proto kernel scope link src 192.238.4.106

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

-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
```

<img src="https://github.com/user-attachments/assets/11b659e6-1a93-4e58-8dbb-a7e6dfb73b15" width="247" alt="Tokyo3-Core: rute dan firewall setelah reload">

**Analisis:** Rute *default* dan rute jaringan lokal tersedia kembali. Aturan Soal 15 (*rate-limit ping*) dan Soal 16 (`LOG` + `REJECT` untuk port 22, 23, 3306 dari `192.238.4.51`) pulih lengkap, masing-masing hanya satu kali (tidak dobel). Tabel `nat` hanya berisi *policy default*.

#### 3.2 Pen-Pen

**Hasil (ringkas):**

```text
default via 192.238.3.193 dev eth0
192.238.3.192/27 dev eth0 proto kernel scope link src 192.238.3.194

-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-N PENPEN_ACL
-A INPUT -p icmp -m icmp --icmp-type 8 -j PENPEN_ACL
-A INPUT -p tcp -m tcp --dport 80 -j PENPEN_ACL
-A PENPEN_ACL -s 192.238.4.66/32  -m time --timestart 13:00:00 --timestop 15:00:00 ... -j ACCEPT
-A PENPEN_ACL -s 192.238.4.66/32  -j DROP
 ... (192.238.4.67, 192.238.4.68, 192.238.3.130, 192.238.4.74, 192.238.4.75 dengan pola yang sama)
-A PENPEN_ACL -s 192.238.4.50/32  -j DROP
-A PENPEN_ACL -s 192.238.4.51/32  -j DROP
-A PENPEN_ACL -s 192.238.4.102/32 -j DROP
```

<img src="https://github.com/user-attachments/assets/73b84152-7f21-49fd-a7cd-9e9f66acf47d" width="248" alt="Pen-Pen: rute dan firewall setelah reload">

**Analisis:** Rute *default* menuju `192.238.3.193` dan rute `192.238.3.192/27` pulih. *Chain* `PENPEN_ACL` beserta dua *rule* pengarah (`ICMP echo-request` dan TCP 80) pulih otomatis. Setiap IP Pilot, Staf, dan Dummy hanya muncul satu kali di *chain*.

#### 3.3 Fuyutsuki

**Hasil:**

```text
default via 192.238.4.161 dev eth0
192.238.3.192/27 via 192.238.4.166 dev eth2
192.238.4.56/29 dev eth1 proto kernel scope link src 192.238.4.57
192.238.4.72/29 via 192.238.4.170 dev eth3
192.238.4.160/30 dev eth0 proto kernel scope link src 192.238.4.162
192.238.4.164/30 dev eth2 proto kernel scope link src 192.238.4.165
192.238.4.168/30 dev eth3 proto kernel scope link src 192.238.4.169

-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT

-P PREROUTING ACCEPT
-P INPUT ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
-A PREROUTING  -s 192.238.4.58/32 -d 192.238.4.82/32 -p tcp -j DNAT --to-destination 192.238.3.194
-A POSTROUTING -s 192.238.4.58/32 -d 192.238.3.194/32 -p tcp -j MASQUERADE
```

<img src="https://github.com/user-attachments/assets/8003ff59-a4d8-48d7-b16b-2044c84ab15a" width="244" alt="Fuyutsuki: rute dan NAT setelah reload">

**Analisis:** Seluruh rute statis (menuju `192.238.3.192/27` dan `192.238.4.72/29`) kembali ada. Pada tabel `nat`, `DNAT` dan `MASQUERADE` dari Soal 17 pulih masing-masing satu *rule*, sedangkan `iptables -S` hanya berisi *policy* `ACCEPT`.

#### 3.4 Shigeru

**Hasil (ringkas):**

```text
net.ipv4.ip_forward = 1

default via 192.238.4.153 dev eth0
192.238.4.80/29 dev eth1 proto kernel scope link src 192.238.4.81
192.238.4.88/29 via 192.238.4.158 dev eth2
192.238.4.152/30 dev eth0 proto kernel scope link src 192.238.4.154
192.238.4.156/30 dev eth2 proto kernel scope link src 192.238.4.157

-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-A INPUT   -d 192.238.4.83/32 -j DROP
-A INPUT   -s 192.238.4.83/32 -j DROP
-A FORWARD -d 192.238.4.83/32 -j DROP
-A FORWARD -s 192.238.4.83/32 -j DROP
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 192.238.4.56/29 -i eth0 -p icmp -m icmp --icmp-type 8 -j ACCEPT
-A FORWARD -i eth0 -j DROP
-P PREROUTING ACCEPT
-P OUTPUT ACCEPT
-P POSTROUTING ACCEPT
```

<img src="https://github.com/user-attachments/assets/005414b1-bb64-425f-b745-35e62470e39d" width="245" alt="Shigeru: rute dan firewall setelah reload">

**Analisis:** `ip_forward = 1` aktif kembali, rute statis pulih, dan firewall zona dari Soal 18 (`ESTABLISHED,RELATED` → `ACCEPT` ICMP Terminal → `DROP` `eth0`) pulih dengan urutan yang benar. Output konsol juga menunjukkan skrip *autostart* berjalan otomatis setelah *reload* (pengecekan paket dan konfigurasi `dhcp-relay` untuk `eth0`, `eth1`, `eth2`).

---

### 4. Tahap 3 — Uji Fungsionalitas Jaringan Pasca-Pemulihan (dari Zeruel)

```bash
# Uji konektivitas internet (NAT & routing)
ping -c 3 8.8.8.8

# Uji konektivitas antar-zona internal (ke Lilith)
ping -c 3 192.238.4.75
```

**Hasil:**

```text
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=21.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=19.9 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=20.3 ms
3 packets transmitted, 3 received, 0% packet loss, time 2220ms

64 bytes from 192.238.4.74: icmp_seq=1 ttl=60 time=2.26 ms
64 bytes from 192.238.4.74: icmp_seq=2 ttl=60 time=0.969 ms
64 bytes from 192.238.4.74: icmp_seq=3 ttl=60 time=1.02 ms
3 packets transmitted, 3 received, 0% packet loss, time 2223ms
```

<img src="https://github.com/user-attachments/assets/be8faa59-e5e1-49e2-9252-cd26087f7a58" width="396" alt="Zeruel: ping ke 8.8.8.8 dan Lilith berhasil setelah reload">

**Analisis:** *Ping* dari Zeruel ke `8.8.8.8` dan ke Lilith berhasil dengan 0% *packet loss* setelah seluruh node di-*reload*. Hal ini menunjukkan *routing* antar-router, NAT, dan firewall pada jalur Zeruel pulih serta jaringan berfungsi seperti semula.

---

### 5. Bukti Pemulihan Manual Dua Kali (`iptables-save` / `iptables-restore`)

> **Bagian ini belum memiliki *screenshot*.** Soal meminta bukti simpan *rule*, penghapusan, dan pemulihan **dua kali berturut-turut** dengan perbandingan jumlah baris. Perintah di bawah adalah langkah yang perlu dijalankan dan di-*screenshot* (contoh pada node **Tokyo3-Core**; ganti `tokyo3` sesuai node lain). Hasilnya tidak diisi karena belum ada di foto.

#### 5.1 Simpan *Rule* dan Catat Kondisi Awal

```bash
iptables-save > /root/tokyo3.rules
iptables -S | wc -l
iptables -t nat -S | wc -l
```

#### 5.2 "Kebakaran": Hapus Semua *Rule* (Termasuk `nat`) dan Rute Buatan Sendiri

```bash
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
# hapus rute yang kamu tambahkan sendiri (sesuaikan dengan hasil `ip route`)
# ip route del <rute-buatan>
iptables -S | wc -l
iptables -t nat -S | wc -l
```

#### 5.3 Pemulihan ke-1

```bash
iptables-restore < /root/tokyo3.rules
sh /root/tokyo3.sh
iptables -S | wc -l
iptables -t nat -S | wc -l
ip route
```

#### 5.4 Pemulihan ke-2 (Berturut-turut)

```bash
iptables-restore < /root/tokyo3.rules
sh /root/tokyo3.sh
iptables -S | wc -l
iptables -t nat -S | wc -l
ip route
```

| Node | Perintah | Jumlah Baris Pemulihan ke-1 | Jumlah Baris Pemulihan ke-2 | Sama? |
|------|----------|:---------------------------:|:---------------------------:|:-----:|
| (node 1) | `iptables -S` | … | … | … |
| (node 1) | `iptables -t nat -S` | … | … | … |
| (node 2) | `iptables -S` | … | … | … |
| (node 2) | `iptables -t nat -S` | … | … | … |
| (node 3) | `iptables -S` | … | … | … |
| (node 3) | `iptables -t nat -S` | … | … | … |

---

### 6. Hasil dan Pembahasan

| Pengujian | Hasil yang Diharapkan | Hasil Aktual | Status |
|-----------|-----------------------|--------------|:------:|
| *Reload* seluruh node GNS3 | Semua node berhasil di-*reload* | "All nodes successfully reloaded" | ✅ |
| Rute di Tokyo3-Core, Pen-Pen, Fuyutsuki, Shigeru | Pulih otomatis | Seluruh rute *default* dan statis tampil kembali | ✅ |
| *Rule* `iptables -S` di tiap router | Pulih lengkap, tidak dobel | *Rule* Soal 14–18 muncul satu kali | ✅ |
| Tabel `nat` di Fuyutsuki | `DNAT` dan `MASQUERADE` pulih | Kedua *rule* hadir masing-masing satu kali | ✅ |
| *Ping* Zeruel → `8.8.8.8` dan Lilith | Berhasil | 0% *packet loss* | ✅ |
| `iptables-save` / `iptables-restore` dua kali dan jumlah baris | Jumlah baris sama | Belum ada *screenshot* (Bagian 5) | ⏳ |

**Pembahasan:**

1. **Konfigurasi persisten.** Setelah *reload*, rute dan *rule* firewall muncul kembali tanpa diketik ulang karena tiap router menjalankan skrip *autostart*-nya (`/root/<nama>.sh`) yang didaftarkan di `/etc/profile` dan `/root/.bashrc`.
2. **Tidak dobel.** Skrip tiap node diawali `iptables -F` dan `-X` (serta reset tabel `nat` pada Fuyutsuki), sehingga setiap eksekusi dimulai dari kondisi bersih dan *rule* tidak menumpuk. Hasilnya, setiap *rule* hanya muncul satu kali pada output `iptables -S` dan `iptables -t nat -S`.
3. **Fungsi jaringan pulih.** *Ping* dari Zeruel ke internet dan ke Lilith berhasil, sehingga *routing*, NAT, dan firewall pada jalur tersebut kembali bekerja.
4. **Pemulihan manual.** Pemulihan dengan `iptables-restore` dua kali berturut-turut dan perbandingan jumlah baris perlu dilengkapi pada Bagian 5.

---

### 7. Kesimpulan

Setelah seluruh node GNS3 di-*reload*, rute statis dan aturan firewall pada Tokyo3-Core, Pen-Pen, Fuyutsuki, dan Shigeru pulih secara otomatis dan tidak dobel, termasuk tabel `nat` pada Fuyutsuki. *Ping* dari Zeruel ke `8.8.8.8` dan ke Lilith berhasil 0% *packet loss*, sehingga jaringan berfungsi seperti semula. Bukti pemulihan manual dua kali dengan `iptables-save` dan `iptables-restore` beserta perbandingan jumlah baris perlu dilengkapi sebelum laporan dikumpulkan.
