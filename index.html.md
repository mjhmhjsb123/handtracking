<!DOCTYPE html>  
<html>  
<head>  
<meta charset="UTF-8">  
<meta name="viewport" content="width=device-width, initial-scale=1.0">  
<title>Hand Tracking</title>  
  
<style>  
body {  
  margin: 0;  
  overflow: hidden;  
  background: black;  
}  
  
canvas {  
  position: absolute;  
  top: 0;  
  left: 0;  
}  
</style>  
</head>  
<body>  
  
<video id="video" autoplay playsinline style="display:none;"></video>  
<canvas id="canvas"></canvas>  
  
<!-- MediaPipe -->  
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js"></script>  
<script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js"></script>  
  
<script>  
const video = document.getElementById("video");  
const canvas = document.getElementById("canvas");  
const ctx = canvas.getContext("2d");  
  
function resize() {  
  canvas.width = window.innerWidth;  
  canvas.height = window.innerHeight;  
}  
resize();  
window.addEventListener("resize", resize);  
  
const hands = new Hands({  
  locateFile: (file) =>  
    `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`  
});  
  
hands.setOptions({  
  maxNumHands: 1,  
  modelComplexity: 1,  
  minDetectionConfidence: 0.7,  
  minTrackingConfidence: 0.7  
});  
  
hands.onResults(results => {  
  ctx.clearRect(0, 0, canvas.width, canvas.height);  
  
  if (results.multiHandLandmarks?.length > 0) {  
    results.multiHandLandmarks[0].forEach(point => {  
      ctx.beginPath();  
      ctx.arc(  
        point.x * canvas.width,  
        point.y * canvas.height,  
        6,  
        0,  
        Math.PI * 2  
      );  
      ctx.fillStyle = "lime";  
      ctx.fill();  
    });  
  }  
});  
  
const camera = new Camera(video, {  
  onFrame: async () => {  
    await hands.send({ image: video });  
  }  
});  
  
camera.start();  
</script>  
  
</body>  
</html>  
