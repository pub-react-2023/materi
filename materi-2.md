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
- [Pengenalan JSX](#pengenalan-jsx)
  - [Menyematkan statement (pernyataan) di JSX](#menyematkan-statement-pernyataan-di-jsx)
  - [JSX juga statement](#jsx-juga-statement)
  - [Menentukan atribut dengan JSX](#menentukan-atribut-dengan-jsx)
  - [Menentukan child dengan JSX](#menentukan-child-dengan-jsx)
- [Rendering element](#rendering-element)
  - [Merender element ke dalam DOM](#merender-element-ke-dalam-dom)
  - [Memperbarui element yang dirender](#memperbarui-element-yang-dirender)
  - [React hanya memperbarui apa yang perlu diperbarui](#react-hanya-memperbarui-apa-yang-perlu-diperbarui)
- [Component dan property](#component-dan-property)
  - [Merender component](#merender-component)
  - [Menyusun component](#menyusun-component)

# Pengenalan React

React (sebelumnya bernama "React.js") adalah library JavaScript open source untuk mempermudah kita dalam membangun user interface (UI) web. React memungkinkan kita membangun user interface dari bagian-bagian individual yang disebut "component". Sebelum adanya Next.js (framework React), React umumnya digunakan untuk membangun _single-page application_ (SPA), yaitu aplikasi satu halaman.

React memungkinkan kita membangun user interface dari bagian-bagian individual yang disebut "komponen". Kita dapat membuat komponen React sendiri seperti `Thumbnail`, `TombolSuka`, dan `Video`. Kemudian komponen-komponen tersebut dapat kita gabungkan ke seluruh layar, halaman, dan aplikasi yang kita bangun.

React dirancang untuk memungkinkan kita menggabungkan komponen-komponen yang ditulis oleh orang, tim, dan organisasi lain dengan mudah.

Komponen React sebenarnya adalah function JavaScript. Untuk menampilkan beberapa konten secara kondisional kita dapat menggunakan pernyataan `if`. Untuk menampilkan daftar kita dapat menggunakan method `map()` array. Belajar React sama seperti belajar pemrograman.

React menggunakan sintaks _markup_ yang disebut "JSX". Ini adalah perluasan sintaks JavaScript yang dipopulerkan oleh React. Sintaks JSX ini membuat komponen React mudah dibuat, dikelola, dan dihapus.

Komponen React menerima data dan mengembalikan apa yang seharusnya muncul di layar. Kita dapat meneruskan data baru ke komponen-komponen itu sebagai respons terhadap interaksi, seperti saat pengguna mengetik input. React kemudian akan memperbarui layar agar sesuai dengan data baru.

# Vite

Vite (kata dari bahasa Perancis yang berarti "cepat", dilafalkan `/vit/`, bukan `/vaɪt/`) adalah alat build yang bertujuan untuk memberikan pengalaman pengembangan yang lebih cepat dan lebih ramping (tidak banyak dependensi) untuk project web modern.

Sebelum adanya Vite, developer React biasanya menggunakan `create-react-app` (CRA) untuk membangun project React. Namun setelah kemunculan Vite, hampir semua beralih ke Vite.

## Menginisialisasi project Vite

Dengan `npm`:

```shell
$ npm create vite@latest
```

Dengan `pnpm`:

```shell
$ pnpm create vite
```

Kemudian ikuti petunjuknya!

Perintah di atas akan menghasilkan project berdasarkan template yang kita pilih.

# Pengenalan JSX

Perhatikan deklarasi variabel berikut.

```jsx
const element = <h1>Hello universe!</h1>;
```

Sintaks tag di atas bukan string atau HTML, melainkan JSX.

JSX merupakan perluasan sintaks untuk JavaScript. Kita perlu menggunakannya dengan React untuk membangun tampilan UI. JSX mungkin mengingatkan kita tentang bahasa template, seperti Blade (di Laravel), Blazor (di ASP.NET), Thymeleaf (di Spring), dll., tetapi yang ini menggunakan JavaScript.

## Menyematkan statement (pernyataan) di JSX

Pada contoh berikut, kita mendeklarasikan variabel bernama `name` dan kemudian menggunakannya di dalam JSX dengan membungkusnya dalam kurung kurawal:

```jsx
const name = "Indah Mentari";
const element = <h1>Hello {name}!</h1>;
```

Kita dapat menempatkan statement JavaScript apapun (yang valid) di dalam kurung kurawal di JSX. Misalnya, `2 + 2`, `user.firstName`, atau `formatName(user)`.

Pada contoh berikut, kita menyematkan hasil pemanggilan function JavaScript, yaitu `formatName(user)`, ke dalam element `<h1>`.

```jsx
function formatName(user) {
  return user.firstName + " " + user.lastName;
}

const user = {
  firstName: "Indah",
  lastName: "Mentari",
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

# Rendering element

## Merender element ke dalam DOM

Misalnya ada sebuah `<div>` di file HTML kita:

```html
<div id="root"></div>
```

`<div>` itu disebut "element DOM root" karena semua yang ada di dalamnya akan dikelola oleh React DOM.

Aplikasi yang dibuat dengan React biasanya hanya memiliki satu element DOM root. Tapi sebenarnya kita bisa menggunakan element DOM root sebanyak yang kita inginkan.

Untuk merender element React, pertama-tama panggil `ReactDOM.createRoot()` dan jadikan element DOM root sebagai argumentnya, lalu panggil `root.render()` dan jadikan element React sebagai argumentnya:

```jsx
const root = ReactDOM.createRoot(document.getElementById("root"));

const element = <h1>Hello world!</h1>;

root.render(element);
```

Itu akan menampilkan "Hello world!" di halaman.

## Memperbarui element yang dirender

Element React tidak dapat diubah. Setelah kita membuat element, kita tidak dapat mengubah child atau atributnya.

Salah satu cara untuk memperbarui UI adalah dengan membuat element baru kemudian memanggil `root.render()` dengan argument berupa element baru tersebut.

Perhatikan contoh jam berikut:

```jsx
const root = ReactDOM.createRoot(document.getElementById("root"));

function tambahDetik() {
  const element = (
    <div>
      <h1>Halo!</h1>
      <h2>Sekarang pukul {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  root.render(element);
}

setInterval(tambahDetik, 1000);
```

Kode di atas memanggil `root.render()` setiap detik dari callback `setInterval()`.

> **Catatan**
>
> Dalam praktiknya, sebagian besar aplikasi React hanya memanggil `root.render()` sekali saja. Pada materi berikutnya kita akan mempelajari penggunaan `useState()`.

## React hanya memperbarui apa yang perlu diperbarui

React DOM membandingkan element dan child-child-nya dengan yang sebelumnya, dan hanya menerapkan pembaruan DOM yang diperlukan untuk membawa DOM ke keadaan yang diinginkan.

Dari contoh sebelumnya, meskipun kita membuat element yang berisi keseluruhan struktur UI pada setiap detik, hanya teks yang isinya telah diubah yang akan diperbarui oleh React DOM.

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
