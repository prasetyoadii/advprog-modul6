# Tutorial Module 6 

Prasetyo Adi Wijonarko <br>
2206830246 <br>
DEE <br>

## Commit 1
**[Commit] Add reflection notes in Readme.md, put the title clearly such as Commit 1 Reflection notes. commit your works, put
commit message “(1) Handle-connection, check response”, and
then push it to your repository.**  <br>

`handle_connection` merupakan fungsi yang menangani koneksi TCP dengan client. Fungsi ini menerima TcpStream yang mewakili koneksi TCP yang sudah terbuka. Tujuan utama fungsi ini adalah untuk membaca request HTTP dari client yang terhubung.

`let buf_reader = BufReader::new(&mut stream);` Baris ini membuat BufReader baru yang menggunakan TcpStream sebagai input. BufReader digunakan untuk membaca input dengan menggunakan buffer.

`let http_request: Vec<_> = buf_reader .lines() .map(|result| result.unwrap()) .take_while(|line| !line.is_empty()) .collect();` Di sini, kita membaca baris-baris dari TcpStream menggunakan iterator yang diberikan oleh `lines()` dari BufReader. Setiap baris kemudian dipetakan dengan mengeluarkan hasil dari Result yang dihasilkan oleh `lines()`, menggunakan `unwrap()` untuk memperoleh String yang sebenarnya. Ini dilakukan karena `lines()` mengembalikan Result, dan kita ingin mengabaikan error handling untuk kesederhanaan. Kemudian, kita menggunakan `take_while()` untuk mengumpulkan baris-baris tersebut selama baris tersebut tidak kosong (di mana sebuah baris kosong menandakan akhir dari request HTTP). Hasilnya dikumpulkan ke dalam `Vec<_>` yang disebut http_request.

`println!("Request: {:#?}", http_request);` Baris ini mencetak http_request ke konsol dengan menggunakan format debug `{:#?}`.

## Commit 2
**[Commit] Complete your reflection on the new handle_connection
in the readme.md, put the title clearly such as Commit 2
Reflection notes. commit with message “(2) Returning HTML”,
push it to your git repository server.** <br>

![Commit 2 screen capture](/assets/images/commit2.jpg)

Pada commit ini, fungsi `handle_connection` diperluas untuk tidak hanya membaca request dari TCP stream, tetapi juga meresponsnya dengan mengirimkan respons HTTP ke klien yang meminta. Proses tersebut melibatkan konfigurasi *status line* agar menunjukkan `200 OK`, pembacaan isi dari file `hello.html` ke dalam string menggunakan `fs::read_to_string()`, penghitungan panjang isi file, pengaturan format respons HTTP yang mencakup baris status, panjang konten, dan isi dari file `hello.html`, serta penulisan respons kembali ke dalam TCP menggunakan `write_all()`. Ini menunjukkan bagaimana fungsi `handle_connection` mengelola permintaan HTTP dengan meresponsnya dengan tepat.