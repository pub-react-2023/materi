# **Materi 7: Pemformatan, Filter & Pencarian, _Sorting_, dan _Pagination_**

# Daftar Isi

- [**Materi 7: Pemformatan, Filter \& Pencarian, _Sorting_, dan _Pagination_**](#materi-7-pemformatan-filter--pencarian-sorting-dan-pagination)
- [Daftar Isi](#daftar-isi)
  - [_Props_ Lanjutan](#props-lanjutan)
    - [Membaca _props_ dengan _destructuring_](#membaca-props-dengan-destructuring)
    - [Meneruskan _props_ dengan _spread_](#meneruskan-props-dengan-spread)
  - [Pemformatan](#pemformatan)
    - [Jumlah](#jumlah)
      - [Persentase](#persentase)
      - [Mata uang](#mata-uang)
    - [Tanggal dan waktu](#tanggal-dan-waktu)
  - [Filter dan Pencarian](#filter-dan-pencarian)
  - [_Pagination_](#pagination)

## _Props_ Lanjutan

### Membaca _props_ dengan _destructuring_

Misalnya kita memiliki komponen `Product` yang perlu membaca _props_:

```jsx
export default function Product(props) {
  return (
    <div>
      <img src={props.image} alt={props.name} />
      <section>
        <h2>{props.name}</h2>
        <p>{props.price}</p>
        <button>Beli</button>
      </section>
    </div>
  );
}
```

Kita dapat men-_destructuring_ properti-properti dalam objek `props` menjadi variabel-variabel tersendiri:

```jsx
export default function Product(props) {
  const { name, image, price } = props;

  return (
    <div>
      <img src={image} alt={name} />
      <section>
        <h2>{name}</h2>
        <p>{price}</p>
        <button>Beli</button>
      </section>
    </div>
  );
}
```

Kita juga dapat melakukan _destructuring_ langsung di parameter:

```jsx
export default function Product({ name, image, price }) {
  return (
    <div>
      <img src={image} alt={name} />
      <section>
        <h2>{name}</h2>
        <p>{price}</p>
        <button>Beli</button>
      </section>
    </div>
  );
}
```

### Meneruskan _props_ dengan _spread_

Misalnya kita ingin merender _list_ produk:

```jsx
<main>
  {products.map((product) => (
    <Product
      key={product.id}
      name={product.name}
      image={product.image}
      price={product.price}
    />
  ))}
</main>
```

Sintaks _spread_ dapat digunakan untuk meneruskan semua properti objek dengan lebih singkat:

```jsx
<main>
  {products.map((product) => (
    <Product key={product.id} {...product} />
  ))}
</main>
```

## Pemformatan

### Jumlah

Dalam EYD V, tanda titik digunakan untuk memisahkan bilangan ribuan atau kelipatannya yang menunjukkan jumlah.

Pemformatan angka terutama bertujuan untuk menambahkan pemisah desimal di setiap 3 digit dari belakang.

Misalnya kita ingin merender diameter galaksi:

```jsx
<p>{diameter}</p>
// output: 152000
```

Kita dapat menformatnya dengan _method_ `toLocaleString()`:

```jsx
<p>{price.toLocaleString()}</p>
// output: 152.000
```

_Method_ di atas akan memformat bilangan sesuai dengan pengaturan di komputer pengguna. Jadi bergantung pada lokal yang mereka pilih. Jadi jika komputer pengguna menggunakan lokal bahasa Inggris Amerika, maka pemisah desimal yang digunakan adalah koma (`,`), bukan titik (`.`):

```jsx
<p>{price.toLocaleString()}</p>
// output: 152,000
```

Tapi kita dapat memaksanya untuk memformat dengan lokal yang kita inginkan dengan menambahkan argumen lokal berupa kode negara:

```jsx
<p>{price.toLocaleString("id")}</p>
// output: 152.000
```

Kita juga dapat menambahkan kode negara untuk lebih spesifik memilih bahasa di negara tertentu:

```jsx
<p>{price.toLocaleString("id-ID")}</p>
// output: 152.000
```

#### Persentase

Coming soon ...

#### Mata uang

Misalnya kita ingin merender harga produk:

```jsx
<p>{price}</p>
// output: 26999999
```

Kita dapat menformatnya menjadi mata uang dengan _method_ `toLocaleString()`:

```jsx
<p>
  {price.toLocaleString("id-ID", {
    style: "currency",
    currency: "IDR",
  })}
</p>
// output: Rp 26.999.999,00
```

### Tanggal dan waktu

Misalnya kita ingin merender tanggal dan waktu pembacaan teks proklamasi:

```jsx
const date = "1945-08-17T10:00:00.000Z";

function DateTime() {
  return <p>{date} WIB</p>;
}
// output: 1945-08-17T10:00:00.000Z WIB
```

Kita dapat menformatnya dengan _method_ `toLocaleString()`:

```jsx
const date = new Date("1945-08-17T10:00:00.000Z");

function DateTime() {
  return (
    <p>
      {date.toLocaleString("id", {
        year: "numeric",
        month: "long",
        day: "numeric",
        hour: "2-digit",
        minute: "2-digit",
      })}{" "}
      WIB
    </p>
  );
}
// output: 17 Agustus 1945 pukul 10.00 WIB
```

## Filter dan Pencarian

Untuk membuat filter, kita perlu membuat _state_ berisi filter yang diinginkan, kemudian sebelum _list_ di-_render_ kita filter terlebih dahulu dengan kondisi menggunakan _method_ _array_ `filter()`.

Misalnya diameter minimal, nilai awalnya harus `0` agar semua memenuhi kondisi saat render pertama kali, kita juga harus memberi nilai alternatif `0` menggunakan _nullish coalescing_:

```jsx
export default function Galaxies() {
  const [galaxies, setGalaxies] = useState([
    {
      id: 1,
      name: "Andromeda",
      diameter: 220000,
    },
    {
      id: 2,
      name: "Bima Sakti",
      diameter: 100000,
    },
    {
      id: 3,
      name: "Triangulum",
      diameter: 60000,
    },
  ]);
  const [minDiameter, setMinDiameter] = useState(0);

  return (
    <div>
      <div>
        <label>
          Diameter minimal:
          <input onChange={(e) => setMinDiameter(e.target.value ?? 0)} />
        </label>
      </div>
      <div>
        {galaxies
          .filter((galaxy) => galaxy.diameter > minDiameter)
          .map((galaxy) => (
            <Galaxy key={galaxy.id} {...galaxy} />
          ))}
      </div>
    </div>
  );
}
```

Kita juga dapat mem-filter dengan lebih dari satu kondisi, misalnya diameter maksimal, nilai awalnya harus `Infinity` agar semua memenuhi kondisi saat render pertama kali, kita juga harus memberi nilai alternatif `Infinity` menggunakan _nullish coalescing_:

```jsx
export default function Galaxies() {
  const [galaxies, setGalaxies] = useState([
    {
      id: 1,
      name: "Andromeda",
      diameter: 220000,
    },
    {
      id: 2,
      name: "Bima Sakti",
      diameter: 100000,
    },
    {
      id: 3,
      name: "Triangulum",
      diameter: 60000,
    },
  ]);
  const [minDiameter, setMinDiameter] = useState(0);
  const [maxDiameter, setMaxDiameter] = useState(Infinity);

  return (
    <div>
      <div>
        <label>
          Diameter minimal:
          <input onChange={(e) => setMinDiameter(e.target.value ?? 0)} />
        </label>
        <label>
          Diameter maksimal:
          <input onChange={(e) => setMaxDiameter(e.target.value ?? Infinity)} />
        </label>
      </div>
      <div>
        {galaxies
          .filter((g) => g.diameter >= minDiameter && g.diameter <= maxDiameter)
          .map((galaxy) => (
            <Galaxy key={galaxy.id} {...galaxy} />
          ))}
      </div>
    </div>
  );
}
```

## _Pagination_

Coming soon ...
