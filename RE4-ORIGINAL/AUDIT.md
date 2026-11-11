# AUDIT TEKNIS — RESIDENT EVIL 4 PS2 (RE4-ORIGINAL)

**Paket:** `RE4-ORIGINAL`
**Target:** USA `SLUS-21134` / CRC `013E349D`; Europe `SLES-53702` / CRC `6BA2F6B9`
**Emulator:** AetherSX2 / PCSX2
**Arsitektur:** Emotion Engine R5900 (MIPS III + MMI), 32-bit little-endian
**Tanggal audit:** 2026-11-11
**Sifat:** audit statis (tanpa ISO, save state, atau emulator)

---

## 0. Kesimpulan singkat

1. **Tidak ada patch yang menulis langsung ke register input controller.**
   Tidak ada D-code/E-code tombol di seluruh paket, dan tidak ada alamat
   `0x00451402` (NTSC) atau `0x00451682` (PAL) yang ditulis.
2. **Satu kerusakan MIPS yang terbukti:** `jr ra` di `0x200C0298` pada code
   cave Rapid Fire tidak memiliki delay slot di `0x200C029C`. Empat file yang
   memakai Rapid Fire (main NTSC/PAL dan MAX NTSC/PAL) sudah diperbaiki dengan
   menulis NOP eksplisit.
3. **Satu label salah:** `20163DF0` (NTSC) dan `20164070` (PAL) bukan
   “Krauser’s Arm Always Ready”. `20163DF0` adalah kode
   “Leon’s Attacks Can’t Hurt/Kill Ashley Except Rocket Launcher”. Kode
   Krauser NTSC yang benar adalah `2086C264 24020000`; tidak ada alamat PAL
   terverifikasi.
4. **Satu port PAL tidak terverifikasi:** `20164070` hanya hasil `+0x280` dari
   `20163DF0` dan tidak ditemukan di daftar kode PAL resmi. Baris ini
   dinonaktifkan di MAX PAL.
5. **Satu konflik logika:** `2012ACFC` dan `2012ACF8` (NTSC), serta
   `2012AF7C` dan `2012AF78` (PAL), adalah dua varian belanja yang saling
   menimpa. Di MAX hanya varian “menambah” yang diaktifkan.
6. **Risiko kontrol yang paling jelas ada di `_KARAKTER`.** Semua barisnya
   masih dikomentari secara default. Jika diaktifkan, Ashley/Ada/Hunk/Krauser/Wesker
   memakai model, animasi, kamera, dan handler yang berbeda dari Leon.
7. **Auto QTE, super health, 1-hit kill, dan unlock pintu tidak menulis input
   controller.** Efek sampingnya ada di gameplay/QTE/event, bukan di pembacaan
   tombol.

---

## 1. Ruang lingkup, metode, dan batasan

### 1.1 File yang diperiksa

| File | Baris aktif sebelum audit | Baris aktif setelah audit |
|---|---:|---:|
| `NTSC-U-USA/013E349D.pnach` | 56 | 57 |
| `NTSC-U-USA/OPTIONAL/013E349D_MAX.pnach` | 71 | 72 |
| `NTSC-U-USA/OPTIONAL/013E349D_NO-RAPIDFIRE.pnach` | 23 | 23 |
| `NTSC-U-USA/OPTIONAL/013E349D_EXTRA.pnach` | 9 | 9 |
| `NTSC-U-USA/OPTIONAL/013E349D_KARAKTER.pnach` | 0 | 0 |
| `PAL-EUROPE/6BA2F6B9.pnach` | 56 | 57 |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_MAX.pnach` | 71 | 70 |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_NO-RAPIDFIRE.pnach` | 23 | 23 |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_EXTRA.pnach` | 9 | 9 |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_KARAKTER.pnach` | 0 | 0 |
| **Total** | **318** | **320** |

Perubahan jumlah baris:
- Main NTSC/PAL: `+1` NOP delay slot.
- MAX NTSC: `+1` NOP, `+1` kode Krauser NTSC, `-1` varian belanja yang
  dikomentari.
- MAX PAL: `+1` NOP, `-1` varian belanja yang dikomentari, `-1` port PAL
  `20164070` yang dinonaktifkan.

### 1.2 Metode

1. Validasi format setiap baris `patch=1,EE,<8 hex>,extended,<8 hex>`.
2. Decode instruksi MIPS pada seluruh code cave Rapid Fire.
3. Pemeriksaan branch, jump, dan delay slot.
4. Pemeriksaan tabrakan alamat antar-cheat dan overwrite memori.
5. Pencarian alamat controller/input (`0x0045xxxx`, D-code, E-code).
6. Perbandingan alamat NTSC vs PAL dan delta tetap `+0x280`.
7. Cross-check sumber: GameHacking.org, PS2-HOME, daftar kode PAL mentah,
   dan arsip pnach komunitas.
8. Validasi akhir: tidak ada duplikat, tidak ada D/E-code, tidak ada alamat
   input controller yang ditulis, dan delay slot ada di semua file Rapid Fire.

### 1.3 Kategori

- **AMAN** — tidak ditemukan indikasi gangguan kontrol. Efek samping gameplay
  tetap dicatat.
- **PERLU PENGUJIAN** — belum cukup bukti untuk memastikan aman; biasanya
  karena patch menyentuh instruksi kode yang tidak didisassembly penuh.
- **BERISIKO** — berpotensi mengganggu kontrol, kamera, aiming, QTE, atau
  alur gameplay, tergantung cara pakai.
- **BERMASALAH** — kesalahan teknis yang bisa dijelaskan dan dibuktikan.

### 1.4 Batasan

