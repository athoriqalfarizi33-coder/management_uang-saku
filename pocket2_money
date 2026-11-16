"""Catatan uang saku yang bisa dijalankan lewat terminal."""

import re
import sqlite3
from contextlib import closing
from pathlib import Path


BERKAS_DATA = Path(__file__).with_name("uang_saku.db")
AMBANG_PERINGATAN = 20
NOMINAL_MAKSIMUM = 9_223_372_036_854_775_807


def format_rupiah(nominal):
    angka = f"{abs(nominal):,}".replace(",", ".")
    tanda = "-" if nominal < 0 else ""
    return f"{tanda}Rp{angka}"


def siapkan_database(koneksi):
    with koneksi:
        koneksi.execute(
            """
            CREATE TABLE IF NOT EXISTS transaksi (
                id INTEGER PRIMARY KEY,
                jenis TEXT NOT NULL CHECK (jenis IN ('masuk', 'keluar')),
                kategori TEXT NOT NULL,
                nominal INTEGER NOT NULL CHECK (nominal > 0)
            )
            """
        )


def baca_nominal():
    while True:
        teks = input("Nominal rupiah (Enter untuk batal): ").strip()
        if not teks:
            return None

        if teks.lower().startswith("rp"):
            teks = teks[2:].strip()

        if not re.fullmatch(r"(?:[0-9]+|[0-9]{1,3}(?:\.[0-9]{3})+)", teks):
            print("Ketik nominal seperti 50000 atau 50.000.")
            continue

        angka = teks.replace(".", "").lstrip("0") or "0"
        if len(angka) > 19 or int(angka) > NOMINAL_MAKSIMUM:
            print("Nominal terlalu besar.")
            continue

        nominal = int(angka)
        if nominal > 0:
            return nominal
        print("Nominal harus lebih dari Rp0.")


def hitung_ringkasan(koneksi):
    transaksi = koneksi.execute(
        "SELECT jenis, kategori, nominal FROM transaksi ORDER BY id"
    ).fetchall()

    uang_masuk = 0
    pengeluaran = {}
    for jenis, kategori, nominal in transaksi:
        if jenis == "masuk":
            uang_masuk += nominal
        else:
            pengeluaran[kategori] = pengeluaran.get(kategori, 0) + nominal

    return uang_masuk, pengeluaran


def tampilkan_peringatan(uang_masuk, saldo):
    if saldo < 0:
        print(f"Pengeluaran melebihi uang masuk sebesar {format_rupiah(-saldo)}.")
    elif uang_masuk > 0 and saldo * 100 <= uang_masuk * AMBANG_PERINGATAN:
        print(f"Saldo mulai menipis. Tersisa {AMBANG_PERINGATAN}% atau kurang.")


def catat_transaksi(koneksi, jenis):
    kategori = ""
    if jenis == "keluar":
        kategori = " ".join(input("Kategori (contoh jajan): ").split()).lower()
        if not kategori:
            print("Pencatatan dibatalkan.")
            return

    nominal = baca_nominal()
    if nominal is None:
        print("Pencatatan dibatalkan.")
        return

    with koneksi:
        koneksi.execute(
            "INSERT INTO transaksi (jenis, kategori, nominal) VALUES (?, ?, ?)",
            (jenis, kategori, nominal),
        )

    uang_masuk, pengeluaran = hitung_ringkasan(koneksi)
    saldo = uang_masuk - sum(pengeluaran.values())
    print(f"Tercatat {format_rupiah(nominal)}. Saldo sekarang {format_rupiah(saldo)}.")
    if jenis == "keluar":
        tampilkan_peringatan(uang_masuk, saldo)


def tampilkan_ringkasan(koneksi):
    uang_masuk, pengeluaran = hitung_ringkasan(koneksi)
    uang_keluar = sum(pengeluaran.values())
    saldo = uang_masuk - uang_keluar

    print("\n=== Ringkasan uang saku ===")
    print(f"Uang masuk        {format_rupiah(uang_masuk)}")
    print(f"Total pengeluaran {format_rupiah(uang_keluar)}")
    for kategori, nominal in pengeluaran.items():
        print(f"  {kategori.capitalize()} {format_rupiah(nominal)}")
    print(f"Sisa saldo        {format_rupiah(saldo)}")

    tampilkan_peringatan(uang_masuk, saldo)

    while True:
        teks = input(
            "Sisa hari sampai uang saku berikutnya (Enter untuk kembali): "
        ).strip()
        if not teks:
            return

        try:
            sisa_hari = int(teks)
        except ValueError:
            sisa_hari = 0

        if sisa_hari > 0:
            batas_harian = max(saldo, 0) // sisa_hari
            print(f"Saran batas harian {format_rupiah(batas_harian)} per hari.")
            return
        print("Isi jumlah hari dengan angka bulat, minimal 1.")


def mulai_periode_baru(koneksi):
    jawaban = input("Kosongkan catatan periode ini? (y/t): ").strip().lower()
    if jawaban != "y":
        print("Catatan periode ini masih ada.")
        return

    with koneksi:
        koneksi.execute("DELETE FROM transaksi")
    print("Periode baru dimulai. Saldo Rp0.")


def jalankan_menu(koneksi):
    while True:
        print("\n=== Pocket Money Manager ===")
        print("1. Catat uang masuk")
        print("2. Catat uang keluar")
        print("3. Lihat ringkasan")
        print("4. Keluar")
        print("5. Mulai periode baru")
        pilihan = input("Pilih menu (1 sampai 5): ").strip()

        if pilihan == "1":
            catat_transaksi(koneksi, "masuk")
        elif pilihan == "2":
            catat_transaksi(koneksi, "keluar")
        elif pilihan == "3":
            tampilkan_ringkasan(koneksi)
        elif pilihan == "4":
            print("Sampai jumpa!")
            return
        elif pilihan == "5":
            mulai_periode_baru(koneksi)
        else:
            print("Pilih angka 1 sampai 5.")


def main():
    try:
        with closing(sqlite3.connect(BERKAS_DATA)) as koneksi:
            siapkan_database(koneksi)
            jalankan_menu(koneksi)
    except (EOFError, KeyboardInterrupt):
        print("\nSampai jumpa!")
    except sqlite3.Error as galat:
        print(f"\nCatatan gagal dibuka atau disimpan. Pesan SQLite ({galat})")
        return 1
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
