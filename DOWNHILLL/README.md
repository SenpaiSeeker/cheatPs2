# DOWNHILLL — Cheat Pack Downhill Domination (PS2) untuk AetherSX2 / PCSX2

Paket kode cheat **Downhill Domination** versi **PlayStation 2** untuk region
**USA (NTSC-U)** dan **Eropa (PAL)**. Format `.pnach`, langsung bisa dipakai di
**AetherSX2** (Android) maupun **PCSX2** (PC).

Isi paket:

- **6 kode gameplay inti**: waktu, uang, energy, trick meter, turbo, skor
- **17 flag unlock permanen**: video, music pack, editor, level, bonus bike
- **9 kode entry video** (termasuk Rider Profile)
- **7 kode unlock rider individual**
- **ASM hook "All Riders"** — membuka 14 slot rider sekaligus
- **22 kode Bonus Purchases $1** (harga item bonus jadi $1) di modul opsional
- **Max Bank Money** $200.000.000 sebagai alternatif Infinite Cash
- **PAL**: 6 kode raw resmi Codejunkies + toggle Hyper Mode
- **PAL opsional**: 60 Hz, Y Fix, dan kombinasinya
- **19 kode tombol in-game** lengkap (lihat `KODE-TOMBOL.txt`)

> Semua kode NTSC sudah diverifikasi terhadap beberapa arsip komunitas
> (GameHacking.org, CheatDevicePS2, ekspor pnach Scribd). Kode PAL memakai
> daftar raw resmi Codejunkies; tidak ada port paksa dari NTSC.

---

## 1. Tabel region & CRC

| Region | Serial | CRC | File cheat |
|---|---|---|---|
| USA (NTSC-U) | SCUS-97177 | `5AE01D98` | `NTSC-U-USA/5AE01D98.pnach` |
| Europe (PAL) | SLES-52202 | `73671EFD` | `PAL-EUROPE/73671EFD.pnach` |

> Nama file **wajib** sama dengan CRC game. Kalau CRC game kamu berbeda,
> cheat tidak akan terbaca.
>
> Sebagian arsip lama memakai CRC `7E709DB4` untuk USA. Paket ini memakai CRC
> resmi dari PCSX2 Wiki, yaitu `5AE01D98`. Kalau emulator kamu melaporkan
> `7E709DB4`, coba rename file USA menjadi `7E709DB4.pnach` (isi kode sama),
> tetapi acuan yang didukung tetap `5AE01D98`.
>
> Versi Jepang (SCPS-15062) dan demo (SCUS-97329 / SLED-52325) **tidak**
> dicakup karena alamat dan CRC-nya berbeda.

Cara melihat CRC di AetherSX2:

1. Jalankan game sebentar, lalu buka **Game Properties / Game Info**.
2. CRC biasanya tampil di bagian info game atau di log emulator.
3. Alternatif di PC: jalankan PCSX2, CRC muncul di title bar / log console.

---

## 2. Cara pasang di AetherSX2 (Android)

1. Salin file `.pnach` yang sesuai ke folder cheat AetherSX2:

   ```
   /storage/emulated/0/Android/data/xyz.aethersx2.android/files/cheats/
   ```

   Kalau folder `cheats` belum ada, buat foldernya manual.

2. Pastikan nama file tetap `5AE01D98.pnach` (USA) atau `73671EFD.pnach` (PAL).

3. Buka AetherSX2 → **Settings → System → Enable Cheats**
   (nama menu bisa sedikit berbeda tergantung versi).
   Di beberapa versi, opsi ini ada di **Game Properties → Enable Cheats**.

4. Jalankan game. Efek kode langsung terasa saat masuk gameplay.

Contoh struktur akhir:

```
.../files/cheats/5AE01D98.pnach
```

### PCSX2 (PC)

Salin ke folder `cheats` PCSX2:

```
Documents/PCSX2/cheats/5AE01D98.pnach
```

Lalu aktifkan **System → Enable Cheats** dan centang cheat di
**Game Properties → Cheats**.

