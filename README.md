# Pemantau antriyuk.id

Satu berkas GitHub Actions yang menyentuh [antriyuk.id](https://antriyuk.id)
berkala dan mengirim pesan Telegram kalau situsnya tidak menjawab.

## 🚨 Seberapa sering ia BENAR-BENAR jalan — diukur 5 Nov 2026

Jadwalnya ditulis `*/5`: tiap lima menit, 288 jalan sehari. Yang terjadi di
kenyataan, dihitung dari seluruh riwayat jalan yang masih disimpan GitHub:

| Jendela | Jalan/hari | Jarak tengah | Terpanjang | Jarak < 1 jam |
|---|---|---|---|---|
| 12–16 Sep 2026 (5 hari) | 89,4 | 15,6 menit | 36 menit | **446 dari 446** |
| 18 Sep – 4 Nov 2026 (48 hari) | **5,4** | **4,4 jam** | **10 jam** | **0 dari 259** |

Sesudah 17 September ia jalan **1,9% dari yang tertulis di jadwalnya**. Dua
jendela itu tidak bersinggungan sama sekali: sebelum tebingnya tiap jarak di
bawah satu jam, sesudahnya tidak ada satu pun. Tanggalnya pun tajam, bukan
penurunan perlahan. Dorongan terakhir ke repo ini 5 September — dua belas hari
sebelumnya. Dugaan terkuat: GitHub menurunkan prioritas jadwal di repo yang
diam, bertahap, sampai mematikannya sama sekali di hari ke-60.

**Jadi jangan membaca pemantau ini sebagai "tahu dalam 5 menit".** Yang
benar-benar dijanjikannya adalah "tahu dalam beberapa jam" — masih jauh lebih
baik daripada baru tahu besok pagi dari tenant yang sedang melayani antrean,
tapi bukan angka yang tertulis di cron-nya. Kalau yang dibutuhkan sungguhan
hitungan menit, tempatnya bukan di sini.

## 🚨 GitHub mematikannya sendiri sesudah 60 hari repo diam

Terjadi **5 November 2026**: surel "workflow has been disabled" sesudah repo
tepat 61 hari tanpa dorongan. Pemantauannya berhenti tanpa satu pun peringatan —
bentuk kegagalan yang persis sama dengan yang ia ada untuk mencegahnya. Pemantau
yang bisu terlihat sama persis seperti situs yang sehat.

Dihidupkan lagi hari itu juga, dan jam 60 harinya direset oleh dorongan ini.
**Repo ini butuh satu dorongan tiap kurang dari 60 hari supaya tetap hidup.**

Sepanjang 49 hari ia setengah buta, produksi ternyata bersih: 0 kegagalan unit,
0 keluar tak bersih, 0 `connection refused`, 0 jawaban 5xx dari Caddy, dan VPS
tidak pernah nyala ulang sejak Juni. Beruntung, bukan aman.

## Kenapa repo ini terpisah, dan kenapa publik

**Terpisah** karena pemantau tidak boleh tinggal di mesin yang dipantau. Kalau ia
jalan di VPS Antri Yuk, ia mati bersama VPS itu dan diam persis ketika paling
dibutuhkan.

**Publik** karena GitHub Actions di repo privat memakai kuota 2.000 menit/bulan,
sementara pemeriksaan tiap 5 menit menghabiskan sekitar 8.600 menit/bulan —
empat kali lipatnya. Pemantauan akan berhenti sendiri dalam seminggu tanpa
memberi tahu siapa pun. Di repo publik, Actions gratis tanpa batas.

Hitungan itu dibuat dari jadwal yang tertulis, dan tetap cara menghitung yang
benar: kuota harus muat untuk jadwal yang diminta, bukan untuk pembatasan yang
kebetulan sedang diberikan GitHub dan bisa dicabut kapan saja.

Repo ini sengaja **tidak berisi apa pun selain pemantau**. Tidak ada kode
aplikasi, tidak ada catatan usaha, tidak ada data pelanggan.

## Rahasianya aman

Token bot Telegram dan ID chat tersimpan sebagai **GitHub Secret terenkripsi**,
tidak terlihat di repo ini walau siapa pun bisa membaca berkasnya.

🚨 Pemicunya sengaja **hanya** `schedule` dan `workflow_dispatch`. Jangan pernah
menambahkan `pull_request` — pemicu itu membuat orang asing bisa menjalankan
workflow lewat fork, dan di situlah rahasia repo publik biasanya bocor.

## Yang diperiksa

| Alamat | Syarat lulus |
|---|---|
| `antriyuk.id/` | HTTP 200 **dan** memuat "Coba gratis" |
| `antriyuk.id/masuk` | HTTP 200 **dan** memuat "Antri Yuk" |

Memeriksa isinya, bukan cuma kode 200 — Next.js tetap memulangkan 200 untuk
halaman yang gagal tergambar, dan pemantauan yang lulus di halaman kosong tidak
menjaga apa pun.

Tiga percobaan berjeda 10 detik sebelum menyimpulkan mati, supaya satu gangguan
jaringan tidak jadi peringatan palsu. Peringatan palsu yang sering datang
membuat orang berhenti membacanya, dan itu lebih berbahaya daripada tidak punya
pemantauan sama sekali.
