# 🏗️ VINZ-MD / RIFF-MD — Web2APK Builder

Build website jadi APK Android via GitHub Actions — **gratis, tanpa API berbayar**.

---

## 📁 Struktur Repo Ini

```
webview-template/                ← nama repo GitHub kamu
├── .github/
│   └── workflows/
│       └── build-apk.yml        ← workflow GitHub Actions (JANGAN dihapus)
├── app/
│   └── src/main/
│       ├── java/com/vinzmd/webview/
│       │   └── MainActivity.java
│       ├── res/
│       │   ├── layout/activity_main.xml
│       │   ├── mipmap-*/           ← icon placeholder (wajib ada)
│       │   └── values/strings.xml
│       └── AndroidManifest.xml
├── app/build.gradle
├── build.gradle
├── settings.gradle
├── gradle/wrapper/
│   └── gradle-wrapper.properties
├── build_icon.png               ← bot akan update file ini tiap build
└── plugins/
    └── tools-buildweb2apk.js    ← copy ke folder plugins bot kamu
```

---

## 🚀 Setup (Sekali Saja)

### 1. Buat Repo GitHub

1. Buat repo **baru** di GitHub (misal: `webview-template`)
2. Set visibility: **Public** atau **Private** (keduanya bisa)
3. Upload semua file dari folder ini ke repo tersebut
4. Pastikan branch default adalah `main`

### 2. Buat Icon Placeholder

Buat file gambar `build_icon.png` (ukuran bebas, contoh 512×512) dan upload ke root repo.
Bot akan menimpa file ini tiap kali ada build baru.

### 3. Buat Icon Dummy di Semua Mipmap

Upload gambar apa saja (PNG polos) ke semua folder `mipmap-*/`:
- `app/src/main/res/mipmap-mdpi/ic_launcher.png`
- `app/src/main/res/mipmap-hdpi/ic_launcher.png`
- `app/src/main/res/mipmap-xhdpi/ic_launcher.png`
- `app/src/main/res/mipmap-xxhdpi/ic_launcher.png`
- `app/src/main/res/mipmap-xxxhdpi/ic_launcher.png`

> ℹ️ Workflow akan menimpa file ini dengan icon dari user.

### 4. Buat GitHub Personal Access Token

1. Buka: https://github.com/settings/tokens/new
2. **Token name**: `vinzmd-builder`
3. **Expiration**: No expiration (atau sesuai kebutuhan)
4. **Scopes yang dicentang**:
   - ✅ `repo` (full)
   - ✅ `workflow`
5. Klik **Generate token**
6. Salin tokennya (hanya tampil sekali!)

### 5. Isi `.env` Bot

```env
# GitHub Actions Builder
GH_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
GH_OWNER=username_github_kamu
GH_REPO=webview-template
GH_WORKFLOW=build-apk.yml
GH_BRANCH=main
BUILD_COST=10
```

### 6. Copy Plugin ke Bot

```bash
cp plugins/tools-buildweb2apk.js /path/ke/bot/plugins/
```

### 7. Daftarkan Command di Main Bot (`riff-bot.js` / `vinz-bot.js`)

Tambahkan ini di bagian **import** (atas file):

```js
const { handleBuildPlugin } = require('./plugins/tools-buildweb2apk');
```

Tambahkan ini di bagian **routing command** (sebelum switch/case):

```js
// ── Web2APK Builder ──────────────────────────────────────────
const buildCmds = ['buildweb2apk','b2a','seticonapk','startbuild','cancelbuild'];
if (buildCmds.includes(command)) {
  await handleBuildPlugin(sock, msg, command, params);
  continue;
}
```

---

## 📱 Cara Pakai (User)

```
Langkah 1 — Mulai build:
  .buildweb2apk https://panel.com,PanelVINZ

Langkah 2 — Kirim foto icon:
  [kirim foto] dengan caption: .seticonapk

Langkah 3 — Mulai proses:
  .startbuild

Batalkan: .cancelbuild
```

---

## ⚙️ Environment Variables

| Variable     | Wajib | Default         | Keterangan                            |
|-------------|-------|-----------------|---------------------------------------|
| GH_TOKEN    | ✅    | -               | GitHub Personal Access Token          |
| GH_OWNER    | ✅    | -               | Username/org pemilik repo             |
| GH_REPO     | ✅    | -               | Nama repo template                    |
| GH_WORKFLOW | ❌    | build-apk.yml   | Nama file workflow                    |
| GH_BRANCH   | ❌    | main            | Branch repo                           |
| BUILD_COST  | ❌    | 10              | Koin yang dipotong tiap build         |

---

## 📊 Estimasi Waktu Build

| Tahap                  | Durasi    |
|------------------------|-----------|
| Upload icon ke GitHub  | ~2 detik  |
| Trigger + antri        | ~10 detik |
| Download dependencies  | ~1-2 menit (cached setelah pertama) |
| Compile + package      | ~2-4 menit |
| Signing + upload       | ~30 detik |
| **Total**              | **~4-7 menit** |

> ✅ Gradle dependencies di-cache otomatis oleh GitHub Actions, build ke-2 dst jauh lebih cepat.

---

## ❌ Troubleshooting

| Error | Penyebab | Solusi |
|-------|----------|--------|
| `GH_TOKEN belum diisi` | `.env` kosong | Isi GH_TOKEN di .env |
| `Gagal trigger workflow` | Token tidak punya izin `workflow` | Buat token baru dengan scope `workflow` |
| `Workflow run tidak ditemukan` | Repo/branch salah | Cek GH_REPO dan GH_BRANCH |
| `Gagal download artifact` | Build gagal di GitHub | Lihat log di GitHub Actions |
| Build timeout >10 menit | GitHub lambat / antrian penuh | Coba lagi beberapa menit kemudian |

---

## 💡 Tips

- **Gratis**: GitHub Actions gratis 2.000 menit/bulan (free tier), cukup untuk ~400 build
- **Cepat**: Setelah build pertama, Gradle cache membuat build berikutnya 2× lebih cepat
- **Aman**: APK debug (tidak perlu keystore), langsung bisa diinstall
- **Custom**: Bisa modifikasi `MainActivity.java` untuk fitur tambahan (splash screen, dll)
