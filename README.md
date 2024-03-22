## Refleksi Advprog Modul 6

*1. what is inside the handle_connection method?*

Di dalam metode `handle_connection`, aliran TCP masuk dibungkus menggunakan `BufReader` untuk memudahkan pembacaan pesan respons dengan cepat dan mudah.

Selain itu, metode tersebut membaca setiap baris dari data masuk, memisahkannya, mengonversi mereka ke format `String` menggunakan `.map`, melakukan iterasi sampai menemukan baris kosong menggunakan `.take_while`, dan akhirnya mengumpulkan hasilnya ke dalam sebuah vektor menggunakan `collect()`.

*2. What have I learned about the new code in the handle connection method?*
Dalam metode `handle_connection` pada kode baru, kita dapat mempelajari bahwa header `Content-Length` disetel sesuai dengan panjang dari isi file respons, yaitu `hello.html`.

```rust
let status_line = "HTTP/1.1 200 OK";
let contents = fs::read_to_string("hello.html").unwrap();
let length = contents.len();

let response = format!("{status_line}\r\nContent-Length:{length}\r\n\r\n{contents}");
stream.write_all(response.as_bytes()).unwrap();
```

Di sini, kode tambahan tersebut memungkinkan pembacaan isi file dan konversi menjadi string dengan `fs::read_to_string()`, dimana panjangnya diperoleh dengan fungsi `len()`. Jika pembacaan file gagal, `unwrap()` akan menghentikan eksekusi program. Dengan menggunakan `Content-Length`, ketika program dijalankan, isi dari file `hello.html` akan diambil, dirender, dan dikirimkan sebagai respons.
Berikut adalah hasil run saya:
![image](https://github.com/Samuelwidjaja/advprog-modul6/assets/119392779/50c272a9-afee-456f-ae26-408f228d0f3c)

*3. How to split between response?*

Dalam program ini, perbedaan respons tergantung pada endpoint yang diminta. Jika endpoint adalah `/`, maka akan menghasilkan respons bahwa program berhasil dijalankan. Namun, jika endpoint adalah yang lain, misalnya `/something-else`, maka respons yang dihasilkan adalah bahwa halaman tidak ditemukan.

Untuk membedakan respons, kita perlu membaca path yang diminta dari permintaan masukan dengan `let request_line = buf_reader.lines().next().unwrap().unwrap();`. Jika path tersebut valid, maka `request_line == "GET / HTTP/1.1"`. Seperti pada tahap sebelumnya, respons yang valid akan menghasilkan file `hello.html`.

Namun, jika respons tersebut tidak valid, maka kita ingin menampilkan pesan "page not found". Untuk melakukan ini, kita perlu membuat kondisi untuk menangani kasus ketika path tidak valid. Perbedaan antara respons yang valid dan tidak valid terletak pada `status_line` dan nama file yang diambil oleh `fs::read_to_string(file)`.

```rust
if request_line == "GET / HTTP/1.1" {
    // Respons valid
    let status_line = "HTTP/1.1 200 OK";
    let contents = fs::read_to_string("hello.html").unwrap();
    let length = contents.len();

    let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
} else {
    // Respons tidak valid
    let status_line = "HTTP/1.1 404 NOT FOUND";
    let contents = fs::read_to_string("404.html").unwrap();
    let length = contents.len();

    let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
}
```
![image](https://github.com/Samuelwidjaja/advprog-modul6/assets/119392779/20e4af88-3a62-4701-be08-fcf5811f2355)

Refaktorisasi diperlukan untuk mengurangi duplikasi kode. Dengan menggunakan pendekatan ini, blok `if` dan `else` hanya membedakan `status_line` dan nama file, sementara variabel lain dikeluarkan dari blok kondisional, menjadikan kode lebih ringkas dan terorganisir.