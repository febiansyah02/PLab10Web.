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
(Silakan tempel atau masukkan screenshot pengujian GET All Data kamu di sini)
