# **Materi 3: _Style_ dalam JSX, _Conditional & List Rendering_, dan Menanggapi _Event_**

# Daftar isi

- [**Materi 3: _Style_ dalam JSX, _Conditional \& List Rendering_, dan Menanggapi _Event_**](#materi-3-style-dalam-jsx-conditional--list-rendering-dan-menanggapi-event)
- [Daftar isi](#daftar-isi)
- [_Style_ (CSS) dalam JSX](#style-css-dalam-jsx)
  - [_External stylesheet_](#external-stylesheet)
  - [_Inline style_](#inline-style)
  - [Tailwind CSS](#tailwind-css)
- [Conditional Rendering](#conditional-rendering)
- [_List Rendering_](#list-rendering)
- [Menanggapi _Event_](#menanggapi-event)

# _Style_ (CSS) dalam JSX

Ada 3 cara umum menambahkan style dalam JSX:

1. Eksternal
2. Inline
3. Tailwind CSS

## _External stylesheet_

_External stylesheet_ adalah CSS dalam file terpisah berekstensi `.css`.

Dalam HTML, _external stylesheet_ diimpor menggunakan elemen `<link>`:

```html
<html lang="id-ID">
  <head>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
</html>
```

React maupun JSX tidak menyediakan cara untuk mengimpor _external stylesheet_, ini tergantung alat build yang kita gunakan. Dalam Vite, _external stylesheet_ diimpor menggunakan sintaks `import`:

```jsx
import "./Header.css";

function Header() {
  return (
    <header>
      <span className="logo">Andromeda</span>
      <button>Login</button>
    </header>
  );
}
```

CSS:

```css
header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  > .logo {
    color: navy;
  }

  > button {
    color: white;
    background-color: navy;
    padding: 8px;

    &:hover {
      color: navy;
      background-color: white;
    }
  }
}
```

## _Inline style_

_Inline style_ adalah pemberian _style_ pada elemen dengan atribut `style` seperti di HTML:

```html
<button style="background-color: navy; padding: 8px;">Login</button>
```

Dalam JSX, _inline style_ berupa objek dengan properti dalam `camelCase` dan _value_-nya berupa _string_ (atau _number_ jika dalam piksel):

```jsx
<button style={{ backgroundColor: "navy", padding: 8 }}>Login</button>
```

Atau dapat juga ditampung di variabel terlebih dahulu:

```jsx
const styles = { backgroundColor: "navy", padding: 8 };

function LoginButton() {
  return <button style={styles}>Login</button>;
}
```

_Inline style_ adalah metode yang **sebaiknya dihindari**, karena sangat tidak efisien dan sulit dibaca.

## Tailwind CSS

Tailwind CSS adalah _framework_ CSS untuk memberi _style_ pada halaman web dengan _class_-_class_ yang telah disediakan seperti `flex`, `pt-4`, `text-center`, and `rotate-90`, sehingga proses development menjadi lebih cepat daripada menulis CSS secara langsung.

Tailwind CSS akan dipelajari di pertemuan 10.

# Conditional Rendering

_Conditional rendering_ (perenderan bersyarat) maksudnya adalah menampilkan sesuatu dalam JSX berdasarkan syarat/kondisi.

Di React, tidak ada sintaks khusus untuk menulis syarat/kondisi, kondisi ditulis menggunakan pernyataan `if...else` seperti JavaScript biasa:

```jsx
let content;

// memeriksa apakah pengguna sudah login
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}

return <div>{content}</div>;
```

Metode yang lebih ringkas adalah menggunakan operator _ternary_ dan _binary logical_:

```jsx
// TERNARY

<div>{isLoggedIn ? <AdminPanel /> : <LoginForm />}</div>
```

```jsx
// BINARY LOGICAL

// and
<div>{isLoggedIn && <AdminPanel />}</div>

// or
<div>{isLoggedIn || <LoginForm />}</div>

// nullish coalescing
<div>{user ?? <LoginForm />}</div>
```

Metode ringkas di atas **lebih umum digunakan** daripada pernyataan `if...else`.

# _List Rendering_

_List rendering_ (perenderan daftar) maksudnya adalah menampilkan sekumpulan sesuatu dalam JSX.

Untuk merender daftar komponen, kita akan mengandalkan fitur bawaan JavaScript seperti loop `for` dan method `map()` pada _array_.

Misalnya, kita memiliki _array_ berisi sekumpulan objek produk:

```jsx
const products = [
  { title: "Kubis", id: 1 },
  { title: "Bawang putih", id: 2 },
  { title: "Apel", id: 3 },
];
```

Gunakan method `map()` untuk mengubah _array_ berisi sekumpulan objek produk menjadi _array_ berisi sekumpulan item `<li>`:

```jsx
const listItems = products.map((product) => (
  <li key={product.id}>{product.title}</li>
));

return <ul>{listItems}</ul>;
```

Perhatikan, elemen `<li>` pada contoh di atas memiliki atribut `key`. Karena untuk setiap item dalam daftar, kita harus memberikan nilai yang secara unik mengidentifikasi item tersebut di antara _sibling_-_sibling_-nya. Biasanya, _key_ harus berasal dari data kita sendiri, misalnya ID dari basis data. React menggunakan _key_-_key_ tersebut untuk mengetahui apa yang seharusnya terjadi jika nanti kita memasukkan, menghapus, atau menyusun ulang item.

# Menanggapi _Event_

Kita dapat "menanggapi _event_" dengan mendeklarasikan _function_ _event handler_ di dalam komponen:

```jsx
function MyButton() {
  function handleClick() {
    alert("Berhasil!");
  }

  return <button onClick={handleClick}>Klik di sini</button>;
}
```

Perhatikan, `onClick={handleClick}` pada contoh di atas tidak terdapat tanda kurung di akhir nama _function_. Menambahkan tanda kurung di akhir artinya memanggil _function_ tersebut. Kita hanya perlu _meneruskannya_ (menjadikannya nilai atribut). React akan memanggil _event handler_ tersebut ketika _event_ terjadi, yaitu saat pengguna mengeklik tombol.
