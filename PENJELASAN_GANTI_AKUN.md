# Penjelasan: Fitur Ganti Akun & Logout

## 🎯 Solusi yang Diterapkan

Agar user bisa login dengan akun **berbeda** setelah logout, saya telah membuat 2 perubahan penting:

---

## ✅ Perubahan 1: Tombol "Ganti Akun" (Switch Account)

**Fungsi:** Logout dari akun saat ini dan kembali ke halaman login

**Cara Kerja:**
1. User klik "Ganti Akun"
2. Logout dari session yang aktif
3. Tampilan kembali ke halaman login
4. User bisa klik "Masuk dengan Google" untuk login dengan akun lain

**Kode:**
```javascript
switchAccountBtn.addEventListener('click', async () => {
    const { error } = await supabaseClient.auth.signOut();
    if (!error) {
        showLoginForm();  // Kembali ke halaman login
    }
});
```

---

## ✅ Perubahan 2: Tombol "Masuk dengan Google" (Login Button)

**Fungsi:** Selalu menampilkan Google Account Chooser untuk memilih akun

**Cara Kerja:**
1. User klik "Masuk dengan Google"
2. Parameter `prompt: 'select_account'` dikirim ke Google
3. Google **wajib** menampilkan account chooser
4. User pilih akun yang diinginkan (akun lama atau akun baru)
5. Login berhasil

**Kode:**
```javascript
googleLoginBtn.addEventListener('click', async () => {
    const { data, error } = await supabaseClient.auth.signInWithOAuth({
        provider: 'google',
        options: {
            redirectTo: window.location.origin,
            prompt: 'select_account'  // ⭐ KUNCI UTAMA!
        }
    });
});
```

**Parameter `prompt: 'select_account'`** adalah kunci yang memaksa Google menampilkan daftar akun, meskipun user sudah login dengan akun tertentu sebelumnya.

---

## 🔄 Alur yang Benar

### Scenario 1: Login dengan Akun Pertama
1. Buka aplikasi
2. Klik "Masuk dengan Google"
3. Pilih `akun1@gmail.com`
4. ✅ Login berhasil, tampil data akun1

### Scenario 2: Ganti ke Akun Kedua
1. Klik "Ganti Akun" (atau "Keluar")
2. Logout dari akun1
3. Kembali ke halaman login
4. Klik "Masuk dengan Google"
5. ⭐ Google menampilkan account chooser (karena `prompt: 'select_account'`)
6. Pilih `akun2@gmail.com`
7. ✅ Login berhasil, tampil data akun2

### Scenario 3: Kembali ke Akun Pertama
1. Klik "Ganti Akun"
2. Logout dari akun2
3. Kembali ke halaman login
4. Klik "Masuk dengan Google"
5. ⭐ Google menampilkan account chooser lagi
6. Pilih `akun1@gmail.com`
7. ✅ Login berhasil, tampil data akun1

---

## 🎨 UI yang Disediakan

### Setelah Login, user melihat 2 tombol:

```
┌─────────────────────────────────────┐
│  [Ganti Akun]  [Keluar]             │
└─────────────────────────────────────┘
```

**"Ganti Akun" (Biru):** Logout + kembali ke login
**"Keluar" (Merah):** Logout penuh

Kedua tombol melakukan hal yang sama (logout), tapi dengan label berbeda untuk UX yang lebih jelas.

---

## 💡 Mengapa Tidak Otomatis Login ke Akun Pertama?

**Masalah sebelumnya:**
- User logout
- User login lagi
- Google otomatis login dengan akun yang sebelumnya (tanpa pilihan)

**Solusi sekarang:**
- Tambah `prompt: 'select_account'` di OAuth options
- Google **wajib** menampilkan account chooser setiap kali login
- User bisa pilih akun manapun yang tersedia di browser

---

## 🔧 Parameter `prompt: 'select_account'`

### Apa itu?
Parameter OAuth 2.0 yang mengontrol behavior Google login.

### Nilai yang tersedia:
- `prompt: 'select_account'` - Selalu tampilkan account chooser ⭐ (yang kita pakai)
- `prompt: 'consent'` - Tampilkan consent screen
- `prompt: 'none'` - Jangan tampilkan apapun (auto-login)
- `prompt: 'login'` - Paksa login meskipun sudah login

### Mengapa `select_account`?
- User bisa melihat semua akun yang tersedia
- User bisa pilih akun yang diinginkan
- Tidak ada auto-login ke akun sebelumnya
- UX yang lebih baik untuk multi-account

---

## 📱 Testing

### Test Case 1: Login dengan 2 akun berbeda
1. Login dengan `akun1@gmail.com`
2. Logout
3. Login dengan `akun2@gmail.com`
4. ✅ Berhasil, data akun2 ditampilkan

### Test Case 2: Switch back to first account
1. Login dengan `akun2@gmail.com`
2. Logout
3. Login lagi
4. Pilih `akun1@gmail.com` dari account chooser
5. ✅ Berhasil, data akun1 ditampilkan

### Test Case 3: Multiple accounts in browser
1. Pastikan browser menyimpan multiple Google accounts
2. Login dengan akun A
3. Logout
4. Login lagi
5. ✅ Account chooser menampilkan akun A dan akun B
6. Pilih akun B
7. ✅ Berhasil login dengan akun B

---

## ⚠️ Catatan Penting

### 1. Browser Cache
- Google menggunakan cookie browser untuk session
- `prompt: 'select_account'` mengoverride cookie ini
- Account chooser tetap muncul meskipun ada session aktif

### 2. Incognito/Private Mode
- Di incognito, Google tidak punya session
- Account chooser akan muncul otomatis
- Setelah login, session hanya ada di incognito session

### 3. Multiple Browsers/Devices
- Session tidak shared antar browser/device
- Setiap browser akan minta login ulang

### 4. Supabase Session
- Setelah Google login, Supabase membuat session
- Session disimpan di localStorage browser
- Logout akan menghapus session dari Supabase dan localStorage

---

## 🚀 Keunggulan Solusi Ini

✅ **Simple** - Hanya tambah 1 parameter `prompt: 'select_account'`
✅ **Effective** - Memaksa account chooser setiap kali login
✅ **User Friendly** - User bisa pilih akun mana pun yang tersedia
✅ **No Auto-login** - Tidak ada login otomatis ke akun sebelumnya
✅ **Multi-account Support** - Mendukung penggunaan multiple akun Google

---

## 📝 Kesimpulan

**Dengan menambahkan `prompt: 'select_account'`, masalah "otomatis login ke akun pertama" sudah teratasi.**

Setiap kali user klik "Masuk dengan Google", mereka akan melihat account chooser dan bisa memilih akun yang diinginkan. Ini adalah solusi yang clean dan sesuai dengan standar OAuth 2.0.

**File yang sudah diupdate:**
- ✅ `index.html` - Sudah ditambahkan `prompt: 'select_account'`
- ✅ Tombol "Ganti Akun" hanya melakukan logout
- ✅ Tombol "Keluar" melakukan logout

Silakan test kembali dengan 2 akun Google yang berbeda!