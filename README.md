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
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/info</span></strong> - Info Family Plan</summary>

Melihat semua slot dan anggota pada Family Plan dari nomor pengelola.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola Family Plan (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Family Plan info retrieved",
  "data": {
    "members": [
      {
        "slot_id": 1,
        "posisi_slot": 0,
        "nomer_anggota": "628xxxxxxxxxx",
        "nama_anggota": "Pengelola",
        "sisa_add": 5,
        "role": "PARENT",
        "tipe_slot": "MAIN",
        "kuber": "5 GB",
        "kuota_dipakai": "1.2 GB",
        "reset": "1 April 2026 00:00:00",
        "jadwal_kick": "31 Maret 2026",
        "family_member_id": "abc123"
      },
      {
        "slot_id": 2,
        "posisi_slot": 1,
        "nomer_anggota": "628xxxxxxxxxx",
        "nama_anggota": "Member A",
        "sisa_add": 0,
        "role": "CHILD",
        "tipe_slot": "ADDITIONAL",
        "kuber": "3 GB",
        "kuota_dipakai": "0 GB",
        "reset": "1 April 2026 00:00:00",
        "jadwal_kick": "31 Maret 2026",
        "family_member_id": "def456"
      },
      {
        "slot_id": 3,
        "posisi_slot": 2,
        "nomer_anggota": "",
        "nama_anggota": "",
        "sisa_add": 5,
        "role": "",
        "tipe_slot": "ADDITIONAL",
        "kuber": "0 GB",
        "kuota_dipakai": "0 GB",
        "reset": "",
        "jadwal_kick": "",
        "family_member_id": ""
      }
    ]
  }
}
```

**Catatan:**
- `posisi_slot` adalah indeks 0-based. Slot `posisi_slot: 0` selalu milik parent/pengelola
- `nomer_anggota` kosong artinya slot belum terisi
- `sisa_add` = sisa kesempatan add ke slot ini
- `family_member_id` diperlukan untuk endpoint `/api/akrab/cek-kuota`

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/add</span></strong> - Tambah Member</summary>

Menambahkan anggota ke slot kosong pada Family Plan.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "nomer_member": "08xxxxxxxxxx",
  "posisi_slot": 1,
  "alias": "Nama Anggota"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `nomer_member` | Nomor XL yang akan ditambahkan (wajib) |
| `posisi_slot` | Indeks slot tujuan (wajib, 0-based, tidak boleh 0) |
| `alias` | Nama tampilan anggota di Family Plan (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Member added successfully",
  "data": { ... }
}
```

**Response Error (400) — Slot sudah terisi**
```json
{
  "keterangan": "Posisi slot dipilih sudah ada anggota"
}
```

**Response Error (400) — Posisi slot tidak valid**
```json
{
  "keterangan": "Posisi slot tidak valid"
}
```

**Response Error (400) — Mencoba ubah slot parent**
```json
{
  "keterangan": "Posisi slot 0 adalah parent, tidak bisa diubah"
}
```

**Response Error (500) — Nomor tidak diizinkan XL**
```json
{
  "keterangan": "NOMER UDAH BENER??"
}
```

**Catatan:**
- Cek slot kosong terlebih dahulu via `/api/akrab/info` (`nomer_anggota == ""`)
- `posisi_slot: 0` selalu milik parent, tidak bisa diubah

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/kuber</span></strong> - Set Kuota Member</summary>

Mengatur besar kuota (kuber) untuk satu slot anggota.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "posisi_slot": 1,
  "kuber": 5
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `posisi_slot` | Indeks slot target (wajib, 0-based) |
| `kuber` | Besar kuota dalam GB (wajib, 0 = reset kuota) |

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Quota set successfully",
  "data": {
    "posisi_slot": 1,
    "msisdn": "628xxxxxxxxxx",
    "kuber_gb": 5,
    "kuber_bytes": 5368709120,
    "quota_formatted": "5 GB",
    "original_quota": 3221225472,
    "original_formatted": "3 GB"
  }
}
```

**Response Error (400) — Slot kosong**
```json
{
  "keterangan": "Slot kosong, tidak bisa set kuota"
}
```

**Catatan:**
- `kuber: 0` digunakan untuk mereset kuota menjadi 0
- Bisa juga digunakan untuk slot parent (`posisi_slot: 0`)

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/kick</span></strong> - Kick Member</summary>

Mengeluarkan anggota dari slot Family Plan berdasarkan posisi slot.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "posisi_slot": 1
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `posisi_slot` | Indeks slot yang akan dikick (wajib, 0-based) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "member_removed": true,
    "msisdn_member": "628xxxxxxxxxx",
    "posisi_slot": 1,
    "message": "Member berhasil dikeluarkan"
  }
}
```

**Catatan:**
- Gunakan `/api/akrab/info` dulu untuk melihood nomor anggota dan `posisi_slot` yang benar

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/cek-kuota</span></strong> - Cek Detail Kuota Member</summary>

