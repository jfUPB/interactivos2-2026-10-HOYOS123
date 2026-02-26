# Unidad 3

## Bitácora de proceso de aprendizaje

```
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
  <style> body { margin: 0; overflow: hidden; background: black; } </style>
</head>
<body>
<script>

let eventQueue = [];

// VISUALES
let bgPulse = 0;
let warp = 0;
let particles = [];
let scanLines = [];
let spirals = [];

let lastDelta = 0;
let history = [];

let rWarp;
let gWarp;

function setup() {
  createCanvas(windowWidth, windowHeight);
  frameRate(60);

  socket = new WebSocket('ws://localhost:8081');

  //let socketOpenStage = new WebSocket('ws://localhost:8082');
  /*
    socketOpenStage.onmessage = (event) => {
    const data = JSON.parse(event.data);

    if (data.address === "/xy_1") {
      rWarp = Number(data.args?.[0] ?? 255);
      gWarp = Number(data.args?.[1] ?? 255);
    }
  };
  
  
  */


  socket.onmessage = (event) => {
    const msg = JSON.parse(event.data);

    if (msg.address === "/dirt/play") {

      if (!msg.args) return;

      let s = "";

      for (let i = 0; i < msg.args.length; i += 2) {
        if (msg.args[i] === "s") {
          s = msg.args[i + 1];
        }
      }

      if (!s) return;

      eventQueue.push({
        timestamp: Math.floor(msg.timestamp),
        s: s
      });

      eventQueue.sort((a, b) => a.timestamp - b.timestamp);
    }

    else if (msg.address === "/fader_1") {
      oscFader = Number(msg.args?.[0] ?? 0);
      status = "fader_1";
    }

    else if (msg.address === "/xy_1") {
      oscX = Number(msg.args?.[0] ?? 0);
      oscY = Number(msg.args?.[1] ?? 0);
      status = "xy_1";
    }

  };
}

function draw() {
  background(0);

  let now = Date.now();
  drawDebugTimeline(now);

  while (eventQueue.length > 0 && now >= eventQueue[0].timestamp) {
    let ev = eventQueue.shift();

    lastDelta = now - ev.timestamp;
    history.push(lastDelta);
    if(history.length > 50) history.shift();

    ejecutarVisual(ev.s);
  }

  if (bgPulse > 0) {
    noStroke();
    for (let i = 0; i < height; i += 5) {
      fill(50 + bgPulse, 0, 80 + bgPulse * 0.5, 60);
      rect(0, i, width, 5);
    }
    bgPulse *= 0.9;
  }


  if (warp > 0) {
    noFill();
    stroke(150, 0, 255, 180);
    strokeWeight(3);
    circle(width/2, height/2, warp);
    warp *= 1.1;
    if (warp > width * 1.5) warp = 0;
  }


  for (let i = particles.length - 1; i >= 0; i--) {
    let p = particles[i];
    fill(0, 255, 120, p.alpha);
    noStroke();
    circle(p.x, p.y, p.size);

    p.x += p.vx;
    p.y += p.vy;
    p.alpha -= 5;

    if (p.alpha <= 0) particles.splice(i, 1);
  }


  for (let i = scanLines.length - 1; i >= 0; i--) {
    let s = scanLines[i];
    stroke(0, 200, 255, s.alpha);
    line(0, s.y, width, s.y);
    s.y += 15;
    s.alpha -= 10;
    if (s.alpha <= 0) scanLines.splice(i, 1);
  }


  push();
  translate(width/2, height/2);

  for (let i = spirals.length - 1; i >= 0; i--) {
    let sp = spirals[i];
    stroke(255, 200, 0, sp.alpha);
    noFill();
    beginShape();
    for (let a = 0; a < TWO_PI * 3; a += 0.1) {
      let r = sp.radius + a * 5;
      let x = cos(a + sp.rot) * r;
      let y = sin(a + sp.rot) * r;
      vertex(x, y);
    }
    endShape();

    sp.rot += 0.05;
    sp.alpha -= 3;

    if (sp.alpha <= 0) spirals.splice(i, 1);
  }

  pop();

  // DEBUG
fill(255);
noStroke();
textSize(14);
text(`Último error: ${lastDelta.toFixed(2)} ms`, 20, 30);
text(`FPS: ${Math.round(frameRate())}`, 20, 50);
}

// ======================
// DISPARADOR
// ======================

function ejecutarVisual(s) {
  if (!s) return;

  // KICK
  if (s.includes('bd')) {
    bgPulse = 150;
    warp = 50;
  }


  if (s.includes('sd')) {
    for (let i = 0; i < 20; i++) {
      particles.push({
        x: width/2,
        y: height/2,
        vx: random(-5, 5),
        vy: random(-5, 5),
        size: random(5, 15),
        alpha: 255
      });
    }
  }


  if (s.includes('hh')) {
    scanLines.push({
      y: random(height),
      alpha: 200
    });
  }


  if (s.includes('rim') || s.includes('lt') || s.includes('rd')) {
    spirals.push({
      radius: random(20, 100),
      rot: random(TWO_PI),
      alpha: 255
    });
  }
}

function drawDebugTimeline(now) {
  push();
  translate(0, height - 120);

  stroke(255, 0, 0);
  line(width / 2, 0, width / 2, 80);

  let escala = 0.5;

  eventQueue.forEach(ev => {
    let x = width / 2 + (ev.timestamp - now) * escala;
    fill(0, 255, 255);
    ellipse(x, 40, 20, 20);
  });

  history.forEach((d, i) => {
    fill(255, 255, 255, i * 5);
    ellipse(width / 2 + d * escala, 60, 10, 10);
  });

  pop();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

</script>
</body>
</html>

```

## Bitácora de aplicación 



## Bitácora de reflexión
