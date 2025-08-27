<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "8da1b5e2c63f749808858c53f37b8ce7",
  "translation_date": "2025-08-27T22:13:28+00:00",
  "source_file": "7-bank-project/1-template-route/README.md",
  "language_code": "id"
}
-->
# Membangun Aplikasi Perbankan Bagian 1: Template HTML dan Rute dalam Aplikasi Web

## Kuis Pra-Pelajaran

[Kuis pra-pelajaran](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/41)

### Pendahuluan

Sejak munculnya JavaScript di browser, situs web menjadi lebih interaktif dan kompleks daripada sebelumnya. Teknologi web kini sering digunakan untuk membuat aplikasi yang sepenuhnya berfungsi langsung di browser, yang kita sebut [aplikasi web](https://en.wikipedia.org/wiki/Web_application). Karena aplikasi web sangat interaktif, pengguna tidak ingin menunggu seluruh halaman dimuat ulang setiap kali sebuah tindakan dilakukan. Oleh karena itu, JavaScript digunakan untuk memperbarui HTML secara langsung menggunakan DOM, sehingga memberikan pengalaman pengguna yang lebih lancar.

Dalam pelajaran ini, kita akan membangun dasar-dasar untuk membuat aplikasi perbankan web, menggunakan template HTML untuk membuat beberapa layar yang dapat ditampilkan dan diperbarui tanpa harus memuat ulang seluruh halaman HTML.

### Prasyarat

Anda memerlukan server web lokal untuk menguji aplikasi web yang akan kita bangun dalam pelajaran ini. Jika Anda belum memilikinya, Anda dapat menginstal [Node.js](https://nodejs.org) dan menggunakan perintah `npx lite-server` dari folder proyek Anda. Perintah ini akan membuat server web lokal dan membuka aplikasi Anda di browser.

### Persiapan

Di komputer Anda, buat folder bernama `bank` dengan file bernama `index.html` di dalamnya. Kita akan memulai dari [boilerplate](https://en.wikipedia.org/wiki/Boilerplate_code) HTML berikut:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bank App</title>
  </head>
  <body>
    <!-- This is where you'll work -->
  </body>
</html>
```

---

## Template HTML

Jika Anda ingin membuat beberapa layar untuk sebuah halaman web, salah satu solusinya adalah membuat satu file HTML untuk setiap layar yang ingin ditampilkan. Namun, solusi ini memiliki beberapa kekurangan:

- Anda harus memuat ulang seluruh HTML saat beralih layar, yang bisa lambat.
- Sulit untuk berbagi data antara layar yang berbeda.

Pendekatan lain adalah hanya memiliki satu file HTML, dan mendefinisikan beberapa [template HTML](https://developer.mozilla.org/docs/Web/HTML/Element/template) menggunakan elemen `<template>`. Template adalah blok HTML yang dapat digunakan kembali, yang tidak ditampilkan oleh browser, dan perlu diinstansiasi saat runtime menggunakan JavaScript.

### Tugas

Kita akan membuat aplikasi perbankan dengan dua layar: halaman login dan dashboard. Pertama, tambahkan elemen placeholder di dalam body HTML yang akan kita gunakan untuk menginstansiasi berbagai layar aplikasi kita:

```html
<div id="app">Loading...</div>
```

Kami memberikan elemen ini sebuah `id` agar lebih mudah ditemukan dengan JavaScript nanti.

> Tip: karena konten elemen ini akan diganti, kita dapat menambahkan pesan atau indikator pemuatan yang akan ditampilkan saat aplikasi sedang dimuat.

Selanjutnya, tambahkan template HTML untuk halaman login di bawahnya. Untuk saat ini, kita hanya akan menambahkan judul dan bagian yang berisi tautan yang akan kita gunakan untuk navigasi.

```html
<template id="login">
  <h1>Bank App</h1>
  <section>
    <a href="/dashboard">Login</a>
  </section>
</template>
```

Kemudian tambahkan template HTML lain untuk halaman dashboard. Halaman ini akan berisi beberapa bagian:

- Header dengan judul dan tautan logout
- Saldo terkini dari rekening bank
- Daftar transaksi, ditampilkan dalam tabel

```html
<template id="dashboard">
  <header>
    <h1>Bank App</h1>
    <a href="/login">Logout</a>
  </header>
  <section>
    Balance: 100$
  </section>
  <section>
    <h2>Transactions</h2>
    <table>
      <thead>
        <tr>
          <th>Date</th>
          <th>Object</th>
          <th>Amount</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>
  </section>
</template>
```

> Tip: saat membuat template HTML, jika Anda ingin melihat tampilannya, Anda dapat mengomentari baris `<template>` dan `</template>` dengan menutupnya menggunakan `<!-- -->`.

✅ Mengapa menurut Anda kita menggunakan atribut `id` pada template? Bisakah kita menggunakan sesuatu yang lain seperti kelas?

## Menampilkan Template dengan JavaScript

Jika Anda mencoba file HTML Anda saat ini di browser, Anda akan melihat bahwa file tersebut hanya menampilkan `Loading...`. Itu karena kita perlu menambahkan kode JavaScript untuk menginstansiasi dan menampilkan template HTML.

Menginstansiasi template biasanya dilakukan dalam 3 langkah:

1. Ambil elemen template di DOM, misalnya menggunakan [`document.getElementById`](https://developer.mozilla.org/docs/Web/API/Document/getElementById).
2. Kloning elemen template, menggunakan [`cloneNode`](https://developer.mozilla.org/docs/Web/API/Node/cloneNode).
3. Lampirkan ke DOM di bawah elemen yang terlihat, misalnya menggunakan [`appendChild`](https://developer.mozilla.org/docs/Web/API/Node/appendChild).

✅ Mengapa kita perlu mengkloning template sebelum melampirkannya ke DOM? Apa yang menurut Anda akan terjadi jika kita melewatkan langkah ini?

### Tugas

Buat file baru bernama `app.js` di folder proyek Anda dan impor file tersebut di bagian `<head>` HTML Anda:

```html
<script src="app.js" defer></script>
```

Sekarang di `app.js`, kita akan membuat fungsi baru bernama `updateRoute`:

```js
function updateRoute(templateId) {
  const template = document.getElementById(templateId);
  const view = template.content.cloneNode(true);
  const app = document.getElementById('app');
  app.innerHTML = '';
  app.appendChild(view);
}
```

Apa yang kita lakukan di sini persis seperti 3 langkah yang dijelaskan di atas. Kita menginstansiasi template dengan id `templateId`, dan menempatkan konten yang telah dikloning di dalam placeholder aplikasi kita. Perhatikan bahwa kita perlu menggunakan `cloneNode(true)` untuk menyalin seluruh subtree dari template.

Sekarang panggil fungsi ini dengan salah satu template dan lihat hasilnya.

```js
updateRoute('login');
```

✅ Apa tujuan dari kode ini `app.innerHTML = '';`? Apa yang terjadi jika kita tidak menggunakannya?

## Membuat Rute

Dalam konteks aplikasi web, *Routing* adalah proses memetakan **URL** ke layar tertentu yang harus ditampilkan. Pada situs web dengan beberapa file HTML, ini dilakukan secara otomatis karena jalur file tercermin pada URL. Misalnya, dengan file-file berikut di folder proyek Anda:

```
mywebsite/index.html
mywebsite/login.html
mywebsite/admin/index.html
```

Jika Anda membuat server web dengan `mywebsite` sebagai root, pemetaan URL akan menjadi:

```
https://site.com            --> mywebsite/index.html
https://site.com/login.html --> mywebsite/login.html
https://site.com/admin/     --> mywebsite/admin/index.html
```

Namun, untuk aplikasi web kita yang menggunakan satu file HTML yang berisi semua layar, perilaku default ini tidak akan membantu kita. Kita harus membuat peta ini secara manual dan memperbarui template yang ditampilkan menggunakan JavaScript.

### Tugas

Kita akan menggunakan objek sederhana untuk mengimplementasikan [peta](https://en.wikipedia.org/wiki/Associative_array) antara jalur URL dan template kita. Tambahkan objek ini di bagian atas file `app.js` Anda.

```js
const routes = {
  '/login': { templateId: 'login' },
  '/dashboard': { templateId: 'dashboard' },
};
```

Sekarang mari kita modifikasi sedikit fungsi `updateRoute`. Alih-alih langsung meneruskan `templateId` sebagai argumen, kita ingin mengambilnya dengan terlebih dahulu melihat URL saat ini, lalu menggunakan peta kita untuk mendapatkan nilai ID template yang sesuai. Kita dapat menggunakan [`window.location.pathname`](https://developer.mozilla.org/docs/Web/API/Location/pathname) untuk mendapatkan bagian jalur dari URL.

```js
function updateRoute() {
  const path = window.location.pathname;
  const route = routes[path];

  const template = document.getElementById(route.templateId);
  const view = template.content.cloneNode(true);
  const app = document.getElementById('app');
  app.innerHTML = '';
  app.appendChild(view);
}
```

Di sini kita memetakan rute yang telah kita deklarasikan ke template yang sesuai. Anda dapat mencobanya dengan mengubah URL secara manual di browser Anda.

✅ Apa yang terjadi jika Anda memasukkan jalur yang tidak dikenal di URL? Bagaimana kita bisa menyelesaikan ini?

## Menambahkan Navigasi

Langkah berikutnya untuk aplikasi kita adalah menambahkan kemampuan untuk bernavigasi antar halaman tanpa harus mengubah URL secara manual. Ini melibatkan dua hal:

1. Memperbarui URL saat ini
2. Memperbarui template yang ditampilkan berdasarkan URL baru

Kita sudah menyelesaikan bagian kedua dengan fungsi `updateRoute`, jadi kita harus mencari cara untuk memperbarui URL saat ini.

Kita akan menggunakan JavaScript, lebih spesifik lagi [`history.pushState`](https://developer.mozilla.org/docs/Web/API/History/pushState) yang memungkinkan kita memperbarui URL dan membuat entri baru dalam riwayat penjelajahan, tanpa memuat ulang HTML.

> Catatan: Meskipun elemen jangkar HTML [`<a href>`](https://developer.mozilla.org/docs/Web/HTML/Element/a) dapat digunakan sendiri untuk membuat hyperlink ke URL yang berbeda, elemen ini secara default akan membuat browser memuat ulang HTML. Kita perlu mencegah perilaku ini saat menangani routing dengan JavaScript khusus, menggunakan fungsi preventDefault() pada event klik.

### Tugas

Mari kita buat fungsi baru yang dapat kita gunakan untuk bernavigasi dalam aplikasi kita:

```js
function navigate(path) {
  window.history.pushState({}, path, path);
  updateRoute();
}
```

Metode ini pertama-tama memperbarui URL saat ini berdasarkan jalur yang diberikan, lalu memperbarui template. Properti `window.location.origin` mengembalikan root URL, memungkinkan kita untuk merekonstruksi URL lengkap dari jalur yang diberikan.

Sekarang setelah kita memiliki fungsi ini, kita dapat menangani masalah yang terjadi jika jalur tidak cocok dengan rute yang telah didefinisikan. Kita akan memodifikasi fungsi `updateRoute` dengan menambahkan fallback ke salah satu rute yang ada jika kita tidak dapat menemukan kecocokan.

```js
function updateRoute() {
  const path = window.location.pathname;
  const route = routes[path];

  if (!route) {
    return navigate('/login');
  }

  ...
```

Jika rute tidak dapat ditemukan, kita sekarang akan diarahkan ke halaman `login`.

Sekarang mari kita buat fungsi untuk mendapatkan URL saat tautan diklik, dan untuk mencegah perilaku tautan default browser:

```js
function onLinkClick(event) {
  event.preventDefault();
  navigate(event.target.href);
}
```

Mari kita lengkapi sistem navigasi dengan menambahkan binding ke tautan *Login* dan *Logout* di HTML.

```html
<a href="/dashboard" onclick="onLinkClick(event)">Login</a>
...
<a href="/login" onclick="onLinkClick(event)">Logout</a>
```

Objek `event` di atas menangkap event `click` dan meneruskannya ke fungsi `onLinkClick` kita.

Menggunakan atribut [`onclick`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onclick), kita mengikat event `click` ke kode JavaScript, di sini panggilan ke fungsi `navigate()`.

Cobalah klik pada tautan ini, Anda sekarang seharusnya dapat bernavigasi antara layar yang berbeda dari aplikasi Anda.

✅ Metode `history.pushState` adalah bagian dari standar HTML5 dan diimplementasikan di [semua browser modern](https://caniuse.com/?search=pushState). Jika Anda membangun aplikasi web untuk browser lama, ada trik yang dapat Anda gunakan sebagai pengganti API ini: menggunakan [hash (`#`)](https://en.wikipedia.org/wiki/URI_fragment) sebelum jalur, Anda dapat mengimplementasikan routing yang bekerja dengan navigasi jangkar biasa dan tidak memuat ulang halaman, karena tujuannya adalah untuk membuat tautan internal dalam halaman.

## Menangani Tombol Kembali dan Maju di Browser

Menggunakan `history.pushState` membuat entri baru dalam riwayat navigasi browser. Anda dapat memeriksanya dengan menahan tombol *kembali* di browser Anda, seharusnya menampilkan sesuatu seperti ini:

![Screenshot riwayat navigasi](../../../../translated_images/history.7fdabbafa521e06455b738d3dafa3ff41d3071deae60ead8c7e0844b9ed987d8.id.png)

Jika Anda mencoba mengklik tombol kembali beberapa kali, Anda akan melihat bahwa URL saat ini berubah dan riwayat diperbarui, tetapi template yang sama tetap ditampilkan.

Itu karena aplikasi tidak tahu bahwa kita perlu memanggil `updateRoute()` setiap kali riwayat berubah. Jika Anda melihat dokumentasi [`history.pushState`](https://developer.mozilla.org/docs/Web/API/History/pushState), Anda dapat melihat bahwa jika status berubah - artinya kita berpindah ke URL yang berbeda - event [`popstate`](https://developer.mozilla.org/docs/Web/API/Window/popstate_event) akan dipicu. Kita akan menggunakan itu untuk memperbaiki masalah ini.

### Tugas

Untuk memastikan template yang ditampilkan diperbarui saat riwayat browser berubah, kita akan menambahkan fungsi baru yang memanggil `updateRoute()`. Kita akan melakukannya di bagian bawah file `app.js` kita:

```js
window.onpopstate = () => updateRoute();
updateRoute();
```

> Catatan: kita menggunakan [arrow function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Functions/Arrow_functions) di sini untuk mendeklarasikan handler event `popstate` kita agar lebih ringkas, tetapi fungsi biasa juga akan bekerja dengan cara yang sama.

Berikut video penyegaran tentang arrow function:

[![Arrow Functions](https://img.youtube.com/vi/OP6eEbOj2sc/0.jpg)](https://youtube.com/watch?v=OP6eEbOj2sc "Arrow Functions")

> 🎥 Klik gambar di atas untuk video tentang arrow function.

Sekarang coba gunakan tombol kembali dan maju di browser Anda, dan periksa bahwa rute yang ditampilkan diperbarui dengan benar kali ini.

---

## 🚀 Tantangan

Tambahkan template dan rute baru untuk halaman ketiga yang menampilkan kredit untuk aplikasi ini.

## Kuis Pasca-Pelajaran

[Kuis pasca-pelajaran](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/42)

## Tinjauan & Studi Mandiri

Routing adalah salah satu bagian yang cukup rumit dalam pengembangan web, terutama saat web beralih dari perilaku refresh halaman ke refresh halaman dalam Aplikasi Halaman Tunggal (Single Page Application). Bacalah sedikit tentang [bagaimana layanan Azure Static Web App](https://docs.microsoft.com/azure/static-web-apps/routes/?WT.mc_id=academic-77807-sagibbon) menangani routing. Bisakah Anda menjelaskan mengapa beberapa keputusan yang dijelaskan dalam dokumen tersebut diperlukan?

## Tugas

[Perbaiki routing](assignment.md)

---

**Penafian**:  
Dokumen ini telah diterjemahkan menggunakan layanan penerjemahan AI [Co-op Translator](https://github.com/Azure/co-op-translator). Meskipun kami berupaya untuk memberikan hasil yang akurat, harap diingat bahwa terjemahan otomatis mungkin mengandung kesalahan atau ketidakakuratan. Dokumen asli dalam bahasa aslinya harus dianggap sebagai sumber yang otoritatif. Untuk informasi yang bersifat kritis, disarankan menggunakan jasa penerjemahan profesional oleh manusia. Kami tidak bertanggung jawab atas kesalahpahaman atau penafsiran yang keliru yang timbul dari penggunaan terjemahan ini.