- Tidak ada ISO/save state Resident Evil 4 di lingkungan audit, jadi tidak ada
  klaim “sudah diuji di AetherSX2/PCSX2”.
- Analisis MIPS dilakukan dari nilai pnach dan sumber kode komunitas, bukan
  dari disassembly ELF penuh.
- Alamat yang tidak memiliki sumber PAL resmi tidak dianggap benar hanya karena
  hasil `+0x280`.
- Kategori “AMAN” berarti aman terhadap kontrol, bukan jaminan bebas crash
  atau bebas efek samping gameplay.

---

## 2. Ringkasan temuan

| # | Temuan | File | Alamat | Kategori | Status |
|---|---|---|---|---|---|
| 1 | `jr ra` tanpa delay slot | main NTSC/PAL, MAX NTSC/PAL | `200C0298` / `200C029C` | BERMASALAH | Diperbaiki: NOP |
| 2 | Label Krauser salah | MAX NTSC/PAL | `20163DF0` / `20164070` | BERMASALAH | Label dikoreksi |
| 3 | Port PAL `20164070` tanpa sumber | MAX PAL | `20164070` | BERMASALAH | Dinonaktifkan |
| 4 | Konflik varian belanja | MAX NTSC/PAL | `2012ACF8`/`2012ACFC`, `2012AF78`/`2012AF7C` | BERMASALAH | Satu varian dinonaktifkan |
| 5 | Ganti karakter non-Leon | KARAKTER NTSC/PAL | `0042DA8A` / `0042DD0A` | BERISIKO | Peringatan ditambahkan |
| 6 | Costume non-Leon / invalid | KARAKTER NTSC/PAL | `0042DA8B`/`0042DA8D`, `0042DD0B`/`0042DD0D` | BERISIKO | Peringatan ditambahkan |
| 7 | Ashley always/never present | KARAKTER NTSC/PAL | `0042DAFF` / `0042DD7F` | BERISIKO | Peringatan ditambahkan |
| 8 | Unlock All Doors | MAX NTSC/PAL | `1042DDA8`+`2042DDC0..D0`, `1042E028`+`2042E040..50` | BERISIKO | Peringatan dipertahankan |
| 9 | 1-hit kill | MAX NTSC/PAL | `201C2A9C` / `201C2D1C` | BERISIKO | Peringatan dipertahankan |
| 10 | Super Infinite Health | MAX NTSC/PAL | `10246F36`/`10163C3A`, `102471B6`/`10163EBA` | PERLU PENGUJIAN | Tidak diubah |
| 11 | Auto QTE | main/MAX/NO-RAPIDFIRE | `101DE596` / `101DE816` | PERLU PENGUJIAN | Tidak diubah |
| 12 | Flag Rapid Fire 16-bit vs load 32-bit | main/MAX | `100C0218` | PERLU PENGUJIAN | Tidak diubah, dicatat |

---

## 3. Temuan BERMASALAH

### 3.1 Rapid Fire: `jr ra` tanpa delay slot

**File:**
- `NTSC-U-USA/013E349D.pnach`
- `PAL-EUROPE/6BA2F6B9.pnach`
- `NTSC-U-USA/OPTIONAL/013E349D_MAX.pnach`
- `PAL-EUROPE/OPTIONAL/6BA2F6B9_MAX.pnach`

**Alamat:**
- `0x200C0298` = `03E00008` (`jr ra`)
- `0x200C029C` = **tidak ditulis** pada versi lama

**Penyebab teknis:**
`jr ra` adalah instruksi branch. Dalam arsitektur MIPS, instruksi pada
`PC+4` (delay slot) **selalu dieksekusi** sebelum branch mengambil alih.
Pnach hanya menulis alamat yang tercantum; jika `0x200C029C` tidak ditulis,
CPU akan mengeksekusi apa pun yang kebetulan berada di sana.

Di code cave yang masih bersih, lokasi itu mungkin berisi `0x00000000`
(efektif NOP), sehingga masalah tidak selalu muncul. Tetapi jika area itu
pernah dipakai game, cheat lain, atau sisa data, instruksi sampah bisa:
- menulis ke register `at`/`v0`/`v1`/register lain;
- menulis ke memori game;
- menjadi branch/jump yang mengalihkan alur eksekusi;
- merusak state player/kamera/input secara tidak langsung.

Karena itu gejalanya bisa **kadang-kadang**: karakter bergerak sendiri,
kamera liar, analog tidak normal, atau game freeze. Ini bukan karena cheat
menulis tombol, tetapi karena CPU mengeksekusi instruksi yang tidak valid
setelah `jr ra`.

**Analisis MIPS (potongan akhir cave):**

| Alamat | Word | Instruksi | Catatan |
|---|---|---|---|
| `0x200C0280` | `3C02000C` | `lui v0, 0x000C` | alamat flag |
| `0x200C0284` | `8C420218` | `lw v0, 0x0218(v0)` | baca flag 32-bit |
| `0x200C0288` | `24030001` | `addiu v1, zero, 1` | pembanding |
| `0x200C028C` | `14430002` | `bne v0, v1, +2` | target `0x200C0298` |
| `0x200C0290` | `24030002` | `addiu v1, zero, 2` | delay slot branch |
| `0x200C0294` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate-of-fire |
| `0x200C0298` | `03E00008` | `jr ra` | kembali ke pemanggil |
| `0x200C029C` | **hilang** | — | **delay slot wajib** |

**Perbaikan:**
```text
patch=1,EE,200C0298,extended,03E00008
patch=1,EE,200C029C,extended,00000000
```

**Validasi:** keempat file sekarang memiliki `200C029C 00000000` tepat setelah
`200C0298 03E00008`.

