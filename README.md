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

Secara bawaan, status Restricted (Dibatasi) pada App Standby Bucket memang baru akan aktif secara agresif saat sistem mendeteksi HP sedang tidak digunakan atau layar mati.

Namun, Anda bisa memaksa sistem Android untuk langsung menerapkan pembatasan tersebut secara instan detik ini juga, tanpa perlu menunggu layar HP target mati atau menunggu HP didiamkan.

Silakan jalankan perintah kombinasi ini di HP pengendali Anda secara berurutan:

1. Kunci Status Aplikasi ke "Restricted"
Jalankan perintah ini untuk memasukkan Google Play Services ke mode pembatasan ketat:
```
adb shell am set-standby-bucket com.google.android.gms restricted

```

2. Paksa Sistem Menerapkan Perubahan Instan (Tanpa Tunggu Layar Mati)
Agar sistem Android langsung mengunci dan menerapkan pembatasan tersebut saat ini juga (meskipun HP sedang Anda gunakan), jalankan perintah state-refresh berikut:
```
adb shell am set-inactive com.google.android.gms true

```
- Fungsi: Perintah set-inactive true ini menipu sistem Android agar menganggap aplikasi Google Play Services sedang dalam kondisi "mati/tidak aktif total", sehingga seluruh batasan hemat daya langsung dieksekusi secara instan.

3. Cara Mengecek Apakah Pembatasan Sudah Berhasil Aktif
Untuk memastikan apakah statusnya sudah benar-benar terkunci di mode hemat energi tanpa menunggu layar mati, ketik perintah pengecekan ini:
```
adb shell am get-standby-bucket com.google.android.gms

```
Cara membaca hasilnya:Jika di terminal muncul angka 45 (atau pada beberapa versi Android muncul teks RESTRICTED), artinya Google Play Services sudah sukses dijinakkan saat ini juga tanpa perlu menunggu HP target tidur.



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


Sebagai langkah penutup untuk memastikan semua konfigurasi hemat baterai massal yang kita masukkan tadi berjalan selaras, jalankan satu perintah optimasi sistem ini:
```
adb shell cmd package bg-dexopt-job

```
Perintah ini akan memaksa sistem Android melakukan optimasi terjadwal (background optimization) pada aplikasi-aplikasi yang jarang digunakan agar tidak membebani baterai.



Menghapus File Sampah Dexopt (Sisa Optimasi)
Setelah Anda menjalankan perintah `bg-dexopt-job` sebelumnya, terkadang ada sisa-sisa file kompilasi lama yang tidak terpakai. Anda bisa membersihkannya untuk menghemat ruang dengan perintah:
```
adb shell pm compile --reset -a
```


Hapus Hanya File Residu/Dexopt Aplikasi:
```
adb shell pm compile --reset NAMA_PAKET_APLIKASI
```


Cek Folder Aplikasi Luar (TikTok, ML, WA, dll.)
Gunakan perintah ini untuk mengecek folder aplikasi di penyimpanan luar yang biasanya paling banyak memakan tempat (bergiga-giga):
```
adb shell du -d 1 /sdcard/Android/data/ | sort -n -r | head -n 15 | while read size path; do echo "$((size/1024)) MB -> $path"; done

```
Cara kerja: Perintah ini mengukur ukuran dalam biner, mengurutkannya dari yang terbesar, lalu mengubah angkhasnya ke satuan MB agar mudah Anda baca.
Output Terminal:
```
$ adb shell du -d 1 /sdcard/Android/data/ | sort -n -r | head -n 15 | while read size path; do echo "$((size/1024)) MB -> $path"; done

6396 MB -> /sdcard/Android/data/
5513 MB -> /sdcard/Android/data/com.google.android.youtube
394 MB -> /sdcard/Android/data/com.unicc.uni
198 MB -> /sdcard/Android/data/com.miui.videoplayer
103 MB -> /sdcard/Android/data/com.spotify.music
84 MB -> /sdcard/Android/data/com.miui.gallery
26 MB -> /sdcard/Android/data/com.v2ray.ang
19 MB -> /sdcard/Android/data/com.deniscerri.ytdl
15 MB -> /sdcard/Android/data/cn.wps.moffice_eng
15 MB -> /sdcard/Android/data/com.android.soundrecorder
13 MB -> /sdcard/Android/data/com.miui.creation
4 MB -> /sdcard/Android/data/com.shopee.id
3 MB -> /sdcard/Android/data/com.milink.service
1 MB -> /sdcard/Android/data/com.linkit.bimatri
0 MB -> /sdcard/Android/data/com.android.deskclock
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



max_phantom_processes=2147483647

Angka `2147483647` adalah nilai maksimal dari sistem biner Android (Integer MAX_VALUE). Jika nilai ini dimasukkan ke dalam pengaturan `max_phantom_processes`, fitur pembatasan anak proses (*phantom processes*) di Android 12 ke atas akan dinonaktifkan secara total.


Cara Mengecek Apakah Nilainya Sudah Berhasil Mengunci
Setelah menjalankan rangkaian perintah terpadu di atas, lakukan verifikasi menggunakan perintah khusus ini:
```
adb shell "/system/bin/device_config get activity_manager max_phantom_processes"

