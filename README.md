# ClipVault V6 — Android-friendly deployment

ClipVault V6 backend menyediakan transkripsi audio video memakai OpenAI Whisper dan upload ke YouTube Data API v3. Frontend disajikan dari backend yang sama melalui HTTPS.

## Isi paket
- `server.js` — API transkripsi, OAuth Google, dan upload YouTube.
- `public/index.html` — antarmuka ClipVault V6.
- `Dockerfile` — Node.js 20 + FFmpeg.
- `render.yaml` — konfigurasi deploy Render.
- `.env.example` — contoh variabel rahasia untuk server lokal.

## Deploy lewat Android
1. Ekstrak ZIP ini di HP.
2. Buat repository GitHub baru bernama `clipvault-v6`.
3. Upload seluruh isi folder `ClipVault_V6_Backend` ke root repository (termasuk `Dockerfile`, `render.yaml`, `package.json`, `server.js`, dan folder `public`). Jangan upload file `.env` atau API key.
4. Buka Render Dashboard → New → Web Service → Connect GitHub → pilih `clipvault-v6`. Runtime harus Docker. Jika Render mendeteksi `render.yaml`, periksa pengaturan sebelum deploy.
5. Setelah Render memberi domain, misalnya `https://clipvault-v6.onrender.com`, buka Environment dan isi:
   - `APP_ORIGIN` = domain HTTPS Render, tanpa garis miring di akhir.
   - `GOOGLE_REDIRECT_URI` = domain + `/oauth2/callback`.
   - `OPENAI_API_KEY` = API key OpenAI.
   - `GOOGLE_CLIENT_ID` dan `GOOGLE_CLIENT_SECRET` = kredensial OAuth Web dari Google Cloud.
   - `SESSION_SECRET` = string acak panjang (jika belum dibuat otomatis).
6. Di Google Cloud Console, aktifkan YouTube Data API v3. Buat OAuth Client ID jenis Web application. Tambahkan Authorized redirect URI persis sama dengan `GOOGLE_REDIRECT_URI`. Tambahkan domain Render ke Authorized JavaScript origins jika diminta.
7. Simpan environment variables lalu redeploy. Buka `https://DOMAIN-RENDER/api/health`; respons `ok:true` berarti server hidup.
8. Buka domain Render untuk memakai ClipVault. Login Google, uji transkripsi video pendek, lalu uji upload dengan privasi Public hanya setelah meninjau video dan metadata.

## Catatan penting
- Jangan pernah membagikan OpenAI API key atau Google Client Secret, termasuk lewat chat atau screenshot.
- Google OAuth consent screen mungkin perlu disetel ke External dan akun Google ditambahkan sebagai Test User saat status Testing.
- Project ini menggunakan sesi in-memory sederhana. Untuk produksi yang serius, pasang persistent session store, rate limiting, batas upload sesuai kebutuhan, dan kebijakan privasi.
- Upload YouTube pada implementasi awal mengirim file video yang dipilih ke YouTube. Pastikan itu adalah hasil klip final jika ingin mengunggah klip, bukan video sumber panjang.
- Upload awal menggunakan satu request, bukan resumable upload; koneksi putus saat file besar bisa menyebabkan gagal. Uji dengan file kecil dahulu.
- Privasi default adalah Public; pengguna tetap harus meninjau dan menekan konfirmasi upload.
- YouTube API quota, verifikasi Google, dan kebijakan akun dapat membatasi publikasi.


## Import link YouTube / Instagram / TikTok
Kolom paste link memakai yt-dlp untuk mengambil video yang dapat diakses publik lalu memasukkannya ke workspace. Dockerfile memasang yt-dlp, Python, dan FFmpeg. Batas 250 MB dan timeout 3 menit. Link privat, login-only, konten berbayar, atau pembatasan akses tidak dibypass. Gunakan hanya video milik sendiri atau yang kamu punya izin untuk mengunduh, mengedit, dan memublikasikannya; patuhi ketentuan platform dan hak cipta. Sebagian link mungkin gagal jika platform mengubah sistemnya.