Mengecek rincian kuota (nasional, area, bersama, reward) dari satu member berdasarkan `family_member_id`.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "family_member_id": "abc123"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `family_member_id` | ID member dari response `/api/akrab/info` (wajib) |

**Response Success (200)**
```json
{
  "benefits": [
    {
      "id": "561208_MAIN",
      "nama": "KUOTA NASIONAL",
      "informasi": "Kuota utama",
      "data_type": "DATA",
      "total_kuota": "5 GB",
      "sisa_kuota": "3.8 GB",
      "benefit_type": "MAIN",
      "benefit_category": "NATIONAL_QUOTA"
    },
    {
      "id": "561208_MAIN_AREA1",
      "nama": "KUOTA AREA 1",
      "informasi": "Kuota area Jawa",
      "data_type": "DATA",
      "total_kuota": "2 GB",
      "sisa_kuota": "2 GB",
      "benefit_type": "MAIN",
      "benefit_category": "AREA_QUOTA"
    }
  ]
}
```

**Catatan:**
- `family_member_id` didapat dari response `/api/akrab/info` di field `family_member_id`
- Benefit SMS dan Call difilter otomatis, tidak muncul di response

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/akrab/massal/add</span></strong> - Tambah Member + Set Kuota Sekaligus</summary>

Menambahkan anggota ke slot sekaligus mengatur kuotanya dalam satu request.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "nomer_member": "08xxxxxxxxxx",
  "posisi_slot": 1,
  "alias": "Nama Anggota",
  "kuber": 5
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `nomer_member` | Nomor XL yang akan ditambahkan (wajib) |
| `posisi_slot` | Indeks slot tujuan (wajib, tidak boleh 0) |
| `alias` | Nama tampilan anggota (wajib) |
| `kuber` | Besar kuota dalam GB (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "message": "Member added and quota set successfully",
  "data": { ... }
}
```

**Catatan:**
- Kombinasi `/api/akrab/add` + `/api/akrab/kuber` dalam satu request
- Lebih efisien untuk workflow add + set kuota sekaligus

</details>

---

## 🔵 Manage Circle [⬆ Kembali ke Atas](#-daftar-isi)

<details>
<summary><code style="background-color:#1a5276;color:white">POST</code> <strong><span style="color:red">/api/circle/validate</span></strong> - Validasi Nomor (PUBLIC)</summary>

Mengecek apakah satu atau beberapa nomor XL bisa bergabung ke Circle. **Tidak memerlukan API Key.**

**Header**
```json
{
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx"
}
```

Atau beberapa nomor sekaligus (pisahkan dengan koma, spasi, atau baris baru):
```json
{
  "nomer": "08xxxxxxxxxx, 08yyyyyyyyyy\n08zzzzzzzzz"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Satu atau beberapa nomor XL (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "total_checked": 2,
    "summary": {
      "valid": 1,
      "invalid": 1,
      "error": 0
    },
    "results": [
      {
        "msisdn": "628xxxxxxxxxx",
        "valid": true,
        "status": "valid",
        "message": "Eligible for Circle",
        "response_code": "200-2001",
        "pesan_tambahan": "BISA JOIN CIRCLE BOS"
      },
      {
        "msisdn": "628yyyyyyyyyy",
        "valid": false,
        "status": "invalid",
        "message": "Already in Circle",
        "response_code": "400-4001",
        "pesan_tambahan": "TIDAK BISA JOIN CIRCLE"
      }
    ]
  }
}
```

**Catatan:**
- `valid: true` / `pesan_tambahan: "BISA JOIN CIRCLE BOS"` = nomor bisa diundang
- `valid: false` = nomor sudah di circle lain atau tidak eligible
- `status: "error"` = gagal dicek (biasanya masalah koneksi ke XL)

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/info</span></strong> - Info Circle</summary>

Melihat informasi lengkap circle: data group, paket, dan daftar anggota.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "msisdn": "628xxxxxxxxxx",
    "circle_info": {
      "group_id": "GRP123",
      "group_name": "GRUB CIRCLE",
      "total_member": 3,
      "max_member": 5
    },
    "package": { ... },
    "members": {
      "active": [ ... ],
      "pending": [ ... ]
    }
  }
}
```

**Response Error (404) — Circle tidak ditemukan**
```json
{
  "keterangan": "Circle tidak ditemukan"
}
```

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/create</span></strong> - Buat Circle Baru</summary>

Membuat circle baru sekaligus mengundang member pertama.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "nama_pengelola": "Mayugoro",
  "nama_grub": "GRUB CIRCLE",
  "nomer_member": "08yyyyyyyyyy",
  "nama_member": "Member Pertama"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola (wajib) |
