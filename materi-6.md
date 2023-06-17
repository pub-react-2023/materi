# **Materi 5: Memperbarui _Array_ dalam _State_**

# Silabus

| Pertemuan | Materi                                                             |
| --------- | ------------------------------------------------------------------ |
| 1         | JavaScript, Node.js, `npm`, dan `pnpm`                             |
| 2         | React, Vite, Komponen, JSX, dan Meneruskan _Props_                 |
| 3         | _Styling_, _Conditional & List Rendering_, dan Merespons _Event_   |
| 4         | Konsep _State_ dalam React                                         |
| 5         | Memperbarui Objek dalam _State_                                    |
| 6         | Memperbarui _Array_ dalam _State_                                  |
| 7         | _Sharing_ Data Antarkomponen Menggunakan Props                     |
| 8         | _Routing_ Menggunakan React Router dan _Deployment_ (Vercel)       |
| **9**     | **UTS**                                                            |
| 10        | Tailwind CSS                                                       |
| 11        | Memperdalam event dan `useState()` (memperbarui objek dan _array_) |
| 12        | `useReducer()` dan `useContext()`                                  |
| 13        | `useRef()` dan `useEffect()`                                       |
| 14        | REST API (Express), CRUD, dan deployment (Vercel)                  |
| 15        | Basis data (Supabase, PostgreSQL) dan `.env`                       |
| 16        | Otentikasi (JWT, cookies, dan hash kata sandi)                     |
| 17        | TypeScript                                                         |
| **18**    | **Projekan**                                                       |

# Daftar isi

