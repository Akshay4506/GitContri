<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "888609c48329c280ca2477d2df40f2e5",
  "translation_date": "2025-08-26T21:40:21+00:00",
  "source_file": "2-js-basics/3-making-decisions/README.md",
  "language_code": "th"
}
-->
# พื้นฐาน JavaScript: การตัดสินใจ

![JavaScript Basics - Making decisions](../../../../translated_images/webdev101-js-decisions.69e1b20f272dd1f0b1cb2f8adaff3ed2a77c4f91db96d8a0594132a353fa189a.th.png)

> สเก็ตโน้ตโดย [Tomomi Imura](https://twitter.com/girlie_mac)

## แบบทดสอบก่อนเรียน

[แบบทดสอบก่อนเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/11)

การตัดสินใจและการควบคุมลำดับการทำงานของโค้ดช่วยให้โค้ดของคุณนำกลับมาใช้ใหม่ได้และมีความแข็งแกร่ง บทนี้จะครอบคลุมไวยากรณ์สำหรับการควบคุมการไหลของข้อมูลใน JavaScript และความสำคัญเมื่อใช้ร่วมกับประเภทข้อมูล Boolean

[![Making Decisions](https://img.youtube.com/vi/SxTp8j-fMMY/0.jpg)](https://youtube.com/watch?v=SxTp8j-fMMY "Making Decisions")

> 🎥 คลิกที่ภาพด้านบนเพื่อดูวิดีโอเกี่ยวกับการตัดสินใจ

> คุณสามารถเรียนบทเรียนนี้ได้ที่ [Microsoft Learn](https://docs.microsoft.com/learn/modules/web-development-101-if-else/?WT.mc_id=academic-77807-sagibbon)!

## ทบทวนเกี่ยวกับ Boolean

Boolean มีค่าได้เพียงสองค่าเท่านั้น: `true` หรือ `false` Boolean ช่วยในการตัดสินใจว่าโค้ดบรรทัดใดควรถูกเรียกใช้เมื่อเงื่อนไขบางอย่างเป็นจริง

ตั้งค่า Boolean ของคุณให้เป็น true หรือ false ได้ดังนี้:

`let myTrueBool = true`  
`let myFalseBool = false`

✅ Boolean ได้รับการตั้งชื่อตาม George Boole นักคณิตศาสตร์ นักปรัชญา และนักตรรกศาสตร์ชาวอังกฤษ (1815–1864)

## ตัวดำเนินการเปรียบเทียบและ Boolean

ตัวดำเนินการใช้เพื่อประเมินเงื่อนไขโดยการเปรียบเทียบ ซึ่งจะสร้างค่าประเภท Boolean ด้านล่างคือตัวอย่างตัวดำเนินการที่ใช้บ่อย

| สัญลักษณ์ | คำอธิบาย                                                                                                                                                   | ตัวอย่าง            |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------- |
| `<`       | **น้อยกว่า**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean `true` หากค่าทางด้านซ้ายมีค่าน้อยกว่าค่าทางด้านขวา                                                | `5 < 6 // true`     |
| `<=`      | **น้อยกว่าหรือเท่ากับ**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean `true` หากค่าทางด้านซ้ายมีค่าน้อยกว่าหรือเท่ากับค่าทางด้านขวา                        | `5 <= 6 // true`    |
| `>`       | **มากกว่า**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean `true` หากค่าทางด้านซ้ายมีค่ามากกว่าค่าทางด้านขวา                                                  | `5 > 6 // false`    |
| `>=`      | **มากกว่าหรือเท่ากับ**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean `true` หากค่าทางด้านซ้ายมีค่ามากกว่าหรือเท่ากับค่าทางด้านขวา                          | `5 >= 6 // false`   |
| `===`     | **เท่ากันแบบเข้มงวด**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean `true` หากค่าทางด้านซ้ายและขวาเท่ากัน **และ** เป็นประเภทข้อมูลเดียวกัน                 | `5 === 6 // false`  |
| `!==`     | **ไม่เท่ากัน**: เปรียบเทียบสองค่าและคืนค่าประเภท Boolean ตรงข้ามกับที่ตัวดำเนินการเท่ากันแบบเข้มงวดจะคืนค่า                                              | `5 !== 6 // true`   |

✅ ทดสอบความเข้าใจของคุณโดยเขียนการเปรียบเทียบในคอนโซลของเบราว์เซอร์ มีข้อมูลที่คืนค่ามาแล้วทำให้คุณประหลาดใจหรือไม่?

## คำสั่ง If

คำสั่ง if จะรันโค้ดในบล็อกของมันหากเงื่อนไขเป็นจริง

```javascript
if (condition) {
  //Condition is true. Code in this block will run.
}
```

ตัวดำเนินการเชิงตรรกะมักถูกใช้เพื่อสร้างเงื่อนไข

```javascript
let currentMoney;
let laptopPrice;

if (currentMoney >= laptopPrice) {
  //Condition is true. Code in this block will run.
  console.log("Getting a new laptop!");
}
```

## คำสั่ง If..Else

คำสั่ง `else` จะรันโค้ดในบล็อกของมันเมื่อเงื่อนไขเป็นเท็จ โดย `else` เป็นตัวเลือกเสริมสำหรับคำสั่ง `if`

```javascript
let currentMoney;
let laptopPrice;

if (currentMoney >= laptopPrice) {
  //Condition is true. Code in this block will run.
  console.log("Getting a new laptop!");
} else {
  //Condition is false. Code in this block will run.
  console.log("Can't afford a new laptop, yet!");
}
```

✅ ทดสอบความเข้าใจของคุณเกี่ยวกับโค้ดนี้และโค้ดต่อไปนี้โดยรันในคอนโซลของเบราว์เซอร์ เปลี่ยนค่าของตัวแปร currentMoney และ laptopPrice เพื่อเปลี่ยนค่าที่ `console.log()` คืนมา

## คำสั่ง Switch

คำสั่ง `switch` ใช้เพื่อดำเนินการต่าง ๆ ตามเงื่อนไขที่แตกต่างกัน ใช้คำสั่ง `switch` เพื่อเลือกหนึ่งในหลายบล็อกโค้ดที่จะถูกรัน

```javascript
switch (expression) {
  case x:
    // code block
    break;
  case y:
    // code block
    break;
  default:
  // code block
}
```

```javascript
// program using switch statement
let a = 2;

switch (a) {
  case 1:
    a = "one";
    break;
  case 2:
    a = "two";
    break;
  default:
    a = "not found";
    break;
}
console.log(`The value is ${a}`);
```

✅ ทดสอบความเข้าใจของคุณเกี่ยวกับโค้ดนี้และโค้ดต่อไปนี้โดยรันในคอนโซลของเบราว์เซอร์ เปลี่ยนค่าของตัวแปร a เพื่อเปลี่ยนค่าที่ `console.log()` คืนมา

## ตัวดำเนินการเชิงตรรกะและ Boolean

การตัดสินใจอาจต้องการการเปรียบเทียบมากกว่าหนึ่งครั้ง และสามารถเชื่อมโยงกันด้วยตัวดำเนินการเชิงตรรกะเพื่อสร้างค่าประเภท Boolean

| สัญลักษณ์ | คำอธิบาย                                                                                     | ตัวอย่าง                                                                 |
| --------- | --------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `&&`      | **AND เชิงตรรกะ**: เปรียบเทียบสองนิพจน์ Boolean คืนค่า true **เฉพาะเมื่อ** ทั้งสองด้านเป็น true | `(5 > 6) && (5 < 6 ) //ด้านหนึ่งเป็น false อีกด้านเป็น true คืนค่า false` |
| `\|\|`    | **OR เชิงตรรกะ**: เปรียบเทียบสองนิพจน์ Boolean คืนค่า true หากมีอย่างน้อยหนึ่งด้านเป็น true     | `(5 > 6) \|\| (5 < 6) //ด้านหนึ่งเป็น false อีกด้านเป็น true คืนค่า true` |
| `!`       | **NOT เชิงตรรกะ**: คืนค่าตรงข้ามของนิพจน์ Boolean                                           | `!(5 > 6) // 5 ไม่ได้มากกว่า 6 แต่ "!" จะคืนค่า true`                  |

## เงื่อนไขและการตัดสินใจด้วยตัวดำเนินการเชิงตรรกะ

ตัวดำเนินการเชิงตรรกะสามารถใช้เพื่อสร้างเงื่อนไขในคำสั่ง if..else

```javascript
let currentMoney;
let laptopPrice;
let laptopDiscountPrice = laptopPrice - laptopPrice * 0.2; //Laptop price at 20 percent off

if (currentMoney >= laptopPrice || currentMoney >= laptopDiscountPrice) {
  //Condition is true. Code in this block will run.
  console.log("Getting a new laptop!");
} else {
  //Condition is true. Code in this block will run.
  console.log("Can't afford a new laptop, yet!");
}
```

### ตัวดำเนินการปฏิเสธ

คุณได้เห็นแล้วว่าคุณสามารถใช้คำสั่ง `if...else` เพื่อสร้างตรรกะตามเงื่อนไขได้ สิ่งใดก็ตามที่ใส่ใน `if` จะต้องประเมินเป็น true/false โดยการใช้ตัวดำเนินการ `!` คุณสามารถ _ปฏิเสธ_ นิพจน์ได้ มันจะมีลักษณะดังนี้:

```javascript
if (!condition) {
  // runs if condition is false
} else {
  // runs if condition is true
}
```

### นิพจน์แบบ Ternary

`if...else` ไม่ใช่วิธีเดียวในการแสดงตรรกะการตัดสินใจ คุณยังสามารถใช้สิ่งที่เรียกว่าตัวดำเนินการ ternary ไวยากรณ์ของมันมีลักษณะดังนี้:

```javascript
let variable = condition ? <return this if true> : <return this if false>
```

ด้านล่างคือตัวอย่างที่จับต้องได้มากขึ้น:

```javascript
let firstNumber = 20;
let secondNumber = 10;
let biggestNumber = firstNumber > secondNumber ? firstNumber : secondNumber;
```

✅ ใช้เวลาสักครู่เพื่ออ่านโค้ดนี้หลาย ๆ ครั้ง คุณเข้าใจหรือไม่ว่าตัวดำเนินการเหล่านี้ทำงานอย่างไร?

สิ่งที่กล่าวไว้ข้างต้นคือ

- หาก `firstNumber` มีค่ามากกว่า `secondNumber`
- ให้กำหนดค่า `firstNumber` ให้กับ `biggestNumber`
- มิฉะนั้นให้กำหนดค่า `secondNumber`

นิพจน์ ternary เป็นเพียงวิธีการเขียนโค้ดแบบย่อดังนี้:

```javascript
let biggestNumber;
if (firstNumber > secondNumber) {
  biggestNumber = firstNumber;
} else {
  biggestNumber = secondNumber;
}
```

---

## 🚀 ความท้าทาย

สร้างโปรแกรมที่เขียนด้วยตัวดำเนินการเชิงตรรกะก่อน จากนั้นเขียนใหม่โดยใช้ตัวดำเนินการ ternary คุณชอบไวยากรณ์แบบไหนมากกว่ากัน?

---

## แบบทดสอบหลังเรียน

[แบบทดสอบหลังเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/12)

## ทบทวนและศึกษาด้วยตนเอง

อ่านเพิ่มเติมเกี่ยวกับตัวดำเนินการที่มีให้ใช้งาน [บน MDN](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators)

ลองดู [operator lookup](https://joshwcomeau.com/operator-lookup/) ที่ยอดเยี่ยมของ Josh Comeau!

## งานที่ได้รับมอบหมาย

[Operators](assignment.md)

---

**ข้อจำกัดความรับผิดชอบ**:  
เอกสารนี้ได้รับการแปลโดยใช้บริการแปลภาษา AI [Co-op Translator](https://github.com/Azure/co-op-translator) แม้ว่าเราจะพยายามให้การแปลมีความถูกต้องมากที่สุด แต่โปรดทราบว่าการแปลอัตโนมัติอาจมีข้อผิดพลาดหรือความไม่ถูกต้อง เอกสารต้นฉบับในภาษาดั้งเดิมควรถือเป็นแหล่งข้อมูลที่เชื่อถือได้ สำหรับข้อมูลที่สำคัญ ขอแนะนำให้ใช้บริการแปลภาษามืออาชีพ เราไม่รับผิดชอบต่อความเข้าใจผิดหรือการตีความผิดที่เกิดจากการใช้การแปลนี้