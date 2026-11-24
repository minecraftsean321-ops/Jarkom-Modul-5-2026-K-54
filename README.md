# Jarkom-Modul-5-2026-K-54

| Nama                 | NRP        |
|----------------------|------------|
| Viko Rizky Fauzan    | 5027251017 |
| Sean Arthur Tamajaya | 5027251050 |

## Laporan Praktikum: Perancangan Subnetting VLSM, Static Routing, NAT, dan Keamanan Jaringan Menggunakan Firewall iptables

## Soal 1: Perancangan Subnetting VLSM (`192.238.0.0/21`)

### 1. Analisis Kebutuhan & Aturan Alokasi

* **Blok IP Induk:** `192.238.0.0/21` (Rentang `192.238.0.0` – `192.238.7.255`, total $2.048$ alamat IP).

* **Total Segmen:** $17\text{ LAN}$ dan $16\text{ Link point-to-point}$ antar-router ($33\text{ subnet}$).

* **Aturan Pengurutan VLSM:** Diurutkan dari kebutuhan alamat terbesar ke terkecil. Apabila ukuran kebutuhan sama, segmen **LAN** diprioritaskan lebih dahulu daripada **link antar-router**, lalu diurutkan sesuai tabel host.

* **Aturan Penomoran Node:** Gateway menggunakan alamat IP *usable* pertama pada subnet, sedangkan host menggunakan alamat IP *usable* berikutnya secara berurutan dari kiri ke kanan sesuai topologi.

### 2. Tabel Pembagian Subnet VLSM dan Pohon IP

Tabel VLSM: 

<img width="1011" height="547" alt="image" src="https://github.com/user-attachments/assets/d31feefc-d4fe-41b5-95b4-6c61d85613cf" />

Gambar Pohon IP:  

<img width="1772" height="4776" alt="pohon_vlsm_192 238 0 0" src="https://github.com/user-attachments/assets/663511e7-19ce-404e-8b39-ec1cb2f126d9" />

### 3. Penjelasan Perhitungan Satu Subnet (Studi Kasus: `Switch-Quarters`)

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

### 4. Pohon Alokasi IP & Jalur Dummy-Plug ke Armisael

Pada diagram pohon biner di atas, lima subnet yang membentuk jalur paket dari **Dummy-Plug** (`Switch-Lab`) menuju **Armisael** (`Switch-Monolith`) ditandai dengan lingkaran merah:

1. `192.238.4.48/29` (**Switch-Lab** — asal `Dummy-Plug`)

2. `192.238.4.140/30` (**Link Asuka – Ritsuko**)

3. `192.238.4.132/30` (**Link Gendou – Asuka**)

4. `192.238.4.152/30` (**Link Gendou – Shigeru**)

5. `192.238.4.80/29` (**Switch-Monolith** — tujuan `Armisael`)

## Soal 2

### Pengujian 

1. Cek status IP & Interface di tiap router:

   ```
    ip -br a
   ```

   - Gendou

     <img width="863" height="331" alt="image" src="https://github.com/user-attachments/assets/3c47496d-70a1-49ac-a5ce-154ddefe2dbb" />

   - Yui
  
     <img width="726" height="197" alt="image" src="https://github.com/user-attachments/assets/cafe4e71-2046-40d5-ab32-51ea349f2964" />

   - Fuyutsuki
  
     <img width="725" height="168" alt="image" src="https://github.com/user-attachments/assets/2718ddd5-7ba5-4665-bc0d-99ad950d4f56" />

