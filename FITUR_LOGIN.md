# Fitur Login yang Tersedia

## ✅ Fitur yang Sudah Diimplementasikan

### 1. Login dengan Google
- Tombol "Masuk dengan Google" dengan desain modern
- Menggunakan OAuth 2.0 via Supabase
- Menampilkan foto profil, nama, dan email user setelah login
- Session management (auto-login jika session masih aktif)

### 2. Logout (Keluar)
- Tombol **"Keluar"** berwarna merah
- Mengakhiri session dan menghapus data login
- Kembali ke halaman login
- Error handling untuk kegagalan logout

### 3. Ganti Akun (Switch Account) - BARU!
- Tombol **"Ganti Akun"** berwarna biru
- Fitur untuk login dengan akun Google yang berbeda
- Proses: Logout → Langsung ke Google Account Chooser
- Menggunakan `prompt: 'select_account'` untuk memaksa Google menampilkan pemilihan akun

---

## 🎨 Tampilan UI

### Sebelum Login:
```
┌─────────────────────────────────┐
│                                 │
│        Selamat Datang            │
│  Masuk dengan akun Google Anda   │
│                                 │
│   [🔵 Masuk dengan Google]      │
│                                 │
└─────────────────────────────────┘
```

### Setelah Login:
```
┌─────────────────────────────────┐
│      [Foto Profil 80x80]        │
│         Nama User                │
│      user@example.com            │
│                                 │
│  [Ganti Akun] [Keluar]          │
│                                 │
└─────────────────────────────────┘
```

---

## 🔄 Alur Kerja

### Login Flow:
1. User klik "Masuk dengan Google"
2. Browser redirect ke halaman login Google
3. User login dengan akun Google
4. Google redirect kembali ke aplikasi
5. Data user ditampilkan (foto, nama, email)

### Logout Flow:
1. User klik "Keluar"
2. Session diakhiri
3. Tampilan kembali ke halaman login
4. User perlu login kembali untuk mengakses aplikasi

### Switch Account Flow:
1. User klik "Ganti Akun"
2. Logout dari akun saat ini
3. Langsung redirect ke Google Account Chooser
4. User pilih akun Google yang berbeda
5. Login dengan akun baru
6. Data akun baru ditampilkan

---

## 🎯 Keunggulan Fitur

### Logout:
✅ Sederhana dan cepat
✅ Membersihkan session sepenuhnya
✅ Aman untuk perangkat publik/shared

### Ganti Akun:
✅ Tidak perlu logout lalu login manual
✅ Langsung ke account chooser Google
✅ Berguna untuk testing dengan multiple accounts
✅ User experience yang smooth

---

## 💡 Penggunaan

### Kapan Pakai "Keluar" (Logout)?
- Selesai menggunakan aplikasi
- Ingin keluar sepenuhnya
- Menggunakan perangkat shared/public
- Ingin memastikan session dihapus

### Kapan Pakai "Ganti Akun" (Switch Account)?
- Ingin login dengan akun Google yang berbeda
- Testing dengan multiple accounts
- Punya akun personal dan work yang berbeda
- Ingin cepat berpindah akun tanpa logout manual

---

## 🔧 Teknis

### Logout Implementation:
```javascript
logoutBtn.addEventListener('click', async () => {
    const { error } = await supabaseClient.auth.signOut();
    // Handle success/error
});
```

### Switch Account Implementation:
```javascript
switchAccountBtn.addEventListener('click', async () => {
    // 1. Logout dulu
    await supabaseClient.auth.signOut();
    
    // 2. Login lagi dengan prompt: 'select_account'
    setTimeout(async () => {
        await supabaseClient.auth.signInWithOAuth({
            provider: 'google',
            options: {
                prompt: 'select_account'  // Force account chooser
            }
        });
    }, 500);
});
```

**Parameter `prompt: 'select_account'`** adalah kunci yang memaksa Google menampilkan account chooser meskipun user sudah login dengan akun tertentu.

---

## 📱 Responsive Design

Kedua tombol (Keluar & Ganti Akun) sudah dioptimalkan untuk:
- ✅ Desktop (side by side)
- ✅ Tablet (side by side dengan wrap)
- ✅ Mobile (stack vertically jika needed)

Menggunakan flexbox dengan `flex-wrap: wrap` untuk memastikan tombol tetap rapi di semua ukuran layar.

---

## 🎨 Styling

### Tombol Keluar (Logout):
- Warna: Merah (#dc3545)
- Hover: Merah gelap (#c82333)
- Icon: Tidak ada (text only)
- Posisi: Kanan

### Tombol Ganti Akun (Switch Account):
- Warna: Biru Google (#4285f4)
- Hover: Biru gelap (#3367d6)
- Icon: Tidak ada (text only)
- Posisi: Kiri

### Button Group:
- Layout: Flexbox horizontal
- Gap: 10px antar tombol
- Alignment: Center
- Wrap: Auto (untuk mobile)

---

## 🚀 Testing

### Test Logout:
1. Login dengan akun Google
2. Klik "Keluar"
3. ✅ Harus kembali ke halaman login
4. ✅ Data user hilang
5. ✅ Session dihapus

### Test Ganti Akun:
1. Login dengan akun Google (misal: akun1@gmail.com)
2. Klik "Ganti Akun"
3. ✅ Harus muncul Google account chooser
4. Pilih akun berbeda (misal: akun2@gmail.com)
5. ✅ Harus login dengan akun baru
6. ✅ Data user update dengan akun baru

---

## 📝 Catatan Penting

1. **Session Management**: Kedua aksi (logout & switch account) menggunakan Supabase auth methods
2. **Error Handling**: Kedua tombol memiliki error handling dan console logging untuk debugging
3. **User Experience**: Switch account menggunakan setTimeout 500ms untuk memastikan logout selesai sebelum login kembali
4. **Security**: Session selalu dihapus sebelum login dengan akun baru

---

## 🎯 Kesimpulan

Fitur logout dan ganti akun sudah **lengkap dan siap pakai**:

- ✅ Tombol "Keluar" untuk logout penuh
- ✅ Tombol "Ganti Akun" untuk switch account
- ✅ Kedua tombol sudah di-style dengan baik
- ✅ Error handling sudah diterapkan
- ✅ Console logging untuk debugging
- ✅ Responsive design untuk semua device

Silakan test kedua tombol ini di aplikasi Anda!