<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "4e8250db84b027c9ff816b4e4c093457",
  "translation_date": "2025-08-26T21:53:09+00:00",
  "source_file": "6-space-game/5-keeping-score/README.md",
  "language_code": "th"
}
-->
# สร้างเกมอวกาศ ตอนที่ 5: คะแนนและชีวิต

## แบบทดสอบก่อนเรียน

[แบบทดสอบก่อนเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/37)

ในบทเรียนนี้ คุณจะได้เรียนรู้วิธีเพิ่มคะแนนในเกมและคำนวณจำนวนชีวิต

## วาดข้อความบนหน้าจอ

เพื่อแสดงคะแนนของเกมบนหน้าจอ คุณจำเป็นต้องรู้วิธีวางข้อความบนหน้าจอ คำตอบคือการใช้เมธอด `fillText()` บนวัตถุ canvas คุณยังสามารถควบคุมองค์ประกอบอื่น ๆ เช่น ฟอนต์ที่ใช้ สีของข้อความ และการจัดตำแหน่ง (ซ้าย ขวา กลาง) ด้านล่างนี้เป็นโค้ดที่วาดข้อความบนหน้าจอ

```javascript
ctx.font = "30px Arial";
ctx.fillStyle = "red";
ctx.textAlign = "right";
ctx.fillText("show this on the screen", 0, 0);
```

