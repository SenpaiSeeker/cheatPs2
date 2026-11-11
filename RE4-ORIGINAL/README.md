# RE4-ORIGINAL — Cheat Pack Resident Evil 4 (PS2) untuk AetherSX2 / PCSX2

Paket kode cheat Resident Evil 4 versi **PlayStation 2** untuk region **USA (NTSC-U)** dan **Eropa (PAL)**.
Format `.pnach`, langsung bisa dipakai di **AetherSX2** (Android) maupun **PCSX2** (PC).

Isi paket:

- Darah penuh + unlimited untuk **Leon dan Ashley** (slot 1 & slot 2)
- **Ammo unlimited** semua senjata + grenade/rocket
- **Rapid fire** untuk semua senjata (ASM hook rate-of-fire)
- **Auto QTE** (timer button masher tidak pernah habis → QTE tidak bisa gagal)
- Uang maksimal + belanja gratis
- Unlock semua karakter Mercenaries, mode, costume, dan senjata bonus
- Mercenaries: waktu unlimited, skor/kill/combo maksimal
- Statistik Results: 0 mati, perfect accuracy, kill maksimal
- Modul opsional: 1-hit kill, super health, unlock pintu, ganti karakter, dll.

> **Audit 2026-11-11:** seluruh file `.pnach`, folder `OPTIONAL/`, dan README
> sudah diaudit statis. Temuan utama: **delay slot Rapid Fire yang hilang sudah
> diperbaiki**, label `20163DF0` dikoreksi (itu kode Leon–Ashley, bukan Krauser),
> port PAL `20164070` yang tidak terverifikasi dinonaktifkan, dan peringatan
> kontrol untuk `_KARAKTER` ditambahkan. Tidak ada patch yang menulis langsung
> ke register input controller. Detail lengkap: [`AUDIT.md`](AUDIT.md).

---

## 1. Tabel region & CRC

| Region | Serial | CRC | File cheat |
|---|---|---|---|
| USA (NTSC-U) | SLUS-21134 | `013E349D` | `NTSC-U-USA/013E349D.pnach` |
| Europe (PAL) | SLES-53702 | `6BA2F6B9` | `PAL-EUROPE/6BA2F6B9.pnach` |

> Nama file **wajib** sama dengan CRC game. Kalau CRC game kamu berbeda, cheat tidak akan terbaca.
> Versi PAL lain (mis. SLES-53756 Jerman) punya CRC dan alamat berbeda, jadi **tidak** dicakup paket ini.

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

2. Pastikan nama file tetap `013E349D.pnach` (USA) atau `6BA2F6B9.pnach` (PAL).

3. Buka AetherSX2 → **Settings → System → Enable Cheats** (nama menu bisa sedikit berbeda tergantung versi).
   Di beberapa versi, opsi ini ada di **Game Properties → Enable Cheats**.

4. Jalankan game. Kalau berhasil, efek langsung terasa saat masuk gameplay
   (bar darah penuh, ammo tidak berkurang, dan seterusnya).

Contoh struktur akhir:

```
.../files/cheats/013E349D.pnach
```

### PCSX2 (PC)

Salin ke folder `cheats` PCSX2:

```
Documents/PCSX2/cheats/013E349D.pnach
```

Lalu aktifkan **System → Enable Cheats** dan centang cheat di **Game Properties → Cheats**.

---

## 3. Isi kode (file utama)

### USA — `013E349D.pnach`

