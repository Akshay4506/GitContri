<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "23f088add24f0f1fa51014a9e27ea280",
  "translation_date": "2025-08-26T21:56:17+00:00",
  "source_file": "6-space-game/3-moving-elements-around/README.md",
  "language_code": "th"
}
-->
# สร้างเกมอวกาศ ตอนที่ 3: เพิ่มการเคลื่อนไหว

## แบบทดสอบก่อนเรียน

[แบบทดสอบก่อนเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/33)

เกมจะไม่สนุกเลยถ้าไม่มีเอเลี่ยนวิ่งไปมาบนหน้าจอ! ในเกมนี้ เราจะใช้การเคลื่อนไหวสองประเภท:

- **การเคลื่อนไหวด้วยคีย์บอร์ด/เมาส์**: เมื่อผู้ใช้โต้ตอบกับคีย์บอร์ดหรือเมาส์เพื่อเคลื่อนย้ายวัตถุบนหน้าจอ
- **การเคลื่อนไหวที่เกิดจากเกม**: เมื่อเกมเคลื่อนย้ายวัตถุในช่วงเวลาที่กำหนด

แล้วเราจะเคลื่อนย้ายสิ่งต่าง ๆ บนหน้าจอได้อย่างไร? ทุกอย่างเกี่ยวกับพิกัดคาร์ทีเซียน: เราเปลี่ยนตำแหน่ง (x, y) ของวัตถุแล้ววาดหน้าจอใหม่

โดยทั่วไป คุณต้องทำตามขั้นตอนต่อไปนี้เพื่อให้เกิด *การเคลื่อนไหว* บนหน้าจอ:

1. **กำหนดตำแหน่งใหม่** ให้กับวัตถุ เพื่อให้ดูเหมือนวัตถุได้เคลื่อนที่
2. **ล้างหน้าจอ** หน้าจอจำเป็นต้องถูกล้างระหว่างการวาดใหม่ เราสามารถล้างได้โดยการวาดสี่เหลี่ยมที่เติมด้วยสีพื้นหลัง
3. **วาดวัตถุใหม่** ที่ตำแหน่งใหม่ การทำเช่นนี้จะทำให้เราสามารถเคลื่อนย้ายวัตถุจากตำแหน่งหนึ่งไปยังอีกตำแหน่งหนึ่งได้สำเร็จ

นี่คือตัวอย่างโค้ด:

```javascript
//set the hero's location
hero.x += 5;
// clear the rectangle that hosts the hero
ctx.clearRect(0, 0, canvas.width, canvas.height);
// redraw the game background and hero
ctx.fillRect(0, 0, canvas.width, canvas.height)
ctx.fillStyle = "black";
ctx.drawImage(heroImg, hero.x, hero.y);
```

✅ คุณคิดว่าเหตุผลอะไรที่การวาดฮีโร่ใหม่หลายเฟรมต่อวินาทีอาจทำให้เกิดค่าใช้จ่ายด้านประสิทธิภาพ? อ่านเพิ่มเติมเกี่ยวกับ [ทางเลือกของรูปแบบนี้](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Optimizing_canvas)

## จัดการเหตุการณ์คีย์บอร์ด

คุณสามารถจัดการเหตุการณ์ได้โดยการผูกเหตุการณ์เฉพาะกับโค้ด เหตุการณ์คีย์บอร์ดจะถูกกระตุ้นบนหน้าต่างทั้งหมด ในขณะที่เหตุการณ์เมาส์ เช่น `click` สามารถเชื่อมโยงกับการคลิกองค์ประกอบเฉพาะ เราจะใช้เหตุการณ์คีย์บอร์ดตลอดโครงการนี้

ในการจัดการเหตุการณ์ คุณต้องใช้เมธอด `addEventListener()` ของหน้าต่าง และให้พารามิเตอร์สองตัวเป็นอินพุต พารามิเตอร์แรกคือชื่อของเหตุการณ์ เช่น `keyup` พารามิเตอร์ที่สองคือฟังก์ชันที่จะถูกเรียกใช้เมื่อเหตุการณ์เกิดขึ้น