| `nama_pengelola` | Nama pengelola di dalam circle (wajib) |
| `nama_grub` | Nama group circle (wajib) |
| `nomer_member` | Nomor XL member pertama yang diundang (wajib) |
| `nama_member` | Nama member pertama di dalam circle (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "circle_created": true,
    "member_auto_accepted": true,
    "message": "Circle created successfully",
    "circle_info": { ... },
    "first_member": { ... }
  }
}
```

**Catatan:**
- Jika `member_auto_accepted: false`, member perlu accept undangan manual via `/api/circle/acc`
- Pastikan `nomer_member` sudah divalidasi via `/api/circle/validate` terlebih dahulu

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/add</span></strong> - Tambah Member ke Circle</summary>

Mengundang nomor XL untuk bergabung ke circle.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "nomer_member": "08yyyyyyyyyy",
  "nama_member": "Nama Member"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |
| `nomer_member` | Nomor XL yang akan diundang (wajib) |
| `nama_member` | Nama member di dalam circle (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "invitation_sent": true,
    "member_auto_accepted": false,
    "message": "Undangan berhasil dikirim",
    "member": { ... },
    "circle": { ... }
  }
}
```

**Catatan:**
- Jika `member_auto_accepted: false`, member harus accept undangan via `/api/circle/acc`
- Pastikan nomor sudah divalidasi via `/api/circle/validate` sebelum diundang
- Nama member hanya boleh huruf, angka, dan spasi

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/acc</span></strong> - Accept Undangan Circle</summary>

Menerima undangan circle secara manual untuk nomor member.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08yyyyyyyyyy"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL **member** yang menerima undangan (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "invitation_accepted": true,
    "message": "Undangan berhasil diterima",
    "member": { ... },
    "circle": { ... }
  }
}
```

**Catatan:**
- Nomor yang diisi adalah **nomor member** (penerima undangan), bukan pengelola
- Digunakan jika `member_auto_accepted: false` pada response `/api/circle/add`

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/kick</span></strong> - Kick Member dari Circle</summary>

Mengeluarkan anggota dari circle.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "nomer_member": "08yyyyyyyyyy"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |
| `nomer_member` | Nomor XL member yang akan dikeluarkan (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "member_removed": true,
    "message": "Member berhasil dikeluarkan",
    "msisdn": "628yyyyyyyyyy",
    "member_id": "MBR123",
    "circle_dissolved": false
  }
}
```

**Catatan:**
- `circle_dissolved: true` artinya circle otomatis dibubarkan karena anggota habis
- Tidak bisa kick diri sendiri (pengelola)

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/cek-bonus</span></strong> - Cek Bonus Circle</summary>

Mengecek daftar bonus yang tersedia untuk diklaim pada circle.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "msisdn": "628xxxxxxxxxx",
    "has_bonus": true,
    "total_bonus": 2,
    "bonus_list": [
      {
        "bonus_name": "Bonus 1GB",
        "bonus_type": "DATA",
        "action_param": "CLAIM_BONUS_1",
        "description": "Bonus kuota 1GB untuk circle aktif"
      }
    ]
  }
}
```

**Response (200) — Tidak ada bonus**
```json
{
  "status": "success",
  "data": {
    "msisdn": "628xxxxxxxxxx",
    "has_bonus": false,
    "total_bonus": 0,
    "bonus_list": []
  }
}
```

**Catatan:**
- `bonus_name` dari response ini digunakan sebagai input `/api/circle/klaim-bonus`

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/klaim-bonus</span></strong> - Klaim Bonus Circle</summary>

Mengklaim bonus circle yang tersedia.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "bonus_name": "Bonus 1GB"
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |
| `bonus_name` | Nama bonus dari response `/api/circle/cek-bonus` (wajib) |

**Response Success (200)**
```json
{
  "status": "success",
  "data": {
    "claimed": true,
    "bonus_name": "Bonus 1GB",
    "bonus_type": "DATA",
    "action_param": "CLAIM_BONUS_1",
    "destination": "628xxxxxxxxxx",
    "message": "Bonus berhasil diklaim"
  }
}
```

**Catatan:**
- Cek bonus tersedia terlebih dahulu via `/api/circle/cek-bonus`
- `bonus_name` harus sama persis dengan yang ada di response cek-bonus

</details>

<details>
<summary><code style="background-color:#1f6f1f;color:white">POST</code> <strong><span style="color:red">/api/circle/massal/add</span></strong> - Tambah Member Massal</summary>

Mengundang banyak anggota ke circle secara massal dalam satu request.

**Requires Authentication** - User API Key

**Header**
```json
{
  "Authorization": "user-api-key",
  "Content-Type": "application/json"
}
```

**Body**
```json
{
  "nomer": "08xxxxxxxxxx",
  "members": [
    { "nomer_member": "08yyyyyyyyy1", "nama_member": "Member 1" },
    { "nomer_member": "08yyyyyyyyy2", "nama_member": "Member 2" }
  ]
}
```

**Catatan kolom:**
| Field | Keterangan |
|---|---|
| `nomer` | Nomor XL pengelola circle (wajib) |
| `members` | Array daftar member yang akan diundang (wajib) |
| `members[].nomer_member` | Nomor XL tiap member (wajib) |
| `members[].nama_member` | Nama tiap member di dalam circle (wajib) |

**Catatan:**
- Setiap member diproses satu per satu, hasil tiap member dilaporkan di response
- Pastikan nomor sudah divalidasi via `/api/circle/validate` sebelum diundang massal

</details>
