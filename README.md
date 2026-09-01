# Pemantau antriyuk.id

Satu berkas GitHub Actions yang menyentuh [antriyuk.id](https://antriyuk.id)
tiap beberapa menit dan mengirim pesan Telegram kalau situsnya tidak menjawab.

## Kenapa repo ini terpisah, dan kenapa publik

**Terpisah** karena pemantau tidak boleh tinggal di mesin yang dipantau. Kalau ia
jalan di VPS Antri Yuk, ia mati bersama VPS itu dan diam persis ketika paling
dibutuhkan.

**Publik** karena GitHub Actions di repo privat memakai kuota 2.000 menit/bulan,
sementara pemeriksaan tiap 5 menit menghabiskan sekitar 8.600 menit/bulan —
empat kali lipatnya. Pemantauan akan berhenti sendiri dalam seminggu tanpa
memberi tahu siapa pun. Di repo publik, Actions gratis tanpa batas.

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
