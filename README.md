# The Ledger

> `~/finance $ budget --sync`

Personal finance tracker dengan estetika terminal — dibangun di atas HTML murni, Supabase, dan Chart.js. Tidak perlu server, tidak perlu build step. Deploy langsung ke GitHub Pages.

**[→ Buka App](https://p3nr0s3.github.io/Budget-Tracker/)**

---

## Fitur

- **Ikhtisar Keuangan** — dashboard ringkasan pemasukan, pengeluaran, dan sisa bulanan
- **Anggaran per Kategori** — tetapkan budget untuk pemasukan, pengeluaran tetap, pengeluaran variabel, dan tabungan
- **Pencatatan Transaksi** — catat transaksi harian dengan kategori, tanggal, dan keterangan
- **Grafik Trend** — visualisasi tren bulanan dan distribusi pengeluaran
- **Laporan** — ringkasan per kategori yang bisa dicetak
- **Multi-device Sync** — data tersinkron real-time via Supabase
- **Multi-user** — setiap akun terisolasi, data pribadi
- **Responsive** — bisa diakses dari desktop maupun smartphone
- **Dark / Light Mode**

---

## Stack

| Layer | Teknologi |
|---|---|
| Frontend | HTML + CSS + Vanilla JS (single file) |
| Database | [Supabase](https://supabase.com) (PostgreSQL) |
| Auth | Supabase Auth (email + password) |
| Charts | [Chart.js](https://www.chartjs.org/) v4.4.1 |
| Hosting | GitHub Pages |

---

## Struktur Database

Dua tabel di Supabase:

```sql
-- Anggaran per kategori
CREATE TABLE budgets (
  id         serial PRIMARY KEY,
  user_id    text NOT NULL,
  type       text NOT NULL,   -- income | fixed | variable | saving
  name       text NOT NULL,
  budget     numeric DEFAULT 0,
  updated_at timestamptz DEFAULT now(),
  UNIQUE(user_id, type, name)
);

-- Transaksi
CREATE TABLE transactions (
  id         serial PRIMARY KEY,
  user_id    text NOT NULL,
  date       date NOT NULL,
  cat        text NOT NULL,
  cat_type   text NOT NULL,
  note       text,
  amount     numeric NOT NULL,
  src        text DEFAULT 'm',  -- m = manual, q = quick entry
  created_at timestamptz DEFAULT now()
);
```

---

## Setup

### 1. Fork / Clone repo

```bash
git clone https://github.com/p3nr0s3/Budget-Tracker.git
cd Budget-Tracker
```

### 2. Buat project Supabase

1. Daftar di [supabase.com](https://supabase.com)
2. Buat project baru
3. Buka **SQL Editor**, jalankan script tabel di atas
4. Salin **Project URL** dan **anon public key** dari Settings → API

### 3. Update konfigurasi

Edit `index.html`, cari baris berikut dan ganti dengan credentials Supabase kamu:

```js
const SUPA_URL = 'https://your-project.supabase.co';
const SUPA_KEY = 'your-anon-key';
```

### 4. Set Supabase Auth URL

Supabase Dashboard → **Authentication → URL Configuration**:

- **Site URL:** `https://username.github.io/Budget-Tracker`
- **Redirect URLs:** tambahkan URL yang sama

### 5. Deploy ke GitHub Pages

Push ke branch `main` → GitHub Actions otomatis deploy. Aktifkan Pages di Settings → Pages → Source: `main` branch.

---

## Penggunaan

1. Buka app → daftar akun dengan email dan password
2. Set anggaran bulanan di halaman **Ikhtisar**
3. Catat transaksi di halaman **Transaksi**
4. Monitor trend di **Grafik** dan **Laporan**
5. Kelola kategori budget di **Pengaturan**

---

## Catatan Keamanan

- Supabase `anon key` bersifat public — aman diekspos di frontend
- Untuk produksi, aktifkan **Row Level Security (RLS)** di Supabase:

```sql
ALTER TABLE budgets ENABLE ROW LEVEL SECURITY;
ALTER TABLE transactions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "own_budgets" ON budgets
  FOR ALL USING (auth.uid()::text = user_id);

CREATE POLICY "own_transactions" ON transactions
  FOR ALL USING (auth.uid()::text = user_id);
```

---

## License

MIT