```
Hasil yang benar: Terminal sekarang harus menampilkan angka 2147483647. Angka ini menandakan fitur Phantom Process Killer Android sukses dilumpuhkan secara permanen.
(Catatan Khusus Android 14 ke Atas: Jika HP target Anda sudah menggunakan Android 14 atau lebih baru, Anda tidak perlu mengetik perintah ini lagi. Google sudah menyediakan saklar instan gratis di dalam menu Pengaturan -> Opsi Pengembang -> aktifkan menu Disable child process restrictions).


Matikan Fitur Monitor Pendeteksi Proses Bayangan
```
adb shell "settings put global settings_enable_monitor_phantom_procs false"

```


Matikan Fitur Sinkronisasi Otomatis Sistem (Wajib)
```
adb shell "/system/bin/device_config set_sync_disabled_for_tests persistent"

```
Untuk mengecek apakah perintah penonaktifan sinkronisasi otomatis (`set_sync_disabled_for_tests persistent`) sudah berhasil diterapkan atau belum, sistem Android tidak menyediakan perintah get langsung yang spesifik bernama "get_sync_disabled".

Cek Status Sinkronisasi melalui Log Sistem (Paling Akurat)
```
adb shell "device_config list | grep -i sync"

```
Output:
```
$ adb shell "device_config list | grep -i sync"
adservices/measurement_async_registration_fallback_job_required_network_type=1
adservices/measurement_async_registration_job_trigger_max_delay_ms=10000
adservices/measurement_async_registration_job_trigger_min_delay_ms=0
adservices/measurement_async_registration_queue_job_required_network_type=1
adservices/spe_on_async_registration_fallback_job_enabled=true
aicore/AicModels__enable_fsync_downloaded_files=false
companion/enable_context_sync_telecom=false
device_personalization_services/AicModels__enable_fsync_downloaded_files=false
```
Jika perintah tersebut tidak memunculkan teks apa pun, gunakan perintah cadangan di bawah ini untuk memeriksa isi database `global_settings`:
```
adb shell "settings get global device_config_sync_disabled"

```
**Cara membaca hasil**: Jika muncul angka `1`, artinya sinkronisasi otomatis ke server pusat Google/merek HP sudah berhasil **dimatikan (disabled)**. Sistem tidak akan mengutak-atik atau mereset setelan kustom Anda lagi.

Untuk menghidupkan kembali fitur sinkronisasi otomatis device_config ke kondisi semula (bawaan pabrik Android), Anda harus membatalkan status pengujian persistent yang telah diatur sebelumnya.
Silakan jalankan perintah kombinasi ini di HP pengendali Anda secara berurutan:
1. Hidupkan Kembali Sinkronisasi Otomatis Global
Jalankan perintah ini untuk mencabut status penonaktifan sinkronisasi:
```
adb shell "/system/bin/device_config set_sync_disabled_for_tests none"

```
(Argumen `none` akan menghapus pemblokiran dan mengembalikan kontrol konfigurasi ke server sistem Android).

2. Bersihkan Sisa Flag dari Database Settings
Untuk memastikan tidak ada sisa pengaturan kustom yang menyangkut di database global, jalankan perintah ini:
```
adb shell "settings delete global device_config_sync_disabled"

```

3. Cara Cek Apakah Sudah Berhasil Hidup Kembali
Untuk memverifikasi bahwa sinkronisasi otomatis sudah aktif kembali, Anda bisa mengetik perintah pengecekan ini:
```
adb shell "settings get global device_config_sync_disabled"

```
**Hasil yang benar**: Terminal harus memunculkan tulisan `null` (atau angka `0` pada sebagian besar versi Android). Ini menandakan pelindung sinkronisasi telah runtuh dan sistem Android target kembali dikendalikan sepenuhnya secara otomatis oleh pabrikan.
Setelah sinkronisasi hidup kembali, nilai-nilai kustom seperti max_phantom_processes lambat laun akan otomatis diatur ulang (reset) ke angka default pabrikan (32) oleh sistem.


q-ai:
Apakah Anda ingin saya bantu memberikan cara melakukan restart cepat pada sistem UI HP target agar setelan default ini langsung diterapkan secara instan?

Untuk melakukan *restart* cepat pada System UI (antarmuka sistem) HP target tanpa harus mematikan total (*reboot*) seluruh HP, Anda bisa menghentikan paksa proses `com.android.systemui`. Sistem Android akan otomatis memuat ulang (*refresh*) antarmuka dalam hitungan detik dan langsung menerapkan setelan default yang baru.
Silakan jalankan salah satu perintah berikut dari HP pengendali Anda:

Perintah Utama: Restart System UI Instan
Jalankan perintah ini di terminal:
```
adb shell pkill -f com.android.systemui

