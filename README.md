Berikut adalah README dalam bahasa Indonesia:

---

# RTOS-Task-7

Repository ini berisi kode untuk proyek RTOS (Real-Time Operating System) menggunakan STM32 dengan beberapa task yang mengelola berbagai LED. Proyek ini diimplementasikan menggunakan FreeRTOS pada STM32 HAL, mengontrol tiga task LED: hijau, merah, dan kuning, dengan akses resource bersama yang diatur melalui critical section.

## Ikhtisar Proyek

Pada proyek ini:
- Tiga task terpisah dibuat untuk mengontrol LED yang berbeda.
- Task menggunakan pin GPIO untuk mengontrol LED pada mikrokontroler STM32.
- Bagian data bersama dilindungi dengan critical section untuk mencegah konflik resource.
- Fungsi debug tambahan menyalakan LED biru ketika terjadi konflik akses.

## Fitur

- **Task LED Hijau**: Menyalakan LED hijau setiap 200ms.
- **Task LED Merah**: Menyalakan LED merah setiap 550ms.
- **Task LED Kuning**: Mengubah status LED kuning setiap 50ms.
- **Deteksi Konflik**: LED Hijau(kanan) akan menyala jika terjadi konflik ketika beberapa task mengakses data bersama.

## Persyaratan Sistem

- Mikrokontroler STM32
- FreeRTOS
- STM32CubeIDE (atau IDE kompatibel dengan library STM32 HAL)

## Setup

1. Clone repository ini:
   ```bash
   git clone https://github.com/FebrianDwiF/RTOS-Task-7.git
   ```
2. Buka proyek pada STM32CubeIDE (atau IDE yang kompatibel).
3. Bangun (build) proyek untuk menghasilkan firmware.
4. Flash firmware ke mikrokontroler STM32 Anda.

## Struktur File

- **main.c**: Berisi kode aplikasi utama, definisi task, dan pengaturan resource bersama.
- **cmsis_os.h**: Integrasi FreeRTOS dan CMSIS-RTOS.
- **SystemClock_Config()**: Mengatur clock sistem untuk STM32.
- **MX_GPIO_Init()**: Menginisialisasi pin GPIO untuk mengontrol LED.

## Fungsi Task

- **Task LED Hijau (`greend_led`)**:
  - Menyalakan LED hijau setiap 200ms.
  - Menggunakan critical section untuk mengakses data bersama.

- **Task LED Merah (`red_led`)**:
  - Menyalakan LED merah setiap 550ms.
  - Menggunakan critical section untuk mengakses data bersama.

- **Task LED Kuning (`yellow_led`)**:
  - Mengubah status LED kuning setiap 50ms.

- **Akses Data Bersama (`accessSharedData`)**:
  - Menjamin konsistensi data dan akses bebas konflik untuk resource bersama.
  - Menyalakan LED Hijau jika terjadi konflik.

## Contoh Kode

### Membuat Task

Setiap task LED didefinisikan dan dibuat menggunakan fungsi `osThreadDef` dan `osThreadCreate`:
```c
osThreadDef(GreenLEDTask, greend_led, osPriorityNormal, 0, 128);
GreenLEDTaskHandle = osThreadCreate(osThread(GreenLEDTask), NULL);
```

### Mengakses Data Bersama

Fungsi `accessSharedData()` menunjukkan penggunaan critical section:
```c
taskENTER_CRITICAL();
accessSharedData();
taskEXIT_CRITICAL();
```


https://github.com/user-attachments/assets/72882697-3748-41a9-baaa-056e52c1ec26