**Catatan:** NOP ini tidak mengubah fitur Rapid Fire. Rate-of-fire tetap
ditulis ke `+0x11A` dari struct player. Yang diperbaiki hanya kontrol alur
setelah `jr ra`.

---

### 3.2 Label `20163DF0` / `20164070` bukan Krauser

**File:**
- `NTSC-U-USA/OPTIONAL/013E349D_MAX.pnach`
- `PAL-EUROPE/OPTIONAL/6BA2F6B9_MAX.pnach`

**Alamat:**
- NTSC `20163DF0 00000000`
- PAL `20164070 00000000` (hasil `+0x280`)

**Bukti:**
- GameHacking.org mencatat `20163DF0 00000000` sebagai
  **“Leon’s Attacks Can’t Hurt/Kill Ashley Except Rocket Launcher”**.
- Kode Krauser NTSC yang tercatat adalah `2086C264 24020000`
  (“Krauser’s Arm is Always Ready”).
- `20164070` tidak ditemukan di daftar kode PAL resmi; alamat itu hanya
  hasil penambahan `+0x280`.

**Dampak:**
Label lama menyesatkan. Pengguna mengira sedang mengaktifkan fitur Krauser,
padahal kode itu mencegah serangan Leon melukai Ashley. Untuk PAL, alamat
`20164070` belum tentu mengenai fungsi yang sama; NOP di alamat yang salah
bisa merusak fungsi lain, termasuk fungsi yang memengaruhi kamera/gerakan.

**Perbaikan:**
- NTSC: label dikoreksi menjadi kode Leon–Ashley; kode Krauser NTSC yang benar
  (`2086C264 24020000`) ditambahkan ke MAX USA.
- PAL: label dikoreksi; `20164070` **dinonaktifkan** karena tidak ada sumber
  PAL terverifikasi. Kode Krauser NTSC tidak dipaksakan ke PAL.

---

### 3.3 Konflik varian belanja di MAX

**File:**
- `NTSC-U-USA/OPTIONAL/013E349D_MAX.pnach`
- `PAL-EUROPE/OPTIONAL/6BA2F6B9_MAX.pnach`

**Alamat:**
- NTSC: `2012ACFC` (“tidak mengurangi”) dan `2012ACF8` (“menambah”)
- PAL: `2012AF7C` (“tidak mengurangi”) dan `2012AF78` (“menambah”)

**Penyebab:**
Keduanya berada di routine belanja yang sama, hanya berjarak satu instruksi.
Kode “tidak mengurangi” menimpa satu instruksi; kode “menambah” mengubah
instruksi di sebelahnya. Mengaktifkan keduanya membuat logika belanja saling
menimpa: efek “menambah” bisa dibatalkan, atau nilai uang menjadi tidak
konsisten.

Ini **bukan** overwrite tombol/input, tetapi tetap kesalahan logika yang
mudah dibuktikan dari pasangan alamat dan label sumber.

**Perbaikan:**
- MAX NTSC: `2012ACFC` dikomentari; `2012ACF8` tetap aktif.
- MAX PAL: `2012AF7C` dikomentari; `2012AF78` tetap aktif.
- File utama dan `_NO-RAPIDFIRE` tetap memakai varian “tidak mengurangi”.
- Tidak ada kehilangan fitur: kedua varian masih tersedia di file berbeda.

---

## 4. Temuan BERISIKO

### 4.1 Ganti karakter (`_KARAKTER`)

**Alamat:**
- NTSC: `0042DA8A` (character slot 1)
- PAL: `0042DD0A` (character slot 1)

**Nilai:** `00` Leon, `01` Ashley, `02` Ada, `03` Hunk, `04` Krauser,
`05` Wesker.

**Kenapa berisiko:**
Campaign normal dibangun untuk Leon. Model dan animasi karakter lain tidak
selalu memiliki:
- state aiming/shooting yang sama;
- handler kamera over-the-shoulder yang sama;
- animasi QTE, ladder, grab, dan context action yang sama;
- script campaign yang sama (Ashley/Ada/Hunk/Krauser/Wesker terutama
  dirancang untuk Mercenaries/Separate Ways).

Memaksa ID karakter di campaign bisa membuat game memakai animasi/kamera
Leon pada model lain, atau memakai handler karakter lain pada state Leon.
Gejala yang mungkin: karakter bergerak sendiri, kamera menempel ke entity
salah, analog tidak responsif, aiming/QTE gagal, freeze saat cutscene.

**Status:** semua baris masih dikomentari (0 patch aktif). Kategori
**BERISIKO**, bukan **BERMASALAH**, karena tidak aktif secara default dan
bergantung pada baris yang dibuka pengguna.

**Perbaikan dokumentasi:** blok `[PERINGATAN KONTROL]` ditambahkan di kedua
file; hanya Leon yang ditandai aman untuk campaign; karakter lain ditandai
`BERISIKO KONTROL`; ada larangan menggabung dengan MAX/1-hit kill/unlock pintu.

---

### 4.2 Costume

**Alamat:**
- NTSC: `0042DA8B` (slot 1), `0042DA8D` (slot 2)
- PAL: `0042DD0B` (slot 1), `0042DD0D` (slot 2)

**Kenapa berisiko:**
ID costume bergantung pada karakter. Costume 4 (RPD/mafia) hanya cocok untuk
Leon; memakainya pada slot Ashley bisa memuat model/animasi yang tidak sesuai.
Ini data write, bukan input, tetapi model yang salah bisa membuat kamera dan
animasi terlihat aneh.

**Status:** semua baris dikomentari; peringatan ditambahkan.