- [**Materi 5: Memperbarui _Array_ dalam _State_**](#materi-5-memperbarui-array-dalam-state)
- [Silabus](#silabus)
- [Daftar isi](#daftar-isi)
- [Memperbarui _Array_ dalam _State_](#memperbarui-array-dalam-state)
  - [Memperbarui _array_ tanpa mutasi](#memperbarui-array-tanpa-mutasi)
    - [Menambahkan ke _array_](#menambahkan-ke-array)
    - [Menghapus dari _array_](#menghapus-dari-array)
    - [Mengubah _array_](#mengubah-array)
    - [Mengganti item dalam _array_](#mengganti-item-dalam-array)
    - [Memasukkan ke dalam _array_](#memasukkan-ke-dalam-array)
    - [Membuat perubahan lain pada _array_](#membuat-perubahan-lain-pada-array)
  - [Memperbarui objek di dalam _array_](#memperbarui-objek-di-dalam-array)
    - [Tulis logika pembaruan yang ringkas dengan Immer](#tulis-logika-pembaruan-yang-ringkas-dengan-immer)

# Memperbarui _Array_ dalam _State_

_Array_ dapat berubah dalam JavaScript, tetapi kita harus memperlakukannya sebagai tidak dapat diubah saat kita menyimpannya dalam _state_. Sama seperti objek, saat kita ingin memperbarui _array_ yang disimpan dalam _state_, kita perlu membuat yang baru (atau membuat salinan dari yang sudah ada), lalu menyetel _state_ untuk menggunakan _array_ baru.

Kita akan belajar:

- Cara menambah, menghapus, atau mengubah item dalam _array_ dalam _state_ React
- Cara memperbarui objek di dalam _array_
- Cara membuat penyalinan _array_ tidak terlalu berulang dengan Immer

## Memperbarui _array_ tanpa mutasi

Dalam JavaScript, _array_ hanyalah jenis objek lainnya. [Seperti objek](https://react.dev/learn/update-objects-in-state), **kita harus memperlakukan _array_ dalam _state_ React sebagai _read_only_.** Ini berarti kita tidak boleh menugaskan ulang item di dalam _array_ seperti `arr[ 0] = 'bird'`, dan kita juga tidak boleh menggunakan metode yang mengubah _array_, seperti `push()` dan `pop()`.

Alih-alih, setiap kali kita ingin memperbarui _array_, kita harus meneruskan _array_ _baru_ ke fungsi pengaturan _state_ kita. Untuk melakukannya, kita bisa membuat _array_ baru dari _array_ asli di _state_ kita dengan memanggil metode non-mutasi seperti `filter()` dan `map()`. Kemudian kita dapat mengatur _state_ kita ke _array_ baru yang dihasilkan.

Berikut adalah tabel referensi operasi _array_ umum. Saat berurusan dengan _array_ di dalam _state_ React, kita harus menghindari metode di kolom kiri, dan memilih metode di kolom kanan:

|             | hindari (mutasi _array_)             | prefer (mengembalikan _array_ baru)                                          |
| ----------- | ------------------------------------ | ---------------------------------------------------------------------------- |
| menambahkan | `tekan`, `lepaskan`                  | `concat`, `[...arr]` menyebar sintaks ([contoh](#menambah-to-an-_array_))    |
| menghapus   | `pop`, `shift`, `sambatan`           | `filter`, `slice` ([contoh](#menghapus-dari-_array_))                        |
| mengganti   | `sambatan`, `arr[i] = ...` penugasan | `peta` ([contoh](#mengganti-item-dalam-_array_))                             |
| menyortir   | `terbalik`, `urutkan`                | salin _array_ terlebih dahulu ([contoh](#membuat-lain-perubahan-ke-_array_)) |

Atau, kita dapat [menggunakan Immer](#write-concise-update-logic-with-immer) yang memungkinkan kita menggunakan metode dari kedua kolom.

<blockquote>

**Peringatan**

Walaupun [`slice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/_array_/slice) dan [`splice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) diberi nama serupa tetapi sangat berbeda:

- `slice` memungkinkan kita menyalin _array_ atau sebagian darinya.
- `splice` **bermutasi** _array_ (untuk menyisipkan atau menghapus item).

Di React, kita akan lebih sering menggunakan `slice` (tanpa `p`!) karena kita tidak ingin mengubah objek atau _array_ dalam _state_. [Memperbarui Objek](https://react.dev/learn/memperbarui-objek-dalam-_state_) menjelaskan apa itu mutasi dan mengapa tidak direkomendasikan untuk _state_.

</blockquote>

### Menambahkan ke _array_

`push()` akan mengubah _array_, yang tidak kita inginkan:

```js
import { useState } from "react";

let nextId = 0;

export default function List() {
  const [name, setName] = useState("");
  const [artists, setArtists] = useState([]);

  return (
    <>
      <h1>Inspiring sculptors:</h1>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button
        onClick={() => {
          artists.push({
            id: nextId++,
            name: name,
          });
        }}
      >
        Add
      </button>
      <ul>
        {artists.map((artist) => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

Alih-alih, buat _array_ _baru_ yang berisi item yang sudah ada _dan_ item baru di bagian akhir. Ada beberapa cara untuk melakukannya, tetapi yang paling mudah adalah menggunakan sintaks [_array spread_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals) `...`:

```js
setArtists(
  // Ganti _state_
  [
    // dengan _array_ baru
    ...artis, // yang berisi semua item lama
    { id: nextId++, name: name }, // dan satu item baru di akhir
  ]
);
```

Sekarang berfungsi dengan benar:

```js
impor { useState } dari 'react';

biarkan nextId = 0;

ekspor fungsi default Daftar () {
  const [nama, setName] = useState('');
  const [artis, setArtis] = useState([]);

  kembali (
    <>
      <h1>Sculptor yang menginspirasi:</h1>
      <masukan
        nilai={nama}
        onChange={e => setName(e.target.nilai)}
      />
      <tombol padaKlik={() => {
        setArtis([
          ...artis,
          { id: nextId++, nama: nama }
        ]);
      }}>Tambahkan</button>
      <ul>
        {artis.peta(artis => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

Sintaks penyebaran _array_ juga memungkinkan kita menambahkan item dengan menempatkannya _sebelum_ `...artists` asli:

```js
setArtis([
  { id: nextId++, nama: nama },
  ...artis, // Taruh item lama di akhir
]);
```

Dengan cara ini, spread dapat melakukan tugas `push()` dengan menambahkan ke akhir _array_ dan `unshift()` dengan menambahkan ke awal _array_. Cobalah di kotak pasir di atas!

### Menghapus dari _array_

Cara termudah untuk menghapus item dari _array_ adalah dengan _memfilternya_. Dengan kata lain, kita akan menghasilkan _array_ baru yang tidak berisi item tersebut. Untuk melakukannya, gunakan metode `filter`, misalnya:

```js
impor { useState } dari 'react';

biarkan initialArtists = [
  { id: 0, nama: 'Marta Colvin Andrade' },
  { id: 1, nama: 'Lamidi Olonade Fakeye'},
  { id: 2, nama: 'Louise Nevelson'},
];

ekspor fungsi default Daftar () {
  const [artis, setArtis] = useState(
    initialArtists
  );

  kembali (
    <>
      <h1>Sculptor yang menginspirasi:</h1>
      <ul>
        {artis.peta(artis => (
          <li key={artist.id}>
            {nama artis}{' '}
            <tombol padaKlik={() => {
              setArtis(
                artis.filter(a =>
                  a.id !== artis.id
                )
              );
            }}>
              Menghapus
            </tombol>
          </li>
        ))}
      </ul>
    </>
  );
}
```

Klik tombol "Hapus" beberapa kali, dan lihat _handler_ kliknya.

```js
setArtis(artis.filter((a) => a.id !== artis.id));
```

Di sini, `artists.filter(a => a.id !== artist.id)` berarti "buat _array_ yang terdiri dari `artists` yang ID-nya berbeda dari `artist.id`". Dengan kata lain, tombol "Hapus" setiap artis akan memfilter _artis_ itu dari _array_, lalu meminta _render_ ulang dengan _array_ yang dihasilkan. Perhatikan bahwa `filter` tidak mengubah _array_ asli.

### Mengubah _array_

Jika kita ingin mengubah beberapa atau semua item _array_, kita dapat menggunakan `map()` untuk membuat _array_ **baru**. Fungsi yang akan kita berikan ke `peta` dapat memutuskan apa yang harus dilakukan dengan setiap item, berdasarkan datanya atau indeksnya (atau keduanya).

Dalam contoh ini, sebuah _array_ menyimpan koordinat dua lingkaran dan sebuah persegi. Saat kita menekan tombol, itu hanya memindahkan lingkaran ke bawah sebanyak 50 piksel. Ini dilakukan dengan membuat _array_ data baru menggunakan `map()`:

```js
impor { useState } dari 'react';

biarkan bentuk awal = [
  { id: 0, ketik: 'lingkaran', x: 50, y: 100 },
  { id: 1, ketik: 'persegi', x: 150, y: 100 },
  { id: 2, ketik: 'lingkaran', x: 250, y: 100 },
];

ekspor fungsi default ShapeEditor() {
  const [bentuk, setShapes] = useState(
    initialShapes
  );

  fungsi handleClick() {
    const nextShapes = bentuk.peta(bentuk => {
      if (shape.type === 'persegi') {
        // Tidak ada perubahan
        bentuk kembali;
      } kalau tidak {
        // Kembalikan lingkaran baru 50px di bawah
        kembali {
          ...membentuk,
          y: bentuk.y + 50,
        };
      }
    });
    // _render_ ulang dengan _array_ baru
    setShapes(nextShapes);
  }

  kembali (
    <>
      <button onClick={handleClick}>
        Pindahkan lingkaran ke bawah!
      </tombol>
      {bentuk.peta(bentuk => (
        <div
          kunci={bentuk.id}
          gaya={{
          latar belakang: 'ungu',
          posisi: 'absolut',
          kiri: bentuk.x,
          atas: shape.y,
          perbatasanRadius:
            bentuk.jenis === 'lingkaran'
              ? '50%' : '',
          lebar: 20,
          tinggi: 20,
        }} />
      ))}
    </>
  );
}
```

### Mengganti item dalam _array_

Sangat umum untuk ingin mengganti satu atau lebih item dalam _array_. Penugasan seperti `arr[0] = 'bird'` memutasi _array_ asli, jadi kita sebaiknya menggunakan `map` untuk ini juga.

Untuk mengganti item, buat _array_ baru dengan `map`. Di dalam panggilan `map` kita, kita akan menerima indeks item sebagai argumen kedua. Gunakan itu untuk memutuskan apakah akan mengembalikan item asli (argumen pertama) atau yang lainnya:

```js
impor { useState } dari 'react';

biarkan penghitung awal = [
  0, 0, 0
];

export default function CounterList() {
  const [penghitung, setCounters] = useState(
    penghitung awal
  );

  fungsi handleIncrementClick(indeks) {
    const nextCounters = penghitung.peta((c, i) => {
      if (i === indeks) {
        // Menaikkan penghitung yang diklik
        kembali c + 1;
      } kalau tidak {
        // Sisanya tidak berubah
        kembali c;
      }
    });
    setCounters(nextCounters);
  }

  kembali (
    <ul>
      {counters.map((penghitung, i) => (
        <li key={i}>
          {menangkal}
          <tombol padaKlik={() => {
            handleIncrementClick(i);
          }}>+1</button>
        </li>
      ))}
    </ul>
  );
}
```

### Memasukkan ke dalam _array_

Terkadang, kita mungkin ingin menyisipkan item pada posisi tertentu yang bukan di awal maupun di akhir. Untuk melakukannya, kita dapat menggunakan sintaks _array_ spread `...` bersama dengan metode `slice()`. Metode `slice()` memungkinkan kita memotong "irisan" dari _array_. Untuk menyisipkan item, kita akan membuat _array_ yang menyebarkan irisan _sebelum_ titik penyisipan, lalu item baru, lalu selebihnya dari _array_ asli.

Dalam contoh ini, tombol Sisipkan selalu menyisipkan pada indeks `1`:

```js
impor { useState } dari 'react';

biarkan nextId = 3;
cons artis awal = [
  { id: 0, nama: 'Marta Colvin Andrade' },
  { id: 1, nama: 'Lamidi Olonade Fakeye'},
  { id: 2, nama: 'Louise Nevelson'},
];

ekspor fungsi default Daftar () {
  const [nama, setName] = useState('');
  const [artis, setArtis] = useState(
    initialArtists
  );

  fungsi handleClick() {
    cons insertAt = 1; // Bisa berupa index
    const Artis berikutnya = [
      // Item sebelum titik penyisipan:
      ...artis.slice(0, insertAt),
      // Barang baru:
      { id: nextId++, nama: nama },
      // Item setelah titik penyisipan:
      ... artis.slice(insertAt)
    ];
    setArtists(nextArtists);
    setNama('');
  }

  kembali (
    <>
      <h1>Sculptor yang menginspirasi:</h1>
      <masukan
        nilai={nama}
        onChange={e => setName(e.target.nilai)}
      />
      <button onClick={handleClick}>
        Menyisipkan
      </tombol>
      <ul>
        {artis.peta(artis => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

### Membuat perubahan lain pada _array_

Ada beberapa hal yang tidak dapat kita lakukan dengan sintaks sebar dan metode non-mutasi seperti `map()` dan `filter()` saja. Misalnya, kita mungkin ingin membalikkan atau mengurutkan _array_. Metode JavaScript `reverse()` dan `sort()` memutasikan _array_ asli, sehingga kita tidak dapat menggunakannya secara langsung.

**Namun, kita dapat menyalin _array_ terlebih dahulu, lalu mengubahnya.**

Misalnya:

```js
impor { useState } dari 'react';

biarkan nextId = 3;
const Daftar awal = [
  { id: 0, title: 'Perut Besar' },
  { id: 1, title: 'Lunar Landscape' },
  { id: 2, title: 'Tentara Terakota' },
];

ekspor fungsi default Daftar () {
  const [daftar, setList] = useState(initialList);

  fungsi handleClick() {
    const Daftar berikutnya = [...daftar];
    nextList.reverse();
    setList(daftarberikutnya);
  }

  kembali (
    <>
      <button onClick={handleClick}>
        Balik
      </tombol>
      <ul>
        {daftar.peta(karya seni => (
          <li key={artwork.id}>{artwork.title}</li>
        ))}
      </ul>
    </>
  );
}
```

Di sini, kita menggunakan sintaks spread `[...list]` untuk membuat salinan _array_ asli terlebih dahulu. Sekarang setelah kita memiliki salinannya, kita dapat menggunakan metode mutasi seperti `nextList.reverse()` atau `nextList.sort()`, atau bahkan menetapkan masing-masing item dengan `nextList[0] = "something"`.

Namun, **bahkan jika kita menyalin _array_, kita tidak dapat mengubah item yang ada _di dalamnya_ secara langsung.** Hal ini karena penyalinan bersifat dangkal--arik baru akan berisi item yang sama dengan yang asli. Jadi jika kita memodifikasi objek di dalam _array_ yang disalin, kita memutasikan _state_ yang ada. Misalnya, kode seperti ini adalah masalah.

```js
const Daftar berikutnya = [...daftar];
nextList[0].seen = true; // Masalah: memutasikan daftar[0]
setList(daftarberikutnya);
```

Meskipun `nextList` dan `list` adalah dua _array_ yang berbeda, **`nextList[0]` dan `list[0]` menunjuk ke objek yang sama.** Jadi dengan mengubah `nextList[0].seen`, kita juga mengubah `list[0].seen`. Ini adalah mutasi _state_, yang harus kita hindari! kita dapat mengatasi masalah ini dengan cara yang mirip dengan [memperbarui objek JavaScript bertingkat](https://react.dev/learn/update-objects-in-state#update-a-nested-object)--dengan menyalin setiap item yang ingin kita ubah, bukan memutasinya. Begini caranya.

## Memperbarui objek di dalam _array_

Objek tidak _benar-benar_ terletak "di dalam" _array_. Mereka mungkin tampak "di dalam" dalam kode, tetapi setiap objek dalam _array_ adalah nilai yang terpisah, yang "ditunjukkan" oleh _array_. Inilah mengapa kita harus berhati-hati saat mengubah _field_ bersarang seperti `list[0]`. Daftar karya seni orang lain mungkin menunjuk ke elemen _array_ yang sama!

**Saat memperbarui _state_ bersarang, kita perlu membuat salinan dari titik di mana kita ingin memperbarui, dan terus ke tingkat teratas.** Mari kita lihat cara kerjanya.

Dalam contoh ini, dua daftar karya seni terpisah memiliki _state_ awal yang sama. Mereka seharusnya diisolasi, tetapi karena mutasi, _state_ mereka secara tidak sengaja dibagikan, dan mencentang kotak di satu daftar memengaruhi daftar lainnya:

```js
impor { useState } dari 'react';

biarkan nextId = 3;
const Daftar awal = [
  { id: 0, judul: 'Perut Besar', dilihat: salah },
  { id: 1, judul: 'Lunar Landscape', seen: false },
  { id: 2, judul: 'Tentara Terakota', terlihat: benar },
];

export default function BucketList() {
  const [daftarsaya, setdaftarsaya] = useState(initialList);
  const [DaftarAnda, setDaftarAnda] = useState(
    Daftar awal
  );

  fungsi handleToggleMyList(artworkId, nextSeen) {
    const DaftarBerikutnya = [...daftarsaya];
    karya seni const = myNextList.find(
      a => a.id === artworkId
    );
    artwork.seen = nextSeen;
    setMyList(myNextList);
  }

  fungsi handleToggleYourList(artworkId, nextSeen) {
    constDaftarBerikutnya = [...DaftarAnda];
    karya seni const = yourNextList.find(
      a => a.id === artworkId
    );
    artwork.seen = nextSeen;
    setDaftarAnda(DaftarBerikutnya);
  }

  kembali (
    <>
      <h1>Daftar Keranjang Seni</h1>
      <h2>Daftar seni saya untuk dilihat:</h2>
      <Daftar Barang
        karya seni={myList}
        onToggle={handleToggleMyList} />
      <h2>Daftar karya seni kita untuk dilihat:</h2>
      <Daftar Barang
        karya seni={DaftarAnda}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ karya seni, onToggle }) {
  kembali (
    <ul>
      {artworks.map(karya seni => (
        <li key={artwork.id}>
          <label>
            <masukan
              ketik = "kotak centang"
              dicentang={artwork.seen}
              onChange={e => {
                pada Toggle(
                  karya seni.id,
                  e.target.diperiksa
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

Masalahnya ada di kode seperti ini:

```js
const DaftarBerikutnya = [...daftarsaya];
const artwork = myNextList.find((a) => a.id === artworkId);
artwork.seen = nextSeen; // Masalah: memutasikan item yang ada
setMyList(myNextList);
```

Meskipun _array_ `myList` itu sendiri baru, _itemnya sendiri_ sama dengan _array_ `myList` asli. Jadi, mengubah `artwork.seen` akan mengubah item karya seni _asli_. Item artwork tersebut juga ada di `yourList`, yang menyebabkan bug. Bug seperti ini mungkin sulit untuk dipikirkan, tetapi untungnya bug tersebut hilang jika kita menghindari _state_ mutasi.

**kita dapat menggunakan `map` untuk mengganti item lama dengan versi terbarunya tanpa mutasi.**

```js
setMyList(myList.map(artwork => {
  if (artwork.id === artworkId) {
    // Buat objek *baru* dengan perubahan
    return { ...artwork, seen: nextSeen };
  } kalau tidak {
    // Tidak ada perubahan
    mengembalikan karya seni;
  }
}));
```

Di sini, `...` adalah sintaks penyebaran objek yang digunakan untuk [membuat salinan objek.](https://react.dev/learn/update-objects-in-state#copying-objects-with-the-spread-syntax)

Dengan pendekatan ini, tidak ada item _state_ yang ada yang dimutasi, dan bug diperbaiki:

```js
impor { useState } dari 'react';

biarkan nextId = 3;
const Daftar awal = [
  { id: 0, judul: 'Perut Besar', dilihat: salah },
  { id: 1, judul: 'Lunar Landscape', seen: false },
  { id: 2, judul: 'Tentara Terakota', terlihat: benar },
];

export default function BucketList() {
  const [daftarsaya, setdaftarsaya] = useState(initialList);
  const [DaftarAnda, setDaftarAnda] = useState(
    Daftar awal
  );

  fungsi handleToggleMyList(artworkId, nextSeen) {
    setMyList(myList.map(artwork => {
      if (artwork.id === artworkId) {
        // Buat objek *baru* dengan perubahan
        return { ...artwork, seen: nextSeen };
      } kalau tidak {
        // Tidak ada perubahan
        mengembalikan karya seni;
      }
    }));
  }

  fungsi handleToggleYourList(artworkId, nextSeen) {
    setYourList(yourList.map(artwork => {
      if (artwork.id === artworkId) {
        // Buat objek *baru* dengan perubahan
        return { ...artwork, seen: nextSeen };
      } kalau tidak {
        // Tidak ada perubahan
        mengembalikan karya seni;
      }
    }));
  }

  kembali (
    <>
      <h1>Daftar Keranjang Seni</h1>
      <h2>Daftar seni saya untuk dilihat:</h2>
      <Daftar Barang
        karya seni={myList}
        onToggle={handleToggleMyList} />
      <h2>Daftar karya seni kita untuk dilihat:</h2>
      <Daftar Barang
        karya seni={DaftarAnda}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ karya seni, onToggle }) {
  kembali (
    <ul>
      {artworks.map(karya seni => (
        <li key={artwork.id}>
          <label>
            <masukan
              ketik = "kotak centang"
              dicentang={artwork.seen}
              onChange={e => {
                pada Toggle(
                  karya seni.id,
                  e.target.diperiksa
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

Secara umum, **kita sebaiknya hanya memutasikan objek yang baru saja kita buat.** Jika kita menyisipkan karya seni _baru_, kita dapat memutasikannya, tetapi jika kita berurusan dengan sesuatu yang sudah dalam _state_, kita perlu membuatnya sebuah salinan.

### Tulis logika pembaruan yang ringkas dengan Immer

Memperbarui _array_ bersarang tanpa mutasi bisa sedikit berulang. [Sama seperti objek](https://react.dev/learn/update-objects-in-state#write-concise-update-logic-with-immer):

- Umumnya, kita tidak perlu memperbarui _state_ lebih dari beberapa level. Jika objek _state_ kita sangat dalam, kita mungkin ingin [merestrukturisasinya secara berbeda](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state) agar objek tersebut rata.
- Jika kita tidak ingin mengubah struktur _state_, kita mungkin lebih memilih untuk menggunakan [Immer](https://github.com/immerjs/use-immer), yang memungkinkan kita menulis menggunakan sintaks yang nyaman namun bermutasi dan berhati-hati memproduksi salinan untuk kita.

Berikut adalah contoh Art Bucket List yang ditulis ulang dengan Immer:

```js
impor { useState } dari 'react';
import { useImmer } from 'use-immer';

biarkan nextId = 3;
const Daftar awal = [
  { id: 0, judul: 'Perut Besar', dilihat: salah },
  { id: 1, judul: 'Lunar Landscape', seen: false },
  { id: 2, judul: 'Tentara Terakota', terlihat: benar },
];

export default function BucketList() {
  const [daftarsaya, perbaruidaftarsaya] = useImmer(
    Daftar awal
  );
  const [DaftarAnda, perbaruiDaftarAnda] = useImmer(
    Daftar awal
  );

  fungsi handleToggleMyList(id, nextSeen) {
    updateMyList(draft => {
      karya seni const = draft.find(a =>
        a.id === id
      );
      artwork.seen = nextSeen;
    });
  }

  fungsi handleToggleYourList(artworkId, nextSeen) {
    perbaruiDaftarAnda(draf => {
      karya seni const = draft.find(a =>
        a.id === artworkId
      );
      artwork.seen = nextSeen;
    });
  }

  kembali (
    <>
      <h1>Daftar Keranjang Seni</h1>
      <h2>Daftar seni saya untuk dilihat:</h2>
      <Daftar Barang
        karya seni={myList}
        onToggle={handleToggleMyList} />
      <h2>Daftar karya seni kita untuk dilihat:</h2>
      <Daftar Barang
        karya seni={DaftarAnda}
        onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ karya seni, onToggle }) {
  kembali (
    <ul>
      {artworks.map(karya seni => (
        <li key={artwork.id}>
          <label>
            <masukan
              ketik = "kotak centang"
              dicentang={artwork.seen}
              onChange={e => {
                pada Toggle(
                  karya seni.id,
                  e.target.diperiksa
                );
              }}
            />
            {artwork.title}
          </label>
        </li>
      ))}
    </ul>
  );
}
```

Perhatikan bagaimana dengan Immer, **mutasi seperti `artwork.seen = nextSeen` sekarang baik-baik saja:**

```js
updateMyTodos(konsep => {
  const karya seni = draft.find(a => a.id === artworkId);
  artwork.seen = nextSeen;
});
```

Ini karena kita tidak mengubah _state_ _asli_, tetapi kita mengubah objek `draft` khusus yang disediakan oleh Immer. Demikian pula, kita dapat menerapkan metode mutasi seperti `push()` dan `pop()` ke konten `draft`.

Di balik layar, Immer selalu membuat _state_ berikutnya dari awal sesuai dengan perubahan yang telah kita lakukan pada `draft`. Ini membuat _event handler_ kita sangat ringkas tanpa pernah mengubah _state_.

Ringkasan:

- Kita dapat memasukkan _array_ ke _state_, tetapi kita tidak dapat mengubahnya.
- Jangan mengubah _array_, melainkan buatlah versi _baru_, dan perbarui _state_-nya.
- Kita dapat menggunakan sintaks penyebaran _array_ `[...arr, newItem]` untuk membuat _array_ dengan item baru.
- Kita dapat menggunakan `filter()` dan `map()` untuk membuat _array_ baru dengan item yang difilter atau diubah.
- Kita dapat menggunakan Immer untuk menjaga agar kode kita tetap ringkas.