```
**Efek di HP Target**: Layar HP target akan berkedip hitam sesaat (sekitar 1-2 detik) lalu kembali ke layar kunci (*lockscreen*). Ini adalah tanda bahwa System UI berhasil dimuat ulang dan konfigurasi sistem yang baru saja Anda ubah sudah aktif.

Perintah Alternatif (Jika Perintah Pertama Tidak Merespons)
Pada beberapa versi Android (khususnya MIUI/HyperOS), proses antarmuka sistem kadang dilindungi secara ketat. Jika perintah di atas tidak memicu kedipan layar, gunakan perintah alternatif ini untuk menghentikan layanannya secara paksa:
```
adb shell am force-stop com.android.systemui

```



Ubah Angka Batas Proses ke Nilai Maksimal (Agar Bebas Crash)
```
adb shell "/system/bin/device_config put activity_manager max_phantom_processes 2147483647"

```



```
adb shell "/system/bin/device_config get activity_manager max_phantom_processes"

```


Untuk mengembalikan pengaturan `max_phantom_processes` ke **setelan default bawaan pabrik Android**, Anda memiliki dua cara berikut. Silakan jalankan perintah ini dari HP pengendali:

Cara 1: Menghapus Konfigurasi Kustom (Sangat Direkomendasikan)
Cara terbaik untuk kembali ke default adalah dengan menghapus baris perintah kustom yang pernah Anda masukkan sebelumnya. Sistem Android akan otomatis memulihkan nilai asli pabrikan.
Jalankan perintah ini di terminal:
```
adb shell settings delete global max_phantom_processes

```

Cara 2: Memasukkan Nilai Default Secara Manual
Jika Anda ingin memasukkan angka aslinya secara manual, nilai default standar dari Google untuk Android adalah `32`.
Jalankan perintah ini di terminal:
```
adb shell settings put global max_phantom_processes 32

```


Cara Mengecek Apakah Sudah Kembali ke Default
Setelah menjalankan salah satu perintah di atas, Anda bisa memastikan hasilnya dengan mengetik:
```
adb shell settings get global max_phantom_processes

```
- Jika menggunakan **Cara 1**, hasilnya akan tertulis `null` (artinya kosong dan sistem kembali menggunakan setelan asli pabrik).
- Jika menggunakan **Cara 2**, hasilnya akan memunculkan angka `32`.

q-ai:
Apakah Anda ingin saya bantu memberikan cara mengembalikan setelan `process_limit` (batas latar belakang) yang tadi ke default juga, atau ada setelan sistem lain yang ingin Anda setel ulang (reset)?




Output:
```
$ adb shell dumpsys activity settings

