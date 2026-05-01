# Folder Imej (Screenshot)

Folder ini menyimpan semua imej/tangkap layar (screenshot) yang dirujuk oleh `README.md` utama.

## Konvensyen Penamaan

Gunakan nama fail dalam huruf kecil, perkataan dipisahkan dengan tanda sengkang (`-`), dan diakhiri dengan format `.png` atau `.jpg`.

| Nama Fail | Bahagian | Penerangan |
|-----------|----------|------------|
| `01-bios-virtualization.png` | 1. Prasyarat | Skrin BIOS menunjukkan tetapan **Virtualization Technology / VT-x / AMD-V** diaktifkan. |
| `02-powershell-admin.png` | 2.1 | Menu Start dengan pilihan **Run as administrator** untuk Windows PowerShell. |
| `02-wsl-install.png` | 2.2 | Output PowerShell selepas menjalankan `wsl --install -d Ubuntu-24.04`. |
| `02-wsl-list-verbose.png` | 2.5 | Output `wsl --list --verbose` menunjukkan Ubuntu-24.04 versi 2. |
| `03-talker-listener.png` | 3.9 | Dua terminal Ubuntu — satu menjalankan `talker`, satu lagi `listener`. |
| `04-usbipd-list.png` | 4.3 | Output `usbipd list` di PowerShell. |
| `04-usbipd-attach.png` | 4.5 | Output selepas `usbipd attach --wsl --busid <BUSID>`. |
| `05-gazebo-turtlebot3.png` | 5.4 | Tetingkap Gazebo memaparkan TurtleBot3 dalam dunia simulasi. |
| `05-rviz-slam.png` | 5.4 | RViz memaparkan peta SLAM yang sedang dibina. |
| `06-firewall-rule.png` | 6.8 | Tetingkap **Windows Defender Firewall** semasa menambah peraturan UDP 7400-7999. |

## Cara Menyumbang Imej

1. Ambil tangkap layar pada langkah berkenaan.
2. Potong (crop) bahagian yang relevan sahaja — elakkan maklumat peribadi (nama pengguna, IP rangkaian, dsb.).
3. Simpan dalam folder ini menggunakan nama fail di atas.
4. Pastikan saiz fail munasabah (< 500 KB setiap satu jika boleh) — guna PNG untuk teks/UI, JPG untuk foto.
5. Hantar Pull Request.

> **Nota:** Sekiranya imej belum tersedia, pautan dalam `README.md` akan kelihatan rosak (broken). Ini adalah normal — sila sumbangkan imej untuk melengkapkan panduan!
