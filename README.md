# UTS RPL

## Biodata
-  #### Nama: Cahya Al Hafiizh
-  #### NIM: 23010220008
-  #### Semester: V (Lima)
-  #### Prodi: Sistem Informasi
-  #### Mata Kuliah: Rekayasa Perangkat Lunak

## 1. Latar Belakang
Perkembangan teknologi digital dan meningkatnya kebutuhan layanan kesehatan jarak jauh mendorong terciptanya sebuah sistem Healthcare terintegrasi untuk mempermudah pasien dalam melakukan pencarian dokter, penjadwalan janji temu, pemrosesan pembayaran online, serta konsultasi video tanpa harus hadir secara fisik. Sistem ini dirancang untuk mengatasi permasalahan umum seperti proses administrasi yang rumit, waktu tunggu panjang, dan keterbatasan akses terhadap layanan medis. Dengan mengimplementasikan prinsip SOLID dan pola desain yang tepat, sistem ini diharapkan menjadi solusi yang mudah dirawat, fleksibel untuk dikembangkan, serta mampu mendukung peningkatan kualitas layanan kesehatan di masa depan.

## 2. Use Case Actor & Diagram
### Main Actor
- User
- Doctor
- Admin
- Third Party Integration : Xendit, VideoSdk

### Use Case Diagram
<img src="use case healthcare.png" alt="Use Case Diagram" width="600"/>



## 3. ERD & Class Diagram
### Entity Relational Database
<img src="ERD HEALTHCARE.png" alt="Use Case Diagram" width="600"/>

### Entity Class
- User
- Doctor
- Role
- UserRole
- Hospital
- Appointment
- Payment
- Specialization
- DoctorSpecialization
- DoctorAvailability
- HospitalDoctorFee

### Class Diagram
<img src="class diagram.png" alt="Use Case Diagram" width="600"/>

## 4. Penerapan Prinsip SOLID pada Sistem Healthcare Appointment & Video Consultation

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

## 5. Sequence & Activity Diagram
### Activity Diagram
<img src="Activity diagram.png" alt="Use Case Diagram" width="600"/>

### Sequence Diagram
<img src="sequence diagram HEALTHCARE.png" alt="Use Case Diagram" width="600"/>

## 6. State Machine & Design Pattern Evaluation
### State Machine Diagram
<img src="state machine diagram.png" alt="Use Case Diagram" width="600"/>

### Design Pattern Evaluation
Penilaian Desain Sistem
- Maintainable : Baik
  - Pemisahan service berdasarkan domain (SRP), modular, perubahan tidak memengaruhi keseluruhan sistem
- Reusable : Baik
  - Pembayaran, video service, storage, dan schedule logic dibuat berbasis interface & dapat digunakan ulang di domain lain
- Extendable : Baik
  - Penerapan SOLID (khususnya OCP & DIP) memudahkan penambahan fitur baru tanpa merombak struktur lama

Contoh: Menambah metode pembayaran baru (VA Bank, QRIS) cukup membuat kelas implementasi baru tanpa mengubah core PaymentService.

### Usulan Fitur Pengembangan di Masa Depan
#### Automated Intelligent Notification System

Deskripsi fitur:
Sistem melakukan notifikasi otomatis melalui email, WhatsApp bot, atau push notification pada beberapa kondisi:
- Pengingat pembayaran yang belum dilakukan
- Pengingat 15 menit sebelum jadwal konsultasi
- Notifikasi obat atau resep digital setelah konsultasi
- Notifikasi evaluasi layanan atau rating dokter

Manfaat:
- Mengurangi appointment hangus akibat lupa
- Meningkatkan pengalaman pengguna & performa sistem
- Memperbaiki kualitas layanan berbasis feedback
- 
Integrasi yang memungkinkan:
- Sistem	Tujuan
- Firebase Cloud Messaging	Push notification
- WhatsApp Cloud API	Reminder real‐time
- Recommendation Engine	Rekomendasi dokter berdasarkan riwayat kebutuhan pasien

#### Kesimpulan Akhir

Desain sistem healthcare yang dibuat:

- Memiliki proses perubahan state yang jelas & terkontrol

- Sangat maintainable, reusable, dan extendable

- Siap dikembangkan lebih jauh ke arah AI‐Assisted Healthcare dan Digital Clinic Full Online