ACTIVITY MANAGER SETTINGS (dumpsys activity settings) activity_manager_constants:
  max_cached_processes=1000
  background_settle_time=60000
  fgservice_min_shown_time=2000
  fgservice_min_report_time=3000
  fgservice_screen_on_before_time=1000
  fgservice_screen_on_after_time=5000
  content_provider_retain_time=20000
  gc_timeout=5000
  gc_min_interval=60000
  force_bg_check_on_restricted=true
  full_pss_min_interval=1200000
  full_pss_lowered_interval=300000
  power_check_interval=300000
  power_check_max_cpu_1=25
  power_check_max_cpu_2=25
  power_check_max_cpu_3=10
  power_check_max_cpu_4=5
  power_check_max_cpu_5=2
  service_usage_interaction_time=1800000
  service_usage_interaction_time_post_s=60000
  usage_stats_interaction_interval=7200000
  usage_stats_interaction_interval_post_s=600000
  service_restart_duration=1000
  service_reset_run_duration=60000
  service_restart_duration_factor=4
  service_min_restart_time_between=10000
  service_max_inactivity=1800000
  service_bg_start_timeout=15000
  service_bg_activity_start_timeout=10000
  service_crash_restart_duration=1800000
  service_crash_max_retry=16
  process_start_async=true
  memory_info_throttle_time=300000
  top_to_fgs_grace_duration=15000
  top_to_almost_perceptible_grace_duration=15000
  min_crash_interval=120000
  process_crash_count_reset_interval=43200000
  process_crash_count_limit=12
  imperceptible_kill_exempt_proc_states=[0, 1, 2, 4, 12]
  imperceptible_kill_exempt_packages=[]
  min_assoc_log_duration=300000
  binder_heavy_hitter_watcher_enabled=false
  binder_heavy_hitter_watcher_batchsize=2000
  binder_heavy_hitter_watcher_threshold=0.333
  binder_heavy_hitter_auto_sampler_enabled=false
  binder_heavy_hitter_auto_sampler_batchsize=400
  binder_heavy_hitter_auto_sampler_threshold=0.333
  max_phantom_processes=2147483647
  boot_time_temp_allowlist_duration=20000
  fg_to_bg_fgs_grace_duration=5000
  fgs_start_foreground_timeout=10000
  enable_app_start_info=false
  default_background_activity_starts_enabled=false
  default_background_fgs_starts_restriction_enabled=true
  default_fgs_starts_restriction_enabled=true
  default_fgs_starts_restriction_notification_enabled=false
  default_fgs_starts_restriction_check_caller_target_sdk=true
  fgs_atom_sample_rate=1.0
  fgs_start_allowed_log_sample_rate=0.25
  fgs_start_denied_log_sample_rate=1.0
  push_messaging_over_quota_behavior=1
  fgs_allow_opt_out=false
  enable_experimental_component_alias=false
  component_alias_overrides=
  defer_boot_completed_broadcast=6
  prioritize_alarm_broadcasts=true
  no_kill_cached_processes_until_boot_completed=true
  no_kill_cached_processes_post_boot_completed_duration_millis=600000
  max_empty_time_millis=43200000
  service_start_foreground_timeout_ms=30000
  service_start_foreground_anr_delay_ms=10000
  service_bind_almost_perceptible_timeout_ms=15000
  network_access_timeout_ms=200
  max_service_connections_per_process=3000
  proactive_kills_enabled=false
  low_swap_threshold_percent=0.1
  deferred_fgs_notifications_enabled=true
  deferred_fgs_notifications_api_gated=false
  deferred_fgs_notification_interval=10000
  deferred_fgs_notification_interval_for_short=10000
  deferred_fgs_notification_exclusion_time=120000
  deferred_fgs_notification_exclusion_time_for_short=120000
  system_exempt_power_restrictions_enabled=true
  short_fgs_timeout_duration=180000
  short_fgs_proc_state_extra_wait_duration=5000
  short_fgs_anr_extra_wait_duration=10000
  use_tiered_cached_adj=false
  tiered_cached_adj_decay_time=60000

  mOverrideMaxCachedProcesses=2
  mCustomizedMaxCachedProcesses=1000
  CUR_MAX_CACHED_PROCESSES=2
  CUR_MAX_EMPTY_PROCESSES=1
  CUR_TRIM_EMPTY_PROCESSES=0
  CUR_TRIM_CACHED_PROCESSES=0
  OOMADJ_UPDATE_QUICK=true
  ENABLE_WAIT_FOR_FINISH_ATTACH_APPLICATION=false
CachedAppOptimizer settings
  use_compaction=true
  debug_compaction=false
  compaction_priority=0
  compact_throttle_1=5000
  compact_throttle_2=10000
  compact_throttle_3=500
  compact_throttle_4=10000
  compact_throttle_min_oom_adj=900
  compact_throttle_max_oom_adj=999
  compact_statsd_sample_rate=0.1
  compact_full_rss_throttle_kb=12000
  compact_full_delta_rss_throttle_kb=8000
  compact_proc_state_throttle=[11]
 Per-Process Compaction Stats

 Per-Source Compaction Stats

Total Compactions Performed by profile: 0 some, 0 full
Total compactions downgraded: 0
Total compactions cancelled by reason:

 System Compaction Memory Stats
    Compactions Performed: 1
    Total Memory Freed (KB): 848759
    Avg Mem Freed per Compact (KB): 848759.0

  Tracking last compaction stats for 0 processes.
Last Compaction per process stats:
    (ProcessName,Source,DeltaAnonRssKBs,ZramConsumedKBs,AnonMemFreedKBs,CompactEfficiency,CompactCost(ms/MB),procState,oomAdj,oomAdjReason)

Last 20 Compactions Stats:
    (ProcessName,Source,DeltaAnonRssKBs,ZramConsumedKBs,AnonMemFreedKBs,CompactEfficiency,CompactCost(ms/MB),procState,oomAdj,oomAdjReason)

  use_freezer=false
  freeze_statsd_sample_rate=0.1
  freeze_debounce_timeout=10000
  freeze_exempt_inst_pkg=true
  freeze_binder_enabled=true
  freeze_binder_threshold=1000
  freeze_binder_divisor=4
  freeze_binder_offset=500
  Apps frozen: 0
CacheOomRanker settings
  use_oom_re_ranking=false
  oom_re_ranking_number_to_re_rank=8
  oom_re_ranking_lru_weight=0.35
  oom_re_ranking_uses_weight=0.5
  oom_re_ranking_rss_weight=0.15
```












<br>
