# 🛒 Dokumentasi API — Seller Tools

Dokumentasi untuk seller yang mengelola nomor XL (pengelola) untuk layanan **Akrab** (Family Plan) dan **Circle** (Family Hub).

> Semua endpoint di bawah memerlukan **User API Key**, kecuali yang ditandai PUBLIC.

---

## 📋 Daftar Isi

- [Manage Akrab](#-manage-akrab--kembali-ke-atas)
- [Manage Circle](#-manage-circle--kembali-ke-atas)

<a name="top"></a>

---

## 👨‍👩‍👧 Manage Akrab [⬆ Kembali ke Atas](#-daftar-isi)


<details>
<summary><code>POST</code> <strong>/api/v1/xl/akrab/info</strong> - Lihat Data & Anggota Keluarga</summary>

Mengambil informasi lengkap tentang paket Family Plan, sisa Kuota Bersama, dan daftar anggotanya.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "x-user": "UUID_USER_ANDA"
}
```

**Response Success (200 OK)**
```json
{
  "status": "success",
  "message": "Family Plan info retrieved",
  "data": {
    "members": [
      {
        "family_member_id": "43928xxx-xxxx-xxxx",
        "jadwal_kick": "Minggu, 12 Juli 2026",
        "kuber": "0 GB",
        "kuota_dipakai": "0 B",
        "nama_anggota": "Pengelola",
        "nomer_anggota": "62819xxxxxxxx",
        "posisi_slot": 0,
        "reset": "Senin, 13 Juli 2026",
        "role": "PARENT",
        "sisa_add": 0,
        "slot_id": 4839213,
        "tipe_slot": "SUBSCRIBER"
      }
    ]
  }
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/akrab/add</strong> - Tambah Anggota (Add Member)</summary>

Memasukkan nomor anak ke dalam slot kosong di Family Plan.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member": "0819yyyyyyyy",
  "alias": "Nama Anak",
  "posisi_slot": 1,
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/akrab/kuber</strong> - Atur Kuota Bersama (Kuber)</summary>

Mengalokasikan Kuota Bersama (Kuber) untuk slot anak tertentu. `kuber` dalam satuan GB. Set ke `0` untuk mencabut batasan (Unlimited Kuber).

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "kuber": 5,
  "posisi_slot": 1,
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/akrab/remove</strong> - Hapus Anggota (Kick Member)</summary>

Menghapus anak dari slot Akrab.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "posisi_slot": 1,
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/akrab/cek-kuota</strong> - Cek Detail Kuota Anggota</summary>

Melihat rincian sisa kuota (Nasional, Area, dll) secara terperinci untuk satu anggota.

**Headers**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
Content-Type: application/json
```

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "family_member_id": "xxxx-xxxx-xxxx-xxxx",
  "x-user": "UUID_USER_ANDA"
}
```
</details>


## 🔵 Manage Circle [⬆ Kembali ke Atas](#-daftar-isi)
<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/validate</strong> - Validasi Target Nomor Massal</summary>

Mengecek kelayakan satu atau beberapa nomor sekaligus untuk mengetahui apakah mereka bisa menerima undangan atau diinjeksi paket Circle. **Sistem kerjanya sama persis seperti `/api/v1/sidompul/cek`** yang bebas mengecek tanpa perlu login nomor target di sistem.

**Header Request (Akses External)**
```http
Authorization: Bearer <API_KEY_ANDA>
X-Client: <X_CLIENT_ANDA>
```

**Body Request**
```json
{
  "nomer_target": "0819xxx, 0878yyy",
  "x-pak": "API_KEY_PUBLIC_SERVER"
}
```

**Response Sukses (200 OK)**
```json
{
  "status": "success",
  "data": {
    "results": [
      {
        "msisdn": "62819xxx",
        "valid": true,
        "status": "valid",
        "message": "Input msisdn is eligible",
        "response_code": "200-2001",
        "pesan_tambahan": "BISA JOIN CIRCLE BOS"
      },
      {
        "msisdn": "62878yyy",
        "valid": false,
        "status": "invalid",
        "message": "Not allowed owner",
        "response_code": "400-4002",
        "pesan_tambahan": "TIDAK BISA JOIN CIRCLE"
      }
    ],
    "summary": {
      "error": 0,
      "invalid": 1,
      "valid": 1
    },
    "total_checked": 2
  }
}
```
</details>
<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/info</strong> - Informasi Circle</summary>

Mengambil informasi detail mengenai grup Circle dan sisa kuota yang tersedia.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/create</strong> - Buat Circle</summary>

Membuat grup Circle baru dan otomatis mengundang satu nomor member awal.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member": "0819xxxxxxxx",
  "alias": "Nama Member",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/add</strong> - Tambah Member (Invite)</summary>

Mengundang member baru untuk bergabung ke grup Circle yang sudah ada.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member": "0819xxxxxxxx",
  "alias": "Nama Member",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/massal/add</strong> - Tambah Massal (Otomatis Buat + Add 3 Member)</summary>

Secara otomatis membuat grup Circle, mengundang hingga 3 member secara berurutan, dan otomatis menerima undangannya tanpa persetujuan manual dari member.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member_1": "0819xxxxxxx1",
  "nomer_member_2": "0819xxxxxxx2",
  "nomer_member_3": "0819xxxxxxx3",
  "alias": "Member Keluarga",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/acc</strong> - Terima Undangan</summary>

Menerima undangan Circle yang telah dikirimkan ke nomor member.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member": "0819xxxxxxxx",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/kick</strong> - Hapus Member</summary>

Mengeluarkan member dari grup Circle. Jika ini adalah member terakhir, grup otomatis dibubarkan.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "nomer_member": "0819xxxxxxxx",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/cek-bonus</strong> - Cek Bonus Gamification</summary>

Melihat daftar misi atau bonus yang bisa diklaim pada grup Circle Anda.

**Body**
```json
{
  "nomer_pengelola": "0819xxxxxxxx",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/klaim-bonus</strong> - Klaim Bonus</summary>

Mengklaim bonus tertentu dari grup Circle. ID Misi (`bonus_name`) didapatkan dari endpoint *Cek Bonus*.

**Body**
```json
{
  "nomer_pengelola": "081912345678",
  "bonus_name": "Bonus Aktivasi Anggota Baru",
  "x-user": "UUID_USER_ANDA"
}
```
</details>

<details>
<summary><code>POST</code> <strong>/api/v1/xl/circle/kirim-bonus</strong> - Kirim Bonus ke Member</summary>

Mengirimkan bonus/misi gamification ke nomor anggota spesifik yang ada di dalam Circle Anda.

**Body**
```json
{
  "nomer_pengelola": "081912345678",
  "nomer_member": "081987654321",
  "bonus_name": "Bonus Aktivasi Anggota Baru",
  "x-user": "UUID_USER_ANDA"
}
```
</details>
