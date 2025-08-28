<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "4e8250db84b027c9ff816b4e4c093457",
  "translation_date": "2025-08-28T11:33:26+00:00",
  "source_file": "6-space-game/5-keeping-score/README.md",
  "language_code": "en"
}
-->
# Build a Space Game Part 5: Scoring and Lives

## Pre-Lecture Quiz

[Pre-lecture quiz](https://ff-quizzes.netlify.app/web/quiz/37)

In this lesson, you'll learn how to add scoring to a game and manage lives.

## Draw text on the screen

To display a game score on the screen, you need to know how to render text. This can be done using the `fillText()` method on the canvas object. You can also customize aspects like the font, text color, and alignment (left, right, center). Below is an example of code that draws text on the screen.

```javascript
ctx.font = "30px Arial";
ctx.fillStyle = "red";
ctx.textAlign = "right";
ctx.fillText("show this on the screen", 0, 0);
```

✅ Learn more about [how to add text to a canvas](https://developer.mozilla.org/docs/Web/API/Canvas_API/Tutorial/Drawing_text), and feel free to make your text look more stylish!

## Life, as a game concept

In games, the concept of "lives" is simply a number. In a space game, it's common to assign a set number of lives that decrease one by one when your ship takes damage. It's even better if you can represent this visually, like using small ships or hearts instead of just a number.

## What to build

Let's add the following features to your game:

- **Game score**: Award points for every enemy ship destroyed. We suggest 100 points per ship. The score should be displayed in the bottom left corner.
- **Lives**: Your ship starts with three lives. You lose a life whenever an enemy ship collides with you. Lives should be displayed in the bottom right corner using the following graphic: ![life image](../../../../translated_images/life.6fb9f50d53ee0413cd91aa411f7c296e10a1a6de5c4a4197c718b49bf7d63ebf.en.png).

## Recommended steps

Locate the files provided in the `your-work` subfolder. It should contain the following:

```bash
-| assets
  -| enemyShip.png
  -| player.png
  -| laserRed.png
-| index.html
-| app.js
-| package.json
```

Start your project in the `your_work` folder by typing:

```bash
cd your-work
npm start
```

This will start an HTTP server at `http://localhost:5000`. Open a browser and navigate to that address. At this point, you should see the hero and all the enemies. You can move the hero using the left and right arrow keys and shoot down enemies.

### Add code

1. **Copy the required assets** from the `solution/assets/` folder into the `your-work` folder. This includes the `life.png` asset. Add the `lifeImg` to the `window.onload` function:

    ```javascript
    lifeImg = await loadTexture("assets/life.png");
    ```

1. Add the `lifeImg` to the list of assets:

    ```javascript
    let heroImg,
    ...
    lifeImg,
    ...
    eventEmitter = new EventEmitter();
    ```
  
2. **Add variables**. Create variables to represent the total score (starting at 0) and remaining lives (starting at 3). Display these values on the screen.

3. **Extend the `updateGameObjects()` function**. Modify the `updateGameObjects()` function to handle collisions with enemies:

    ```javascript
    enemies.forEach(enemy => {
        const heroRect = hero.rectFromGameObject();
        if (intersectRect(heroRect, enemy.rectFromGameObject())) {
          eventEmitter.emit(Messages.COLLISION_ENEMY_HERO, { enemy });
        }
      })
    ```

4. **Add lives and points**. 
   1. **Initialize variables**. Inside the `Hero` class, under `this.cooldown = 0`, initialize the lives and points:

        ```javascript
        this.life = 3;
        this.points = 0;
        ```

   1. **Display variables on the screen**. Render these values on the screen:

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

   1. **Add methods to the game loop**. Ensure these functions are added to the `window.onload` function under `updateGameObjects()`:

        ```javascript
        drawPoints();
        drawLife();
        ```

1. **Implement game rules**. Add the following rules:

   1. **Deduct a life for every collision between the hero and an enemy**.
   
      Extend the `Hero` class to handle this deduction:

        ```javascript
        decrementLife() {
          this.life--;
          if (this.life === 0) {
            this.dead = true;
          }
        }
        ```

   2. **Increase the score by 100 points for every laser that hits an enemy**.

      Extend the `Hero` class to handle this increment:
    
        ```javascript
          incrementPoints() {
            this.points += 100;
          }
        ```

        Add these functions to your collision event emitters:

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

✅ Explore other games created using JavaScript/Canvas. What common features do they share?

By the end of this task, you should see small 'life' ships displayed in the bottom right corner, points displayed in the bottom left corner, and observe your life count decreasing when you collide with enemies and your score increasing when you shoot enemies. Great job! Your game is almost complete.

---

## 🚀 Challenge

Your code is nearly finished. What do you think your next steps should be?

## Post-Lecture Quiz

[Post-lecture quiz](https://ff-quizzes.netlify.app/web/quiz/38)

## Review & Self Study

Research different ways to increment and decrement game scores and lives. There are some interesting game engines like [PlayFab](https://playfab.com). How could using one of these enhance your game?

## Assignment

[Build a Scoring Game](assignment.md)

---

**Disclaimer**:  
This document has been translated using the AI translation service [Co-op Translator](https://github.com/Azure/co-op-translator). While we aim for accuracy, please note that automated translations may include errors or inaccuracies. The original document in its native language should be regarded as the authoritative source. For critical information, professional human translation is advised. We are not responsible for any misunderstandings or misinterpretations resulting from the use of this translation.