---

### 4.3 Ashley selalu / tidak pernah muncul

**Alamat:**
- NTSC: `0042DAFF`
- PAL: `0042DD7F`

**Nilai:** `04` selalu muncul, `00` tidak pernah muncul.

**Kenapa berisiko:**
Mengubah state partner bisa memengaruhi event escort, trigger cutscene,
posisi kamera, dan QTE yang melibatkan Ashley. Bukan kontrol langsung, tetapi
bisa membuat event macet atau kamera mengikuti entity yang salah.

**Status:** dikomentari; peringatan ditambahkan.

---

### 4.4 Unlock All Doors (MAX)

**Alamat:**
- NTSC: `1042DDA8`, `2042DDC0`, `2042DDC4`, `2042DDC8`, `2042DDCC`,
  `2042DDD0`
- PAL: `1042E028`, `2042E040`, `2042E044`, `2042E048`, `2042E04C`,
  `2042E050`

**Kenapa berisiko:**
Membuka semua pintu dapat melewati trigger cerita, memicu event di luar urutan,
atau membuat softlock. Tidak menulis input controller, tetapi bisa membuat
game berada di state yang tidak dirancang untuk alur normal.

**Status:** tetap ada di MAX dengan komentar “bisa melewati event, hati-hati”.

---

### 4.5 1-hit kill (MAX)

**Alamat:**
- NTSC: `201C2A9C 24020000`
- PAL: `201C2D1C 24020000`

**Kenapa berisiko:**
Ini patch instruksi di routine damage. Efek yang diinginkan adalah musuh mati
sekali kena, tetapi di beberapa event bisa ikut mengenai player atau NPC.
Tidak menyentuh input/kamera.

**Status:** tetap ada di MAX dengan peringatan; kategori BERISIKO gameplay.

**Catatan PAL:** daftar PAL utama mencantumkan `201C2D1C` (cocok dengan
`+0x280` dari NTSC `201C2A9C`). Thread PS2-HOME PAL juga mencantumkan
`201C2B84` untuk “1-Hit Death (Enemies)”. Paket ini memakai `201C2D1C`
karena konsisten dengan kode NTSC yang dipakai dan daftar PAL utama. Jika
tidak bekerja, uji `201C2B84` secara terpisah; jangan aktifkan keduanya.

---

## 5. Temuan PERLU PENGUJIAN

### 5.1 Super Infinite Health (MAX)

**Alamat:**
- NTSC: `10246F36 00001000` (slot 1), `10163C3A 00001000` (slot 2)
- PAL: `102471B6 00001000` (slot 1), `10163EBA 00001000` (slot 2)

**Kenapa perlu pengujian:**
Ini bukan data write biasa, melainkan write 16-bit ke tengah instruksi kode
(immediate field). Sumber komunitas mencantumkannya sebagai “Super Infinite
Health”, tetapi instruction target tidak didisassembly penuh dalam audit ini.
Jika alamat/CRC tepat, efeknya seharusnya hanya pada perhitungan damage.
Jika tidak, patch bisa mengenai instruksi lain.

**Status:** tidak diubah; hanya dipakai di MAX; pengguna diminta backup save.

### 5.2 Auto QTE

**Alamat:**
- NTSC: `101DE596 00001000`
- PAL: `101DE816 00001000`

**Kenapa perlu pengujian:**
Ini juga write 16-bit ke kode, kemungkinan mengubah konstanta timer button
masher menjadi `0x1000`. Tidak ada indikasi menulis input controller, tetapi
efeknya perlu dipastikan pada QTE grab, QTE boulder, dan QTE knife fight.
Jika QTE justru gagal, nonaktifkan baris ini dan uji ulang.

**Status:** tidak diubah karena fitur ini inti dari paket dan tidak ada bukti
kerusakan.

### 5.3 Flag Rapid Fire 16-bit vs load 32-bit

**Alamat:** `100C0218 00000001` (NTSC/PAL memakai alamat cave yang sama).

**Catatan teknis:**
Cave membaca flag dengan `lw` 32-bit di `0x200C0284` (`8C420218`), tetapi
pnach menulis flag dengan `100C0218` yang hanya 16-bit. Jika upper halfword
`0x000C021A` bukan nol, perbandingan `flag == 1` bisa gagal dan blok
“universal” tidak dijalankan. Akibatnya Rapid Fire hanya bekerja untuk lima
ID handgun, bukan semua senjata.

Ini **bukan** gangguan kontrol. Audit tidak mengubahnya karena:
- kode sumber asli juga memakai `100C0218`;
- menulis 32-bit (`200C0218`) bisa menimpa area yang mungkin dipakai game;
- belum ada bukti upper halfword selalu nol atau tidak.

Jika Rapid Fire tidak terasa di senjata non-handgun, uji dengan mengganti
`100C0218` menjadi `200C0218` di salinan terpisah, lalu bandingkan.

---

## 6. TEMUAN AMAN (kontrol)

Bagian ini mendaftar semua kelompok patch yang tidak menunjukkan indikasi
menulis input controller. “AMAN” di sini khusus kontrol; efek samping gameplay
tetap ada di catatan.

### 6.1 Darah & health (data write)

| Kode | NTSC | PAL | Catatan |
|---|---|---|---|
| Infinite Health Leon | `1042DA76 00000960` | `1042DCF6 00000960` | data health |
| Max Health Leon | `1042DA78 00000960` | `1042DCF8 00000960` | data health |
| Infinite Health Ashley | `1042DA7A 000004B0` | `1042DCFA 000004B0` | data health |
| Max Health Ashley | `1042DA7C 000004B0` | `1042DCFC 000004B0` | data health |