✅ อ่านเพิ่มเติมเกี่ยวกับ [วิธีเพิ่มข้อความลงใน canvas](https://developer.mozilla.org/docs/Web/API/Canvas_API/Tutorial/Drawing_text) และลองทำให้ข้อความของคุณดูน่าสนใจยิ่งขึ้น!

## ชีวิตในฐานะองค์ประกอบของเกม

แนวคิดของการมีชีวิตในเกมเป็นเพียงตัวเลข ในบริบทของเกมอวกาศ มักจะกำหนดจำนวนชีวิตที่ลดลงทีละหนึ่งเมื่อยานของคุณได้รับความเสียหาย จะดีมากถ้าคุณสามารถแสดงชีวิตในรูปแบบกราฟิก เช่น ยานขนาดเล็กหรือหัวใจแทนตัวเลข

## สิ่งที่ต้องสร้าง

เพิ่มสิ่งต่อไปนี้ลงในเกมของคุณ:

- **คะแนนเกม**: ทุกครั้งที่ยานศัตรูถูกทำลาย ฮีโร่ควรได้รับคะแนน เราแนะนำให้เพิ่ม 100 คะแนนต่อยาน คะแนนเกมควรแสดงที่มุมล่างซ้าย
- **ชีวิต**: ยานของคุณมีสามชีวิต คุณจะเสียชีวิตทุกครั้งที่ยานศัตรูชนคุณ คะแนนชีวิตควรแสดงที่มุมล่างขวาและใช้กราฟิกดังนี้ ![life image](../../../../translated_images/life.6fb9f50d53ee0413cd91aa411f7c296e10a1a6de5c4a4197c718b49bf7d63ebf.th.png)

## ขั้นตอนที่แนะนำ

ค้นหาไฟล์ที่ถูกสร้างไว้ให้คุณในโฟลเดอร์ `your-work` ซึ่งควรมีไฟล์ดังนี้:

```bash
-| assets
  -| enemyShip.png
  -| player.png
  -| laserRed.png
-| index.html
-| app.js
-| package.json
```

เริ่มโปรเจกต์ของคุณในโฟลเดอร์ `your_work` โดยพิมพ์:

```bash
cd your-work
npm start
```

คำสั่งด้านบนจะเริ่ม HTTP Server ที่ที่อยู่ `http://localhost:5000` เปิดเบราว์เซอร์และใส่ที่อยู่นั้น ตอนนี้คุณควรเห็นฮีโร่และศัตรูทั้งหมด และเมื่อคุณกดลูกศรซ้ายและขวา ฮีโร่จะเคลื่อนที่และสามารถยิงศัตรูได้

### เพิ่มโค้ด

1. **คัดลอกทรัพยากรที่จำเป็น** จากโฟลเดอร์ `solution/assets/` ไปยังโฟลเดอร์ `your-work` คุณจะเพิ่มทรัพยากร `life.png` เพิ่ม lifeImg ลงในฟังก์ชัน window.onload:

    ```javascript
    lifeImg = await loadTexture("assets/life.png");
    ```

1. เพิ่ม `lifeImg` ลงในรายการทรัพยากร:

    ```javascript
    let heroImg,
    ...
    lifeImg,
    ...
    eventEmitter = new EventEmitter();
    ```
  
2. **เพิ่มตัวแปร** เพิ่มโค้ดที่แสดงคะแนนรวม (0) และจำนวนชีวิตที่เหลือ (3) แสดงคะแนนเหล่านี้บนหน้าจอ

3. **ขยายฟังก์ชัน `updateGameObjects()`** ขยายฟังก์ชัน `updateGameObjects()` เพื่อจัดการการชนกับศัตรู:

    ```javascript
    enemies.forEach(enemy => {
        const heroRect = hero.rectFromGameObject();
        if (intersectRect(heroRect, enemy.rectFromGameObject())) {
          eventEmitter.emit(Messages.COLLISION_ENEMY_HERO, { enemy });
        }
      })
    ```

4. **เพิ่มชีวิตและคะแนน**
   1. **กำหนดค่าเริ่มต้นให้ตัวแปร** ใต้ `this.cooldown = 0` ในคลาส `Hero` ตั้งค่าชีวิตและคะแนน:

        ```javascript
        this.life = 3;
        this.points = 0;
        ```

   1. **วาดตัวแปรบนหน้าจอ** วาดค่าตัวแปรเหล่านี้บนหน้าจอ:

        ```javascript
        function drawLife() {
          // TODO, 35, 27
          const START_POS = canvas.width - 180;
          for(let i=0; i < hero.life; i++ ) {
            ctx.drawImage(
              lifeImg, 
              START_POS + (45 * (i+1) ), 
              canvas.height - 37);
          }
        }
        
        function drawPoints() {
          ctx.font = "30px Arial";
          ctx.fillStyle = "red";
          ctx.textAlign = "left";
          drawText("Points: " + hero.points, 10, canvas.height-20);
        }
        
        function drawText(message, x, y) {
          ctx.fillText(message, x, y);
        }

        ```

   1. **เพิ่มเมธอดลงใน Game loop** ตรวจสอบให้แน่ใจว่าคุณเพิ่มฟังก์ชันเหล่านี้ลงในฟังก์ชัน window.onload ใต้ `updateGameObjects()`:

        ```javascript
        drawPoints();
        drawLife();
        ```

1. **กำหนดกฎของเกม** กำหนดกฎของเกมดังนี้:

   1. **ทุกครั้งที่ฮีโร่ชนกับศัตรู** ลดจำนวนชีวิตลงหนึ่ง

      ขยายคลาส `Hero` เพื่อทำการลดชีวิต:

        ```javascript
        decrementLife() {
          this.life--;
          if (this.life === 0) {
            this.dead = true;
          }
        }
        ```

   2. **ทุกครั้งที่เลเซอร์ชนศัตรู** เพิ่มคะแนนเกม 100 คะแนน

      ขยายคลาส Hero เพื่อเพิ่มคะแนน:

        ```javascript
          incrementPoints() {
            this.points += 100;
          }
        ```

        เพิ่มฟังก์ชันเหล่านี้ลงใน Collision Event Emitters:

        ```javascript
        eventEmitter.on(Messages.COLLISION_ENEMY_LASER, (_, { first, second }) => {
           first.dead = true;
           second.dead = true;
           hero.incrementPoints();
        })

        eventEmitter.on(Messages.COLLISION_ENEMY_HERO, (_, { enemy }) => {
           enemy.dead = true;
           hero.decrementLife();
        });
        ```

✅ ลองค้นคว้าเพิ่มเติมเกี่ยวกับเกมอื่น ๆ ที่สร้างขึ้นโดยใช้ JavaScript/Canvas เกมเหล่านี้มีลักษณะร่วมกันอย่างไร?

เมื่อคุณทำงานนี้เสร็จ คุณควรเห็นยานชีวิตเล็ก ๆ ที่มุมล่างขวา คะแนนที่มุมล่างซ้าย และคุณควรเห็นจำนวนชีวิตลดลงเมื่อชนกับศัตรู และคะแนนเพิ่มขึ้นเมื่อยิงศัตรู ยอดเยี่ยม! เกมของคุณใกล้จะเสร็จสมบูรณ์แล้ว

---

## 🚀 ความท้าทาย

โค้ดของคุณใกล้จะเสร็จสมบูรณ์แล้ว คุณมองเห็นขั้นตอนต่อไปของคุณหรือไม่?

## แบบทดสอบหลังเรียน

[แบบทดสอบหลังเรียน](https://ashy-river-0debb7803.1.azurestaticapps.net/quiz/38)

## ทบทวนและศึกษาด้วยตนเอง

ค้นคว้าวิธีการเพิ่มและลดคะแนนเกมและชีวิต มีเอนจินเกมที่น่าสนใจ เช่น [PlayFab](https://playfab.com) การใช้เอนจินเหล่านี้จะช่วยเพิ่มประสิทธิภาพให้เกมของคุณได้อย่างไร?

## งานที่ได้รับมอบหมาย

[สร้างเกมที่มีการนับคะแนน](assignment.md)

---

**ข้อจำกัดความรับผิดชอบ**:  
เอกสารนี้ได้รับการแปลโดยใช้บริการแปลภาษา AI [Co-op Translator](https://github.com/Azure/co-op-translator) แม้ว่าเราจะพยายามให้การแปลมีความถูกต้องมากที่สุด แต่โปรดทราบว่าการแปลอัตโนมัติอาจมีข้อผิดพลาดหรือความไม่ถูกต้อง เอกสารต้นฉบับในภาษาดั้งเดิมควรถือเป็นแหล่งข้อมูลที่เชื่อถือได้ สำหรับข้อมูลที่สำคัญ ขอแนะนำให้ใช้บริการแปลภาษามืออาชีพ เราไม่รับผิดชอบต่อความเข้าใจผิดหรือการตีความผิดที่เกิดจากการใช้การแปลนี้