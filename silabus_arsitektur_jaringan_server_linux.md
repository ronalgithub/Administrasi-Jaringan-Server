# Kurikulum Pembelajaran: Arsitektur Jaringan Server & Administrasi Linux (Tingkat Enterprise)

Dokumen ini berisi kerangka *brainstorming* untuk silabus pembelajaran sistem jaringan server dan administrasi Linux, dirancang dengan pendekatan skenario dunia nyata dan praktik operasional.

---

## Modul 1: Arsitektur Jaringan Server & Pemilihan OS

### 1. Konsep Dasar Client-Server
Jangan hanya berhenti di model *request-response* sederhana. Arahkan pemahaman ke arsitektur modern.
*   **Pengembangan Materi:**
    *   **Arsitektur N-Tier & Microservices:** Jelaskan bagaimana satu *request* dari klien memicu rantai komunikasi di *backend* (misal: Web Server -> App Server -> Database Server).
    *   **Stateful vs. Stateless:** Kapan server perlu mengingat sesi klien (seperti *login* ke *controller* jaringan) dan kapan harus *stateless* (seperti API RESTful) agar mudah di-*scale*.
    *   **Trafik East-West vs North-South:** Pemahaman fundamental—membedakan trafik klien ke server eksternal, dengan trafik antar-server di dalam *data center*.
*   **Saran Praktik (Lab):** Implementasi sederhana menggunakan Docker Compose. Buat skenario di mana satu kontainer bertindak sebagai *client* yang melakukan *query* ke kontainer *database server* untuk visualisasi isolasi jaringan.

### 2. Spesifikasi Perangkat Keras Server
Fokuskan pada alasan teknis *mengapa* komponen *enterprise* berbeda dari spesifikasi *desktop/gaming* (uptime & throughput).
*   **Pengembangan Materi:**
    *   **CPU (Core vs. Clock Speed):** Server web/kontainerisasi (butuh banyak *core*) vs server *database* monolitik (butuh *clock speed* tinggi).
    *   **RAM ECC (Error-Correcting Code):** Fenomena *bit-flip* dan pencegahan korupsi data permanen pada *database* besar.
    *   **RAID Storage (Redundansi vs Performa):** Penggunaan RAID 1 (OS), RAID 5/6 (Arsip), dan RAID 10 (Database). Konsep IOPS dan DWPD pada SSD Enterprise.
    *   **Network Interface Card (NIC):** Pentingnya *teaming/bonding* untuk redundansi jaringan tulang punggung.
*   **Saran Praktik (Lab):** Simulasi kegagalan disk menggunakan *software RAID* (`mdadm` di Linux) dengan mencabut satu disk virtual dan melihat proses *rebuild*.

### 3. Perbandingan OS Server (Linux vs Windows Server)
Fokus pada "mana yang tepat untuk beban kerja (*workload*) spesifik".
*   **Pengembangan Materi:**
    *   **Linux (mis. Ubuntu Server, RHEL):** Overhead sangat rendah, fondasi ekosistem modern (*Containerization*, Kubernetes), TCO rendah. Cocok untuk web server, sistem berbasis Python, *database*, dan *network appliances*.
    *   **Windows Server:** Integrasi ekosistem melalui Active Directory (AD) dan Group Policy Object (GPO). Cocok untuk manajemen identitas perusahaan tersentralisasi dan aplikasi berbasis .NET.
*   **Saran Praktik (Lab):** Bandingkan konsumsi memori OS saat *idle* antara Windows Server Core dan Ubuntu Server untuk perspektif efisiensi *resource*.

---

## Modul 2: Arsitektur Jaringan (Subnetting, VLAN) dan Keamanan Server Dasar

### 1. Subnetting & Desain Topologi Jaringan
*   **Pengembangan Materi:**
    *   **CIDR (Classless Inter-Domain Routing):** Menggunakan notasi *slash* (misal /24, /26) untuk efisiensi IP.
    *   **Segmentasi Berbasis Fungsi:** Blok IP terpisah untuk *backbone*, infrastruktur, dan pengguna akhir.
    *   **Subnetting untuk Keamanan:** Membatasi *broadcast domain* agar *malware* atau *broadcast storm* tidak melumpuhkan seluruh jaringan.
*   **Saran Praktik (Lab):** Merancang alokasi IP (VLSM) yang paling efisien tanpa *overlap* untuk topologi gedung kantor (50 AP, 10 server, 100 staf).

### 2. VLAN (Virtual LAN) & Segmentasi Trafik
*   **Pengembangan Materi:**
    *   **Konsep 802.1Q Tagging:** Perbedaan *Access Port* (tanpa tag) dan *Trunk Port* (jalur tulang punggung).
    *   **Management VLAN vs Data VLAN:** Perangkat sentral (seperti *wireless controller*) harus di *Management VLAN* yang terisolasi dari *User VLAN*.
    *   **Inter-VLAN Routing:** Konsep *Router on a Stick* atau *Layer 3 Switch* (SVI).
*   **Saran Praktik (Lab):** Konfigurasi *trunking* antar *switch* di simulator (GNS3/Packet Tracer) dan memisahkan trafik dua departemen.