นี่คือตัวอย่าง:

```javascript
window.addEventListener('keyup', (evt) => {
  // `evt.key` = string representation of the key
  if (evt.key === 'ArrowUp') {
    // do something
  }
})
```

สำหรับเหตุการณ์คีย์ มีคุณสมบัติสองอย่างในเหตุการณ์ที่คุณสามารถใช้เพื่อตรวจสอบว่าปุ่มใดถูกกด:

- `key` เป็นตัวแทนข้อความของปุ่มที่ถูกกด เช่น `ArrowUp`
- `keyCode` เป็นตัวแทนตัวเลข เช่น `37` ซึ่งสอดคล้องกับ `ArrowLeft`

✅ การจัดการเหตุการณ์คีย์มีประโยชน์นอกเหนือจากการพัฒนาเกม คุณคิดว่าเทคนิคนี้สามารถนำไปใช้ในกรณีอื่น ๆ ได้อย่างไร?

### ปุ่มพิเศษ: ข้อควรระวัง

มีปุ่ม *พิเศษ* บางปุ่มที่ส่งผลต่อหน้าต่าง นั่นหมายความว่าหากคุณกำลังฟังเหตุการณ์ `keyup` และใช้ปุ่มพิเศษเหล่านี้เพื่อเคลื่อนย้ายฮีโร่ของคุณ มันจะทำให้เกิดการเลื่อนในแนวนอนด้วย ดังนั้นคุณอาจต้องการ *ปิด* พฤติกรรมเริ่มต้นของเบราว์เซอร์ในขณะที่คุณสร้างเกมของคุณ คุณต้องใช้โค้ดแบบนี้:

```javascript
let onKeyDown = function (e) {
  console.log(e.keyCode);
  switch (e.keyCode) {
    case 37:
    case 39:
    case 38:
    case 40: // Arrow keys
    case 32:
      e.preventDefault();
      break; // Space
    default:
      break; // do not block other keys
  }
};

window.addEventListener('keydown', onKeyDown);
```

โค้ดด้านบนจะทำให้แน่ใจว่าปุ่มลูกศรและปุ่ม Space จะมีพฤติกรรม *เริ่มต้น* ถูกปิด การปิดพฤติกรรมนี้เกิดขึ้นเมื่อเราเรียกใช้ `e.preventDefault()`

## การเคลื่อนไหวที่เกิดจากเกม

เราสามารถทำให้สิ่งต่าง ๆ เคลื่อนไหวได้เองโดยใช้ตัวจับเวลา เช่น ฟังก์ชัน `setTimeout()` หรือ `setInterval()` ที่อัปเดตตำแหน่งของวัตถุในแต่ละช่วงเวลา นี่คือตัวอย่าง:

```javascript
let id = setInterval(() => {
  //move the enemy on the y axis
  enemy.y += 10;
})
```

## ลูปของเกม

ลูปของเกมเป็นแนวคิดที่เป็นฟังก์ชันที่ถูกเรียกใช้ในช่วงเวลาปกติ เรียกว่าลูปของเกมเพราะทุกสิ่งที่ควรมองเห็นได้โดยผู้ใช้จะถูกวาดในลูปนี้ ลูปของเกมจะใช้วัตถุเกมทั้งหมดที่เป็นส่วนหนึ่งของเกม วาดทั้งหมดเว้นแต่วัตถุใด ๆ จะไม่เป็นส่วนหนึ่งของเกมอีกต่อไป ตัวอย่างเช่น หากวัตถุเป็นศัตรูที่ถูกเลเซอร์ยิงและระเบิด มันจะไม่เป็นส่วนหนึ่งของลูปเกมอีกต่อไป (คุณจะได้เรียนรู้เพิ่มเติมในบทเรียนถัดไป)

นี่คือลูปของเกมที่แสดงในโค้ด:

