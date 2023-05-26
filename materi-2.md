# **Materi 2: React, Vite, komponen, dan JSX**

# Silabus

| Pertemuan | Materi                                                           |
| --------- | ---------------------------------------------------------------- |
| 1         | JavaScript, Node.js, `npm`, dan `pnpm`                           |
| 2         | React, Vite, komponen, dan JSX                                   |
| 3         | Menambahkan style (CSS) dan menampilkan data (kurung kurawal)    |
| 4         | Render kondisional (operator ternary dan logical)                |
| 5         | Render list (method `map()` array)                               |
| 6         | Event dan `useState()`                                           |
| 7         | Sharing data antarkomponen (`props`)                             |
| 8         | React Router dan deployment (Vercel)                             |
| **9**     | **UTS**                                                          |
| 10        | Tailwind CSS                                                     |
| 11        | Memperdalam event dan `useState()` (memperbarui objek dan array) |
| 12        | `useReducer()` dan `useContext()`                                |
| 13        | `useRef()` dan `useEffect()`                                     |
| 14        | REST API (Express), CRUD, dan deployment (Vercel)                |
| 15        | Basis data (Supabase, PostgreSQL) dan `.env`                     |
| 16        | Otentikasi (JWT, cookies, dan hash kata sandi)                   |
| 17        | TypeScript                                                       |
| **18**    | **Projekan**                                                     |

# Daftar isi