2. Uji ping ke seluruh 16 link router-ke-router (pakai loop satu baris):

  - Gendou ( Uji ke 7 link router cabang utama)

    ```
      for ip in 192.238.4.110 192.238.4.126 192.238.4.134 192.238.4.146 192.238.4.150 192.238.4.154 192.238.4.162; do ping -c 2 -W 1 $ip; done
    ```

    <img width="1060" height="471" alt="image" src="https://github.com/user-attachments/assets/9312e641-2e88-4618-b83f-f554173d3aff" />
    
    <img width="1055" height="587" alt="image" src="https://github.com/user-attachments/assets/92561487-72f0-4398-a97b-86b167986373" />

    <img width="1058" height="279" alt="image" src="https://github.com/user-attachments/assets/63bd8d81-d940-4c63-9d75-d8414e1964fb" />


  - Yui ( Uji link ke Mari, Rei, Shinji)

    ```
      for ip in 192.238.4.114 192.238.4.118 192.238.4.122; do ping -c 2 -W 1 $ip; done
    ```

    <img width="1011" height="571" alt="image" src="https://github.com/user-attachments/assets/9ce67002-a9ab-4dcd-bd01-041fb4bc5cb0" />

  - Hikari ( uji link ke Kaworu)

    ```
      ping -c 2 192.238.4.130
    ```

    <img width="915" height="196" alt="image" src="https://github.com/user-attachments/assets/6df61c6a-0061-401a-b0e4-a5df58fc5a6a" />

  - Asuka (Uji 2 link ke Kensuke & Ritsuko)

    ```
      for ip in 192.238.4.138 192.238.4.142; do ping -c 2 -W 1 $ip; done
    ```

    <img width="890" height="381" alt="image" src="https://github.com/user-attachments/assets/f1c5a472-cd54-49af-bcb4-64b269595d80" />

## Soal 3

### Pengujian 

1. Cek tabel routing

   ```
    ip route
    ping -c 3 8.8.8.8
   ```

   - Gendou

   <img width="1058" height="623" alt="image" src="https://github.com/user-attachments/assets/0fd3a0f0-a2de-429a-a201-cfb789edb28a" />

   <img width="1056" height="490" alt="image" src="https://github.com/user-attachments/assets/d389d812-24a4-4264-9dc8-3d644383c6dd" />

   - Yui
  
     <img width="927" height="161" alt="image" src="https://github.com/user-attachments/assets/d7eb1acc-b570-433a-8aa4-38fc60848a85" />

   - Shigeru
  
     <img width="994" height="112" alt="image" src="https://github.com/user-attachments/assets/44384bff-136a-4204-ab28-5ef5293016a6" />

2. Pasang  IP sementara pada 5 host uji dari cabang yang berjauhan:

   - Armisael (Cabang Shigeru — menguji summary 192.238.4.80/28)
     
     ```
      ip addr flush dev eth0 && ip addr add 192.238.4.83/29 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.4.81
     ```

   - Quarters
  
     ```
      ip addr flush dev eth0 && ip addr add 192.238.0.2/23 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.0.1
     ```

   - Lilith
  
     ```
      ip addr flush dev eth0 && ip addr add 192.238.4.74/29 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.4.73
     ```

   - Sachiel
  
     ```
      ip addr flush dev eth0 && ip addr add 192.238.3.130/26 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.3.129
     ```

   - Comm-Net
  
     ```
      ip addr flush dev eth0 && ip addr add 192.238.2.2/24 dev eth0 && ip link set eth0 up && ip route replace default via 192.238.2.1
     ```
     
3. Uji ping lintas cabang dari Armisael:

   ```
    for ip in 192.238.0.2 192.238.4.74 192.238.3.130 192.238.2.2; do ping -c 3 $ip; done
   ```

   <img width="1060" height="547" alt="image" src="https://github.com/user-attachments/assets/6ef86d57-4c32-4cde-825e-14b2f86c23be" />

   <img width="1058" height="317" alt="image" src="https://github.com/user-attachments/assets/d2a41cdc-b0b2-4bd5-b1c2-6c26ebcc7daa" />

## Soal 4

### Pengujian