| Kode | Alamat | Efek |
|---|---|---|
| Infinite Health Leon | `1042DA76` | HP Leon selalu 0x960 (bar penuh) |
| Max Health Leon | `1042DA78` | Max HP Leon 0x960 |
| Infinite Health Ashley | `1042DA7A` | HP Ashley selalu 0x4B0 |
| Max Health Ashley | `1042DA7C` | Max HP Ashley 0x4B0 |
| Infinite Ammo | `201200AC` | Amunisi tidak berkurang |
| Infinite Grenades/RPG | `2011EF30` + `1011EF3E` | Grenade & rocket tidak habis |
| Rapid Fire | ASM `000C0220`–`000C029F` + hook `201672EC` | Rate-of-fire = 2; delay slot `200C029C` = NOP |
| Auto QTE | `101DE596` | Timer mashing QTE tidak habis |
| Max Pesetas | `2042DA6C` | 99.999.999 |
| Belanja gratis | `2012ACFC` | Pesetas tidak berkurang saat beli |
| Unlock Everything | `10430F66` | Semua karakter/mode/costume/senjata bonus |
| Ada's Reports | `00430F6D` | Semua laporan Ada |
| Mercenaries Time | `2041B668` | Waktu tidak habis |
| Mercenaries Score/Kill/Combo | `203CFA6C`, `203CFA84`, `203CFA70` | Nilai maksimal |
| Statistik Results | `00430F34` dst. | 0 mati, accuracy 100%, kill maksimal |

### Eropa — `6BA2F6B9.pnach`

Isi sama, alamat memakai basis PAL:

| Kode | Alamat PAL |
|---|---|
| Infinite Health Leon | `1042DCF6` |
| Max Health Leon | `1042DCF8` |
| Infinite Health Ashley | `1042DCFA` |
| Max Health Ashley | `1042DCFC` |
| Infinite Ammo | `2012032C` |
| Infinite Grenades/RPG | `2011F1B0` + `1011F1BE` |
| Rapid Fire hook | `2016756C` |
| Auto QTE | `101DE816` |
| Max Pesetas | `2042DCEC` |
| Belanja gratis | `2012AF7C` |
| Unlock Everything | `104311E6` |
| Ada's Reports | `004311ED` |
| Mercenaries Time | `2041B8E8` |

> Konversi NTSC → PAL memakai selisih tetap **+0x280** pada alamat statis.
> Pola ini sudah diverifikasi silang dengan daftar kode PAL komunitas
> (health, ammo, QTE, uang, unlock, Mercenaries) — semuanya cocok.
>
> **Rapid Fire:** code cave berakhir di `0x200C0298` (`jr ra`). Audit menemukan
> bahwa delay slot di `0x200C029C` belum ditulis. Tanpa instruksi NOP eksplisit,
> CPU mengeksekusi sisa data di lokasi itu; ini bisa merusak register/state dan
> menjelaskan gangguan kontrol yang muncul kadang-kadang. Semua file yang masih
> memakai Rapid Fire sekarang menulis `200C029C 00000000`.
>
> **PAL:** pointer player memakai `[0x00420630]` dan weapon ID `[0x00431228]`.
> Ini bukan alamat NTSC; jangan menukar baris `8C210630`/`8C421228` dengan
> `8C2103B0`/`8C420FA8`.

---

## 4. Modul opsional (`OPTIONAL/`)

AetherSX2 hanya memuat **satu** file per CRC, yaitu `<CRC>.pnach`.
Untuk memakai modul opsional, **rename** file modul menjadi `<CRC>.pnach`
dan simpan file utama sebagai cadangan.

| File | Isi |
|---|---|
| `<CRC>_NO-RAPIDFIRE.pnach` | Semua kode inti tanpa Rapid Fire (kalau rapid fire bikin crash) |
| `<CRC>_MAX.pnach` | Kode inti + super health hook, 1-hit kill, unlock pintu, statistik waktu, kode Leon–Ashley, Krauser (NTSC) |
| `<CRC>_KARAKTER.pnach` | Ganti karakter (Leon/Ashley/Ada/Hunk/Krauser/Wesker) & costume — **berisiko kontrol** |
| `<CRC>_EXTRA.pnach` | Ammo ekstra (max clip, never reload), uang ekstra, waktu save |

