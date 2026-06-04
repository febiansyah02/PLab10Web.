```markdown
# Laporan Praktikum 10: Pemrograman Web 2 - RESTful API

Laporan ini dibuat untuk memenuhi tugas Praktikum 10 pada program studi Teknik Informatika, Universitas Pelita Bangsa. Praktikum kali ini berfokus pada implementasi Representational State Transfer (REST) API menggunakan framework **CodeIgniter 4** dan pengujian menggunakan **Postman**.

---

## 1. Konfigurasi Controller (`app/Controllers/Post.php`)
Membuat RESTful controller baru memanfaatkan `ResourceController` dan `ResponseTrait` bawaan CodeIgniter 4. Controller ini menyediakan 5 method HTTP utama untuk manipulasi data artikel secara langsung tanpa interaksi visual (view).

```php
<?php

namespace App\Controllers;

use CodeIgniter\RESTful\ResourceController;
use CodeIgniter\API\ResponseTrait;
use App\Models\ArtikelModel;

class Post extends ResourceController
{
    use ResponseTrait;

    // GET /post - Menampilkan seluruh data artikel
    public function index()
    {
        $model = new ArtikelModel();
        $data['artikel'] = $model->orderBy('id', 'DESC')->findAll();
        return $this->respond($data);
    }

    // POST /post - Menambahkan data artikel baru
    public function create()
    {
        $model = new ArtikelModel();
        $data = [
            'judul' => $this->request->getVar('judul'),
            'isi'   => $this->request->getVar('isi'),
        ];
        
        $model->insert($data);
        
        $response = [
            'status'   => 201,
            'error'    => null,
            'messages' => [
                'success' => 'Data artikel berhasil ditambahkan.'
            ]
        ];
        return $this->respondCreated($response);
    }

    // GET /post/(:num) - Menampilkan satu data artikel berdasarkan ID
    public function show($id = null)
    {
        $model = new ArtikelModel();
        $data = $model->where('id', $id)->first();
        
        if ($data) {
            return $this->respond($data);
        } else {
            return $this->failNotFound('Data tidak ditemukan.');
        }
    }

    // PUT /post/(:num) - Mengubah data artikel berdasarkan ID
    public function update($id = null)
    {
        $model = new ArtikelModel();
        $data = [
            'judul' => $this->request->getVar('judul'),
            'isi'   => $this->request->getVar('isi'),
        ];
        
        $model->update($id, $data);
        
        $response = [
            'status'   => 200,
            'error'    => null,
            'messages' => [
                'success' => 'Data artikel berhasil diubah.'
            ]
        ];
        return $this->respond($response);
    }

    // DELETE /post/(:num) - Menghapus data artikel berdasarkan ID
    public function delete($id = null)
    {
        $model = new ArtikelModel();
        $cekData = $model->where('id', $id)->first();
        
        if ($cekData) {
            $model->delete($id);
            $response = [
                'status'   => 200,
                'error'    => null,
                'messages' => [
                    'success' => 'Data artikel berhasil dihapus.'
                ]
            ];
            return $this->respondDeleted($response);
        } else {
            return $this->failNotFound('Data tidak ditemukan.');
        }
    }
}
```

2. Konfigurasi Routing (app/Config/Routes.php)
   
Menambahkan fungsi pemetaan resource otomatis ke bagian paling bawah file Routes.php agar CodeIgniter mengarahkan request HTTP secara modular ke controller Post:

   // --- Route Praktikum 10 (REST API) ---
   
$routes->resource('post');

Memverifikasi pendaftaran rute berhasil dijalankan melalui terminal dengan mengetikkan:

php spark routes

3. Hasil Pengujian REST API via Postman

   Berikut merupakan dokumentasi rangkaian pengujian endpoint API lokal yang telah berhasil dieksekusi dengan status respons yang valid:

A. Pengujian GET (Menampilkan Semua Data)
- Endpoint URL: http://localhost:8080/post

- HTTP Method: GET

- Status HTTP: 200 OK

- Keterangan: Mengembalikan struktur data objek bersarang (JSON) berisi seluruh list isi baris tabel artikel di database.

- Bukti Dokumentasi:
<img width="1360" height="763" alt="Cuplikan layar 2026-06-04 160751" src="https://github.com/user-attachments/assets/de51fd06-2a4d-464a-bc83-35dcd5c45367" />

B. Pengujian POST (Menambahkan Data Baru)
- Endpoint URL: http://localhost:8080/post

- HTTP Method: POST

- Format Body: x-www-form-urlencoded

- Status HTTP: 201 Created

- Keterangan: Mengirim data judul dan isi baru ke server, menghasilkan penambahan record data dinamis pada baris database.

- Bukti Dokumentasi:
<img width="1364" height="763" alt="Cuplikan layar 2026-06-04 161417" src="https://github.com/user-attachments/assets/c0d416ae-14c8-453d-a9d1-7b378e6fa633" />

C. Pengujian DELETE (Menghapus Data Spesifik)
- Endpoint URL: http://localhost:8080/post/{id} (Contoh: http://localhost:8080/post/5)

- HTTP Method: DELETE

- Status HTTP: 200 OK

- Keterangan: Mengirimkan parameter ID data spesifik untuk dibersihkan secara permanen dari penyimpanan basis data.

- Bukti Dokumentasi:
<img width="1363" height="763" alt="Cuplikan layar 2026-06-04 161924" src="https://github.com/user-attachments/assets/026ed4f8-2cdc-45bd-a7ae-794001b5b2f5" />

Kesimpulan
Melalui pengerjaan Praktikum 10 ini, arsitektur RESTful web service berhasil diterapkan pada framework CodeIgniter 4. Pemisahan logika data melalui pembatasan hak akses client menggunakan metode verbs standar (GET, POST, PUT, DELETE) terbukti mempermudah komunikasi antar-platform yang aman, teratur, dan efisien.
