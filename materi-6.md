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

Dalam JavaScript, _array_ bersifat _mutable_ (dapat diubah), tetapi kita harus memperlakukannya sebagai _immutable_ (tidak dapat diubah) saat kita menyimpannya dalam _state_. Sama seperti objek, saat kita ingin memperbarui _array_ yang disimpan dalam _state_, kita perlu membuat yang baru (atau membuat salinan dari yang sudah ada), lalu menge-_set_ _state_ untuk menggunakan _array_ baru.

Kita akan belajar:

- Cara menambah, menghapus, atau mengubah item dalam _array_ dalam _state_ React
- Cara memperbarui objek di dalam _array_
- Cara membuat penyalinan _array_ tanpa penulisan ulang dengan Immer

## Memperbarui _array_ tanpa mutasi

Dalam JavaScript, _array_ sebenarnya hanyalah objek (jenis lain). [Seperti objek](https://react.dev/learn/update-objects-in-state), **kita harus memperlakukan _array_ dalam _state_ React sebagai _read_only_.** Ini berarti kita tidak boleh meng-_assign_ ulang item di dalam _array_ seperti `arr[ 0] = 'bird'`, dan kita juga tidak boleh menggunakan _method_-_method_ yang memutasi _array_, seperti `push()` dan `pop()`.

Sebagai gantinya, setiap kali kita ingin memperbarui _array_, kita harus meneruskan _array_ _baru_ ke fungsi _setter_ _state_ kita. Untuk melakukannya, kita bisa membuat _array_ baru dari _array_ asli di _state_ kita dengan memanggil method non-mutasi seperti `filter()` dan `map()`. Kemudian kita dapat menge-_set_ _state_ kita ke _array_ baru yang dihasilkan.

Berikut adalah tabel referensi operasi _array_ umum. Saat berurusan dengan _array_ di dalam _state_ React, kita harus menghindari _method_-_method_ di kolom kiri, dan memilih _method_-_method_ di kolom kanan:

|             | hindari (memutasi _array_)                        | gunakan (mengembalikan _array_ baru)                                         |
| ----------- | ------------------------------------------------- | ---------------------------------------------------------------------------- |
| menambahkan | `push`, `unshift`                                 | `concat`, `[...arr]` sintaks _spread_ ([contoh](#menambah-to-an-_array_))    |
| menghapus   | `pop`, `shift`, `splice`                          | `filter`, `slice` ([contoh](#menghapus-dari-_array_))                        |
| mengganti   | `splice`, `arr[i] = ...` _assignment_ (penugasan) | `map` ([contoh](#mengganti-item-dalam-_array_))                              |
| mengurutkan | `reverse`, `sort`                                 | salin _array_ terlebih dahulu ([contoh](#membuat-lain-perubahan-ke-_array_)) |

Atau, kita dapat [menggunakan Immer](#write-concise-update-logic-with-immer) yang memungkinkan kita menggunakan _method_-_method_ pada kedua kolom di atas.

<blockquote>

**Peringatan**

Walaupun [`slice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/_array_/slice) dan [`splice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice) diberi nama serupa tetapi sangat berbeda:

- `slice` memungkinkan kita menyalin _array_ atau sebagian darinya.
- `splice` **memutasi** _array_ (untuk menyisipkan atau menghapus item).

Di React, kita akan lebih sering menggunakan `slice` (tanpa `p`!) karena kita tidak ingin memutasi objek atau _array_ dalam _state_. Materi sebelumnya ([Memperbarui Objek](https://react.dev/learn/memperbarui-objek-dalam-state)) menjelaskan apa itu mutasi dan mengapa itu tidak direkomendasikan untuk _state_.

</blockquote>

### Menambahkan ke _array_

`push()` akan memutasi _array_ (yang kita tidak menginginkannya):

```js
import { useState } from "react";

let nextId = 0;

export default function List() {
  const [name, setName] = useState("");
  const [artists, setArtists] = useState([]);

  return (
    <>
      <h1>Pematung yang menginspirasi:</h1>
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

Sebagai gantinya, buat _array_ _baru_ yang berisi item yang sudah ada _dan_ item baru di bagian akhir. Ada beberapa cara untuk melakukannya, tetapi yang paling mudah adalah menggunakan sintaks [_array spread_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals) `...`:

```js
setArtists(
  // Ganti state
  [
    // dengan array baru
    ...artists, // yang berisi semua item lama
    { id: nextId++, name: name }, // dan satu item baru di akhir
  ]
);
```

Sekarang berfungsi dengan benar:

```js
import { useState } from "react";

let nextId = 0;

export default function List() {
  const [name, setName] = useState("");
  const [artists, setArtists] = useState([]);

  return (
    <>
      <h1>Pematung yang menginspirasi:</h1>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button
        onClick={() => {
          setArtists([...artists, { id: nextId++, name: name }]);
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

Sintaks _array spread_ juga memungkinkan kita menambahkan item dengan menempatkannya _sebelum_ `...artists` (_array_ asli):

```js
setArtists([
  { id: nextId++, nama: nama },
  ...artists, // Taruh item lama di akhir
]);
```

Dengan cara ini, spread dapat melakukan tugas `push()` dengan menambahkan ke akhir _array_ dan `unshift()` dengan menambahkan ke awal _array_.

### Menghapus dari _array_

Cara termudah untuk menghapus item dari _array_ adalah dengan _memfilternya_. Dengan kata lain, kita akan menghasilkan _array_ baru yang tidak berisi item tersebut. Untuk melakukannya, gunakan _method_ `filter`, misalnya:

```js
import { useState } from "react";

let initialArtists = [
  { id: 0, name: "Marta Colvin Andrade" },
  { id: 1, name: "Lamidi Olonade Fakeye" },
  { id: 2, name: "Louise Nevelson" },
];

export default function List() {
  const [artists, setArtists] = useState(initialArtists);

  return (
    <>
      <h1>Pematung yang menginspirasi:</h1>
      <ul>
        {artists.map((artist) => (
          <li key={artist.id}>
            {artist.name}{" "}
            <button
              onClick={() => {
                setArtists(artists.filter((a) => a.id !== artist.id));
              }}
            >
              Delete
            </button>
          </li>
        ))}
      </ul>
    </>
  );
}
```

Klik tombol "Hapus" beberapa kali, dan lihat _handler_ kliknya.

```js
setArtists(artists.filter((a) => a.id !== artist.id));
```

Di sini, `artists.filter(a => a.id !== artist.id)` berarti "buat _array_ yang terdiri dari `artists` yang ID-nya berbeda dari `artist.id`". Dengan kata lain, tombol "Hapus" setiap artis akan memfilter _artis_ itu dari _array_, lalu meminta _render_ ulang dengan _array_ yang dihasilkan. Perhatikan bahwa `filter` tidak mengubah _array_ asli.

### Mengubah _array_

Jika kita ingin mengubah beberapa atau semua item _array_, kita dapat menggunakan `map()` untuk membuat _array_ **baru**. Fungsi yang akan kita berikan ke `peta` dapat memutuskan apa yang harus dilakukan dengan setiap item, berdasarkan datanya atau indeksnya (atau keduanya).

Dalam contoh ini, sebuah _array_ menyimpan koordinat dua lingkaran dan sebuah persegi. Saat kita menekan tombol, itu hanya memindahkan lingkaran ke bawah sebanyak 50 piksel. Ini dilakukan dengan membuat _array_ data baru menggunakan `map()`:

```js
import { useState } from "react";

let initialShapes = [
  { id: 0, type: "circle", x: 50, y: 100 },
  { id: 1, type: "square", x: 150, y: 100 },
  { id: 2, type: "circle", x: 250, y: 100 },
];

export default function ShapeEditor() {
  const [shapes, setShapes] = useState(initialShapes);

  function handleClick() {
    const nextShapes = shapes.map((shape) => {
      if (shape.type === "square") {
        // No change
        return shape;
      } else {
        // Return a new circle 50px below
        return {
          ...shape,
          y: shape.y + 50,
        };
      }
    });
    // Re-render with the new array
    setShapes(nextShapes);
  }

  return (
    <>
      <button onClick={handleClick}>Pindahkan lingkaran ke bawah!</button>
      {shapes.map((shape) => (
        <div
          key={shape.id}
          style={{
            background: "purple",
            position: "absolute",
            left: shape.x,
            top: shape.y,
            borderRadius: shape.type === "circle" ? "50%" : "",
            width: 20,
            height: 20,
          }}
        />
      ))}
    </>
  );
}
```

### Mengganti item dalam _array_

Sangat umum untuk ingin mengganti satu atau lebih item dalam _array_. Penugasan seperti `arr[0] = 'bird'` memutasi _array_ asli, jadi kita sebaiknya menggunakan `map` untuk ini juga.

Untuk mengganti item, buat _array_ baru dengan `map`. Di dalam panggilan `map` kita, kita akan menerima indeks item sebagai argumen kedua. Gunakan itu untuk memutuskan apakah akan mengembalikan item asli (argumen pertama) atau yang lainnya:

```js
import { useState } from "react";

let initialCounters = [0, 0, 0];

export default function CounterList() {
  const [counters, setCounters] = useState(initialCounters);

  function handleIncrementClick(index) {
    const nextCounters = counters.map((c, i) => {
      if (i === index) {
        // Increment the clicked counter
        return c + 1;
      } else {
        // The rest haven't changed
        return c;
      }
    });
    setCounters(nextCounters);
  }

  return (
    <ul>
      {counters.map((counter, i) => (
        <li key={i}>
          {counter}
          <button
            onClick={() => {
              handleIncrementClick(i);
            }}
          >
            +1
          </button>
        </li>
      ))}
    </ul>
  );
}
```

### Memasukkan ke dalam _array_

Terkadang, kita mungkin ingin menyisipkan item pada posisi tertentu yang bukan di awal maupun di akhir. Untuk melakukannya, kita dapat menggunakan sintaks _array_ spread `...` bersama dengan _method_ `slice()`. Metode `slice()` memungkinkan kita memotong "irisan" dari _array_. Untuk menyisipkan item, kita akan membuat _array_ yang menyebarkan irisan _sebelum_ titik penyisipan, lalu item baru, lalu selebihnya dari _array_ asli.

Dalam contoh ini, tombol Sisipkan selalu menyisipkan pada indeks `1`:

```js
import { useState } from "react";

let nextId = 3;
const initialArtists = [
  { id: 0, name: "Marta Colvin Andrade" },
  { id: 1, name: "Lamidi Olonade Fakeye" },
  { id: 2, name: "Louise Nevelson" },
];

export default function List() {
  const [name, setName] = useState("");
  const [artists, setArtists] = useState(initialArtists);

  function handleClick() {
    const insertAt = 1; // Bisa indeks berapa pun
    const nextArtists = [
      // Item-item sebelum titik penyisipan:
      ...artists.slice(0, insertAt),
      // Item baru:
      { id: nextId++, name: name },
      // Item-item setelah titik penyisipan:
      ...artists.slice(insertAt),
    ];
    setArtists(nextArtists);
    setName("");
  }

  return (
    <>
      <h1>Pematung yang menginspirasi:</h1>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button onClick={handleClick}>Sisipkan</button>
      <ul>
        {artists.map((artist) => (
          <li key={artist.id}>{artist.name}</li>
        ))}
      </ul>
    </>
  );
}
```

### Membuat perubahan lain pada _array_

Ada beberapa hal yang tidak dapat kita lakukan dengan sintaks sebar dan _method_-_method_ non-mutasi seperti `map()` dan `filter()` saja. Misalnya, kita mungkin ingin membalikkan atau mengurutkan _array_. Metode JavaScript `reverse()` dan `sort()` memutasikan _array_ asli, sehingga kita tidak dapat menggunakannya secara langsung.

**Namun, kita dapat menyalin _array_ terlebih dahulu, lalu mengubahnya.**

Misalnya:

```js
import { useState } from "react";

let nextId = 3;
const initialList = [
  { id: 0, title: "Big Bellies" },
  { id: 1, title: "Lunar Landscape" },
  { id: 2, title: "Terracotta Army" },
];

export default function List() {
  const [list, setList] = useState(initialList);

  function handleClick() {
    const nextList = [...list];
    nextList.reverse();
    setList(nextList);
  }

  return (
    <>
      <button onClick={handleClick}>Balikkan</button>
      <ul>
        {list.map((artwork) => (
          <li key={artwork.id}>{artwork.title}</li>
        ))}
      </ul>
    </>
  );
}
```

Di sini, kita menggunakan sintaks spread `[...list]` untuk membuat salinan _array_ asli terlebih dahulu. Sekarang setelah kita memiliki salinannya, kita dapat menggunakan _method_-_method_ mutasi seperti `nextList.reverse()` atau `nextList.sort()`, atau bahkan menetapkan masing-masing item dengan `nextList[0] = "something"`.

Namun, **bahkan jika kita menyalin _array_, kita tidak dapat memutasi item yang ada _di dalamnya_ secara langsung.** Hal ini karena penyalinan bersifat dangkal--arik baru akan berisi item yang sama dengan yang asli. Jadi jika kita memodifikasi objek di dalam _array_ yang disalin, kita memutasikan _state_ yang ada. Misalnya, kode seperti ini adalah masalah.

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
import { useState } from "react";

let nextId = 3;
const initialList = [
  { id: 0, title: "Big Bellies", seen: false },
  { id: 1, title: "Lunar Landscape", seen: false },
  { id: 2, title: "Terracotta Army", seen: true },
];

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(initialList);

  function handleToggleMyList(artworkId, nextSeen) {
    const myNextList = [...myList];
    const artwork = myNextList.find((a) => a.id === artworkId);
    artwork.seen = nextSeen;
    setMyList(myNextList);
  }

  function handleToggleYourList(artworkId, nextSeen) {
    const yourNextList = [...yourList];
    const artwork = yourNextList.find((a) => a.id === artworkId);
    artwork.seen = nextSeen;
    setYourList(yourNextList);
  }

  return (
    <>
      <h1>Daftar Karya Seni</h1>
      <h2>Daftar karya seni saya untuk dilihat:</h2>
      <ItemList artworks={myList} onToggle={handleToggleMyList} />
      <h2>Daftar karya seni Anda untuk dilihat:</h2>
      <ItemList artworks={yourList} onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map((artwork) => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={(e) => {
                onToggle(artwork.id, e.target.checked);
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
const myNextList = [...myList];
const artwork = myNextList.find((a) => a.id === artworkId);
artwork.seen = nextSeen; // Masalah: memutasikan item yang ada
setMyList(myNextList);
```

Meskipun _array_ `myList` itu sendiri baru, _itemnya sendiri_ sama dengan _array_ `myList` asli. Jadi, mengubah `artwork.seen` akan mengubah item karya seni _asli_. Item artwork tersebut juga ada di `yourList`, yang menyebabkan bug. Bug seperti ini mungkin sulit untuk dipikirkan, tetapi untungnya bug tersebut hilang jika kita menghindari _state_ mutasi.

**Kita dapat menggunakan `map` untuk mengganti item lama dengan versi terbarunya tanpa mutasi.**

```js
setMyList(
  myList.map((artwork) => {
    if (artwork.id === artworkId) {
      // Buat objek *baru* dengan perubahan
      return { ...artwork, seen: nextSeen };
    } else {
      // Tidak ada perubahan
      return artwork;
    }
  })
);
```

Di sini, `...` adalah sintaks penyebaran objek yang digunakan untuk [membuat salinan objek.](https://react.dev/learn/update-objects-in-state#copying-objects-with-the-spread-syntax)

Dengan pendekatan ini, tidak ada item _state_ yang ada yang dimutasi, dan bug diperbaiki:

```js
import { useState } from "react";

let nextId = 3;
const initialList = [
  { id: 0, title: "Big Bellies", seen: false },
  { id: 1, title: "Lunar Landscape", seen: false },
  { id: 2, title: "Terracotta Army", seen: true },
];

export default function BucketList() {
  const [myList, setMyList] = useState(initialList);
  const [yourList, setYourList] = useState(initialList);

  function handleToggleMyList(artworkId, nextSeen) {
    setMyList(
      myList.map((artwork) => {
        if (artwork.id === artworkId) {
          // Buat objek *baru* dengan perubahan
          return { ...artwork, seen: nextSeen };
        } else {
          // Tidak ada perubahan
          return artwork;
        }
      })
    );
  }

  function handleToggleYourList(artworkId, nextSeen) {
    setYourList(
      yourList.map((artwork) => {
        if (artwork.id === artworkId) {
          // Buat objek *baru* dengan perubahan
          return { ...artwork, seen: nextSeen };
        } else {
          // Tidak ada perubahan
          return artwork;
        }
      })
    );
  }

  return (
    <>
      <h1>Daftar Karya Seni</h1>
      <h2>Daftar karya seni saya untuk dilihat:</h2>
      <ItemList artworks={myList} onToggle={handleToggleMyList} />
      <h2>Daftar karya seni Anda untuk dilihat:</h2>
      <ItemList artworks={yourList} onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map((artwork) => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={(e) => {
                onToggle(artwork.id, e.target.checked);
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

Berikut adalah contoh Daftar Karya Seni yang ditulis ulang dengan Immer:

```js
import { useState } from "react";
import { useImmer } from "use-immer";

let nextId = 3;
const initialList = [
  { id: 0, title: "Big Bellies", seen: false },
  { id: 1, title: "Lunar Landscape", seen: false },
  { id: 2, title: "Terracotta Army", seen: true },
];

export default function BucketList() {
  const [myList, updateMyList] = useImmer(initialList);
  const [yourList, updateYourList] = useImmer(initialList);

  function handleToggleMyList(id, nextSeen) {
    updateMyList((draft) => {
      const artwork = draft.find((a) => a.id === id);
      artwork.seen = nextSeen;
    });
  }

  function handleToggleYourList(artworkId, nextSeen) {
    updateYourList((draft) => {
      const artwork = draft.find((a) => a.id === artworkId);
      artwork.seen = nextSeen;
    });
  }

  return (
    <>
      <h1>Daftar Karya Seni</h1>
      <h2>Daftar karya seni saya untuk dilihat:</h2>
      <ItemList artworks={myList} onToggle={handleToggleMyList} />
      <h2>Daftar karya seni Anda untuk dilihat:</h2>
      <ItemList artworks={yourList} onToggle={handleToggleYourList} />
    </>
  );
}

function ItemList({ artworks, onToggle }) {
  return (
    <ul>
      {artworks.map((artwork) => (
        <li key={artwork.id}>
          <label>
            <input
              type="checkbox"
              checked={artwork.seen}
              onChange={(e) => {
                onToggle(artwork.id, e.target.checked);
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
updateMyTodos((draft) => {
  const artwork = draft.find((a) => a.id === artworkId);
  artwork.seen = nextSeen;
});
```

Ini karena kita tidak memutasi _state_ _asli_, tetapi kita memutasi objek `draft` khusus yang disediakan oleh Immer. Demikian pula, kita dapat menerapkan _method_-_method_ mutasi seperti `push()` dan `pop()` ke konten `draft`.

Di balik layar, Immer selalu membuat _state_ berikutnya dari awal sesuai dengan perubahan yang telah kita lakukan pada `draft`. Ini membuat _event handler_ kita sangat ringkas tanpa pernah memutasi _state_.

Ringkasan:

- Kita dapat memasukkan _array_ ke dalam _state_, tetapi kita tidak dapat mengubahnya.
- Jangan memutasi _array_, tapi buatlah versi _baru_, dan perbarui _state_-nya.
- Kita dapat menggunakan sintaks _array spread_ `[...arr, newItem]` untuk membuat _array_ dengan item baru.
- Kita dapat menggunakan `filter()` dan `map()` untuk membuat _array_ baru dengan item yang difilter atau diubah.
- Kita dapat menggunakan Immer untuk menjaga agar kode kita tetap ringkas.
