
# **STM32 LED Control menggunakan FreeRTOS**

Proyek ini adalah implementasi pengendalian LED dengan **STM32** menggunakan **FreeRTOS**, yang mencakup beberapa tugas untuk mengatur LED hijau, merah, dan kuning secara bersamaan. Proyek ini juga mendemonstrasikan penggunaan **critical section** untuk manajemen sumber daya bersama dan menangani konflik akses menggunakan LED indikator.

---

## **Fitur Utama**
- **Pengendalian Multi-LED**:
  - LED hijau menyala/mati setiap 200ms.
  - LED merah menyala/mati setiap 550ms.
  - LED kuning berkedip pada frekuensi 10Hz.
- **Manajemen Sumber Daya Bersama**:
  - Tugas mengakses sumber daya bersama menggunakan critical section dari FreeRTOS.
  - Konflik akses ditunjukkan dengan menyalakan LED biru.
- **Simulasi Operasi Proses**:
  - Fungsi simulasi membaca/menulis digunakan untuk menunjukkan beban kerja tugas.

---

## **Struktur Proyek**
- **`main.c`**: Kode utama yang mencakup konfigurasi GPIO, tugas FreeRTOS, dan logika pengendalian LED.
- **`FreeRTOS`**: Kernel RTOS untuk manajemen tugas dan penjadwalan.
- **`HAL`**: Library STM32 Hardware Abstraction Layer untuk kontrol periferal.

---

## **Persiapan**

### **Perangkat Keras yang Dibutuhkan**
1. **Board STM32** (misalnya STM32F103 atau STM32F4).
2. **LED dan Resistor** (4 LED: Hijau, Merah, Kuning, Biru).
3. **Kabel jumper dan breadboard**.

### **Konfigurasi Pin GPIO**
| LED   | GPIO Pin | Warna LED | Keterangan        |
|-------|----------|-----------|-------------------|
| LED1  | GPIOA_0  | Hijau     | Berkedip setiap 200ms. |
| LED2  | GPIOA_1  | Merah     | Berkedip setiap 550ms. |
| LED3  | GPIOA_2  | Kuning    | Berkedip dengan kecepatan tinggi (10Hz). |
| LED4  | GPIOA_3  | Biru      | Menyala saat terjadi konflik akses. |

---

## **Langkah-Langkah**
1. **Kompilasi dan Flash**:
   - Gunakan **STM32CubeIDE** untuk membuka proyek.
   - Kompilasi proyek.
   - Upload (flash) ke board STM32.

2. **Periksa Perilaku LED**:
   - LED hijau dan merah berkedip sesuai jadwal masing-masing.
   - LED kuning berkedip dengan cepat (10Hz).
   - LED biru menyala jika ada konflik akses sumber daya.

---

## **Penjelasan Kode**

### **Jalan Kerja Kode**
1. **Konfigurasi GPIO**:
   - Semua pin GPIO untuk LED diinisialisasi dalam mode **output push-pull**.
   - LED dimatikan secara default setelah inisialisasi.

2. **Tugas FreeRTOS**:
   - Tugas dibuat untuk setiap LED:
     - **GreenLEDTask**: Mengontrol LED hijau dengan interval 200ms.
     - **RedLEDTask**: Mengontrol LED merah dengan interval 550ms.
     - **YellowLEDTask**: Mengontrol LED kuning dengan kecepatan tinggi (10Hz).
   - Scheduler FreeRTOS menjalankan semua tugas secara bersamaan berdasarkan prioritas.

3. **Critical Section**:
   - **Critical section** melindungi sumber daya bersama (variabel `startFlag`) untuk menghindari konflik akses.
   - Saat dua tugas mencoba mengakses sumber daya secara bersamaan:
     - **LED biru menyala** sebagai indikator konflik.
     - Setelah konflik diatasi, LED biru dimatikan.

4. **Fungsi Pendukung**:
   - **SimulateReadWriteOperation**: Mensimulasikan operasi akses sumber daya dengan delay loop.
   - **accessSharedData**: Mengelola sumber daya bersama dan mengatur logika konflik.

5. **Prioritas Tugas**:
   - Tugas kuning memiliki prioritas lebih tinggi (`osPriorityAboveNormal`) dibandingkan tugas hijau dan merah (`osPriorityNormal`), sehingga akan lebih sering dijalankan.

---

## **Cuplikan Kode Penting**

### **Critical Section**
```c
void accessSharedData(void) {
    if (startFlag == 1) {
        startFlag = 0;  // Tandai sumber daya sedang digunakan
    } else {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3, GPIO_PIN_SET);  // Nyalakan LED biru
    }

    SimulateReadWriteOperation();  // Simulasi operasi pada sumber daya bersama

    startFlag = 1;  // Bebaskan sumber daya
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3, GPIO_PIN_RESET);  // Matikan LED biru
}
```

### **Tugas LED Hijau**
```c
void greend_led(void const * argument) {
    for(;;) {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_SET);  // Nyalakan LED hijau
        taskENTER_CRITICAL();
        accessSharedData();  // Akses sumber daya bersama
        taskEXIT_CRITICAL();
        osDelay(200);
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_0, GPIO_PIN_RESET);  // Matikan LED hijau
        osDelay(200);
    }
}
```

### **Simulasi Operasi**
```c
void SimulateReadWriteOperation(void) {
    volatile uint32_t delay_count = 0;
    const uint32_t delay_target = 2000000;

    for (delay_count = 0; delay_count < delay_target; delay_count++) {
        __asm("nop");  // Menahan prosesor tanpa mengubah keadaan sistem
    }
}
```

---

https://github.com/user-attachments/assets/db086f76-40c8-4113-9465-1e1432762bb0



---

