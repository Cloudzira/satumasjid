# SatuMasjid – Panduan Setup & Keamanan

## 1. Berkas yang di-deploy (satu folder, root Vercel)
`index.html`, `sw.js`, `manifest.webmanifest`, `vercel.json`, folder `icons/`.
`firestore.rules` di-publish lewat Firebase (bukan lewat Vercel).

## 2. Firebase Console (wajib, urut)
1. **Authentication → Sign-in method**: aktifkan **Email/Password**, **Google**, dan **Anonymous**
   (Anonymous dipakai agar jamaah bisa mengirim permohonan tanpa akun).
2. **Authentication → Settings → Authorized domains**: tambahkan domain Vercel Anda.
3. **Firestore Database**: buat database, lalu **Rules → tempel isi `firestore.rules` → Publish**.
4. **Hapus dokumen uji lama** di koleksi `masjid` (versi sebelumnya memakai struktur data berbeda).
5. **Buat akun Admin**: login sekali di aplikasi dengan akun Anda (email/Google), buka
   Authentication → Users → salin **UID** akun itu. Di Firestore buat koleksi `admins`,
   Document ID = UID tersebut, isi 1 field bebas (mis. `aktif: true`).
   Setelah itu menu samping akan menampilkan **Verifikasi & Laporan**.

## 3. Penguatan tambahan (sangat disarankan)
- **Batasi API key**: Google Cloud Console → APIs & Services → Credentials → API key web →
  *HTTP referrers* → hanya domain Anda (+ `localhost` bila perlu).
- **Firebase App Check** (reCAPTCHA Enterprise/v3) lalu *Enforce* untuk Firestore dan Authentication:
  menahan bot yang menyalahgunakan API key. Membutuhkan penambahan SDK App Check di `index.html`.
- **Budget alert** di Google Cloud agar tahu bila ada lonjakan pemakaian.
- Uji `vercel.json` (CSP) di *Preview Deployment* dulu. Jika ada fitur yang diblokir, buka DevTools →
  Console untuk melihat sumber yang ditolak, tambahkan ke CSP.

## 4. Prosedur admin sebelum menyetujui masjid
1. Hubungi nomor WhatsApp pengurus; pastikan benar-benar pengurus masjid tersebut.
2. Cocokkan lokasi di Google Maps dan alamat.
3. Periksa foto dokumen (SK/surat tugas).
4. **Rekening/QRIS**: nama pemilik harus nama masjid/yayasan, bukan pribadi. Pindai QRIS dan
   pastikan nama merchant sesuai.
5. Setiap perubahan identitas atau rekening/QRIS oleh pengurus otomatis mencabut status dan perlu disetujui ulang.

## 5. Yang dilindungi oleh Rules
- Masjid & rekening/QRIS tidak tampil ke jamaah sebelum disetujui admin (ditegakkan **server**, bukan hanya tampilan).
- Pemilik tidak bisa menyetujui dirinya sendiri atau mengubah status verifikasi.
- Data verifikasi, mustahik, dan permohonan tidak pernah publik.
- Akun harus berverifikasi email untuk mendaftarkan masjid/mengubah rekening.
- Batas ukuran dan bentuk data diperiksa di server.

## 6. Keterbatasan yang perlu diketahui
- Admin menyetujui secara manual; keamanan akhir bergantung pada ketelitian verifikasi.
- Tanpa App Check, pihak luar masih dapat memanggil API (dibatasi Rules, tetapi bisa membebani kuota).
- Gambar QRIS disimpan di Firestore (dikompres ±600 KB). Untuk skala besar, pindahkan ke Cloud Storage (paket Blaze).
- Admin membaca daftar masjid satu per satu; efisien untuk puluhan masjid, bukan ribuan.
