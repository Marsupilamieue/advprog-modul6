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

