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

## Commit 3
**[Commit] Add additional reflection notes, put the title clearly such
as Commit 3 Reflection notes. Commit your work with message
“(3) Validating request and selectively responding”. Push your
commit.**

1. Membuat file `404.html` yang isinya sebagai berikut 
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <title>Hello!</title>
    </head>
    <body>
    <h1>Oops!</h1>
    <p>Sorry, I don't know what you're asking for.</p>
    </body>
    </html>
    ``` 

2. Kemudian, melakukan modifikasi fungsi `handle_connection` seperti berikut. 
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
   Fungsi `handle_connection` bertindak sebagai server HTTP sederhana. Membaca baris permintaan dari klien dalam permintaan HTTP dan memeriksa apakah itu permintaan `GET / HTTP/1.1`, menandakan permintaan untuk jalur root (/). Jika permintaan adalah untuk jalur root, fungsi memberikan tanggapan dengan status `HTTP/1.1 200 OK`. Selanjutnya, ia membaca konten dari file `hello.html`, menghitung panjangnya, membuat tanggapan HTTP yang berisi konten tersebut, dan mengirimkannya kembali ke klien. Namun, jika permintaan bukan untuk jalur root, menunjukkan bahwa sumber daya yang diminta tidak ditemukan, fungsi akan memberikan tanggapan dengan status `HTTP/1.1 404 NOT FOUND`. Kemudian, ia membaca konten dari file `404.html`, menghitung panjangnya, membuat tanggapan HTTP yang berisi konten tersebut, dan mengirimkannya kembali ke klien. Dengan demikian, fungsi `handle_connection` menanggapi dengan konten yang berbeda berdasarkan permintaan yang diterima, entah itu untuk jalur root atau tidak. <br>

   Setelah menambahkan modifikasi, block if dan else memiliki banyak repetisi karena keduanya sama-sama membaca dan menulis file ke stream. Perbedaan utamanya terletak pada status line dan nama file yang dibaca. Oleh karena itu perlu dilakukan refactoring

3. Lalu, saya melakukan *refactor* pada fungsi `handle_connection` seperti berikut.
    ```rust
    fn handle_connection(mut stream: TcpStream) {
        let buf_reader = BufReader::new(&mut stream);
        let request_line = buf_reader.lines().next().unwrap().unwrap();

        let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
            ("HTTP/1.1 200 OK", "hello.html")
        } else {
            ("HTTP/1.1 404 NOT FOUND", "404.html")
        };
        let contents = fs::read_to_string(filename).unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );
        stream.write_all(response.as_bytes()).unwrap();
    }
    ```
4. berikut merupakan hasil screenshot
![Commit 3 screen capture](/assets/images/commit3.jpg)