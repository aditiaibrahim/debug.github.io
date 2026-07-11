# Diagnostik: Tombol Login Tidak Bekerja

Gunakan panduan ini untuk mengidentifikasi dan memperbaiki masalah.

## Langkah 1: Buka Developer Tools (F12)

1. Buka aplikasi di browser
2. Tekan **F12** atau klik kanan > **Inspect**
3. Klik tab **Console**
4. Klik tombol "Masuk dengan Google"
5. Perhatikan apa yang muncul di console

---

## Checklist Diagnostik

### ✅ 1. Cek Console untuk Error Messages

**Jika muncul error di console, catat pesan errornya:**

- ❌ "Access blocked: This app's request is invalid"
  - **Penyebab:** OAuth Consent Screen belum dikonfigurasi
  - **Solusi:** Ikuti Langkah 1.3 di PANDUAN_SETUP.md

- ❌ "redirect_uri_mismatch"
  - **Penyebab:** Redirect URI tidak cocok
  - **Solusi:** Pastikan di Google Cloud Console ada: `https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback`

- ❌ "Invalid login credentials"
  - **Penyebab:** Client ID/Secret salah atau Google Provider belum aktif
  - **Solusi:** Cek Langkah 2 di PANDUAN_SETUP.md

- ❌ "Uncaught SyntaxError: Identifier 'supabase' has already been declared"
  - **Status:** ✅ SUDAH DIPERBAIKI di versi terbaru
  - **Solusi:** Refresh browser (Ctrl+F5)

- ❌ "supabase is not defined"
  - **Penyebab:** CDN Supabase gagal dimuat
  - **Solusi:** Cek koneksi internet, atau coba refresh

---

### ✅ 2. Cek Apakah Google Provider Aktif di Supabase

