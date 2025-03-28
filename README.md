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
1. Kita menggunakan RwLock<> untuk sinkronisasi notifikasi karena memiliki sifat lock hanya untuk write. Hanya boleh ada 1 user yang melakukan write, dimana selama dia write, writer lain akan di block oleh thread. Namun, untuk read RwLock membuka akses sehingga read secara parallel didukung. Hal ini bagus untuk design pattern observer dimana terdapat lebih banyak reader daripada writer sehingga efektif untuk digunakan. Mutex<> tidak kami pakai karena akses akan di lock secara utuh dan hanya ada 1 user yang dapat masuk untuk read maupun write. Hal ini tidak efektif, mengetahui bahwa kita memiliki reader atau subscriber yang banyak sehingga akan membuat queue yang panjang hanya untuk read.
2. Rust dikenal sebagai programming language yang thread safe. Hal ini mengakibatkan kita tidak bisa langsung mengedit static variable seperti Java. Kita harus memasang Mutex<> atau RwLock<> untuk menspesifikasi lock access rights. Hal inilah yang melindungi kita dari race condition, suatu vulnerability yang tidak langsung di handle oleh Java. Dengan lazy_static, kita bisa menginisialisasi vec sebagai variabel static yang thread safe dan modifiable di runtime dengan wrapper RwLock<> atau Mutex<>.

#### Reflection Subscriber-2
1. Dengan membaca sekilas, saya mendapatkan bahwa lib.rs berfungsi sebagai configuration. Kode ini mengandung inisialisasi dengan lazy_static, config aplikasi yang di setting melalui .env, set up error message, serta HTTP handling dengan rocket. Di sisi lain, terdapat file seperti main.rs yang menyatukan semua source untuk dipublish.
2. Jika kita memiliki 2 instance dari suatu publisher app yang sama, kemungkinannya adalah keduanya memiliki subscriber yang terpisah jika databasenya tidak combined. Hal ini menyebabkan notifikasi dari salah satu server tidak mencapai subscriber dari server lain. Hal ini akan mempersulit jalannya design pattern observer ini, karena menjauh dari konsep awal dimana satu publisher memberi notifikasi ke banyak subscriber. Hal ini akan menciptakan environment yang terpisah bagaikan dua publisher yang tidak berhubungan. Solusi dari hal tersebut adalah menyatukan database subscriber dan notification sehingga seluruh subscriber dapat menerima notifikasi yang sama dalam waktu yang sama.
3. Saya telah mencoba meningkatkan testcases di dalam Postman Collection. Seperti halnya mengubah port antara receiver 8001, 8002, 8003 untuk grasp concept terkait subscriber yang bisa subscribe ke beda produk. Saya juga mendapatkan bahwa notifikasi sebelumnya tidak akan masuk ke subscriber saat ia baru join. Hal ini sudah menyerupai notifikasi dalam aplikasi-aplikasi seperti tokopedia ketika mengirimkan notifikasi diskon atau promo. Menggunakan dan mencoba-coba test cases di dalam Postman menurut saya bermanfaat untuk projek ini maupun projek lain, karena kita dapat dengan mudah memahami behaviour dari aplikasi yang telah kita buat. Hal ini akan mempermudah kita mengetahui ketika terdapat bugs atau unintended behaviour agar segera di patch sebelum menuju ke tahap deployment.