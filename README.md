# C.A.P.S. Personal Style Inventory — HCR.ID

Aplikasi kuesioner online (Analyzer / Controller / Promotor / Supporter) dengan:
- **Dashboard Peserta** — isi 18 pertanyaan, tidak melihat hasil.
- **Dashboard Admin** — login kata sandi, lihat semua jawaban, skor otomatis (O/G/D/I), dan kode hasil.

Aplikasi ini adalah **1 file statis** (`index.html`) — tidak butuh server/backend
sendiri. Data jawaban disimpan di **Firebase Firestore** (gratis untuk skala kecil).

---

## Langkah 1 — Buat project Firebase & Firestore

1. Buka [console.firebase.google.com](https://console.firebase.google.com) → **Add project** → beri nama (mis. `caps-hcr-id`) → selesaikan wizard.
2. Di sidebar kiri, klik **Build → Firestore Database** → **Create database**.
   - Pilih lokasi server (mis. `asia-southeast2 (Jakarta)`).
   - Pilih mode **Production mode** (kita sudah sediakan aturan keamanannya sendiri, lihat langkah 3).
3. Setelah database aktif, buka tab **Rules**, hapus isinya, lalu **copy-paste seluruh isi file `firestore.rules`** dari folder ini, klik **Publish**.
4. Kembali ke **Project settings** (ikon gerigi di sidebar) → scroll ke **Your apps** → klik ikon **`</>` (Web)** → beri nickname (mis. `caps-web`) → **Register app**.
5. Firebase akan menampilkan objek `firebaseConfig` seperti ini:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "caps-hcr-id.firebaseapp.com",
     projectId: "caps-hcr-id",
     storageBucket: "caps-hcr-id.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```
   **Salin nilai-nilai ini.**

## Langkah 2 — Masukkan config ke `index.html`

1. Buka `index.html` di editor teks apa saja.
2. Cari bagian ini (gunakan Ctrl+F, cari `firebaseConfig`):
   ```js
   const firebaseConfig = {
     apiKey: "GANTI_API_KEY",
     authDomain: "GANTI.firebaseapp.com",
     projectId: "GANTI_PROJECT_ID",
     storageBucket: "GANTI.appspot.com",
     messagingSenderId: "GANTI_SENDER_ID",
     appId: "GANTI_APP_ID"
   };
   ```
3. Ganti semua nilai `"GANTI..."` dengan nilai asli dari Langkah 1.5 di atas. Simpan file.

> Selama masih ada tulisan `"GANTI"` di config, aplikasi akan menampilkan banner peringatan dan tidak bisa menyimpan jawaban — ini sengaja, sebagai pengingat.

## Langkah 3 — (Opsional tapi disarankan) Ganti kata sandi admin

Masih di `index.html`, cari:
```js
const ADMIN_PASSWORD = "hcr2026";
```
Ganti `"hcr2026"` dengan kata sandi pilihanmu.

> Catatan: ini adalah gerbang sederhana di sisi tampilan, bukan sistem login penuh. Cukup untuk mencegah orang iseng, tapi bukan proteksi tingkat enterprise. Lihat catatan keamanan di `firestore.rules` jika data peserta bersifat sensitif.

---

## Langkah 4 — Upload ke GitHub

```bash
cd caps-online
git init
git add .
git commit -m "CAPS Personal Style Inventory"
git branch -M main
git remote add origin https://github.com/USERNAME/NAMA-REPO.git
git push -u origin main
```
(Ganti `USERNAME/NAMA-REPO` dengan repo GitHub milikmu — buat dulu repo kosong di github.com jika belum ada.)

---

## Langkah 5 — Deploy

### Opsi A: Vercel (paling cepat)
1. Buka [vercel.com](https://vercel.com) → **Add New → Project**.
2. Pilih **Import Git Repository**, pilih repo yang baru di-push.
3. Framework preset: pilih **Other** (situs statis, tidak perlu build command).
4. Klik **Deploy**. Selesai dalam ~30 detik — kamu akan dapat URL seperti `https://nama-repo.vercel.app`.
5. Setiap kali kamu `git push`, Vercel otomatis deploy ulang.

### Opsi B: Firebase Hosting (satu ekosistem dengan Firestore)
```bash
npm install -g firebase-tools
firebase login
cd caps-online
firebase init hosting
# - Pilih project Firebase yang sama dengan Langkah 1
# - Public directory: isi "." (folder ini sendiri)
# - Configure as single-page app: No
# - Jangan overwrite index.html
firebase deploy
```
Firebase akan memberi URL seperti `https://caps-hcr-id.web.app`.

---

## Struktur data di Firestore

Setiap dokumen di koleksi `submissions` berisi:
```json
{
  "name": "Nama Peserta",
  "answers": { "1": "A", "2": "B", "...": "..." },
  "totals": { "O": 5, "G": 4, "D": 6, "I": 3 },
  "code": "GD",
  "resultName": "Controller",
  "timestamp": "2026-08-12T10:00:00.000Z"
}
```
Kamu bisa melihat/mengekspor data ini langsung dari Firebase Console (Firestore Database → tab Data) kapan saja, terpisah dari dashboard admin di aplikasi.

---

## Berkas dalam folder ini
- `index.html` — seluruh aplikasi (frontend + koneksi Firestore).
- `firestore.rules` — aturan keamanan database, tempel di Firebase Console.
- `README.md` — panduan ini.