1. Cek Interface, Rute& DHCP Relay

   Minta IP DHCP dulu di node yang akan diuji
   
   ```
     udhcpc -i eth0
   ```
   
   Lalu gunakan kode ini untuk pengujian:
   
   ```
    ip -br a
    ip route
    ping -c 3 8.8.8.8
    ping -c 3 google.com
   ```

   - Spy
  
     <img width="1058" height="611" alt="image" src="https://github.com/user-attachments/assets/435ad1a6-d7f2-4b88-95e5-fb483c0dbdd8" />

     <img width="1060" height="83" alt="image" src="https://github.com/user-attachments/assets/22dcf3d8-2391-4f94-801b-9ed101d27341" />

   - Armisael
  
     <img width="1060" height="605" alt="image" src="https://github.com/user-attachments/assets/78efa1c7-3cce-4bef-abee-747332d41dff" />

     <img width="1057" height="96" alt="image" src="https://github.com/user-attachments/assets/931430fa-efe9-4503-9645-d329d383ee19" />

   - Dock-Crew
  
     <img width="1060" height="612" alt="image" src="https://github.com/user-attachments/assets/ebd9829b-6898-4631-aa5f-7eeaac56cbc6" />

     <img width="1057" height="97" alt="image" src="https://github.com/user-attachments/assets/a061a3ae-5276-468f-93b3-9b5db1c2862f" />

2. Cek apakah DHCP Server & Masquerade-nya sudah aktif di Gendou

   ```
   cat /var/lib/dhcp/dhcpd.leases
   iptables -t nat -L POSTROUTING -n -v
   ```

   <img width="1061" height="522" alt="image" src="https://github.com/user-attachments/assets/cdfc6d7a-862d-47f8-b367-54541ba06be2" />

   <img width="1060" height="595" alt="image" src="https://github.com/user-attachments/assets/ee15e38b-a44c-4e9f-8d69-7b2562b47798" />

   <img width="1060" height="598" alt="image" src="https://github.com/user-attachments/assets/fa292095-cbc2-4598-8944-984d1f3c2a19" />

   <img width="1061" height="431" alt="image" src="https://github.com/user-attachments/assets/8883ea61-b3e2-4729-a394-b7ac793cacaf" />

## Soal 5 

### Langkah 1: Tambahkan aturan Firewall ke Gendou 

Masukkan script ini ke Gendou:

```
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

1. Cek aturan firewall dan default policy pada INPUT & FORWARD di Gendou

   ```
    iptables -S
   ```
   
   <img width="1021" height="325" alt="image" src="https://github.com/user-attachments/assets/bcd8b2ce-7ee6-4a11-b003-a077b9a72b07" />


2. Membuktikan akses internet, akses ke Gendou, dan akses antar LAN masih berjalan mulus

   ```
    ping -c 3 8.8.8.8
    ping -c 3 192.238.4.105
   ```

   <img width="982" height="489" alt="image" src="https://github.com/user-attachments/assets/df5de829-3b03-47ec-9c2a-9c2b3490f97b" />


## Soal 6 

Gunakan script ini untuknode Unit-01:

```
printf 'auto lo\niface lo inet loopback\n\nauto eth0\niface eth0 inet static\n    address 192.238.4.66\n    netmask 255.255.255.248\n    gateway 192.238.4.65\n' > /etc/network/interfaces && printf '#!/bin/sh\npkill udhcpc 2>/dev/null\nip addr flush dev eth0\nip addr add 192.238.4.66/29 dev eth0\nip link set eth0 up\nip route replace default via 192.238.4.65\necho "nameserver 8.8.8.8" > /etc/resolv.conf\n' > /root/ip.sh && chmod +x /root/ip.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART STATIC IP ===\n[ -f /root/ip.sh ] && sh /root/ip.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && sh /root/ip.sh
```

### Pengujian 

  - Unit-01

    ```
      ip -br a
      ip route
      ping -c 3 192.238.4.65
      cat /etc/network/interfaces
    ```
    <img width="1058" height="633" alt="image" src="https://github.com/user-attachments/assets/29a197ff-a4a5-4342-87a8-ca0af7039281" />

  - Tokyo-Core3

    ```
      ip -br a
      ip route
      ping -c 3 192.238.4.105
      cat /etc/network/interfaces
    ```
    
    <img width="945" height="626" alt="image" src="https://github.com/user-attachments/assets/8cc47737-0f9a-4317-8630-ca474020b3e8" />

  - Magi-Casper

    ```
      ip -br a
      ip route
      ping -c 3 192.238.4.57
      cat /etc/network/interfaces
    ```

    <img width="1064" height="624" alt="image" src="https://github.com/user-attachments/assets/6c72ba03-aad3-4315-aa9e-5259e64a5fab" />

## Soal 7 

### Langkah 1: Pasang skrip & Nyalakan Listener di Ketiga Node MAGI

### Langkah 2: Kirim Pesan dari Lilith 

<img width="1060" height="220" alt="image" src="https://github.com/user-attachments/assets/8bc2be94-902c-4efc-8052-e013149d0919" />

### Pengujian 

  - Magi-Casper

    ```
      ss -tlnp | grep nc
      cat /tmp/casper_5000.log
    ```

    <img width="1055" height="134" alt="image" src="https://github.com/user-attachments/assets/7214f3b2-038c-4432-aab4-cdf85a809f8b" />

  - Magi-Balthasar

    ```
      ss -tlnp | grep nc
      cat /tmp/balthasar_5000.log
      cat /tmp/balthasar_5001.log
    ```

    <img width="1059" height="243" alt="image" src="https://github.com/user-attachments/assets/1efdfdf2-a5c4-483b-a8bf-3c94a314f289" />


  - Magi-Melchior

    ```
      ss -tlnp | grep nc
      cat /tmp/melchior_5000.log
    ```

    <img width="1060" height="134" alt="image" src="https://github.com/user-attachments/assets/642fab33-0321-4e1d-97ec-c6df0a056d7c" />

## Soal 8 

### Langkah 1: Konfigurasi Listener di Pen-Pen 

```
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/penpen.sh && chmod +x /root/penpen.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/penpen.sh ] && bash /root/penpen.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/penpen.sh
```

<img width="1062" height="84" alt="image" src="https://github.com/user-attachments/assets/b735d440-93f4-4a69-be12-6d7c26e21de4" />

### Langkah 2: Konfigurasi Listener di Tokyo3-Core

Jalankan perintah satu baris berikut untuk menayalakan port 80 dan 8080:

```
printf '#!/bin/bash\n[ -f /root/ip.sh ] && sh /root/ip.sh\nif ! command -v nc.traditional >/dev/null 2>&1 || ! command -v iptables >/dev/null 2>&1; then\n  apt-get update -o Acquire::Check-Date=false -o Acquire::Check-Valid-Until=false -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true\n  DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated iptables netcat-traditional\n  update-alternatives --set nc /bin/nc.traditional 2>/dev/null || true\nfi\nif ! pgrep -f "[n]c.traditional -l -p 80 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 80 -c "echo Welcome to $(hostname)"; done'\'' >/dev/null 2>&1 &\nfi\nif ! pgrep -f "[n]c.traditional -l -p 8080 " >/dev/null 2>&1; then\n  nohup sh -c '\''while true; do nc.traditional -l -p 8080 -c "echo syifa imutz"; done'\'' >/dev/null 2>&1 &\nfi\n' > /root/tokyo3.sh && chmod +x /root/tokyo3.sh && touch /root/.bashrc && sed -i '/# === AUTOSTART SERVICE ===/,$d' /etc/profile /root/.bashrc && printf '\n# === AUTOSTART SERVICE ===\n[ -f /root/tokyo3.sh ] && bash /root/tokyo3.sh\n' | tee -a /etc/profile /root/.bashrc >/dev/null && bash /root/tokyo3.sh
```

Lalu cek apakah port 80 dan 8080 sudah menyala: 

```
ss -tlnp | grep nc
```

<img width="1063" height="136" alt="image" src="https://github.com/user-attachments/assets/74c9cd73-1906-4387-97f0-2313b8f0ccf9" />



















     
  
     
