<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "d9da6dc61fb712b29f65e108c79b8a5d",
  "translation_date": "2025-08-26T22:05:47+00:00",
  "source_file": "6-space-game/1-introduction/README.md",
  "language_code": "th"
}
-->
# สร้างเกมอวกาศ ตอนที่ 1: บทนำ

![video](../../../../6-space-game/images/pewpew.gif)

## แบบทดสอบก่อนเรียน

[แบบทดสอบก่อนเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/29)

### การสืบทอดและการประกอบในพัฒนาเกม

ในบทเรียนก่อนหน้านี้ คุณอาจไม่ต้องกังวลเกี่ยวกับการออกแบบโครงสร้างของแอปพลิเคชันที่คุณสร้างมากนัก เนื่องจากโปรเจกต์มีขนาดเล็กและขอบเขตจำกัด อย่างไรก็ตาม เมื่อแอปพลิเคชันของคุณมีขนาดและขอบเขตที่ใหญ่ขึ้น การตัดสินใจเกี่ยวกับโครงสร้างจะกลายเป็นเรื่องสำคัญมากขึ้น มีสองวิธีหลักในการสร้างแอปพลิเคชันขนาดใหญ่ใน JavaScript: *การประกอบ* หรือ *การสืบทอด* ทั้งสองวิธีมีข้อดีและข้อเสีย แต่เราจะอธิบายผ่านบริบทของเกม

