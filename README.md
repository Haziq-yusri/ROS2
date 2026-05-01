# Panduan ROS2 dengan WSL untuk Pengguna Windows 11

> **Bahasa:** Bahasa Melayu  
> **Versi ROS2:** Jazzy Jalisco  
> **Sistem Operasi:** Windows 11 + WSL 2 (Ubuntu 24.04)

> 📷 **Nota tentang imej:** Panduan ini merujuk kepada beberapa tangkap layar (screenshot) yang disimpan dalam folder [`images/`](./images/). Jika imej tidak dipaparkan, ia bermakna fail belum disumbangkan — sila rujuk [`images/README.md`](./images/README.md) untuk konvensyen penamaan dan cara menyumbang.

---

## Senarai Kandungan

1. [Prasyarat](#1-prasyarat)
2. [Pasang WSL di Windows 11](#2-pasang-wsl-di-windows-11)
3. [Pasang ROS2 Jazzy di WSL](#3-pasang-ros2-jazzy-di-wsl)
4. [Sambungkan Peranti USB ke WSL](#4-sambungkan-peranti-usb-ke-wsl)
5. [Jalankan RViz untuk SLAM](#5-jalankan-rviz-untuk-slam)
6. [Komunikasi dengan Peranti ROS2 Lain](#6-komunikasi-dengan-peranti-ros2-lain)
7. [Penyelesaian Masalah](#7-penyelesaian-masalah)

---

## 1. Prasyarat

Sebelum memulakan, pastikan komputer anda memenuhi keperluan berikut:

| Keperluan | Spesifikasi Minimum |
|-----------|---------------------|
| Sistem Operasi | Windows 11 (versi 21H2 ke atas) |
| RAM | 8 GB (disyorkan 16 GB) |
| Storan | 20 GB ruang kosong |
| Pemproses | 64-bit, sokongan virtualisasi (Intel VT-x / AMD-V) |

### Aktifkan Virtualisasi di BIOS

1. Mulakan semula komputer dan masuk ke BIOS (tekan `Del`, `F2`, atau `F10` semasa but — bergantung kepada jenama komputer).
2. Cari tetapan **Virtualization Technology**, **VT-x**, atau **AMD-V**.
3. Pastikan ia **Enabled (Diaktifkan)**.
4. Simpan dan keluar dari BIOS.

![Tetapan Virtualization di BIOS](images/01-bios-virtualization.png)
*Rajah 1: Contoh tetapan **Virtualization Technology** diaktifkan di BIOS.*

---

## 2. Pasang WSL di Windows 11

### Langkah 2.1 — Buka PowerShell sebagai Pentadbir

1. Tekan butang **Start** (logo Windows).
2. Taip `PowerShell`.
3. Klik kanan pada **Windows PowerShell** dan pilih **Run as administrator (Jalankan sebagai pentadbir)**.

![Buka PowerShell sebagai pentadbir](images/02-powershell-admin.png)
*Rajah 2.1: Pilihan **Run as administrator** dalam menu Start.*

### Langkah 2.2 — Pasang WSL dan Ubuntu 24.04

Taip arahan berikut dalam PowerShell dan tekan `Enter`:

```powershell
wsl --install -d Ubuntu-24.04
```

> **Nota:** Arahan ini akan memasang WSL 2 dan Ubuntu 24.04 secara automatik. Proses ini memerlukan sambungan internet dan mungkin mengambil masa beberapa minit.

![Pemasangan WSL dan Ubuntu](images/02-wsl-install.png)
*Rajah 2.2: Output PowerShell semasa `wsl --install -d Ubuntu-24.04` dijalankan.*

### Langkah 2.3 — Mulakan Semula Komputer

Setelah pemasangan selesai, mulakan semula komputer anda:

```powershell
Restart-Computer
```

### Langkah 2.4 — Tetapkan Nama Pengguna dan Kata Laluan Ubuntu

1. Selepas but semula, Ubuntu akan dibuka secara automatik.
2. Masukkan **nama pengguna** pilihan anda (contoh: `pengguna`).
3. Masukkan **kata laluan** (kata laluan tidak akan kelihatan semasa ditaip — ini adalah normal).
4. Masukkan semula kata laluan untuk pengesahan.

### Langkah 2.5 — Sahkan Versi WSL

Buka semula PowerShell sebagai pentadbir dan jalankan:

```powershell
wsl --list --verbose
```

Anda sepatutnya melihat output seperti ini:

```
  NAME            STATE           VERSION
* Ubuntu-24.04    Running         2
```

![Output wsl --list --verbose](images/02-wsl-list-verbose.png)
*Rajah 2.5: Pengesahan Ubuntu-24.04 berjalan pada WSL versi 2.*

Pastikan **VERSION** adalah **2**. Jika tidak, jalankan:

```powershell
wsl --set-version Ubuntu-24.04 2
```

### Langkah 2.6 — Kemas Kini Pakej Ubuntu

Buka terminal Ubuntu dan jalankan:

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 3. Pasang ROS2 Jazzy di WSL

### Langkah 3.1 — Tetapkan Lokal (Locale)

```bash
sudo apt install locales -y
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

Sahkan tetapan lokal:

```bash
locale
```

### Langkah 3.2 — Aktifkan Repositori Universe Ubuntu

```bash
sudo apt install software-properties-common -y
sudo add-apt-repository universe
```

### Langkah 3.3 — Tambah Kunci ROS2 GPG

```bash
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
  -o /usr/share/keyrings/ros-archive-keyring.gpg
```

### Langkah 3.4 — Tambah Repositori ROS2

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
  http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" \
  | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

### Langkah 3.5 — Kemas Kini Indeks Pakej

```bash
sudo apt update
```

### Langkah 3.6 — Pasang ROS2 Jazzy (Versi Lengkap)

```bash
sudo apt install ros-jazzy-desktop -y
```

> **Nota:** Proses ini mungkin mengambil masa 10–30 minit bergantung kepada kelajuan internet anda.

### Langkah 3.7 — Pasang Alatan Pembangunan ROS2

```bash
sudo apt install ros-dev-tools -y
```

### Langkah 3.8 — Sumber (Source) Persekitaran ROS2

Agar ROS2 sentiasa dimuat apabila terminal dibuka, tambahkan baris berikut ke dalam fail `~/.bashrc`:

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Langkah 3.9 — Uji Pemasangan ROS2

Buka **dua terminal Ubuntu** yang berasingan.

**Terminal 1 — Pengeluar (Talker):**

```bash
ros2 run demo_nodes_cpp talker
```

**Terminal 2 — Penerima (Listener):**

```bash
ros2 run demo_nodes_py listener
```

Jika pemasangan berjaya, Terminal 2 akan memaparkan mesej yang diterima daripada Terminal 1, seperti:

```
[INFO] [1234567890.000]: I heard: [Hello World: 1]
[INFO] [1234567890.000]: I heard: [Hello World: 2]
```

![Talker dan Listener berkomunikasi](images/03-talker-listener.png)
*Rajah 3.9: Dua terminal — `talker` (kiri) menghantar mesej, `listener` (kanan) menerima mesej.*

Tekan `Ctrl+C` untuk hentikan kedua-dua proses.

---

## 4. Sambungkan Peranti USB ke WSL

WSL 2 tidak menyokong USB secara langsung. Kita perlu menggunakan alat **USBIPD-WIN** untuk menghantar (forward) peranti USB dari Windows ke WSL.

### Langkah 4.1 — Pasang USBIPD-WIN di Windows

1. Buka pelayar web dan pergi ke: [https://github.com/dorssel/usbipd-win/releases](https://github.com/dorssel/usbipd-win/releases)
2. Muat turun fail `.msi` terbaru (contoh: `usbipd-win_x.x.x.msi`).
3. Jalankan fail `.msi` dan ikuti langkah pemasangan.
4. Mulakan semula komputer setelah pemasangan selesai.

### Langkah 4.2 — Pasang Alat USBIP di Ubuntu (WSL)

Dalam terminal Ubuntu, jalankan:

```bash
sudo apt install linux-tools-generic hwdata -y
sudo update-alternatives --install /usr/local/bin/usbip usbip \
  "$(ls /usr/lib/linux-tools/*/usbip | tail -n1)" 20
```

### Langkah 4.3 — Senaraikan Peranti USB yang Disambungkan

Buka **PowerShell sebagai pentadbir** di Windows dan jalankan:

```powershell
usbipd list
```

Anda akan melihat senarai peranti USB seperti:

```
BUSID  VID:PID    DEVICE                                                        STATE
1-1    10c4:ea60  Silicon Labs CP210x USB to UART Bridge (COM3)                 Not shared
2-3    0483:5740  STMicroelectronics Virtual COM Port (COM4)                    Not shared
```

![Output usbipd list](images/04-usbipd-list.png)
*Rajah 4.3: Senarai peranti USB yang dikesan oleh `usbipd`.*

### Langkah 4.4 — Kongsi (Share) Peranti USB

Kongsi peranti yang ingin digunakan (gantikan `1-1` dengan BUSID peranti anda):

```powershell
usbipd bind --busid 1-1
```

### Langkah 4.5 — Sambungkan Peranti USB ke WSL

```powershell
usbipd attach --wsl --busid 1-1
```

![usbipd attach ke WSL](images/04-usbipd-attach.png)
*Rajah 4.5: Peranti USB berjaya disambungkan ke WSL.*

### Langkah 4.6 — Sahkan Sambungan di WSL

Dalam terminal Ubuntu, jalankan:

```bash
lsusb
```

Anda sepatutnya melihat peranti USB anda tersenarai. Untuk melihat port serial:

```bash
ls /dev/ttyUSB* /dev/ttyACM*
```

### Langkah 4.7 — Berikan Kebenaran Akses Port Serial

```bash
sudo chmod 666 /dev/ttyUSB0
```

> **Nota:** Gantikan `/dev/ttyUSB0` dengan nama port peranti anda.

Untuk kebenaran kekal (tidak perlu ulang setiap kali), tambah pengguna ke kumpulan `dialout`:

```bash
sudo usermod -aG dialout $USER
```

Log keluar dan log masuk semula untuk perubahan berkuat kuasa.

### Langkah 4.8 — Nyahsambung (Detach) USB apabila Selesai

Dalam PowerShell:

```powershell
usbipd detach --busid 1-1
```

---

## 5. Jalankan RViz untuk SLAM

### Langkah 5.1 — Pasang Pakej SLAM

```bash
sudo apt install ros-jazzy-slam-toolbox ros-jazzy-navigation2 \
  ros-jazzy-nav2-bringup ros-jazzy-rviz2 -y
```

### Langkah 5.2 — Tetapkan Paparan (Display) untuk RViz

Oleh kerana WSL tidak mempunyai antara muka grafik secara lalai, kita perlu menggunakan **WSLg** (Windows Subsystem for Linux GUI) yang sudah disertakan dalam Windows 11.

Pastikan WSLg berfungsi dengan menjalankan:

```bash
echo $DISPLAY
```

Output sepatutnya seperti `:0` atau `:0.0`. Jika tiada output, jalankan:

```bash
export DISPLAY=:0
```

Tambahkan ke `~/.bashrc` untuk kekal:

```bash
echo "export DISPLAY=:0" >> ~/.bashrc
source ~/.bashrc
```

### Langkah 5.3 — Pasang Pakej Robot (Contoh: LiDAR)

Untuk contoh ini, kita akan menggunakan simulasi dengan **TurtleBot3**:

```bash
sudo apt install ros-jazzy-turtlebot3* -y
echo "export TURTLEBOT3_MODEL=burger" >> ~/.bashrc
source ~/.bashrc
```

### Langkah 5.4 — Jalankan SLAM dengan TurtleBot3 (Simulasi)

Buka **tiga terminal Ubuntu** yang berasingan.

**Terminal 1 — Jalankan Simulasi Gazebo:**

```bash
source ~/.bashrc
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

**Terminal 2 — Jalankan SLAM Toolbox:**

```bash
source ~/.bashrc
ros2 launch slam_toolbox online_async_launch.py \
  slam_params_file:=/opt/ros/jazzy/share/slam_toolbox/config/mapper_params_online_async.yaml \
  use_sim_time:=true
```

**Terminal 3 — Jalankan RViz:**

```bash
source ~/.bashrc
ros2 launch nav2_bringup rviz_launch.py
```

![Gazebo TurtleBot3](images/05-gazebo-turtlebot3.png)
*Rajah 5.4a: Simulasi TurtleBot3 dalam Gazebo.*

![RViz SLAM](images/05-rviz-slam.png)
*Rajah 5.4b: RViz memaparkan peta SLAM yang sedang dibina secara langsung.*

### Langkah 5.5 — Gerakan Robot untuk Membina Peta

Buka **Terminal 4** untuk mengawal robot:

```bash
source ~/.bashrc
ros2 run turtlebot3_teleop teleop_keyboard
```

Gunakan kekunci berikut untuk menggerakkan robot:

| Kekunci | Tindakan |
|---------|----------|
| `W` | Maju |
| `S` | Undur |
| `A` | Pusing kiri |
| `D` | Pusing kanan |
| `Spacebar` | Berhenti |

Gerakkan robot mengelilingi kawasan untuk membina peta lengkap dalam RViz.

### Langkah 5.6 — Simpan Peta

Apabila peta sudah lengkap, simpan dengan arahan berikut dalam terminal baharu:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/my_map
```

Ini akan menjana dua fail: `my_map.pgm` (imej peta) dan `my_map.yaml` (metadata peta).

### Langkah 5.7 — SLAM dengan LiDAR Fizikal (melalui USB)

Jika anda menggunakan LiDAR fizikal (contoh: RPLIDAR A1/A2/A3), pastikan USB sudah disambungkan (rujuk [Bahagian 4](#4-sambungkan-peranti-usb-ke-wsl)) kemudian:

**Pasang pakej RPLIDAR:**

```bash
sudo apt install ros-jazzy-rplidar-ros -y
```

**Jalankan nod LiDAR:**

```bash
ros2 launch rplidar_ros rplidar_a1_launch.py
```

**Dalam terminal lain, jalankan SLAM:**

```bash
ros2 launch slam_toolbox online_async_launch.py
```

**Dalam terminal lain, jalankan RViz:**

```bash
rviz2 -d /opt/ros/jazzy/share/slam_toolbox/rviz/mapper_viz.rviz
```

---

## 6. Komunikasi dengan Peranti ROS2 Lain

ROS2 menggunakan DDS (Data Distribution Service) untuk komunikasi rangkaian. Peranti-peranti dalam rangkaian yang sama boleh berkomunikasi secara automatik.

### Langkah 6.1 — Pastikan Semua Peranti dalam Rangkaian yang Sama

Pastikan komputer Windows anda dan peranti ROS2 lain (contoh: Raspberry Pi, robot) disambungkan ke rangkaian WiFi atau LAN yang sama.

### Langkah 6.2 — Tetapkan ROS_DOMAIN_ID

`ROS_DOMAIN_ID` digunakan untuk mengasingkan kumpulan peranti ROS2 yang berbeza dalam rangkaian yang sama. Nilai lalai adalah `0`.

Tetapkan `ROS_DOMAIN_ID` yang sama pada **semua** peranti yang ingin berkomunikasi:

**Di WSL (komputer Windows anda):**

```bash
echo "export ROS_DOMAIN_ID=30" >> ~/.bashrc
source ~/.bashrc
```

**Di peranti ROS2 lain (contoh: Raspberry Pi):**

```bash
echo "export ROS_DOMAIN_ID=30" >> ~/.bashrc
source ~/.bashrc
```

> **Nota:** Pilih nombor dari 0 hingga 101 (termasuk kedua-dua hujung). Gunakan nombor yang sama pada semua peranti dalam kumpulan anda.

### Langkah 6.3 — Tetapkan ROS_LOCALHOST_ONLY ke 0

Pastikan ROS2 tidak terhad kepada localhost sahaja:

```bash
echo "export ROS_LOCALHOST_ONLY=0" >> ~/.bashrc
source ~/.bashrc
```

### Langkah 6.4 — Uji Komunikasi Antara Peranti

**Di WSL (Komputer Windows) — Jalankan Pengeluar:**

```bash
ros2 run demo_nodes_cpp talker
```

**Di peranti ROS2 lain (contoh: Raspberry Pi) — Jalankan Penerima:**

```bash
ros2 run demo_nodes_py listener
```

Jika komunikasi berjaya, penerima akan memaparkan mesej yang diterima daripada pengeluar.

### Langkah 6.5 — Semak Topik yang Tersedia

Untuk melihat semua topik ROS2 dalam rangkaian (termasuk dari peranti lain):

```bash
ros2 topic list
```

Untuk melihat maklumat lanjut tentang topik tertentu:

```bash
ros2 topic echo /nama_topik
```

### Langkah 6.6 — Semak Nod yang Aktif

```bash
ros2 node list
```

### Langkah 6.7 — Tetapkan IP Statik (Pilihan — Untuk Persekitaran Stabil)

Jika anda ingin memastikan peranti sentiasa menggunakan IP yang sama, anda boleh menetapkan IP statik pada penghala (router) menggunakan ciri **DHCP Reservation** berdasarkan alamat MAC peranti.

Rujuk manual penghala anda untuk langkah-langkah terperinci.

### Langkah 6.8 — Pengesahan Firewall Windows

Windows Firewall mungkin menyekat komunikasi ROS2. Untuk membenarkan komunikasi:

1. Buka **Windows Defender Firewall with Advanced Security**.
2. Klik **Inbound Rules** > **New Rule**.
3. Pilih **Port** > **UDP** > masukkan julat port `7400-7999`.
4. Pilih **Allow the connection**.
5. Beri nama peraturan, contoh: `ROS2 DDS`.
6. Klik **Finish**.

Ulang untuk **Outbound Rules**.

![Peraturan Firewall ROS2 DDS](images/06-firewall-rule.png)
*Rajah 6.8: Menambah peraturan UDP 7400-7999 dalam Windows Defender Firewall.*

> **Nota:** Julat port `7400-7999` merangkumi port standard yang digunakan oleh pelbagai implementasi DDS (termasuk FastDDS dan Cyclone DDS). Pastikan kedua-dua peraturan masuk (Inbound) dan keluar (Outbound) ditetapkan.

---

## 7. Penyelesaian Masalah

### Masalah: `ros2: command not found`

**Penyelesaian:**

```bash
source /opt/ros/jazzy/setup.bash
```

Atau pastikan baris berikut ada dalam `~/.bashrc`:

```bash
grep "source /opt/ros/jazzy/setup.bash" ~/.bashrc
```

Jika tiada output, tambahkan:

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

---

### Masalah: RViz tidak muncul / ralat grafik

**Penyelesaian:**

Pastikan anda menggunakan Windows 11 (bukan Windows 10) kerana WSLg hanya disokong di Windows 11.

```bash
# Semak versi kernel WSL
uname -r

# Kemas kini WSL ke versi terbaru (di PowerShell Windows)
# wsl --update
```

Jika masih tidak berjaya, cuba pasang dan guna X server pihak ketiga seperti **VcXsrv**:

1. Muat turun VcXsrv dari [https://sourceforge.net/projects/vcxsrv/](https://sourceforge.net/projects/vcxsrv/).
2. Jalankan **XLaunch** dengan tetapan lalai.
3. Dalam WSL, tetapkan:

```bash
export DISPLAY="$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0"
export LIBGL_ALWAYS_INDIRECT=1
```

---

### Masalah: Peranti USB tidak kelihatan di WSL

**Penyelesaian:**

1. Semak status peranti di PowerShell:

```powershell
usbipd list
```

2. Pastikan status peranti adalah **Shared** sebelum `attach`:

```powershell
usbipd bind --busid <BUSID>
usbipd attach --wsl --busid <BUSID>
```

3. Jika masih gagal, mulakan semula perkhidmatan USBIPD:

```powershell
Restart-Service usbipd
```

---

### Masalah: Peranti ROS2 lain tidak dapat berkomunikasi

**Penyelesaian:**

1. Pastikan `ROS_DOMAIN_ID` sama pada semua peranti.
2. Pastikan semua peranti dalam subnet yang sama.
3. Semak firewall di Windows dan pada peranti lain.
4. Uji sambungan rangkaian asas:

```bash
ping <IP_peranti_lain>
```

5. Cuba gunakan FastDDS sebagai middleware DDS:

```bash
sudo apt install ros-jazzy-rmw-fastrtps-cpp -y
echo "export RMW_IMPLEMENTATION=rmw_fastrtps_cpp" >> ~/.bashrc
source ~/.bashrc
```

---

### Masalah: SLAM perlahan atau peta tidak tepat

**Penyelesaian:**

1. Pastikan komputer memenuhi keperluan minimum.
2. Kurangkan resolusi peta dalam fail konfigurasi SLAM.
3. Pastikan data LiDAR/sensor diterima dengan betul:

```bash
ros2 topic hz /scan
```

Output sepatutnya menunjukkan kadar sekurang-kurangnya 10 Hz.

---

## Rujukan

- [Dokumentasi Rasmi ROS2 Jazzy](https://docs.ros.org/en/jazzy/)
- [Dokumentasi WSL Microsoft](https://learn.microsoft.com/en-us/windows/wsl/)
- [USBIPD-WIN GitHub](https://github.com/dorssel/usbipd-win)
- [SLAM Toolbox](https://github.com/SteveMacenski/slam_toolbox)
- [Nav2 Navigation Stack](https://nav2.ros.org/)

---

*Panduan ini disediakan untuk membantu pengguna baru memulakan perjalanan mereka dengan ROS2 menggunakan WSL di Windows 11. Jika anda menghadapi sebarang masalah yang tidak tersenarai di sini, sila buka isu (issue) baru dalam repositori ini.*
