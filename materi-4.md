# **Materi 4: Konsep _State_ dalam React**

# Daftar isi

- [**Materi 4: Konsep _State_ dalam React**](#materi-4-konsep-state-dalam-react)
- [Daftar isi](#daftar-isi)
- [_State_: Memori Komponen](#state-memori-komponen)
  - [Kekurangan variabel biasa](#kekurangan-variabel-biasa)
  - [Menambahkan variabel _state_](#menambahkan-variabel-state)
  - [Hook pertama kita: `useState`](#hook-pertama-kita-usestate)
  - [Anatomi dari `useState`](#anatomi-dari-usestate)
  - [Memberikan komponen beberapa variabel _state_](#memberikan-komponen-beberapa-variabel-state)
  - [_State_ bersifat terisolasi dan _private_](#state-bersifat-terisolasi-dan-private)
  - [Rangkuman](#rangkuman)

# _State_: Memori Komponen

Komponen seringkali perlu mengubah apa yang ada di layar sebagai hasil interaksi. Mengetik dalam _form_ akan memperbarui nilai bidang input, mengeklik "berikutnya" pada _slide_ gambar akan mengubah gambar mana yang ditampilkan, mengeklik "beli" akan memasukkan produk ke dalam keranjang belanja. Komponen perlu "mengingat" hal-hal tertentu: nilai input saat ini, gambar saat ini, keranjang belanja. Di React, jenis memori khusus komponen ini disebut _state_.

Kita akan belajar:

- Cara menambahkan variabel _state_ dengan Hook `useState`
- Pasangan nilai apa yang dikembalikan oleh Hook `useState`
- Cara menambahkan lebih dari satu variabel _state_
- Mengapa _state_ disebut lokal

## Kekurangan variabel biasa

Berikut contoh komponen yang merender nama planet. Mengeklik tombol "Berikutnya" akan menampilkan planet berikutnya dengan mengubah `index` ke `1`, lalu `2`, dan seterusnya. Namun, ini **tidak akan berhasil** (silakan dicoba!):

```jsx
const planets = ["Mercury", "Venus", "Earth"];

export default function SolarSystem() {
  let index = 0;

  function handleClick() {
    index++;
  }

  return (
    <>
      <h1>{planets[index]}</h1>
      <button onClick={handleClick}>Berikutnya</button>
    </>
  );
}
```

_Event handler_ `handleClick` sebenarnya berhasil memperbarui variabel lokal, `index`. Tetapi ada dua hal yang mencegah perubahan itu terlihat:

1. **Variabel lokal tidak bertahan di antara perenderan.** Saat React merender komponen ini untuk kedua kalinya, React merendernya dari awal, tidak mempertimbangkan perubahan apa pun pada variabel lokal.
2. **Perubahan pada variabel lokal tidak akan memicu perenderan.** React tidak menyadari perlu merender komponen lagi dengan data baru.

Untuk memperbarui komponen dengan data baru, dua hal perlu terjadi:

1. Pertahankan data di antara render.
2. Picu React untuk merender komponen dengan data baru (re-rendering).

Hook `useState` menyediakan dua hal tersebut:

1. Variabel _state_ untuk mempertahankan data di antara render.
2. Fungsi _setter_ _state_ untuk memperbarui variabel dan memicu React untuk merender komponen lagi.

## Menambahkan variabel _state_

Untuk menambahkan variabel _state_, impor `useState` dari React di bagian atas file:

```jsx
import { useState } from "react";
```

Kemudian, ganti baris ini:

```jsx
let index = 0;
```

dengan

```jsx
const [index, setIndex] = useState(0);
```

`index` adalah variabel _state_ dan `setIndex` merupakan fungsi _setter_ (fungsi untuk mengubah nilai _state_).

> Sintaks `[` dan `]` di atas disebut _array destructuring_ yang memungkinkan kita untuk mengeluarkan nilai-nilai dari _array_. _Array_ yang dikembalikan oleh `useState` selalu memiliki dua item.

Beginilah cara menggunakannya dengan `handleClick`:

```jsx
function handleClick() {
  setIndex(index + 1);
}
```

Sekarang mengeklik tombol "Berikutnya" akan berhasil mengganti planet saat ini:

```jsx
const planets = ["Mercury", "Venus", "Earth"];

export default function SolarSystem() {
  const [index, setIndex] = useState(0);

  function handleClick() {
    setIndex(index + 1);
  }

  return (
    <>
      <h1>{planets[index]}</h1>
      <button onClick={handleClick}>Berikutnya</button>
    </>
  );
}
```

## Hook pertama kita: `useState`

Dalam React, `useState`, serta fungsi-fungsi lainnya yang diawali dengan ”use”, disebut Hook.

Hook adalah fungsi-fungsi khusus yang hanya tersedia saat React merender (yang akan kita bahas lebih detail di materi berikutnya). Mereka dinamai "hook" (pengait) karena mengaitkan/menghubungkan kita ke berbagai fitur React.

_State_ hanyalah salah satu dari fitur tersebut, tetapi kita akan bertemu dengan Hook lainnya nanti.

> **Peringatan**
>
> Hook (fungsi-fungsi yang diawali dengan _use_) hanya dapat dipanggil di bagian atas komponen atau di Hook buatan kita sendiri (akan dipelajari di materi berikutnya). Kita tidak dapat memanggil Hook di dalam _decision_ (kondisi), _loop_ (perulangan), atau fungsi lain di dalam komponen (fungsi bersarang). Meskipun Hook hanyalah fungsi, tapi ia kita anggap sebagai deklarasi untuk kebutuhan komponen kita, seperti saat kita meng-“import” modul di bagian atas file.

## Anatomi dari `useState`

Saat kita memanggil `useState`, kita memberi tahu React bahwa kita ingin komponen ini mengingat sesuatu:

```jsx
const [index, setIndex] = useState(0);
```

Dalam contoh di atas, kita ingin React mengingat `index`.

> Konvensi yang disarankan untuk menamai sepasang nilai tersebut adalah `const [sesuatu, useSesuatu]`. Meskipun kita dapat menamainya dengan apa pun yang kita suka, tetapi konvensi tersebut akan membuat segalanya lebih mudah dipahami di seluruh _project_ yang dikerjakan.

Satu-satunya argumen `useState` adalah nilai awal dari variabel _state_ kita. Dalam contoh ini, nilai awal `index` diatur ke `0` dengan `useState(0)`.

Setiap kali komponen kita merender, `useState` memberi kita _array_ yang berisi dua nilai:

1. Variabel _state_ (`index`) berisi nilai yang kita simpan.
2. Fungsi _setter_ _state_ (`setIndex`) yang dapat memperbarui variabel _state_ dan memicu React untuk merender komponen lagi.

Inilah cara kerja `useState`:

```jsx
const [index, setIndex] = useState(0);
```

1. **Komponen merender pertama kali**. Karena kita meneruskan `0` ke `useState` sebagai nilai awal untuk `index`, maka akan mengembalikan `[0, setIndex]`. React mengingat bahwa `0` adalah nilai _state_ terbaru saat ini.
2. **Pengguna memperbarui _state_**. Ketika pengguna mengeklik tombol, itu akan memanggil `setIndex(index + 1)`. `index` saat ini adalah `0`, jadi itu sama seperti `setIndex(1)`. Ini memberitahu React untuk mengingat bahwa `index` sekarang adalah `1` dan memicu render lagi.
3. **Render kedua**. React masih membaca `useState(0)`, tetapi karena React ingat bahwa kita telah mengatur `index` ke `1`, `useState` akan mengembalikan `[1, setIndex]`.
4. Dan seterusnya!

## Memberikan komponen beberapa variabel _state_

Kita dapat memiliki banyak variabel _state_ sebanyak yang kita inginkan dalam satu komponen. Komponen ini memiliki dua variabel _state_, sebuah angka `index` dan sebuah _boolean_ `showMore` yang diaktifkan saat kita mengeklik “Tampilkan detail”:

```jsx
const planets = ["Mercury", "Venus", "Earth"];

export default function SolarSystem() {
  const [index, setIndex] = useState(0);
  const [showMore, setShowMore] = useState(false);

  function handleClick() {
    setIndex(index + 1);
  }

  function handleMoreClick() {
    setShowMore(!showMore);
  }

  return (
    <>
      <h1>{planets[index]}</h1>
      {showMore && <p>Planet ke-{index + 1} dalam tata surya.</p>}
      <button onClick={handleMoreClick}>
        {showMore ? "Sembunyikan" : "Tampilkan"} detail
      </button>
      <button onClick={handleClick}>Berikutnya</button>
    </>
  );
}
```

Memiliki beberapa variabel _state_ adalah cara yang baik jika _state_-nya tidak saling terkait, seperti `index` dan `showMore` dalam contoh di atas. Tetapi jika kita menemukan bahwa kita sering mengubah dua variabel _state_ secara bersamaan, mungkin akan lebih mudah untuk menggabungkannya menjadi satu. Misalnya, jika kita memiliki _form_ dengan banyak bidang, akan lebih mudah untuk memiliki satu variabel _state_ yang menyimpan objek daripada variabel _state_ untuk menyimpan nilai per bidang. Baca dokumentasi React: [Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure) untuk tips lainnya.

## _State_ bersifat terisolasi dan _private_

_State_ bersifat lokal untuk _instance_ komponen yang dirender di layar. Dengan kata lain, jika kita merender komponen yang sama dua kali, setiap salinan akan memiliki _state_ yang benar-benar terisolasi! Mengubah salah satunya tidak akan mempengaruhi yang lain.

Dalam contoh ini, komponen `SolarSystem` dari contoh sebelumnya dirender dua kali tanpa perubahan pada logikanya. Coba klik tombol di dalam setiap komponen `SolarSystem`. Perhatikan bahwa _state_ mereka independen:

```jsx
import SolarSystem from "./SolarSystem.js";

export default function Page() {
  return (
    <div className="Page">
      <SolarSystem />
      <SolarSystem />
    </div>
  );
}
```

Inilah yang membuat _state_ berbeda dari variabel biasa yang biasa kita deklarasikan di bagian atas _module_ kita. _State_ tidak terikat pada pemanggilan fungsi tertentu atau tempat dalam kode, tetapi bersifat "lokal" ke tempat tertentu di layar. Kita merender dua komponen `<SolarSystem />`, sehingga _state_-nya disimpan secara terpisah.

Perhatikan juga bagaimana komponen `Page` tidak "tahu" apapun tentang _state_ `SolarSystem` atau bahkan apakah _state_ tersebut ada. Tidak seperti _props_, _state_ sepenuhnya _private_ untuk setiap komponen yang mendeklarasikannya. Komponen induk tidak dapat mengubahnya. Ini memungkinkan kita menambahkan _state_ ke komponen apa pun atau menghapusnya tanpa memengaruhi komponen lainnya.

Bagaimana jika kita ingin kedua komponen `SolarSystem` menjaga _state_-nya tetap sinkron? Cara yang tepat untuk melakukannya di React adalah menghapus _state_ dari komponen anak dan menambahkannya ke _ancestor_ yang sama terdekat. Topik ini dapat dipelajari di dokumentasi React: [Sharing State Between Components](https://react.dev/learn/sharing-state-between-components).

## Rangkuman

- Gunakan variabel _state_ saat komponen perlu "mengingat" beberapa informasi di antara render (agar informasi itu tidak hilang saat komponen dirender ulang).
- Variabel _state_ dideklarasikan dengan memanggil Hook `useState`.
- Hook adalah fungsi-fungsi khusus yang dimulai dengan `use`. Mereka meng-"hook" (mengaitkan) kita ke fitur React seperti _state_.
- Hook mungkin mengingatkan kita tentang pengimporan: mereka harus dipanggil tanpa kondisi (di luar _decision_ dan _loop_). Memanggil Hook, termasuk `useState`, hanya valid pada level teratas dari sebuah komponen atau Hook lainnya.
- Hook `useState` mengembalikan sepasang nilai: _state_ saat ini dan fungsi untuk memperbaruinya.
- Kita dapat memiliki lebih dari satu variabel _state_ (meskipun tidak ada “identifier”, React dapat mencocokkan kembali masing-masing variabel pada setiap render berdasarkan urutan deklarasinya).
- _State_ bersifat _private_ untuk setiap komponen. Jika kita merendernya di dua tempat, setiap salinan mendapatkan _state_-nya sendiri.