✅ หนึ่งในหนังสือเกี่ยวกับการเขียนโปรแกรมที่มีชื่อเสียงที่สุดเกี่ยวข้องกับ [รูปแบบการออกแบบ](https://en.wikipedia.org/wiki/Design_Patterns)

ในเกม คุณมี `วัตถุในเกม` ซึ่งเป็นวัตถุที่ปรากฏบนหน้าจอ ซึ่งหมายความว่ามันมีตำแหน่งในระบบพิกัดคาร์ทีเซียน โดยมีลักษณะเป็นพิกัด `x` และ `y` เมื่อคุณพัฒนาเกม คุณจะสังเกตเห็นว่าวัตถุในเกมทั้งหมดมีคุณสมบัติที่เป็นมาตรฐาน ซึ่งเป็นองค์ประกอบที่เหมือนกันในทุกเกมที่คุณสร้าง ได้แก่:

- **อิงตามตำแหน่ง** วัตถุในเกมส่วนใหญ่จะอิงตามตำแหน่ง ซึ่งหมายความว่ามันมีตำแหน่ง `x` และ `y`
- **เคลื่อนที่ได้** วัตถุเหล่านี้สามารถเคลื่อนที่ไปยังตำแหน่งใหม่ได้ โดยทั่วไปจะเป็นฮีโร่ มอนสเตอร์ หรือ NPC (ตัวละครที่ไม่ใช่ผู้เล่น) แต่ไม่ใช่วัตถุที่อยู่นิ่ง เช่น ต้นไม้
- **ทำลายตัวเอง** วัตถุเหล่านี้มีอยู่เพียงช่วงเวลาหนึ่งก่อนที่จะตั้งค่าตัวเองให้ถูกลบออก โดยปกติจะมีตัวบูลีน `dead` หรือ `destroyed` ที่ส่งสัญญาณไปยังเอนจิ้นเกมว่าวัตถุนี้ไม่ควรแสดงผลอีกต่อไป
- **คูลดาวน์** 'คูลดาวน์' เป็นคุณสมบัติทั่วไปของวัตถุที่มีอายุสั้น ตัวอย่างทั่วไปคือข้อความหรือเอฟเฟกต์กราฟิก เช่น การระเบิด ที่ควรปรากฏเพียงไม่กี่มิลลิวินาที

✅ ลองคิดถึงเกมอย่าง Pac-Man คุณสามารถระบุวัตถุทั้งสี่ประเภทที่กล่าวถึงข้างต้นในเกมนี้ได้หรือไม่?

### การแสดงพฤติกรรม

สิ่งที่เราอธิบายข้างต้นคือพฤติกรรมที่วัตถุในเกมสามารถมีได้ แล้วเราจะเข้ารหัสพฤติกรรมเหล่านี้ได้อย่างไร? เราสามารถแสดงพฤติกรรมเหล่านี้เป็นเมธอดที่เกี่ยวข้องกับคลาสหรือออบเจ็กต์

**คลาส**

แนวคิดคือการใช้ `คลาส` ร่วมกับ `การสืบทอด` เพื่อเพิ่มพฤติกรรมบางอย่างให้กับคลาส

✅ การสืบทอดเป็นแนวคิดสำคัญที่ควรเข้าใจ เรียนรู้เพิ่มเติมได้ที่ [บทความของ MDN เกี่ยวกับการสืบทอด](https://developer.mozilla.org/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

ในรูปแบบโค้ด วัตถุในเกมอาจมีลักษณะดังนี้:

```javascript

//set up the class GameObject
class GameObject {
  constructor(x, y, type) {
    this.x = x;
    this.y = y;
    this.type = type;
  }
}

//this class will extend the GameObject's inherent class properties
class Movable extends GameObject {
  constructor(x,y, type) {
    super(x,y, type)
  }

//this movable object can be moved on the screen
  moveTo(x, y) {
    this.x = x;
    this.y = y;
  }
}

//this is a specific class that extends the Movable class, so it can take advantage of all the properties that it inherits
class Hero extends Movable {
  constructor(x,y) {
    super(x,y, 'Hero')
  }
}

//this class, on the other hand, only inherits the GameObject properties
class Tree extends GameObject {
  constructor(x,y) {
    super(x,y, 'Tree')
  }
}

//a hero can move...
const hero = new Hero();
hero.moveTo(5,5);

//but a tree cannot
const tree = new Tree();
```

✅ ใช้เวลาสักครู่เพื่อจินตนาการถึงฮีโร่ใน Pac-Man (เช่น Inky, Pinky หรือ Blinky) และวิธีการเขียนใน JavaScript

**การประกอบ**

วิธีการจัดการการสืบทอดวัตถุอีกแบบหนึ่งคือการใช้ *การประกอบ* จากนั้นวัตถุจะแสดงพฤติกรรมของมันในลักษณะนี้:

```javascript
//create a constant gameObject
const gameObject = {
  x: 0,
  y: 0,
  type: ''
};

//...and a constant movable
const movable = {
  moveTo(x, y) {
    this.x = x;
    this.y = y;
  }
}
//then the constant movableObject is composed of the gameObject and movable constants
const movableObject = {...gameObject, ...movable};

//then create a function to create a new Hero who inherits the movableObject properties
function createHero(x, y) {
  return {
    ...movableObject,
    x,
    y,
    type: 'Hero'
  }
}
//...and a static object that inherits only the gameObject properties
function createStatic(x, y, type) {
  return {
    ...gameObject
    x,
    y,
    type
  }
}
//create the hero and move it
const hero = createHero(10,10);
hero.moveTo(5,5);
//and create a static tree which only stands around
const tree = createStatic(0,0, 'Tree'); 
```

**ควรใช้รูปแบบไหน?**

ขึ้นอยู่กับคุณว่าจะเลือกรูปแบบไหน JavaScript รองรับทั้งสองแนวทางนี้

--

รูปแบบอีกแบบหนึ่งที่พบได้ทั่วไปในพัฒนาเกมคือการจัดการประสบการณ์ผู้ใช้และประสิทธิภาพของเกม

## รูปแบบ Pub/Sub

✅ Pub/Sub ย่อมาจาก 'publish-subscribe'

รูปแบบนี้เกี่ยวข้องกับแนวคิดที่ว่าส่วนต่าง ๆ ของแอปพลิเคชันของคุณไม่ควรรู้จักกันและกัน ทำไมถึงเป็นเช่นนั้น? เพราะมันทำให้ง่ายต่อการดูว่าเกิดอะไรขึ้นโดยรวม หากส่วนต่าง ๆ ถูกแยกออกจากกัน นอกจากนี้ยังทำให้ง่ายต่อการเปลี่ยนแปลงพฤติกรรมอย่างรวดเร็วหากจำเป็น เราทำสิ่งนี้ได้อย่างไร? เราทำโดยการสร้างแนวคิดบางอย่าง:

- **ข้อความ**: ข้อความมักจะเป็นสตริงข้อความที่มาพร้อมกับข้อมูลเพิ่มเติม (payload) ซึ่งช่วยอธิบายว่าข้อความนั้นเกี่ยวกับอะไร ตัวอย่างข้อความในเกมอาจเป็น `KEY_PRESSED_ENTER`
- **ผู้เผยแพร่**: องค์ประกอบนี้ *เผยแพร่* ข้อความและส่งไปยังผู้สมัครรับข้อความทั้งหมด
- **ผู้สมัครรับข้อความ**: องค์ประกอบนี้ *ฟัง* ข้อความเฉพาะและดำเนินการบางอย่างเป็นผลจากการรับข้อความนั้น เช่น การยิงเลเซอร์

การนำไปใช้มีขนาดเล็กมาก แต่เป็นรูปแบบที่ทรงพลังมาก นี่คือตัวอย่างการนำไปใช้:

```javascript
//set up an EventEmitter class that contains listeners
class EventEmitter {
  constructor() {
    this.listeners = {};
  }
//when a message is received, let the listener to handle its payload
  on(message, listener) {
    if (!this.listeners[message]) {
      this.listeners[message] = [];
    }
    this.listeners[message].push(listener);
  }
//when a message is sent, send it to a listener with some payload
  emit(message, payload = null) {
    if (this.listeners[message]) {
      this.listeners[message].forEach(l => l(message, payload))
    }
  }
}

```

เพื่อใช้โค้ดข้างต้น เราสามารถสร้างการนำไปใช้ขนาดเล็กมาก:

```javascript
//set up a message structure
const Messages = {
  HERO_MOVE_LEFT: 'HERO_MOVE_LEFT'
};
//invoke the eventEmitter you set up above
const eventEmitter = new EventEmitter();
//set up a hero
const hero = createHero(0,0);
//let the eventEmitter know to watch for messages pertaining to the hero moving left, and act on it
eventEmitter.on(Messages.HERO_MOVE_LEFT, () => {
  hero.move(5,0);
});

//set up the window to listen for the keyup event, specifically if the left arrow is hit, emit a message to move the hero left
window.addEventListener('keyup', (evt) => {
  if (evt.key === 'ArrowLeft') {
    eventEmitter.emit(Messages.HERO_MOVE_LEFT)
  }
});
```

ในตัวอย่างข้างต้น เราเชื่อมโยงเหตุการณ์คีย์บอร์ด `ArrowLeft` และส่งข้อความ `HERO_MOVE_LEFT` เราฟังข้อความนั้นและเคลื่อนย้าย `hero` เป็นผลลัพธ์ ความแข็งแกร่งของรูปแบบนี้คือ event listener และ hero ไม่รู้จักกัน คุณสามารถเปลี่ยนแปลง `ArrowLeft` เป็นคีย์ `A` ได้ นอกจากนี้ยังสามารถทำสิ่งที่แตกต่างออกไปบน `ArrowLeft` โดยการแก้ไขฟังก์ชัน `on` ของ eventEmitter:

```javascript
eventEmitter.on(Messages.HERO_MOVE_LEFT, () => {
  hero.move(5,0);
});
```

เมื่อเกมของคุณซับซ้อนขึ้น รูปแบบนี้ยังคงมีความซับซ้อนเท่าเดิม และโค้ดของคุณยังคงสะอาด เป็นที่แนะนำอย่างยิ่งให้ใช้รูปแบบนี้

---

## 🚀 ความท้าทาย

ลองคิดดูว่ารูปแบบ pub-sub สามารถเพิ่มประสิทธิภาพเกมได้อย่างไร ส่วนใดควรส่งข้อความ และเกมควรตอบสนองต่อข้อความเหล่านั้นอย่างไร? นี่เป็นโอกาสของคุณที่จะสร้างสรรค์ ลองคิดถึงเกมใหม่และวิธีที่ส่วนต่าง ๆ ของมันอาจทำงานร่วมกัน

## แบบทดสอบหลังเรียน

[แบบทดสอบหลังเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/30)

## ทบทวนและศึกษาด้วยตัวเอง

เรียนรู้เพิ่มเติมเกี่ยวกับ Pub/Sub โดย [อ่านเกี่ยวกับมัน](https://docs.microsoft.com/azure/architecture/patterns/publisher-subscriber/?WT.mc_id=academic-77807-sagibbon)

## งานที่ได้รับมอบหมาย

[สร้างเกมจำลอง](assignment.md)

---

**ข้อจำกัดความรับผิดชอบ**:  
เอกสารนี้ได้รับการแปลโดยใช้บริการแปลภาษา AI [Co-op Translator](https://github.com/Azure/co-op-translator) แม้ว่าเราจะพยายามให้การแปลมีความถูกต้อง แต่โปรดทราบว่าการแปลอัตโนมัติอาจมีข้อผิดพลาดหรือความไม่ถูกต้อง เอกสารต้นฉบับในภาษาดั้งเดิมควรถือเป็นแหล่งข้อมูลที่เชื่อถือได้ สำหรับข้อมูลที่สำคัญ ขอแนะนำให้ใช้บริการแปลภาษามืออาชีพ เราไม่รับผิดชอบต่อความเข้าใจผิดหรือการตีความผิดที่เกิดจากการใช้การแปลนี้