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
