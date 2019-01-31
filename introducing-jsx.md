---
id: introducing-jsx
title: Memperkenalkan JSX
permalink: docs/introducing-jsx.html
prev: hello-world.html
next: rendering-elements.html
---
Perhatikan deklarasi variabel berikut:

```js
const element = <h1>Hello, World!</h1>;
```

Susunan elemen tag yang lucu ini bukan berbentuk string dan bukan pula berbentuk HTML.

Sintaks seperti ini disebut JSX, dan ini merupakan sintaks ekstensi dari JavaScript. Kami merekemondasikan JSX untuk digunakan dengan React untuk mendeskripsikan tampilan UI. JSX mungkin mirip dengan template language, tapi JSX memiliki semua fitur yang dimiliki JavaScript.

JSX menghasilkan "elemen" React. Kita akan mempelajari rendering JSX ke dalam DOM di [bagian berikutnya](/docs/rendering-elements.html). Di bagian bawah, Anda akan menemukan dasar-dasar JSX yang anda butuhkan untuk memulai.

### Mengapa JSX?

React mengadopsi prinsip bahwa melakukan rendering sebuah logic berkaitan erat dengan UI logic lainnya: bagaimana menangani events, bagaimana state berubah, dan bagaimana menyiapkan data untuk ditampilkan.

Malahan memisahkan secara artifisial *teknologi* dengan menempatkan markup dan logic dalam file terpisah, React [memisahkan*perhatian*](https://en.wikipedia.org/wiki/Separation_of_concerns) dengan unit yang gabungkan secara longgar disebut "komponen" yang mengandung keduanya. Kita akan kembali ke komponen dalam [bagian selanjutnya](/docs/components-and-props.html), namun jika anda belum nyaman menempatkan markup dalam JS, [pembicaraan ini](https://www.youtube.com/watch?v=x7cQ3mrcKaY)mungkin meyakinkan anda sebaliknya.

React [tidak perlu](/docs/react-without-jsx.html) menggunakan JSX, tetapi kebanyakan orang terbantu sebagai bantuan visual ketika bekerja dengan UI dalam kode JavaScript. Hal ini juga memungkinkan Bereaksi untuk menunjukkan pesan kesalahan dan peringatan yang lebih berguna.

Dengan itu, mari kita mulai!

### Menyertakan Ekspresi di JSX

Anda dapat menyertakan [ekspresi JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) apapun di dalam JSX dengan membungkusnya dengan tanda kurung kurawal.

Sebagai contoh, `2 + 2`, `user.firstName`, dan `formatName(user)` adalah ekspresi yang valid:

```js{12}
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[](codepen://introducing-jsx).

Kita membagi JSX ke beberapa baris agar lebih mudah dibaca. Walaupun sebenarnya tidak dibutuhkan, ketika melakukan ini, kami selalu merekomendasikan untuk membungkusnya dengan tanda kurung untuk menghindari [automatic semicolon insertion](http://stackoverflow.com/q/2846283).

### JSX adalah Ekspresi juga

Setelah kompilasi, ekspresi JSX menjadi fungsi JavaScript reguler yang memanggil dan mengevaluasi ke objek JavaScript.

Ini berarti anda dapat menggunakan JSX di dalam statemen `if` dan perulangan `for`, menaruhnya di dalam variabel, menerimanya sebagai argumen, dan mengembalikannya dari fungsi:

```js{3,5}
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### Menspesifikasikan Atribut dengan JSX

Anda dapat menggunakan tanda petik untuk menspesifikasikan string sebagai atribut:

```js
const element = <div tabIndex="0"></div>;
```

Anda juga bisa menggunakan tanda kurung kurawal untuk memasukkan eksp[resi JavaScript ke dalam sebuah atribut:

```js
const element = <img src={user.avatarUrl}></img>;
```

Jangan membungkus tanda kurung kurawal dengan tanda petik ketika memasukkan ekspresi JavScript di dalam sebuah atribut. Anda boleh menggunakan tanda petik (untuk nilai string) atau tanda kurung kurawal (untuk ekspresi), namun tidak keduanya di satu atribut yang sama.

> **Warning:**
> 
> Karena JSX lebih dekat ke JavaScript daripada HTML, React DOM menggunakan `camelCase` konvensi penamaan properti dan bukan nama atribut HTML.
> 
> Sebagai contoh, `class` menjadi [`className`](https://developer.mozilla.org/en-US/docs/Web/API/Element/className) di JSX, dan `tabindex` menjadi [`tabIndex`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex).

### Menspesifikasikan Elemen Anak dengan JSX

JIka tag bersifat kosong (tidak memiliki elemen anak), anda bisa saja menutupnya secara langsung dengan `/>`, seperti XML:

```js
const element = <img src={user.avatarUrl} />;
```

Tag JSX dimungkinkan untuk memiliki elemen anak:

```js
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

### JSX Mencegah Serangan dengan Injeksi

Menaruh input user di JSX diperbolehkan:

```js
const title = response.potentiallyMaliciousInput;
// Hal seperti ini aman:
const element = <h1>{title}</h1>;
```

Secara default, React DOM [meng-escape](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html) nilai apapun yang ditaruh di dalam JSX sebelum me-render mereka. Dengan demikian dapat dipastikan anda tidak akan pernah menginjek apapun yang tidak ditulis di aplikasi anda secara eksplisit. Semuanya di-ocnvert menjadi string sebelum di-render. Ini membantu mencegah serangan [XSS (cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting).

### JSX Merepresentasikan Obyek

Babel akan meng-compile JSX menjadi pemanggilan `React.createElement()`.

Dua contoh ini akan menghasilkan hal yang sama:

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React.createElement()` melakukan serangkaian pengecekan yang membantu anda menulis kode yang bebas dari bug namun pada dasarnya akan membuat obyek seperti ini:

```js
// Catatan: struktur ini telah di sederhanakan
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};
```

Obyek seperti ini disebut "elemen React". Anda dapat menganggap mereka sebagai deskripsi dari apa yang anda ingin lihat di layar. React membaca obyek-obyek ini dan menggunakan mereka untuk membangun DOM dan membuatnya tetap sesuai dengan kondisi saat ini.

Kita akan mengeksplorasi rendering pada elemen React ke DOM dalam bagian berikutnya.

> **Saran:**
> 
> We recommend using the ["Babel" language definition](http://babeljs.io/docs/editors) for your editor of choice so that both ES6 and JSX code is properly highlighted. This website uses the [Oceanic Next](https://labs.voronianski.com/oceanic-next-color-scheme/) color scheme which is compatible with it.