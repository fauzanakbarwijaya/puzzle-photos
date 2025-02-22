# Puzzle Generator

Puzzle Generator adalah aplikasi berbasis Vue.js yang memungkinkan pengguna untuk mengunggah gambar sendiri atau menggunakan gambar acak dari Picsum Photos untuk diubah menjadi puzzle interaktif.

## Fitur
- **Unggah Gambar:** Pengguna dapat mengunggah gambar sendiri untuk dijadikan puzzle.
- **Generate Gambar Acak:** Menggunakan API dari Picsum Photos untuk mendapatkan gambar acak.
- **Pilih Ukuran Puzzle:** 3x3, 4x4, atau 5x5.
- **Timer:** Pemain diberikan waktu 5 menit untuk menyelesaikan puzzle.
- **Drag and Drop:** Sistem interaktif untuk menyusun kembali potongan puzzle.

## Teknologi yang Digunakan
- **Vue.js** (Composition API, ref, computed, event handling)
- **Bootstrap 5** (Styling UI)
- **JavaScript Vanilla** (Canvas API, Drag & Drop API)

## Cara Menjalankan
1. Pastikan Anda memiliki Vue.js di proyek Anda.
2. Simpan file komponen ini sebagai `App.vue` atau komponen Vue lainnya.
3. Jalankan proyek Vue Anda menggunakan `npm run dev` jika menggunakan Vite atau `vue serve` jika menggunakan Vue CLI.

## Cara Menggunakan
1. **Unggah gambar** atau klik "Generate Random Image" untuk mendapatkan gambar acak.
2. Pilih ukuran puzzle.
3. Klik "Mulai" untuk memulai game.
4. Susun potongan puzzle dengan drag and drop hingga kembali ke bentuk asli.

## Struktur Kode
- **Image Handling:** `handleImageUpload` untuk gambar dari file dan `generateRandomImage` untuk gambar dari API.
- **Puzzle Generation:** `createPuzzle` memotong gambar menjadi beberapa bagian dengan `Canvas API`.
- **Drag & Drop Mechanism:** `onDragStart`, `onDrop`, dan `onDragOver` menangani pertukaran posisi potongan puzzle.
- **Timer:** `startTimer` mengelola waktu permainan.

## Catatan Teknis
- Gambar dari Picsum Photos harus dikonversi ke `dataURL` untuk menghindari masalah **tainted canvas**.
- Puzzle hanya bisa dimainkan setelah gambar di-load sepenuhnya.
- Menggunakan **computed properties** untuk menghitung ukuran potongan puzzle secara dinamis.

## Pengembangan Lebih Lanjut
- **Leaderboard:** Menambahkan sistem skor berdasarkan waktu penyelesaian.
- **Animation Effects:** Animasi untuk transisi potongan puzzle.
- **Mode Multiplayer:** Kompetisi real-time dalam menyusun puzzle.

---
Dibuat dengan ❤️ menggunakan Vue.js 🚀

