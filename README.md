## Refleksi Advprog Modul 6

*1. what is inside the handle_connection method?*

Di dalam metode `handle_connection`, aliran TCP masuk dibungkus menggunakan `BufReader` untuk memudahkan pembacaan pesan respons dengan cepat dan mudah.

Selain itu, metode tersebut membaca setiap baris dari data masuk, memisahkannya, mengonversi mereka ke format `String` menggunakan `.map`, melakukan iterasi sampai menemukan baris kosong menggunakan `.take_while`, dan akhirnya mengumpulkan hasilnya ke dalam sebuah vektor menggunakan `collect()`.
