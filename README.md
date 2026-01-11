<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Игра с самолётом — 3 линии</title>
  <style>
    body {
      margin: 0;
      background: #000;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: sans-serif;
      color: #fff;
    }
    canvas {
      background: linear-gradient(#8fd3ff, #e9f6ff);
      box-shadow: 0 0 20px rgba(0,0,0,0.6);
      border-radius: 10px;
    }
  </style>
</head>
<body>
<canvas id="game" width="360" height="640"></canvas>

<script>
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');

  const W = canvas.width;
  const H = canvas.height;

  // Настройки полос
  const lanesCount = 3;
  const laneWidth = W / lanesCount;
  const lanesX = [
    laneWidth * 0.5,       // левая полоса (центр)
    laneWidth * 1.5,       // центральная
    laneWidth * 2.5        // правая
  ];

  // Самолёт
  const plane = {
    laneIndex: 1,          // стартуем по центру (0 - лево, 1 - центр, 2 - право)
    x: lanesX[1],
    y: H - 120,
    width: 40,
    height: 60,
    targetLaneIndex: 1,
    speedLaneChange: 0.1   // скорость плавного перестроения
  };

  // Фон (облака / движение)
  let bgOffset = 0;
  const bgSpeed = 0.5;

  // Управление
  window.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'ф') {
      plane.targetLaneIndex = Math.max(0, plane.targetLaneIndex - 1);
    }
    if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'в') {
      plane.targetLaneIndex = Math.min(lanesCount - 1, plane.targetLaneIndex + 1);
    }
  });

  function updatePlane() {
    // целевая позиция по X
    const targetX = lanesX[plane.targetLaneIndex];

    // плавно двигаем самолёт к целевой полосе
    plane.x += (targetX - plane.x) * plane.speedLaneChange;
  }

  function drawBackground() {
    // имитация движения — вертикальные линии / "дороги в небе"
    bgOffset += bgSpeed;
    if (bgOffset > 40) bgOffset = 0;

    ctx.save();
    ctx.globalAlpha = 0.3;
    ctx.strokeStyle = '#ffffff';
    ctx.lineWidth = 4;
    ctx.setLineDash([20, 20]);

    for (let i = 0; i < lanesCount; i++) {
      const x = laneWidth * (i + 0.5);
      ctx.beginPath();
      ctx.moveTo(x, -40 + bgOffset);
      ctx.lineTo(x, H + 40 + bgOffset);
      ctx.stroke();
    }

    ctx.restore();
  }

  function drawPlane() {
    const { x, y, width, height } = plane;

    ctx.save();
    ctx.translate(x, y);

    // корпус
    ctx.fillStyle = '#ffffff';
    ctx.beginPath();
    ctx.moveTo(0, -height / 2);
    ctx.lineTo(width / 2, height / 2);
    ctx.lineTo(-width / 2, height / 2);
    ctx.closePath();
    ctx.fill();

    // крылья
    ctx.fillStyle = '#ff4040';
    ctx.beginPath();
    ctx.moveTo(-width / 2, 0);
    ctx.lineTo(-width, height / 8);
    ctx.lineTo(-width / 2, height / 4);
    ctx.closePath();
    ctx.fill();

    ctx.beginPath();
    ctx.moveTo(width / 2, 0);
    ctx.lineTo(width, height / 8);
    ctx.lineTo(width / 2, height / 4);
    ctx.closePath();
    ctx.fill();

    // хвост
    ctx.fillStyle = '#ff4040';
    ctx.fillRect(-width / 6, height / 4, width / 3, height / 4);

    ctx.restore();
  }

  function clear() {
    ctx.clearRect(0, 0, W, H);
  }

  function gameLoop() {
    clear();
    drawBackground();
    updatePlane();
    drawPlane();

    requestAnimationFrame(gameLoop);
  }

  gameLoop();
</script>
</body>
</html>