Tidak ada akses ke `0x0045xxxx`; hanya field health di struct karakter.

### 6.2 Ammo & grenade (patch kode, bukan input)

| Kode | NTSC | PAL |
|---|---|---|
| Infinite Ammo | `201200AC 00000000` | `2012032C 00000000` |
| Infinite Grenades/RPG | `2011EF30 00000000` | `2011F1B0 00000000` |
| Isi grenade/RPG | `1011EF3E 00001000` | `1011F1BE 00001000` |

Semua berada di routine ammo/weapon, bukan di pembacaan controller.

### 6.3 Rapid Fire setelah perbaikan

| Komponen | NTSC | PAL |
|---|---|---|
| Hook | `201672EC 08030088` | `2016756C 08030088` |
| Cave | `200C0220`–`200C029F` | `200C0220`–`200C029F` |
| Pointer player | `8C2103B0` | `8C210630` |
| Pointer weapon | `8C420FA8` | `8C421228` |
| Flag | `100C0218 00000001` | `100C0218 00000001` |
| Delay slot | `200C029C 00000000` | `200C029C 00000000` |

Setelah NOP delay slot, cave tidak menulis input controller. Yang ditulis
hanya `+0x11A` dari struct player (rate-of-fire). Kategori kontrol:
**AMAN secara struktur setelah fix**; stabilitas code cave tetap
**PERLU PENGUJIAN** (uji beban, ganti senjata, cutscene, dan Mercenaries).

### 6.4 Uang & toko (file utama / NO-RAPIDFIRE)

| Kode | NTSC | PAL |
|---|---|---|
| Max Pesetas | `2042DA6C 05F5E0FF` | `2042DCEC 05F5E0FF` |
| Belanja tidak mengurangi | `2012ACFC 00000000` | `2012AF7C 00000000` |

Data uang dan satu patch instruksi di routine belanja; tidak ada input.

### 6.5 Unlock & Ada’s Reports

| Kode | NTSC | PAL |
|---|---|---|
| Unlock Everything | `10430F66 0000FFFF` | `104311E6 0000FFFF` |
| Have All Ada’s Reports | `00430F6D 00000004` | `004311ED 00000004` |

Flag data save; tidak ada input. Efek samping progresi dicatat di README.

### 6.6 Mercenaries

| Kode | NTSC | PAL |
|---|---|---|
| Infinite Time | `2041B668 00000E00` | `2041B8E8 00000E00` |
| Max Score | `203CFA6C 05F5E0FF` | `203CFCEC 05F5E0FF` |
| Max Kills | `203CFA84 0000270F` | `203CFD04 0000270F` |
| Max Combo | `203CFA70 0000270F` | `203CFCF0 0000270F` |

Data statistik Mercenaries; tidak ada input.

### 6.7 Statistik Results

| Kode | NTSC | PAL |
|---|---|---|
| 0 kali mati | `00430F34 00000000` | `004311B4 00000000` |
| Max Enemies Killed | `20430F38 000003E7` | `204311B8 000003E7` |
| Lanjutan | `20430F3C 0000270F` | `204311BC 0000270F` |
| Perfect Hit Ratio | `20430F40 00000064` | `204311C0 00000064` |
| Lanjutan 1 | `20430F44 0000270F` | `204311C4 0000270F` |
| Lanjutan 2 | `20430F48 00000064` | `204311C8 00000064` |
| Lanjutan 3 | `20430F4C 0000270F` | `204311CC 0000270F` |

Data hasil; tidak ada input.

### 6.8 EXTRA — ammo, uang, waktu save

| Kode | NTSC | PAL |
|---|---|---|
| Max Infinite Ammo | `2012009C 24020318` | `2012031C 24020318` |
| Equip Weapon = Max Ammo | `2011FA60 24021F38` | `2011FCE0 24021F38` |
| Equip Weapon = Red Rounds | `2011FA64 34420001` | `2011FCE4 34420001` |
| Never Reload | `2011FF8C 24420000` | `2012020C 24420000` |
| Belanja menambah | `2012ACF8 00451021` | `2012AF78 00451021` |
| Jual item max (1) | `2012A90C 3C0205F5` | `2012AB8C 3C0205F5` |
| Jual item max (2) | `2012A910 3442E0FF` | `2012AB90 3442E0FF` |
| Always Low Game Save Time | `2042DA54 00000000` | `2042DCD4 00000000` |
| No Times Saved Recorded | `1042DA5A 00000000` | `1042DCDA 00000000` |

Patch kode di routine ammo/uang/save; tidak ada input controller.

### 6.9 MAX — Krauser NTSC

| Kode | NTSC | PAL |
|---|---|---|
| Krauser’s Arm Always Ready | `2086C264 24020000` | tidak tersedia |

Khusus Mercenaries NTSC. Tidak ada alamat PAL terverifikasi, jadi tidak
dimasukkan ke PAL.

### 6.10 KARAKTER — baris yang dikomentari

Semua baris `patch=` di `_KARAKTER` berstatus komentar (16 baris per region).
Karena tidak ada patch aktif, file ini tidak mengubah apa pun sampai pengguna
membuka salah satu baris. Kategori risiko per baris dijelaskan di bagian 4.

---

## 7. Analisis MIPS lengkap code cave Rapid Fire

### 7.1 Hook

```text
0x001672EC: 08030088   j 0x000C0220
0x001672F0: <instruksi asli>   ; delay slot, tidak diubah
```

Perhitungan target:
`(0x08030088 & 0x03FFFFFF) << 2 = 0x000C0220`.
Karena `PC+4` berada di segmen `0x00000000`, target efektif adalah
`0x000C0220`.

