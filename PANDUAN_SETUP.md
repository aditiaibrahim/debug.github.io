# Panduan Setup Login Google dengan Supabase

## ❓ Apakah Wajib Pakai Google Cloud Console?

**YA, WAJIB!** Google mewajibkan semua aplikasi yang ingin menggunakan "Login with Google" untuk mendaftar di Google Cloud Console dan mendapatkan OAuth 2.0 credentials. Ini adalah security measure dari Google untuk melindungi user data.

**Tidak ada cara lain** selain ini - ini adalah standar industri untuk OAuth authentication.

## Langkah 1: Konfigurasi Google Cloud Console (WAJIB!)

### 1.1 Buat Project di Google Cloud Console
1. Buka [Google Cloud Console](https://console.cloud.google.com/)
2. Login dengan akun Google Anda
3. Di dropdown project di pojok kiri atas, klik **"Select a project"**
4. Klik **"NEW PROJECT"** di pojok kanan atas
5. Masukkan nama project (misal: "Aplikasi Login Saya")
6. Klik **"CREATE"**
7. Pilih project yang baru dibuat

### 1.2 Enable Google API
1. Di menu kiri, klik **"APIs & Services"** > **"Library"**
2. Di search bar, ketik: **"Google+ API"** atau **"Google People API"**
3. Klik pada hasil yang muncul
4. Klik tombol **"ENABLE"**
5. Tunggu hingga API aktif (beberapa detik)

**Catatan:** Jika "Google+ API" tidak ditemukan, coba "Google People API" - ini adalah API yang lebih baru.

### 1.3 Konfigurasi OAuth Consent Screen
1. Di menu kiri, klik **"APIs & Services"** > **"OAuth consent screen"**
2. Pilih **"External"** (kecuali Anda menggunakan Google Workspace)
3. Klik **"CREATE"**
4. Isi form:
   - **App name**: Nama aplikasi Anda (misal: "Aplikasi Login")
   - **User support email**: Email Anda
   - **Developer contact information**: Email Anda
5. Klik **"SAVE AND CONTINUE"**
6. Di halaman "Scopes", klik **"ADD OR REMOVE SCOPES"**
7. Centang scope yang dibutuhkan:
   - `../auth/userinfo.email`
   - `../auth/userinfo.profile`
   - `openid`
8. Klik **"UPDATE"** lalu **"SAVE AND CONTINUE"**
9. Di halaman "Test users", klik **"ADD USERS"**
10. Tambahkan email Google yang akan digunakan untuk testing
11. Klik **"SAVE AND CONTINUE"**
12. Review summary, lalu klik **"BACK TO DASHBOARD"**

### 1.4 Buat OAuth 2.0 Credentials
1. Di menu kiri, klik **"APIs & Services"** > **"Credentials"**
2. Klik **"+ CREATE CREDENTIALS"** > **"OAuth client ID"**
3. Jika muncul prompt, klik **"CONFIGURE CONSENT SCREEN"** (jika belum dikonfigurasi)
4. Pilih **"Web application"** sebagai Application type
5. Isi form:
   - **Name**: Nama aplikasi (misal: "Login App")
   - **Authorized JavaScript origins**: 
     ```
     http://localhost:5500
     http://127.0.0.1:5500
     ```
   - **Authorized redirect URIs** (PENTING!):
     ```
     https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback
     ```
6. Klik **"CREATE"**
7. **SALIN DAN SIMPAN** dua nilai berikut (akan dipakai nanti):
   - **Client ID** (contoh: `123456789-abc.apps.googleusercontent.com`)
   - **Client Secret** (contoh: `GOCSPX-xxxxxxxxxxxxx`)

**⚠️ PENTING:** Jangan bagikan Client ID dan Client Secret kepada orang lain! Ini adalah kredensial rahasia aplikasi Anda.

## Langkah 2: Konfigurasi Supabase (5 Menit!)

1. Buka [Supabase Dashboard](https://app.supabase.com/)
2. Login dengan akun Supabase Anda
3. Pilih project: **`ziwjmsjvhzivmvqimiei`**
4. Di menu kiri, klik **"Authentication"**
5. Klik tab **"Providers"**
6. Cari **"Google"** dalam daftar providers
7. Klik pada **Google** untuk membuka settings
8. Toggle **"Enable Sign in with Google"** menjadi ON (biru)
9. Isi form dengan data dari Langkah 1.4:
   - **Client ID (for OAuth)**: Paste Client ID yang Anda salin dari Google Cloud Console
   - **Client Secret (for OAuth)**: Paste Client Secret yang Anda salin dari Google Cloud Console
10. Klik **"Save"** (tombol di pojok kanan atas)

✅ Google Provider sudah aktif di Supabase!

## Langkah 3: Konfigurasi URL Redirect (SANGAT PENTING!)

Ini adalah langkah yang **paling sering dilewatkan** dan menyebabkan error!

1. Di Supabase Dashboard, pastikan Anda masih di menu **"Authentication"**
2. Klik tab **"URL Configuration"** (ada di bawah "Providers")
3. Isi **"Site URL"** dengan URL tempat aplikasi berjalan:
   - Untuk development: `http://localhost:5500` atau `http://127.0.0.1:5500`
   - Untuk production: `https://domain-anda.com`
4. Di bagian **"Redirect URLs"**, tambahkan URL-URL berikut:
   ```
   http://localhost:5500/**
   http://127.0.0.1:5500/**
   http://localhost:5500/index.html
   http://127.0.0.1:5500/index.html
   ```
5. Klik **"Save"**

**Penjelasan:**
- `/**` artinya semua path di domain tersebut
- Redirect URLs harus sesuai dengan URL dimana aplikasi Anda berjalan
- Jika menggunakan port lain (misal 3000), ganti 5500 dengan port Anda

## Langkah 4: Testing Aplikasi

### Opsi A: Menggunakan Live Server (VS Code)
1. Install ekstensi "Live Server" di VS Code
2. Klik kanan pada file `index.html`
3. Pilih "Open with Live Server"
4. Browser akan terbuka di `http://127.0.0.1:5500`

### Opsi B: Menggunakan Python HTTP Server
```bash
# Python 3
python -m http.server 5500
```

### Opsi C: Menggunakan Node.js HTTP Server
```bash
# Install http-server globally (jika belum)
npm install -g http-server

# Jalankan server
http-server -p 5500
```

## Langkah 5: Test Login

1. Buka browser dan akses aplikasi
2. Klik tombol "Masuk dengan Google"
3. Anda akan diarahkan ke halaman login Google
4. Setelah login berhasil, Anda akan kembali ke aplikasi
5. Data user akan ditampilkan (nama, email, foto profil)

## Fitur yang Tersedia

✅ Login dengan Google OAuth 2.0
✅ Menampilkan foto profil, nama, dan email user
✅ Logout functionality
✅ Session management (auto-login jika session masih aktif)
✅ Error handling
✅ Responsive design
✅ Animasi dan transisi yang smooth

## Troubleshooting

### Error: "redirect_uri_mismatch"
- Pastikan redirect URI di Google Cloud Console sesuai dengan Supabase callback URL
- Format yang benar: `https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback`

### Error: "Access blocked: This app's request is invalid"
- Pastikan Google+ API sudah di-enable di Google Cloud Console
- Periksa Authorized JavaScript origins dan redirect URI

### Error: "Invalid login credentials"
- Pastikan Client ID dan Client Secret sudah benar di Supabase
- Periksa apakah Google Provider sudah diaktifkan di Supabase

### Tombol login tidak bekerja
- Buka Developer Tools (F12) dan periksa Console untuk error messages
- Pastikan file `index.html` diakses melalui HTTP server, bukan langsung (file://)

## Catatan Penting

- **Jangan** buka file HTML langsung (file:// protocol) - harus melalui HTTP server
- Untuk production, ganti URL redirect dengan domain production Anda
- Simpan Client ID dan Client Secret dengan aman, jangan commit ke version control
- Anon key yang digunakan adalah public key, aman untuk di-expose di client-side

## Struktur File

```
debug 2/
├── index.html          # File utama aplikasi
└── PANDUAN_SETUP.md    # Panduan ini
```

## FAQ - Frequently Asked Questions

### ❓ Apakah benar harus pakai Google Cloud Console?
**Ya, benar.** Ini adalah **wajib** dari Google. Tidak ada cara lain untuk menggunakan "Login with Google" tanpa mendaftar di Google Cloud Console. Ini adalah standar keamanan OAuth 2.0 yang digunakan oleh seluruh industri.

### ❓ Apakah ada biaya untuk setup Google Cloud Console?
**Tidak ada biaya.** Google Cloud Console gratis untuk development. Anda hanya perlu akun Google (Gmail) yang sudah ada. Tidak perlu input kartu kredit atau apapun.

### ❓ Berapa lama proses setup?
- Google Cloud Console: **10-15 menit** (sekali saja)
- Supabase configuration: **5 menit**
- Total: **20 menit** untuk setup pertama kali

### ❓ Apakah saya perlu coding di Google Cloud Console?
**Tidak.** Anda hanya perlu:
1. Buat project
2. Enable API
3. Buat OAuth credentials (copy-paste Client ID & Secret)
4. Masukkan credentials tersebut ke Supabase Dashboard

Tidak perlu menulis kode apapun di Google Cloud Console.

### ❓ Apakah aman menggunakan Google Login?
**Sangat aman.** Google Login menggunakan OAuth 2.0 yang adalah standar keamanan internasional. Data user tidak pernah disimpan di aplikasi Anda - hanya token yang diberikan Google.

### ❓ Bisa login tanpa Google? (Email/Password)
**Bisa.** Supabase mendukung multiple authentication methods:
- Google OAuth (yang kita buat sekarang)
- Email/Password
- Magic Link
- GitHub, GitLab, dll

Anda bisa menambahkan method lain kapan saja di Supabase Dashboard.

### ❓ Apakah anon key aman di-expose di client-side?
**Ya, aman.** Anon key adalah public key yang dirancang untuk di-expose di client-side. Supabase menggunakan Row Level Security (RLS) untuk melindungi data di database.

### ❓ Bagaimana cara deploy ke production?
1. Ganti Site URL dan Redirect URLs di Supabase dengan domain production Anda
2. Update Authorized JavaScript origins di Google Cloud Console dengan domain production
3. Deploy `index.html` ke hosting (Netlify, Vercel, dll)
4. Pastikan HTTPS aktif (wajib untuk OAuth)

### ❓ Error "Access blocked: This app's request is invalid"?
Penyebabnya biasanya:
1. OAuth Consent Screen belum dikonfigurasi dengan benar
2. Belum menambahkan test user di OAuth Consent Screen
3. Google+ API / People API belum di-enable

Solusi: Ikuti Langkah 1.1 - 1.3 dengan teliti.

### ❓ Error "redirect_uri_mismatch"?
Penyebab: Redirect URI di Google Cloud Console tidak cocok dengan Supabase callback URL.

Solusi: Pastikan di Google Cloud Console ada:
```
https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback
```

Dan di Supabase URL Configuration ada:
```
http://localhost:5500/**
```

### ❓ Mau tambah provider lain (GitHub, Facebook, dll)?
**Mudah!** Di Supabase Dashboard:
1. Authentication > Providers
2. Pilih provider yang diinginkan (GitHub, Facebook, Twitter, dll)
3. Ikuti instruksi untuk mendapatkan Client ID & Secret dari masing-masing platform
4. Masukkan credentials ke Supabase
5. Save

### ❓ Data user disimpan dimana?
Data user disimpan di Supabase Auth tables. Anda bisa:
- Melihat daftar user di Supabase Dashboard > Authentication > Users
- Menyimpan data tambahan ke tabel database Supabase
- Menggunakan user metadata untuk menyimpan informasi tambahan

### ❓ Bisa customisasi tampilan halaman login?
**Bisa.** File `index.html` bisa dimodifikasi sesuka hati:
- Ganti warna, font, layout
- Tambah logo perusahaan
- Tambah teks atau gambar
- Ubah animasi

Yang penting: Jangan ubah logic Supabase authentication di bagian `<script>`.

## Kesimpulan

**Google Cloud Console adalah WAJIB** untuk menggunakan Google Login. Ini adalah security requirement dari Google yang tidak bisa dilewati. Namun setup-nya mudah dan hanya perlu dilakukan sekali. Setelah setup, Anda tinggal menggunakan `index.html` yang sudah saya buat tanpa perlu mengubah apapun lagi.

Jika ada pertanyaan lain, silakan baca troubleshooting section di atas atau periksa console browser (F12) untuk error messages yang lebih detail.
