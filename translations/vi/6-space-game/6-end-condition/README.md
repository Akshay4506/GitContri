<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "01336cddd638242e99b133614111ea40",
  "translation_date": "2025-08-27T22:41:02+00:00",
  "source_file": "6-space-game/6-end-condition/README.md",
  "language_code": "vi"
}
-->
# Xây dựng trò chơi không gian Phần 6: Kết thúc và Khởi động lại

## Câu hỏi trước bài giảng

[Câu hỏi trước bài giảng](https://ff-quizzes.netlify.app/web/quiz/39)

Có nhiều cách để biểu thị *điều kiện kết thúc* trong một trò chơi. Là người tạo ra trò chơi, bạn có quyền quyết định lý do tại sao trò chơi kết thúc. Dưới đây là một số lý do, nếu chúng ta giả định đang nói về trò chơi không gian mà bạn đã xây dựng cho đến nay:

- **`N` tàu địch đã bị tiêu diệt**: Đây là một cách khá phổ biến nếu bạn chia trò chơi thành các cấp độ khác nhau, yêu cầu tiêu diệt `N` tàu địch để hoàn thành một cấp độ.
- **Tàu của bạn đã bị phá hủy**: Có những trò chơi mà bạn sẽ thua nếu tàu của bạn bị phá hủy. Một cách tiếp cận phổ biến khác là sử dụng khái niệm "mạng sống". Mỗi lần tàu của bạn bị phá hủy, bạn sẽ mất một mạng sống. Khi tất cả mạng sống bị mất, bạn sẽ thua trò chơi.
- **Bạn đã thu thập được `N` điểm**: Một điều kiện kết thúc phổ biến khác là thu thập điểm. Cách bạn kiếm điểm tùy thuộc vào bạn, nhưng thường thì điểm được gán cho các hoạt động như tiêu diệt tàu địch hoặc thu thập các vật phẩm mà tàu địch *rơi* ra khi bị phá hủy.
- **Hoàn thành một cấp độ**: Điều này có thể bao gồm nhiều điều kiện như tiêu diệt `X` tàu địch, thu thập `Y` điểm hoặc có thể là thu thập một vật phẩm cụ thể.

## Khởi động lại

Nếu mọi người thích trò chơi của bạn, họ có khả năng muốn chơi lại. Khi trò chơi kết thúc vì bất kỳ lý do nào, bạn nên cung cấp tùy chọn để khởi động lại.

✅ Hãy suy nghĩ một chút về các điều kiện mà bạn thấy trò chơi kết thúc, và sau đó cách bạn được nhắc để khởi động lại.

## Những gì cần xây dựng

Bạn sẽ thêm các quy tắc sau vào trò chơi của mình:

1. **Chiến thắng trò chơi**. Khi tất cả tàu địch bị tiêu diệt, bạn sẽ chiến thắng trò chơi. Ngoài ra, hiển thị một thông báo chiến thắng.
1. **Khởi động lại**. Khi tất cả mạng sống của bạn bị mất hoặc trò chơi đã được chiến thắng, bạn nên cung cấp cách để khởi động lại trò chơi. Nhớ rằng! Bạn sẽ cần khởi tạo lại trò chơi và xóa trạng thái trò chơi trước đó.

## Các bước đề xuất

Tìm các tệp đã được tạo cho bạn trong thư mục con `your-work`. Nó sẽ chứa các tệp sau:

```bash
-| assets
  -| enemyShip.png
  -| player.png
  -| laserRed.png
  -| life.png
-| index.html
-| app.js
-| package.json
```

Bạn bắt đầu dự án của mình trong thư mục `your_work` bằng cách nhập:

```bash
cd your-work
npm start
```

Lệnh trên sẽ khởi động một HTTP Server tại địa chỉ `http://localhost:5000`. Mở trình duyệt và nhập địa chỉ đó. Trò chơi của bạn sẽ ở trạng thái có thể chơi được.

> mẹo: để tránh cảnh báo trong Visual Studio Code, chỉnh sửa hàm `window.onload` để gọi `gameLoopId` như hiện tại (không có `let`), và khai báo `gameLoopId` ở đầu tệp, độc lập: `let gameLoopId;`

### Thêm mã

1. **Theo dõi điều kiện kết thúc**. Thêm mã để theo dõi số lượng tàu địch hoặc nếu tàu của người chơi đã bị phá hủy bằng cách thêm hai hàm sau:

    ```javascript
    function isHeroDead() {
      return hero.life <= 0;
    }

    function isEnemiesDead() {
      const enemies = gameObjects.filter((go) => go.type === "Enemy" && !go.dead);
      return enemies.length === 0;
    }
    ```

1. **Thêm logic vào trình xử lý thông báo**. Chỉnh sửa `eventEmitter` để xử lý các điều kiện này:

    ```javascript
    eventEmitter.on(Messages.COLLISION_ENEMY_LASER, (_, { first, second }) => {
        first.dead = true;
        second.dead = true;
        hero.incrementPoints();

        if (isEnemiesDead()) {
          eventEmitter.emit(Messages.GAME_END_WIN);
        }
    });

    eventEmitter.on(Messages.COLLISION_ENEMY_HERO, (_, { enemy }) => {
        enemy.dead = true;
        hero.decrementLife();
        if (isHeroDead())  {
          eventEmitter.emit(Messages.GAME_END_LOSS);
          return; // loss before victory
        }
        if (isEnemiesDead()) {
          eventEmitter.emit(Messages.GAME_END_WIN);
        }
    });
    
    eventEmitter.on(Messages.GAME_END_WIN, () => {
        endGame(true);
    });
      
    eventEmitter.on(Messages.GAME_END_LOSS, () => {
      endGame(false);
    });
    ```

1. **Thêm các loại thông báo mới**. Thêm các Thông báo này vào đối tượng constants:

    ```javascript
    GAME_END_LOSS: "GAME_END_LOSS",
    GAME_END_WIN: "GAME_END_WIN",
    ```

2. **Thêm mã khởi động lại** để khởi động lại trò chơi khi nhấn một nút được chọn.

   1. **Lắng nghe phím `Enter`**. Chỉnh sửa eventListener của cửa sổ để lắng nghe phím này:

    ```javascript
     else if(evt.key === "Enter") {
        eventEmitter.emit(Messages.KEY_EVENT_ENTER);
      }
    ```

   1. **Thêm thông báo khởi động lại**. Thêm Thông báo này vào constants của bạn:

        ```javascript
        KEY_EVENT_ENTER: "KEY_EVENT_ENTER",
        ```

1. **Triển khai các quy tắc trò chơi**. Triển khai các quy tắc trò chơi sau:

   1. **Điều kiện chiến thắng của người chơi**. Khi tất cả tàu địch bị tiêu diệt, hiển thị thông báo chiến thắng.

      1. Đầu tiên, tạo hàm `displayMessage()`:

        ```javascript
        function displayMessage(message, color = "red") {
          ctx.font = "30px Arial";
          ctx.fillStyle = color;
          ctx.textAlign = "center";
          ctx.fillText(message, canvas.width / 2, canvas.height / 2);
        }
        ```

      1. Tạo hàm `endGame()`:

        ```javascript
        function endGame(win) {
          clearInterval(gameLoopId);
        
          // set a delay so we are sure any paints have finished
          setTimeout(() => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = "black";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            if (win) {
              displayMessage(
                "Victory!!! Pew Pew... - Press [Enter] to start a new game Captain Pew Pew",
                "green"
              );
            } else {
              displayMessage(
                "You died !!! Press [Enter] to start a new game Captain Pew Pew"
              );
            }
          }, 200)  
        }
        ```

   1. **Logic khởi động lại**. Khi tất cả mạng sống bị mất hoặc người chơi chiến thắng trò chơi, hiển thị rằng trò chơi có thể được khởi động lại. Ngoài ra, khởi động lại trò chơi khi phím *khởi động lại* được nhấn (bạn có thể quyết định phím nào sẽ được gán để khởi động lại).

      1. Tạo hàm `resetGame()`:

        ```javascript
        function resetGame() {
          if (gameLoopId) {
            clearInterval(gameLoopId);
            eventEmitter.clear();
            initGame();
            gameLoopId = setInterval(() => {
              ctx.clearRect(0, 0, canvas.width, canvas.height);
              ctx.fillStyle = "black";
              ctx.fillRect(0, 0, canvas.width, canvas.height);
              drawPoints();
              drawLife();
              updateGameObjects();
              drawGameObjects(ctx);
            }, 100);
          }
        }
        ```

     1. Thêm một lệnh gọi đến `eventEmitter` để khởi động lại trò chơi trong `initGame()`:

        ```javascript
        eventEmitter.on(Messages.KEY_EVENT_ENTER, () => {
          resetGame();
        });
        ```

     1. Thêm hàm `clear()` vào EventEmitter:

        ```javascript
        clear() {
          this.listeners = {};
        }
        ```

👽 💥 🚀 Chúc mừng, Thuyền trưởng! Trò chơi của bạn đã hoàn thành! Làm tốt lắm! 🚀 💥 👽

---

## 🚀 Thử thách

Thêm âm thanh! Bạn có thể thêm âm thanh để tăng cường trải nghiệm chơi trò chơi, có thể khi có tia laser bắn trúng, hoặc khi nhân vật chính chết hoặc chiến thắng? Hãy xem [sandbox này](https://www.w3schools.com/jsref/tryit.asp?filename=tryjsref_audio_play) để tìm hiểu cách phát âm thanh bằng JavaScript.

## Câu hỏi sau bài giảng

[Câu hỏi sau bài giảng](https://ff-quizzes.netlify.app/web/quiz/40)

## Ôn tập & Tự học

Bài tập của bạn là tạo một trò chơi mẫu mới, vì vậy hãy khám phá một số trò chơi thú vị ngoài kia để xem loại trò chơi nào bạn có thể xây dựng.

## Bài tập

[Xây dựng một trò chơi mẫu](assignment.md)

---

**Tuyên bố miễn trừ trách nhiệm**:  
Tài liệu này đã được dịch bằng dịch vụ dịch thuật AI [Co-op Translator](https://github.com/Azure/co-op-translator). Mặc dù chúng tôi cố gắng đảm bảo độ chính xác, xin lưu ý rằng các bản dịch tự động có thể chứa lỗi hoặc không chính xác. Tài liệu gốc bằng ngôn ngữ bản địa nên được coi là nguồn thông tin chính thức. Đối với các thông tin quan trọng, khuyến nghị sử dụng dịch vụ dịch thuật chuyên nghiệp từ con người. Chúng tôi không chịu trách nhiệm cho bất kỳ sự hiểu lầm hoặc diễn giải sai nào phát sinh từ việc sử dụng bản dịch này.