PAL:
```text
0x0016756C: 08030088   j 0x000C0220
```
Delta hook NTSC→PAL = `+0x280`.

### 7.2 Cave

| Alamat | Word | Instruksi | Fungsi |
|---|---|---|---|
| `0x200C0220` | `3C010042` | `lui at, 0x0042` | siapkan alamat pointer |
| `0x200C0224` | `8C2103B0` | `lw at, 0x03B0(at)` | `at = [0x004203B0]` (player) |
| `0x200C0228` | `3C020043` | `lui v0, 0x0043` | siapkan alamat weapon |
| `0x200C022C` | `8C420FA8` | `lw v0, 0x0FA8(v0)` | `v0 = [0x00430FA8]` (weapon ID) |
| `0x200C0230` | `24030220` | `addiu v1, zero, 0x220` | ID handgun 1 |
| `0x200C0234` | `14430002` | `bne v0, v1, +2` | ke `0x200C0240` |
| `0x200C0238` | `24030002` | `addiu v1, zero, 2` | delay slot: rate 2 |
| `0x200C023C` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate-of-fire |
| `0x200C0240` | `24030221` | `addiu v1, zero, 0x221` | ID handgun 2 |
| `0x200C0244` | `14430002` | `bne v0, v1, +2` | ke `0x200C0250` |
| `0x200C0248` | `24030002` | `addiu v1, zero, 2` | delay slot |
| `0x200C024C` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate |
| `0x200C0250` | `24030222` | `addiu v1, zero, 0x222` | ID handgun 3 |
| `0x200C0254` | `14430002` | `bne v0, v1, +2` | ke `0x200C0260` |
| `0x200C0258` | `24030002` | `addiu v1, zero, 2` | delay slot |
| `0x200C025C` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate |
| `0x200C0260` | `24030224` | `addiu v1, zero, 0x224` | ID handgun 4 |
| `0x200C0264` | `14430002` | `bne v0, v1, +2` | ke `0x200C0270` |
| `0x200C0268` | `24030002` | `addiu v1, zero, 2` | delay slot |
| `0x200C026C` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate |
| `0x200C0270` | `24030228` | `addiu v1, zero, 0x228` | ID handgun 5 |
| `0x200C0274` | `14430002` | `bne v0, v1, +2` | ke `0x200C0280` |
| `0x200C0278` | `24030002` | `addiu v1, zero, 2` | delay slot |
| `0x200C027C` | `A423011A` | `sh v1, 0x011A(at)` | tulis rate |
| `0x200C0280` | `3C02000C` | `lui v0, 0x000C` | alamat flag |
| `0x200C0284` | `8C420218` | `lw v0, 0x0218(v0)` | baca flag 32-bit |
| `0x200C0288` | `24030001` | `addiu v1, zero, 1` | pembanding |
| `0x200C028C` | `14430002` | `bne v0, v1, +2` | ke `0x200C0298` |
| `0x200C0290` | `24030002` | `addiu v1, zero, 2` | delay slot |
| `0x200C0294` | `A423011A` | `sh v1, 0x011A(at)` | universal rate |
| `0x200C0298` | `03E00008` | `jr ra` | kembali |
| `0x200C029C` | `00000000` | `nop` | **fix audit** |

### 7.3 Register yang disentuh

- `at` (`$1`): pointer player.
- `v0` (`$2`): weapon ID / flag.
- `v1` (`$3`): nilai rate-of-fire / ID pembanding.
- `ra` (`$31`): hanya dibaca oleh `jr ra`, tidak ditulis.
- Tidak ada `s0`–`s7`, `sp`, `fp`, `gp`, `k0`, `k1`.

Karena hook berada di epilogue fungsi rate-of-fire, clobber `at`/`v0`/`v1`
tidak mengubah pointer stack. Tidak ada `sw`/`sh` ke alamat input.

### 7.4 Mengapa bukan “tombol dipaksa”

Tidak ada instruksi `sw`/`sh` ke `0x00451402` (NTSC) atau `0x00451682` (PAL).
Yang ditulis hanya `0x011A(at)`, yaitu field rate-of-fire di struct player.
Tidak ada D-code yang membaca tombol, karena paket ini menghapus toggle
`D0451402` dari kode sumber dan menulis flag `1` secara permanen.

---

## 8. Pemeriksaan input controller

### 8.1 Hasil pencarian

| Pemeriksaan | Hasil |
|---|---|
| Alamat `0x00451402` (NTSC pad) | Tidak ada |
| Alamat `0x00451682` (PAL pad) | Tidak ada |
| Alamat `0x0045xxxx` lainnya | Tidak ada |
| D-code (`D...`) | Tidak ada |
| E-code (`E...`) | Tidak ada |
| `sw`/`sh` ke register input | Tidak ada |
| Toggle tombol Rapid Fire (`D0451402`) | Tidak disertakan; flag ditulis `1` |

### 8.2 Alamat yang sering disalahartikan

| Alamat | Sebenarnya | Bukan |
|---|---|---|
| `0042DA8A` / `0042DD0A` | character ID | input |
| `0042DAFF` / `0042DD7F` | Ashley present flag | input |
| `2012ACF8` / `2012ACFC` | logika belanja | input |
| `101DE596` / `101DE816` | timer QTE | input |
| `200C0294` | rate-of-fire field | input |
| `00430F34` dst. | statistik Results | input |

### 8.3 Kemungkinan penyebab gejala di luar cheat