```javascript
let gameLoopId = setInterval(() =>
  function gameLoop() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = "black";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    drawHero();
    drawEnemies();
    drawStaticObjects();
}, 200);
```

ลูปด้านบนจะถูกเรียกใช้ทุก ๆ `200` มิลลิวินาทีเพื่อวาดแคนวาสใหม่ คุณสามารถเลือกช่วงเวลาที่เหมาะสมที่สุดสำหรับเกมของคุณได้

## ดำเนินการต่อในเกมอวกาศ

คุณจะนำโค้ดที่มีอยู่มาเพิ่มฟีเจอร์ใหม่ คุณสามารถเริ่มต้นด้วยโค้ดที่คุณทำเสร็จในตอนที่ 1 หรือใช้โค้ดใน [Part II- starter](../../../../6-space-game/3-moving-elements-around/your-work)

- **การเคลื่อนย้ายฮีโร่**: คุณจะเพิ่มโค้ดเพื่อให้สามารถเคลื่อนย้ายฮีโร่ด้วยปุ่มลูกศร
- **การเคลื่อนย้ายศัตรู**: คุณจะต้องเพิ่มโค้ดเพื่อให้ศัตรูเคลื่อนที่จากบนลงล่างในอัตราที่กำหนด

## ขั้นตอนที่แนะนำ

ค้นหาไฟล์ที่ถูกสร้างไว้ให้คุณในโฟลเดอร์ `your-work` ซึ่งควรมีดังนี้:

```bash
-| assets
  -| enemyShip.png
  -| player.png
-| index.html
-| app.js
-| package.json
```

เริ่มโครงการของคุณในโฟลเดอร์ `your_work` โดยพิมพ์:

```bash
cd your-work
npm start
```

คำสั่งด้านบนจะเริ่ม HTTP Server ที่อยู่ `http://localhost:5000` เปิดเบราว์เซอร์และใส่ที่อยู่นั้น ตอนนี้มันควรจะแสดงฮีโร่และศัตรูทั้งหมด แต่ยังไม่มีอะไรเคลื่อนไหว!

### เพิ่มโค้ด

1. **เพิ่มออบเจกต์เฉพาะ** สำหรับ `hero`, `enemy` และ `game object` ซึ่งควรมีคุณสมบัติ `x` และ `y` (จำส่วนที่เกี่ยวกับ [Inheritance หรือ composition](../README.md))

   *คำแนะนำ* `game object` ควรเป็นออบเจกต์ที่มี `x` และ `y` และความสามารถในการวาดตัวเองลงบนแคนวาส

   >คำแนะนำ: เริ่มต้นด้วยการเพิ่มคลาส GameObject ใหม่พร้อมตัวสร้างดังนี้ แล้ววาดมันลงบนแคนวาส:

    ```javascript
        
    class GameObject {
      constructor(x, y) {
        this.x = x;
        this.y = y;
        this.dead = false;
        this.type = "";
        this.width = 0;
        this.height = 0;
        this.img = undefined;
      }
    
      draw(ctx) {
        ctx.drawImage(this.img, this.x, this.y, this.width, this.height);
      }
    }
    ```

    จากนั้น ขยาย GameObject เพื่อสร้าง Hero และ Enemy

    ```javascript
    class Hero extends GameObject {
      constructor(x, y) {
        ...it needs an x, y, type, and speed
      }
    }
    ```

    ```javascript
    class Enemy extends GameObject {
      constructor(x, y) {
        super(x, y);
        (this.width = 98), (this.height = 50);
        this.type = "Enemy";
        let id = setInterval(() => {
          if (this.y < canvas.height - this.height) {
            this.y += 5;
          } else {
            console.log('Stopped at', this.y)
            clearInterval(id);
          }
        }, 300)
      }
    }
    ```