> **MAX PAL:** kode `20164070` (label lama “Krauser”) adalah port `+0x280` dari
> kode NTSC `20163DF0` (Leon’s Attacks Can’t Hurt/Kill Ashley). Alamat PAL itu
> tidak ditemukan di daftar kode PAL resmi, jadi **dinonaktifkan** di file PAL.
> Kode Krauser NTSC `2086C264 24020000` hanya ada di MAX USA; tidak ada alamat
> PAL terverifikasi, jadi jangan dipaksakan ke PAL.
>
> **MAX money:** `2012ACFC` (belanja tidak mengurangi) dan `2012ACF8` (belanja
> menambah) berada di routine yang sama dan saling menimpa. Di MAX hanya varian
> “menambah” yang aktif; varian “tidak mengurangi” tetap ada di file utama.
>
> **KARAKTER:** hanya Leon (ID `0`) yang aman untuk campaign normal. Ashley,
> Ada, Hunk, Krauser, dan Wesker memakai model/animasi/kamera yang berbeda;
> memaksakannya bisa membuat karakter bergerak sendiri, kamera liar, analog
> tidak responsif, aiming/QTE rusak, atau freeze saat cutscene.

Contoh: untuk memakai versi MAX di USA:

```
013E349D.pnach          -> rename jadi 013E349D_BACKUP.pnach
013E349D_MAX.pnach      -> rename jadi 013E349D.pnach
```

---

## 5. Catatan penting

- **Backup memory card / save state** sebelum memakai cheat, terutama `_MAX`
  dan `_KARAKTER`. Kode unlock pintu dan 1-hit kill bisa melewati event cerita.
- **Rapid Fire** adalah ASM hook yang ditulis ke code cave `0x000C0220`–
  `0x000C029F` lalu di-hook dari fungsi rate-of-fire. Flag `0x000C0218`
  dipaksa `1` supaya berlaku untuk semua senjata, bukan hanya handgun.
  Audit menemukan `jr ra` di `0x200C0298` tanpa delay slot; semua file yang
  memakai Rapid Fire sekarang menulis NOP di `0x200C029C`. Versi PAL memakai
  pointer `0x00420630`/`0x00431228` dan **belum diuji seluas NTSC** — kalau
  masih bermasalah, pakai `_NO-RAPIDFIRE`.
- **Tidak ada patch yang menulis langsung ke input controller** (`0x00451402`
  NTSC / `0x00451682` PAL), dan tidak ada D-code/E-code tombol di paket ini.
  Kalau karakter bergerak sendiri atau kamera liar, penyebab yang perlu dicek
  adalah `_KARAKTER` (model/animasi/kamera) atau code cave yang korup karena
  file lama tanpa delay slot — bukan tombol yang dipaksa oleh cheat.
- **1-hit kill** (`201C2A9C` NTSC / `201C2D1C` PAL) bisa ikut mengenai player
  di beberapa event. Pakai hanya di `_MAX` dan setelah backup save.
- **Unlock Everything** menulis flag di data save. Kalau save sudah terlanjur
  rusak/aneh, kembalikan dari backup.
- **KARAKTER** bukan cheat kosmetik biasa. Hanya Leon (ID `0`) yang aman untuk
  campaign normal. Jangan aktifkan lebih dari satu baris karakter, dan jangan
  gabung dengan `_MAX`, 1-hit kill, atau unlock pintu. Untuk Mercenaries,
  ganti karakter lewat menu game.
- **MAX money:** jangan aktifkan `2012ACFC` dan `2012ACF8` bersamaan. Di file
  MAX hanya varian “menambah” yang aktif; varian “tidak mengurangi” ada di
  file utama. Aturan yang sama berlaku untuk `_EXTRA` + file utama: pilih
  salah satu, jangan digabung.
- Jangan mengaktifkan dua file `.pnach` untuk CRC yang sama.
- Beberapa kode (mis. Max Infinite Ammo, Equip Weapon = Max Ammo) saling
  menimpa efek. Semua sudah dipisahkan ke modul `_EXTRA` supaya tidak bentrok.

