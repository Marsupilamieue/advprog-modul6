Faris Zhafir Faza 2206081931

**Commit 1 Reflection Notes**
```rust
fn handle_connection(mut stream: TcpStream) { 
    let buf_reader = BufReader::new(&mut stream);
    let http_request: Vec<_> = buf_reader 
        .lines() 
        .map(|result| result.unwrap())
        .take_while(|line| !line.is_empty()) 
        .collect();
    println!("Request: {:#?}", http_request);
}
```

Di dalam fungsi handle_connection, terdapat sebuah instance ```BufReader``` yang membungkus referensi mutable ke stream. ```BufReader``` menambahkan buffering dengan mengelola pemanggilan ke method ```std::io::Read```.

Variabel http_request dibuat untuk mengumpulkan baris permintaan yang dikirim browser ke server dan mengumpulkan baris ini dalam sebuah vektor dengan menambahkan anotasi tipe ```Vec<_>```.

```BufReader``` mengimplementasikan ```std::io::BufRead```, yang menyediakan method lines. Method lines mengembalikan iterator dari ```Result<String, std::io::Error>``` dengan membagi stream setiap kali melihat byte baris baru. Untuk mendapatkan setiap String, method ini memetakan dan membuka setiap result. Result mungkin memproduksi kesalahan jika data bukan UTF-8 yang valid atau jika ada masalah saat membaca dari stream.

Browser menandai akhir dari permintaan HTTP dengan mengirim dua karakter baris baru secara berurutan, jadi untuk mendapatkan satu permintaan dari stream dan mengambil baris sampai kita mendapatkan baris yang merupakan string kosong. Kemudian dilanjutkan dengan mengumpulkan baris-baris ke dalam vektor dan mencetaknya menggunakan format debug yang cukup sehingga dapat terlihat instruksi yang dikirim browser web ke server.

**Commit 2 Reflection Notes**

![Commit 2 screen capture](/assets/images/commit2.png)

**Commit 3 Reflection Notes**

```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
}
```

Saat ini, pembuatan respon dilakukan di dalam blok if dan else. Kode ini hampir identik, kecuali untuk status baris, isi, dan panjang. Kita bisa membuat fungsi terpisah yang menerima parameter-parameter ini dan mengembalikan respon yang diformat. Refactoring diperlukan untuk membuat kode lebih mudah dibaca dan dimaintain dengan memisahkan logika pembuatan respon ke dalam fungsi tersendiri, kita bisa menghindari duplikasi kode dan membuatnya lebih mudah untuk maintain kedepanya.

![Commit 3 screen capture](/assets/images/commit3.png)

**Commit 4 Reflection Notes**

Jika mencoba membuka dua jendela browser dan mengakses ```127.0.0.1/sleep``` di salah satunya dan ```127.0.0.1/``` di tab lainnya, browser membutuhkan waktu untuk memuat halaman. Hal ini terjadi karena server ini adalah server tunggal yang menangani semua permintaan secara berurutan, bukan secara paralel. Jadi, ketika server sedang menangani request ```/sleep``` dan sleep selama lima detik, request lainnya harus menunggu sampai server selesai menangani request tersebut.

**Commit 5 Reflection Notes**

Thread pool adalah kumpulan thread yang telah dibuat dan siap untuk menangani suatu tugas. Ketika program menerima tugas baru, ia menugaskan salah satu thread dalam pool untuk menangani tugas tersebut. Thread lainnya dalam pool tersedia untuk menangani tugas lain yang masuk saat thread pertama sedang diproses. Ketika thread pertama selesai memproses tugasnya, thread tersebut dikembalikan ke pool thread yang sedang menunggu, siap untuk menangani tugas baru. Thread pool memungkinkan untuk memproses koneksi secara bersamaan, meningkatkan throughput server Anda.