Jika gangguan tetap muncul dengan semua cheat dimatikan:
- mapping/deadzone controller di AetherSX2/PCSX2;
- analog drift pada controller fisik;
- save state dibuat saat cheat karakter aktif, lalu dimuat tanpa cheat;
- patch widescreen/No-Interlacing dari sumber lain yang memakai code cave
  atau alamat yang sama;
- versi game/CRC berbeda dari `013E349D`/`6BA2F6B9`.

---

## 9. Konflik dan tabrakan alamat

### 9.1 Rapid Fire vs flag

| Area | Alamat | Ukuran | Catatan |
|---|---|---|---|
| Flag | `0x000C0218`–`0x000C021B` | 4 byte | ditulis 16-bit |
| Cave | `0x000C0220`–`0x000C029F` | 128 byte | tidak overlap |
| Jarak | `0x000C021C`–`0x000C021F` | 4 byte | gap |

Tidak ada tabrakan byte antara flag dan cave. Namun flag ditulis 16-bit
sedangkan cave membaca 32-bit; lihat bagian 5.3.

### 9.2 Health vs character/costume

| Field | Alamat NTSC | Alamat PAL |
|---|---|---|
| Health | `0x0042DA76`–`0x0042DA7D` | `0x0042DCF6`–`0x0042DCFD` |
| Character | `0x0042DA8A` | `0x0042DD0A` |
| Costume slot 1 | `0x0042DA8B` | `0x0042DD0B` |
| Costume slot 2 | `0x0042DA8D` | `0x0042DD0D` |
| Ashley present | `0x0042DAFF` | `0x0042DD7F` |

Health dan character/costume berada di struct yang sama tetapi offset berbeda.
Menulis health tidak menimpa character ID; menulis character ID tidak menimpa
health. Namun mengganti karakter tetap berisiko karena layout model/animation
yang berbeda, bukan karena tabrakan alamat.

### 9.3 Money

| Varian | NTSC | PAL |
|---|---|---|
| Tidak mengurangi | `2012ACFC` | `2012AF7C` |
| Menambah | `2012ACF8` | `2012AF78` |
| Jual max (1) | `2012A90C` | `2012AB8C` |
| Jual max (2) | `2012A910` | `2012AB90` |

`2012ACF8` dan `2012ACFC` tidak overlap byte (selisih 4), tetapi secara logika
saling menimpa karena berada di routine yang sama. MAX hanya memakai satu.
`_EXTRA` memakai varian “menambah”; jangan menggabungkannya dengan file utama
yang memakai varian “tidak mengurangi”.

### 9.4 Results / Mercenaries

Tidak ada overlap dengan health, character, atau input. Semua berada di
blok statistik/flag terpisah.

---

## 10. Kompatibilitas USA vs PAL

### 10.1 Prinsip konversi

Mayoritas alamat statis bergeser `+0x280` dari NTSC ke PAL. Code cave Rapid
Fire berada di alamat yang sama (`0x200C0220`–`0x200C029F`) karena ditulis ke
EE RAM, bukan ke segmen kode game. Yang berbeda adalah **nilai pointer** yang
dimuat cave:

| Komponen | NTSC | PAL |
|---|---|---|
| Pointer player | `8C2103B0` → `[0x004203B0]` | `8C210630` → `[0x00420630]` |
| Pointer weapon | `8C420FA8` → `[0x00430FA8]` | `8C421228` → `[0x00431228]` |
| Hook | `201672EC` | `2016756C` |
| Offset rate-of-fire | `+0x11A` | `+0x11A` (sama) |

### 10.2 Tabel delta utama

| Kode | NTSC | PAL | Delta |
|---|---|---|---|
| Health Leon | `1042DA76` | `1042DCF6` | `+0x280` |
| Ammo | `201200AC` | `2012032C` | `+0x280` |
| QTE | `101DE596` | `101DE816` | `+0x280` |
| Max Pesetas | `2042DA6C` | `2042DCEC` | `+0x280` |
| Belanja | `2012ACFC` | `2012AF7C` | `+0x280` |
| Unlock Everything | `10430F66` | `104311E6` | `+0x280` |
| Mercenaries Time | `2041B668` | `2041B8E8` | `+0x280` |
| Results | `00430F34` | `004311B4` | `+0x280` |
| Character | `0042DA8A` | `0042DD0A` | `+0x280` |
| Costume slot 1 | `0042DA8B` | `0042DD0B` | `+0x280` |
| Ashley present | `0042DAFF` | `0042DD7F` | `+0x280` |
| 1-hit kill | `201C2A9C` | `201C2D1C` | `+0x280` |
| Super health 1 | `10246F36` | `102471B6` | `+0x280` |
| Super health 2 | `10163C3A` | `10163EBA` | `+0x280` |
| Leon–Ashley | `20163DF0` | `20164070` | `+0x280` (**tidak terverifikasi**) |
| Krauser | `2086C264` | — | tidak ada padanan PAL |

### 10.3 Hasil pemeriksaan pencampuran

- Main PAL memakai `8C210630` dan `8C421228`, bukan pointer NTSC.
- MAX PAL memakai pointer PAL yang sama.
- Tidak ada file PAL yang memakai `201672EC` (hook NTSC) atau `8C2103B0`.
- Tidak ada file NTSC yang memakai `2016756C` atau `8C210630`.
- Semua alamat non-cave mengikuti delta `+0x280`.
- `20164070` adalah satu-satunya alamat PAL hasil port yang tidak ditemukan
  di sumber PAL; karena itu dinonaktifkan.
- Kode Krauser NTSC `2086C264` tidak dimasukkan ke PAL.

---

## 11. Perbaikan yang diterapkan

