📋 Deskripsi Workflow: Telegram → OCR → LLM → Google Sheets + Google Drive + Telegram Reply

🎯 Tujuan:

Membuat sistem otomatis yang menerima foto struk (nota) dari Telegram, mengekstrak isinya (tanggal, lokasi, saldo, dll), lalu:

    Menyimpan foto ke Google Drive

    Mengekstrak isi struk dengan OCR & LLM (AI)

    Menyimpan hasilnya ke Google Sheets

    Mengirimkan ringkasan hasil kembali ke pengirim Telegram

🧱 Alur Node:
1. Telegram Trigger

    Menerima kiriman foto dari user Telegram

    Download otomatis file gambar

2. Write Binary File

    Menyimpan file gambar ke folder /data/ dalam Docker n8n

    Format biner digunakan untuk OCR & Google Drive

3. Upload File (Google Drive)

    Mengupload file foto ke Google Drive secara otomatis

    Nama file dinamakan dinamis berdasarkan tanggal, misal: nota_2025-07-01_20-36-00.jpg

4. Run OCR

    Menggunakan tesseract OCR (diinstal di dalam container)

    Mengekstrak teks dari gambar nota

5. LLM Extractor (OpenAI Chat Model)

    Mengambil teks hasil OCR

    Menjalankan prompt khusus untuk menghasilkan JSON terstruktur, seperti:

{
  "date_time": "2020-10-02 15:20:24",
  "location": "GERBANG SERANG BARAT",
  "origin_gate": "CILEGON BARAT",
  "transaction_id": "THRIF Re 138000",
  ...
}

6. Code

    Membersihkan output LLM dari format markdown (json ... )

    Melakukan JSON.parse() terhadap string untuk menjadikannya objek

    Menangani error jika hasil tidak valid

7. Set Extracted Fields

    Menyimpan nilai-nilai dari JSON ke variabel standar untuk dikirim ke Sheets dan Telegram

    Field: date_time, location, origin_gate, amount, balance, dll.

8. Append row in sheet (Google Sheets)

    Menambahkan baris baru ke spreadsheet Google Sheets kamu

    Kolom disesuaikan dengan format data yang diekstrak

9. Telegram2 (Send Message)

    Mengirim pesan ringkasan kembali ke pengirim Telegram

    Contoh isi pesan:

Struk berhasil diproses:

📅 Tanggal: 2020-10-02 15:20:24  
📍 Lokasi: GERBANG SERANG BARAT  
🔁 Dari: CILEGON BARAT  
🚗 Golongan: GOL III  
💳 e-Toll Card: 145000120516519  
💰 Tarif: Rp 138000  
💰 Saldo: Rp 208600  
🏦 Bank: BCA  
📡 Operator: ASTRA Infra

💡 Catatan Teknis

    OCR berjalan di container Docker n8n dengan tesseract terinstal

    Model LLM (OpenAI) digunakan untuk mengidentifikasi isi struk dari berbagai jenis: tol, e-toll, Indomaret, SPBU, dsb.

    Error handling aman: jika parsing gagal, tidak crash — dan Telegram akan menerima fallback respon

    Workflow dapat dikembangkan untuk: pengkategorian pengeluaran, dashboard visualisasi, filter pengirim, dan sebagainya

✅ Ringkasan Benefit

    📷 Struk bisa difoto saja, tidak perlu input manual

    🧠 AI mengenali dan memformat data tanpa template

    🗃️ Disimpan rapi di Google Sheets untuk laporan

    ☁️ Foto tersimpan di Google Drive

    🔁 User dapat konfirmasi otomatis di Telegram