> **Mastercode tidak diperlukan.** Kode mastercode Action Replay
> (`F010A008 0024520F` untuk NTSC dan mastercode PAL) sengaja tidak
> dimasukkan karena AetherSX2/PCSX2 tidak membutuhkannya.

---

## 3. Isi kode file utama

### 3.1 USA — `5AE01D98.pnach` (51 baris patch)

#### A. Gameplay inti (6 kode)

| Kode | Alamat | Nilai | Efek |
|---|---|---|---|
| Always Low Time | `2043EEC8` | `00000000` | Timer balapan selalu 0 |
| Infinite Cash | `204347A8` | `773593EC` | Uang bank ~$2.000.000.000 |
| Infinite Energy | `2016352C` | `E4A30004` | Energy tidak berkurang |
| Infinite Trick Meter | `201E629C` | `E601002C` | Trick meter tidak berkurang |
| Infinite Energy Turbo-R3 | `202B2158` | `42C80000` | Boost/turbo selalu penuh (100.0f) |
| Max Score | `202B58DC` | `497423F0` | Skor 999.999 |

> `Infinite Cash` dan `Max Bank Money` memakai alamat yang sama
> (`0x4347A8`). **Jangan aktifkan keduanya sekaligus.** Versi Max Bank Money
> ada di `OPTIONAL/5AE01D98_MAX-MONEY.pnach`.

#### B. Unlock flag permanen (17 kode)

| Kode | Alamat | Nilai |
|---|---|---|
| Unlock Video Clips: All Rider Profiles (16 slot) | `10434720` | `0000FFFF` |
| Unlock Video: Mountain Cross | `00434730` | `00000001` |
| Unlock Video: Freeride | `00434731` | `00000001` |
| Unlock Video: Technical DH | `00434732` | `00000001` |
| Unlock Video: Crashes | `00434733` | `00000001` |
| Unlock Music 1 Pack | `00434735` | `00000001` |
| Unlock Music 2 Pack | `00434736` | `00000001` |
| Unlock Music 3 Pack | `00434737` | `00000001` |
| Unlock Pickup Editor | `00434738` | `00000001` |
| Unlock Fog Editor | `00434739` | `00000001` |
| Unlock Moshbowl | `0043473B` | `00000001` |
| Unlock Dual Slalom | `0043473C` | `00000001` |
| Unlock Super Jump | `0043473E` | `00000001` |
| Unlock Poster Maker | `0043473F` | `00000001` |
| Unlock Bonus Bike: Deer | `00434741` | `00000001` |
| Unlock Bonus Bike: Llama | `00434742` | `00000001` |
| Unlock Bonus Bike: Sheep | `00434743` | `00000001` |

#### C. Entry video individual (9 kode)

| Kode | Alamat | Nilai |
|---|---|---|
| Unlock Video: Intro | `104C6FAE` | `00000001` |
| Unlock Video: Technical DH | `104C6FBE` | `00000001` |
| Unlock Video: Mountain Cross | `104C6FCE` | `00000001` |
| Unlock Video: Freeride | `104C6FDE` | `00000001` |
| Unlock Video: Crashes | `104C6FEE` | `00000001` |
| Unlock Video: Tricks | `104C6FFE` | `00000001` |
| Unlock Video: Credits | `104C700E` | `00000001` |
| Unlock Video: Rider Profile | `104C701E` | `00000001` |
| Rider Profile bypass | `2018E7FC` | `00000000` |

#### D. Unlock rider individual (7 kode)

| Rider | Alamat | Nilai |
|---|---|---|
| Rakel (Sweden) | `004B25E3` | `00000001` |
| Crud (Germany) | `004B25E4` | `00000001` |
| Eric Carter (USA) | `004B25E5` | `00000001` |
| Tara Llanes (USA) | `004B25E6` | `00000001` |
| Richie Schley (Canada) | `004B25E7` | `00000001` |
| Brian Lopes (USA) | `004B25E8` | `00000001` |
| Missy Giove / Missy Glove (USA) | `004B25E9` | `00000001` |