| File | Perubahan | Alasan teknis |
|---|---|---|
| `NTSC-U-USA/013E349D.pnach` | Tambah `200C029C 00000000`; komentar audit | Delay slot `jr ra` |
| `PAL-EUROPE/6BA2F6B9.pnach` | Tambah `200C029C 00000000`; komentar pointer PAL | Delay slot + dokumentasi |
| `NTSC-U-USA/OPTIONAL/013E349D_MAX.pnach` | Tambah NOP; koreksi label `20163DF0`; tambah `2086C264`; nonaktifkan `2012ACFC` | Delay slot, mislabel, fitur Krauser, konflik uang |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_MAX.pnach` | Tambah NOP; koreksi label; nonaktifkan `20164070`; nonaktifkan `2012AF7C` | Delay slot, port PAL tidak terverifikasi, konflik uang |
| `NTSC-U-USA/OPTIONAL/013E349D_KARAKTER.pnach` | Blok peringatan kontrol; label per baris | Risiko karakter/kamera |
| `PAL-EUROPE/OPTIONAL/6BA2F6B9_KARAKTER.pnach` | Blok peringatan kontrol; label per baris | Risiko karakter/kamera |
| `README.md` | Bagian audit, Rapid Fire, MAX, KARAKTER, troubleshooting | Dokumentasi |
| `AUDIT.md` | Dokumen ini | Bukti dan analisis |

Tidak ada perubahan pada:
- `_EXTRA` NTSC/PAL — semua kode aman kontrol dan tidak memakai Rapid Fire.
- `_NO-RAPIDFIRE` NTSC/PAL — sudah bebas dari code cave Rapid Fire.

---

## 12. Validasi akhir

| Pemeriksaan | Hasil |
|---|---|
| Format semua baris `patch=` | Lolos regex `^patch=1,EE,[0-9A-F]{8},extended,[0-9A-F]{8}$` |
| Duplikat alamat dalam satu file | Tidak ada |
| D-code / E-code | Tidak ada |
| Alamat `0x0045xxxx` | Tidak ada |
| Delay slot `200C029C` | Ada di 4 file Rapid Fire |
| Main NTSC/PAL aktif | 57 / 57 |
| MAX NTSC/PAL aktif | 72 / 70 |
| EXTRA NTSC/PAL aktif | 9 / 9 |
| NO-RAPIDFIRE NTSC/PAL aktif | 23 / 23 |
| KARAKTER aktif | 0 / 0 (16 baris komentar per region) |
| Pointer PAL | `8C210630` / `8C421228` |
| Pointer NTSC | `8C2103B0` / `8C420FA8` |
| Encoding | UTF-8/ASCII, LF, tanpa BOM |
| Uji emulator | **Belum dilakukan** (tidak ada ISO/save state) |

---

## 13. Rekomendasi pemakaian

1. **Mulai dari file utama** (`013E349D.pnach` atau `6BA2F6B9.pnach`) tanpa
   modul opsional. Uji gerakan, analog kiri/kanan, aiming, shooting, kamera,
   dan QTE selama minimal satu chapter.
2. Jika muncul gejala kontrol, **jangan langsung menuduh semua cheat**.
   Urutan isolasi:
   - ganti ke `_NO-RAPIDFIRE`;
   - jika masih, matikan semua cheat dan uji controller;
   - jika hanya muncul saat `_KARAKTER`, kembalikan ke Leon ID `0`.
3. **Jangan gabung** `_KARAKTER` dengan `_MAX`, 1-hit kill, atau unlock pintu.
4. **MAX** hanya setelah backup memory card/save state. 1-hit kill dan unlock
   pintu bisa melewati event.
5. **PAL MAX**: kode Leon–Ashley `20164070` dinonaktifkan; tidak ada Krauser
   PAL. Jangan mengaktifkan baris yang dikomentari tanpa disassembly PAL.
6. **Rapid Fire**: jika ingin efek paling konservatif, pakai `_NO-RAPIDFIRE`.
   Jika memakai Rapid Fire, pastikan file sudah memiliki NOP `200C029C`.
7. **Simpan satu file per CRC.** AetherSX2 hanya memuat satu `<CRC>.pnach`;
   rename modul opsional, jangan menaruh dua file dengan CRC sama.
8. **Rotasi token GitHub** yang pernah terekspos di chat. Ini di luar kode
   cheat, tetapi penting untuk keamanan repository.

---

## 14. Sumber

- GameHacking.org — Resident Evil 4 (NTSC-U), game `104753`
  <https://gamehacking.org/game/104753>
- GameHacking.org — Resident Evil 4 (PAL), game `102398`
  <https://gamehacking.org/game/102398>
- PS2-HOME — thread cheat Resident Evil 4 SLUS-21134 (Rapid Fire)
  <https://www.ps2-home.com/forum/viewtopic.php?t=2657>
- PS2-HOME — thread cheat Resident Evil 4 SLES-53702 (PAL)
  <https://www.ps2-home.com/forum/viewtopic.php?t=3834>
- Daftar kode PAL mentah SLES-53702 (arsip komunitas) — health, ammo, QTE,
  uang, unlock, Mercenaries, Results, character/costume.
- PCSX2 — dokumentasi format pnach `extended` dan perilaku D-code.
- PCSX2 Wiki — serial dan CRC Resident Evil 4
  <https://wiki.pcsx2.net/Resident_Evil_4>

---

**Catatan akhir:** audit ini tidak menghapus fitur yang masih valid. Rapid Fire
tetap aktif, cheat lain tetap berfungsi, dan file USA/PAL tetap terpisah.
Satu-satunya kode yang dinonaktifkan adalah port PAL `20164070` yang tidak
memiliki sumber verifikasi, serta varian belanja yang redundan di MAX.
