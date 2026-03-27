# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. Dalam kasus Vec notifikasi di aplikasi receiver, kita menggunakan RwLock (Read-Write Lock) karena alasan efisiensi pada skenario akses data yang tidak seimbang:

Kenapa RwLock? Aplikasi receiver ini kemungkinan besar akan menerima banyak permintaan untuk membaca log (misal: saat user melihat daftar notifikasi di dashboard). RwLock mengizinkan banyak thread untuk membaca (read) data secara bersamaan tanpa saling menunggu, asalkan tidak ada yang sedang menulis.

Kenapa bukan Mutex? Mutex (Mutual Exclusion) jauh lebih kaku; hanya satu thread yang boleh mengakses data, baik itu untuk membaca maupun menulis. Jika kita menggunakan Mutex, maka ketika ada satu orang sedang melihat daftar notifikasi, orang lain (atau sistem yang ingin menambah notifikasi baru) harus mengantre. Hal ini akan menyebabkan bottleneck performa yang tidak perlu pada aplikasi web yang bersifat multi-threaded.

2. Perbedaan mendasar antara Rust dan Java dalam menangani variabel statis terletak pada filosofi Memory Safety dan Data Race Prevention.

Di Java: Variabel statis bisa diubah dengan mudah karena Java menyerahkan tanggung jawab thread-safety sepenuhnya kepada programmer. Jika dua thread mengubah variabel statis secara bersamaan tanpa synchronized, akan terjadi race condition yang sulit dilacak.

Di Rust: Compiler Rust sangat protektif. Mengubah variabel statis global dianggap Unsafe karena variabel tersebut bisa diakses oleh thread mana pun kapan saja. Tanpa sinkronisasi, ini adalah resep bencana bagi keamanan memori.

Kenapa butuh lazy_static? Di Rust, variabel statis harus bisa dievaluasi saat waktu kompilasi (compile-time). Namun, tipe data seperti Vec atau DashMap butuh alokasi memori di heap yang hanya bisa terjadi saat program berjalan (runtime). lazy_static mengizinkan kita menginisialisasi variabel kompleks ini secara "malas" (hanya saat pertama kali diakses) sambil tetap menjamin bahwa proses tersebut aman secara thread-safe.

#### Reflection Subscriber-2
1. Ya, saya sempat melihat-lihat isi src/lib.rs. Di sana saya belajar bagaimana Rust mengorganisir modul. lib.rs berperan sebagai "jembatan" atau crate root yang mendefinisikan modul-modul apa saja yang tersedia (seperti models, services, dll) agar bisa diakses oleh main.rs. Saya juga melihat bagaimana struktur data seperti Notification didefinisikan secara terpusat agar konsisten saat digunakan di berbagai layer, mulai dari repository sampai ke controller. Mengerti lib.rs membantu saya paham alur dependency di dalam project Rust ini.

2. Observer Pattern sangat mempermudah penambahan subscriber baru karena adanya loose coupling (keterkaitan yang longgar). Si Main app (Publisher) tidak perlu tahu detail teknis dari setiap Receiver. Dia hanya perlu menyimpan daftar URL subscriber. Jadi, mau kita jalankan 3, 10, atau 100 instance Receiver pun, kita tinggal memanggil endpoint /subscribe tanpa perlu mengubah satu baris kode pun di sisi Publisher.

Bagaimana jika ada lebih dari satu Main app?
Nah, ini akan sedikit lebih menantang. Kalau ada banyak Main app, sistem akan menjadi distributed system. Kita harus memastikan:

Receiver tahu harus mendaftar ke Main app yang mana.

Perlu ada mekanisme sinkronisasi kalau kita ingin semua Main app punya daftar subscriber yang sama.
Secara teori masih bisa ditambahkan ke sistem, tapi kompleksitasnya meningkat karena kita harus menangani masalah konsistensi data antar Publisher.

3. Saya mencoba merapikan koleksi Postman dengan menambahkan deskripsi di setiap request dan mengatur environment variables (seperti base_url). Hal ini sangat berguna, terutama untuk:

Dokumentasi Diri Sendiri: Supaya nggak lupa fungsi masing-masing endpoint dan payload JSON-nya.

Kerja Kelompok (Group Project): Teman satu tim bisa langsung pakai koleksi tersebut tanpa harus menebak-nebak cara kerja API yang saya buat.
Untuk automated tests di Rust, ini sangat krusial untuk memastikan bahwa saat kita melakukan refactoring (misal: menyederhanakan kode match menjadi .map()), fungsi utamanya tidak rusak.