#### E. ASM "All Riders" — 14 slot (12 baris)

| Kode | Alamat | Nilai |
|---|---|---|
| Hook 1: paksa `v1 = -1` | `20193E34` | `2003FFFF` |
| Code cave `0x000C0000` | `200C0000` | `3C030043` |
| Code cave `0x000C0004` | `200C0004` | `2001FFFF` |
| Code cave `0x000C0008` | `200C0008` | `03E00008` |
| Code cave `0x000C000C` | `200C000C` | `AC6146B0` |
| Hook 2: `j 0x000C0000` | `20194B90` | `08030000` |
| Flag rider slot 1 | `004B25DD` | `00000001` |
| Flag rider slot 2–3 | `104B25DE` | `00000101` |
| Flag rider slot 4–7 | `204B25E0` | `01010101` |
| Flag rider slot 8–11 | `204B25E4` | `01010101` |
| Flag rider slot 12–13 | `104B25E8` | `00000101` |
| Flag rider slot 14 | `004B25EA` | `00000001` |

Cara kerja singkat:

```
0x000C0000: lui  v1, 0x0043        ; v1 = 0x00430000
0x000C0004: addi at, zero, -1      ; at = 0xFFFFFFFF
0x000C0008: jr   ra
0x000C000C: sw   at, 0x46B0(v1)    ; [0x004346B0] = 0xFFFFFFFF (delay slot)
```

Hook di `0x00194B90` melompat ke code cave, menulis flag global
`0x004346B0 = -1`, lalu kembali. Flag rider di `0x004B25DD`–`0x004B25EA`
juga ditulis langsung sebagai lapisan kedua.

### 3.2 Eropa — `73671EFD.pnach` (9 baris patch)

Kode raw resmi Codejunkies (GameHacking.org game `101214`):

| Kode | Alamat | Nilai | Efek |
|---|---|---|---|
| Max Money (1/2) | `2013C850` | `3C010100` | `lui at, 0x0100` |
| Max Money (2/2) | `2013C854` | `AC612208` | `sw at, 0x2208(v1)` |
| Max Power | `202DC35C` | `42C80000` | Power 100.0f |
| Max Score After 1 Jump (1/2) | `201EA328` | `3C014960` | `lui at, 0x4960` |
| Max Score After 1 Jump (2/2) | `201EA338` | `44811000` | `mtc1 at, f2` |
| Hyper Mode: tahan X + Up | `D068E902` | `0000BFEF` | Cek input X+Up |
| Hyper Mode: set kecepatan | `2024DC04` | `00000000` | Aktifkan hyper |
| Normal Mode: tahan X + Down | `D068E902` | `0000BFBF` | Cek input X+Down |
| Normal Mode: kembalikan | `2024DC04` | `0C09833C` | Kembali normal |

Cara pakai Hyper Mode:

- Tahan **X + Up** saat berkendara → mode hyper aktif.
- Tahan **X + Down** → kembali ke kecepatan normal.

> **Keterbatasan PAL:** hanya 6 kode raw resmi yang tersedia untuk
> SLES-52202. Tidak ada kode unlock rider/item PAL yang terverifikasi.
> Unlock di PAL memakai **kode tombol in-game** (lihat `KODE-TOMBOL.txt`).
>
> Kode unlock NTSC **tidak dipaksakan** ke PAL karena pergeseran alamat
> statis tidak seragam. Perbandingan patch widescreen resmi NTSC vs PAL
> menunjukkan selisih yang berbeda-beda (mis. `0x1828`, `0x3AD8`, `0x52D0`,
> `0x61D0`, `0xBB80`), sementara sebagian alamat live-memory justru identik.
> Port dengan offset tetap akan menghasilkan alamat yang salah.

---

## 4. Modul opsional (`OPTIONAL/`)

AetherSX2 hanya memuat **satu** file per CRC, yaitu `<CRC>.pnach`.
Untuk memakai modul opsional, **rename** file modul menjadi `<CRC>.pnach`
dan simpan file utama sebagai cadangan.

