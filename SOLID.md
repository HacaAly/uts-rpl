## Penerapan Prinsip SOLID pada Sistem Healthcare Appointment & Video Consultation

Sistem Healthcare yang dikembangkan mendukung fitur utama seperti pencarian dokter, penjadwalan konsultasi, pembayaran online menggunakan Xendit, dan konsultasi video menggunakan VideoSDK. Untuk memastikan sistem tetap scalable, maintainable, dan mudah dikembangkan, prinsip SOLID menjadi pendekatan arsitektur yang penting untuk diterapkan.

## Prinsip SOLID yang Paling Relevan
### 1. Single Responsibility Principle (SRP)

Setiap class harus hanya memiliki satu alasan untuk berubah. Pada konteks sistem ini:

- AppointmentService hanya mengatur logika pembuatan dan pengelolaan appointment.

- PaymentService hanya menangani proses pembayaran dan callback.

- VideoService hanya bertanggung jawab untuk pembuatan session meeting dan akses link.

Manfaat:

- Kode lebih mudah diuji dan dirawat.

- Perubahan pada fitur pembayaran tidak memengaruhi modul penjadwalan.

### 2. Open–Closed Principle (OCP)

Class harus terbuka untuk ekstensi tetapi tertutup untuk modifikasi.
Contoh penerapan pada sistem ini:

- Mengimplementasikan interface PaymentGateway sehingga sistem dapat menambah penyedia pembayaran baru selain Xendit tanpa mengubah logika bisnis.

- NotificationService bisa memiliki jenis output berbeda (Email, WhatsApp, SMS) dengan strategi yang dapat di‐extend.

```java
interface PaymentGateway {
    initiatePayment(data: PaymentRequest): PaymentResponse;
}
```

### 3. Dependency Inversion Principle (DIP)

Modul tingkat tinggi tidak boleh bergantung langsung pada modul tingkat rendah, tetapi keduanya harus bergantung pada abstraksi.

Contoh pada sistem:

- Controller CreateAppointmentController tidak bergantung pada implementasi spesifik PaymentService:

- constructor(private paymentGateway: PaymentGateway) {}


Dampak:

- Memudahkan mocking ketika melakukan unit testing.

- Mengurangi tight-coupling antar komponen.

## Creational Design Patterns yang Digunakan

Berikut minimal tiga pola desain Creational Pattern yang relevan untuk sistem ini:

### 1. Factory Method

Digunakan untuk pembuatan instance Payment Gateway (Xendit, Midtrans, PayPal).

```java
PaymentFactory.create("xendit");
```

#### 📌 Memudahkan scale-up jika nanti ingin menambahkan metode pembayaran lain.

### 2. Builder Pattern

Digunakan saat membangun objek Appointment dengan data kompleks, misalnya data pasien, jadwal dokter, metode pembayaran, dan opsi video call.
```java
const appointment = AppointmentBuilder
    .withPatient(patient)
    .withDoctor(doctor)
    .withSchedule(schedule)
    .withPayment(payment)
    .build();
```

#### 📌 Menghindari constructor panjang dan meningkatkan readability.

### 3. Singleton Pattern

Digunakan untuk komponen global seperti:

- Database connection

- Cache Redis instance

- Configuration provider

```java
class Database {
  private static instance: Database;
  public static getInstance(): Database { ... }
}
```


#### 📌 Menghemat sumber daya dan mencegah konflik koneksi database.

#### Kesimpulan

Dengan menerapkan prinsip SOLID pada architecture service-based, dan memanfaatkan Creational Design Patterns seperti Factory, Builder, dan Singleton, sistem menjadi:

- Lebih mudah dikembangkan dan diuji

- Lebih modular dan fleksibel terhadap perubahan

- Mendukung integrasi sistem eksternal seperti Xendit & VideoSDK secara efisien