- [**Materi 2: React, Vite, komponen, dan JSX**](#materi-2-react-vite-komponen-dan-jsx)
- [Silabus](#silabus)
- [Daftar isi](#daftar-isi)
- [Pengenalan React](#pengenalan-react)
- [Vite](#vite)
  - [Menginisialisasi project Vite](#menginisialisasi-project-vite)
  - [Memahami isi dari template Vite](#memahami-isi-dari-template-vite)
  - [Menjalankan aplikasi Vite](#menjalankan-aplikasi-vite)
  - [Memulai dari awal](#memulai-dari-awal)
- [Hello, universe!](#hello-universe)
- [Pengenalan JSX](#pengenalan-jsx)
  - [Menyematkan statement (pernyataan) di JSX](#menyematkan-statement-pernyataan-di-jsx)
  - [JSX juga statement](#jsx-juga-statement)
  - [Menentukan atribut dengan JSX](#menentukan-atribut-dengan-jsx)
  - [Menentukan child dengan JSX](#menentukan-child-dengan-jsx)
- [Component dan property](#component-dan-property)
  - [Merender component](#merender-component)
  - [Menyusun component](#menyusun-component)

# Pengenalan React

React (sebelumnya bernama "React.js") adalah library JavaScript open source untuk mempermudah kita dalam membangun user interface (UI) web.

React memungkinkan kita membangun user interface dari bagian-bagian individual yang disebut "komponen". Kita dapat membuat komponen React sendiri seperti `Thumbnail`, `LikeButton`, dan `Video`. Kemudian komponen-komponen tersebut dapat kita gabungkan ke seluruh layar, halaman, dan aplikasi yang kita bangun.

Contoh:

```jsx
function Video({ video }) {
  return (
    <div>
      <Thumbnail video={video} />
      <a href={video.url}>
        <h3>{video.title}</h3>
        <p>{video.description}</p>
      </a>
      <LikeButton video={video} />
    </div>
  );
}
```

Output:

![](images/react/video.png)

React dirancang untuk memungkinkan kita menggabungkan komponen-komponen yang ditulis oleh orang, tim, dan organisasi lain dengan mudah.

Komponen React sebenarnya adalah function JavaScript. Untuk menampilkan beberapa konten secara kondisional kita dapat menggunakan pernyataan `if`. Untuk menampilkan daftar kita dapat menggunakan method `map()` array. Belajar React sama seperti belajar pemrograman.

```jsx
function DaftarVideo({ videos, emptyHeading }) {
  const count = videos.length;
  let heading = emptyHeading;
  if (count > 0) {
    const noun = count > 1 ? "Videos" : "Video";
    heading = count + " " + noun;
  }
  return (
    <section>
      <h2>{heading}</h2>
      {videos.map((video) => (
        <Video key={video.id} video={video} />
      ))}
    </section>
  );
}
```

Output:

![](images/react/video-list.png)

React menggunakan sintaks _markup_ yang disebut "JSX". Ini adalah perluasan sintaks JavaScript yang dipopulerkan oleh React. Sintaks JSX ini membuat komponen React mudah dibuat, dikelola, dan dihapus.

Komponen React menerima data dan mengembalikan apa yang seharusnya muncul di layar. Kita dapat meneruskan data baru ke komponen-komponen itu sebagai respons terhadap interaksi, seperti saat pengguna mengetik input. React kemudian akan memperbarui layar agar sesuai dengan data baru.

# Vite

Vite (kata dari bahasa Perancis yang berarti "cepat", dilafalkan `/vit/`, bukan `/vaɪt/`) adalah alat build yang bertujuan untuk memberikan pengalaman pengembangan yang lebih cepat dan lebih ramping (tidak banyak dependensi) untuk project web modern.

Vite dapat digunakan dengan framework apapun (termasuk React, walaupun sebenarnya React bukan framework, melainkan library).

Sebelum adanya Vite, developer React biasanya menggunakan `create-react-app` (CRA) untuk membangun project React. Namun alat tersebut memiliki banyak masalah performa. Setelah kemunculan Vite, hampir semua developer beralih ke Vite.

## Menginisialisasi project Vite

1. Jalankan perintah:

   ```shell
   > pnpm create vite
   ```

   Tunggu sampai proses download selesai.

2. Masukkan nama project, contoh: `vite-project`.
3. Pilih framework yang ingin kita gunakan, yaitu `React` (gunakan tombol atas-bawah di keyboard lalu enter untuk memilih).
4. Pilih varian yang ingin kita gunakan, yaitu `JavaScript`.

   `pnpm` akan menghasilkan folder dengan nama sesuai nama project yang kita masukkan tadi. Folder tersebut berisi file-file awal dari template yang kita pilih tadi.

5. Buka folder yang dihasilkan tadi di VS Code.

## Memahami isi dari template Vite

Saat kita menginisialisasi project Vite, kita akan diberi template _starter kit_:

1. Folder `public`, ini adalah folder statis, artinya file-file di dalamnya dapat diakses langsung dari URL. Misalnya file `vite.svg` dapat diakses langsung melalui `http://localhost:5173/vite.svg`.

2. Folder `src`, kita akan fokus ke folder ini, karena file-file di folder inilah yang akan **sering kita ubah**, di sinilah kita akan membuat component-component React.

3. File `.eslintrc.cjs`, yaitu konfigurasi ESLint, ESLint adalah alat untuk memeriksa kesalahan pada kode kita.

4. File `.gitignore`

5. File `index.html`, ini adalah file berisi struktur dasar aplikasi kita, kemungkinan besar kita **tidak akan pernah mengubahnya**.

6. File `package.json`

7. File `vite.config.js`, yaitu konfigurasi Vite, biasanya untuk memasang plugin Vite.

## Menjalankan aplikasi Vite

Jika kita membuka file `package.json`, kita akan melihat beberapa dependensi. Perintah `pnpm create vite` tidak secara otomatis mendownload semua dependensi tersebut.

Kita perlu mendownload semua dependensi tersebut dengan perintah:

```shell
> pnpm install
```

Perintah di atas akan mendownload semua dependensi dan menghasilkan folder `node_modules`.

Sekarang project siap dijalankan:

1. Jalankan perintah `pnpm dev` (ini akan menjalankan script `dev` di `package.json`).

2. Buka link yang muncul di terminal (misalnya http://localhost:5173/) dengan `Ctrl` + klik.

## Memulai dari awal

Template Vite telah memberi kita contoh yang bagus berupa aplikasi React sederhana. Namun kita tidak akan pernah paham cara kerjanya tanpa memulainya dari awal.

Langkah-langkah yang direkomendasikan untuk memulai dari awal (fokus ke folder `src`):

1. Hapus file `App.css` (ini adalah CSS untuk `App.jsx`)

2. Kosongkan isi dari file `index.css` (ini adalah CSS global)

3. Kosongkan isi dari file `App.jsx`

Aplikasi mungkin akan error karena file `main.jsx` mengimpor komponen dari `App.jsx` untuk ditampilkan, sedangkan `App.jsx` telah kita kosongkan. Tapi ini akan segera teratasi setelah kita membuat komponen pertama kita di `App.jsx`.

# Hello, universe!

Sebagai pembukaan, kita akan menyapa alam semesta dengan komponen pertama kita di dalam file `App.jsx`.

Komponen React adalah function yang dinamai dengan `PascalCase` yang mengembalikan markup JSX (sintaks mirip HTML), misalnya bernama `App`:

```jsx
function App() {
  return <h1>Hello, universe!</h1>;
}
```

Komponen `App` perlu diekspor karena komponen tersebut perlu diimpor oleh file `main.jsx` untuk ditampilkan:

```jsx
function App() {
  return <h1>Hello, universe!</h1>;
}

export default App;
```

Kode di atas seharusnya sudah bisa menampilkan "Hello, universe!".

Kita juga dapat membuat komponen lain di file yang sama, misalnya bernama `MyButton`:

```jsx
function MyButton() {
  return <button>Aku sebuah tombol</button>;
}
```

Kita dapat menyarangkan (_nesting_) `MyButton` ke dalam komponen `App`. Misalnya kita ingin meletakannya di bawah tulisan "Hello, universe!". Namun karena komponen React hanya dapat memiliki satu root (elemen terluar), kita perlu menyarangkan keduanya di sebuah elemen, misalnya `<div>`. Untuk markup JSX dengan banyak baris, disarankan menggunakan tanda kurung:

```jsx
function App() {
  return (
    <div>
      <h1>Hello, universe!</h1>
      <MyButton />
    </div>
  );
}
```

Walapun satu file dapat memiliki beberapa komponen, sebaiknya buat file untuk masing-masing komponen. Misalnya pisahkan komponen `MyButton` di file sendiri dengan nama yang sama, yaitu `MyButton.jsx`. Kemudian ekspor dengan `export default` sehingga kita dapat mengimpornya ke `App.jsx`.

# Pengenalan JSX

Perhatikan inisialisasi variabel berikut.

```jsx
const element = <h1>Hello, universe!</h1>;
```

Sintaks mirip HTML yang dimasukkan ke variabel `element` di atas bukan string atau HTML, melainkan JSX.

JSX merupakan perluasan sintaks untuk JavaScript. Kita perlu menggunakannya dengan React untuk membangun tampilan UI. JSX mungkin mengingatkan kita tentang bahasa template, seperti Blade (di Laravel), Blazor (di ASP.NET), Thymeleaf (di Spring), dll., tetapi yang ini menggunakan JavaScript.

## Menyematkan statement (pernyataan) di JSX

Pada contoh berikut, kita mendeklarasikan variabel bernama `name` dan kemudian menggunakannya di dalam JSX dengan membungkusnya dalam kurung kurawal:

```jsx
const name = "Elon Musk";
const element = <h1>Hello, {name}!</h1>;
```

Kita dapat menempatkan statement JavaScript apapun (yang valid) di dalam kurung kurawal di JSX. Misalnya, `2 + 2`, `user.firstName`, atau `formatName(user)`.

Pada contoh berikut, kita menyematkan hasil pemanggilan function JavaScript, yaitu `formatName(user)`, ke dalam element `<h1>`.

```jsx
function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = {
  firstName: "Elon",
  lastName: "Musk",
};

const element = <h1>Hello, {formatName(user)}!</h1>;
```

Kita membagi JSX menjadi beberapa baris agar mudah dibaca. JSX yang lebih dari 1 baris disarankan untuk membungkusnya dalam tanda kurung untuk menghindari masalah titik koma.

## JSX juga statement

JSX juga merupakan statement. Ini berarti kita dapat menggunakannya di dalam decision dan loop, memasukkannya ke variabel, menerimanya sebagai argument, dan mengembalikannya dari function:

```jsx
function getGreeting(user) {
  if (user) {
    // jika user berisi
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  // jika user kosong
  return <h1>Hello, Stranger.</h1>;
}
```

## Menentukan atribut dengan JSX

Kita dapat menggunakan tanda kutip untuk menentukan literal string sebagai atribut:

```jsx
const link = <a href="https://www.pubpasim.org">Buka website PUB</a>;
```

Kita juga dapat menggunakan kurung kurawal untuk menyematkan statement JavaScript dalam sebuah atribut:

```jsx
const image = <img src={user.avatarUrl}></img>;
```

Jangan beri tanda kutip di sekitar kurung kurawal saat menyematkan statement JavaScript dalam sebuah atribut. Gunakan tanda kutip (untuk nilai string) atau kurung kurawal (untuk statement), tetapi tidak keduanya (dalam atribut yang sama).

> **Peringatan**
>
> Karena JSX lebih dekat dengan JavaScript daripada HTML, penamaan property menggunakan konvensi `camelCase`, bukan nama atribut HTML.
>
> Misalnya, `class` menjadi `className` di JSX, dan `tabindex` menjadi `tabIndex`.

## Menentukan child dengan JSX

Jika element tidak memiliki child, kita perlu menutupnya dengan `/>` (seperti XML):

```jsx
const element = <img src={user.avatarUrl} />;
```

Jika element memiliki child, maka penulisannya seperti HTML biasa:

```jsx
const element = (
  <div>
    <h1>Halo!</h1>
    <h2>Selamat datang di PUB Portal!</h2>
  </div>
);
```

# Component dan property

Component memungkinkan kita membagi UI menjadi bagian-bagian independen yang dapat digunakan kembali, dan memikirkan setiap bagian secara terpisah.

Konsep component seperti function JavaScript. Component menerima argument (disebut "props") dan mengembalikan element React yang akan muncul di layar.

Cara mendefinisikan component adalah dengan menulis function JavaScript:

```jsx
function Welcome(props) {
  return <h1>Halo {props.name}!</h1>;
}
```

Function di atas adalah component React yang valid karena menerima argument objek "props" (yang berarti properties) tunggal dengan data dan mengembalikan element React. Component itu disebut "function component" karena secara harfiah merupakan function JavaScript.

## Merender component

Sebelumnya, element React yang kita buat hanya mewakili tag DOM:

```jsx
const element = <div />;
```

Namun, element juga dapat mewakili component yang telah kita buat:

```jsx
const element = <Welcome name="Muzayyin" />;
```

Ketika terdapat element yang mewakili component, atribut JSX dan child-child-nya diteruskan ke component ini melalui argumen sebagai objek tunggal. Objek ini disebut "props".

Misalnya, kode berikut merender "Halo Muzayyin!" di halaman:

```jsx
function Welcome(props) {
  return <h1>Halo {props.name}!</h1>;
}
```

```jsx
const root = ReactDOM.createRoot(document.getElementById("root"));
const element = <Welcome name="Muzayyin" />;
root.render(element);
```

> **Catatan**
>
> Selalu awali nama component dengan huruf kapital.

## Menyusun component

Kita dapat menggunakan component di dalam component. Di aplikasi React, tombol, form, dialog, layar, semua itu biasanya dibuat sebagai component.

Misalnya, kita dapat membuat component `App` yang merender `Welcome` berkali-kali:

```jsx
function Welcome(props) {
  return <h1>Halo {props.name}!</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Muzayyin" />
      <Welcome name="Anti" />
      <Welcome name="Yusfa" />
    </div>
  );
}
```

Biasanya, aplikasi React yang baru memiliki satu component `App`.
