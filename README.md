<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Grabadora de Voz</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    button { margin: 5px; padding: 10px; font-size: 16px; }
    audio { margin-top: 20px; display: block; }
  </style>
</head>
<body>
  <h2>🎙️ Grabadora de Voz</h2>
  <button id="startBtn">🎤 Iniciar grabación</button>
  <button id="pauseBtn" disabled>⏸️ Pausar</button>
  <button id="resumeBtn" disabled>▶️ Reanudar</button>
  <button id="stopBtn" disabled>⏹️ Detener</button>
  <br>
  <audio id="audioPlayback" controls></audio>
  <br>
  <a id="downloadLink" style="display:none;">⬇️ Descargar Audio</a>

  <script>
    let mediaRecorder;
    let audioChunks = [];

    const startBtn = document.getElementById("startBtn");
    const pauseBtn = document.getElementById("pauseBtn");
    const resumeBtn = document.getElementById("resumeBtn");
    const stopBtn = document.getElementById("stopBtn");
    const audioPlayback = document.getElementById("audioPlayback");
    const downloadLink = document.getElementById("downloadLink");

    startBtn.addEventListener("click", async () => {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        mediaRecorder = new MediaRecorder(stream);

        audioChunks = [];

        mediaRecorder.ondataavailable = (event) => {
          audioChunks.push(event.data);
        };

        mediaRecorder.onstop = () => {
          const blob = new Blob(audioChunks, { type: "audio/webm" });
          const url = URL.createObjectURL(blob);
          audioPlayback.src = url;
          downloadLink.href = url;
          downloadLink.download = "grabacion.webm";
          downloadLink.textContent = "⬇️ Descargar Audio";
          downloadLink.style.display = "inline";
        };

        mediaRecorder.start();
        startBtn.disabled = true;
        pauseBtn.disabled = false;
        stopBtn.disabled = false;
      } catch (error) {
        alert("No se pudo acceder al micrófono. Revisa los permisos del navegador.");
        console.error("Error al acceder al micrófono:", error);
      }
    });

    pauseBtn.addEventListener("click", () => {
      if (mediaRecorder && mediaRecorder.state === "recording") {
        mediaRecorder.pause();
        pauseBtn.disabled = true;
        resumeBtn.disabled = false;
      }
    });

    resumeBtn.addEventListener("click", () => {
      if (mediaRecorder && mediaRecorder.state === "paused") {
        mediaRecorder.resume();
        resumeBtn.disabled = true;
        pauseBtn.disabled = false;
      }
    });

    stopBtn.addEventListener("click", () => {
      if (mediaRecorder) {
        mediaRecorder.stop();
        startBtn.disabled = false;
        pauseBtn.disabled = true;
        resumeBtn.disabled = true;
        stopBtn.disabled = true;
      }
    });
  </script>
</body>
</html>