### USA — `NTSC-U-USA/OPTIONAL/`

| File | Baris | Isi |
|---|---:|---|
| `5AE01D98_NO-ASM.pnach` | 46 | Semua kode tanpa ASM/code cave. Unlock rider memakai flag langsung + global flag `204346B0` |
| `5AE01D98_GAMEPLAY-ONLY.pnach` | 6 | Hanya 6 kode gameplay, tanpa unlock (progres career normal) |
| `5AE01D98_UNLOCK-ONLY.pnach` | 45 | Hanya unlock video/item/rider, tanpa kode gameplay |
| `5AE01D98_BONUS-1-DOLLAR.pnach` | 95 | File utama + 22 kode Bonus Purchases $1 (paling lengkap) |
| `5AE01D98_MAX-MONEY.pnach` | 51 | File utama dengan Max Bank Money $200.000.000, bukan Infinite Cash |

Contoh: untuk memakai versi paling lengkap di USA:

```
5AE01D98.pnach                    -> rename jadi 5AE01D98_BACKUP.pnach
OPTIONAL/5AE01D98_BONUS-1-DOLLAR.pnach -> rename jadi 5AE01D98.pnach
```

### Eropa — `PAL-EUROPE/OPTIONAL/`

| File | Baris | Isi |
|---|---:|---|
| `73671EFD_NO-HYPER.pnach` | 5 | Uang/power/skor saja, tanpa Hyper Mode |
| `73671EFD_HYPER-ONLY.pnach` | 4 | Hanya toggle Hyper Mode |
| `73671EFD_60HZ.pnach` | 1 | Paksa mode video 60 Hz |
| `73671EFD_Y-FIX.pnach` | 5 | Y Fix (hook `0x00224DCC`, code cave `0x000FFF00`) |
| `73671EFD_60HZ-YFIX.pnach` | 6 | Gabungan 60 Hz + Y Fix |

### Daftar 22 item Bonus Purchases $1 (USA)

Kode memakai pasangan **D-code + write**:

```
D036A8D0 00300001   ; kalau harga item > 1, jangan skip
2036A8D0 00000001   ; set harga jadi 1
```

| Item | Alamat harga |
|---|---|
| Gamelbak Bonus | `0x036A8D0` |
| Sparky Tricks | `0x036A8D4` |
| Stretchy Mode | `0x036A8D8` |
| Bonus Rider: ????? | `0x036A8DC` |
| Music 1: 5-Song Variety Pack | `0x036A8E0` |
| Music 2: 5-Song Variety Pack | `0x036A8E4` |
| Music 3: 5-Song Variety Pack | `0x036A8E8` |
| Bonus Level 1: Moshbowl | `0x036A8EC` |
| Bonus Level 2: Moshbowl | `0x036A8F0` |
| Bonus Level 3: Moshbowl | `0x036A8F4` |
| Video 1: Mountain Cross Clip | `0x036A8F8` |
| Video 2: Freeride Clip | `0x036A8FC` |
| Video 3: Technical Downhill Clip | `0x036A900` |
| Video 4: Crash Clip | `0x036A904` |
| Video 5: Trick Clip | `0x036A908` |
| Poster Maker | `0x036A90C` |
| Pickup Editor | `0x036A910` |
| Fog Editor | `0x036A914` |
| Bonus Bike 1: Sheep | `0x036A918` |
| Bonus Bike 2: Deer | `0x036A91C` |
| Bonus Bike 3: Llama | `0x036A920` |
| Prototype Bike | `0x036A924` |

> D-code `D...00300001` mengecek apakah harga saat ini **lebih besar dari 1**.
> Kalau harga sudah 1 (atau 0), write dilewati. Jadi kode tidak menimpa
> item yang sudah dibeli/gratis.

---

## 5. Kode tombol in-game

Kode tombol berlaku di USA maupun PAL, dimasukkan **saat gameplay dengan cepat**.
**Unlock Code wajib dimasukkan lebih dulu** sebelum kode lain.

