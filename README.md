# Jarkom-Modul-5-2026-K-54

| Nama                 | NRP        |
|----------------------|------------|
| Viko Rizky Fauzan    | 5027251017 |
| Sean Arthur Tamajaya | 5027251050 |

## Laporan Praktikum: Perancangan Subnetting VLSM, Static Routing, NAT, dan Keamanan Jaringan Menggunakan Firewall iptables

## Soal 1: Perancangan Subnetting VLSM (`192.238.0.0/21`)

## 1. Analisis Kebutuhan & Aturan Alokasi

* **Blok IP Induk:** `192.238.0.0/21` (Rentang `192.238.0.0` – `192.238.7.255`, total $2.048$ alamat IP).

* **Total Segmen:** $17\text{ LAN}$ dan $16\text{ Link point-to-point}$ antar-router ($33\text{ subnet}$).

* **Aturan Pengurutan VLSM:** Diurutkan dari kebutuhan alamat terbesar ke terkecil. Apabila ukuran kebutuhan sama, segmen **LAN** diprioritaskan lebih dahulu daripada **link antar-router**, lalu diurutkan sesuai tabel host.

* **Aturan Penomoran Node:** Gateway menggunakan alamat IP *usable* pertama pada subnet, sedangkan host menggunakan alamat IP *usable* berikutnya secara berurutan dari kiri ke kanan sesuai topologi.

## 2. Tabel Pembagian Subnet VLSM dan Pohon IP

Tabel VLSM: 

<img width="1011" height="547" alt="image" src="https://github.com/user-attachments/assets/d31feefc-d4fe-41b5-95b4-6c61d85613cf" />

Gambar Pohon IP:  

<img width="1772" height="4776" alt="pohon_vlsm_192 238 0 0" src="https://github.com/user-attachments/assets/663511e7-19ce-404e-8b39-ec1cb2f126d9" />

## 3. Penjelasan Perhitungan Satu Subnet (Studi Kasus: `Switch-Quarters`)

Sebagai ilustrasi metode perhitungan VLSM yang diterapkan pada seluruh tabel, berikut rincian perhitungan pada segmen **Switch-Quarters**:

1. **Menentukan Total Kebutuhan IP Usable**

   * Berdasarkan tabel host, container `Quarters` membutuhkan $500\text{ host}$.

   * Ditambah $1\text{ alamat IP}$ untuk *default gateway* pada interface router `Rei`.

   * Total alamat IP *usable* yang dibutuhkan:
     

     $$
     \text{Total Kebutuhan Usable} = 500 + 1 = 501\text{ IP}
     $$

2. **Menentukan Panjang Prefix (`/prefix`), Ukuran Blok, dan Subnet Mask**

   * Menggunakan rumus kapasitas host usable $2^h - 2 \ge \text{kebutuhan usable}$ (dikurangi $2$ untuk alamat *Network* dan *Broadcast*):

     * Jika $h = 8 \rightarrow 2^8 - 2 = 254$ (tidak mencukupi $501\text{ IP}$).

     * Jika $h = 9 \rightarrow 2^9 - 2 = 512 - 2 = 510\text{ IP usable}$ (memenuhi syarat $\ge 501$).

   * Dengan jumlah bit host $h = 9$, panjang prefix terkecil yang sesuai adalah:
     

     $$
     \text{Prefix} = 32 - 9 = /23
     $$

   * **Ukuran Blok:** $2^9 = 512\text{ alamat IP}$ (mencakup dua blok `/24` berturut-turut, yaitu lompatan $+2$ pada oktet ketiga).

   * **Subnet Mask:** Representasi biner `/23` adalah `11111111.11111111.11111110.00000000`, sehingga dalam desimal menjadi **`255.255.254.0`**.

3. **Menentukan Alamat Network, Gateway, Range Host, Broadcast, dan Sisa IP**

   * Karena `Switch-Quarters` memiliki kebutuhan terbesar, alokasi dimulai tepat di titik awal blok induk `192.238.0.0/21` dengan nilai **Offset = 0**:

     * **Network Address:** `192.238.0.0/23`

     * **Broadcast Address:** Ukuran blok $512$ menghabiskan oktet ketiga `.0` dan `.1` secara penuh, sehingga alamat terakhir (broadcast) berada di **`192.238.1.255`**.

     * **Range Host (Usable):** `192.238.0.1` s/d `192.238.1.254` ($510\text{ IP}$).

     * **Gateway (Router Rei):** Mengambil alamat *usable* pertama, yaitu **`192.238.0.1`**.

     * **IP Host (`Quarters`):** Mengambil alamat *usable* berikutnya setelah gateway, yaitu **`192.238.0.2`**.

     * **Sisa IP Usable:** $510 - 501 = 9\text{ IP}$.

## 4. Pohon Alokasi IP & Jalur Dummy-Plug ke Armisael

Pada diagram pohon biner di atas, lima subnet yang membentuk jalur paket dari **Dummy-Plug** (`Switch-Lab`) menuju **Armisael** (`Switch-Monolith`) ditandai dengan lingkaran merah:

1. `192.238.4.48/29` (**Switch-Lab** — asal `Dummy-Plug`)

2. `192.238.4.140/30` (**Link Asuka – Ritsuko**)

3. `192.238.4.132/30` (**Link Gendou – Asuka**)

4. `192.238.4.152/30` (**Link Gendou – Shigeru**)

5. `192.238.4.80/29` (**Switch-Monolith** — tujuan `Armisael`)
