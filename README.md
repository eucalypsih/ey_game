# ey_game

```
dumpsys package dexopt | grep -A 1 
```
```
dumpsys package dexopt | grep -A 1 com.google.android.youtube
```

1. Paksa Mode Performa Maksimal pada Hardware:
```
cmd power set-fixed-performance-mode-enabled true
```
(Perintah ini memaksa CPU dan GPU berjalan di frekuensi tinggi tanpa menunggu beban game memuncak, mengurangi kendala frame drop tiba-tiba).


2. Kompilasi Ulang Data Game ke Mode Cepat (Speed):
```
cmd package compile -m speed -f com.mobile.legends
```
(Langkah ini akan memaksa sistem mengompilasi ulang seluruh baris kode Mobile Legends ke dalam kode mesin bawaan, sehingga game termuat jauh lebih cepat dan enteng di latar belakang).[Scribd](https://www.scribd.com/document/892412842/FIX-LAGS-ML)


```
cmd package compile -m speed -f com.google.android.youtube
```
Perintah yang Anda ketik sudah 100% benar secara sintaksis. Proses kompilasi ini akan mengubah seluruh kode aplikasi YouTube menjadi kode mesin (AOT - Ahead-Of-Time compilation).

Namun, berbeda dengan perintah device_config yang instan, perintah cmd package compile membutuhkan proses pemrosesan data di latar belakang. Saat Anda menekan Enter di Termux (rish), terminal akan tertahan (tidak memunculkan teks baru) selama 30 detik hingga 2 menit tergantung kecepatan memori (UFS) HP Xiaomi Anda.

Tunggu saja sampai terminal memunculkan teks sukses seperti berikut:
`Success`

💡 Manfaat Kompilasi "Speed" untuk YouTube
Setelah statusnya muncul `Success`, Anda akan langsung merasakan perubahan positif pada aplikasi YouTube:
- Buka Aplikasi Lebih Instan: YouTube akan terbuka jauh lebih cepat dari kondisi mati (cold start).
- Gulir Beranda Super Mulus: Efek patah-patah (stuttering) saat Anda menggeser beranda video yang penuh dengan gambar thumbnail akan hilang.
- Buka Video Lebih Cepat: Waktu pemuatan (loading) awal saat mengeklik video akan terpangkas.

⚠️ Informasi Penting yang Wajib Diketahui
1. Beban Baterai & Memori: Proses kompilasi ini membutuhkan daya CPU yang besar secara instan. Pastikan baterai HP Anda berada di atas 20% saat mengeksekusi perintah ini agar prosesnya tidak terputus di tengah jalan.




1. Kembalikan YouTube ke Mode Hemat (Kompilasi Standar)Jika Anda merasa performa YouTube sudah lancar dan ingin mengembalikan profil kompilasinya ke mode hemat baterai bawaan Android, jalankan perintah ini:
```
adb shell cmd package compile -m speed-profile -f com.google.android.youtube
```
Perintah ini mengembalikan YouTube agar hanya mengompilasi bagian kode yang paling sering digunakan saja, sehingga meringankan beban CPU dan menghemat baterai [1].


2. Batasi Aktivitas Latar Belakang YouTube
Agar YouTube tidak berjalan diam-diam di latar belakang saat sedang tidak digunakan (yang sering menyedot baterai), paksa aplikasinya masuk ke mode App Standby Restricted:
```
adb shell am set-standby-bucket com.google.android.youtube restricted
```


3. Aktifkan Mode Hemat Baterai Sistem (Battery Saver) via ADB
Anda bisa menyalakan fitur penghemat baterai global Android langsung dari HP pengendali menggunakan perintah berikut:
```
adb shell settings put global low_power 1
```
(Ubah angka 1 menjadi 0 jika ingin mematikannya kembali).


4. Trik Ekstrem Menghemat Baterai Global (Doze Mode)
Android memiliki fitur bernama Doze Mode (mode tidur nyenyak). Anda bisa memaksa HP target langsung masuk ke mode hemat energi total ini saat layarnya mati dengan perintah:
```
adb shell dumpsys deviceidle force-idle
```


5. Batasi Proses Latar Belakang Maksimal (Limit Background Process)
Batasi jumlah aplikasi yang boleh berjalan bersamaan di latar belakang. Ini sangat ampuh mencegah aplikasi media sosial (Instagram, TikTok, dll.) memakan RAM dan baterai secara diam-diam.

Rekomendasi (Maksimal 2 aplikasi di latar belakang):
```
adb shell service call activity 51 i32 2
```
(Jika ingin mengembalikan ke setelan standar bawaan pabrik, jalankan: adb shell service call activity 51 i32 0)


6. Lakukan Pembersihan Cache Sistem Massal
Setelah banyak melakukan modifikasi, bersihkan sisa-sisa file sampah sistem agar sistem operasi berjalan lebih ringan dan tidak membebani baterai:
```
adb shell pm trim-caches 999G
```



device_config get game_overlay com.google.android.youtube

```
device_config put game_overlay com.google.android.youtube mode=3,downscaleFactor=0.3
```
Arti perintah ini: Memaksa Mobile Legends berjalan di Mode 2 (Performance) dan memberikan sedikit penyesuaian render (downscale aman ke 0.9) agar beban kerja GPU Xiaomi Anda menjadi enteng sehingga FPS terkunci stabil di angka tertinggi.

device_config put game_overlay com.mobile.legends mode=2,downscaleFactor=0.9

device_config get game_overlay com.mobile.legends




```
adb shell cmd game mode performance com.google.android.youtube
Game mode: performance not supported by com.google.android.youtube
```
`Error not supported` muncul karena sistem Android secara bawaan hanya mengaktifkan fitur **Game Mode** untuk aplikasi yang terdaftar sebagai **"Game"**. Aplikasi media sosial dan pemutar video seperti YouTube (`com.google.android.youtube`) tidak dikategorikan sebagai game oleh sistem Android Anda.

Namun, Anda masih bisa memanipulasi performa, FPS, dan resolusi YouTube menggunakan perintah `custom` (bukan `performance`) dengan cara berikut:

Solusi 1: Gunakan Mode custom (Untuk Mengubah FPS & Resolusi)
1. Atur Konfigurasi Custom Terlebih DahuluGunakan perintah `set` untuk menentukan resolusi (downscale) dan FPS yang diinginkan.

- Contoh mengunci YouTube di 60 FPS dengan resolusi normal:
```
adb shell cmd game set --downscale disable --fps 60 com.google.android.youtube
```

- Contoh memaksa YouTube berjalan di 90 FPS:
```
adb shell cmd game set --downscale disable --fps 90 com.google.android.youtube
```


2. Aktifkan Mode Custom Tersebut
Setelah konfigurasinya diatur, aktifkan dengan perintah ini:
```
adb shell cmd game mode custom com.google.android.youtube
```
















<br>