| Kode | Urutan tombol |
|---|---|
| **Unlock Code (wajib pertama)** | Up, Triangle, Down, X, Left, Circle, Right, Square |
| Unlock Everything (versi 1) | Up, Down, Down, Up, Up, Down, Down |
| Unlock Everything (versi 2) | Down, Up, Up, Down, Down, Up, Up |
| $2.000 Instan | Right, Triangle, Triangle, Left |
| Free Money | Right, Up, Up, Circle, Circle, Square |
| Adrenaline Boost | Down, Left, Left, Right |
| Always Stoked | Down, Square, Square, Left, Circle |
| Anti Gravity | Down, Triangle, Square, Square, Up |
| Combat Free Code | Left, Square, Circle, Square, Left |
| Combat Upgrade | Up, Down, Left, Left, Right |
| Energy Restore | Down, Right, Right, Left, Left |
| Unlimited Energy | Down, Triangle, Left, Left, Square |
| Infinite Bottles | Up, X, Left, Left, Circle, Circle |
| Mega Flip | Right, Up, Up, Right, Right, Square |
| Speed Freak | Down, Triangle, Right, Right, Square |
| Stoke Trick Meter | Down, Left, Left, Right, Right |
| Super Bounce | Left, Square, X, Up, Triangle |
| Super Bunny Hop | Up, X, Left, Square, Up |
| Upgrade to Bottle | Up, Down, Left, Left, Right, Right |

Daftar lengkap dengan catatan ada di **`KODE-TOMBOL.txt`**.

---

## 6. Catatan penting

- **Backup memory card / save state** sebelum memakai cheat unlock.
  Kode unlock menulis flag ke data save; kalau save terlanjur aneh,
  kembalikan dari backup.
- **Jangan aktifkan dua file `.pnach` untuk CRC yang sama.** AetherSX2 hanya
  memuat satu file; kalau ada duplikat, bisa terjadi efek ganda.
- **Infinite Cash vs Max Bank Money** — keduanya menulis alamat `0x4347A8`.
  Pilih salah satu. `MAX-MONEY` memberi $200.000.000; `Infinite Cash` memberi
  ~$2.000.000.000.
- **ASM All Riders** memakai code cave `0x000C0000` dan meng-hook
  `0x00194B90`. Kalau game crash atau bentrok dengan patch lain
  (mis. widescreen), pakai `5AE01D98_NO-ASM.pnach`.
- **Bonus Purchases $1** ada di file terpisah karena berisi 44 baris tambahan
  (22 pasang D-code + write). Kalau game terasa berat, pakai file utama.
- **60 Hz (PAL)** mengubah mode video. Kalau layar/TV tidak mendukung 60 Hz,
  gambar bisa hitam atau tidak stabil — hapus file 60Hz.
- **Y Fix (PAL)** memakai code cave `0x000FFF00`. Jangan digabung dengan patch
  lain yang memakai code cave yang sama.
- **Hyper Mode (PAL)** adalah toggle: tahan X+Up untuk aktif, X+Down untuk
  kembali normal. Kode ini menulis instruksi di `0x0024DC04`, jadi efeknya
  langsung terasa saat berkendara.
- **Kode tombol** tidak bisa dimasukkan saat pause/loading/menu. Kalau gagal,
  ulangi dari Unlock Code.
- **Mastercode tidak dipakai** di pnach. Kalau kamu memakai Action Replay
  asli di PS2, mastercode tetap diperlukan — tetapi paket ini untuk emulator.

---

## 7. Troubleshooting

