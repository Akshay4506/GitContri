<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "8da1b5e2c63f749808858c53f37b8ce7",
  "translation_date": "2025-08-26T23:04:23+00:00",
  "source_file": "7-bank-project/1-template-route/README.md",
  "language_code": "th"
}
-->
# สร้างแอปธนาคาร ตอนที่ 1: HTML Templates และ Routes ในเว็บแอป

## แบบทดสอบก่อนเรียน

[แบบทดสอบก่อนเรียน](https://ff-quizzes.netlify.app/web/quiz/41)

### บทนำ

ตั้งแต่การมาของ JavaScript ในเบราว์เซอร์ เว็บไซต์ก็มีความโต้ตอบและซับซ้อนมากขึ้นกว่าเดิม เทคโนโลยีเว็บถูกนำมาใช้สร้างแอปพลิเคชันที่ทำงานได้เต็มรูปแบบในเบราว์เซอร์ ซึ่งเราเรียกว่า [เว็บแอปพลิเคชัน](https://en.wikipedia.org/wiki/Web_application) เนื่องจากเว็บแอปมีความโต้ตอบสูง ผู้ใช้ไม่ต้องการรอการโหลดหน้าเว็บใหม่ทุกครั้งที่มีการดำเนินการ นั่นคือเหตุผลที่ JavaScript ถูกใช้ในการอัปเดต HTML โดยตรงผ่าน DOM เพื่อให้ประสบการณ์การใช้งานราบรื่นยิ่งขึ้น

ในบทเรียนนี้ เราจะวางรากฐานในการสร้างแอปธนาคารโดยใช้ HTML templates เพื่อสร้างหน้าจอหลายหน้าที่สามารถแสดงและอัปเดตได้โดยไม่ต้องโหลดหน้า HTML ทั้งหมดใหม่

### สิ่งที่ต้องเตรียม

คุณต้องมีเว็บเซิร์ฟเวอร์ในเครื่องเพื่อทดสอบเว็บแอปที่เราจะสร้างในบทเรียนนี้ หากคุณยังไม่มี คุณสามารถติดตั้ง [Node.js](https://nodejs.org) และใช้คำสั่ง `npx lite-server` จากโฟลเดอร์โปรเจกต์ของคุณ มันจะสร้างเว็บเซิร์ฟเวอร์ในเครื่องและเปิดแอปของคุณในเบราว์เซอร์

### การเตรียมตัว

บนคอมพิวเตอร์ของคุณ สร้างโฟลเดอร์ชื่อ `bank` และไฟล์ชื่อ `index.html` ภายใน เราจะเริ่มต้นจาก HTML [boilerplate](https://en.wikipedia.org/wiki/Boilerplate_code):

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

## HTML templates

หากคุณต้องการสร้างหน้าจอหลายหน้าสำหรับหน้าเว็บ วิธีหนึ่งคือการสร้างไฟล์ HTML หนึ่งไฟล์สำหรับทุกหน้าจอที่คุณต้องการแสดง อย่างไรก็ตาม วิธีนี้มีข้อเสียบางประการ:

- คุณต้องโหลด HTML ทั้งหมดใหม่เมื่อเปลี่ยนหน้าจอ ซึ่งอาจช้า
- การแชร์ข้อมูลระหว่างหน้าจอต่าง ๆ เป็นเรื่องยาก

อีกวิธีหนึ่งคือการมีไฟล์ HTML เพียงไฟล์เดียว และกำหนด [HTML templates](https://developer.mozilla.org/docs/Web/HTML/Element/template) หลายตัวโดยใช้ `<template>` element template เป็นบล็อก HTML ที่สามารถนำกลับมาใช้ใหม่ได้ ซึ่งเบราว์เซอร์จะไม่แสดง และต้องถูกสร้างขึ้นใน runtime โดยใช้ JavaScript

### งาน

เราจะสร้างแอปธนาคารที่มีสองหน้าจอ: หน้าเข้าสู่ระบบและหน้าแดชบอร์ด ก่อนอื่นให้เพิ่ม placeholder element ใน HTML body ที่เราจะใช้สร้างหน้าจอต่าง ๆ ของแอป:

```html
<div id="app">Loading...</div>
```

เราให้ `id` เพื่อให้ง่ายต่อการค้นหาด้วย JavaScript ในภายหลัง

> เคล็ดลับ: เนื่องจากเนื้อหาของ element นี้จะถูกแทนที่ เราสามารถใส่ข้อความหรือตัวบ่งชี้การโหลดที่จะแสดงในขณะที่แอปกำลังโหลด

ถัดไป ให้เพิ่ม HTML template สำหรับหน้าเข้าสู่ระบบด้านล่าง สำหรับตอนนี้เราจะใส่เพียงแค่หัวข้อและส่วนที่มีลิงก์ที่เราจะใช้ในการนำทาง

```html
<template id="login">
  <h1>Bank App</h1>
  <section>
    <a href="/dashboard">Login</a>
  </section>
</template>
```

จากนั้นเราจะเพิ่ม HTML template อีกตัวสำหรับหน้าแดชบอร์ด หน้านี้จะมีส่วนต่าง ๆ ดังนี้:

- ส่วนหัวที่มีหัวข้อและลิงก์ออกจากระบบ
- ยอดเงินปัจจุบันในบัญชีธนาคาร
- รายการธุรกรรมที่แสดงในตาราง

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

> เคล็ดลับ: เมื่อสร้าง HTML templates หากคุณต้องการดูว่ามันจะมีลักษณะอย่างไร คุณสามารถคอมเมนต์บรรทัด `<template>` และ `</template>` โดยใส่ไว้ใน `<!-- -->`

✅ คุณคิดว่าเราทำไมถึงใช้ `id` attributes ใน templates? เราสามารถใช้สิ่งอื่นเช่น classes ได้หรือไม่?

## การแสดง templates ด้วย JavaScript

หากคุณลองไฟล์ HTML ปัจจุบันในเบราว์เซอร์ คุณจะเห็นว่ามันติดอยู่ที่การแสดง `Loading...` นั่นเป็นเพราะเราต้องเพิ่มโค้ด JavaScript เพื่อสร้างและแสดง HTML templates

การสร้าง template มักทำใน 3 ขั้นตอน:

1. ดึง element template ใน DOM เช่นโดยใช้ [`document.getElementById`](https://developer.mozilla.org/docs/Web/API/Document/getElementById)
2. โคลน element template โดยใช้ [`cloneNode`](https://developer.mozilla.org/docs/Web/API/Node/cloneNode)
3. แนบมันเข้ากับ DOM ใต้ element ที่มองเห็นได้ เช่นโดยใช้ [`appendChild`](https://developer.mozilla.org/docs/Web/API/Node/appendChild)

✅ ทำไมเราต้องโคลน template ก่อนแนบเข้ากับ DOM? คุณคิดว่าจะเกิดอะไรขึ้นหากเราข้ามขั้นตอนนี้?

### งาน

สร้างไฟล์ใหม่ชื่อ `app.js` ในโฟลเดอร์โปรเจกต์ของคุณและนำเข้าไฟล์นั้นในส่วน `<head>` ของ HTML:

```html
<script src="app.js" defer></script>
```

ตอนนี้ใน `app.js` เราจะสร้างฟังก์ชันใหม่ชื่อ `updateRoute`:

```js
function updateRoute(templateId) {
  const template = document.getElementById(templateId);
  const view = template.content.cloneNode(true);
  const app = document.getElementById('app');
  app.innerHTML = '';
  app.appendChild(view);
}
```

สิ่งที่เราทำที่นี่คือ 3 ขั้นตอนที่อธิบายไว้ข้างต้น เราสร้าง template ด้วย id `templateId` และใส่เนื้อหาที่โคลนไว้ใน placeholder ของแอปของเรา โปรดทราบว่าเราต้องใช้ `cloneNode(true)` เพื่อคัดลอก subtree ทั้งหมดของ template

ตอนนี้เรียกฟังก์ชันนี้ด้วย template หนึ่งตัวและดูผลลัพธ์

```js
updateRoute('login');
```

✅ โค้ดนี้ `app.innerHTML = '';` มีวัตถุประสงค์อะไร? จะเกิดอะไรขึ้นหากไม่มีมัน?

## การสร้าง routes

เมื่อพูดถึงเว็บแอป เราเรียก *Routing* ว่าความตั้งใจที่จะจับคู่ **URLs** กับหน้าจอเฉพาะที่ควรแสดง บนเว็บไซต์ที่มีไฟล์ HTML หลายไฟล์ สิ่งนี้จะทำโดยอัตโนมัติเนื่องจากเส้นทางไฟล์สะท้อนอยู่ใน URL ตัวอย่างเช่น ด้วยไฟล์เหล่านี้ในโฟลเดอร์โปรเจกต์ของคุณ:

```
mywebsite/index.html
mywebsite/login.html
mywebsite/admin/index.html
```

หากคุณสร้างเว็บเซิร์ฟเวอร์โดยมี `mywebsite` เป็น root การจับคู่ URL จะเป็น:

```
https://site.com            --> mywebsite/index.html
https://site.com/login.html --> mywebsite/login.html
https://site.com/admin/     --> mywebsite/admin/index.html
```

อย่างไรก็ตาม สำหรับเว็บแอปของเรา เราใช้ไฟล์ HTML เดียวที่มีหน้าจอทั้งหมด ดังนั้นพฤติกรรมเริ่มต้นนี้จะไม่ช่วยเรา เราต้องสร้างแผนที่นี้ด้วยตนเองและอัปเดต template ที่แสดงโดยใช้ JavaScript

### งาน

เราจะใช้ object ง่าย ๆ เพื่อสร้าง [map](https://en.wikipedia.org/wiki/Associative_array) ระหว่าง URL paths และ templates ของเรา เพิ่ม object นี้ที่ด้านบนของไฟล์ `app.js` ของคุณ

```js
const routes = {
  '/login': { templateId: 'login' },
  '/dashboard': { templateId: 'dashboard' },
};
```

ตอนนี้ให้ปรับเปลี่ยนฟังก์ชัน `updateRoute` เล็กน้อย แทนที่จะส่ง `templateId` โดยตรงเป็น argument เราต้องการดึงมันโดยดูที่ URL ปัจจุบันก่อน แล้วใช้ map ของเราเพื่อรับค่าของ template ID ที่เกี่ยวข้อง เราสามารถใช้ [`window.location.pathname`](https://developer.mozilla.org/docs/Web/API/Location/pathname) เพื่อรับเฉพาะส่วน path จาก URL

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

ที่นี่เราได้จับคู่ routes ที่เราประกาศกับ template ที่เกี่ยวข้อง คุณสามารถลองดูว่ามันทำงานถูกต้องโดยเปลี่ยน URL ด้วยตนเองในเบราว์เซอร์ของคุณ

✅ จะเกิดอะไรขึ้นหากคุณใส่ path ที่ไม่รู้จักใน URL? เราจะแก้ปัญหานี้ได้อย่างไร?

## การเพิ่มการนำทาง

ขั้นตอนถัดไปสำหรับแอปของเราคือการเพิ่มความสามารถในการนำทางระหว่างหน้าโดยไม่ต้องเปลี่ยน URL ด้วยตนเอง ซึ่งหมายถึงสองสิ่ง:

1. อัปเดต URL ปัจจุบัน
2. อัปเดต template ที่แสดงตาม URL ใหม่

เราได้จัดการส่วนที่สองด้วยฟังก์ชัน `updateRoute` แล้ว ดังนั้นเราต้องหาวิธีอัปเดต URL ปัจจุบัน

เราจะต้องใช้ JavaScript และโดยเฉพาะ [`history.pushState`](https://developer.mozilla.org/docs/Web/API/History/pushState) ซึ่งช่วยให้อัปเดต URL และสร้างรายการใหม่ในประวัติการท่องเว็บโดยไม่ต้องโหลด HTML ใหม่

> หมายเหตุ: ในขณะที่ element anchor HTML [`<a href>`](https://developer.mozilla.org/docs/Web/HTML/Element/a) สามารถใช้เองเพื่อสร้างลิงก์ไปยัง URL ต่าง ๆ ได้ แต่โดยค่าเริ่มต้นมันจะทำให้เบราว์เซอร์โหลด HTML ใหม่ จำเป็นต้องป้องกันพฤติกรรมนี้เมื่อจัดการ routing ด้วย JavaScript แบบกำหนดเอง โดยใช้ฟังก์ชัน `preventDefault()` บน event คลิก

### งาน

มาสร้างฟังก์ชันใหม่ที่เราสามารถใช้ในการนำทางในแอปของเรา:

```js
function navigate(path) {
  window.history.pushState({}, path, path);
  updateRoute();
}
```

วิธีนี้จะอัปเดต URL ปัจจุบันตาม path ที่ให้มา จากนั้นอัปเดต template คุณสมบัติ `window.location.origin` จะคืนค่า root URL ซึ่งช่วยให้เราสร้าง URL ที่สมบูรณ์จาก path ที่กำหนด

ตอนนี้เรามีฟังก์ชันนี้แล้ว เราสามารถจัดการปัญหาที่เรามีหาก path ไม่ตรงกับ route ที่กำหนดได้ เราจะปรับเปลี่ยนฟังก์ชัน `updateRoute` โดยเพิ่ม fallback ไปยัง route ที่มีอยู่หากเราไม่พบการจับคู่

```js
function updateRoute() {
  const path = window.location.pathname;
  const route = routes[path];

  if (!route) {
    return navigate('/login');
  }

  ...
```

หาก route ไม่สามารถพบได้ เราจะเปลี่ยนเส้นทางไปยังหน้า `login`

ตอนนี้มาสร้างฟังก์ชันเพื่อรับ URL เมื่อคลิกที่ลิงก์ และเพื่อป้องกันพฤติกรรมลิงก์เริ่มต้นของเบราว์เซอร์:

```js
function onLinkClick(event) {
  event.preventDefault();
  navigate(event.target.href);
}
```

มาสร้างระบบนำทางให้สมบูรณ์โดยเพิ่ม bindings ไปยังลิงก์ *Login* และ *Logout* ใน HTML

```html
<a href="/dashboard" onclick="onLinkClick(event)">Login</a>
...
<a href="/login" onclick="onLinkClick(event)">Logout</a>
```

object `event` ด้านบนจะจับ event `click` และส่งผ่านไปยังฟังก์ชัน `onLinkClick` ของเรา

โดยใช้ attribute [`onclick`](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers/onclick) ผูก event `click` กับโค้ด JavaScript ที่นี่คือการเรียกฟังก์ชัน `navigate()`

ลองคลิกที่ลิงก์เหล่านี้ คุณควรจะสามารถนำทางระหว่างหน้าจอต่าง ๆ ของแอปของคุณได้แล้ว

✅ วิธี `history.pushState` เป็นส่วนหนึ่งของมาตรฐาน HTML5 และถูกนำไปใช้ใน [เบราว์เซอร์สมัยใหม่ทั้งหมด](https://caniuse.com/?search=pushState) หากคุณกำลังสร้างเว็บแอปสำหรับเบราว์เซอร์รุ่นเก่า มีเคล็ดลับที่คุณสามารถใช้แทน API นี้: โดยใช้ [hash (`#`)](https://en.wikipedia.org/wiki/URI_fragment) ก่อน path คุณสามารถสร้าง routing ที่ทำงานร่วมกับการนำทาง anchor ปกติและไม่โหลดหน้าใหม่ เนื่องจากวัตถุประสงค์ของมันคือการสร้างลิงก์ภายในหน้า

## การจัดการปุ่มย้อนกลับและเดินหน้าในเบราว์เซอร์

การใช้ `history.pushState` จะสร้างรายการใหม่ในประวัติการท่องเว็บของเบราว์เซอร์ คุณสามารถตรวจสอบได้โดยกดปุ่ม *ย้อนกลับ* ค้างไว้ในเบราว์เซอร์ของคุณ มันควรจะแสดงบางอย่างเช่นนี้:

![ภาพหน้าจอของประวัติการนำทาง](../../../../translated_images/history.7fdabbafa521e06455b738d3dafa3ff41d3071deae60ead8c7e0844b9ed987d8.th.png)

หากคุณลองคลิกปุ่มย้อนกลับหลายครั้ง คุณจะเห็นว่า URL ปัจจุบันเปลี่ยนไปและประวัติถูกอัปเดต แต่ template เดิมยังคงแสดงอยู่

นั่นเป็นเพราะแอปพลิเคชันไม่รู้ว่าเราต้องเรียก `updateRoute()` ทุกครั้งที่ประวัติเปลี่ยนไป หากคุณดูที่ [เอกสาร `history.pushState`](https://developer.mozilla.org/docs/Web/API/History/pushState) คุณจะเห็นว่าหากสถานะเปลี่ยนไป - หมายความว่าเราไปยัง URL ที่แตกต่างกัน - event [`popstate`](https://developer.mozilla.org/docs/Web/API/Window/popstate_event) จะถูกเรียก เราจะใช้สิ่งนี้เพื่อแก้ไขปัญหานั้น

### งาน

เพื่อให้แน่ใจว่า template ที่แสดงถูกอัปเดตเมื่อประวัติของเบราว์เซอร์เปลี่ยนไป เราจะผูกฟังก์ชันใหม่ที่เรียก `updateRoute()` เราจะทำสิ่งนี้ที่ด้านล่างของไฟล์ `app.js`:

```js
window.onpopstate = () => updateRoute();
updateRoute();
```

> หมายเหตุ: เราใช้ [arrow function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Functions/Arrow_functions) ที่นี่เพื่อประกาศ event handler `popstate` ของเราเพื่อความกระชับ แต่ฟังก์ชันปกติก็จะทำงานเหมือนกัน

นี่คือวิดีโอรีเฟรชเกี่ยวกับ arrow functions:

[![Arrow Functions](https://img.youtube.com/vi/OP6eEbOj2sc/0.jpg)](https://youtube.com/watch?v=OP6eEbOj2sc "Arrow Functions")

> 🎥 คลิกที่ภาพด้านบนเพื่อดูวิดีโอเกี่ยวกับ arrow functions

ตอนนี้ลองใช้ปุ่มย้อนกลับและเดินหน้าของเบราว์เซอร์ของคุณ และตรวจสอบว่า route ที่แสดงถูกอัปเดตอย่างถูกต้องในครั้งนี้

---

## 🚀 ความท้าทาย

เพิ่ม template และ route ใหม่สำหรับหน้าที่สามที่แสดงเครดิตสำหรับแอปนี้

## แบบทดสอบหลังเรียน

[แบบทดสอบหลังเรียน](https://ff-quizzes.netlify.app/web/quiz/42)

## ทบทวนและศึกษาด้วยตนเอง

Routing เป็นหนึ่งในส่วนที่น่าประหลาดใจที่ยุ่งยากของการพัฒนาเว็บ โดยเฉพาะอย่างยิ่งเมื่อเว็บเปลี่ยนจากพฤติกรรมการรีเฟรชหน้าไปเป็นการรีเฟรชหน้าแบบ Single Page Application อ่านเพิ่มเติมเกี่ยวกับ [วิธีที่บริการ Azure Static Web App](https://docs.microsoft.com/azure/static-web-apps/routes/?WT.mc_id=academic-77807-sagibbon) จัดการ routing คุณสามารถอธิบายได้หรือไม่ว่าทำไมการตัดสินใจบางอย่างที่อธิบายไว้ในเอกสารนั้นจึงจำเป็น?

## งานที่ได้รับ

[ปรับปรุง routing](assignment.md)

---

**ข้อจำกัดความรับผิดชอบ**:  
เอกสารนี้ได้รับการแปลโดยใช้บริการแปลภาษา AI [Co-op Translator](https://github.com/Azure/co-op-translator) แม้ว่าเราจะพยายามให้การแปลมีความถูกต้องมากที่สุด แต่โปรดทราบว่าการแปลอัตโนมัติอาจมีข้อผิดพลาดหรือความไม่ถูกต้อง เอกสารต้นฉบับในภาษาดั้งเดิมควรถือเป็นแหล่งข้อมูลที่เชื่อถือได้ สำหรับข้อมูลที่สำคัญ ขอแนะนำให้ใช้บริการแปลภาษามืออาชีพ เราไม่รับผิดชอบต่อความเข้าใจผิดหรือการตีความผิดที่เกิดจากการใช้การแปลนี้