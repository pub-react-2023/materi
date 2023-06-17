# **Materi 5: Memperbarui Objek dalam _State_**

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

- [**Materi 5: Memperbarui Objek dalam _State_**](#materi-5-memperbarui-objek-dalam-state)
- [Silabus](#silabus)
- [Daftar isi](#daftar-isi)
- [Memperbarui Objek dalam _State_](#memperbarui-objek-dalam-state)
  - [Memahami tentang mutasi](#memahami-tentang-mutasi)
  - [Perlakukan _state_ sebagai _read-only_](#perlakukan-state-sebagai-read-only)
    - [Mutasi lokal tidak menyebabkan masalah](#mutasi-lokal-tidak-menyebabkan-masalah)
  - [Menyalin objek dengan sintaks _spread_](#menyalin-objek-dengan-sintaks-spread)
    - [Menggunakan satu _event handler_ untuk beberapa _field_](#menggunakan-satu-event-handler-untuk-beberapa-field)
  - [Memperbarui objek bersarang](#memperbarui-objek-bersarang)
    - [Objek bersarang sebenarnya tidak bersarang](#objek-bersarang-sebenarnya-tidak-bersarang)
    - [Tulis logika pembaruan yang ringkas dengan Immer](#tulis-logika-pembaruan-yang-ringkas-dengan-immer)
      - [Bagaimana cara kerja Immer?](#bagaimana-cara-kerja-immer)
      - [Mengapa memutasikan _state_ tidak direkomendasikan di React?](#mengapa-memutasikan-state-tidak-direkomendasikan-di-react)

# Memperbarui Objek dalam _State_

_State_ dapat menyimpan segala jenis nilai JavaScript, termasuk objek. Tetapi kita tidak boleh mengubah objek yang kita simpan dalam _state_ React secara langsung. Sebagai gantinya, saat kita ingin memperbarui objek, kita perlu membuat yang baru (atau membuat salinan dari yang sudah ada), lalu meng-_set_ _state_ untuk menggunakan salinan tersebut.

Kita akan belajar:

- Cara memperbarui objek dengan benar dalam _state_ React
- Cara memperbarui objek bersarang tanpa memutasinya
- Apa itu _immutability_ (kekekalan), dan cara agar tidak merusaknya
- Cara membuat penyalinan objek tanpa penulisan ulang dengan Immer

## Memahami tentang mutasi

Kita dapat menyimpan segala jenis nilai JavaScript dalam _state_.

```js
const [x, setX] = useState(0);
```

Sejauh ini kita telah bekerja dengan angka, _string_, dan _boolean_. Jenis nilai JavaScript ini "_immutable_" (kekal), artinya tidak dapat diubah atau "_read-only_" (hanya-baca). Kita dapat memicu _render_ ulang untuk me-_replace_ suatu nilai:

```js
setX(5);
```

_State_ `x` berubah dari `0` menjadi `5`, tetapi _angka `0` itu sendiri_ tidak berubah. Tidak mungkin membuat perubahan apa pun pada nilai primitif bawaan seperti angka, _string_, dan _boolean_ di JavaScript.

Sekarang pertimbangkan sebuah objek dalam _state_:

```js
const [posisi, setPosition] = useState({ x: 0, y: 0 });
```

Secara teknis, dimungkinkan untuk mengubah isi dari _objek itu sendiri_. **Ini disebut mutasi:**

```js
posisi.x = 5;
```

Namun, meskipun objek dalam _state_ React secara teknis dapat diubah, kita harus memperlakukannya **seolah-olah** tidak dapat diubah (seperti angka, _boolean_, dan _string_). Jangan memutasikannya, melainkan kita harus selalu menggantinya.

## Perlakukan _state_ sebagai _read-only_

Dengan kata lain, kita harus **memperlakukan objek JavaScript apa pun yang kita masukkan ke dalam _state_ sebagai _read-only_.**

Contoh berikut menyimpan objek dalam _state_ untuk mewakili posisi _pointer_ saat ini. Titik merah seharusnya bergerak saat kita menyentuh atau memindahkan kursor ke area pratinjau. Namun ternyata titik tersebut tetap di posisi awal:

```js
import { useState } from "react";

export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0,
  });

  return (
    <div
      onPointerMove={(e) => {
        position.x = e.clientX;
        position.y = e.clientY;
      }}
      style={{
        position: "relative",
        width: "100vw",
        height: "100vh",
      }}
    >
      <div
        style={{
          position: "absolute",
          backgroundColor: "red",
          borderRadius: "50%",
          transform: `translate(${position.x}px, ${position.y}px)`,
          left: -10,
          top: -10,
          width: 20,
          height: 20,
        }}
      />
    </div>
  );
}
```

Masalahnya terletak pada kode berikut:

```js
onPointerMove={e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
```

Kode di atas memodifikasi objek yang ditetapkan ke `position` dari [_render_ sebelumnya.](https://react.dev/learn/state-as-a-snapshot#_render_ing-takes-a-snapshot-in-time) Tapi tanpa menggunakan fungsi _setter_ _state_, React tidak tahu bahwa objek telah berubah. Jadi React tidak melakukan apa pun sebagai respons. Ini seperti kita meminta perubahan pesanan di restoran setelah kita selesai makan. Meskipun _state_ mutasi kadang berfungsi dalam beberapa kasus, cara ini tidak disarankan. Kita harus memperlakukan nilai _state_ yang dapat kita akses dalam _render_ sebagai _read-only_.

Untuk benar-benar [memicu _render_ ulang](https://react.dev/learn/state-as-a-snapshot#setting-state-triggers-_render_s) dalam kasus ini, **buat objek _baru_ dan _pass_ (teruskan) ke fungsi _setter_ _state_:**

```js
onPointerMove={(e) => {
  setPosition({
    x: e.clientX,
    y: e.clientY
  });
}}
```

Dengan `setPosition`, kita meminta React untuk:

- Ganti `position` dengan objek baru ini
- Dan _render_ komponen ini lagi

Dengan kode berikut, titik merah sekarang mengikuti _pointer_ kita saat kita menyentuh atau mengarahkan kursor ke area pratinjau:

```js
import { useState } from "react";

export default function MovingDot() {
  const [position, setPosition] = useState({
    x: 0,
    y: 0,
  });

  return (
    <div
      onPointerMove={(e) => {
        setPosition({
          x: e.clientX,
          y: e.clientY,
        });
      }}
      style={{
        position: "relative",
        width: "100vw",
        height: "100vh",
      }}
    >
      <div
        style={{
          position: "absolute",
          backgroundColor: "red",
          borderRadius: "50%",
          transform: `translate(${position.x}px, ${position.y}px)`,
          left: -10,
          top: -10,
          width: 20,
          height: 20,
        }}
      />
    </div>
  );
}
```

<blockquote>

**Memahami Lebih Dalam**

#### Mutasi lokal tidak menyebabkan masalah

Kode seperti berikut merupakan masalah karena memodifikasi objek _yang sudah ada_ dalam _state_:

```js
position.x = e.clientX;
position.y = e.clientY;
```

Sedangkan kode seperti berikut **tidak akan menyebabkan masalah** karena kita mengubah objek baru yang _baru saja kita buat_:

```js
const nextPosition = {};
nextPosition.x = e.clientX;
nextPosition.y = e.clientY;
setPosition(nextPosition);
```

Dan sebenarnya, kode di atas sama persis dengan kode berikut:

```js
setPosition({
  x: e.clientX,
  y: e.klienY,
});
```

Mutasi hanya menjadi masalah saat kita mengubah objek _yang sudah ada_ dalam _state_. Memutasi objek yang baru saja kita buat tidak akan menyebabkan masalah karena _belum ada kode lain yang mereferensikannya._ Mengubahnya tidak akan secara tidak sengaja memengaruhi sesuatu yang bergantung padanya. Ini disebut "mutasi lokal". Kita bahkan dapat melakukan mutasi lokal [saat me-_render_.](https://react.dev/learn/keeping-components-pure#local-mutation-your-components-little-secret)

</blockquote>

## Menyalin objek dengan sintaks _spread_

Pada contoh sebelumnya, objek `position` selalu dibuat baru dari posisi kursor saat ini. Namun seringkali, kita ingin menyertakan data _yang ada_ sebagai bagian dari objek baru yang kita buat. Misalnya, kita mungkin ingin memperbarui _hanya satu_ _field_ dalam _form_, tetapi mempertahankan nilai sebelumnya untuk semua _field_ lainnya.

_Field_ input berikut tidak akan berfungsi karena _handler_ `onChange` memutasikan _state_:

```js
import { useState } from "react";

export default function Form() {
  const [person, setPerson] = useState({
    firstName: "Eko",
    lastName: "Travada",
    email: "etravada@pasim.ac.id",
  });

  function handleFirstNameChange(e) {
    person.firstName = e.target.value;
  }

  function handleLastNameChange(e) {
    person.lastName = e.target.value;
  }

  function handleEmailChange(e) {
    person.email = e.target.value;
  }

  return (
    <>
      <label>
        Nama depan:
        <input value={person.firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Nama belakang:
        <input value={person.lastName} onChange={handleLastNameChange} />
      </label>
      <label>
        Email:
        <input value={person.email} onChange={handleEmailChange} />
      </label>
      <p>
        {person.firstName} {person.lastName} ({person.email})
      </p>
    </>
  );
}
```

Misalnya, baris ini memutasikan _state_ dari _render_ sebelumnya:

```js
person.firstName = e.target.nilai;
```

Cara yang tepat untuk mendapatkan perilaku yang kita inginkan tersebut adalah membuat objek baru dan mem-_pass_-nya ke `setPerson`. Namun di sini, kita juga ingin **menyalin data yang ada ke dalamnya** karena hanya satu _field_ yang berubah:

```js
setPerson({
  firstName: e.target.value, // Nama depan baru dari input
  lastName: person.lastName,
  email: person.email,
});
```

Kita dapat menggunakan sintaks [_object spread_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_object_literals) `...` sehingga kita tidak perlu menyalin setiap properti secara terpisah.

```js
setPerson({
  ...person, // Salin field lama
  firstName: e.target.value, // Tapi ganti yang ini
});
```

Sekarang _form_ berfungsi!

Pada kode ini kita tidak mendeklarasikan variabel _state_ terpisah untuk setiap _field_ input. Untuk _form_ besar (berisi banyak _field_), menjaga agar semua data tetap dikelompokkan dalam sebuah objek juga sangatlah mudah, asalkan kita memperbaruinya dengan benar!

```js
import { useState } from "react";

export default function Form() {
  const [person, setPerson] = useState({
    firstName: "Eko",
    lastName: "Travada",
    email: "etravada@pasim.ac.id",
  });

  function handleFirstNameChange(e) {
    setPerson({
      ...person,
      firstName: e.target.value,
    });
  }

  function handleLastNameChange(e) {
    setPerson({
      ...person,
      lastName: e.target.value,
    });
  }

  function handleEmailChange(e) {
    setPerson({
      ...person,
      email: e.target.value,
    });
  }

  return (
    <>
      <label>
        Nama depan:
        <input value={person.firstName} onChange={handleFirstNameChange} />
      </label>
      <label>
        Nama belakang:
        <input value={person.lastName} onChange={handleLastNameChange} />
      </label>
      <label>
        Email:
        <input value={person.email} onChange={handleEmailChange} />
      </label>
      <p>
        {person.firstName} {person.lastName} ({person.email})
      </p>
    </>
  );
}
```

Perhatikan bahwa sintaks _spread_ `...` bersifat "_shallow_" (dangkal), ini hanya menyalin hal-hal sedalam satu tingkat. Ini membuatnya cepat, tetapi juga berarti jika kita ingin memperbarui properti bersarang, kita harus menggunakannya lebih dari sekali.

<blockquote>

**Memahami Lebih Dalam**

#### Menggunakan satu _event handler_ untuk beberapa _field_

Kita juga dapat menggunakan tanda kurung `[` dan `]` di dalam pembuatan objek untuk menentukan properti dengan nama dinamis. Berikut adalah contoh yang sama, tetapi hanya dengan satu _event handler_, bukan tiga (berbeda-beda untuk masing-masing field):

```js
import { useState } from "react";

export default function Form() {
  const [person, setPerson] = useState({
    firstName: "Eko",
    lastName: "Travada",
    email: "etravada@pasim.ac.id",
  });

  function handleChange(e) {
    setPerson({
      ...person,
      [e.target.name]: e.target.value,
    });
  }

  return (
    <>
      <label>
        Nama depan:
        <input
          name="firstName"
          value={person.firstName}
          onChange={handleChange}
        />
      </label>
      <label>
        Nama belakang:
        <input
          name="lastName"
          value={person.lastName}
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input name="email" value={person.email} onChange={handleChange} />
      </label>
      <p>
        {person.firstName} {person.lastName} ({person.email})
      </p>
    </>
  );
}
```

Pada contoh di atas, `e.target.name` mengacu pada properti `name` yang diberikan ke elemen DOM `<input>`.

</blockquote>

## Memperbarui objek bersarang

Misalnya kita memiliki struktur objek bersarang seperti ini:

```js
const [person, setPerson] = useState({
  name: "Niki de Saint Phalle",
  artwork: {
    title: "Blue Nana",
    city: "Hamburg",
    image: "https://i.imgur.com/Sd1AgUOm.jpg",
  },
});
```

Jika kita ingin memperbarui `person.artwork.city`, cara melakukannya dengan mutasi:

```js
person.artwork.city = "New Delhi";
```

Namun di React, kita memperlakukan _state_ sebagai _immutable_! Untuk mengubah `city`, pertama-tama kita perlu membuat objek `artwork` baru (diisi terlebih dahulu dengan data dari yang sebelumnya), lalu menghasilkan objek `person` baru yang menunjuk ke `artwork` baru:

```js
const nextArtwork = { ...person.artwork, city: "New Delhi" };
const nextPerson = { ...person, artwork: nextArtwork };
setPerson(nextPerson);
```

Atau, ditulis sebagai pemanggilan fungsi tunggal:

```js
setPerson({
  ...person, // Salin field lain
  artwork: {
    // tetapi ganti artwork-nya
    ...person.artwork, // dengan yang sama
    city: "New Delhi", // tapi di New Delhi!
  },
});
```

Cara seperti ini agak bertele-tele, tetapi berfungsi dengan baik untuk banyak kasus:

```js
import { useState } from "react";

export default function Form() {
  const [person, setPerson] = useState({
    name: "Niki de Saint Phalle",
    artwork: {
      title: "Blue Nana",
      city: "Hamburg",
      image: "https://i.imgur.com/Sd1AgUOm.jpg",
    },
  });

  function handleNameChange(e) {
    setPerson({
      ...person,
      name: e.target.value,
    });
  }

  function handleTitleChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        title: e.target.value,
      },
    });
  }

  function handleCityChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        city: e.target.value,
      },
    });
  }

  function handleImageChange(e) {
    setPerson({
      ...person,
      artwork: {
        ...person.artwork,
        image: e.target.value,
      },
    });
  }

  return (
    <>
      <label>
        Nama:
        <input value={person.name} onChange={handleNameChange} />
      </label>
      <label>
        Judul:
        <input value={person.artwork.title} onChange={handleTitleChange} />
      </label>
      <label>
        Kota:
        <input value={person.artwork.city} onChange={handleCityChange} />
      </label>
      <label>
        Gambar:
        <input value={person.artwork.image} onChange={handleImageChange} />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {" oleh "}
        {person.name}
        <br />
        (berlokasi di {person.artwork.city})
      </p>
      <img src={person.artwork.image} alt={person.artwork.title} />
    </>
  );
}
```

<blockquote>

**Memahami Lebih Dalam**

#### Objek bersarang sebenarnya tidak bersarang

Objek seperti berikut tertulis secara "bersarang" dalam suatu kode:

```js
let obj = {
  name: "Niki de Saint Phalle",
  artwork: {
    title: "Blue Nana",
    city: "Hamburg",
    image: "https://i.imgur.com/Sd1AgUOm.jpg",
  },
};
```

Namun, "bersarang" adalah cara yang kurang tepat untuk berpikir tentang perilaku objek. Saat kode dijalankan, tidak ada yang namanya objek "bersarang". Ini sebenarnya dua objek yang berbeda:

```js
let obj1 = {
  title: "Blue Nana",
  city: "Hamburg",
  image: "https://i.imgur.com/Sd1AgUOm.jpg",
};

let obj2 = {
  name: "Niki de Saint Phalle",
  artwork: obj1,
};
```

Objek `obj1` bukan "di dalam" `obj2`. Misalnya, `obj3` juga dapat "menunjuk" ke `obj1`:

```js
let obj1 = {
  title: "Blue Nana",
  city: "Hamburg",
  image: "https://i.imgur.com/Sd1AgUOm.jpg",
};

let obj2 = {
  name: "Niki de Saint Phalle",
  artwork: obj1,
};

let obj3 = {
  name: "Copycat",
  artwork: obj1,
};
```

Jika kita mengubah `obj3.artwork.city`, itu akan memengaruhi `obj2.artwork.city` dan `obj1.city`. Ini karena `obj3.artwork`, `obj2.artwork`, dan `obj1` adalah objek yang sama. Ini sulit dilihat ketika kita menganggap objek sebagai "bersarang". Sebaliknya, mereka adalah objek terpisah yang "menunjuk" satu sama lain dengan properti.

</blockquote>

### Tulis logika pembaruan yang ringkas dengan Immer

Jika _state_ kita bersarang mendalam, kita mungkin ingin mempertimbangkan untuk [meratakannya.](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state) Namun, jika kita tidak ingin mengubah struktur _state_ , kita mungkin lebih memilih pintasan daripada spread bersarang. [Immer](https://github.com/immerjs/use-immer) adalah library populer yang memungkinkan kita menulis menggunakan sintaksis yang nyaman namun bermutasi dan mengurus pembuatan salinannya untuk kita. Dengan Immer, kode yang kita tulis terlihat seperti "melanggar aturan" dan memutasi objek:

```js
updatePerson((draft) => {
  draft.artwork.city = "Lagos";
});
```

Tapi tidak seperti mutasi biasa, itu tidak menimpa _state_ sebelumnya!

<blockquote>

**Memahami Lebih Dalam**

#### Bagaimana cara kerja Immer?

`Draf` yang disediakan oleh Immer adalah jenis objek khusus, yang disebut [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy), yang "mencatat "apa yang kau lakukan dengannya. Inilah mengapa kita dapat memutasikannya dengan bebas sebanyak yang kita suka! Di bawah tenda, Immer mengetahui bagian mana dari `draf` yang telah diubah, dan menghasilkan objek yang benar-benar baru yang berisi suntingan kita.

</blockquote>

Untuk mencoba Immer:

1. Jalankan `pnpm add use-immer` untuk menambahkan Immer sebagai dependensi
2. Lalu ganti `import { useState } from 'react'` dengan `import { useImmer } from 'use-immer'`

Berikut adalah contoh di atas yang dikonversi ke Immer:

```js
import { useImmer } from "use-immer";

export default function Form() {
  const [person, updatePerson] = useImmer({
    name: "Niki de Saint Phalle",
    artwork: {
      title: "Blue Nana",
      city: "Hamburg",
      image: "https://i.imgur.com/Sd1AgUOm.jpg",
    },
  });

  function handleNameChange(e) {
    updatePerson((draft) => {
      draft.name = e.target.value;
    });
  }

  function handleTitleChange(e) {
    updatePerson((draft) => {
      draft.artwork.title = e.target.value;
    });
  }

  function handleCityChange(e) {
    updatePerson((draft) => {
      draft.artwork.city = e.target.value;
    });
  }

  function handleImageChange(e) {
    updatePerson((draft) => {
      draft.artwork.image = e.target.value;
    });
  }

  return (
    <>
      <label>
        Nama:
        <input value={person.name} onChange={handleNameChange} />
      </label>
      <label>
        Judul:
        <input value={person.artwork.title} onChange={handleTitleChange} />
      </label>
      <label>
        Kota:
        <input value={person.artwork.city} onChange={handleCityChange} />
      </label>
      <label>
        Gambar:
        <input value={person.artwork.image} onChange={handleImageChange} />
      </label>
      <p>
        <i>{person.artwork.title}</i>
        {" oleh "}
        {person.name}
        <br />
        (berlokasi di {person.artwork.city})
      </p>
      <img src={person.artwork.image} alt={person.artwork.title} />
    </>
  );
}
```

Perhatikan betapa lebih ringkasnya penulisan _event handler_. Kita dapat mencampur dan mencocokkan `useState` dan `useImmer` dalam satu komponen sebanyak yang kita suka. Immer adalah cara yang bagus untuk menjaga _handler_ pembaruan tetap ringkas, terutama jika ada _nesting_ di _state_ kita, dan menyalin objek menyebabkan penulisan ulang kode.

<blockquote>

**Memahami Lebih Dalam**

#### Mengapa memutasikan _state_ tidak direkomendasikan di React?

Ada beberapa alasan:

- **Debugging:** Jika kita menggunakan `console.log` dan tidak memutasikan _state_, log lama kita tidak akan terpengaruh oleh perubahan _state_ yang lebih baru. Jadi, kita dapat dengan jelas melihat bagaimana _state_ telah berubah di antara pe_render_an.
- **Pengoptimalan:** [Strategi pengoptimalan](https://react.dev/reference/react/memo) React umumnya bergantung pada melewatkan pekerjaan jika properti atau _state_ sebelumnya sama dengan yang berikutnya. Jika kita tidak pernah mengubah _state_, sangat cepat untuk memeriksa apakah ada perubahan. Jika `prevObj === obj`, kita dapat yakin bahwa tidak ada yang berubah di dalamnya.
- **Fitur Baru:** Fitur React baru yang kami buat bergantung pada _state_ yang [diperlakukan seperti _snapshot_.](https://react.dev/learn/state-as-a-snapshot) Jika kita mengubah versi _state_ sebelumnya, itu dapat mencegah kita menggunakan fitur baru.
- **Perubahan Persyaratan:** Beberapa fitur aplikasi, seperti mengimplementasikan Undo/Redo, menampilkan riwayat perubahan, atau mengizinkan pengguna me-_reset_ _form_ ke nilai sebelumnya, lebih mudah dilakukan saat tidak ada yang dimutasi. Ini karena kita dapat menyimpan salinan _state_ sebelumnya di memori, dan menggunakannya kembali jika perlu. Jika kita memulai dengan pendekatan mutatif, fitur seperti ini mungkin akan sulit ditambahkan di kemudian hari.
- **Implementasi Lebih Sederhana:** Karena React tidak bergantung pada mutasi, React tidak perlu melakukan sesuatu yang khusus dengan objek kita. Itu tidak perlu "membajak" propertinya, selalu membungkusnya menjadi Proxy, atau melakukan pekerjaan lain saat inisialisasi seperti yang dilakukan oleh banyak solusi "reaktif". Ini juga mengapa React memungkinkan kita menempatkan objek apa pun ke dalam _state_, tidak peduli seberapa besar, tanpa kinerja tambahan atau jebakan kebenaran.

Dalam praktiknya, kita sering kali dapat "lolos" dengan memutasikan _state_ di React, tetapi sangat disarankan untuk tidak melakukannya agar kita dapat menggunakan fitur React baru yang dikembangkan dengan mempertimbangkan pendekatan ini.

</blockquote>

Ringkasan:

- Perlakukan semua _state_ di React sebagai _immutable_ (tidak dapat diubah).
- Saat kita menyimpan objek dalam _state_, memutasikannya tidak akan memicu _render_ dan hanya akan mengubah _state_ dalam "_snapshot_" _render_ sebelumnya.
- Jangan memutasi objek, melainkan kita buat versi _baru_ darinya, dan picu _render_ ulang dengan meng-_set_ _state_ ke objek tersebut.
- Kita dapat menggunakan sintaks _object spread_ `{...obj, something: 'newValue'}` untuk membuat salinan objek.
- Sintaks _spread_ bersifat _shallow_ (dangkal): ini hanya menyalin sedalam satu tingkat.
- Untuk memperbarui objek bersarang, kita perlu membuat salinan dari tempat yang kita perbarui.
- Untuk mengurangi penyalinan kode berulang, gunakan Immer.