2. **เพิ่มตัวจัดการเหตุการณ์คีย์** เพื่อจัดการการนำทางด้วยปุ่ม (เคลื่อนย้ายฮีโร่ขึ้น/ลง ซ้าย/ขวา)

   *จำไว้* ว่านี่คือระบบคาร์ทีเซียน มุมบนซ้ายคือ `0,0` และอย่าลืมเพิ่มโค้ดเพื่อหยุด *พฤติกรรมเริ่มต้น*

   >คำแนะนำ: สร้างฟังก์ชัน onKeyDown ของคุณและผูกมันกับหน้าต่าง:

   ```javascript
    let onKeyDown = function (e) {
	      console.log(e.keyCode);
	        ...add the code from the lesson above to stop default behavior
	      }
    };

    window.addEventListener("keydown", onKeyDown);
   ```
    
   ตรวจสอบคอนโซลของเบราว์เซอร์ ณ จุดนี้ และดูการกดปุ่มที่ถูกบันทึก

3. **นำไปใช้** [รูปแบบ Pub sub](../README.md) เพื่อให้โค้ดของคุณสะอาดเมื่อคุณทำส่วนที่เหลือ

   ในการทำส่วนสุดท้ายนี้ คุณสามารถ:

   1. **เพิ่มตัวฟังเหตุการณ์** บนหน้าต่าง:

       ```javascript
        window.addEventListener("keyup", (evt) => {
          if (evt.key === "ArrowUp") {
            eventEmitter.emit(Messages.KEY_EVENT_UP);
          } else if (evt.key === "ArrowDown") {
            eventEmitter.emit(Messages.KEY_EVENT_DOWN);
          } else if (evt.key === "ArrowLeft") {
            eventEmitter.emit(Messages.KEY_EVENT_LEFT);
          } else if (evt.key === "ArrowRight") {
            eventEmitter.emit(Messages.KEY_EVENT_RIGHT);
          }
        });
        ```

    1. **สร้างคลาส EventEmitter** เพื่อเผยแพร่และสมัครสมาชิกข้อความ:

        ```javascript
        class EventEmitter {
          constructor() {
            this.listeners = {};
          }
        
          on(message, listener) {
            if (!this.listeners[message]) {
              this.listeners[message] = [];
            }
            this.listeners[message].push(listener);
          }
        
          emit(message, payload = null) {
            if (this.listeners[message]) {
              this.listeners[message].forEach((l) => l(message, payload));
            }
          }
        }
        ```

    1. **เพิ่มค่าคงที่** และตั้งค่า EventEmitter:

        ```javascript
        const Messages = {
          KEY_EVENT_UP: "KEY_EVENT_UP",
          KEY_EVENT_DOWN: "KEY_EVENT_DOWN",
          KEY_EVENT_LEFT: "KEY_EVENT_LEFT",
          KEY_EVENT_RIGHT: "KEY_EVENT_RIGHT",
        };
        
        let heroImg, 
            enemyImg, 
            laserImg,
            canvas, ctx, 
            gameObjects = [], 
            hero, 
            eventEmitter = new EventEmitter();
        ```

    1. **เริ่มต้นเกม**

    ```javascript
    function initGame() {
      gameObjects = [];
      createEnemies();
      createHero();
    
      eventEmitter.on(Messages.KEY_EVENT_UP, () => {
        hero.y -=5 ;
      })
    
      eventEmitter.on(Messages.KEY_EVENT_DOWN, () => {
        hero.y += 5;
      });
    
      eventEmitter.on(Messages.KEY_EVENT_LEFT, () => {
        hero.x -= 5;
      });
    
      eventEmitter.on(Messages.KEY_EVENT_RIGHT, () => {
        hero.x += 5;
      });
    }
    ```

1. **ตั้งค่าลูปของเกม**

   ปรับปรุงฟังก์ชัน window.onload เพื่อเริ่มต้นเกมและตั้งค่าลูปของเกมในช่วงเวลาที่เหมาะสม คุณยังจะเพิ่มลำแสงเลเซอร์:

    ```javascript
    window.onload = async () => {
      canvas = document.getElementById("canvas");
      ctx = canvas.getContext("2d");
      heroImg = await loadTexture("assets/player.png");
      enemyImg = await loadTexture("assets/enemyShip.png");
      laserImg = await loadTexture("assets/laserRed.png");
    
      initGame();
      let gameLoopId = setInterval(() => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "black";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        drawGameObjects(ctx);
      }, 100)
      
    };
    ```