| Masalah | Penyebab / solusi |
|---|---|
| Cheat tidak terbaca | Nama file bukan CRC game, folder salah, atau Enable Cheats belum aktif |
| Semua kode jalan tapi game crash | Coba `5AE01D98_NO-ASM.pnach` atau `GAMEPLAY-ONLY`; pastikan region/CRC benar |
| Rider masih terkunci | Pakai file utama (ASM), atau masukkan kode tombol Unlock Code + Unlock Everything |
| Uang aneh / tidak berubah | Jangan gabung Infinite Cash dengan Max Bank Money; coba `MAX-MONEY` |
| Bonus masih mahal | Pakai `BONUS-1-DOLLAR.pnach`; kode hanya menurunkan harga jika harga > 1 |
| PAL Max Money tidak langsung terlihat | Lakukan transaksi/earn money sekali agar fungsi uang berjalan |
| PAL Hyper Mode tidak bereaksi | Tahan X+Up saat gameplay (bukan menu); pastikan tidak memakai file `NO-HYPER` |
| Layar hitam setelah 60 Hz | Hapus `73671EFD_60HZ.pnach` atau `60HZ-YFIX`; TV tidak mendukung 60 Hz |
| Grafik aneh setelah Y Fix | Hapus `73671EFD_Y-FIX.pnach`; code cave bisa bentrok dengan patch widescreen |
| Game freeze saat unlock semua | Aktifkan kode unlock satu per satu, atau pakai `UNLOCK-ONLY` |

---

## 8. Sumber & kredit

Kode dikumpulkan dari arsip komunitas PS2:

- **GameHacking.org — Downhill Domination (NTSC-U)**, game `103566`
  <https://gamehacking.org/game/103566>
  (kode Code Master, MadCatz, bonus purchases $1, unlock rider/video)
- **GameHacking.org — Downhill Domination (PAL)**, game `101214`
  <https://gamehacking.org/game/101214>
  (6 kode raw Codejunkies: Max Money, Max Power, Max Score, Hyper Mode,
  60 Hertz, Y Fix)
- **xs1l3n7x / pcsx2_cheats_collection** — pnach NTSC lengkap
  <https://github.com/xs1l3n7x/pcsx2_cheats_collection>
- **CheatDevicePS2** — sumber kode ARMax NTSC
- **Scribd** — ekspor pnach NTSC-U dan PAL (verifikasi silang)
- **PCSX2 Wiki — Downhill Domination** — serial & CRC resmi
  <https://wiki.pcsx2.net/Downhill_Domination>
- **psxdatacenter — SLES-52202** — daftar kode tombol in-game
  <https://psxdatacenter.com/psx2/games2/SLES-52202.html>
- **PCSX2 / pcsx2_patches** dan **PS2-Widescreen / OPL-Widescreen-Cheats** —
  referensi alamat widescreen NTSC vs PAL
  <https://github.com/PCSX2/pcsx2_patches>
  <https://github.com/PS2-Widescreen/OPL-Widescreen-Cheats>

Paket ini hanya merapikan, menerjemahkan, dan menggabungkan kode-kode tersebut
ke format `.pnach` yang siap pakai. Tidak ada kode yang diklaim sebagai temuan
baru; semua kredit tetap milik pembuat aslinya.

---

## 9. Struktur repository

```
DOWNHILLL/
├── README.md
├── KODE-TOMBOL.txt
├── NTSC-U-USA/
│   ├── 5AE01D98.pnach
│   └── OPTIONAL/
│       ├── 5AE01D98_NO-ASM.pnach
│       ├── 5AE01D98_GAMEPLAY-ONLY.pnach
│       ├── 5AE01D98_UNLOCK-ONLY.pnach
│       ├── 5AE01D98_BONUS-1-DOLLAR.pnach
│       └── 5AE01D98_MAX-MONEY.pnach
└── PAL-EUROPE/
    ├── 73671EFD.pnach
    └── OPTIONAL/
        ├── 73671EFD_NO-HYPER.pnach
        ├── 73671EFD_HYPER-ONLY.pnach
        ├── 73671EFD_60HZ.pnach
        ├── 73671EFD_Y-FIX.pnach
        └── 73671EFD_60HZ-YFIX.pnach
```

Selamat bermain. Kalau ada kode yang tidak jalan di versi game tertentu,
kirim CRC + region-nya supaya bisa dipetakan.
