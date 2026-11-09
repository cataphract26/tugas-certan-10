# Segmentasi Polip pada Citra Kolonoskopi dengan U-Net

<!--
  TEMPLATE LAPORAN — Tugas P10 Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA.
  Petunjuk lengkap: https://mctm.web.id/ai-bm/tugas

  Cara memakai:
  - Ganti semua teks yang diawali "✍️" dengan tulisan kelompokmu, lalu hapus tanda ✍️-nya.
  - Blok komentar seperti ini adalah petunjuk; tidak tampil di GitHub. Boleh dihapus.
  - Gambar diambil dari folder hasil/ yang dibuat otomatis oleh notebook.
  - Jangan hapus judul-judul bagian (##).
-->

**Tugas P10 — Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA**

## Anggota kelompok

| Nama | NIM | Username GitHub | Kontribusi utama |
|---|---|---|---|
| Akhmad Rasyid Alfarabi | 123430096 | cataphract26 | Menjalankan eksperimen di Google Colab |
| Geofany Srydevy Sinambela | 123430014 | geofanysinambela15-maz | pembuat laporan |
| Zahra Salim | 123430033 | zahrasalim222333 | Pembuatan Laporan |

## 1. Pertanyaan

Augmentasi apa yang membantu U-Net menemukan polip pada citra kolonoskopi, dan augmentasi apa yang tidak? Kenapa?

## 2. Data & pengaturan

<!-- Isi dari Bagian 2 notebook. Pastikan CEPAT = False untuk hasil akhir. -->

- Dataset: Kvasir-SEG (1000 citra kolonoskopi + mask polip), diperkecil ke 128 × 128 piksel.
- Data uji: 200 citra (tetap). Data latih: 200.
- Model: U-Net mini (±0,5 juta parameter), 40 epoch, 3 ulangan per skenario.
- Dijalankan di: Google Colab T4.

![Contoh data](hasil/contoh_data.png)

## 3. Skenario augmentasi & hipotesis

<!-- Tulis bagian ini SEBELUM menjalankan eksperimen. Commit dulu, baru jalankan.
     Alasan klinis = kenapa variasi ini mungkin (atau tidak mungkin) terjadi pada kolonoskopi sungguhan. -->

| Skenario | Augmentasi (kode) | Alasan klinis | Hipotesis: Dice naik/turun/sama? |
|---|---|---|---|
| Tanpa augmentasi | — | Pembanding | — |
| Flip kiri-kanan dan atas-bawah | v2.RandomHorizontalFlip() & v2.RandomVerticalFlip() | Untuk membuat polip berpindah sisi | Dice sama |
| Rotasi | v2.RandomRotation(degrees=180) | Perputaran kamera di dalam usus cukup sulit dikendalikan dan butuh keahlian khusus | Dice sama |
| Geser & perbesar | v2.RandomAffine(degrees=0, translate=(0.1, 0.1), scale(0.8, 1.2)) | Polip bisa dipotret dari dekat | Dice naik |
| Kecerahan & kontras | v2.ColorJitter(brightness=0.3, contrast-0.3) | Lampu endoskop terangnya tidak selalu sama, tergantung jarak polip dengan lampu | Dice naik |
| Saturasi | v2.ColorJitter(saturation=0.3) | warna pucat akan menyulitkan deteksi. warna pekat akan menyulitkan membedakan mana objek yang diinginkan. nilai yang ditentukan tetap sama | Dice sama |
| Hue (rona warna) | v2.ColorJitter(hue=0.1) | Rona warna tidak perlu diubah untuk melihat petunjuk penting untuk dideteksi | Dice sama |
| Blur | v2.GaussianBlur(kernel_size=5, sigma=(0.1, 2.0)) | Kamera bergerak menyebabkan foto kabur. Blur perlu dikurangi | Dice naik |
| Deformasi elastis | v2.ElasticTransform(alpha=30.0) | Ini dapat membantu untuk meniru bentuk polip, karena bentuk polip dapat berbeda-beda | Dice naik |

Contoh augmentasi yang dicoba:

![Contoh augmentasi](hasil/augmentasi_flip.png)
<!-- Ganti nama file sesuai skenario kalian (lihat isi folder hasil/). -->

## 4. Hasil

| Skenario | Dice (rata-rata ± sb) | IoU (rata-rata ± sb) | Dice tiap ulangan |
|---|---|---|---|
| Tanpa augmentasi | 0.603 ± 0.013 | 0.483 ± 0.013 | 0.588, 0.619, 0.601 |
| Flip | 0.649 ± 0.035 | 0.535 ± 0.032 | 0.599, 0.673, 0.675 |
| Geser & perbesar | 0.539 ± 0.009 | 0.429 ± 0.006 | 0.531, 0.533, 0.552 |
| Rotasi | 0.640 ± 0.032 | 0.525 ± 0.032 | 0.609, 0.684, 0.627 |
| Blur | 0.597 ± 0.015 | 0.481 ± 0.012 | 0.597, 0.616, 0.578 |
| Deformasi elastis | 0.530 ± 0.036 | 0.423 ± 0.029 | 0.505, 0.505, 0.580 |
| Saturasi | 0.530 ± 0.047 | 0.421 ± 0.038 | 0.555, 0.571, 0.463 |
| Hue | 0.577 ± 0.019 | 0.468 ± 0.017 | 0.578, 0.600, 0.554 |
| Kecerahan & kontras | 0.571 ± 0.001 | 0.450 ± 0.004 | 0.570, 0.571, 0.572 |


![Perbandingan Dice](hasil/perbandingan.png)

## 5. Analisis peta kesalahan

![Peta kesalahan](hasil/peta_kesalahan.png)

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->

✍️

## 6. Pembahasan & kesimpulan

<!-- Bandingkan hasil dengan hipotesis di Bagian 3. Hipotesis yang ternyata salah tetap bernilai,
     asalkan dijelaskan kenapa. Perbedaan kecil (lebih kecil dari simpangan baku) belum tentu berarti. -->

A. Pembahasan 


B. Kesimpulan
1. 

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

✍️

## 8. Penggunaan AI

<!-- Wajib diisi, walau tidak memakai AI (tulis "Tidak memakai AI").
     Contoh: "Claude Code (CLI) untuk menulis skenario augmentasi dan memperbaiki error di Bagian 4.
     Draf Bagian 6 ditulis sendiri, lalu dirapikan bahasanya dengan bantuan AI." -->

✍️

## Cara menjalankan ulang

```bash
uv sync
uv run jupyter lab segmentasi_polip.ipynb
```

Atau buka notebook di Google Colab (Runtime → Change runtime type → T4 GPU).

## Referensi
1. Jha, D., dkk. (2020). Kvasir-SEG: A Segmented Polyp Dataset. *MultiMedia Modeling (MMM 2020)*, 451–462.
2. Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. *MICCAI*. arXiv:1505.04597.
3. Yeung, M., Sala, E., Schönlieb, C. B., & Rundo, L. (2021). Focus U-Net: A novel dual attention-gated CNN for polyp segmentation during colonoscopy. Computers in biology and medicine, 137, 104815. https://doi.org/10.1016/j.compbiomed.2021.104815
4. Safarov, S., & Whangbo, T. K. (2021). A-DenseUNet: Adaptive Densely Connected UNet for Polyp Segmentation in Colonoscopy Images with Atrous Convolution. Sensors (Basel, Switzerland), 21(4), 1441. https://doi.org/10.3390/s21041441
5. Ramzan, M., Raza, M., Sharif, M. I., & Kadry, S. (2022). Gastrointestinal Tract Polyp Anomaly Segmentation on Colonoscopy Images Using Graft-U-Net. Journal of personalized medicine, 12(9), 1459. https://doi.org/10.3390/jpm12091459
6. Patel, K., Bur, A. M., & Wang, G. (2021). Enhanced U-Net: A Feature Enhancement Network for Polyp Segmentation. Proceedings of the International Robots & Vision Conference. International Robots & Vision Conference, 2021, 181–188. https://doi.org/10.1109/crv52889.2021.00032 