5. **เพิ่มโค้ด** เพื่อเคลื่อนย้ายศัตรูในช่วงเวลาที่กำหนด

    ปรับปรุงฟังก์ชัน `createEnemies()` เพื่อสร้างศัตรูและเพิ่มพวกมันลงในคลาส gameObjects ใหม่:

    ```javascript
    function createEnemies() {
      const MONSTER_TOTAL = 5;
      const MONSTER_WIDTH = MONSTER_TOTAL * 98;
      const START_X = (canvas.width - MONSTER_WIDTH) / 2;
      const STOP_X = START_X + MONSTER_WIDTH;
    
      for (let x = START_X; x < STOP_X; x += 98) {
        for (let y = 0; y < 50 * 5; y += 50) {
          const enemy = new Enemy(x, y);
          enemy.img = enemyImg;
          gameObjects.push(enemy);
        }
      }
    }
    ```
    
    และเพิ่มฟังก์ชัน `createHero()` เพื่อทำกระบวนการที่คล้ายกันสำหรับฮีโร่
    
    ```javascript
    function createHero() {
      hero = new Hero(
        canvas.width / 2 - 45,
        canvas.height - canvas.height / 4
      );
      hero.img = heroImg;
      gameObjects.push(hero);
    }
    ```

    และสุดท้าย เพิ่มฟังก์ชัน `drawGameObjects()` เพื่อเริ่มการวาด:

    ```javascript
    function drawGameObjects(ctx) {
      gameObjects.forEach(go => go.draw(ctx));
    }
    ```

    ศัตรูของคุณควรเริ่มเคลื่อนที่เข้าหายานอวกาศฮีโร่ของคุณ!

---

## 🚀 ความท้าทาย

อย่างที่คุณเห็น โค้ดของคุณอาจกลายเป็น 'โค้ดสปาเก็ตตี้' เมื่อคุณเริ่มเพิ่มฟังก์ชัน ตัวแปร และคลาส คุณจะจัดระเบียบโค้ดของคุณให้ดีขึ้นได้อย่างไรเพื่อให้อ่านง่ายขึ้น? ลองร่างระบบเพื่อจัดระเบียบโค้ดของคุณ แม้ว่ามันจะยังอยู่ในไฟล์เดียวก็ตาม

## แบบทดสอบหลังเรียน

[แบบทดสอบหลังเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/34)

## ทบทวนและศึกษาด้วยตนเอง

ในขณะที่เราเขียนเกมของเราโดยไม่ใช้เฟรมเวิร์ก มีเฟรมเวิร์ก Canvas ที่ใช้ JavaScript สำหรับการพัฒนาเกมมากมาย ใช้เวลาอ่านเพิ่มเติมเกี่ยวกับ [เฟรมเวิร์กเหล่านี้](https://github.com/collections/javascript-game-engines)

## งานที่ได้รับมอบหมาย

[แสดงความคิดเห็นในโค้ดของคุณ](assignment.md)

---

**ข้อจำกัดความรับผิดชอบ**:  
เอกสารนี้ได้รับการแปลโดยใช้บริการแปลภาษา AI [Co-op Translator](https://github.com/Azure/co-op-translator) แม้ว่าเราจะพยายามให้การแปลมีความถูกต้อง แต่โปรดทราบว่าการแปลโดยอัตโนมัติอาจมีข้อผิดพลาดหรือความไม่ถูกต้อง เอกสารต้นฉบับในภาษาดั้งเดิมควรถือเป็นแหล่งข้อมูลที่เชื่อถือได้ สำหรับข้อมูลที่สำคัญ ขอแนะนำให้ใช้บริการแปลภาษามืออาชีพ เราไม่รับผิดชอบต่อความเข้าใจผิดหรือการตีความผิดที่เกิดจากการใช้การแปลนี้