### 3. Keamanan Server Dasar (Hardening)
Fokus pada prinsip *Defense in Depth* dan *Least Privilege*.
*   **Pengembangan Materi:**
    *   **SSH Hardening:** Mewajibkan *Public/Private Key* (Ed25519), menonaktifkan *password authentication*, dan akses `root`.
    *   **Host-Based Firewall (UFW/iptables):** Menutup semua port kecuali yang esensial (misal: hanya port 8443 dan 22).
    *   **Mitigasi Brute Force:** Implementasi `fail2ban`.
    *   **Patch Management:** Konfigurasi *unattended-upgrades*.
*   **Saran Praktik (Lab):** *Fresh install* Linux, mengunci server (SSH key, matikan password login, set UFW), lalu lakukan *scanning nmap* dari *client* untuk validasi keamanan.

---

## Modul 3: Kontainerisasi (Docker/Compose) dan Implementasi Layanan

### 1. Konsep Dasar Kontainerisasi (Docker)
*   **Pengembangan Materi:**
    *   **Kontainer vs Virtual Machine:** Isolasi proses yang berbagi *kernel* vs virtualisasi perangkat keras penuh.
    *   **Arsitektur Image & Layering:** Konsep *immutable layers* untuk efisiensi penyimpanan dan kecepatan.
    *   **Lifecycle & Ephemeral Storage:** Kontainer bisa dihancurkan kapan saja; data hilang jika tidak dikelola (*volumes*).
*   **Saran Praktik (Lab):** Menjalankan web server murni via CLI (`docker run`), melakukan *port mapping*, dan menghancurkan kontainer untuk melihat hilangnya data statis.

### 2. Orkestrasi Multi-Kontainer dengan Docker Compose
*   **Pengembangan Materi:**
    *   **Sintaks YAML:** Deklarasi *services*, *networks*, dan *volumes* dalam `docker-compose.yml`.
    *   **Manajemen Persistent Volume:** Menggunakan *Bind Mounts* atau *Docker Volumes* untuk data persisten (*database*).
    *   **Jaringan Internal:** Komunikasi otomatis antar kontainer via nama layanan (resolusi DNS internal).
    *   **Troubleshooting:** Membaca log *real-time*.
*   **Saran Praktik (Lab):** *Deploy* *network controller* (misal UniFi) beserta *backend database* (MongoDB) menggunakan Docker Compose, lengkap dengan *volume mapping*.

### 3. Implementasi Reverse Proxy
*   **Pengembangan Materi:**
    *   **Konsep Reverse Proxy:** Menggunakan Nginx/Traefik untuk merutekan trafik domain ke kontainer yang tepat.
    *   **Terminasi SSL/TLS:** Enkripsi di level proksi untuk keamanan akses eksternal.
*   **Saran Praktik (Lab):** Konfigurasi Nginx di depan *controller*, merutekan domain lokal dengan *self-signed certificate*.

---

## Modul 4: Pemantauan Kinerja, Manajemen Log, dan Troubleshooting

### 1. Pemantauan Kinerja (Proactive Monitoring)
*   **Pengembangan Materi:**
    *   **Metrik OS vs Metrik Layanan:** Memantau CPU/RAM vs kesehatan spesifik aplikasi.
    *   **Arsitektur Push vs Pull:** Pengenalan ekosistem Prometheus (*pull*) dan Grafana (visualisasi).
    *   **Alerting:** Menentukan ambang batas (*threshold*) yang logis untuk mencegah *alert fatigue*.
*   **Saran Praktik (Lab):** *Deploy stack monitoring* (Prometheus, Grafana, cAdvisor) via Compose. Memantau resource MongoDB dan membuat dasbor beban server *real-time*.

### 2. Manajemen Log Sentralisasi
*   **Pengembangan Materi:**
    *   **Single Pane of Glass:** Mengumpulkan log ke dalam satu repositori terpusat (*searchable*).
    *   **Log Parsing:** Mengubah teks mentah menjadi log terstruktur (JSON).
    *   **Tools Standar:** Stack ELK atau PLG (Promtail, Loki, Grafana) untuk lingkungan kontainer.
*   **Saran Praktik (Lab):** Set *logging driver* Docker ke Loki. Berlatih mencari jejak *error* spesifik (misal: *crash* pada *database*) melalui dasbor.

### 3. Troubleshooting & Diagnostik Terstruktur
*   **Pengembangan Materi:**
    *   **Metodologi OSI Layer:** *Bottom-Up* (cek kabel/Layer 1) vs *Top-Down* (cek log/Layer 7).
    *   **Diagnostik Tingkat Lanjut:** Menggunakan `mtr`, `ip route`, dan `tcpdump`.
    *   **Isolasi Masalah:** Membedakan error aplikasi (misal sertifikat rusak) dari error infrastruktur (routing/firewall terblokir).
*   **Saran Praktik (Lab):** Skenario "Break/Fix". Instruktur merusak sistem lab (mengubah *permission* direktori database atau memutuskan *route*). Peserta harus mencari *root cause* dan memulihkan layanan dengan tenggat waktu.