---

## 6. Troubleshooting

| Masalah | Penyebab / solusi |
|---|---|
| Cheat tidak terbaca | Nama file bukan CRC game, folder salah, atau Enable Cheats belum aktif |
| Semua kode jalan tapi game crash | Coba `_NO-RAPIDFIRE`; pastikan region/CRC benar |
| Darah tetap berkurang | Kode max health & current health harus aktif bersamaan; cek save state lama |
| Rapid fire tidak terasa | Senjata yang dipakai bukan senjata api, atau hook di-patch versi game lain |
| Karakter bergerak sendiri / kamera liar | Cek `_KARAKTER` lebih dulu (Ashley/Ada/Hunk/Krauser/Wesker). Kembalikan ke Leon ID `0` atau pakai file utama tanpa KARAKTER |
| Analog kiri/kanan tidak responsif | Sama seperti di atas; pastikan juga file Rapid Fire sudah punya NOP `200C029C` (versi lama belum) |
| Aiming/shooting/QTE rusak | Jangan gabung KARAKTER dengan MAX/1-hit kill; pastikan Auto QTE memakai `101DE596` (NTSC) / `101DE816` (PAL) |
| Tombol seperti ditekan sendiri | Tidak ada D/E-code tombol di paket ini; cek mapping/deadzone controller emulator dan coba save state baru |
| Game freeze saat masuk Mercenaries | Matikan kode statistik/Mercenaries, lalu aktifkan satu per satu |

---

## 7. Sumber & kredit

Kode dikumpulkan dari arsip komunitas PS2:

- PS2-HOME — thread cheat Resident Evil 4 SLUS-21134 (termasuk kode Rapid Fire)
  <https://www.ps2-home.com/forum/viewtopic.php?t=2657>
- GameHacking.org — database kode NTSC-U dan PAL
  <https://gamehacking.org/game/104753> · <https://gamehacking.org/game/102398>
- PCSX2 Forum — thread cheats/patch dan pnach Resident Evil 4
  <https://forums.pcsx2.net/Thread-Post-your-PCSX2-cheats-patches-here>
- Daftar kode PAL komunitas untuk SLES-53702
- PCSX2 Wiki — CRC & serial resmi
  <https://wiki.pcsx2.net/Resident_Evil_4>

Paket ini hanya merapikan, menerjemahkan, dan menggabungkan kode-kode tersebut
ke format `.pnach` yang siap pakai, plus port PAL berbasis offset +0x280.

Audit 2026-11-11 bersifat **statis**: format pnach, decode MIPS, pengecekan
alamat/tabrakan, dan cross-check sumber. Tidak ada ISO/save state Resident
Evil 4 di lingkungan audit, jadi klaim “sudah diuji di emulator” tidak dibuat.
Kode yang aman secara kontrol tetap ditandai `AMAN`; kode yang belum cukup
bukti ditandai `PERLU PENGUJIAN`.

---

## 8. Struktur repository

```
RE4-ORIGINAL/
├── README.md
├── AUDIT.md
├── NTSC-U-USA/
│   ├── 013E349D.pnach
│   └── OPTIONAL/
│       ├── 013E349D_NO-RAPIDFIRE.pnach
│       ├── 013E349D_MAX.pnach
│       ├── 013E349D_KARAKTER.pnach
│       └── 013E349D_EXTRA.pnach
└── PAL-EUROPE/
    ├── 6BA2F6B9.pnach
    └── OPTIONAL/
        ├── 6BA2F6B9_NO-RAPIDFIRE.pnach
        ├── 6BA2F6B9_MAX.pnach
        ├── 6BA2F6B9_KARAKTER.pnach
        └── 6BA2F6B9_EXTRA.pnach
```

Selamat bermain. Kalau ada kode yang tidak jalan di versi game tertentu,
kirim CRC + region-nya supaya bisa dipetakan.
