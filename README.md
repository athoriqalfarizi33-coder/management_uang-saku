# Pocket Money Manager

Uang saku sering habis sebelum waktunya. Program ini membantu siswa mencatat uang masuk dan keluar, melihat pengeluaran per kategori, dan memperkirakan berapa rupiah yang bisa dipakai setiap hari.

## Cara pakai

1. Ekstrak ZIP dan buka folder **PocketMoneyManager** di VS Code.
2. Buka terminal di folder tersebut. Jalankan `python pocket_money.py`. Jika perintahnya berbeda di komputermu, coba `py pocket_money.py` di Windows atau `python3 pocket_money.py` di macOS dan Linux.
3. Pilih **1** untuk mencatat uang masuk. Nominal bisa ditulis seperti `50000` atau `50.000`.
4. Pilih **2** untuk mencatat uang keluar. Ketik kategori sesuai pengeluaranmu, misalnya jajan, transportasi, atau skincare. Jika ada uang yang disisihkan, kamu bisa mencatatnya di kategori tabungan.
5. Pilih **3** untuk melihat ringkasan. Isi jumlah hari sampai uang saku berikutnya untuk mendapat batas belanja harian.
6. Pilih **4** untuk keluar. Gunakan menu **5** jika kamu ingin mengosongkan catatan dan mulai periode baru.

## Contoh

Masukkan uang saku Rp50.000. Catat Rp30.000 untuk skincare dan Rp5.000 untuk jajan. Ringkasan menampilkan pengeluaran Rp35.000 dan saldo Rp15.000. Kalau tersisa dua hari, saran batas belanjanya Rp7.500 per hari.

Peringatan muncul saat saldo tinggal 20% atau kurang dari total uang masuk pada periode itu. Batas harian dihitung dari saldo dibagi sisa hari, lalu dibulatkan ke bawah. Kalau saldo sudah minus, batas hariannya Rp0.

## Teknologi

Program berjalan di terminal dengan Python 3. Fungsi Python menangani menu dan perhitungan. Daftar transaksi dikelompokkan dengan kamus Python. Modul bawaan SQLite menyimpan catatan di `uang_saku.db`, yang dibuat otomatis di folder program. Catatan tetap bisa dibuka saat program dijalankan lagi.

## Penggunaan AI

ChatGPT membantu menyusun menu, merapikan input nominal, dan menguji perhitungan. Salah satu permintaan dalam percakapan ini adalah membuat pencatatan keuangan yang mengategorikan pengeluaran dan memberi peringatan saat mendekati batas anggaran. Contoh nominal dan kategori berasal dari pengguna. Lihat `AI_LOGBOOK.md` untuk catatan tiap tahap.

## Identitas tim

Isi nama lengkap, kelas, jurusan, dan asal sekolah tiap anggota sebelum mengumpulkan karya.
