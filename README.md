# Tutorial 8 Pemrograman Lanjut: High Level Networking
### Madeline Clairine Gultom - 2306207846 - ADPRO A

> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

- Unary RPC: Tipe protokol komunikasi ketika  klien mengirim satu permintaan dan server membalas dengan satu respons. Cocok digunakan ketika sedang melakukan autentikasi.
- Server streaming RPC: Tipe protokol komunikasi yang memungkinkan klien untuk mengirim satu permintaan ke server dan server membalas dengan banyak data secara bertahap (stream). Cocok digunakan ketika ingin mengirim data besar secara bertahap, seperti update cuaca terkini.
- Bi-directional streaming RPC: Klien dan server dapat mengirim banyak pesan secara bersamaan. Cocok digunakan ketika ingin mengimplementasikan interaksi yang berjalan dua arah secara real-time.

> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Pada saat mengimplementasikan gRPC, untuk autentikasi, pastikan hanya klien sah yang dapat mengakses layanan, dapat diimplementasikan menggunakan token seperti JWT. Selanjutnya, untuk otorisasi, pastikan ada batasan akses berdasarkan peran atau hak akses pengguna yang didapatkan setelah melalui proses autentikasi (RBAC). Lalu, terkait enkripsi data, dapat menggunakan TLS (Transport Layer Security) untuk mengenkripsi komunikasi agar data tidak disalahgunakan oleh pihak tidak bertanggung jawab.
> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Tantangan yang mungkin muncul adalah kita harus memastikan sinkronisasi antara klien dan server yang dapat berjalan secara bersamaan tidak bentrok. Kita juga perlu mengelola concurrency dengan baik agar tidak terjadi race condition atau deadlock. Selain itu, kita perlu menangani menggunakan logic khusus jika terjadi koneksi putus antara klien dan server. Penggunaan memori juga menjadi salah satu pertimbangan, semisal ketika satu pihak mengirim pesan terlalu cepat, dapat menyebabkan buffer atau kehabisan memori. Terakhir, kita juga perlu menangani kemungkinan error yang akan terjadi karena komunikasi berjalan dua arah secara asinkronus.
> 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Kelebihan:
- Integrasi mudah dengan `mpsc`, karena `ReceiverStream` membungkus `tokio::sync::mpsc::Receiver` yang biasa digunakan untuk komunikasi antartugas.
- Responsif dan asinkron, memungkinkan server mengirim data ke klien tanpa perlu buffer besar.
- Sederhana, bagi yang sudah terbiasa menggunakan Tokio dan Rust async, penggunaan `mpsc` dan `ReceiverStream` akan lebih mudah dipahami.

Kekurangan:
- Overhead, jika jumlah pesan sangat besar, dapat menimbulkan overhead memori dan CPU.
- Berpotensi terjadi memory leak, jika tidak diselesaikan dengan benar, dapat meninggalkan task menggantung.
- Kurang efisien untuk data yang sangat besar.

> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Dengan mengatur struktur proyek dengan jelas dan teratur sesuai tanggung jawab, seperti pemisahan logika bisnis, implementasi service gRPC, fungsi umum, dan lainnya ke dalam folder-folder yang berbeda. Menggunakan trait untuk abstraksi layanan juga akan mempermudah penggantian implementasi. Menggunakan async atau await dan tokio agar setiap bagian kode dapat berjalan secara konkuren tanpa bergantung satu sama lain.
> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Kita dapat menambah validasi untuk data yang dimasukkan sebelum memproses pembayaran, baik dari jumlah, tipe data, atau secara autentikasi untuk memastikan hanya pengguna sah yang dapat melakukan pembayaran. 
> 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

gRPC memiliki efisiensi dan kinerja tinggi karena menggunakan HTTP/2 yang membuat komunikasi antar layanan lebih cepat. Komunikasi dengan gRPC didasarkan pada Protobuf yang terdokumentasi melalui file `.proto` sehingga memudahkan pembuatan stub otomatis di berbagai bahasa. gRPC juga mendukung banyak bahasa pemrograman sehingga dapat dikembangkan secara leluasi tanpa perlu menulis ulang logika parsing atau komunikasi.
> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Kelebihan:
- Multiplexing, HTTP/2 memungkinkan banyak permintaan dikirim secara paralel melalui satu koneksi TCP sehingga performa lebih tinggi dan latency lebih rendah.
- Kompresi header, HTTP/2 mengompres header secara efisien sehingga lebih hemat bandwidth terutama pada komunikasi antar microservices.
- Mendukung streaming, seperti server streaming, client streaming, bidirectional streaming yang cocok digunakan untuk aplikasi realtime.

Kekurangan:
- Kompatibiltas terbatas, karena tidak semua environment atau browser mendukung HTTP/2 sepenuhnya.
- Debugging lebih sulit, membutuhkan bantuan tool seperti `grpcurl`.
- Lebih kompleks, karena memerlukan TLS, sertifikat, dan dependensi khusus.

> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

REST API berkomunikasi dengan klien mengirim satu permintaan, lalu server membalas dengan satu respons, bersifat stateless di mana setiap permintaan berdiri sendiri, kemampuan real-time terbatas, dan kurang efisien secara responsivitas jika data harus sering diperbarui karena membutuhkan banyak permintaan berulang.

gRPC berkomunikasi dengan klien dan server yang dapat saling mengirim banyak pesan secara terus-menerus melalui satu koneksi, tidak perlu membuat koneksi baru tiap mengirim pesan, kemampuan real-timenya sangat baik, dan memiliki responsivitas yang tinggi.
> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

gRPC menghasilkan payload yang lebih kecil dan ringan dibanding JSON, proses serialisasi/deserialisasi jauh lebih cepat, memiliki definisi data yang terstruktur secara eksplisit dalam file `.proto` sehingga mengurangi risiko kesalahan tipe data dan mempermudah validasi otomatis, dari file tersebut juga dapat langsung digenerate klien dan server stub dalam berbagai bahasa pemrograman.

Di sisi lain, JSON lebih fleksibel untuk kebutuhan seperti konfigurasi atau log dan data dinamis, serta tidak perlu mempelajari struktur `.proto`.