1. Buka [Supabase Dashboard](https://app.supabase.com/)
2. Pilih project: **ziwjmsjvhzivmvqimiei**
3. Menu kiri: **Authentication** > **Providers**
4. Cari **Google**
5. Pastikan toggle **"Enable Sign in with Google"** adalah **ON** (biru)
6. Pastikan **Client ID** dan **Client Secret** sudah diisi

**Jika belum:**
- Ikuti Langkah 2 di PANDUAN_SETUP.md
- Jangan lupa klik **Save**

---

### ✅ 3. Cek URL Configuration di Supabase

1. Di Supabase Dashboard: **Authentication** > **URL Configuration**
2. Cek **Site URL**:
   - Harus sesuai dengan URL dimana aplikasi berjalan
   - Contoh: `http://localhost:5500` atau `http://127.0.0.1:5500`
3. Cek **Redirect URLs**:
   - Harus ada: `http://localhost:5500/**`
   - Harus ada: `http://127.0.0.1:5500/**`

**Jika belum diisi:**
- Ikuti Langkah 3 di PANDUAN_SETUP.md

---

### ✅ 4. Cek Konfigurasi Google Cloud Console

#### 4.1 OAuth Consent Screen
1. Buka [Google Cloud Console](https://console.cloud.google.com/)
2. Pilih project Anda
3. Menu: **APIs & Services** > **OAuth consent screen**
4. Pastikan status adalah **"PUBLISHED"** atau sudah ada test users
5. Jika masih "Testing", pastikan email Anda ditambahkan sebagai test user

#### 4.2 Credentials
1. Menu: **APIs & Services** > **Credentials**
2. Cari OAuth 2.0 Client ID yang Anda buat
3. Klik edit (pensil icon)
4. Cek **Authorized JavaScript origins**:
   ```
   http://localhost:5500
   http://127.0.0.1:5500
   ```
5. Cek **Authorized redirect URIs**:
   ```
   https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback
   ```

#### 4.3 Enabled APIs
1. Menu: **APIs & Services** > **Enabled APIs**
2. Pastikan ada **Google+ API** atau **Google People API**

---

### ✅ 5. Cek Apakah Menggunakan HTTP Server

**PENTING:** File HTML harus diakses melalui HTTP server, bukan langsung!

❌ **SALAH:** `file:///C:/Users/aditi/Documents/PANDUAN_SETUP.md`
✅ **BENAR:** `http://localhost:5500` atau `http://127.0.0.1:5500`

**Cara menjalankan HTTP Server:**

#### Opsi A: Live Server (VS Code)
1. Install ekstensi "Live Server" di VS Code
2. Klik kanan pada `index.html`
3. Pilih "Open with Live Server"

#### Opsi B: Python
```bash
python -m http.server 5500
```

#### Opsi C: Node.js
```bash
npx http-server -p 5500
```

---

### ✅ 6. Test dengan Console Logs

Setelah memperbaiki semua di atas, buka Console (F12) dan:

1. Refresh halaman (Ctrl+F5)
2. Anda seharusnya melihat log:
   ```
   Tombol login diklik
   Mencoba login dengan Google...
   Response: {data: {...}, error: null}
   Login berhasil, mengarahkan ke Google...
   ```
3. Browser akan redirect ke halaman login Google

**Jika tidak ada log sama sekali:**
- Ada masalah dengan JavaScript
- Cek apakah ada error lain di console
- Coba hard refresh (Ctrl+Shift+R)

---

## Troubleshooting Berdasarkan Error

### Error: "Access blocked: This app's request is invalid"

**Penyebab:**
1. OAuth Consent Screen belum dikonfigurasi
2. Belum menambahkan test user
3. App masih dalam status "Testing" dan email Anda bukan test user

**Solusi:**
1. Google Cloud Console > OAuth Consent Screen
2. Pilih "External"
3. Isi semua field (App name, email, dll)
4. Tambahkan email Anda sebagai test user
5. Atau publish app (untuk development, "Testing" sudah cukup asal email ada di test users)

---

### Error: "redirect_uri_mismatch"

**Penyebab:** Redirect URI di Google Cloud Console tidak cocok

**Solusi:**
1. Google Cloud Console > Credentials
2. Edit OAuth Client ID Anda
3. Di "Authorized redirect URIs", pastikan ada:
   ```
   https://ziwjmsjvhzivmvqimiei.supabase.co/auth/v1/callback
   ```
4. Klik Save

---

### Error: "Invalid login credentials"

**Penyebab:**
1. Client ID atau Client Secret salah di Supabase
2. Google Provider belum di-enable

**Solusi:**
1. Double-check Client ID dan Client Secret
2. Pastikan tidak ada spasi tambahan saat copy-paste
3. Pastikan Google Provider ON di Supabase
4. Klik Save di Supabase

---

### Tombol Tidak Ada Reaksi

**Checklist:**
- [ ] File diakses via HTTP server (bukan file://)
- [ ] Tidak ada error di Console (F12)
- [ ] Console shows "Tombol login diklik" saat tombol diklik
- [ ] Internet connection stabil
- [ ] Supabase CDN berhasil dimuat (cek tab Network di DevTools)

---

## Langkah Verifikasi Cepat

Jalankan checklist ini secara berurutan:

1. ☐ Buka aplikasi via HTTP server (http://localhost:5500)
2. ☐ Buka Developer Tools (F12) > Console
3. ☐ Refresh halaman (Ctrl+F5)
4. ☐ Klik tombol "Masuk dengan Google"
5. ☐ Perhatikan console logs
6. ☐ Apakah ada error? Ya → Cek troubleshooting di atas
7. ☐ Tidak ada error? → Apakah redirect ke Google? Ya → Berhasil!
8. ☐ Tidak redirect? → Cek konfigurasi Supabase dan Google Cloud Console

---

## Jika Masih Tidak Bekerja

Setelah melakukan semua langkah di atas, kumpulkan informasi berikut:

1. **Screenshot Console** (F12 > Console) saat tombol diklik
2. **Screenshot Supabase Dashboard** > Authentication > Providers (Google)
3. **Screenshot Supabase Dashboard** > Authentication > URL Configuration
4. **Screenshot Google Cloud Console** > Credentials (OAuth Client ID)
5. **URL** dimana aplikasi dijalankan (misal: http://localhost:5500)

Dengan informasi ini, masalah bisa diidentifikasi dengan cepat.

---

## Tips Penting

💡 **Always check Console first** - 90% masalah bisa dilihat dari console logs

💡 **Hard refresh** - Tekan Ctrl+Shift+R untuk clear cache

💡 **Incognito mode** - Test di incognito untuk memastikan tidak ada cache/extension yang mengganggu

💡 **Copy-paste credentials carefully** - Pastikan tidak ada spasi tambahan saat copy Client ID/Secret

💡 **Wait 5-10 minutes** - Setelah mengubah konfigurasi di Google Cloud Console, tunggu beberapa menit untuk propagasi