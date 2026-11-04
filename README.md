# API Blue

REST API marketplace berbasis **Laravel**. Mendukung autentikasi, manajemen toko, produk, transaksi, saldo toko, dan penarikan dana (withdrawal), dengan pembayaran melalui **Midtrans**.

## Fitur

- **Autentikasi** token-based dengan Laravel Sanctum (register, login)
- **Role & permission** dengan spatie/laravel-permission
- **Pengguna & pembeli** (`User`, `Buyer`)
- **Toko** (`Store`) beserta **saldo** dan **riwayat saldo** (`StoreBalance`, `StoreBalanceHistory`)
- **Kategori produk, produk, gambar produk, dan ulasan produk**
- **Transaksi** beserta detail transaksi, terintegrasi Midtrans
- **Withdrawal**: pengajuan penarikan saldo toko dan persetujuan (approve)
- **Pagination** dengan format response seragam (`PaginateResource`, `ResponseHelper`)
- **Image helper** (`app/Helpers/ImageHelper`) untuk pengolahan gambar dengan font Arial
- **Lokalisasi** pesan validasi dalam Bahasa Indonesia dan Inggris (`lang/id`, `lang/en`)
- **Debugging** dengan Laravel Telescope
- **Testing** dengan Pest
- **Docker** (`docker-compose.yml`)

## Arsitektur

Proyek memakai **Repository Pattern**:

```
Controller → Request (validasi) → Repository (via Interface) → Model → Resource (response)
```

```
app/
├── Helpers/            # ImageHelper, ResponseHelper
├── Http/
│   ├── Controllers/    # Auth, Buyer, Product, ProductCategory, ProductReview,
│   │                   # Store, StoreBalance, StoreBalanceHistory,
│   │                   # Transaction, User, Withdrawal
│   ├── Requests/       # Form request validation
│   └── Resources/      # API resources (format response JSON)
├── Interfaces/         # Kontrak repository
├── Models/             # Eloquent models (UUID trait)
├── Providers/          # RepositoryServiceProvider mengikat Interface → Repository
├── Repositories/       # Logika akses data
└── Traits/UUID.php     # Primary key UUID
database/
├── factories/
├── migrations/
└── seeders/            # Role, Permission, User, Store, Buyer, Category, Product, Transaction
routes/api.php          # Definisi endpoint API
tests/                  # Pest (Feature & Unit)
```

## Model Data

| Model | Keterangan |
|---|---|
| `User` | Akun pengguna (dengan role) |
| `Buyer` | Profil pembeli |
| `Store` | Toko milik user |
| `StoreBalance` / `StoreBalanceHistory` | Saldo toko dan riwayat mutasinya |
| `Withdrawal` | Permintaan penarikan saldo |
| `ProductCategory` | Kategori produk |
| `Product` / `ProductImage` / `ProductReview` | Produk, gambar, dan ulasan |
| `Transaction` / `TransactionDetail` | Transaksi dan item di dalamnya |

## Prasyarat

- PHP (sesuai `composer.json`)
- Composer
- Node.js & npm
- SQLite (default, `database/database.sqlite`) atau database lain yang didukung Laravel
- Akun Midtrans (untuk pembayaran)

## Instalasi

```bash
# 1. Install dependency
composer install
npm install

# 2. Siapkan environment
cp .env.example .env
php artisan key:generate

# 3. Migrasi dan seeder
php artisan migrate --seed

# 4. Buat symlink storage (untuk upload gambar)
php artisan storage:link

# 5. Jalankan aplikasi
php artisan serve
npm run dev
```

API tersedia di `http://localhost:8000/api`.

## Konfigurasi

Atur nilai berikut di `.env`:

```env
APP_NAME="API Blue"
APP_URL=http://localhost:8000

DB_CONNECTION=sqlite

# Midtrans (lihat config/midtrans.php untuk nama variabel yang dipakai)
MIDTRANS_SERVER_KEY=
MIDTRANS_CLIENT_KEY=
MIDTRANS_IS_PRODUCTION=false
```

> Nama variabel Midtrans di atas adalah contoh umum. Sesuaikan dengan isi `config/midtrans.php` dan `.env.example`.

## Menjalankan dengan Docker

```bash
docker compose up -d --build
```

## Autentikasi

Endpoint terproteksi memerlukan token Sanctum:

```
Authorization: Bearer <token>
Accept: application/json
```

Token diperoleh dari endpoint login pada `AuthController`.

## Endpoint

Daftar lengkap ada di `routes/api.php`. Untuk melihatnya:

```bash
php artisan route:list --path=api
```

Kelompok resource utama: `auth`, `user`, `buyer`, `store`, `store-balance`, `store-balance-history`, `product-category`, `product`, `product-review`, `transaction`, `withdrawal`.

## Testing

```bash
php artisan test
# atau
./vendor/bin/pest
```

## Telescope

Dashboard debugging tersedia di `/telescope` (sesuai konfigurasi `config/telescope.php`). Nonaktifkan atau batasi aksesnya di production.

## Catatan Keamanan

- Jangan commit file `.env` (berisi kredensial).
- Jangan commit `storage/logs/*.log`, `storage/framework/views/*`, `bootstrap/cache/*`, dan `database/database.sqlite`.
- Batasi akses Telescope di lingkungan production.

## Lisensi

Tentukan lisensi proyek ini (mis. MIT).
